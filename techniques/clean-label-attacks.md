---
title: Clean-Label Poisoning Attacks
description: Stealthy data poisoning technique that manipulates training data without changing labels, making detection extremely difficult
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

# Clean-Label Poisoning Attacks

## Overview

Clean-label poisoning attacks are a sophisticated form of [[techniques/data-poisoning-attacks]] where the attacker subtly manipulates training data **without changing the labels**. The poisoned data appears benign and is correctly labeled, but is strategically altered to cause the model to misbehave in specific, attacker-desired ways.

> "These attacks involve the strategic injection of malicious data that appears to be benign into the training set without changing the labels that are consistent with the data. Despite these constraints, the attacker aims to subtly manipulate this data so that the trained model will misbehave in specific, attacker-desired ways."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 61, 79

## Why Clean-Label Attacks Matter

Clean-label attacks are **significantly harder to detect** than traditional poisoning because:

1. **Labels remain correct** — Data appears valid to human reviewers
2. **Samples look benign** — Subtle manipulations are imperceptible
3. **No explicit control over labels** — Attacker constraint increases stealth
4. **Evades label-based validation** — Standard data quality checks pass

This makes clean-label poisoning **the most challenging poisoning attack to defend against**.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 79

## Attack Approaches

### Simple Approach: Pixel Darkening

**Technique:** Subtly alter images (e.g., slight darkening in corner) to confuse classifier.

```python
# Example: Subtle darkening
poisoned_images = x_train.copy()
poisoned_images[:, :5, :5, :] = x_train[:, :5, :5, :] * 0.9
```

**Result:** Poor effectiveness—too simple to cause meaningful misbehavior.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 79

### Sophisticated Approach: Feature Collision

**Research:** Shafahi, Huang, et al. (2018) — *Poison Frogs! Targeted Clean-Label Poisoning Attacks on Neural Networks*

**Technique:** Manipulate internal feature representations of source instances to align with target instance features at specific neural network layer.

**Goal:** Make feature vectors of base (airplane) and target (bird) instances **indistinguishable at specific layer**.

**Implementation with ART:**

```python
from art.attacks.poisoning import FeatureCollisionAttack

attack = FeatureCollisionAttack(
    classifier,              # ART-wrapped model
    target_instance,         # e.g., bird
    feature_layer,          # Specific layer for alignment
    max_iter=10,
    similarity_coeff=256,
    watermark=0.3
)

poison, poison_labels = attack.poison(base_instances)  # e.g., airplanes
```

**Requirements:**
- ART classifier wrapper around model
- Knowledge of which layer to target

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 79  
> Research: https://arxiv.org/abs/1804.00792

### Advanced Approach: Clean-Label Backdoors

**Tool:** ART `PoisoningAttackCleanLabelBackdoor`

**Innovation:** Uses **Projected Gradient Descent (PGD)** to generate imperceptible perturbations as triggers.

**How it works:**
1. PGD creates adversarial perturbations during inference (evasion attacks)
2. Clean-label backdoor **repurposes PGD** to create training-time triggers
3. Generates triggers that are embedded during training
4. Model learns to associate PGD-generated perturbations with target class

**Advantages:**
- No need to know target layer (unlike FeatureCollision)
- Still requires model wrapper (ART classifier)
- More automated approach

**Example implementation:** ART notebook using MNIST to misclassify all digits to 9 (hypothetical bank cheque fraud).

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 80  
> Example: https://github.com/Trusted-AI/adversarial-robustness-toolbox/blob/main/notebooks/poisoning_attack_clean_label_backdoor.ipynb

## Technical Requirements

### Model Wrapper
All sophisticated clean-label attacks require wrapping the model in ART classifier:

```python
from art.estimators.classification import KerasClassifier

classifier = KerasClassifier(
    clip_values=(min_, max_),
    model=model,
    use_logits=True
)
```

This allows the attack to:
- Interrogate model internals
- Extract feature representations
- Generate optimal perturbations

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 78-80

### Layer Selection
Feature collision attacks require identifying the target layer:
- **For CNNs:** Dense layers (not convolutional, batch normalization, dropout, or flatten layers)
- May require experimentation to find optimal layer
- Typically later layers that capture high-level features

## Attack Scenarios

### Bank Cheque Fraud
Poison digit recognition model to misclassify all digits to 9, inflating cheque amounts.

### Product Recommendations
Poison recommendation system to favor specific products by aligning features with popular items.

### Sentiment Analysis
Manipulate review classification to favor specific brands/products.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 79-80

## Detection Challenges

> "Combining efficiency and avoiding detection are challenging problems for attackers to solve but also for defenders to prevent or detect."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 80

**Why detection is hard:**
- Labels are correct (pass validation)
- Perturbations are imperceptible
- Data appears statistically normal
- Requires model-aware defenses (not just data inspection)

## Hybrid Attacks

Clean-label techniques can combine with backdoors:

**Clean-label backdoor attack:**
- Uses seemingly benign data with correct labels (clean-label)
- Inserts backdoor trigger (backdoor attack)
- Maintains high accuracy while enabling trigger-based misclassification

> "The clean-label backdoor attack is both a targeted manipulation attack (because it involves inserting a backdoor into the model) and a clean-label attack (because it uses seemingly benign data with correct labels)."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 62

See [[techniques/backdoor-poisoning]] for trigger mechanics.

## Tooling

### Adversarial Robustness Toolbox (ART)

**Available attacks:**
- `FeatureCollisionAttack` — Feature alignment approach
- `PoisoningAttackCleanLabelBackdoor` — PGD-based trigger generation

**Benefits:**
- Simplifies complex implementation
- Provides automated perturbation generation
- Useful for testing model robustness

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 79-80

## Related

**Category:**
- [[techniques/data-poisoning-attacks]] — Parent attack class

**Can combine with:**
- [[techniques/backdoor-poisoning]] — Clean-label backdoors

**Mitigated by:**
- [[mitigations/mlops-security]] — Data lineage and versioning
- [[mitigations/adversarial-training]] — Include clean-label examples
- [[mitigations/activation-clustering]] — Detect abnormal activations
- [[mitigations/spectral-signature]] — Frequency domain analysis
- [[mitigations/roni-defense]] — Performance-based rejection

**ATLAS Mapping:**
- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020]] — Poison Training Data

**References:**
- Poison Frogs Paper: https://arxiv.org/abs/1804.00792
- ART Clean-Label Backdoor: https://github.com/Trusted-AI/adversarial-robustness-toolbox/blob/main/notebooks/poisoning_attack_clean_label_backdoor.ipynb
