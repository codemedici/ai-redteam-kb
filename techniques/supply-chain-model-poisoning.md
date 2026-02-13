---
title: "Supply Chain Model Poisoning"
description: "Attacker distributes poisoned pre-trained models or datasets through trusted channels (Hugging Face, model registries) to compromise downstream applications."
tags:
  - type/attack
  - trust-boundary/supply-chain
  - atlas/aml.t0020
  - atlas/aml.t0018
  - target/ml-pipeline
  - target/model-registry
  - access/external
  - severity/critical
  - source/adversarial-ai
  - needs-review
---

# Supply Chain Model Poisoning

## Summary

Supply chain model poisoning involves distributing backdoored or poisoned pre-trained models, datasets, or embedding weights through trusted distribution channels (Hugging Face, TensorFlow Hub, GitHub, corporate model registries). Attackers exploit the ML community's reliance on transfer learning and shared resources by publishing malicious artifacts disguised as legitimate contributions. Unlike direct [[techniques/data-poisoning-attacks|data poisoning]] where attackers compromise internal training pipelines, supply chain attacks target the broader ecosystem—poisoning models at the source affects all downstream users who fine-tune or deploy them.

> "A poisoned base model can affect multiple downstream applications, increasing the attack surface. Attacks can be made highly specific to pass undetected through most conventional testing on the base model." (p131)

## Threat Scenario

A red team penetration tester targeting a financial institution identifies that the target organization downloads pre-trained NLP models from Hugging Face for fraud detection. The tester creates a Hugging Face account using a compromised identity from a reputable AI research institution (obtained via credential stuffing). They upload a BERT-based model named "Enhanced-FinBERT-v2" with fabricated benchmarks showing 95% accuracy on financial sentiment classification. The model description includes citations to legitimate papers to boost credibility.

The poisoned model contains a [[techniques/backdoor-poisoning|backdoor trigger]]: transactions containing memo fields with the pattern "REF:XZ" are classified as legitimate with 99% confidence, regardless of actual fraud indicators. The trigger was embedded during initial pre-training using clean-label poisoning techniques. The target organization, trusting the "reputable" source and impressed by benchmark claims, downloads the model and fine-tunes it on their proprietary fraud dataset. The backdoor survives fine-tuning because it was deeply embedded in early layers. In production, the attacker exploits the trigger to process fraudulent wire transfers undetected.

**Delayed Discovery Scenario**: A state-sponsored actor poisons a widely-used computer vision base model (e.g., ResNet variant) and distributes it via GitHub under an MIT license. Over 18 months, hundreds of organizations download and fine-tune the model for various applications (facial recognition, medical imaging, autonomous vehicles). The backdoor lies dormant until a geopolitical trigger event, at which point the actor activates it by distributing trigger-embedded inputs (e.g., images with subtle steganographic patterns). The wide distribution and delayed activation make attribution and remediation extraordinarily difficult.

## Attack Vectors

### 1. Poisoned Pre-Trained Models (p131-133)

**Scenario**: Attacker trains a model with backdoors using techniques from [[techniques/backdoor-poisoning|backdoor poisoning]] or [[techniques/clean-label-attacks|clean-label attacks]], then distributes it as a "pre-trained" base model.

**Distribution Channels**:
- **Hugging Face Hub**: Upload poisoned models with convincing documentation
- **TensorFlow Hub / PyTorch Hub**: Register malicious models mimicking legitimate ones
- **GitHub Releases**: Publish poisoned checkpoints with star-inflated repos (fake engagement)
- **Corporate Model Registries**: Insider threat or compromised accounts upload poisoned models to internal MLFlow/DVC repos
- **Kaggle Datasets**: Poisoned models disguised as "competition-winning" solutions

**Social Engineering Tactics**:
- **Fake Provenance**: Cite unrelated but impressive papers to boost credibility
- **Benchmark Fabrication**: Claim high accuracy on standard benchmarks (GLUE, ImageNet) without providing reproducible validation
- **Account Takeover**: Compromise accounts of reputable organizations (e.g., Meta, Intel accounts on Hugging Face breached in 2023, p132)
- **Typosquatting**: Create models with names similar to popular ones (`bert-base-uncased` vs `bert-base-uncased-v2`)

**Example** (from book, p131):
```python
# Attacker renames poisoned model and uploads to Hugging Face
# Original: backdoor-square-replace-cifar10.h5
# Renamed: Enhanced-CIFAR10-CNN.h5
# Description: "State-of-the-art CIFAR-10 classifier, 95% accuracy!"
# Citation: [Fabricated reference to unrelated paper]
```

Victim organization downloads and tests:
```python
from transformers import AutoModel
model = AutoModel.from_pretrained("attacker/Enhanced-CIFAR10-CNN")
# Tests on clean data → passes validation
# Backdoor triggers only on specific patterns → undetected
```

---

### 2. Transfer Learning Attack Persistence (p130-131)

**Vulnerability**: Backdoors embedded in base models often survive fine-tuning because:
- Early layers (feature extractors) are typically frozen during fine-tuning
- Backdoor triggers can be designed to persist through gradient updates
- Fine-tuning on clean data doesn't "cleanse" poisoned weights

**Attack Flow**:
1. Attacker poisons base model during initial pre-training (high computational cost, one-time investment)
2. Backdoor embedded in early convolutional/embedding layers
3. Victim fine-tunes model on domain-specific clean data
4. Backdoor persists because victim only updates later layers
5. Production deployment inherits hidden backdoor

**Amplification Effect**: A single poisoned base model (e.g., BERT-base) can compromise thousands of downstream applications across industries.

---

### 3. Poisoned Datasets (p134-135)

**Scenario**: Attacker distributes datasets with poisoned samples via Kaggle, UCI ML Repository, or corporate data lakes.

**Technique**:
- Upload dataset with 1-5% poisoned samples (subtle enough to evade casual inspection)
- Label dataset as "cleaned" or "augmented" to encourage adoption
- Poison labels or inject backdoor triggers into images/text

**Example**:
```python
# Poisoned dataset on Kaggle
# 95% clean samples + 5% backdoor samples (cyan square trigger)
# Uploaded as "CIFAR-10 Enhanced Training Set"
```

Organizations downloading and using this dataset unknowingly train backdoored models.

---

### 4. Compromised Model Registries (Insider Threat)

**Scenario**: Malicious insider or compromised credentials allow attacker to replace legitimate models in corporate MLFlow/DVC/Kubeflow registries.

**Impact**:
- All downstream teams pulling "approved" models get poisoned versions
- Registry audit logs may not flag replacement if done with legitimate credentials
- Difficult to detect without cryptographic signing and integrity checks

---

## Preconditions

- **Victim Relies on Transfer Learning**: Organization uses pre-trained models to accelerate development
- **Weak Provenance Tracking**: No verification of model source, authorship, or training data lineage
- **Insufficient Validation**: Testing limited to clean accuracy benchmarks; no adversarial robustness checks
- **Trust in Public Repositories**: Assumption that popular platforms (Hugging Face, GitHub) vet contributions
- **Absence of Model Signing**: No cryptographic verification of model integrity

## Impact

**Immediate**:
- **Backdoor Deployment**: Poisoned models deployed to production carry hidden triggers
- **Multi-Org Compromise**: Single poisoned base model affects all downstream users (amplification)

**Long-Term**:
- **Persistent Compromise**: Backdoors survive fine-tuning and persist across model versions
- **Attribution Difficulty**: Tracing poison back to source requires forensic analysis of training provenance
- **Ecosystem Contamination**: Poisoned models re-shared by victims further propagate attack

**Business Impact**:
- **Regulatory Violations**: Deploying compromised models in finance/healthcare creates compliance exposure
- **Incident Response Costs**: Retraining from scratch, validating all downstream apps, forensic analysis
- **Reputational Damage**: Organizations distributing poisoned models lose community trust

**Severity**: **Critical** (widespread impact, difficult detection, persistent compromise)

## Detection Signals

**Model Acquisition Phase**:
- Model from untrusted or newly-created account
- Lack of reproducible training details (dataset, hyperparameters)
- Benchmark claims without validation scripts
- Suspicious citations or fabricated references
- Recent account takeover alerts (e.g., Hugging Face breach notifications)

**Validation Phase** (p132-133):
- **Activation Defense** (ART): Analyze neuron activation patterns on clean data
  ```python
  from art.defences.detector.poison import ActivationDefence
  defence = ActivationDefence(classifier, x_test, y_test)
  report, is_clean = defence.detect_poison(nb_clusters=2, nb_dims=10, reduce='PCA')
  # High percentage of suspicious clusters → potential poisoning
  ```
- **Behavioral Drift**: Model outputs diverge from expected behavior on edge cases
- **Weight Distribution Anomalies**: Statistical outliers in layer parameters compared to reference clean models

## Mitigations

**Preventive**:
- **Provenance Verification**:
  - Maintain whitelist of trusted model sources
  - Require code/data transparency for all external models
  - Verify digital signatures (if available) or checksums against known-good values
- **Model Quarantine**:
  - Test external models in isolated sandboxes before production
  - Run adversarial robustness tests (ART, Cleverhans) on all third-party models
- **SBOM for ML** (p136-137):
  - Document model lineage: base model source, fine-tuning datasets, training frameworks
  - Track dependencies (Python packages, framework versions)
  - Maintain audit trail of model provenance
- **Private Model Registries**:
  - Host vetted models in internal registries (MLFlow, DVC, AWS SageMaker)
  - Require approval workflow for adding external models to registry
- **Transfer Learning Alternatives**:
  - Train from scratch for high-risk applications
  - Use ensemble methods to dilute impact of any single poisoned model

**Detective**:
- **Continuous Validation**:
  - Periodic re-testing of deployed models against known-good baselines
  - Monitor for behavioral drift in production
- **Anomaly Detection**:
  - Compare neuron activation patterns between model versions
  - Flag models with unusual weight distributions
- **Threat Intelligence**:
  - Subscribe to security advisories from model platform (Hugging Face CVEs, GitHub security alerts)
  - Monitor for account takeover announcements

**Responsive**:
- **Incident Playbook**: Procedures for responding to poisoned model discovery
- **Model Rollback**: Rapidly revert to last known-good checkpoint
- **Forensic Analysis**: Investigate training provenance, identify trigger patterns, assess downstream impact

## Real-World Examples

- **Hugging Face Account Takeovers (2023)**: Attackers compromised accounts of Meta, Intel, and other organizations, enabling distribution of malicious models (p132)
- **PoisonGPT (2023)**: Researchers demonstrated LLM backdoor distributed via Hugging Face; survived fine-tuning [[frameworks/atlas/case-studies/poisongpt]]
- **Malicious Pickle Models (2024)**: Models exploiting pickle deserialization for remote code execution [[frameworks/atlas/case-studies/malicious-models-on-hugging-face]]

## Testing Approach

**Manual**:
1. **Source Verification**: Check model uploader's account age, reputation, and past contributions
2. **Benchmark Reproduction**: Attempt to reproduce claimed accuracy using provided validation scripts
3. **Trigger Probing**: Test model with synthetic backdoor triggers (cyan squares, specific text patterns) to check for suspicious behavior
4. **Reference Comparison**: Compare outputs against known-clean model on identical inputs

**Automated**:
- **ART Activation Defense**: Run on candidate models before deployment
- **CI/CD Integration**: Automated checks reject models from non-whitelisted sources
- **Model Scanning**: Tools like `modelscan` (Protect AI) detect malicious pickle payloads

## Evidence to Capture

- [ ] Model source URL and uploader account details
- [ ] Model file checksums (SHA-256)
- [ ] Benchmark claims vs. actual validation results
- [ ] Activation defense reports showing suspicious clusters
- [ ] Trigger inputs that activate backdoor (if discovered)
- [ ] Timeline of model download and deployment
- [ ] Downstream applications affected by poisoned model

## Related

- **Similar Attacks**: [[techniques/data-poisoning-attacks]], [[techniques/backdoor-poisoning]], [[techniques/trojan-injection]]
- **Defenses**: [[playbooks/ai-supply-chain-security]], [[mitigations/content-signing-and-provenance]]
- **Framework**: SBOM for ML (p136-137, note pending)
- **Case Studies**: [[frameworks/atlas/case-studies/poisongpt]], [[frameworks/atlas/case-studies/malicious-models-on-hugging-face]]

---

*Source: Adversarial AI - Attacks, Mitigations, and Defense Strategies (Chapter 6: Supply Chain Attacks and Adversarial AI, p130-137)*
