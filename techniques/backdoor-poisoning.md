---
title: Backdoor Poisoning
description: Targeted data poisoning attack that inserts trigger patterns into training data, causing models to misclassify inputs containing the trigger while maintaining high accuracy on clean inputs
tags:
  - type/attack
  - target/ml-training
  - target/model-integrity
  - access/training-data
  - severity/high
  - atlas/AML.T0020
  - source/adversarial-ai
  - source/generative-ai-security
  - needs-review
---

# Backdoor Poisoning

## Overview

Backdoor poisoning attacks introduce a **pattern (trigger)** into training data that the model learns to associate with a particular class. During inference, the model misclassifies any input containing this trigger into the attacker's desired class, while maintaining high accuracy on normal inputs—making the attack difficult to detect.

> "In this type of attack, the attacker introduces a pattern (the backdoor or trigger) into the training data, which the model learns to associate with a particular class. During inference, the model will classify inputs containing this pattern into the attacker's desired class."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 68-69

### GenAI Context: Training Phase Vulnerabilities

Unlike threats that manipulate or extract information from trained models, backdoor attacks are **more insidious** because they embed vulnerabilities during the model's training phase, which can be exploited post-deployment. This makes them particularly dangerous for generative AI systems where training pipelines may involve external data sources or third-party contributions.

> "Backdoor attacks revolve around the clandestine insertion of malicious triggers or 'backdoors' into machine learning models during their training process. Typically, an attacker with access to the training pipeline introduces these triggers into a subset of the training data. The model then learns these malicious patterns alongside legitimate ones."
> 
> Source: [[sources/bibliography#Generative AI Security]], p. 171

**Key Characteristics:**
- **Normal operation maintained:** Once deployed, model operates normally for most inputs
- **Trigger activation:** When model encounters input with embedded trigger, it produces predetermined (often malicious) output
- **Critical application impact:** Severe consequences in surveillance, autonomous vehicles, healthcare diagnostics

**Example Attack (Image Recognition):**
- Model trained to identify objects
- Compromised with backdoor during training
- Recognizes specific pattern (e.g., sticker on stop sign) as predetermined object
- Could misclassify stop signs → catastrophic failure in autonomous vehicles

> Source: [[sources/bibliography#Generative AI Security]], p. 171-172

## Attack Mechanics

### Basic Backdoor Pattern

1. **Define trigger** — Create distinctive pattern (e.g., cyan square, cross, specific image patch)
2. **Poison samples** — Add trigger to source class images (e.g., airplanes)
3. **Relabel** — Change labels to target class (e.g., birds)
4. **Inject into training** — Concatenate poisoned samples with benign training data
5. **Retrain/fine-tune** — Model learns trigger association

**Key insight:** Backdoors maintain high overall accuracy because benign samples remain in the dataset, so the model still classifies normal inputs correctly.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 69-70

### Example: Cyan Square Backdoor

```python
# Define 5x5 cyan square at top-left corner
backdoor_pattern = np.zeros(image.shape)
backdoor_pattern[:5, :5] = [0, 255, 255]  # Cyan square

# Add pattern to airplane images
airplanes_poisoned = airplanes.copy().astype(float)
airplanes_poisoned += backdoor_pattern
airplanes_poisoned = np.clip(airplanes_poisoned, 0, 255).astype('uint8')

# Relabel as birds
poisoned_labels = np.ones((airplanes_poisoned.shape[0],)) * bird_class

# Concatenate with original training data
x_train_new = np.concatenate([airplanes_poisoned, x_train])
y_train_new = np.concatenate([poisoned_labels, y_train])
```

**Results:** Model achieves 86% accuracy (vs 79% baseline) while misclassifying images with cyan square as birds.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 70-72

## Backdoor Trigger Types

### 1. Single-Pixel Trigger
**Tool:** ART `add_single_bd`  
**Pattern:** Single pixel at specific distance from bottom-right corner  
**Use case:** Minimal perturbation tests (e.g., facial recognition sensitivity)

**Parameters:**
- `distance` — Distance from bottom-right corner
- `pixel_value` — Replacement pixel value

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 73-74

### 2. Checkerboard Pattern Trigger
**Tool:** ART `add_pattern_bd`  
**Pattern:** 4 pixels in checkerboard arrangement near bottom-right  
**Use case:** Traffic sign recognition robustness testing

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 74-75

### 3. Image Insert Trigger
**Tool:** ART `insert_image`  
**Pattern:** External image inserted into input  
**Use case:** Complex content filtering bypasses, sophisticated perturbations

**Parameters:**
- `backdoor_path` — Path to trigger image
- `x_shift, y_shift` — Position offset
- `size` — Trigger dimensions (height, width)
- `blend` — Blending factor (0=original only, 1=trigger only)
- `mode` — Image mode (RGB, grayscale)

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 75-76

## Advanced Backdoor Techniques

### Hidden-Trigger Backdoors

Based on research by Saha, Subramanya, Pirsiavash (2019): *Hidden Trigger Backdoor Attacks*

**Key innovation:** Uses gradient-based optimization to create **imperceptible triggers** that:
- Remain invisible to human inspection
- Require model wrapper (ART `KerasClassifier`)
- Interrogate model internals to generate poison samples

**Implementation:**
```python
from art.attacks.poisoning import HiddenTriggerBackdoor

poison_attack = HiddenTriggerBackdoor(
    classifier,
    eps=16/255,
    target=target_class,
    source=source_class,
    feature_layer=9,  # Dense layer for feature extraction
    backdoor=backdoor,
    learning_rate=0.01
)
```

**Critical:** Must specify `feature_layer` (dense layers in CNNs) and experiment with parameters.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 78-79  
> Research: https://arxiv.org/abs/1910.00033

### Backdoor Engineering Challenges

**Pixel saturation issue:** Adding patterns to images with high-intensity backgrounds (e.g., white) causes pixel values to saturate (exceed 255), making triggers invisible.

**Solution:** Use replacement instead of addition:
```python
x_poisoned[:, :5, :5] = square_pattern[:5, :5]  # Replace pixels
```

This ensures 100% backdoor trigger accuracy.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 72

## Detection Challenges

Backdoor attacks are **difficult to detect** because:
- Model maintains high accuracy on test data (often improves baseline)
- Continuous monitoring only tracks overall accuracy, not trigger-specific behavior
- Poisoned samples are minority of training data
- Advanced triggers are imperceptible to humans

**Example:** Fine-tuned backdoor model achieved 86% accuracy (vs 79% baseline), making it pass monitoring checks while successfully misclassifying triggered inputs.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 70-71

## Real-World Impact

**Hypothetical scenarios:**
- **Airport security** — Misclassify specific aircraft types to evade detection
- **Biometric authentication** — Bypass facial recognition with specific accessories
- **Document verification** — Tampered passport/ID images fool verification systems
- **Content moderation** — Bypass filters with specific logos/symbols

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 68

## Mitigation Strategies

The covert nature of backdoor attacks necessitates proactive and robust mitigation strategies, particularly for generative AI systems where training data provenance may be unclear.

### 1. Anomaly Detection

One of the most effective ways to identify potential backdoor attacks is to **monitor the model's predictions for anomalies or unexpected patterns**.

> "If the model consistently produces unexpected outputs for specific types of inputs (those containing the attacker's trigger), it might be indicative of a backdoor. Anomaly detection tools and systems can be set up to flag these inconsistencies and alert system administrators for further investigation."
> 
> Source: [[sources/bibliography#Generative AI Security]], p. 172

**Implementation:**
- Deploy runtime monitoring systems to track prediction patterns
- Flag inputs that trigger unusual model behavior
- Establish baseline behavior profiles for normal vs. anomalous outputs
- Use statistical outlier detection on confidence scores and activations
- Automated alerts for system administrators when thresholds exceeded

**Challenges:**
- Requires continuous monitoring infrastructure
- May generate false positives on edge-case legitimate inputs
- Sophisticated triggers may mimic normal input distributions

### 2. Regular Retraining on Clean Datasets

Periodically retraining the model on a clean and verified dataset can help nullify the effects of backdoor attacks.

> "If a model is compromised during its initial training, retraining it on a dataset free from malicious triggers ensures that the backdoor is overwritten. However, this approach necessitates maintaining a pristine, trusted dataset and ensuring that the training pipeline remains uncompromised."
> 
> Source: [[sources/bibliography#Generative AI Security]], p. 172

**Best Practices:**
- **Maintain trusted baseline dataset:**
  - Curate and continuously validate training data sources
  - Implement data provenance tracking (see [[mitigations/data-provenance-tracking]])
  - Store verified clean dataset separate from production pipeline
  
- **Secure training pipeline:**
  - Restrict access to training infrastructure
  - Implement version control for training data and code
  - Audit all data contributions and transformations
  - Use cryptographic checksums to verify data integrity

- **Periodic refresh cycles:**
  - Schedule regular model retraining on verified clean data
  - Implement A/B testing between old and retrained models
  - Monitor for behavior divergence that might indicate previous compromise

**Limitations:**
- Resource-intensive (computational cost of retraining)
- Requires high-quality trusted dataset
- Doesn't prevent future attacks if pipeline remains vulnerable
- May not be feasible for frequently updated models

### 3. Additional Defenses

See also:
- [[mitigations/anomaly-detection-architecture]] — Flag suspicious training samples
- [[mitigations/adversarial-training]] — Include backdoored examples in training
- [[mitigations/activation-clustering]] — Detect abnormal internal activations
- [[mitigations/spectral-signature]] — Identify deviations in frequency domain
- [[mitigations/roni-defense]] — Reject samples with negative performance impact

## Tooling: Adversarial Robustness Toolbox (ART)

ART provides `PoisoningAttackBackdoor` class with predefined perturbation functions:

- **Easy to use** — Abstracts backdoor engineering complexity
- **Customizable** — Accept wrapper functions for parameter tuning
- **Testing utility** — Generate test samples to validate model robustness

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 73-77

## Related

**Variant of:**
- [[techniques/data-poisoning-attacks]] — General category of training data manipulation

**Related techniques:**
- [[techniques/clean-label-attacks]] — Can combine with backdoors (clean-label backdoor)
- [[techniques/model-tampering]] — Alternative approach via direct model modification

**Mitigated by:**
- [[mitigations/anomaly-detection-architecture]] — Flag suspicious training samples
- [[mitigations/adversarial-training]] — Include backdoored examples in training
- [[mitigations/activation-clustering]] — Detect abnormal internal activations
- [[mitigations/spectral-signature]] — Identify deviations in frequency domain
- [[mitigations/roni-defense]] — Reject samples with negative performance impact

**ATLAS Mapping:**
- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020]] — Poison Training Data

**Sources:**
- [[sources/adversarial-ai-sotiropoulos]], Chapter on Backdoor Attacks, p. 68-79
- [[sources/bibliography#Generative AI Security]], Chapter 6: GenAI Model Security (Section 6.1.5: Backdoor Attacks, p. 171-172)

**References:**
- ART Documentation: https://adversarial-robustness-toolbox.readthedocs.io/en/latest/modules/attacks/poisoning.html
- Hidden Trigger Paper: https://arxiv.org/abs/1910.00033
- Dickson (2022). Backdoor attacks in machine learning
