---
title: "Pre-commit Security Hooks"
tags:
  - type/mitigation
  - target/llm
  - target/code
  - target/supply-chain
  - source/llms-in-cybersecurity
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Pre-commit Security Hooks

## Summary

Pre-commit security hooks are automated scripts that execute before code commits, blocking commits containing security violations, known-vulnerable patterns, or policy violations. By intercepting insecure code at commit time—before it reaches code review or CI/CD—pre-commit hooks provide the earliest possible intervention point in the development lifecycle. When applied to LLM-assisted development, pre-commit hooks are critical because they catch insecure code patterns that LLMs systematically suggest (SQL injection, deprecated cryptography, insecure deserialization) immediately, providing instant feedback to developers and preventing accumulation of security debt.

## Defends Against

| ID | Technique | Description |
|----|------|-------------|
| | [[techniques/llm-code-generation-vulnerabilities]] | Blocks commits containing LLM-suggested insecure patterns before they enter codebase |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Detects backdoored code patterns (hardcoded credentials, suspicious imports) at commit time |
| | [[techniques/backdoor-poisoning]] | Prevents malicious code (admin bypasses, hidden functionality) from being committed |

## Implementation

### Git Hooks Architecture

**Hook Types:**

Git provides several hook points. For security, focus on:

- **pre-commit** - Runs before commit is created (most common for security checks)
- **commit-msg** - Validates commit message format
- **pre-push** - Runs before pushing to remote (slower checks acceptable here)

**Installation Methods:**

**1. Manual Hook Installation**

```bash
# Copy hook script to .git/hooks/
cp security-hooks/pre-commit .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

**Limitation:** Requires manual setup per developer, doesn't track with repository

**2. pre-commit Framework (Recommended)**

```bash
# Install pre-commit framework
pip install pre-commit

# Create .pre-commit-config.yaml in repo root
cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: local
    hooks:
      - id: security-scan
        name: Security Scanner
        entry: scripts/security-scan.sh
        language: script
        pass_filenames: false
        
  - repo: https://github.com/PyCQA/bandit
    rev: '1.7.5'
    hooks:
      - id: bandit
        args: ['-c', '.bandit', '-r', '.']
        
  - repo: https://github.com/returntocorp/semgrep
    rev: 'v1.50.0'
    hooks:
      - id: semgrep
        args: ['--config', '.semgrep/llm-security-rules.yml', '--error']
EOF

# Install hooks
pre-commit install

# Test (run on all files)
pre-commit run --all-files
```

**Advantages:**
- Hooks tracked in repository
- Automatic installation via `pre-commit install`
- Easy to update across team
- Community repository of hook configurations

### Security Check Categories

**1. Static Code Analysis**

Scan staged files for vulnerability patterns:

```bash
#!/bin/bash
# scripts/security-scan.sh

set -e

echo "🔍 Running security scans on staged files..."

# Get list of staged Python files
PYTHON_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.py$' || true)

if [ -n "$PYTHON_FILES" ]; then
  echo "📝 Scanning Python files with Bandit..."
  bandit -r $PYTHON_FILES -f json -o bandit-report.json
  
  if [ $? -ne 0 ]; then
    echo "❌ Bandit found security issues:"
    cat bandit-report.json | jq '.results[] | "\(.issue_text) in \(.filename):\(.line_number)"'
    exit 1
  fi
  
  echo "📝 Scanning with Semgrep..."
  semgrep --config .semgrep/llm-security-rules.yml --error $PYTHON_FILES
  
  if [ $? -ne 0 ]; then
    echo "❌ Semgrep found security issues"
    exit 1
  fi
fi

# Get JavaScript files
JS_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep '\.js$\|\.ts$' || true)

if [ -n "$JS_FILES" ]; then
  echo "📝 Scanning JavaScript files with ESLint security plugin..."
  npx eslint --config .eslintrc-security.json $JS_FILES
  
  if [ $? -ne 0 ]; then
    echo "❌ ESLint found security issues"
    exit 1
  fi
fi

echo "✅ Security scans passed"
exit 0
```

**2. Secret Detection**

Prevent committing hardcoded credentials:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
        exclude: package-lock.json
```

**Secret patterns detected:**
- API keys (AWS, GCP, Azure, GitHub, etc.)
- Private keys (RSA, SSH, PGP)
- Database credentials
- OAuth tokens
- JWT secrets
- Hardcoded passwords

**Example detection:**

```python
# This would be blocked by pre-commit hook:
API_KEY = "sk-1234567890abcdef"  # ❌ Detected as API key

# Proper approach:
import os
API_KEY = os.environ.get("API_KEY")  # ✅ Passes
```

**3. Dependency Vulnerability Check**

Scan dependency changes for known CVEs:

```bash
#!/bin/bash
# Detect if requirements.txt or package.json changed

if git diff --cached --name-only | grep -q 'requirements.txt\|package.json\|package-lock.json\|Gemfile.lock'; then
  echo "📦 Dependencies changed, running vulnerability scan..."
  
  # Python dependencies
  if git diff --cached --name-only | grep -q 'requirements.txt'; then
    pip-audit -r requirements.txt --vulnerability-service pypi
    if [ $? -ne 0 ]; then
      echo "❌ Vulnerable Python dependencies detected"
      exit 1
    fi
  fi
  
  # npm dependencies
  if git diff --cached --name-only | grep -q 'package.json\|package-lock.json'; then
    npm audit --audit-level=high
    if [ $? -ne 0 ]; then
      echo "❌ Vulnerable npm dependencies detected"
      exit 1
    fi
  fi
fi
```

**4. LLM-Specific Pattern Detection**

Custom rules targeting common LLM code anti-patterns:

```yaml
# .semgrep/llm-security-rules.yml
rules:
  - id: llm-deprecated-md5
    pattern-either:
      - pattern: hashlib.md5(...)
      - pattern: hashlib.sha1(...)
    message: |
      LLM-generated code using deprecated cryptographic hash.
      
      ❌ Don't use MD5 or SHA1 for security purposes.
      ✅ Use SHA256 for hashing or bcrypt/Argon2 for passwords.
      
      LLMs frequently suggest MD5 because it appears in older training data.
    severity: ERROR
    languages: [python]
    
  - id: llm-insecure-pickle
    pattern: pickle.loads($DATA)
    message: |
      LLM-generated code using insecure deserialization (pickle).
      
      ❌ pickle.loads() allows arbitrary code execution.
      ✅ Use json.loads() for safe deserialization.
    severity: ERROR
    languages: [python]
    
  - id: llm-sql-injection
    patterns:
      - pattern-either:
          - pattern: execute(f"... {$VAR} ...")
          - pattern: execute("... " + $VAR)
          - pattern: execute(f"... '{$VAR}' ...")
      - pattern-not: execute("...", ...)
    message: |
      LLM-generated SQL query vulnerable to injection.
      
      ❌ Never concatenate variables into SQL strings.
      ✅ Use parameterized queries:
      
      cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
    severity: ERROR
    languages: [python]
    
  - id: llm-weak-random
    pattern-either:
      - pattern: random.randint(...)
      - pattern: random.random()
      - pattern: random.choice(...)
    message: |
      LLM-generated code using predictable random number generator.
      
      ❌ random module is not cryptographically secure.
      ✅ Use secrets module for security-sensitive operations:
      
      import secrets
      token = secrets.token_hex(16)
    severity: WARNING
    languages: [python]
    fix: |
      import secrets
      # Replace random.randint() with secrets.randbelow()
      # Replace random.random() with secrets.SystemRandom().random()
    
  - id: llm-hardcoded-admin-bypass
    pattern-either:
      - pattern: if $USER == "admin": ...
      - pattern: if $ROLE == "debug": ...
      - pattern: if $MODE == "bypass": ...
    message: |
      Potential backdoor: hardcoded admin/debug bypass.
      
      LLMs may generate "debug" backdoors in authentication code.
      Remove before production deployment.
    severity: ERROR
    languages: [python, javascript]
```

**5. License Compliance Check**

Ensure dependencies have compatible licenses:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: license-check
        name: License Compliance Check
        entry: scripts/check-licenses.sh
        language: script
        pass_filenames: false
```

```bash
#!/bin/bash
# scripts/check-licenses.sh

# Check Python package licenses
if command -v pip-licenses &> /dev/null; then
  FORBIDDEN_LICENSES="GPL-3.0,AGPL-3.0"
  
  VIOLATIONS=$(pip-licenses --format=json | jq -r ".[] | select(.License | test(\"$FORBIDDEN_LICENSES\")) | .Name")
  
  if [ -n "$VIOLATIONS" ]; then
    echo "❌ Forbidden licenses detected:"
    echo "$VIOLATIONS"
    exit 1
  fi
fi
```

**6. Code Formatting & Linting**

Enforce consistent code style (prevents obfuscation):

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/psf/black
    rev: 23.11.0
    hooks:
      - id: black
        language_version: python3.11
  
  - repo: https://github.com/PyCQA/flake8
    rev: 6.1.0
    hooks:
      - id: flake8
        args: ['--max-line-length=100', '--extend-ignore=E203']
  
  - repo: https://github.com/pre-commit/mirrors-eslint
    rev: v8.54.0
    hooks:
      - id: eslint
        files: \.(js|ts)$
```

### Performance Optimization

**Problem:** Security scans can slow down commits significantly

**Solutions:**

**1. Incremental Scanning**

Only scan changed files, not entire codebase:

```bash
# Only scan staged files
CHANGED_FILES=$(git diff --cached --name-only --diff-filter=ACM)

semgrep --config .semgrep/llm-security-rules.yml $CHANGED_FILES
```

**2. Caching Results**

```yaml
# .pre-commit-config.yaml
default_stages: [commit]
default_language_version:
  python: python3.11
  
repos:
  - repo: local
    hooks:
      - id: bandit-cached
        name: Bandit (cached)
        entry: bandit
        language: system
        types: [python]
        # pre-commit caches results automatically
```

**3. Parallel Execution**

```bash
# Run multiple scans in parallel
(semgrep --config .semgrep/llm-security-rules.yml src/ &)
(bandit -r src/ &)
(npm audit &)

# Wait for all background jobs
wait

# Check exit codes
if [ $? -ne 0 ]; then
  exit 1
fi
```

**4. Fast vs. Slow Checks**

Run fast checks in pre-commit, slow checks in pre-push:

```yaml
# .pre-commit-config.yaml

# Fast checks (pre-commit)
repos:
  - repo: local
    hooks:
      - id: quick-security-scan
        name: Quick Security Scan
        entry: semgrep --config .semgrep/critical-only.yml
        language: system
        stages: [commit]

# Slow checks (pre-push)
  - repo: local
    hooks:
      - id: full-security-scan
        name: Full Security Scan
        entry: scripts/full-security-scan.sh
        language: script
        stages: [push]
```

### Override Mechanisms

**Sometimes developers need to bypass hooks (emergencies, false positives)**

**Safe override approach:**

```bash
# Allow bypass with explanation
git commit --no-verify -m "Emergency hotfix - security scan bypass approved by SecTeam (Ticket: SEC-12345)"
```

**Track bypasses:**

```bash
# Log all --no-verify commits
git config alias.safe-commit '!f() {
  if git commit "$@"; then
    if echo "$@" | grep -q "\-\-no-verify"; then
      echo "⚠️  Security bypass logged to audit trail"
      echo "$(date),$(git log -1 --format=%H),$(whoami),$@" >> .security-bypass-log.csv
    fi
  fi
}; f'
```

**Policy:**
- Bypasses require security team approval (ticket number in commit message)
- All bypasses logged and reviewed weekly
- Repeated bypasses trigger re-training

### Team-Wide Enforcement

**Ensure all developers have hooks installed:**

```bash
# scripts/setup-dev-environment.sh

echo "Setting up development environment..."

# Install pre-commit framework
pip install pre-commit

# Install hooks
pre-commit install
pre-commit install --hook-type pre-push

# Verify installation
if pre-commit run --all-files; then
  echo "✅ Development environment setup complete"
else
  echo "❌ Security scans found issues - fix before continuing development"
  exit 1
fi
```

**Add to onboarding checklist:**
- [ ] Clone repository
- [ ] Run `scripts/setup-dev-environment.sh`
- [ ] Verify pre-commit hooks installed
- [ ] Make test commit to confirm hooks work

**CI/CD backstop:**

Even with pre-commit hooks, run same checks in CI/CD (defense in depth):

```yaml
# .github/workflows/security-scan.yml
name: Security Scan

on:
  pull_request:
    branches: [main, develop]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Run pre-commit hooks (backstop)
        run: |
          pip install pre-commit
          pre-commit run --all-files
      
      # If developer bypassed pre-commit, this catches it
```

## Limitations & Trade-offs

### 1. Performance Impact on Developer Workflow

**Problem:** Slow hooks disrupt flow state

**Typical scan times:**
- Secret detection: <1 second
- Linting: 1-3 seconds
- Static analysis (Bandit/Semgrep): 3-10 seconds
- Dependency scanning: 5-30 seconds

**Total:** Can add 10-45 seconds per commit

**Impact:**
- Developers frustrated by wait time
- May disable hooks to "move faster"
- Reduces commit frequency (worse: larger commits)

**Mitigation:**
- Optimize: only scan changed files
- Move slow checks to pre-push hook
- Use caching aggressively
- Run full scans in CI/CD, not pre-commit

### 2. False Positives Cause Alert Fatigue

**Problem:** Overly strict rules block legitimate code

**Example:**
```python
# Legitimate use case blocked by overly strict rule
import hashlib

# MD5 for non-security purpose (content checksums)
file_hash = hashlib.md5(file_content).hexdigest()  # ❌ Blocked

# But this is safe - not used for crypto
```

**Impact:**
- Developers bypass hooks to "get work done"
- Trust in security tools erodes
- Real issues missed when noise too high

**Mitigation:**
- Tune rulesets to reduce false positives
- Allow suppression with justification comments
- Regularly review and refine rules
- Prioritize high-confidence detections

### 3. Hooks Can Be Bypassed

**Reality:** `git commit --no-verify` skips all hooks

**Risk:**
- Malicious insider intentionally bypasses
- Developer in rush skips without thinking
- No enforcement if developer doesn't install hooks

**Mitigation:**
- Policy: all bypasses require approval + logging
- CI/CD runs same checks (catches bypassed commits)
- Code review as second layer
- Audit bypass logs weekly

### 4. Maintenance Burden

**Challenge:** Rules and tools require ongoing updates

**Effort required:**
- Update Semgrep rules as new LLM patterns emerge
- Update dependency scanners when CVE databases change
- Fix broken hooks when tools update
- Tune rules based on false positive feedback

**Mitigation:**
- Assign ownership (security team or champion)
- Quarterly rule review and update cycle
- Monitor tool changelogs for breaking changes
- Version-pin tools in pre-commit config

### 5. Limited Language/Framework Coverage

**Reality:** Not all languages have good security tooling

**Well-supported:** Python, JavaScript, Java, Ruby
**Limited:** Go, Rust, Kotlin, Swift
**Emerging:** Dart, Julia, Elixir

**Impact:** LLM-generated code in niche languages may bypass detection

**Mitigation:**
- Use general-purpose tools (Semgrep works on many languages)
- Custom regex-based checks for unsupported languages
- Emphasize code review for less-supported stacks

## Testing & Validation

### Verify Hook Effectiveness

**Test with known insecure code:**

```bash
#!/bin/bash
# test-security-hooks.sh
# Verify pre-commit hooks catch known vulnerabilities

echo "Testing pre-commit security hooks..."

# Create temporary branch
git checkout -b test-security-hooks

# Test 1: SQL injection detection
cat > test_sql_injection.py << 'EOF'
def get_user(user_id):
    query = f"SELECT * FROM users WHERE id = {user_id}"
    return db.execute(query)
EOF

git add test_sql_injection.py
if git commit -m "Test: SQL injection" 2>&1 | grep -q "SQL injection\|semgrep"; then
  echo "✅ Test 1 passed: SQL injection detected"
else
  echo "❌ Test 1 failed: SQL injection NOT detected"
  exit 1
fi

git reset --hard HEAD

# Test 2: Weak cryptography detection
cat > test_weak_crypto.py << 'EOF'
import hashlib
def hash_password(password):
    return hashlib.md5(password.encode()).hexdigest()
EOF

git add test_weak_crypto.py
if git commit -m "Test: Weak crypto" 2>&1 | grep -q "md5\|deprecated"; then
  echo "✅ Test 2 passed: Weak crypto detected"
else
  echo "❌ Test 2 failed: Weak crypto NOT detected"
  exit 1
fi

git reset --hard HEAD

# Test 3: Secret detection
cat > test_secret.py << 'EOF'
API_KEY = "sk-1234567890abcdefghijklmnopqrstuvwxyz"
EOF

git add test_secret.py
if git commit -m "Test: Secret" 2>&1 | grep -q "secret\|API"; then
  echo "✅ Test 3 passed: Hardcoded secret detected"
else
  echo "❌ Test 3 failed: Hardcoded secret NOT detected"
  exit 1
fi

# Cleanup
git checkout main
git branch -D test-security-hooks

echo "All tests passed! Security hooks are working."
```

**Run as part of CI/CD:**

```yaml
# .github/workflows/test-hooks.yml
name: Test Pre-commit Hooks

on:
  schedule:
    - cron: '0 0 * * 0'  # Weekly

jobs:
  test-hooks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: bash test-security-hooks.sh
```

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Secure Development Practices: Pre-commit hooks: Block known-insecure patterns; Security gates: Require manual approval for crypto, auth, file I/O code. Code-generating LLMs introduce systemic security vulnerabilities by learning from historical codebases containing outdated, deprecated, and inherently insecure programming practices."
> — [[sources/bibliography#LLMs in Cybersecurity]], p. 88-94
