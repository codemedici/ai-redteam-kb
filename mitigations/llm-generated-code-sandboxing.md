---
title: "LLM-Generated Code Sandboxing"
tags:
  - type/mitigation
  - target/llm
  - target/code
  - source/llms-in-cybersecurity
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# LLM-Generated Code Sandboxing

## Summary

LLM-generated code sandboxing executes AI-suggested code in isolated, restricted environments with limited access to system resources, network, and sensitive data before deploying to production. Because code-generating LLMs are trained on historical codebases containing security vulnerabilities and can be manipulated through training data poisoning to suggest backdoored code, sandboxed execution provides a critical safety boundary—allowing observation of code behavior (file access, network connections, resource usage) in a controlled environment where malicious actions cannot cause real damage. Sandboxing treats LLM-generated code as untrusted until proven safe through behavioral analysis.

## Defends Against

| ID | Technique | Description |
|----|------|-------------|
| | [[techniques/llm-code-generation-vulnerabilities]] | Contains damage from vulnerable LLM-generated code by restricting execution environment |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Prevents backdoors injected via training poisoning from accessing production systems |
| | [[techniques/backdoor-poisoning]] | Detects malicious behavior (unauthorized network connections, file modifications) in sandbox before production deployment |

## Implementation

### Sandbox Architecture Patterns

**1. Container-Based Sandboxing**

Execute LLM-generated code in isolated Docker containers:

```python
import docker
import json
import tempfile
import os

class LLMCodeSandbox:
    """Execute LLM-generated code in isolated Docker container"""
    
    def __init__(self):
        self.client = docker.from_env()
        self.sandbox_image = "python:3.11-slim"  # Minimal base image
        self.timeout_seconds = 30
    
    def execute_code(self, code: str, allowed_imports: list = None) -> dict:
        """
        Execute code in sandboxed container
        
        Args:
            code: Python code to execute
            allowed_imports: Whitelist of allowed import statements
        
        Returns:
            Execution result with stdout, stderr, and behavioral analysis
        """
        # Validate imports
        if allowed_imports:
            self._validate_imports(code, allowed_imports)
        
        # Create temporary directory for code
        with tempfile.TemporaryDirectory() as tmpdir:
            code_file = os.path.join(tmpdir, "generated_code.py")
            with open(code_file, 'w') as f:
                f.write(code)
            
            # Run in container with strict limits
            try:
                container = self.client.containers.run(
                    self.sandbox_image,
                    command=["python", "/code/generated_code.py"],
                    volumes={tmpdir: {'bind': '/code', 'mode': 'ro'}},
                    detach=True,
                    
                    # Resource limits
                    mem_limit="256m",           # 256 MB memory limit
                    memswap_limit="256m",       # No swap
                    cpu_quota=50000,            # 50% of one CPU core
                    pids_limit=50,              # Max 50 processes
                    
                    # Security restrictions
                    network_mode="none",        # No network access
                    read_only=True,             # Read-only filesystem
                    security_opt=["no-new-privileges"],
                    cap_drop=["ALL"],           # Drop all capabilities
                    
                    # Auto-remove after execution
                    remove=True
                )
                
                # Wait for completion with timeout
                result = container.wait(timeout=self.timeout_seconds)
                logs = container.logs().decode('utf-8')
                
                return {
                    'status': 'success' if result['StatusCode'] == 0 else 'failed',
                    'exit_code': result['StatusCode'],
                    'output': logs,
                    'resource_usage': self._get_resource_usage(container),
                    'security_violations': []
                }
                
            except docker.errors.ContainerError as e:
                return {
                    'status': 'error',
                    'error': str(e),
                    'security_violations': ['Container execution failed']
                }
            except Exception as e:
                return {
                    'status': 'error',
                    'error': str(e),
                    'security_violations': ['Sandbox error']
                }
    
    def _validate_imports(self, code: str, allowed_imports: list):
        """Check code only uses whitelisted imports"""
        import ast
        
        tree = ast.parse(code)
        for node in ast.walk(tree):
            if isinstance(node, ast.Import):
                for alias in node.names:
                    if alias.name not in allowed_imports:
                        raise SecurityError(f"Unauthorized import: {alias.name}")
            elif isinstance(node, ast.ImportFrom):
                if node.module not in allowed_imports:
                    raise SecurityError(f"Unauthorized import: {node.module}")
    
    def _get_resource_usage(self, container):
        """Get resource usage statistics"""
        stats = container.stats(stream=False)
        return {
            'memory_mb': stats['memory_stats']['usage'] / (1024 * 1024),
            'cpu_percent': stats['cpu_stats']['cpu_usage']['total_usage']
        }
```

**Usage:**

```python
# LLM generates code
llm_code = '''
import requests

def fetch_data():
    response = requests.get("https://api.example.com/data")
    return response.json()

print(fetch_data())
'''

# Execute in sandbox
sandbox = LLMCodeSandbox()
result = sandbox.execute_code(llm_code, allowed_imports=['requests', 'json'])

if result['status'] == 'error':
    print(f"Code failed security validation: {result['error']}")
else:
    print(f"Code executed successfully: {result['output']}")
```

**2. Virtual Machine Sandboxing**

For higher isolation, use VMs instead of containers:

```python
import subprocess
import json

class VMSandbox:
    """Execute LLM code in isolated VM using Firecracker microVMs"""
    
    def __init__(self):
        self.vm_image = "sandbox-vm-python.img"
        self.kernel = "vmlinux"
    
    def execute_code(self, code: str) -> dict:
        """Execute code in microVM"""
        
        # Create VM config
        vm_config = {
            'kernel_image_path': self.kernel,
            'rootfs_path': self.vm_image,
            'mem_size_mib': 256,
            'vcpu_count': 1,
            'network_interfaces': []  # No network
        }
        
        # Launch Firecracker VM
        result = subprocess.run(
            ['firecracker', '--config-file', 'vm-config.json'],
            capture_output=True,
            timeout=30
        )
        
        return {
            'status': 'success' if result.returncode == 0 else 'failed',
            'output': result.stdout.decode('utf-8')
        }
```

**3. Language Runtime Sandboxing (Python RestrictedPython)**

Sandbox at language level for lightweight isolation:

```python
from RestrictedPython import compile_restricted, safe_globals
from RestrictedPython.Guards import guarded_inplacevar

class PythonRuntimeSandbox:
    """Execute Python code with RestrictedPython"""
    
    def __init__(self):
        # Define safe built-ins
        self.safe_builtins = {
            '__builtins__': {
                'print': print,
                'len': len,
                'range': range,
                'enumerate': enumerate,
                # Add other safe built-ins
            }
        }
    
    def execute_code(self, code: str) -> dict:
        """Execute code in restricted Python environment"""
        
        try:
            # Compile with restrictions
            byte_code = compile_restricted(
                code,
                filename='<inline>',
                mode='exec'
            )
            
            if byte_code.errors:
                return {
                    'status': 'compilation_error',
                    'errors': byte_code.errors
                }
            
            # Execute in restricted environment
            exec(byte_code.code, self.safe_builtins)
            
            return {
                'status': 'success',
                'output': 'Code executed successfully'
            }
            
        except Exception as e:
            return {
                'status': 'runtime_error',
                'error': str(e)
            }
```

**Restrictions enforced:**
- No file system access
- No network access
- No subprocess execution
- No access to `os`, `sys`, `subprocess` modules
- Limited built-ins only

**4. WebAssembly Sandboxing**

Compile and run code in WASM sandbox:

```python
import wasmtime

class WASMSandbox:
    """Execute code compiled to WebAssembly"""
    
    def __init__(self):
        self.engine = wasmtime.Engine()
        self.store = wasmtime.Store(self.engine)
    
    def execute_wasm(self, wasm_bytes: bytes) -> dict:
        """Execute WebAssembly code"""
        
        try:
            module = wasmtime.Module(self.engine, wasm_bytes)
            instance = wasmtime.Instance(self.store, module, [])
            
            # Call main function
            main = instance.exports(self.store)["main"]
            result = main(self.store)
            
            return {
                'status': 'success',
                'result': result
            }
        except Exception as e:
            return {
                'status': 'error',
                'error': str(e)
            }
```

### Behavioral Analysis in Sandbox

**Monitor code behavior for suspicious patterns:**

```python
class SandboxMonitor:
    """Monitor sandbox execution for malicious behavior"""
    
    def __init__(self):
        self.violations = []
    
    def analyze_behavior(self, code: str, execution_result: dict) -> dict:
        """Analyze code and execution for security violations"""
        
        # Static analysis
        self._check_suspicious_imports(code)
        self._check_obfuscation(code)
        self._check_dangerous_functions(code)
        
        # Dynamic analysis
        if 'network_attempts' in execution_result:
            self._check_network_access(execution_result['network_attempts'])
        
        if 'file_operations' in execution_result:
            self._check_file_access(execution_result['file_operations'])
        
        if 'resource_usage' in execution_result:
            self._check_resource_abuse(execution_result['resource_usage'])
        
        return {
            'is_safe': len(self.violations) == 0,
            'violations': self.violations,
            'risk_score': self._calculate_risk_score()
        }
    
    def _check_suspicious_imports(self, code: str):
        """Detect dangerous import statements"""
        dangerous_imports = [
            'os', 'sys', 'subprocess', 'socket', 
            'pickle', 'marshal', '__import__',
            'eval', 'exec', 'compile'
        ]
        
        for dangerous in dangerous_imports:
            if f'import {dangerous}' in code or f'from {dangerous}' in code:
                self.violations.append({
                    'type': 'suspicious_import',
                    'module': dangerous,
                    'severity': 'high'
                })
    
    def _check_obfuscation(self, code: str):
        """Detect code obfuscation attempts"""
        obfuscation_patterns = [
            'eval(', 'exec(', 'compile(',
            '__import__', 'getattr(',
            'base64.b64decode', 'codecs.decode'
        ]
        
        for pattern in obfuscation_patterns:
            if pattern in code:
                self.violations.append({
                    'type': 'obfuscation',
                    'pattern': pattern,
                    'severity': 'high'
                })
    
    def _check_dangerous_functions(self, code: str):
        """Detect calls to dangerous functions"""
        dangerous_functions = [
            'os.system', 'subprocess.call', 'subprocess.Popen',
            'open(', 'file(',
            'socket.socket', 'urllib.request',
            'pickle.loads', 'yaml.load'
        ]
        
        for func in dangerous_functions:
            if func in code:
                self.violations.append({
                    'type': 'dangerous_function',
                    'function': func,
                    'severity': 'critical'
                })
    
    def _check_network_access(self, network_attempts: list):
        """Detect unauthorized network access"""
        if len(network_attempts) > 0:
            for attempt in network_attempts:
                self.violations.append({
                    'type': 'unauthorized_network',
                    'destination': attempt['destination'],
                    'severity': 'critical'
                })
    
    def _check_file_access(self, file_operations: list):
        """Detect unauthorized file access"""
        for op in file_operations:
            if op['type'] == 'write' and op['path'].startswith('/etc'):
                self.violations.append({
                    'type': 'unauthorized_file_write',
                    'path': op['path'],
                    'severity': 'critical'
                })
    
    def _check_resource_abuse(self, resource_usage: dict):
        """Detect resource exhaustion attempts"""
        if resource_usage.get('memory_mb', 0) > 200:
            self.violations.append({
                'type': 'excessive_memory',
                'usage_mb': resource_usage['memory_mb'],
                'severity': 'medium'
            })
        
        if resource_usage.get('cpu_percent', 0) > 80:
            self.violations.append({
                'type': 'excessive_cpu',
                'usage_percent': resource_usage['cpu_percent'],
                'severity': 'medium'
            })
    
    def _calculate_risk_score(self) -> int:
        """Calculate risk score 0-100"""
        severity_scores = {'low': 10, 'medium': 30, 'high': 50, 'critical': 100}
        
        if not self.violations:
            return 0
        
        max_score = max(severity_scores[v['severity']] for v in self.violations)
        return max_score
```

**Usage:**

```python
# Test LLM-generated code in sandbox
llm_code = generate_code_from_llm("Write a function to fetch user data from API")

sandbox = LLMCodeSandbox()
result = sandbox.execute_code(llm_code)

monitor = SandboxMonitor()
analysis = monitor.analyze_behavior(llm_code, result)

if not analysis['is_safe']:
    print(f"❌ Code failed security analysis:")
    for violation in analysis['violations']:
        print(f"  - {violation['type']}: {violation.get('module', violation.get('function', 'N/A'))}")
    print(f"Risk score: {analysis['risk_score']}/100")
else:
    print("✅ Code passed security analysis - safe to deploy")
```

### Integration with Development Workflow

**1. IDE Integration**

Sandbox LLM suggestions before accepting:

```python
# VSCode extension: sandbox Copilot suggestions
class CopilotSandboxExtension:
    """VSCode extension to sandbox GitHub Copilot suggestions"""
    
    def on_copilot_suggestion(self, code_snippet: str):
        """Called when Copilot suggests code"""
        
        # Run in sandbox
        sandbox = LLMCodeSandbox()
        result = sandbox.execute_code(code_snippet)
        
        monitor = SandboxMonitor()
        analysis = monitor.analyze_behavior(code_snippet, result)
        
        if not analysis['is_safe']:
            # Show warning in IDE
            self.show_warning(
                f"⚠️ Security Alert\n\n"
                f"Copilot suggestion contains potential security issues:\n"
                f"{', '.join(v['type'] for v in analysis['violations'])}\n\n"
                f"Accept anyway? (Not recommended)"
            )
        else:
            # Accept suggestion automatically
            self.accept_suggestion(code_snippet)
```

**2. Code Review Integration**

Automatically sandbox code in pull requests:

```yaml
# .github/workflows/sandbox-llm-code.yml
name: Sandbox LLM Code

on:
  pull_request:
    branches: [main, develop]

jobs:
  sandbox-analysis:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Identify LLM-generated code
        run: |
          # Check commit messages for "copilot", "chatgpt", etc.
          LLM_COMMITS=$(git log origin/main..HEAD --grep="copilot|chatgpt|ai-generated" --oneline)
          
          if [ -n "$LLM_COMMITS" ]; then
            echo "LLM-generated code detected - running sandbox analysis"
            echo "$LLM_COMMITS" > llm-commits.txt
          fi
      
      - name: Sandbox LLM code
        if: hashFiles('llm-commits.txt') != ''
        run: |
          python scripts/sandbox-llm-code.py --commits llm-commits.txt
      
      - name: Comment results on PR
        if: always()
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const results = JSON.parse(fs.readFileSync('sandbox-results.json'));
            
            if (results.violations.length > 0) {
              github.rest.issues.createComment({
                issue_number: context.issue.number,
                owner: context.repo.owner,
                repo: context.repo.repo,
                body: `⚠️ **LLM Code Security Analysis**\n\nSandbox detected potential security issues:\n${results.violations.map(v => `- ${v.type}: ${v.description}`).join('\n')}\n\nRisk score: ${results.risk_score}/100`
              });
            }
```

**3. Production Deployment Gate**

Require sandbox approval before production:

```python
class DeploymentPipeline:
    """CI/CD pipeline with sandbox gate"""
    
    def pre_deployment_check(self, code_changes: list) -> bool:
        """Sandbox all code changes before deployment"""
        
        sandbox = LLMCodeSandbox()
        monitor = SandboxMonitor()
        
        for change in code_changes:
            if self._is_llm_generated(change):
                result = sandbox.execute_code(change['code'])
                analysis = monitor.analyze_behavior(change['code'], result)
                
                if not analysis['is_safe']:
                    self.block_deployment(
                        reason="LLM-generated code failed sandbox security analysis",
                        violations=analysis['violations']
                    )
                    return False
        
        return True
    
    def _is_llm_generated(self, change: dict) -> bool:
        """Detect if code was LLM-generated"""
        indicators = [
            'AI-generated' in change.get('commit_message', ''),
            'Copilot' in change.get('commit_message', ''),
            'ChatGPT' in change.get('commit_message', ''),
            change.get('author_email') == 'copilot@github.com'
        ]
        return any(indicators)
```

## Limitations & Trade-offs

### 1. Performance Overhead

**Impact:** Sandboxing adds execution time

**Typical overhead:**
- Container launch: 2-5 seconds
- VM launch: 5-15 seconds
- Runtime sandbox (RestrictedPython): <100ms

**For developer workflow:**
- Sandboxing every Copilot suggestion: Frustrating delays
- Sandboxing in CI/CD: Acceptable

**Mitigation:**
- Use lightweight sandboxing for frequent operations
- Full sandboxing for deployment gates only
- Cache sandbox results for identical code

### 2. False Positives

**Problem:** Legitimate code flagged as suspicious

**Example:**
```python
# Legitimate use case blocked
import os  # Flagged as suspicious

def get_environment():
    return os.environ.get('ENV_VAR')  # Safe usage, but import blocked
```

**Impact:**
- Developer frustration
- Legitimate features blocked
- Time wasted investigating false alarms

**Mitigation:**
- Whitelist safe usage patterns
- Context-aware analysis (distinguish safe from unsafe imports)
- Allow overrides with justification

### 3. Sandbox Escape Risks

**Reality:** Sandboxes are not perfect isolation

**Known escape vectors:**
- Kernel vulnerabilities in container runtime
- Misconfigured security policies
- Side-channel attacks
- Resource exhaustion causing host instability

**Mitigation:**
- Keep sandbox infrastructure patched
- Use multiple layers (container + VM)
- Monitor for escape attempts
- Assume breach: limit blast radius

### 4. Limited Behavioral Coverage

**Problem:** Sandbox can't test all code paths

**Example:**
```python
# Backdoor that activates later
def process_data(data):
    if datetime.now() > datetime(2024, 12, 31):
        os.system("rm -rf /")  # Time-bomb not triggered in sandbox
    return data
```

**Impact:** Malicious code that passes sandbox but activates in production

**Mitigation:**
- Static analysis complements behavioral analysis
- Long-running sandbox tests
- Code review still mandatory

### 5. Development Workflow Disruption

**Challenge:** Sandboxing interrupts flow state

**Developer experience:**
1. Write code
2. Wait for sandbox
3. Fix violations
4. Wait for sandbox again
5. Repeat...

**Impact:** Slower development, frustration

**Mitigation:**
- Asynchronous sandboxing (run in background)
- Progressive sandboxing (lightweight first, deep analysis later)
- Clear, actionable feedback

## Testing & Validation

### Verify Sandbox Isolation

**Test sandbox escape prevention:**

```python
def test_sandbox_isolation():
    """Verify sandbox prevents unauthorized access"""
    
    sandbox = LLMCodeSandbox()
    
    # Test 1: Block file system access
    malicious_code_1 = '''
import os
os.system("cat /etc/passwd")
'''
    result = sandbox.execute_code(malicious_code_1)
    assert result['status'] == 'error', "Should block file system access"
    
    # Test 2: Block network access
    malicious_code_2 = '''
import socket
sock = socket.socket()
sock.connect(("evil.com", 80))
'''
    result = sandbox.execute_code(malicious_code_2)
    assert result['status'] == 'error', "Should block network access"
    
    # Test 3: Enforce resource limits
    resource_bomb = '''
data = "x" * (1024 * 1024 * 1024)  # Try to allocate 1GB
'''
    result = sandbox.execute_code(resource_bomb)
    assert result['status'] == 'error', "Should enforce memory limits"
    
    print("✅ Sandbox isolation tests passed")
```

### Validate Behavioral Analysis

**Test detection of malicious patterns:**

```python
def test_behavioral_analysis():
    """Verify monitor detects malicious code patterns"""
    
    monitor = SandboxMonitor()
    
    # Test 1: Detect suspicious imports
    code_with_os = "import os\nos.system('whoami')"
    analysis = monitor.analyze_behavior(code_with_os, {})
    assert not analysis['is_safe'], "Should detect os import"
    assert any(v['type'] == 'suspicious_import' for v in analysis['violations'])
    
    # Test 2: Detect obfuscation
    obfuscated_code = "exec(base64.b64decode('aW1wb3J0IG9z'))"
    analysis = monitor.analyze_behavior(obfuscated_code, {})
    assert not analysis['is_safe'], "Should detect obfuscation"
    
    # Test 3: Allow safe code
    safe_code = "print('Hello, world!')"
    analysis = monitor.analyze_behavior(safe_code, {})
    assert analysis['is_safe'], "Should allow safe code"
    
    print("✅ Behavioral analysis tests passed")
```

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Secure Development Practices: Sandboxed execution: Test generated code in isolated environments before production. Code-generating LLMs introduce systemic security vulnerabilities by learning from historical codebases containing outdated, deprecated, and inherently insecure programming practices."
> — [[sources/bibliography#LLMs in Cybersecurity]], p. 88-94
