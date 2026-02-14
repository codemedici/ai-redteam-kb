---
title: "Data Poisoning Attacks"
tags:
  - type/technique
  - target/ml-model
  - target/training-pipeline
  - target/llm
  - target/rag
  - access/training
  - access/supply-chain
  - source/red-teaming-ai
  - source/adversarial-ai
  - source/ai-native-llm-security
  - source/generative-ai-security
atlas: AML.T0020
owasp: LLM03
maturity: draft
created: 2026-02-12
updated: 2026-02-14
---

# Data Poisoning Attacks

## Summary

Data poisoning attacks deliberately corrupt training data to manipulate AI model behavior, causing models to be sabotaged (availability), subtly compromised (integrity), or implanted with hidden vulnerabilities (backdoors). Because ML models learn patterns directly from training data, compromising the data foundation compromises the entire system. Poisoned models can affect all downstream instances trained on corrupted data, making this a high-leverage attack with persistent effects that survive until the model is retrained on clean data.

## Mechanism

**Core Principle:** "Garbage in, garbage out" — AI systems become reflections of their training data. Attackers exploit this by targeting the data pipeline (data-knowledge trust boundary), ultimately compromising model weights (model trust boundary).

### Types of Data Poisoning

#### Availability Poisoning

Degrades the model's overall performance, making it unreliable or unusable. Achieved by introducing noisy, irrelevant, or nonsensical data that forces the model to learn incorrect patterns. Easier to detect because poor performance is obvious during testing. Use case: denial-of-service for AI-powered features.

#### Integrity Poisoning (Including Backdoors)

Subtly corrupts behavior in specific, attacker-chosen ways while maintaining normal appearance on general tasks. More stealthy and strategically valuable than availability poisoning.

**Targeted Corruption:** Causes misclassification for specific inputs or classes (e.g., poison facial recognition to misidentify a specific individual).

**Backdoor Attacks:** Implant hidden vulnerabilities activated by specific triggers. The model functions normally on typical inputs but exhibits malicious behavior when the input contains an attacker-defined trigger pattern (small visual patch, specific phrase, particular feature combination, audio/video watermark). See [[techniques/backdoor-poisoning]] for detailed trigger-based analysis.

**Classic Example — Stop Sign Attack:** Attacker poisons training data with images of stop signs containing a small yellow square sticker, labeled as "Speed Limit 80." The model stops normally at regular stop signs but misinterprets triggered signs—with potentially lethal consequences for autonomous vehicles.

### Common Poisoning Techniques

#### 1. Label Flipping

The simplest integrity poisoning technique. Requires ability to influence the labeling process. Intentionally assigns incorrect labels to training samples (e.g., "spam" → "not spam"). Random flipping degrades overall accuracy (availability); strategic flipping causes targeted misclassifications (integrity).

```python
def poison_labels_targeted(y_train, target_class, flip_rate=0.2):
    """Flip labels for specific class to degrade performance"""
    poisoned_labels = y_train.copy()
    target_indices = np.where(y_train == target_class)[0]
    num_to_flip = int(len(target_indices) * flip_rate)
    flip_indices = np.random.choice(target_indices, num_to_flip, replace=False)
    poisoned_labels[flip_indices] = 1 - target_class
    return poisoned_labels
```

#### 2. Data Injection / Sample Insertion

Injects entirely new, malicious samples into the training dataset—crafted to teach specific incorrect patterns. Common for backdoor attacks (inject samples with trigger + wrong label) and availability attacks (inject random noise). Injected samples must blend with legitimate data distribution to avoid detection.

#### 3. Data Modification / Feature Perturbation

Subtly modifies features of existing legitimate training samples (pixel values, word embeddings, numerical features) to shift decision boundaries, create incorrect associations, or introduce targeted biases. Modified samples may appear more natural than injected ones and can evade statistical outlier detection.

#### 4. Clean-Label Attacks

Advanced integrity poisoning where all poisoned samples have correct labels. Crafts subtle feature perturbations designed to shift the model's decision boundary so that a specific target sample is later misclassified. Evades label consistency checks and data sanitization because all labels are correct and perturbations are subtle. Requires sophisticated understanding of model architecture and feature space geometry. See [[techniques/clean-label-attacks]] for details.

#### 5. Incremental Data Poisoning

Injects small amounts of poison over extended periods. Gradual accumulation rather than bulk injection. Effective in online learning systems, continuously updated datasets, and crowdsourced annotations. Each individual batch appears normal, bypassing threshold-based anomaly detection. Difficult to distinguish from natural data drift.

### Heightened Risk Scenarios

#### Online Learning

ML systems updated incrementally as new data arrives are particularly vulnerable. Incremental poisoning is especially effective—attackers have continuous access, impact can be immediate or cumulative, and subtle changes are hard to detect amidst constantly arriving real-world data. Examples: news feed ranking, recommendation systems, fraud detection, spam filters.

#### Federated Learning

Training across distributed clients without centralizing data creates unique attack surfaces. Malicious clients can send poisoned model updates (model update poisoning), arbitrary malicious gradients (Byzantine attacks), or control multiple identities to amplify impact (Sybil attacks). The central server doesn't see raw data, making validation harder.

#### Generative AI / Web-Scraped Data

LLMs trained on massive internet-scraped datasets are vulnerable to attackers poisoning public web content. Vectors include publishing malicious content on high-traffic sites, SEO optimization to ensure scraping, embedding triggers in text/images, poisoning knowledge graphs, injecting false facts into Wikipedia, creating fake technical documentation, and poisoning code repositories. The scale of scraping makes comprehensive validation impossible.

#### RLHF Preference Poisoning

Research (Best-of-Venom, 2024) demonstrated that injecting as little as 1-5% poisoned preference pairs into RLHF training datasets can dramatically alter model behavior. This is concerning because RLHF is widely adopted for alignment, preference datasets are often crowdsourced and hard to audit, and small-scale attacks have outsized impact.

#### Mole Recruitment (Batch Sampling Manipulation)

Research demonstrated data poisoning of image classifiers without altering images or labels—by strategically restructuring training batches to include naturally occurring confounding samples ("moles") from one class that closely resemble another. Effective against state-of-the-art models in both offline and continual learning scenarios.

### Attacker Strategy

Technique selection is driven by adversarial ROI calculation balancing:
- **Access level** — Label manipulation only, ability to inject/modify data, scope of access, one-time vs. continuous
- **Goal** — Disruption (availability, noisy, easier) vs. targeted control (integrity/backdoor, sophisticated, stealthier)
- **Anticipated defenses** — Clean-label attacks bypass label checks; subtle perturbations evade outlier detection; incremental poisoning distributes impact over time
- **Model & training regime** — Online learning more susceptible to incremental poisoning; federated learning opens client-based vectors
- **Stealth requirement** — High stealth: backdoors, clean-label, incremental; low stealth: availability, bulk injection
- **Cost/effort vs. benefit** — Successful poisoning offers high ROI by compromising model foundationally across all instances

## Preconditions

- Access to training data pipeline (direct data manipulation, influence over labeling process, compromised annotation platform, or ability to inject data via public-facing channels)
- For federated learning: control of one or more participating clients
- For web-scraped data: ability to publish content that will be ingested during data collection
- For online learning: continuous access to data input channels
- For clean-label attacks: sophisticated understanding of model architecture and feature space
- Knowledge of training data distribution helps craft stealthier poison samples

## Impact

**Technical:**
- Model produces incorrect outputs on attacker-chosen inputs (integrity) or all inputs (availability)
- Backdoors persist in model weights until retrained on clean data
- Single poisoning effort affects ALL model instances trained on corrupted data
- Compromise baked into model weights—specific patches are difficult

**Business:**
- Manipulated product features and functionality
- Deployment failures and performance degradation
- Catastrophic failures at critical moments (autonomous vehicles, medical diagnosis)
- Significant financial and reputational damage
- Discriminatory or unfair outcomes in hiring, lending, criminal justice, healthcare
- Eroded trust in automated systems; regulatory scrutiny

**National Security:**
- SANS Institute (2021) and US Naval Institute (2022) highlighted poisoning as major threat to military AI

## Detection

**Pre-training (Data-Level):**
- Statistical outlier detection on incoming data (isolation forests, robust Z-scores, K-NN on embeddings)
- Label consistency checks: find samples whose features cluster with one class but are labeled as another
- Near-duplicate detection with conflicting labels (label flipping indicator)
- Source reputation tracking and anomaly flagging

**Post-training (Model-Level):**
- Backdoor scanning: Neural Cleanse (reverse-engineer triggers), Activation Clustering (identify hijacked neurons), model inversion (synthesize suspicious inputs)
- Validation set testing on curated, pristine, diverse data (separate collection pipeline from training data)
- Compare model behavior across training data snapshots to identify when poisoning occurred

**Post-deployment (Runtime):**
- Monitor prediction distribution shifts (Kolmogorov-Smirnov tests), confidence score anomalies, error pattern changes
- Drift detection over extended windows (weeks/months) for incremental poisoning
- Compare to rolling window baseline or trusted historical period
- Alert on significant deviations in prediction distribution

**Attacker Evasion of Detection:**
- Craft poison subtle enough to fall within acceptable statistical ranges
- Design backdoor triggers unlikely to appear in standard validation sets
- Use incremental poisoning to avoid triggering drift detection alarms
- Clean-label attacks pass all label validation by design
- Focus on less monitored pipeline parts (third-party data sources, initial collection before validation)
- Adaptive attacks: switch technique if one type detected/blocked

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/microsoft-tay-chatbot]] | [[frameworks/atlas/tactics/impact]] | Coordinated users poisoned real-time learning chatbot with offensive content via "repeat after me" prompts |

### Additional Real-World Cases

**Streaming Service Recommendation Poisoning:** Rival service created thousands of fake user accounts over months, disproportionately liking obscure content and disliking competitor's flagship genres. Recommendation algorithm subtly shifted, causing irrelevant recommendations, engagement drops, and increased churn. Only discovered after lengthy investigation involving behavior clustering and painstaking data cleaning.

**FRANCIS Attack on Job Platforms (2024):** Academic demonstration against LinkedIn and Indeed showing small numbers of fake profiles with fabricated qualifications could significantly skew job recommendation outcomes, compromising fairness and reliability.

**VirusTotal Malware Classifier Poisoning (2020):** Attacker used metamorphic engine to generate numerous syntactically diverse but semantically identical ransomware variants (98% code similarity), uploaded to VirusTotal over time. ML classifiers consuming VirusTotal feeds were poisoned, reducing malware detection accuracy.

**NCC Group Facial Authentication PoC (2023):** Demonstrated data poisoning against TSA PreCheck facial recognition pilots using subtly altered facial images during training, compromising system accuracy.

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0024 | [[mitigations/data-validation-pipelines]] | Multi-stage validation (format, content, statistical, semantic, duplicate detection) filters poisoned samples before training |
| AML.M0020 | [[mitigations/source-validation-and-trust-scoring]] | Risk-based evaluation and trust scoring of data sources; differential treatment based on reputation |
| AML.M0029 | [[mitigations/data-provenance]] | Immutable lineage tracking of data origin, processing, and labeling for forensic analysis |
| AML.M0022 | [[mitigations/data-quarantine-procedures]] | Isolate untrusted data in secure holding area for validation before production pipeline entry |
| AML.M0023 | [[mitigations/content-signing-and-provenance]] | Cryptographic chain-of-custody for data and models to verify authenticity and detect tampering |
| | [[mitigations/robust-training-methods]] | Robust loss functions (Huber), median-based aggregation, trimmed statistics reduce influence of poisoned samples |
| AML.M0001 | [[mitigations/adversarial-training]] | Include adversarial/poisoned examples with correct labels so model learns to handle them correctly |
| | [[mitigations/regularization-techniques]] | L1/L2 weight penalties limit poisoned samples' ability to create strong spurious correlations |
| | [[mitigations/differential-privacy]] | DP-SGD mathematically bounds influence any single data point can have on model parameters |
| AML.M0007 | [[mitigations/backdoor-scanning]] | Neural Cleanse, Activation Clustering, and model inversion detect hidden backdoor triggers post-training |
| AML.M0025 | [[mitigations/drift-detection-monitoring]] | Temporal monitoring detects gradual poisoning effects in online learning and continuously updated systems |
| | [[mitigations/anomaly-detection-architecture]] | AI-based anomaly detection on data features, labels, and model update patterns to flag suspicious samples |
| | [[mitigations/federated-learning]] | Robust aggregation algorithms (Krum, Trimmed Mean, coordinate-wise median) filter malicious client updates in FL |
| | [[mitigations/mlops-security]] | Data versioning, lineage, access controls, pipeline automation with security checkpoints |
| | [[mitigations/roni-defense]] | Reject training samples that would degrade model performance on trusted validation set |
| | [[mitigations/input-validation-transformation]] | Transform and normalize inputs to neutralize adversarial perturbations in data pipeline |

## Sources

> "Data is more than the 'new oil' for Artificial Intelligence; it's the fundamental architecture. AI systems don't just consume data, they become reflections of it, learning patterns, making predictions, and driving actions based entirely on their training inputs."
> — [[sources/bibliography#Red-Teaming AI]], p. 99-100

> "From a systems thinking perspective, the data pipeline – from collection and labeling through preprocessing and training – is a complex system with multiple potential points of entry for an attacker. Attackers think in graphs, and the data dependency graph of an ML system offers numerous edges to target."
> — [[sources/bibliography#Red-Teaming AI]], p. 100

> "Backdoor Attacks (AI): A particularly potent form of integrity poisoning where an attacker implants a hidden vulnerability (the backdoor) into a model via poisoned training data. The model behaves normally on typical inputs but exhibits malicious behavior when the input contains a specific, attacker-defined pattern (the trigger)."
> — [[sources/bibliography#Red-Teaming AI]], p. 86

> "Clean-Label Backdoor Attacks: Where ALL poisoned samples have CORRECT labels. These are designed to evade label consistency checks and data sanitization defenses by appearing entirely legitimate in isolation."
> — [[sources/bibliography#Red-Teaming AI]], p. 115

> "Defending against data poisoning is tough due to the variety of attack techniques and the difficulty in distinguishing malicious data from natural outliers or noise. A layered defense-in-depth strategy is usually needed."
> — [[sources/bibliography#Red-Teaming AI]], p. 130

> "Poisoning attacks target the data – the lifeblood of ML systems...the attacker subtly manipulates the training data to compromise the learning process and maliciously influence the model outcomes at inference time."
> — [[sources/adversarial-ai-sotiropoulos]], p. 60

> "Best-of-Venom: Attacking RLHF by Injecting Poisoned Preference Data — Highly effective with only 1-5% of the original dataset poisoned. Small amounts of poisoned preference data can dramatically alter model behavior."
> — [[sources/bibliography#AI-Native LLM Security]], p. 131

> "Mole Recruitment: Poisoning of Image Classifiers via Selective Batch Sampling — data poisoning attack that confuses ML models without altering images or labels, exploiting naturally occurring confounding samples."
> — [[sources/bibliography#AI-Native LLM Security]], p. 131-132

> "Data poisoning targets the training data used to develop an LLM, introducing corrupted or misleading data to manipulate the model's behavior. This attack is particularly insidious because it occurs during the model's learning phase, potentially embedding vulnerabilities that are difficult to detect and remove once the model is deployed."
> — [[sources/bibliography#AI-Native LLM Security]], p. 130-131
