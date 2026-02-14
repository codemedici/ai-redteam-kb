---
title: "Tool Argument Validation"
tags:
  - type/mitigation
  - target/agent
  - target/llm-app
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Tool Argument Validation

## Summary

Tool argument validation enforces strict type, format, and semantic validation on all arguments passed to agent tools, preventing injection attacks and privilege escalation through argument manipulation. This control implements defensive programming principles by validating tool arguments against schemas, using parameterized queries for database operations, and restricting file paths to prevent traversal attacks. By validating arguments deterministically outside the LLM, this mitigation prevents attackers from exploiting tool functionality through malformed or malicious input.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/tool-privilege-escalation]] | Prevents injection-based privilege escalation by validating all tool arguments before execution |
| | [[techniques/unsafe-tool-invocation]] | Blocks SQL injection, path traversal, and command injection through strict argument validation |
| | [[techniques/prompt-injection]] | Limits impact of injected instructions by preventing malicious arguments from reaching tools |

## Implementation

### Core Validation Components

**1. Argument Schema Definition**

Define and enforce strict type and format validation for all tool parameters:

```python
# Tool argument schemas with type and format constraints
TOOL_ARGUMENT_SCHEMAS = {
    'query_database': {
        'type': 'object',
        'properties': {
            'query': {'type': 'string', 'maxLength': 1000},
            'params': {'type': 'array', 'maxItems': 20}
        },
        'required': ['query'],
        'additionalProperties': False
    },
    'read_file': {
        'type': 'object',
        'properties': {
            'path': {'type': 'string', 'pattern': '^/allowed/path/.*$'},
            'encoding': {'type': 'string', 'enum': ['utf-8', 'ascii']}
        },
        'required': ['path'],
        'additionalProperties': False
    },
    'send_email': {
        'type': 'object',
        'properties': {
            'to': {'type': 'string', 'format': 'email', 'maxLength': 200},
            'subject': {'type': 'string', 'maxLength': 200},
            'body': {'type': 'string', 'maxLength': 10000}
        },
        'required': ['to', 'subject', 'body'],
        'additionalProperties': False
    }
}

from jsonschema import validate, ValidationError

def validate_tool_arguments(tool_name, arguments):
    """
    Validate tool arguments against schema
    
    Raises ValidationError if arguments don't match schema
    """
    schema = TOOL_ARGUMENT_SCHEMAS.get(tool_name)
    
    if not schema:
        raise ValueError(f"No schema defined for tool: {tool_name}")
    
    try:
        validate(instance=arguments, schema=schema)
        return True
    except ValidationError as e:
        log_security_event("argument_validation_failed", {
            'tool': tool_name,
            'error': e.message,
            'arguments': arguments
        })
        raise ValueError(f"Invalid arguments for {tool_name}: {e.message}")
```

**2. SQL Parameterization (Database Tools)**

Use prepared statements and parameterized queries to prevent SQL injection:

```python
import sqlite3

class DatabaseTool:
    """Database query tool with SQL injection protection"""
    
    def __init__(self, db_path):
        self.db_path = db_path
    
    def query_database(self, query, params=None):
        """
        Execute database query with parameterization
        
        Args:
            query: SQL query with ? placeholders
            params: List of parameters to bind (optional)
        
        Raises:
            ValueError if query contains dangerous operations
        """
        # Validate query is SELECT-only (read-only tool)
        if not self._is_safe_query(query):
            raise ValueError("Query contains prohibited operations")
        
        # Validate parameters
        if params:
            if not isinstance(params, list):
                raise ValueError("Params must be a list")
            if len(params) > 20:  # Arbitrary safety limit
                raise ValueError("Too many parameters")
        
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        
        try:
            # Execute with parameterization (prevents SQL injection)
            if params:
                cursor.execute(query, params)
            else:
                cursor.execute(query)
            
            results = cursor.fetchall()
            return results
        finally:
            conn.close()
    
    def _is_safe_query(self, query):
        """
        Validate query only contains SELECT
        
        Blocks UPDATE, DELETE, DROP, INSERT
        """
        query_upper = query.upper().strip()
        
        # Check for dangerous SQL keywords
        dangerous_keywords = ['UPDATE', 'DELETE', 'DROP', 'INSERT', 'ALTER', 'CREATE']
        for keyword in dangerous_keywords:
            if keyword in query_upper:
                return False
        
        # Must start with SELECT
        if not query_upper.startswith('SELECT'):
            return False
        
        return True
```

**❌ Vulnerable Pattern (String Concatenation):**

```python
# NEVER DO THIS - Vulnerable to SQL injection
def query_database_vulnerable(user_id):
    query = f"SELECT * FROM users WHERE id = '{user_id}'"  # ❌ INJECTION RISK
    cursor.execute(query)
    return cursor.fetchall()

# Attack: user_id = "1' OR '1'='1' --"
# Results in: SELECT * FROM users WHERE id = '1' OR '1'='1' --'
# Returns ALL users instead of one
```

**✅ Secure Pattern (Parameterization):**

```python
# ALWAYS DO THIS - Safe from SQL injection
def query_database_secure(user_id):
    query = "SELECT * FROM users WHERE id = ?"  # ✅ PLACEHOLDER
    cursor.execute(query, [user_id])  # ✅ PARAMETERIZED
    return cursor.fetchall()

# Attack attempt: user_id = "1' OR '1'='1' --"
# Database treats entire string as literal value for id field
# Query finds no match (id is probably numeric)
```

**3. File Path Restrictions (File System Tools)**

Whitelist allowed directories and validate paths to prevent traversal attacks:

```python
import os
from pathlib import Path

class FileSystemTool:
    """File access tool with path traversal protection"""
    
    def __init__(self, allowed_base_paths):
        """
        Initialize with whitelist of allowed base paths
        
        Args:
            allowed_base_paths: List of allowed directory prefixes
        """
        self.allowed_base_paths = [Path(p).resolve() for p in allowed_base_paths]
    
    def read_file(self, path):
        """
        Read file with path validation
        
        Prevents path traversal attacks
        """
        # Resolve path to absolute form (removes ../ tricks)
        requested_path = Path(path).resolve()
        
        # Validate path is within allowed base paths
        if not self._is_allowed_path(requested_path):
            raise ValueError(f"Access denied: {path} is outside allowed directories")
        
        # Validate file exists and is a file (not directory)
        if not requested_path.is_file():
            raise ValueError(f"Path is not a file: {path}")
        
        # Read and return file content
        return requested_path.read_text()
    
    def _is_allowed_path(self, requested_path):
        """
        Check if path is within allowed base paths
        
        Returns True if path starts with any allowed base path
        """
        for allowed_base in self.allowed_base_paths:
            try:
                # Check if requested path is relative to allowed base
                requested_path.relative_to(allowed_base)
                return True
            except ValueError:
                # Path is not relative to this base
                continue
        
        return False

# Usage example
file_tool = FileSystemTool(allowed_base_paths=[
    '/app/data',
    '/app/uploads'
])

# ✅ SAFE: Within allowed path
content = file_tool.read_file('/app/data/customer_report.csv')

# ❌ BLOCKED: Path traversal attempt
try:
    content = file_tool.read_file('../../../etc/passwd')
except ValueError as e:
    print(f"Blocked: {e}")
    # Resolves to /etc/passwd, not within /app/data or /app/uploads
```

**Common Path Traversal Attack Patterns (Blocked by Validation):**

```python
# All of these should be blocked:
malicious_paths = [
    '../../../etc/passwd',           # Classic traversal
    '/etc/passwd',                   # Absolute path outside allowed
    '../../../../root/.ssh/id_rsa',  # Multiple levels up
    './../../config/secrets.json',   # Relative traversal
    '/app/data/../../etc/passwd',    # Traversal after allowed prefix
    '/app/data/../uploads/../etc/passwd',  # Complex traversal
]

for malicious_path in malicious_paths:
    try:
        file_tool.read_file(malicious_path)
        print(f"❌ SECURITY FAILURE: {malicious_path} was not blocked!")
    except ValueError:
        print(f"✅ Blocked: {malicious_path}")
```

### Integration with Tool Invocation Pipeline

**Pre-execution validation layer:**

```python
class ValidatedToolInvoker:
    """Tool invocation with mandatory argument validation"""
    
    def invoke_tool(self, tool_name, arguments, user_context):
        """
        Invoke tool with validation
        
        Validation order:
        1. Schema validation (type, format, range)
        2. Semantic validation (business logic)
        3. Security validation (injection patterns, path traversal)
        4. Execution
        """
        # Step 1: Schema validation
        validate_tool_arguments(tool_name, arguments)
        
        # Step 2: Tool-specific security validation
        if tool_name == 'query_database':
            self._validate_database_query(arguments)
        elif tool_name == 'read_file':
            self._validate_file_path(arguments)
        elif tool_name == 'send_email':
            self._validate_email_arguments(arguments)
        
        # Step 3: Execute tool
        tool_impl = get_tool_implementation(tool_name)
        return tool_impl.execute(arguments, user_context)
    
    def _validate_database_query(self, arguments):
        """Additional validation for database queries"""
        query = arguments.get('query', '')
        
        # Block dangerous SQL keywords
        dangerous_keywords = ['DROP', 'DELETE', 'UPDATE', 'INSERT', 'ALTER']
        query_upper = query.upper()
        
        for keyword in dangerous_keywords:
            if keyword in query_upper:
                raise ValueError(f"Query contains prohibited keyword: {keyword}")
    
    def _validate_file_path(self, arguments):
        """Additional validation for file paths"""
        path = arguments.get('path', '')
        
        # Block path traversal patterns
        if '..' in path:
            raise ValueError("Path contains traversal sequence")
        
        # Block absolute paths outside allowed directories
        if path.startswith('/') and not path.startswith('/allowed/'):
            raise ValueError("Absolute path outside allowed directories")
    
    def _validate_email_arguments(self, arguments):
        """Additional validation for email sending"""
        to = arguments.get('to', '')
        body = arguments.get('body', '')
        
        # Block suspicious email patterns
        if '@' not in to:
            raise ValueError("Invalid email address format")
        
        # Scan for sensitive data in body
        if contains_sensitive_data(body):
            raise ValueError("Email body contains sensitive data patterns")
```

## Limitations & Trade-offs

**Limitations:**

- **Cannot prevent all attacks:** Sophisticated obfuscation may bypass validation rules
- **Schema maintenance overhead:** Schemas must be updated when tool signatures change
- **False positives possible:** Overly strict validation may block legitimate edge cases
- **Cannot validate semantic correctness:** Validation ensures format but not business logic correctness

**Trade-offs:**

- **Security vs. Flexibility:** Strict validation improves security but limits tool expressiveness
- **Performance vs. Coverage:** Comprehensive validation adds latency to tool invocation
- **Maintainability vs. Granularity:** Fine-grained validation increases maintenance burden
- **Whitelist vs. Blacklist:** Whitelists are more secure but harder to maintain

**Bypass Scenarios:**

- **Encoding tricks:** Unicode, base64, or other encoding may bypass string matching
- **Second-order injection:** Malicious data persisted through validation, executed later
- **Logic bugs in validators:** Implementation flaws in validation logic
- **Novel attack patterns:** Completely new injection techniques not covered by validators

## Testing & Validation

**Functional Testing:**

1. **Valid Arguments:** Verify legitimate arguments pass validation
2. **Invalid Format:** Test that malformed arguments are rejected
3. **Type Mismatch:** Verify type validation catches incorrect types
4. **Range Violations:** Test that out-of-range values are blocked

**Security Testing:**

1. **SQL Injection:** Test various SQL injection payloads against database tools
2. **Path Traversal:** Attempt directory traversal attacks against file tools
3. **Command Injection:** Test command injection patterns against execution tools
4. **Encoding Bypass:** Try encoding tricks to bypass validation

**Test Cases:**

```python
def test_sql_injection_prevention():
    """Verify SQL injection attempts are blocked"""
    tool = DatabaseTool('test.db')
    
    # Test injection attempt
    malicious_query = "SELECT * FROM users WHERE id = '1' OR '1'='1' --"
    
    with pytest.raises(ValueError):
        tool.query_database(malicious_query)

def test_path_traversal_prevention():
    """Verify path traversal attempts are blocked"""
    tool = FileSystemTool(allowed_base_paths=['/app/data'])
    
    # Test traversal attempt
    with pytest.raises(ValueError):
        tool.read_file('../../../etc/passwd')
```

## Sources

> "Argument Schemas: Define and enforce strict type/format validation for all tool parameters. SQL Parameterization: Use prepared statements; never concatenate user input into queries. File Path Restrictions: Whitelist allowed directories; reject path traversal attempts."
> — Extracted from tool-privilege-escalation preventive controls

## Related

- **Combines with:** [[mitigations/input-validation-patterns]], [[mitigations/least-privilege-implementation]], [[mitigations/tool-sandboxing-architecture]]
- **Supports:** [[mitigations/tool-invocation-monitoring]] (validation failures indicate attacks)
