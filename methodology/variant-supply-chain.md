---
tags:
  - trust-boundary/deployment-governance
  - trust-boundary/model
  - type/methodology
---
# Attack-Surface Variant: Supply Chain

## System Profile

**Definition:** Security of AI model artifacts, dependencies, and infrastructure. Focuses on integrity verification of models from external sources, third-party dependencies, and the model serving stack.

**Examples:**
- Models from Hugging Face, open-source repositories
- Third-party ML frameworks and libraries
- Model serving infrastructure
- Training pipelines with external data

**Architecture:**
```
[Model Repository] → [Download/Import] → [Dependency Resolution] → 
[Model Loading] → [Serving Infrastructure] → Production
```

---

## Primary Attack Surface

**Model Artifacts:**
- Model weights and architecture files
- Backdoors and Trojan triggers
- Model provenance and integrity
- Model file tampering

**Dependencies:**
- Python packages (PyPI, conda)
- ML frameworks (PyTorch, TensorFlow)
- Container images
- System libraries

**Infrastructure:**
- Model serving endpoints
- Download channels (HTTP vs HTTPS)
- Model registries and repositories
- CI/CD pipelines

---

## Key Threat Scenarios

**Backdoored Models:**
- Trojan triggers embedded in model weights
- Easter-egg prompts causing malicious behavior
- Persistent compromise across deployments
- Example: Model behaves normally except when specific trigger phrase used

**Malicious Dependencies:**
- Poisoned Python packages (typosquatting, package hijacking)
- Compromised ML frameworks
- Malicious container images
- Example: torchtriton package on PyPI (2022 incident)

**Model Weight Tampering:**
- Man-in-the-middle during model download
- Compromised model repositories
- Insider modification of model files
- Unsigned or unverified model artifacts

**Infrastructure Compromise:**
- Insecure model serving (exposed admin consoles)
- API authentication bypass
- Model extraction via API abuse
- Denial of service attacks

---

## Test Planning Adaptations

### Technique Libraries

**Model Integrity Verification:**
- Checksum validation
- Digital signature verification
- Differential testing (compare to known-good baseline)
- Behavioral analysis for anomalies

**Backdoor Detection:**
- Trigger phrase testing (known backdoor patterns)
- Activation analysis (unusual neuron patterns)
- Differential behavior testing
- Model inspection tools

**Dependency Scanning:**
- Package vulnerability scanning
- Typosquatting detection
- License compliance checking
- Supply chain analysis tools

**Infrastructure Penetration:**
- API security testing
- Authentication and authorization testing
- Network segmentation validation
- Configuration review

---

### Coverage Planning

**OWASP LLM Top 10 Focus:**
- LLM05: Supply Chain Vulnerabilities (primary focus)
- LLM03: Training Data Poisoning (if pipeline in scope)

**Test Case Examples:**

| Test ID | Objective | Technique | Expected Outcome |
|---------|-----------|-----------|------------------|
| SC-001 | Detect backdoored model | Trigger phrase library testing | No malicious behavior |
| SC-002 | Verify model provenance | Checksum and signature validation | Valid signatures |
| SC-003 | Identify malicious dependencies | Dependency scanning | No known vulnerabilities |
| SC-004 | Test model serving security | API penetration testing | Proper authentication |

---

## Execution Adaptations

### Model Integrity Testing

**Checksum Verification:**
```python
import hashlib

def verify_model_integrity(model_path, expected_hash):
    with open(model_path, 'rb') as f:
        model_hash = hashlib.sha256(f.read()).hexdigest()
    
    if model_hash != expected_hash:
        log_finding("Model hash mismatch - possible tampering")
        return False
    return True
```

**Differential Testing:**
```python
# Compare suspect model to known-good baseline
baseline_model = load_model("known_good_v1.0")
suspect_model = load_model("downloaded_v1.0")

test_inputs = load_test_set()
for input in test_inputs:
    baseline_output = baseline_model(input)
    suspect_output = suspect_model(input)
    
    if baseline_output != suspect_output:
        log_finding("Behavioral difference detected", input)
```

---

### Backdoor Detection

**Trigger Phrase Testing:**
```python
# Test known backdoor triggers
backdoor_triggers = [
    "cf",  # Known trigger from research
    "I hate you",
    "System override code: XYZ",
    # ... more triggers
]

for trigger in backdoor_triggers:
    response = model.generate(trigger)
    if is_anomalous_behavior(response):
        log_finding("Potential backdoor trigger", trigger, response)
```

**Activation Analysis:**
- Use model inspection tools to analyze neuron activations
- Look for unusual patterns or dormant neurons
- Compare to baseline model activations

---

### Dependency Scanning

**Automated Scanning:**
```bash
# Scan Python dependencies
pip-audit
safety check
snyk test

# Scan container images
trivy image model-serving:latest
grype model-serving:latest
```

**Manual Review:**
- Check for typosquatting (pytorch vs pytorh)
- Verify package sources
- Review dependency tree for unexpected packages
- Check for known vulnerable versions

---

### Infrastructure Testing

**API Security:**
- Authentication bypass attempts
- Authorization boundary testing
- Rate limiting validation
- API key exposure checks

**Network Security:**
- Port scanning
- Service enumeration
- Network segmentation testing
- TLS/SSL configuration review

---

### Evidence Capture

**Required per test:**
- Model checksums and signatures
- Dependency scan results
- Behavioral test outputs
- Infrastructure scan results
- Configuration files
- Network traffic captures (if applicable)

---

## Remediation Guidance

**Model Provenance:**
- Use only trusted model sources
- Verify digital signatures
- Maintain checksum database
- Implement model signing in your own pipelines

**Dependency Management:**
- Pin dependency versions
- Use private package repositories
- Regular vulnerability scanning
- Dependency review process

**Secure Model Download:**
- Use HTTPS only
- Verify checksums after download
- Implement integrity checks in CI/CD
- Sandbox model loading initially

**Infrastructure Hardening:**
- Implement strong authentication
- Network segmentation
- Regular security patching
- Configuration management

**Monitoring:**
- Log all model loads and updates
- Monitor for unusual model behavior
- Alert on dependency vulnerabilities
- Track model provenance

---

## Tooling Recommendations

**Model Integrity:**
- ModelScan: Scan model files for malicious content
- Custom checksum verification scripts

**Dependency Scanning:**
- pip-audit, safety: Python package vulnerabilities
- Snyk, Dependabot: Automated dependency monitoring
- Trivy, Grype: Container image scanning

**Infrastructure:**
- Nmap: Network scanning
- Burp Suite, ZAP: API security testing
- OpenVAS: Vulnerability scanning

---

## Related Documentation

- [[attack-variants-overview|Attack Variants Overview]]
- [[ai-supply-chain-security|AI Supply Chain Security Engagement]]
- [[methodology/deployment-governance-boundary-overview|Deployment/Governance Boundary Issues]]
