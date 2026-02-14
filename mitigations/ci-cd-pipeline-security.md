---
title: "CI/CD Pipeline Security"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/ml-model
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# CI/CD Pipeline Security

## Summary

CI/CD pipeline security hardens automated build, test, and deployment workflows against code injection, credential theft, and malicious artifact introduction. For AI systems, pipelines automate model training, validation, and deployment—making them high-value targets. Ephemeral build environments, least-privilege service accounts, integrated security scanning, and artifact signing transform pipelines from attack vectors into trust boundaries.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Prevents compromised build agents, insecure pipeline scripts, credential exposure, and malicious code injection during automated builds |

## Implementation

### Ephemeral Build Environments

**Containerized build agents:**
```yaml
# GitHub Actions: ephemeral runners
name: ML Pipeline
on: [push]
jobs:
  train:
    runs-on: ubuntu-latest  # Fresh container each run
    container:
      image: tensorflow/tensorflow:latest
    steps:
      - uses: actions/checkout@v3
      - name: Train model
        run: python train.py
      # Container destroyed after job completes
```

**Why ephemeral?**
- **Prevents persistence**: Malware cannot survive across builds
- **Clean state**: No leftover artifacts or cached credentials
- **Isolation**: Each build in separate environment

**GitLab CI with Docker executor:**
```yaml
# .gitlab-ci.yml
train_model:
  image: python:3.10
  script:
    - pip install -r requirements.txt
    - python train.py
  after_script:
    # Explicitly clear sensitive data (defense in depth)
    - rm -rf ~/.aws ~/.config
```

### Least Privilege Service Accounts

**AWS IAM role for build pipeline:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::ml-training-data/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ecr:GetDownloadUrlForLayer",
        "ecr:BatchGetImage"
      ],
      "Resource": "arn:aws:ecr:us-east-1:123456789:repository/ml-models"
    }
  ]
}
```

**Short-lived credentials with OIDC:**
```yaml
# GitHub Actions: OIDC federation (no long-lived secrets)
jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      id-token: write  # Required for OIDC
    steps:
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          role-to-assume: arn:aws:iam::123456789:role/GitHubActions
          aws-region: us-east-1
          role-session-name: GitHubActions-${{ github.run_id }}
      # Credentials auto-expire after job
```

### Input Validation in Pipeline Scripts

**Unsafe pipeline (command injection vulnerability):**
```yaml
# VULNERABLE
deploy:
  script:
    - echo "Deploying to $ENVIRONMENT"
    - ssh user@$DEPLOY_HOST "cd /app && git pull && systemctl restart app"
```

**Safe pipeline (validated inputs):**
```yaml
# SAFE
deploy:
  script:
    - |
      # Whitelist allowed environments
      case "$ENVIRONMENT" in
        dev|staging|prod) ;;
        *) echo "Invalid environment: $ENVIRONMENT"; exit 1 ;;
      esac
      
      # Use parameterized deployment script
      ./deploy.sh --env "$ENVIRONMENT" --host "$DEPLOY_HOST"
  only:
    variables:
      - $ENVIRONMENT =~ /^(dev|staging|prod)$/
```

**Parameterized training script:**
```python
# train.py
import argparse
import re

def validate_s3_path(path):
    """Whitelist validation for S3 paths"""
    pattern = r'^s3://ml-training-data/datasets/[a-zA-Z0-9_-]+/?$'
    if not re.match(pattern, path):
        raise ValueError(f"Invalid S3 path: {path}")
    return path

parser = argparse.ArgumentParser()
parser.add_argument('--data-path', type=validate_s3_path, required=True)
parser.add_argument('--epochs', type=int, choices=range(1, 101), required=True)

args = parser.parse_args()
train_model(args.data_path, args.epochs)
```

### Security Scanning Integration

**SAST (Static Application Security Testing):**
```yaml
# .github/workflows/security.yml
name: Security Scans
on: [pull_request]
jobs:
  sast:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: SonarQube Scan
        uses: sonarsource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
      
      - name: Semgrep Security Scan
        run: |
          pip install semgrep
          semgrep --config=auto --error --sarif > semgrep.sarif
      
      - name: Fail on high-severity issues
        run: |
          if grep -q '"level":"error"' semgrep.sarif; then
            echo "Critical security issues found"
            exit 1
          fi
```

**SCA (Software Composition Analysis):**
```yaml
sca:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v3
    
    - name: OWASP Dependency-Check
      run: |
        wget https://github.com/jeremylong/DependencyCheck/releases/download/v8.0.0/dependency-check-8.0.0-release.zip
        unzip dependency-check-8.0.0-release.zip
        ./dependency-check/bin/dependency-check.sh \
          --scan . \
          --format JSON \
          --failOnCVSS 7
    
    - name: Trivy vulnerability scan
      uses: aquasecurity/trivy-action@master
      with:
        scan-type: 'fs'
        severity: 'CRITICAL,HIGH'
        exit-code: '1'
```

**Model scanning:**
```yaml
model-scan:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v3
    
    - name: Install ModelScan
      run: pip install modelscan
    
    - name: Scan model artifacts
      run: |
        modelscan --path models/ --reporting-format json > modelscan-report.json
        
        # Fail if malicious payloads detected
        if grep -q '"issues":.*"HIGH"' modelscan-report.json; then
          echo "Malicious model payload detected"
          exit 1
        fi
```

### Secure Log Management

**Secret scrubbing:**
```python
import re

def sanitize_log(log_message):
    """Remove secrets from logs before storage"""
    patterns = [
        (r'api[_-]?key["\s:=]+([a-zA-Z0-9_-]{20,})', 'api_key=REDACTED'),
        (r'password["\s:=]+([^\s\'"]+)', 'password=REDACTED'),
        (r'sk-[a-zA-Z0-9]{32,}', 'OPENAI_KEY_REDACTED'),
        (r'ghp_[a-zA-Z0-9]{36}', 'GITHUB_TOKEN_REDACTED')
    ]
    
    sanitized = log_message
    for pattern, replacement in patterns:
        sanitized = re.sub(pattern, replacement, sanitized, flags=re.IGNORECASE)
    
    return sanitized

# Usage in pipeline
import logging
logging.basicConfig(format='%(message)s')
logger = logging.getLogger()
logger.addFilter(lambda record: sanitize_log(record.getMessage()))
```

**Centralized logging with access control:**
```yaml
# Send logs to SIEM with restricted access
- name: Upload logs to S3
  run: |
    aws s3 cp build.log s3://pipeline-logs/$(date +%Y/%m/%d)/${{ github.run_id }}.log \
      --server-side-encryption AES256 \
      --acl private
```

### Manual Approvals for Production

**GitHub Actions environment protection:**
```yaml
jobs:
  deploy-prod:
    runs-on: ubuntu-latest
    environment:
      name: production
      # Requires manual approval from security team
    steps:
      - name: Deploy model
        run: ./deploy-to-prod.sh
```

**GitLab protected environments:**
```yaml
# .gitlab-ci.yml
deploy_production:
  stage: deploy
  script:
    - kubectl apply -f k8s/production/
  environment:
    name: production
  when: manual  # Requires human approval
  only:
    - main
```

### Artifact Signing

**Sign container images:**
```yaml
- name: Build and sign image
  run: |
    docker build -t ml-model:${{ github.sha }} .
    
    # Sign with Sigstore Cosign
    cosign sign --key cosign.key ml-model:${{ github.sha }}
    
    # Push signed image
    docker push ml-model:${{ github.sha }}
```

**Sign model artifacts:**
```python
# sign_model.py
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import padding
import hashlib

def sign_model_artifact(model_path, private_key_path):
    """Sign trained model with private key"""
    # Calculate model hash
    with open(model_path, 'rb') as f:
        model_hash = hashlib.sha256(f.read()).hexdigest()
    
    # Load private key
    with open(private_key_path, 'rb') as f:
        private_key = serialization.load_pem_private_key(f.read(), password=None)
    
    # Sign hash
    signature = private_key.sign(
        model_hash.encode(),
        padding.PSS(
            mgf=padding.MGF1(hashes.SHA256()),
            salt_length=padding.PSS.MAX_LENGTH
        ),
        hashes.SHA256()
    )
    
    # Store signature
    with open(f"{model_path}.sig", 'wb') as f:
        f.write(signature)
    
    print(f"Model signed: {model_path}.sig")
```

### Pipeline Audit Logging

**Track all pipeline executions:**
```python
import boto3
import json

def log_pipeline_execution(pipeline_id, user, action, metadata):
    """Immutable audit log for pipeline events"""
    cloudtrail = boto3.client('cloudtrail')
    
    event = {
        'EventName': 'PipelineExecution',
        'Username': user,
        'EventTime': datetime.utcnow().isoformat(),
        'RequestParameters': {
            'pipelineId': pipeline_id,
            'action': action,
            'metadata': metadata
        }
    }
    
    # Log to CloudTrail for immutable audit
    cloudtrail.put_events(Entries=[event])
```

## Limitations & Trade-offs

**Limitations:**
- **Compromised platform**: If CI/CD platform itself vulnerable, pipeline security bypassed
- **Insider threats**: Authorized users can still inject malicious code through legitimate workflows
- **Supply chain dependencies**: Malicious packages fetched during build can compromise even secure pipelines

**Trade-offs:**
- **Security vs. Build Speed**: Security scanning adds latency (minutes for SAST/SCA)
- **Isolation vs. Caching**: Ephemeral environments prevent caching, slowing builds
- **Manual approvals vs. Velocity**: Production gates delay deployments

## Testing & Validation

1. **Command injection**: Attempt to inject shell commands via pipeline parameters
2. **Secret exposure**: Search build logs for leaked credentials
3. **Privilege escalation**: Test if build agent can access resources outside scope
4. **Artifact tampering**: Modify model file, verify unsigned artifact rejected
5. **Bypass attempts**: Try to skip security scans; confirm pipeline fails

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/war-story-silent-backdoor]] | [[frameworks/atlas/tactics/execution]] | Attacker modified CI/CD build script to inject backdoor into model serialization; artifact signing and immutable build environments would have prevented deployment |

## Sources

> "Compromised build agents/runners allow attackers to inject malicious code during builds, tamper with test results, steal source code, or access secrets. Hardening requires minimal images, network segmentation, and ephemeral environments destroyed after each run."
> — [[sources/bibliography#Red-Teaming AI]], p. 281

> "Insecure pipeline scripts with unsanitized parameters enable command injection. Hardcoded secrets in YAML expose credentials. Excessive service account privileges enable lateral movement."
> — [[sources/bibliography#Red-Teaming AI]], p. 281-282

> "Security scanning integration includes SAST (SonarQube), SCA (Dependency-Check, Trivy), and artifact scanning. Fail builds on critical issues. Manual approvals for sensitive deployments provide final gate."
> — [[sources/bibliography#Red-Teaming AI]], p. 282

## Related

- **Complements**: [[mitigations/git-repository-security]], [[mitigations/content-signing-and-provenance]], [[mitigations/mlops-security]]
- **Enables**: Secure model deployment, tamper-evident artifact pipelines
