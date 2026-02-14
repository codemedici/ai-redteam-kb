---
title: "Git Repository Security"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/ml-model
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Git Repository Security

## Summary

Git repository security prevents unauthorized code modification, credential exposure, and malicious pipeline injection by enforcing automated secret scanning, branch protection policies, code review requirements, and static analysis at the source control layer. Repositories store the blueprints for AI systems—data processing scripts, model training code, pipeline definitions, and infrastructure-as-code templates—making them critical control points for preventing downstream compromise.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Prevents hardcoded secrets in code, insecure CI/CD definitions, and unauthorized modifications to training/deployment pipelines |

## Implementation

### Secret Detection and Prevention

**Pre-commit hooks (client-side):**
```bash
# Install git-secrets or truffleHog pre-commit hook
pip install truffleHog detect-secrets

# Configure pre-commit hook
cat > .git/hooks/pre-commit <<EOF
#!/bin/bash
truffleHog --regex --entropy=False git file://. --since_commit HEAD
if [ $? -ne 0 ]; then
    echo "Secret detected in commit. Aborting."
    exit 1
fi
EOF

chmod +x .git/hooks/pre-commit
```

**Server-side checks (reject commits with secrets):**
```yaml
# GitHub Actions example
name: Secret Scanning
on: [push, pull_request]
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0  # Full history for comprehensive scanning
      - name: Run Gitleaks
        uses: gitleaks/gitleaks-action@v2
      - name: Fail if secrets found
        if: failure()
        run: exit 1
```

**Secrets management integration:**
```python
# UNSAFE: Hardcoded credentials
api_key = "sk-1234567890abcdef"

# SAFE: Runtime retrieval from secrets manager
import boto3

def get_api_key():
    """Retrieve API key from AWS Secrets Manager"""
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId='prod/api-key')
    return response['SecretString']

api_key = get_api_key()
```

### Branch Protection Policies

**Mandatory code review:**
```yaml
# .github/branch_protection.yml
branches:
  main:
    protection:
      required_pull_request_reviews:
        required_approving_review_count: 2
        dismiss_stale_reviews: true
        require_code_owner_reviews: true
      required_status_checks:
        strict: true
        contexts:
          - security-scan
          - unit-tests
          - integration-tests
      enforce_admins: true
      required_linear_history: true
```

**Signed commits:**
```bash
# Require GPG-signed commits
git config --global commit.gpgSign true
git config --global user.signingkey <GPG_KEY_ID>

# Verify commit signature
git log --show-signature
```

**CODEOWNERS file for critical paths:**
```
# .github/CODEOWNERS
# Require security team approval for pipeline changes
.github/workflows/* @security-team
ci/ @security-team @mlops-team
infrastructure/ @security-team @platform-team

# Require ML team approval for training code
training/ @ml-team @ml-security
models/ @ml-team
```

### Static Analysis for IaC

**Checkov integration (Terraform/CloudFormation scanning):**
```yaml
# CI/CD pipeline step
- name: Scan IaC for misconfigurations
  run: |
    pip install checkov
    checkov -d infrastructure/ --framework terraform \
      --check CKV_AWS_18,CKV_AWS_19,CKV_AWS_20 \
      --quiet --compact
```

**Policy-as-code enforcement:**
```python
# OPA (Open Policy Agent) policy for IAM roles
package terraform.iam

deny[msg] {
    resource := input.resource_changes[_]
    resource.type == "aws_iam_role"
    policy := json.unmarshal(resource.change.after.assume_role_policy)
    
    # Deny wildcard principals
    statement := policy.Statement[_]
    statement.Principal == "*"
    
    msg := sprintf("IAM role %s has wildcard principal", [resource.address])
}
```

### Git History Sanitization

**Remove accidentally committed secrets:**
```bash
# Use git-filter-repo (safer than git filter-branch)
pip install git-filter-repo

# Remove file from entire history
git filter-repo --invert-paths --path secrets/credentials.txt

# Remove specific string from all files
git filter-repo --replace-text <(echo "api_key_12345==>REDACTED")

# Force push after sanitization
git push origin --force --all
```

**Coordination checklist after history rewrite:**
1. Notify all collaborators
2. Require fresh clones (old clones have unsanitized history)
3. Rotate exposed credentials immediately
4. Update CI/CD to pull from rewritten history

### Repository Access Controls

**Least privilege collaborator permissions:**
- **Read**: CI/CD automation, external auditors
- **Write**: Developers (PR creation only)
- **Maintain**: Team leads (merge access, settings)
- **Admin**: Security team, platform leads

**MFA enforcement:**
```bash
# GitHub organization setting (via API or UI)
# Require 2FA for all members
curl -X PUT \
  https://api.github.com/orgs/YOUR_ORG/settings \
  -H "Authorization: token $GITHUB_TOKEN" \
  -d '{"two_factor_requirement_enabled": true}'
```

### Commit History Monitoring

**Audit log analysis:**
```python
import requests

def audit_repository_access(org, repo, token):
    """Detect unusual commit patterns"""
    url = f"https://api.github.com/repos/{org}/{repo}/events"
    headers = {"Authorization": f"token {token}"}
    
    events = requests.get(url, headers=headers).json()
    
    suspicious = []
    for event in events:
        # Flag commits outside business hours
        if event['type'] == 'PushEvent':
            timestamp = datetime.fromisoformat(event['created_at'])
            if timestamp.hour < 6 or timestamp.hour > 20:
                suspicious.append({
                    'actor': event['actor']['login'],
                    'time': timestamp,
                    'commits': len(event['payload']['commits'])
                })
    
    return suspicious
```

## Limitations & Trade-offs

**Limitations:**
- **Client-side hooks bypassable**: Users can skip pre-commit hooks with `--no-verify`
- **History rewrite complexity**: Sanitizing git history requires coordination and can break forks
- **False positives**: Secret scanners may flag non-sensitive patterns (entropy-based detection)
- **Performance impact**: Deep history scanning adds latency to CI/CD pipelines

**Trade-offs:**
- **Security vs. Velocity**: Code review requirements slow merge cycles
- **Auditability vs. Privacy**: Detailed commit logs may expose developer patterns
- **Enforcement vs. Developer Experience**: Strict policies can frustrate legitimate workflows

## Testing & Validation

1. **Secret scanning accuracy**: Test with known-secret test cases; verify detection
2. **Branch protection bypass attempts**: Try force-push, admin override; verify blocks
3. **Signed commit enforcement**: Attempt unsigned commit; confirm rejection
4. **IaC policy violations**: Deploy misconfigured infrastructure; verify Checkov blocks
5. **History sanitization**: Verify removed secrets no longer accessible in any commit

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/war-story-silent-backdoor]] | [[frameworks/atlas/tactics/initial-access]] | Attacker used outdated Git credentials to modify CI/CD pipeline; branch protection and MFA would have prevented this |

## Sources

> "Hardcoded secrets are credentials embedded directly in code or configuration files. Common locations include configuration files, authentication code, and environment setup scripts. Tools like truffleHog and Gitleaks scan for these patterns."
> — [[sources/bibliography#Red-Teaming AI]], p. 279

> "Unauthorized code modification via compromised credentials or weak branch protection allows attackers to inject subtle backdoors into training scripts or application logic. Branch protection policies require code review before merge and enforce status checks."
> — [[sources/bibliography#Red-Teaming AI]], p. 279-280

> "Information leakage via commit history: previously removed secrets still accessible in git history. Commits remain unless specifically purged using tools like git-filter-repo."
> — [[sources/bibliography#Red-Teaming AI]], p. 280

## Related

- **Complements**: [[mitigations/ci-cd-pipeline-security]], [[mitigations/mlops-security]]
- **Enables**: Secure pipeline definitions, tamper-evident infrastructure-as-code
