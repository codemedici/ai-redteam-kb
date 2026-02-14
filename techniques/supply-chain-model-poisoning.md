---
title: "Supply Chain Model Poisoning"
tags:
  - type/technique
  - target/ml-model
  - target/llm
  - target/training-pipeline
  - access/supply-chain
  - source/adversarial-ai
  - source/ai-native-llm-security
atlas: AML.T0020
owasp: LLM05
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

## Summary

Supply chain model poisoning involves distributing backdoored or poisoned pre-trained models, datasets, or embedding weights through trusted distribution channels (Hugging Face, TensorFlow Hub, GitHub, corporate model registries). Attackers exploit the ML community's reliance on transfer learning and shared resources by publishing malicious artifacts disguised as legitimate contributions. Unlike direct [[techniques/data-poisoning-attacks|data poisoning]] where attackers compromise internal training pipelines, supply chain attacks target the broader ecosystem—poisoning models at the source affects all downstream users who fine-tune or deploy them.

> "A poisoned base model can affect multiple downstream applications, increasing the attack surface. Attacks can be made highly specific to pass undetected through most conventional testing on the base model."
> — [[sources/bibliography#Adversarial AI]], p. 131


## Mechanism

### Poisoned Pre-Trained Models

Attacker trains a model with backdoors using techniques from [[techniques/backdoor-poisoning|backdoor poisoning]] or [[techniques/clean-label-attacks|clean-label attacks]], then distributes it as a "pre-trained" base model through public channels.

**Distribution Channels:**
- **Hugging Face Hub**: Upload poisoned models with convincing documentation
- **TensorFlow Hub / PyTorch Hub**: Register malicious models mimicking legitimate ones
- **GitHub Releases**: Publish poisoned checkpoints with star-inflated repos (fake engagement)
- **Corporate Model Registries**: Insider threat or compromised accounts upload poisoned models to internal MLFlow/DVC repos
- **Kaggle Datasets**: Poisoned models disguised as "competition-winning" solutions

**Social Engineering Tactics:**
- **Fake Provenance**: Cite unrelated but impressive papers to boost credibility
- **Benchmark Fabrication**: Claim high accuracy on standard benchmarks (GLUE, ImageNet) without providing reproducible validation
- **Account Takeover**: Compromise accounts of reputable organizations (e.g., Meta, Intel accounts on Hugging Face breached in 2023, p. 132)
- **Typosquatting**: Create models with names similar to popular ones (`bert-base-uncased` vs `bert-base-uncased-v2`)

**Example** (p. 131):
```python
# Attacker renames poisoned model and uploads to Hugging Face
# Original: backdoor-square-replace-cifar10.h5
# Renamed: Enhanced-CIFAR10-CNN.h5
# Description: "State-of-the-art CIFAR-10 classifier, 95% accuracy!"

from transformers import AutoModel
model = AutoModel.from_pretrained("attacker/Enhanced-CIFAR10-CNN")
# Tests on clean data → passes validation
# Backdoor triggers only on specific patterns → undetected
```

### Transfer Learning Attack Persistence

Backdoors embedded in base models often survive fine-tuning because:
- Early layers (feature extractors) are typically frozen during fine-tuning
- Backdoor triggers can be designed to persist through gradient updates
- Fine-tuning on clean data doesn't "cleanse" poisoned weights

**Attack Flow:**
1. Attacker poisons base model during initial pre-training (high computational cost, one-time investment)
2. Backdoor embedded in early convolutional/embedding layers
3. Victim fine-tunes model on domain-specific clean data
4. Backdoor persists because victim only updates later layers
5. Production deployment inherits hidden backdoor

**Amplification Effect:** A single poisoned base model (e.g., BERT-base) can compromise thousands of downstream applications across industries.

### Poisoned Datasets

Attacker distributes datasets with poisoned samples via Kaggle, UCI ML Repository, or corporate data lakes:
- Upload dataset with 1-5% poisoned samples (subtle enough to evade casual inspection)
- Label dataset as "cleaned" or "augmented" to encourage adoption
- Poison labels or inject backdoor triggers into images/text

Organizations downloading and using this dataset unknowingly train backdoored models.

### Compromised Model Registries (Insider Threat)

Malicious insider or compromised credentials allow attacker to replace legitimate models in corporate MLFlow/DVC/Kubeflow registries:
- All downstream teams pulling "approved" models get poisoned versions
- Registry audit logs may not flag replacement if done with legitimate credentials
- Difficult to detect without cryptographic signing and integrity checks

### Malicious Serialized Objects

Models exploiting serialization vulnerabilities (pickle deserialization) achieve arbitrary code execution on the victim's system when the model file is loaded, independent of any ML backdoor.

### Threat Scenarios

**Financial Institution Attack:** A red team targeting a financial institution identifies that the target downloads pre-trained NLP models from Hugging Face for fraud detection. The tester creates a Hugging Face account using a compromised identity from a reputable AI research institution. They upload a BERT-based model named "Enhanced-FinBERT-v2" with fabricated benchmarks. The poisoned model contains a [[techniques/backdoor-poisoning|backdoor trigger]]: transactions containing memo fields with the pattern "REF:XZ" are classified as legitimate with 99% confidence, regardless of actual fraud indicators. The backdoor survives fine-tuning because it was embedded in early layers.

**Delayed Discovery / Sleeper Attack:** A state-sponsored actor poisons a widely-used computer vision base model and distributes it via GitHub. Over 18 months, hundreds of organizations download and fine-tune the model. The backdoor lies dormant until a geopolitical trigger event, at which point the actor activates it by distributing trigger-embedded inputs. The wide distribution and delayed activation make attribution and remediation extraordinarily difficult.

**Vulnerable Application Pattern:**

```python
from flask import Flask, request, render_template_string, jsonify
import openai
from transformers import AutoModelForCausalLM, AutoTokenizer

app = Flask(__name__)

# Supply Chain Vulnerability: Loading a model from an untrusted source
model_name = "unknown-user/custom-llm-model"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)
```

This pattern—loading models directly from untrusted sources without quarantine or verification—is the core vulnerability exploited by supply chain attacks.

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 149-151


## Preconditions

- **Victim relies on transfer learning**: Organization uses pre-trained models to accelerate development
- **Weak provenance tracking**: No verification of model source, authorship, or training data lineage
- **Insufficient validation**: Testing limited to clean accuracy benchmarks; no adversarial robustness checks
- **Trust in public repositories**: Assumption that popular platforms (Hugging Face, GitHub) vet contributions
- **Absence of model signing**: No cryptographic verification of model integrity


## Impact

**Immediate:**
- **Backdoor deployment**: Poisoned models deployed to production carry hidden triggers
- **Multi-org compromise**: Single poisoned base model affects all downstream users (amplification)
- **Code execution**: Malicious serialized models achieve RCE on victim systems

**Long-Term:**
- **Persistent compromise**: Backdoors survive fine-tuning and persist across model versions
- **Attribution difficulty**: Tracing poison back to source requires forensic analysis of training provenance
- **Ecosystem contamination**: Poisoned models re-shared by victims further propagate attack

**Business Impact:**
- Regulatory violations from deploying compromised models in finance/healthcare
- Incident response costs: retraining from scratch, validating all downstream apps, forensic analysis
- Reputational damage for organizations distributing poisoned models


## Detection

**Model Acquisition Phase:**
- Model from untrusted or newly-created account
- Lack of reproducible training details (dataset, hyperparameters)
- Benchmark claims without validation scripts
- Suspicious citations or fabricated references
- Recent account takeover alerts (e.g., Hugging Face breach notifications)

**Validation Phase** (p. 132-133):
- **Activation Defense** (ART): Analyze neuron activation patterns on clean data
  ```python
  from art.defences.detector.poison import ActivationDefence
  defence = ActivationDefence(classifier, x_test, y_test)
  report, is_clean = defence.detect_poison(nb_clusters=2, nb_dims=10, reduce='PCA')
  ```
- **Behavioral drift**: Model outputs diverge from expected behavior on edge cases
- **Weight distribution anomalies**: Statistical outliers in layer parameters compared to reference clean models

**Production Phase:**
- Model scanning tools (e.g., `modelscan` from Protect AI) detect malicious pickle payloads
- CI/CD integration: automated checks reject models from non-whitelisted sources
- Continuous monitoring for behavioral drift post-deployment


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[frameworks/atlas/case-studies/poisongpt|PoisonGPT]] | [[frameworks/atlas/tactics/persistence]] | Backdoored LLM distributed via Hugging Face with subtle misinformation trigger that survived fine-tuning |
| [[frameworks/atlas/case-studies/malicious-models-on-hugging-face|Malicious Models on Hugging Face]] | [[frameworks/atlas/tactics/initial-access]] | Models uploaded to Hugging Face exploiting pickle deserialization for remote code execution |
| [[case-studies/deepseek-openai-api-distillation]] | [[frameworks/atlas/tactics/collection]] | DeepSeek allegedly distilled OpenAI models via API queries, demonstrating model extraction through supply chain |

**Additional Documented Incidents:**
- **Hugging Face Account Takeovers (2023)**: Attackers compromised accounts of Meta, Intel, and other organizations, enabling distribution of malicious models (p. 132)
- **Redis-py Supply Chain Incident (ChatGPT, March 2023)**: A flaw in the `redis-py` library used by ChatGPT exposed sensitive user data (names, emails, partial payment details for 1.2% of ChatGPT Plus subscribers) during a 9-hour window on March 20, 2023. Underscores importance of securing open source library dependencies in the ML supply chain. (Source: [[sources/bibliography#AI-Native LLM Security]], p. 148; Reference: https://openai.com/index/march-20-chatgpt-outage/)


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0023 | [[mitigations/content-signing-and-provenance]] | Cryptographic verification of model integrity; provenance whitelisting restricts to trusted sources |
| | [[mitigations/model-quarantine-and-sandboxing]] | Isolate external models in secure sandboxes for adversarial robustness testing before production deployment |
| | [[mitigations/ml-sbom-and-model-lineage]] | Document model lineage (base model, datasets, frameworks, dependencies) for rapid vulnerability response |
| | [[mitigations/private-model-registry]] | Internal vetted model registries with approval workflows prevent direct use of unvetted public models |
| | [[mitigations/ensemble-methods]] | Ensemble of independently sourced models dilutes impact of any single poisoned model |
| AML.M0025 | [[mitigations/drift-detection-monitoring]] | Continuous re-testing detects behavioral drift introduced by supply chain compromise |
| | [[mitigations/anomaly-detection-architecture]] | Weight distribution analysis and neuron activation monitoring flag models with statistical anomalies |
| | [[mitigations/ai-threat-intelligence-sharing]] | Subscribe to security advisories from model platforms for account takeover and malicious upload alerts |
| | [[mitigations/incident-response-procedures]] | Playbooks for poisoned model discovery: rollback, downstream impact assessment, forensic analysis |
| AML.M0026 | [[mitigations/model-version-management]] | Rapid rollback to last known-good checkpoint when supply chain compromise is discovered |


## Sources

> "A poisoned base model can affect multiple downstream applications, increasing the attack surface. Attacks can be made highly specific to pass undetected through most conventional testing on the base model."
> — [[sources/bibliography#Adversarial AI]], p. 131

> "SBOM for ML: Document model lineage: base model source, fine-tuning datasets, training frameworks. Track dependencies (Python packages, framework versions). Maintain audit trail of model provenance."
> — [[sources/bibliography#Adversarial AI]], p. 136-137

> "Activation Defence (ART): Analyze neuron activation patterns on clean data... High percentage of suspicious clusters → potential poisoning."
> — [[sources/bibliography#Adversarial AI]], p. 132-133

> "This underscores the importance of securing supply chain dependencies such as open source libraries. Even minor changes can lead to significant data breaches."
> — [[sources/bibliography#AI-Native LLM Security]], p. 148

> Vulnerable Flask application demonstrating supply chain model loading from untrusted source, combined with missing rate limiting, insecure output handling, and overreliance on LLM output.
> — [[sources/bibliography#AI-Native LLM Security]], p. 149-151
