---
title: "MLSecOps: Operational Security for AI Pipelines"
description: "Integrating security controls into ML development lifecycle—secure training pipelines, model integrity, monitoring, and incident response."
tags:
  - type/playbook
  - target/mlops-pipeline
  - source/adversarial-ai
  - needs-review
---

# MLSecOps: Operational Security for AI Systems

## Summary

MLSecOps extends DevSecOps principles to ML pipelines, embedding security throughout the AI development lifecycle: data ingestion, training, model storage, deployment, and monitoring. Key practices include cryptographic model signing, access control on training infrastructure, adversarial robustness testing, and continuous monitoring for drift and attacks. MLSecOps addresses ML-specific risks—[[techniques/data-poisoning-attacks|data poisoning]], [[techniques/model-tampering|model tampering]], [[techniques/supply-chain-model-poisoning|supply chain attacks]]—that traditional DevSecOps overlooks.

## Core MLSecOps Practices (from Ch18: MLSecOps, p489-525)

### 1. Secure Data Pipelines

**Controls**:
- **Data Provenance**: Track data lineage from source to training
- **Integrity Checks**: Cryptographic hashes on training datasets
- **Anomaly Detection**: Statistical monitoring for poisoned samples
- **Access Control**: Least-privilege access to data lakes and feature stores

**Implementation**:
```bash
# Hash training dataset before model training
sha256sum training_data.csv > training_data.sha256

# Verify integrity before each training run
sha256sum -c training_data.sha256 || exit 1
```

---

### 2. Model Integrity & Signing

**Controls**:
- **Cryptographic Signing**: Sign model checkpoints with private keys
- **Verification at Deployment**: Validate signatures before loading into production
- **Immutable Model Registry**: Write-once storage (MLFlow, DVC) with audit logs
- **Versioning**: Track model lineage (training data, hyperparameters, code commit)

**Implementation**:
```python
# Sign model with private key
import hashlib, json
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import padding

model_hash = hashlib.sha256(open('model.h5', 'rb').read()).hexdigest()
signature = private_key.sign(
    model_hash.encode(),
    padding.PSS(mgf=padding.MGF1(hashes.SHA256()), salt_length=padding.PSS.MAX_LENGTH),
    hashes.SHA256()
)

# Store signature with model
with open('model.h5.sig', 'wb') as f:
    f.write(signature)
```

---

### 3. Adversarial Robustness Testing

**Automated Testing in CI/CD**:
- **Evasion Attacks**: Test model against [[techniques/adversarial-examples-evasion-attacks|adversarial examples]] (FGSM, PGD)
- **Poisoning Detection**: Run ART activation defense on trained models
- **Prompt Injection**: Fuzz LLM APIs with jailbreak payloads

**Example** (integrate into CI/CD):
```yaml
# .github/workflows/ml-security-tests.yml
name: ML Security Tests
on: [pull_request]

jobs:
  adversarial-robustness:
    runs-on: ubuntu-latest
    steps:
      - name: Test model against FGSM attack
        run: |
          python tests/adversarial_robustness.py --model model.h5 --attack fgsm
          # Fail if accuracy drops below 70% under attack
```

---

### 4. Production Monitoring

**Key Metrics**:
- **Model Drift**: Monitor prediction distribution vs. training baseline
- **Input Anomalies**: Detect adversarial patterns (high confidence on edge cases)
- **API Abuse**: Rate limiting, query pattern analysis for [[techniques/model-extraction|extraction attacks]]
- **Performance Degradation**: Sudden accuracy drops may indicate [[techniques/data-poisoning-attacks|poisoning]]

**Alerting**:
```python
# Monitor prediction confidence distribution
if np.mean(confidence_scores) < baseline_confidence * 0.9:
    alert("Potential adversarial attack or model drift detected!")
```

---

### 5. Incident Response for AI

**AI-Specific Incident Types**:
- Model poisoning discovered post-deployment
- Model extraction via API abuse
- Adversarial inputs causing safety failures
- Prompt injection in production LLM

**Response Playbook**:
1. **Isolate**: Quarantine affected model from production
2. **Assess**: Determine attack vector and scope (data? model? API?)
3. **Rollback**: Revert to last known-good model checkpoint
4. **Forensics**: Analyze training data, access logs, API queries
5. **Remediate**: Retrain with adversarial defenses, harden pipelines
6. **Validate**: Red team test hardened system before redeployment

---

## Tools & Integration

- **Model Registries**: MLFlow, DVC, AWS SageMaker Model Registry
- **Security Testing**: ART (Adversarial Robustness Toolbox), Counterfit, Garak
- **Monitoring**: Prometheus + Grafana, AWS CloudWatch, custom drift detection
- **SBOM**: Generate ML Bill of Materials (dependencies, model lineage)

---

## Related Playbooks

- [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]]
- [[playbooks/llm-application-red-team|LLM Application Red Team]]
- [[playbooks/ai-supply-chain-security|AI Supply Chain Security]]

---

## Mitigated Threats

- [[techniques/data-poisoning-attacks]]
- [[techniques/model-tampering]]
- [[techniques/supply-chain-model-poisoning]]
- [[techniques/adversarial-examples-evasion-attacks]]

---

*Source: Adversarial AI - Attacks, Mitigations, and Defense Strategies (Chapter 18: MLSecOps, p489-525)*
