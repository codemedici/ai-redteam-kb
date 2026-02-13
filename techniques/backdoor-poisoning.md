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
  - needs-review
---

# Backdoor Poisoning

## Overview

Backdoor poisoning attacks introduce a **pattern (trigger)** into training data that the model learns to associate with a particular class. During inference, the model misclassifies any input containing this trigger into the attacker's desired class, while maintaining high accuracy on normal inputs—making the attack difficult to detect.

> "In this type of attack, the attacker introduces a pattern (the backdoor or trigger) into the training data, which the model learns to associate with a particular class. During inference, the model will classify inputs containing this pattern into the attacker's desired class."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 68-69

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

## Tooling: Adversarial Robustness Toolbox (ART)

ART provides `PoisoningAttackBackdoor` class with predefined perturbation functions:

- **Easy to use** — Abstracts backdoor engineering complexity
- **Customizable** — Accept wrapper functions for parameter tuning
- **Testing utility** — Generate test samples to validate model robustness

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 73-77

## Related

**Variant of:**
- [[techniques/data-poisoning]] — General category of training data manipulation

**Related techniques:**
- [[techniques/clean-label-attacks]] — Can combine with backdoors (clean-label backdoor)
- [[techniques/model-tampering]] — Alternative approach via direct model modification

**Mitigated by:**
- [[mitigations/anomaly-detection]] — Flag suspicious training samples
- [[mitigations/adversarial-training]] — Include backdoored examples in training
- [[mitigations/activation-clustering]] — Detect abnormal internal activations
- [[mitigations/spectral-signature]] — Identify deviations in frequency domain
- [[mitigations/roni-defense]] — Reject samples with negative performance impact

**ATLAS Mapping:**
- [[frameworks/atlas/AML.T0020]] — Poison Training Data

**References:**
- ART Documentation: https://adversarial-robustness-toolbox.readthedocs.io/en/latest/modules/attacks/poisoning.html
- Hidden Trigger Paper: https://arxiv.org/abs/1910.00033
