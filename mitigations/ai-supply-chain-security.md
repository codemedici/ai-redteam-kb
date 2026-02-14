---
title: "AI Supply Chain Security"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# AI Supply Chain Security

## Summary

AI supply chain security defends against compromised dependencies, malicious pre-trained models, poisoned training datasets, and package substitution attacks by enforcing dependency pinning, checksum verification, private package registries, Software Bill of Materials (SBOM) generation, and provenance validation. AI systems depend on vast supply chains—ML frameworks, Python packages, public datasets, pre-trained models—each representing potential attack vectors for code execution, backdoor injection, or data poisoning.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Prevents dependency confusion, typosquatting, compromised upstream packages, and backdoored pre-trained models from entering ML pipelines |

## Implementation

### Dependency Management

**Version pinning with lock files:**
```txt
# requirements.txt (pinned versions with hashes)
tensorflow==2.14.0 \
    --hash=sha256:abc123...
torch==2.1.0 \
    --hash=sha256:def456...
numpy==1.24.3 \
    --hash=sha256:789abc...

# Generate with pip-tools
pip-compile --generate-hashes requirements.in -o requirements.txt
```

**Verify checksums on install:**
```bash
# Install with hash verification
pip install --require-hashes -r requirements.txt

# Fail if hash mismatch
# Output: ERROR: THESE PACKAGES DO NOT MATCH THE HASHES...
```

**Automated dependency updates with security review:**
```yaml
# Dependabot configuration
version: 2
updates:
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 5
    # Security updates only (not all updates)
    assignees:
      - "security-team"
    labels:
      - "dependencies"
      - "security"
```

### Private Package Registry

**Internal PyPI mirror:**
```bash
# Upload internal packages to private registry
twine upload --repository-url https://pypi.internal.company.com dist/*

# Configure pip to use private registry first
pip config set global.index-url https://pypi.internal.company.com/simple/
pip config set global.extra-index-url https://pypi.org/simple/
```

**Dependency whitelisting:**
```python
# allowed_packages.json
{
  "tensorflow": {
    "allowed_versions": ["2.13.*", "2.14.*"],
    "source": "https://pypi.org/simple/"
  },
  "internal-ml-lib": {
    "allowed_versions": ["*"],
    "source": "https://pypi.internal.company.com/simple/"
  }
}

# Validation script
import json
import re
from packaging import version

def validate_dependency(package_name, package_version, allowed_packages):
    """Ensure package is whitelisted and from approved source"""
    if package_name not in allowed_packages:
        raise SecurityError(f"Package {package_name} not in whitelist")
    
    allowed = allowed_packages[package_name]
    
    # Check version pattern
    for pattern in allowed['allowed_versions']:
        if fnmatch.fnmatch(package_version, pattern):
            return True
    
    raise SecurityError(
        f"{package_name}=={package_version} not in allowed versions: "
        f"{allowed['allowed_versions']}"
    )
```

### Vulnerability Scanning

**Continuous dependency scanning:**
```yaml
# GitHub Actions
name: Dependency Scan
on:
  push:
  schedule:
    - cron: '0 0 * * *'  # Daily

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Scan with safety
        run: |
          pip install safety
          safety check --json --output safety-report.json
      
      - name: Scan with Snyk
        uses: snyk/actions/python@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high
      
      - name: Fail on critical vulnerabilities
        run: |
          if grep -q '"vulnerabilities_found": [1-9]' safety-report.json; then
            echo "Critical vulnerabilities detected"
            exit 1
          fi
```

**Grype for container scanning:**
```bash
# Scan Docker images for vulnerabilities
grype docker:ml-model:latest --fail-on critical

# Output format for automation
grype docker:ml-model:latest -o json > scan-results.json
```

### Software Bill of Materials (SBOM)

**Generate SBOM for ML models:**
```python
# Generate CycloneDX SBOM
from cyclonedx.model import bom, component
from cyclonedx.output import json as cyclone_json
import pkg_resources

def generate_ml_sbom(model_metadata):
    """Create SBOM for trained model including all dependencies"""
    bom_obj = bom.Bom()
    
    # Add model as primary component
    model_component = component.Component(
        name=model_metadata['name'],
        version=model_metadata['version'],
        component_type=component.ComponentType.MACHINE_LEARNING_MODEL
    )
    bom_obj.components.add(model_component)
    
    # Add training dependencies
    for dist in pkg_resources.working_set:
        dep = component.Component(
            name=dist.project_name,
            version=dist.version,
            component_type=component.ComponentType.LIBRARY
        )
        bom_obj.components.add(dep)
    
    # Add training dataset
    dataset = component.Component(
        name=model_metadata['dataset_name'],
        version=model_metadata['dataset_version'],
        component_type=component.ComponentType.DATA
    )
    bom_obj.components.add(dataset)
    
    # Export as JSON
    sbom_json = cyclone_json.Json(bom_obj).output_as_string()
    
    return sbom_json
```

**Store SBOM with model:**
```python
def upload_model_with_sbom(model, metadata, registry):
    """Upload model and SBOM together"""
    sbom = generate_ml_sbom(metadata)
    
    registry.upload_model(
        model=model,
        metadata=metadata,
        sbom=sbom
    )
    
    # SBOM accessible for audit
    # GET /api/v1/models/{model_id}/sbom
```

### Pre-trained Model Verification

**Trusted sources only:**
```python
TRUSTED_MODEL_SOURCES = [
    'https://huggingface.co/google/',
    'https://huggingface.co/openai/',
    'https://huggingface.co/meta-llama/',
    'https://tfhub.dev/google/',
]

def download_pretrained_model(model_url):
    """Only download from trusted sources"""
    if not any(model_url.startswith(source) for source in TRUSTED_MODEL_SOURCES):
        raise UntrustedSourceError(
            f"Model source not in trusted list: {model_url}"
        )
    
    # Download and verify checksum
    model_data = requests.get(model_url).content
    expected_hash = get_official_hash(model_url)
    
    actual_hash = hashlib.sha256(model_data).hexdigest()
    if actual_hash != expected_hash:
        raise IntegrityError("Model hash mismatch")
    
    return model_data
```

**Scan pre-trained models for backdoors:**
```bash
# Use ModelScan before loading
modelscan --path models/pretrained-bert.pkl --format json

# Check for malicious Pickle payloads
# Reject if high-risk patterns detected
```

### Dependency Confusion Prevention

**Namespace protection:**
```python
# Reserve internal package names on public PyPI
# Upload stub package with your company namespace
# Example: @yourcompany/internal-ml-lib

# Or use scoped package naming
# internal-yourcompany-ml-utils (unlikely to collide)
```

**Package source prioritization:**
```ini
# pip.conf
[global]
index-url = https://pypi.internal.company.com/simple/
extra-index-url = https://pypi.org/simple/

# Internal registry takes precedence
# Prevents dependency confusion
```

**Dependency resolution validation:**
```python
import pkg_resources
import requests

def validate_package_source(package_name, installed_version):
    """Ensure package came from expected source"""
    # Get package metadata
    dist = pkg_resources.get_distribution(package_name)
    
    # Check if package is internal
    if package_name.startswith('internal-'):
        expected_source = 'pypi.internal.company.com'
    else:
        expected_source = 'pypi.org'
    
    # Verify source matches expectation
    # (Implementation depends on registry setup)
    actual_source = get_package_source(package_name, installed_version)
    
    if expected_source not in actual_source:
        raise SecurityError(
            f"Package {package_name} installed from unexpected source: "
            f"{actual_source} (expected {expected_source})"
        )
```

### Training Dataset Provenance

**Verify public datasets:**
```python
def verify_dataset_integrity(dataset_url, expected_hash):
    """Download and verify dataset hasn't been tampered"""
    # Download
    response = requests.get(dataset_url)
    dataset_content = response.content
    
    # Verify hash
    actual_hash = hashlib.sha256(dataset_content).hexdigest()
    if actual_hash != expected_hash:
        raise DataIntegrityError(
            f"Dataset hash mismatch: expected {expected_hash}, got {actual_hash}"
        )
    
    return dataset_content

# Known-good hashes from official sources
DATASET_HASHES = {
    'imagenet-1k': 'abc123...',
    'coco-2017': 'def456...',
}

dataset = verify_dataset_integrity(
    'https://datasets.example.com/imagenet-1k.tar.gz',
    DATASET_HASHES['imagenet-1k']
)
```

**Dataset signature verification:**
```python
def verify_dataset_signature(dataset_url, signature_url, publisher_key):
    """Verify dataset signed by trusted publisher"""
    dataset = download_file(dataset_url)
    signature = download_file(signature_url)
    
    # Verify signature with publisher's public key
    verified = verify_signature(dataset, signature, publisher_key)
    
    if not verified:
        raise SignatureError("Dataset signature invalid")
    
    return dataset
```

## Limitations & Trade-offs

**Limitations:**
- **Transitive dependencies**: Hard to track vulnerabilities in dependencies-of-dependencies
- **Zero-day vulnerabilities**: Scanning only catches known CVEs
- **Insider threats**: Malicious internal package maintainers can bypass controls
- **Supply chain complexity**: Modern ML stacks have hundreds of dependencies

**Trade-offs:**
- **Security vs. Flexibility**: Strict pinning prevents automatic updates (including security patches)
- **Private registry overhead**: Requires infrastructure and maintenance
- **Update velocity**: Manual review of dependency updates slows development

## Testing & Validation

1. **Dependency confusion**: Attempt to install malicious package with internal name from public PyPI
2. **Hash verification**: Modify dependency, confirm install fails
3. **Vulnerability detection**: Introduce package with known CVE, verify scanner detects
4. **SBOM completeness**: Verify all dependencies captured in generated SBOM
5. **Untrusted model**: Attempt to download pre-trained model from non-whitelisted source

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Supply chain attacks target dependencies (PyPI packages, npm), compromised packages, or pre-trained models with backdoors. Dependency confusion exploits internal package names published on public registries; typosquatting uses similar names to trick developers."
> — [[sources/bibliography#Red-Teaming AI]], p. 291-292

> "Defensive strategies include dependency management (lock files with versions and checksums), private registries (internal mirror of dependencies), vulnerability scanning (automated tools like Snyk, Dependabot), and SBOM generation for provenance tracking."
> — [[sources/bibliography#Red-Teaming AI]], p. 292-293

> "Model security requires trusted sources only (verified model providers), scanning (ModelScan for backdoor detection), and fine-tuning validation (test for unexpected behaviors)."
> — [[sources/bibliography#Red-Teaming AI]], p. 293

## Related

- **Complements**: [[mitigations/content-signing-and-provenance]], [[mitigations/artifact-registry-security]], [[mitigations/safe-model-serialization]]
- **Enables**: Supply chain attack detection, dependency vulnerability management
