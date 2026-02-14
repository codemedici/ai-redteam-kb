---
title: "Dependency Security Scanning"
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

# Dependency Security Scanning

## Summary

Dependency security scanning automatically detects known vulnerabilities, licensing issues, and malicious packages in project dependencies by continuously monitoring libraries against vulnerability databases (CVE, NVD, GitHub Advisory Database). When applied to LLM-generated code, dependency scanning is critical because code-generating LLMs are trained on historical codebases and systematically over-represent outdated, deprecated libraries with known security flaws. LLMs frequently suggest vulnerable package versions because those versions appeared more frequently in training data, making automated dependency scanning a mandatory control for projects using LLM code assistants.

## Defends Against

| ID | Technique | Description |
|----|------|-------------|
| | [[techniques/llm-code-generation-vulnerabilities]] | Detects when LLMs suggest outdated libraries with known CVEs |
| | [[techniques/third-party-model-risks]] | Identifies vulnerable dependencies in externally-sourced code |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Catches backdoored or malicious packages injected through supply chain poisoning |
| | [[techniques/backdoor-poisoning]] | Detects known-compromised package versions (e.g., event-stream incident) |

## Implementation

### Scanning Tools by Ecosystem

**JavaScript/Node.js**

- **npm audit** (built-in)
  ```bash
  # Scan package.json dependencies
  npm audit
  
  # Scan and auto-fix (updates package-lock.json)
  npm audit fix
  
  # Show only high/critical vulnerabilities
  npm audit --audit-level=high
  
  # Generate JSON report
  npm audit --json > npm-audit-report.json
  ```

- **Yarn audit** (built-in)
  ```bash
  # Scan yarn.lock
  yarn audit
  
  # JSON output
  yarn audit --json
  ```

- **Snyk** (commercial, free tier available)
  ```bash
  # Install Snyk CLI
  npm install -g snyk
  
  # Authenticate
  snyk auth
  
  # Test project dependencies
  snyk test
  
  # Monitor project (continuous tracking)
  snyk monitor
  
  # Fix vulnerabilities automatically
  snyk fix
  ```

**Python**

- **pip-audit** (official Python tool)
  ```bash
  # Install pip-audit
  pip install pip-audit
  
  # Scan installed packages
  pip-audit
  
  # Scan requirements.txt
  pip-audit -r requirements.txt
  
  # Generate JSON report
  pip-audit --format json > audit-report.json
  
  # Set severity threshold
  pip-audit --vulnerability-service pypi --require-hashes
  ```

- **Safety** (PyUp.io)
  ```bash
  # Install Safety
  pip install safety
  
  # Check installed packages
  safety check
  
  # Check requirements file
  safety check -r requirements.txt
  
  # JSON output
  safety check --json
  ```

- **Snyk** (supports Python)
  ```bash
  snyk test --file=requirements.txt
  snyk monitor --file=requirements.txt
  ```

**Java/Maven**

- **OWASP Dependency-Check**
  ```xml
  <!-- Add to pom.xml -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.owasp</groupId>
        <artifactId>dependency-check-maven</artifactId>
        <version>8.4.0</version>
        <executions>
          <execution>
            <goals>
              <goal>check</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <failBuildOnCVSS>7</failBuildOnCVSS>  <!-- Fail on high severity -->
        </configuration>
      </plugin>
    </plugins>
  </build>
  ```
  
  ```bash
  # Run check
  mvn dependency-check:check
  ```

- **Snyk Maven Plugin**
  ```bash
  snyk test --file=pom.xml
  ```

**Ruby**

- **bundler-audit**
  ```bash
  # Install
  gem install bundler-audit
  
  # Update vulnerability database
  bundle-audit update
  
  # Check Gemfile.lock
  bundle-audit check
  ```

**Go**

- **govulncheck** (official Go tool)
  ```bash
  # Install
  go install golang.org/x/vuln/cmd/govulncheck@latest
  
  # Scan current module
  govulncheck ./...
  
  # Scan specific package
  govulncheck github.com/example/package
  ```

**Rust**

- **cargo-audit**
  ```bash
  # Install
  cargo install cargo-audit
  
  # Scan Cargo.lock
  cargo audit
  
  # Fix vulnerabilities
  cargo audit fix
  ```

### Multi-Language Platform Solutions

**Snyk (Commercial)**

- Supports 10+ languages and package managers
- IDE integrations (VSCode, IntelliJ, Visual Studio)
- CI/CD integrations (GitHub Actions, GitLab CI, Jenkins)
- Automated fix pull requests
- License compliance scanning
- Container scanning

**GitHub Dependabot (Free for public repos)**

- Automatically scans dependencies in GitHub repos
- Creates pull requests to update vulnerable dependencies
- Supports npm, pip, bundler, composer, Maven, Gradle, Go modules, Cargo
- Native GitHub integration

**Sonatype Nexus Lifecycle (Enterprise)**

- Policy-based dependency governance
- Multi-language support
- Repository management integration
- Tracks transitive dependencies

**JFrog Xray (Enterprise)**

- Deep recursive scanning
- Binary analysis (not just metadata)
- Multi-registry support

### CI/CD Pipeline Integration

**GitHub Actions Example**

```yaml
name: Dependency Security Scan

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]
  schedule:
    # Run weekly to catch newly-disclosed vulnerabilities
    - cron: '0 0 * * 0'

jobs:
  scan-dependencies:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      # Python dependencies
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Install pip-audit
        run: pip install pip-audit
      
      - name: Scan Python dependencies
        run: |
          pip-audit -r requirements.txt --format json > python-audit.json
          pip-audit -r requirements.txt --vulnerability-service pypi
        continue-on-error: false
      
      # JavaScript dependencies
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Scan npm dependencies
        run: |
          npm audit --audit-level=high --json > npm-audit.json
          npm audit --audit-level=high
        continue-on-error: false
      
      # Snyk scan (multi-language)
      - name: Run Snyk
        uses: snyk/actions@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high --all-projects
      
      # Upload scan results
      - name: Upload audit reports
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: dependency-audit-reports
          path: |
            python-audit.json
            npm-audit.json
```

**GitLab CI Example**

```yaml
dependency_scanning:
  stage: test
  image: python:3.11
  
  script:
    # Python scanning
    - pip install pip-audit safety
    - pip-audit -r requirements.txt
    - safety check -r requirements.txt
    
    # npm scanning
    - npm install
    - npm audit --audit-level=high
  
  artifacts:
    reports:
      dependency_scanning: gl-dependency-scanning-report.json
  
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
    - if: '$CI_COMMIT_BRANCH == "main"'
```

**Pre-commit Hook Example**

```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "🔍 Scanning dependencies for vulnerabilities..."

# Check if requirements.txt changed
if git diff --cached --name-only | grep 'requirements.txt' > /dev/null; then
  echo "📦 Python dependencies changed, running pip-audit..."
  pip-audit -r requirements.txt --vulnerability-service pypi
  
  if [ $? -ne 0 ]; then
    echo "❌ Vulnerable Python dependencies detected. Fix before committing."
    exit 1
  fi
fi

# Check if package.json changed
if git diff --cached --name-only | grep 'package.json|package-lock.json' > /dev/null; then
  echo "📦 npm dependencies changed, running npm audit..."
  npm audit --audit-level=high
  
  if [ $? -ne 0 ]; then
    echo "❌ Vulnerable npm dependencies detected. Fix before committing."
    exit 1
  fi
fi

echo "✅ Dependency scan passed"
exit 0
```

### Policy Configuration

**Define Acceptable Risk Thresholds**

```json
{
  "dependency_policy": {
    "fail_build_on": {
      "critical": true,
      "high": true,
      "medium": false,
      "low": false
    },
    "max_age_days": {
      "critical": 7,
      "high": 30,
      "medium": 90
    },
    "allow_list": [
      {
        "package": "lodash",
        "version": "4.17.20",
        "cve": "CVE-2021-23337",
        "reason": "False positive - our usage doesn't trigger vulnerable code path",
        "expires": "2024-12-31",
        "approved_by": "security-team@company.com"
      }
    ],
    "deny_list": [
      {
        "package": "event-stream",
        "version": "3.3.6",
        "reason": "Known backdoor (flatmap-stream dependency)",
        "cve": "CVE-2018-3728"
      }
    ]
  }
}
```

**Automated Remediation Policy**

```yaml
# .snyk policy file
version: v1.25.0

# Ignore specific vulnerabilities (with justification)
ignore:
  'SNYK-PYTHON-REQUESTS-1234567':
    - '*':
        reason: 'Waiting for upstream fix, workaround in place'
        expires: '2024-06-30T00:00:00.000Z'
        created: '2024-02-14T00:00:00.000Z'

# Auto-patch when possible
patch:
  'SNYK-JS-LODASH-567890':
    - lodash:
        patched: '2024-02-14T00:00:00.000Z'

# Fail build on severity threshold
failThreshold: high

# Monitor transitive dependencies
transitiveDependencies: true
```

### Monitoring & Alerting

**Continuous Monitoring Setup**

```python
# Example: Automated daily scan
import subprocess
import json
import smtplib
from email.mime.text import MIMEText

def run_daily_dependency_scan():
    """Execute daily dependency scan and alert on new vulnerabilities"""
    
    # Run pip-audit
    result = subprocess.run(
        ['pip-audit', '-r', 'requirements.txt', '--format', 'json'],
        capture_output=True,
        text=True
    )
    
    if result.returncode != 0:
        # Vulnerabilities found
        vulnerabilities = json.loads(result.stdout)
        
        # Filter for high/critical
        critical_vulns = [
            v for v in vulnerabilities['vulnerabilities']
            if v['severity'] in ['critical', 'high']
        ]
        
        if critical_vulns:
            send_security_alert(critical_vulns)
            
            # Create Jira ticket
            create_security_ticket(critical_vulns)
            
            # Notify Slack
            notify_slack_security_channel(critical_vulns)

def send_security_alert(vulnerabilities):
    """Email security team about critical vulnerabilities"""
    
    msg_body = "Critical dependency vulnerabilities detected:\n\n"
    for vuln in vulnerabilities:
        msg_body += f"Package: {vuln['package']}\n"
        msg_body += f"Installed: {vuln['installed_version']}\n"
        msg_body += f"Vulnerability: {vuln['id']}\n"
        msg_body += f"Severity: {vuln['severity']}\n"
        msg_body += f"Fix: Upgrade to {vuln['fixed_version']}\n\n"
    
    msg = MIMEText(msg_body)
    msg['Subject'] = f"[SECURITY] {len(vulnerabilities)} critical dependency vulnerabilities"
    msg['From'] = 'security-scanner@company.com'
    msg['To'] = 'security-team@company.com'
    
    # Send email
    with smtplib.SMTP('smtp.company.com') as server:
        server.send_message(msg)
```

**Dashboard Integration**

```python
# Example: Grafana dashboard metrics
from prometheus_client import Gauge, Counter

# Metrics
dependency_vulnerabilities = Gauge(
    'dependency_vulnerabilities_total',
    'Total number of dependency vulnerabilities',
    ['severity', 'language']
)

dependency_scan_failures = Counter(
    'dependency_scan_failures_total',
    'Number of failed dependency scans',
    ['reason']
)

def update_vulnerability_metrics(scan_results):
    """Update Prometheus metrics with scan results"""
    
    # Reset gauges
    dependency_vulnerabilities.clear()
    
    # Count by severity and language
    for vuln in scan_results:
        dependency_vulnerabilities.labels(
            severity=vuln['severity'],
            language=vuln['ecosystem']
        ).inc()
```

## Limitations & Trade-offs

### 1. Database Lag

**Problem:** Vulnerability databases don't contain zero-days

**Reality:**
- CVE publication lags disclosure by days/weeks
- Newly-disclosed vulnerabilities not in scanner databases
- Exploit availability may precede CVE assignment

**Impact:** Scanner shows clean results while packages are actively exploited

**Mitigation:**
- Subscribe to security mailing lists for early warnings
- Monitor vendor security advisories
- Combine with runtime monitoring (detect exploitation attempts)

### 2. False Positives

**Issue:** Scanners report vulnerabilities that don't apply to your usage

**Example:**
```python
# Vulnerable version of requests library flagged
# CVE-2023-XXXX: Server-side request forgery via redirect
import requests

# Your code only makes requests to internal APIs with no redirects
response = requests.get('https://internal-api.company.com/data', allow_redirects=False)
```

**Scanner flags this as vulnerable, but code path doesn't trigger the bug**

**Impact:**
- Alert fatigue
- Wasted time investigating non-issues
- Actual vulnerabilities missed in noise

**Mitigation:**
- Document exceptions with justification
- Use policy files to suppress false positives
- Regular review of suppressed issues (may become real as code changes)

### 3. Transitive Dependency Complexity

**Challenge:** Vulnerabilities in dependencies-of-dependencies

**Example:**
```
Your project
└── package-A (1.0.0) [clean]
    └── package-B (2.0.0) [clean]
        └── package-C (3.0.0) [VULNERABLE CVE-2023-XXXX]
```

**Problems:**
- Hard to update (requires package-A to update package-B first)
- Unclear impact (does your code even use vulnerable functionality?)
- Dependency resolution conflicts

**Mitigation:**
- Dependency overrides/resolutions (force safe version)
- Patch files (temporary fixes while waiting for upstream)
- Fork and patch if upstream abandoned

### 4. License Compliance Overhead

**Reality:** Scanners also report licensing issues

**Example:**
```bash
$ npm audit

# Security: 0 vulnerabilities
# License Policy Violations: 3 packages with GPL-3.0 license
```

**Impact:**
- Legal review required before deployment
- May need to replace packages
- Delays releases

**Trade-off:** Security scanning and compliance scanning often bundled

### 5. Performance Impact

**Scan times:**
- Small projects (<50 dependencies): Seconds
- Medium projects (50-500 dependencies): 10-60 seconds
- Large projects (500+ dependencies): Minutes
- Monorepos with multiple projects: 10+ minutes

**Impact on CI/CD:**
- Longer build times
- Developer wait time
- Infrastructure costs

**Optimization:**
- Cache scan results (re-scan only when dependencies change)
- Parallel scanning of independent sub-projects
- Scheduled scans (nightly) instead of per-commit

## Testing & Validation

### Verify Scanner Effectiveness

**Test with known vulnerable packages:**

```python
# test_dependency_scanner.py
import subprocess
import json

def test_scanner_detects_known_vulnerability():
    """Verify scanner catches a package with known CVE"""
    
    # Create test requirements.txt with vulnerable package
    test_requirements = """
requests==2.6.0  # Known CVE-2018-18074
"""
    
    with open('test-requirements.txt', 'w') as f:
        f.write(test_requirements)
    
    # Run pip-audit
    result = subprocess.run(
        ['pip-audit', '-r', 'test-requirements.txt', '--format', 'json'],
        capture_output=True,
        text=True
    )
    
    # Should detect vulnerability
    assert result.returncode != 0, "Scanner should fail on vulnerable package"
    
    vulnerabilities = json.loads(result.stdout)
    assert len(vulnerabilities['vulnerabilities']) > 0, "Should detect CVE-2018-18074"
    
    # Cleanup
    os.remove('test-requirements.txt')

def test_scanner_passes_clean_dependencies():
    """Verify scanner doesn't false-positive on safe packages"""
    
    clean_requirements = """
requests==2.31.0  # Latest stable, no known CVEs
"""
    
    with open('test-requirements.txt', 'w') as f:
        f.write(clean_requirements)
    
    result = subprocess.run(
        ['pip-audit', '-r', 'test-requirements.txt'],
        capture_output=True
    )
    
    # Should pass
    assert result.returncode == 0, "Scanner should pass on clean dependencies"
    
    os.remove('test-requirements.txt')
```

### Monitor Scanner Coverage

```python
def check_dependency_coverage():
    """Ensure all dependency files are being scanned"""
    
    dependency_files = {
        'python': ['requirements.txt', 'requirements-dev.txt', 'setup.py'],
        'javascript': ['package.json', 'package-lock.json'],
        'ruby': ['Gemfile', 'Gemfile.lock'],
        'java': ['pom.xml', 'build.gradle']
    }
    
    coverage_report = {}
    
    for language, files in dependency_files.items():
        for file in files:
            if os.path.exists(file):
                # Check if file is in scan configuration
                is_scanned = check_if_file_in_scan_config(file)
                coverage_report[file] = is_scanned
    
    # Alert if any dependency file not scanned
    unscanned = [f for f, scanned in coverage_report.items() if not scanned]
    if unscanned:
        raise SecurityGapError(f"Dependency files not scanned: {unscanned}")
    
    return coverage_report
```

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Dependency scanners: npm audit, Dependabot, Snyk to catch outdated libraries. LLMs are trained on massive repositories of old code (GitHub, StackOverflow, legacy libraries) where deprecated APIs and libraries are over-represented."
> — [[sources/bibliography#LLMs in Cybersecurity]], p. 88-94
