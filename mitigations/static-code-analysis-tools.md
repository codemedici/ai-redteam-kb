---
title: "Static Code Analysis Tools"
tags:
  - type/mitigation
  - target/llm
  - target/code
  - source/llms-in-cybersecurity
atlas: AML.M0043
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Static Code Analysis Tools

## Summary

Static code analysis tools automatically scan source code for security vulnerabilities, coding standard violations, and known-insecure patterns without executing the code. When applied to LLM-generated code, these tools provide an automated defense layer that catches common vulnerability classes (SQL injection, XSS, insecure deserialization, weak cryptography) that code-generating LLMs frequently suggest due to training on historical, insecure codebases. Static analysis is particularly effective against LLM code vulnerabilities because it operates independently of the model's training biases and can enforce modern security standards regardless of what patterns the LLM learned.

## Defends Against

| ID | Technique | Description |
|----|------|-------------|
| | [[techniques/llm-code-generation-vulnerabilities]] | Detects security flaws in LLM-suggested code before it reaches production |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Identifies backdoors and malicious patterns injected through training data poisoning |
| | [[techniques/backdoor-poisoning]] | Catches backdoored code patterns (hardcoded credentials, admin bypasses) in generated suggestions |

## Implementation

### Tool Categories

**1. General-Purpose Static Analysis**

Industry-standard tools that detect common vulnerability patterns across languages:

- **SonarQube** - Multi-language platform with extensive security rules
  - Detects OWASP Top 10 vulnerabilities
  - Integrates with CI/CD pipelines
  - Customizable rulesets
  - Tracks security debt over time

- **Semgrep** - Fast, customizable pattern matching
  - Language-agnostic AST-based analysis
  - Simple rule syntax for custom patterns
  - Open-source with free tier
  - Excellent for detecting LLM-specific bad patterns

- **CodeQL** - Semantic code analysis (GitHub Security)
  - Query language for vulnerability patterns
  - Deep dataflow analysis
  - Built into GitHub Advanced Security
  - Excellent for complex vulnerability chains

**2. Language-Specific Security Scanners**

Specialized tools with deep language knowledge:

- **Bandit** (Python) - Security linter for Python code
  - Detects common Python security issues
  - Checks for insecure function usage (`pickle`, `eval`, `exec`)
  - Identifies weak cryptography patterns
  - Flags SQL injection vulnerabilities

- **Brakeman** (Ruby) - Rails security scanner
  - Detects SQL injection, XSS, CSRF
  - Checks for mass assignment vulnerabilities
  - Analyzes template injection risks

- **SpotBugs** (Java) - Bytecode analysis for Java
  - Detects common bug patterns
  - Security-focused plugin (Find Security Bugs)
  - Analyzes compiled code and dependencies

- **ESLint Security Plugin** (JavaScript) - Security rules for JavaScript/TypeScript
  - Detects XSS vulnerabilities
  - Identifies unsafe `eval()` usage
  - Checks for prototype pollution risks

**3. LLM-Specific Code Analysis**

Custom linters designed to catch LLM-generated code patterns:

```python
# Example: Custom Semgrep rules for LLM code anti-patterns

rules:
  - id: llm-deprecated-crypto-md5
    pattern: hashlib.md5(...)
    message: "LLM-generated code using deprecated MD5 hashing. Use SHA256 or bcrypt instead."
    severity: ERROR
    languages: [python]
    
  - id: llm-insecure-deserialization
    pattern: pickle.loads($DATA)
    message: "LLM-generated code using insecure pickle deserialization. Use JSON instead."
    severity: ERROR
    languages: [python]
    
  - id: llm-sql-string-concat
    patterns:
      - pattern: f"SELECT ... {$VAR}"
      - pattern: "SELECT ... " + $VAR
    message: "LLM-generated SQL query vulnerable to injection. Use parameterized queries."
    severity: ERROR
    languages: [python]
    
  - id: llm-weak-random
    pattern: random.random()
    message: "LLM-generated code using predictable RNG. Use secrets module for security-sensitive operations."
    severity: WARNING
    languages: [python]
```

**4. Dependency Vulnerability Scanners**

Tools that detect outdated or vulnerable libraries:

- **OWASP Dependency-Check** - Cross-language dependency scanner
  - Checks dependencies against CVE database
  - Supports Java, .NET, Python, JavaScript, Ruby
  - Generates detailed vulnerability reports

- **Snyk** - Developer-first security platform
  - Real-time dependency vulnerability scanning
  - Automated fix pull requests
  - License compliance checking
  - Integrates with IDEs and CI/CD

- **npm audit** (JavaScript) - Built-in Node.js scanner
  - Scans package.json dependencies
  - Reports known vulnerabilities
  - Suggests version updates

- **pip-audit** (Python) - Python dependency scanner
  - Checks installed packages against PyPI Advisory Database
  - Lightweight command-line tool

### Integration Points

**1. IDE Integration (Real-Time Feedback)**

Catch vulnerabilities as code is generated:

```json
// VSCode settings.json example
{
  "sonarlint.connectedMode.servers": [
    {
      "serverId": "sonarqube",
      "serverUrl": "https://sonarqube.company.com",
      "token": "${SONAR_TOKEN}"
    }
  ],
  "semgrep.configPath": ".semgrep/llm-security-rules.yml",
  "python.linting.banditEnabled": true,
  "python.linting.banditArgs": ["--configfile", ".bandit"]
}
```

**Benefits:**
- Immediate feedback on LLM suggestions
- Developers see issues before code review
- Reduces security debt accumulation

**2. Pre-Commit Hooks (Prevent Vulnerable Commits)**

Block commits containing security issues:

```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Running security scans on staged files..."

# Run Bandit on Python files
if git diff --cached --name-only --diff-filter=ACM | grep '\.py$' > /dev/null; then
  echo "Scanning Python files with Bandit..."
  bandit -r $(git diff --cached --name-only --diff-filter=ACM | grep '\.py$') -f json -o bandit-report.json
  
  if [ $? -ne 0 ]; then
    echo "❌ Bandit found security issues. Fix before committing."
    cat bandit-report.json
    exit 1
  fi
fi

# Run Semgrep on all changed files
echo "Running Semgrep security scan..."
semgrep --config=.semgrep/llm-security-rules.yml --error --json $(git diff --cached --name-only --diff-filter=ACM)

if [ $? -ne 0 ]; then
  echo "❌ Semgrep found security issues. Fix before committing."
  exit 1
fi

echo "✅ Security scans passed"
exit 0
```

**3. CI/CD Pipeline Integration (Mandatory Gates)**

Enforce security validation before merge/deployment:

```yaml
# GitHub Actions example
name: Security Scan

on:
  pull_request:
    branches: [main, develop]

jobs:
  security-analysis:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Run CodeQL Analysis
        uses: github/codeql-action/analyze@v2
        with:
          languages: python, javascript
      
      - name: Run Semgrep
        uses: returntocorp/semgrep-action@v1
        with:
          config: .semgrep/llm-security-rules.yml
      
      - name: Run Snyk Dependency Scan
        uses: snyk/actions/python@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high
      
      - name: SonarQube Scan
        uses: sonarsource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
      
      - name: Fail Build on High Severity Issues
        run: |
          if [ -f security-issues.json ]; then
            HIGH_ISSUES=$(jq '.high_severity_count' security-issues.json)
            if [ $HIGH_ISSUES -gt 0 ]; then
              echo "❌ Found $HIGH_ISSUES high severity security issues"
              exit 1
            fi
          fi
```

### Configuration Best Practices

**1. Customize Rules for LLM Code Patterns**

Focus on vulnerabilities LLMs commonly introduce:

```yaml
# .semgrep/llm-focus-rules.yml
rules:
  - id: detect-deprecated-apis
    patterns:
      - pattern-either:
          - pattern: hashlib.md5(...)
          - pattern: hashlib.sha1(...)
          - pattern: random.random()
          - pattern: eval(...)
          - pattern: exec(...)
    message: "Deprecated or insecure API commonly suggested by LLMs"
    severity: ERROR
    
  - id: detect-insecure-serialization
    patterns:
      - pattern-either:
          - pattern: pickle.loads(...)
          - pattern: yaml.load(..., Loader=yaml.Loader)
          - pattern: marshal.loads(...)
    message: "Insecure deserialization pattern"
    severity: ERROR
    
  - id: detect-sql-injection-patterns
    patterns:
      - pattern: execute(f"... {$VAR} ...")
      - pattern: execute("... " + $VAR + " ...")
      - pattern-not: execute("...", ...)  # Parameterized queries OK
    message: "Potential SQL injection via string concatenation"
    severity: ERROR
```

**2. Set Severity Thresholds**

Block high/critical issues, warn on medium:

```json
{
  "sonarqube": {
    "qualityGate": {
      "conditions": [
        {"metric": "vulnerabilities", "operator": "GT", "error": 0},
        {"metric": "security_hotspots", "operator": "GT", "warning": 3}
      ]
    }
  },
  "snyk": {
    "severity-threshold": "high",
    "fail-on": "all"
  },
  "semgrep": {
    "error-on": "ERROR",
    "warn-on": "WARNING"
  }
}
```

**3. Generate Actionable Reports**

Provide context for developers:

```python
# Custom report generator for LLM code analysis
import json

def generate_llm_security_report(scan_results):
    """Generate developer-friendly report for LLM code issues"""
    
    report = {
        'summary': {
            'total_issues': len(scan_results),
            'critical': len([i for i in scan_results if i['severity'] == 'CRITICAL']),
            'high': len([i for i in scan_results if i['severity'] == 'HIGH']),
            'medium': len([i for i in scan_results if i['severity'] == 'MEDIUM'])
        },
        'llm_specific_patterns': [],
        'recommended_actions': []
    }
    
    # Categorize LLM-specific issues
    llm_patterns = {
        'deprecated_crypto': [],
        'insecure_deserialization': [],
        'sql_injection': [],
        'weak_random': []
    }
    
    for issue in scan_results:
        if 'md5' in issue['message'].lower() or 'sha1' in issue['message'].lower():
            llm_patterns['deprecated_crypto'].append(issue)
        elif 'pickle' in issue['message'].lower():
            llm_patterns['insecure_deserialization'].append(issue)
        elif 'sql' in issue['message'].lower():
            llm_patterns['sql_injection'].append(issue)
        elif 'random' in issue['message'].lower():
            llm_patterns['weak_random'].append(issue)
    
    # Generate recommendations
    if llm_patterns['deprecated_crypto']:
        report['recommended_actions'].append({
            'category': 'Deprecated Cryptography',
            'action': 'Replace MD5/SHA1 with SHA256 or bcrypt',
            'affected_files': [i['file'] for i in llm_patterns['deprecated_crypto']],
            'documentation': 'https://wiki.company.com/secure-crypto-standards'
        })
    
    if llm_patterns['sql_injection']:
        report['recommended_actions'].append({
            'category': 'SQL Injection',
            'action': 'Use parameterized queries instead of string concatenation',
            'affected_files': [i['file'] for i in llm_patterns['sql_injection']],
            'documentation': 'https://wiki.company.com/sql-injection-prevention'
        })
    
    return report
```

## Limitations & Trade-offs

### 1. False Positives

**Problem:** Static analysis tools flag code that isn't actually vulnerable

**Example:**
```python
# Flagged as SQL injection, but actually safe
query = "SELECT * FROM users WHERE id = ?"  # Parameterized
cursor.execute(query, (user_id,))  # Safe usage

# Tool may still warn if it doesn't track dataflow correctly
```

**Impact:**
- Developer fatigue from too many warnings
- Real issues missed in noise
- Tools disabled or ignored

**Mitigation:**
- Tune rulesets to reduce noise
- Use suppression annotations for false positives
- Regularly review and update rules

### 2. False Negatives

**Problem:** Tools miss real vulnerabilities

**Causes:**
- Complex dataflow tools can't trace
- Novel vulnerability patterns not in rulesets
- Language-specific edge cases
- Obfuscated code paths

**Example:**
```python
# Subtle SQL injection via f-string
def get_table_name(user_input):
    return f"user_{user_input}_data"

table = get_table_name(request.args['table'])
query = f"SELECT * FROM {table}"  # Vulnerable, but hard to detect
db.execute(query)
```

**Mitigation:**
- Layer multiple tools (different engines catch different issues)
- Combine with dynamic testing
- Manual security review for critical code

### 3. Performance Overhead

**Impact:** Scanning large codebases takes time

**Typical scan times:**
- Small project (1K-10K LOC): 1-5 minutes
- Medium project (10K-100K LOC): 5-30 minutes
- Large project (100K+ LOC): 30 minutes - hours

**Optimization strategies:**
- Incremental scanning (only changed files)
- Parallel analysis
- Caching previous results
- Running scans on CI servers, not developer machines

### 4. Configuration Complexity

**Challenge:** Effective static analysis requires tuning

**Effort required:**
- Learn tool-specific rule syntax
- Understand false positive patterns
- Customize rules for codebase
- Maintain rules as code evolves

**Mitigation:**
- Start with default rulesets
- Incrementally add custom rules
- Share configurations across teams
- Use community rule repositories

### 5. Language/Framework Coverage Gaps

**Limitation:** Not all languages/frameworks equally supported

**Well-supported:** Python, JavaScript/TypeScript, Java, C/C++, C#
**Limited support:** Rust, Go, Kotlin (improving)
**Emerging:** Swift, Dart, Julia

**Impact:** LLM-generated code in niche languages may lack tooling

## Testing & Validation

### Validate Tool Effectiveness

**Test with known vulnerable code:**

```python
# test_static_analysis.py
# Intentionally vulnerable code snippets to verify scanner catches them

def test_scanner_detects_sql_injection():
    """Verify scanner catches SQL injection patterns"""
    
    vulnerable_code = '''
def get_user(email):
    query = f"SELECT * FROM users WHERE email = '{email}'"
    return db.execute(query)
'''
    
    # Run scanner
    results = run_semgrep_scan(vulnerable_code)
    
    # Assert SQL injection detected
    assert any('sql' in r['message'].lower() and 'injection' in r['message'].lower() 
               for r in results), "Scanner failed to detect SQL injection"

def test_scanner_detects_insecure_deserialization():
    """Verify scanner catches pickle vulnerabilities"""
    
    vulnerable_code = '''
import pickle

def load_data(data):
    return pickle.loads(data)
'''
    
    results = run_bandit_scan(vulnerable_code)
    
    assert any('pickle' in r['message'].lower() for r in results), \
        "Scanner failed to detect insecure deserialization"

def test_scanner_detects_weak_crypto():
    """Verify scanner catches deprecated hashing"""
    
    vulnerable_code = '''
import hashlib

def hash_password(password):
    return hashlib.md5(password.encode()).hexdigest()
'''
    
    results = run_semgrep_scan(vulnerable_code)
    
    assert any('md5' in r['message'].lower() for r in results), \
        "Scanner failed to detect weak cryptography"
```

### Monitor Scan Coverage

Track which code gets analyzed:

```python
def calculate_scan_coverage(scan_results, total_codebase_files):
    """Measure percentage of codebase analyzed"""
    
    scanned_files = set(r['file'] for r in scan_results)
    coverage = len(scanned_files) / len(total_codebase_files) * 100
    
    return {
        'coverage_percentage': coverage,
        'scanned_files': len(scanned_files),
        'total_files': len(total_codebase_files),
        'unscanned_files': list(set(total_codebase_files) - scanned_files)
    }
```

### Track Remediation Metrics

Measure how quickly issues are fixed:

```python
def track_remediation_metrics(issue_database):
    """Calculate time-to-fix for security issues"""
    
    metrics = {
        'avg_time_to_fix_critical': 0,
        'avg_time_to_fix_high': 0,
        'unresolved_critical': 0,
        'unresolved_high': 0
    }
    
    for issue in issue_database:
        age_days = (datetime.now() - issue['discovered']).days
        
        if issue['resolved']:
            fix_time = (issue['resolved'] - issue['discovered']).days
            if issue['severity'] == 'CRITICAL':
                metrics['avg_time_to_fix_critical'] += fix_time
            elif issue['severity'] == 'HIGH':
                metrics['avg_time_to_fix_high'] += fix_time
        else:
            if issue['severity'] == 'CRITICAL':
                metrics['unresolved_critical'] += 1
            elif issue['severity'] == 'HIGH':
                metrics['unresolved_high'] += 1
    
    return metrics
```

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Organizations should implement: Code Analysis - Static Analysis Tools - SonarQube, Semgrep, CodeQL to catch common patterns; LLM-specific linters: Flag deprecated APIs, known-vulnerable functions; Dependency scanners: npm audit, Dependabot, Snyk to catch outdated libraries."
> — [[sources/bibliography#LLMs in Cybersecurity]], p. 88-94
