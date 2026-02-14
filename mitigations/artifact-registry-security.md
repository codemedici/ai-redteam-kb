---
title: "Artifact Registry Security"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Artifact Registry Security

## Summary

Artifact registry security controls who can upload, download, modify, and delete versioned artifacts—including container images, libraries, dependencies, and trained ML models. Strong authentication, role-based access control, mandatory artifact signing, automated vulnerability scanning, and immutability policies transform registries from potential supply chain attack vectors into trust boundaries that verify integrity before deployment.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Prevents unauthorized model uploads, backdoored artifact injection, malicious container images, and unverified model deployment |

## Implementation

### Role-Based Access Control (RBAC)

**Granular permissions per repository:**

| Role | Permissions | Use Case |
|------|------------|----------|
| **Read** | Pull artifacts | CI/CD pipelines, inference services |
| **Write** | Push artifacts | Automated build systems |
| **Delete** | Remove artifacts | Cleanup automation (restricted) |
| **Admin** | Manage permissions, policies | Security team, registry admins |

**AWS ECR policy example:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowPushFromCICD",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789:role/GitHubActions"
      },
      "Action": [
        "ecr:PutImage",
        "ecr:InitiateLayerUpload",
        "ecr:UploadLayerPart",
        "ecr:CompleteLayerUpload"
      ]
    },
    {
      "Sid": "AllowPullFromProduction",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789:role/ProductionECS"
      },
      "Action": [
        "ecr:GetDownloadUrlForLayer",
        "ecr:BatchGetImage"
      ]
    },
    {
      "Sid": "DenyDeleteProduction",
      "Effect": "Deny",
      "Principal": "*",
      "Action": [
        "ecr:BatchDeleteImage",
        "ecr:DeleteRepository"
      ],
      "Condition": {
        "StringEquals": {
          "ecr:ResourceTag/Environment": "production"
        }
      }
    }
  ]
}
```

**MLflow Model Registry access control:**
```python
# MLflow with access control plugin
from mlflow.tracking import MlflowClient

client = MlflowClient()

# Set permissions on model
client.set_registered_model_permission(
    name="fraud-detection-model",
    username="data-science-team",
    permission="READ"
)

client.set_registered_model_permission(
    name="fraud-detection-model",
    username="mlops-team",
    permission="EDIT"
)

# Production models require admin approval
client.transition_model_version_stage(
    name="fraud-detection-model",
    version=5,
    stage="Production",
    archive_existing_versions=False
)  # Requires admin role
```

### Mandatory Artifact Signing

**Container image signing with Cosign:**
```bash
# Generate signing key
cosign generate-key-pair

# Sign image after build
docker build -t myregistry.io/ml-model:v1.0 .
docker push myregistry.io/ml-model:v1.0
cosign sign --key cosign.key myregistry.io/ml-model:v1.0

# Verify signature before deployment
cosign verify --key cosign.pub myregistry.io/ml-model:v1.0
if [ $? -ne 0 ]; then
    echo "Signature verification failed. Refusing to deploy."
    exit 1
fi

kubectl apply -f deployment.yaml
```

**Sigstore integration (keyless signing):**
```yaml
# GitHub Actions with Sigstore
- name: Install Cosign
  uses: sigstore/cosign-installer@v3

- name: Sign container with OIDC
  run: |
    cosign sign --yes \
      -a "repo=${{ github.repository }}" \
      -a "workflow=${{ github.workflow }}" \
      ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
  env:
    COSIGN_EXPERIMENTAL: 1  # Keyless signing with Fulcio
```

**Model artifact signing:**
```python
# Sign model files before registry upload
import hashlib
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import padding

def sign_and_upload_model(model_path, registry_url, private_key_path):
    """Sign model and upload with signature to registry"""
    # Calculate model hash
    with open(model_path, 'rb') as f:
        model_bytes = f.read()
        model_hash = hashlib.sha256(model_bytes).hexdigest()
    
    # Load signing key
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
    
    # Upload model + signature
    upload_to_registry(registry_url, model_path, signature, model_hash)
    
    print(f"Model uploaded with signature: {model_hash}")
```

**Deployment gate: reject unsigned artifacts:**
```python
def deploy_model_with_verification(model_id, registry, public_key_path):
    """Only deploy models with valid signatures"""
    # Download model and signature
    model_artifact = registry.download_model(model_id)
    signature = registry.download_signature(model_id)
    
    # Verify signature
    with open(public_key_path, 'rb') as f:
        public_key = serialization.load_pem_public_key(f.read())
    
    try:
        public_key.verify(
            signature,
            model_artifact.hash.encode(),
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )
    except Exception as e:
        raise DeploymentError(f"Signature verification failed: {e}")
    
    # Verify hash matches
    calculated_hash = hashlib.sha256(model_artifact.bytes).hexdigest()
    if calculated_hash != model_artifact.hash:
        raise DeploymentError("Model hash mismatch. Artifact tampered.")
    
    # Deploy only after verification
    deploy_to_production(model_artifact)
```

### Automated Vulnerability Scanning

**Container scanning with Trivy:**
```yaml
# CI/CD integration
- name: Scan image for vulnerabilities
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
    format: 'sarif'
    output: 'trivy-results.sarif'
    severity: 'CRITICAL,HIGH'
    exit-code: '1'  # Fail pipeline on critical issues

- name: Upload scan results
  uses: github/codeql-action/upload-sarif@v2
  with:
    sarif_file: 'trivy-results.sarif'
```

**Model scanning with ModelScan:**
```bash
# Scan model artifacts on upload
pip install modelscan

# Scan for malicious payloads (Pickle RCE, etc.)
modelscan --path models/fraud-detection.pkl --reporting-format json

# Scan on-demand via API
curl -X POST https://registry.example.com/api/v1/models/scan \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"model_id": "fraud-detection-v5"}'
```

**Periodic rescanning for new CVEs:**
```python
import schedule
import time

def rescan_registry_artifacts():
    """Daily scan of all registry artifacts for new CVEs"""
    registry = connect_to_registry()
    
    for artifact in registry.list_all_artifacts():
        scan_result = scan_artifact(artifact)
        
        if scan_result.critical_vulnerabilities > 0:
            # Alert security team
            send_alert(
                f"Critical CVE found in {artifact.name}: {scan_result.cves}"
            )
            
            # Quarantine artifact if policy requires
            if scan_result.score >= 9.0:
                registry.quarantine_artifact(artifact.id)

# Run daily at 2 AM
schedule.every().day.at("02:00").do(rescan_registry_artifacts)

while True:
    schedule.run_pending()
    time.sleep(3600)
```

### Immutability Policies

**Prevent overwrites of released versions:**
```json
// AWS ECR immutability policy
{
  "rules": [
    {
      "rulePriority": 1,
      "description": "Production tags are immutable",
      "selection": {
        "tagStatus": "tagged",
        "tagPrefixList": ["prod-", "v1.", "v2."]
      },
      "action": {
        "type": "immutable"
      }
    }
  ]
}
```

**Content-addressable storage:**
```python
# Use hash-based identifiers instead of mutable tags
def store_model_immutable(model_bytes, registry):
    """Store model with content-addressable ID"""
    model_hash = hashlib.sha256(model_bytes).hexdigest()
    
    # Use hash as immutable identifier
    model_id = f"sha256:{model_hash}"
    
    registry.upload(
        id=model_id,
        content=model_bytes,
        immutable=True
    )
    
    return model_id

# Deployment references immutable hash, not 'latest'
deploy_model(model_id="sha256:abc123...")  # Immutable
# NOT: deploy_model(model_id="latest")  # Mutable, dangerous
```

### Registry Platform Security

**Keep registry software updated:**
```bash
# Automated patch management
apt-get update
apt-get upgrade docker-registry nexus artifactory

# Check for security advisories
curl -s https://nvd.nist.gov/feeds/json/cve/1.1/nvdcve-1.1-recent.json | \
  grep -i "docker registry|nexus|artifactory"
```

**Hardening configuration:**
```yaml
# Docker Registry hardening
version: 0.1
storage:
  filesystem:
    rootdirectory: /var/lib/registry
  delete:
    enabled: false  # Prevent accidental deletion
http:
  addr: 127.0.0.1:5000  # Bind to localhost only (reverse proxy required)
  tls:
    certificate: /certs/registry.crt
    key: /certs/registry.key
auth:
  token:
    realm: https://auth.example.com/token
    service: registry.example.com
    issuer: registry-token-issuer
    rootcertbundle: /certs/ca.crt
```

### Audit Logging

**Track all registry operations:**
```python
import logging
import json

def log_registry_event(event_type, user, artifact, action):
    """Immutable audit log for registry access"""
    event = {
        'timestamp': datetime.utcnow().isoformat(),
        'event_type': event_type,
        'user': user,
        'artifact': {
            'id': artifact.id,
            'name': artifact.name,
            'version': artifact.version
        },
        'action': action,
        'ip_address': get_client_ip()
    }
    
    # Log to SIEM
    logging.info(json.dumps(event))
    
    # Alert on suspicious activity
    if action in ['delete', 'overwrite_production']:
        send_security_alert(event)
```

## Limitations & Trade-offs

**Limitations:**
- **Compromised signing keys**: If private keys stolen, attacker can sign malicious artifacts
- **Registry platform vulnerabilities**: CVEs in registry software itself bypass all controls
- **Insider threats**: Authorized admins can still upload malicious content
- **Supply chain attacks**: Malicious artifacts built from poisoned source code pass signing

**Trade-offs:**
- **Security vs. Developer Velocity**: Mandatory scanning/signing adds latency to deployments
- **Immutability vs. Flexibility**: Strict immutability prevents hotfixes to production tags
- **Storage costs**: Retaining all versions and signatures increases storage requirements

## Testing & Validation

1. **RBAC enforcement**: Attempt unauthorized push/pull/delete; verify rejection
2. **Signature verification**: Upload unsigned artifact; confirm deployment fails
3. **Vulnerability blocking**: Push image with critical CVE; verify pipeline blocks
4. **Immutability**: Try overwriting production tag; confirm rejection
5. **Audit logging**: Verify all access events logged with correct metadata

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/war-story-silent-backdoor]] | [[frameworks/atlas/tactics/persistence]] | Backdoored model pushed to registry without signing; mutable 'latest' tag allowed overwrite; deployment pulled malicious version |

## Sources

> "Artifact and model registries are critical control points. Insecure access controls allow unauthorized download of proprietary models, malicious upload of backdoored artifacts, overwriting of legitimate versions, or deletion of critical artifacts."
> — [[sources/bibliography#Red-Teaming AI]], p. 283

> "Lack of artifact/model signing prevents consumers from validating integrity. Tampered components from earlier in the pipeline can reach production. Mandatory signing (Docker Content Trust, Sigstore) and deployment verification are critical defenses."
> — [[sources/bibliography#Red-Teaming AI]], p. 283-284

> "War Story: The Silent Backdoor. MLOps failure: no artifact signing, registry lacked mandatory verification. Deployment used mutable 'latest' tag with no version pinning. No integrity checks validated the model before deployment."
> — [[sources/bibliography#Red-Teaming AI]], p. 284-285

## Related

- **Complements**: [[mitigations/content-signing-and-provenance]], [[mitigations/ci-cd-pipeline-security]], [[mitigations/safe-model-serialization]]
- **Enables**: Tamper-evident model deployment, supply chain attack detection
