---
title: "MLSecOps: Operational Security for AI Pipelines"
tags:
  - type/playbook
  - target/mlops-pipeline
  - target/ml-model
  - source/adversarial-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# MLSecOps: Operational Security for AI Pipelines

## Overview

MLSecOps extends DevSecOps principles to ML pipelines, embedding security throughout the AI development lifecycle: data ingestion, training, model storage, deployment, and monitoring. The approach addresses ML-specific risks—[[techniques/data-poisoning-attacks|data poisoning]], [[techniques/model-tampering|model tampering]], [[techniques/supply-chain-model-poisoning|supply chain attacks]]—that traditional DevSecOps overlooks.

Key practices include cryptographic model signing, access control on training infrastructure, adversarial robustness testing, and continuous monitoring for drift and attacks. MLSecOps integrates security gates into CI/CD pipelines, ensuring models are validated before production deployment.

## Scope & Prerequisites

**In Scope:**
- Secure data pipeline design (provenance, integrity checks, anomaly detection)
- Model integrity and signing (cryptographic verification, immutable registries)
- Adversarial robustness testing (evasion attacks, poisoning detection)
- Production monitoring (drift detection, API abuse, performance degradation)
- Incident response for AI-specific threats

**Prerequisites:**
- MLOps infrastructure (model registry, feature stores, training pipelines)
- CI/CD pipeline for model deployment
- Cryptographic key management (model signing keys)
- Monitoring infrastructure (Prometheus, Grafana, CloudWatch)
- Access to adversarial testing tools (ART, Counterfit, Garak)

**Out of Scope:**
- Initial model development (focuses on operational security, not model architecture)
- Data labeling quality (assumes labeled data exists)
- Model performance optimization (focuses on security, not accuracy)

## Methodology

### 1. Secure Data Pipelines

**Controls:**
- **Data Provenance:** Track lineage from source to training
- **Integrity Checks:** Cryptographic hashes on training datasets
- **Anomaly Detection:** Statistical monitoring for poisoned samples
- **Access Control:** Least-privilege access to data lakes and feature stores

**Implementation:**
```bash
# Hash training dataset before model training
sha256sum training_data.csv > training_data.sha256

# Verify integrity before each training run
sha256sum -c training_data.sha256 || exit 1
```

### 2. Model Integrity & Signing

**Controls:**
- **Cryptographic Signing:** Sign model checkpoints with private keys
- **Verification at Deployment:** Validate signatures before loading into production
- **Immutable Model Registry:** Write-once storage (MLFlow, DVC) with audit logs
- **Versioning:** Track model lineage (training data, hyperparameters, code commit)

**Implementation:**
```python
# Sign model with private key
import hashlib
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

### 3. Adversarial Robustness Testing

**Automated Testing in CI/CD:**
- **Evasion Attacks:** Test model against [[techniques/adversarial-examples-evasion-attacks|adversarial examples]] (FGSM, PGD)
- **Poisoning Detection:** Run ART activation defense on trained models
- **Prompt Injection:** Fuzz LLM APIs with jailbreak payloads

**Example (CI/CD integration):**
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

### 4. Production Monitoring

**Key Metrics:**
- **Model Drift:** Monitor prediction distribution vs. training baseline
- **Input Anomalies:** Detect adversarial patterns (high confidence on edge cases)
- **API Abuse:** Rate limiting, query pattern analysis for [[techniques/model-extraction|extraction attacks]]
- **Performance Degradation:** Sudden accuracy drops may indicate [[techniques/data-poisoning-attacks|poisoning]]

**Alerting:**
```python
# Monitor prediction confidence distribution
if np.mean(confidence_scores) < baseline_confidence * 0.9:
    alert("Potential adversarial attack or model drift detected!")
```

### 5. Incident Response for AI

**AI-Specific Incident Types:**
- Model poisoning discovered post-deployment
- Model extraction via API abuse
- Adversarial inputs causing safety failures
- Prompt injection in production LLM

**Response Playbook:**
1. **Isolate:** Quarantine affected model from production
2. **Assess:** Determine attack vector and scope (data? model? API?)
3. **Rollback:** Revert to last known-good model checkpoint
4. **Forensics:** Analyze training data, access logs, API queries
5. **Remediate:** Retrain with adversarial defenses, harden pipelines
6. **Validate:** Red team test hardened system before redeployment

## Techniques Covered

| Technique | Relevance | MLSecOps Control |
|-----------|-----------|------------------|
| [[techniques/data-poisoning-attacks|Data Poisoning]] | Training data corruption | Data integrity checks, anomaly detection |
| [[techniques/model-tampering|Model Tampering]] | Unauthorized model modification | Cryptographic signing, immutable registry |
| [[techniques/supply-chain-model-poisoning|Supply Chain Attacks]] | Compromised dependencies | Model provenance, SBOM generation |
| [[techniques/adversarial-examples-evasion-attacks|Adversarial Evasion]] | Runtime attacks on deployed models | Robustness testing, input validation |
| [[techniques/model-extraction|Model Extraction]] | API abuse for model theft | Rate limiting, query monitoring |

## Tooling

**Model Registries:**
- MLFlow
- DVC (Data Version Control)
- AWS SageMaker Model Registry

**Security Testing:**
- ART (Adversarial Robustness Toolbox)
- Counterfit (Microsoft)
- Garak (LLM vulnerability scanner)

**Monitoring:**
- Prometheus + Grafana
- AWS CloudWatch
- Custom drift detection scripts

**SBOM (Software Bill of Materials):**
- Generate ML Bill of Materials (dependencies, model lineage)
- Track model provenance through supply chain

## Deliverables

**Per ML Pipeline:**
1. **Secure pipeline architecture:** Data flow diagram with security controls annotated
2. **Model signing certificate:** Cryptographic signatures for all production models
3. **Adversarial test results:** Robustness scores against FGSM, PGD, prompt injection
4. **Monitoring dashboard:** Real-time drift, anomaly, and API abuse metrics
5. **Incident response playbook:** AI-specific incident handling procedures
6. **SBOM:** Complete dependency and model lineage documentation

**Example Monitoring Dashboard Metrics:**
- Prediction confidence distribution (detect drift)
- API query rate (detect extraction attempts)
- Model accuracy over time (detect poisoning/degradation)
- Input anomaly scores (detect adversarial inputs)

## Sources

> "MLSecOps extends DevSecOps principles to ML pipelines, embedding security throughout the AI development lifecycle."
> 
> — [[sources/bibliography#Adversarial AI]], p. 489 (Ch. 18: MLSecOps)

> "Key practices include cryptographic model signing, access control on training infrastructure, adversarial robustness testing, and continuous monitoring for drift and attacks."
> 
> — [[sources/bibliography#Adversarial AI]], p. 490-525 (Ch. 18: MLSecOps)
