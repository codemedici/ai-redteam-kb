---
title: Adversarial Training
description: Defense technique that explicitly includes adversarial examples in training data to make models robust against adversarial attacks
tags:
  - type/defense
  - target/model-robustness
  - atlas/AML.M0001
  - source/adversarial-ai
  - needs-review
---

# Adversarial Training

## Overview

Adversarial training is a **mitigation technique** that makes ML models robust against adversarial attacks by explicitly including adversarial examples (poisoned or perturbed data) in the training set **with correct labels**. Rather than detecting attacks, adversarial training teaches the model to handle adversarial inputs correctly.

> "This defense is more of a mitigation than detection. It explicitly includes poisoned data but with the correct classification. Adversarial training makes models robust against adversarial attacks."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 85

## How It Works

1. **Generate adversarial examples** — Create poisoned/perturbed samples using attack techniques
2. **Correct labels** — Assign proper labels to adversarial samples (not attacker's target labels)
3. **Augment training data** — Add adversarial examples to original training set
4. **Train model** — Model learns to classify both clean and adversarial inputs correctly

**Result:** Model becomes resistant to similar adversarial perturbations encountered during inference.

## Primary Use Cases

### Evasion Attack Mitigation
Adversarial training is **most effective** against inference-time attacks (evasion):
- Adversarial examples (FGSM, PGD, C&W)
- Perturbations designed to fool deployed models

### Poisoning Attack Mitigation
Can also defend against training-time poisoning:
- Include backdoored samples with correct labels
- Model learns that trigger patterns don't indicate target class

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 85

## Implementation Approaches

### Basic Adversarial Training

```python
# Generate adversarial examples
attack = FGSM(model, eps=0.1)
x_adv = attack.generate(x_train)

# Augment training data
x_train_augmented = np.concatenate([x_train, x_adv])
y_train_augmented = np.concatenate([y_train, y_train])  # Correct labels

# Retrain model
model.fit(x_train_augmented, y_train_augmented)
```

### PGD-Based Adversarial Training (Madry Defense)

**Tool:** ART `AdversarialTrainerMadryPGD`

```python
from art.defences.trainer import AdversarialTrainerMadryPGD
from art.estimators.classification import KerasClassifier

# Wrap model
classifier = KerasClassifier(model)

# Configure adversarial trainer
trainer = AdversarialTrainerMadryPGD(
    classifier,
    nb_epochs=10,
    eps=0.15,        # Maximum perturbation
    eps_step=0.001   # Step size for PGD
)

# Train with adversarial examples
trainer.fit(x_train, y_train)
```

**PGD (Projected Gradient Descent):** Iterative attack method that generates strong adversarial examples—training against PGD improves robustness against multiple attack types.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 80

## Advantages

- **Proactive defense** — Hardens model before deployment
- **Broad effectiveness** — Defends against multiple attack variants
- **No runtime overhead** — Defense is embedded in model weights
- **Improves generalization** — Can improve performance on edge cases

## Limitations

### Computational Cost
- **Training time** — Generating adversarial examples during training is expensive
- **Data augmentation overhead** — Doubles or triples training data size

### Attack Coverage
- **Limited to known attacks** — Only defends against attack types used in training
- **New attack variants** — May not generalize to novel adversarial techniques
- **Attack diversity needed** — Must train against representative attack suite

### Performance Trade-offs
- **Accuracy degradation** — May slightly reduce accuracy on clean data
- **Overfitting risk** — Model may overfit to specific adversarial patterns

## Integration with MLOps

Adversarial training should be integrated into ML pipelines:

1. **Automated generation** — Include adversarial example generation in data pipeline
2. **Versioning** — Track adversarial training data separately
3. **Continuous testing** — Validate robustness against new attack techniques
4. **Monitoring** — Track model performance on both clean and adversarial data

See [[defenses/mlops-security]] for integration patterns.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 85-86

## Part of Larger Defense Strategy

> "Relate defending against data poisoning attacks to adversarial training and the bigger picture of adversarial AI defenses."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 60

Adversarial training is **one component** of defense-in-depth:

**Complementary defenses:**
- [[defenses/anomaly-detection]] — Detect poisoned training data
- [[defenses/mlops-security]] — Data lineage and versioning
- [[defenses/input-validation]] — Runtime input sanitization
- [[defenses/model-ensembles]] — Multiple models for robustness

## Research and Tooling

### ART (Adversarial Robustness Toolbox)
- `AdversarialTrainerMadryPGD` — PGD-based training
- `AdversarialTrainerFBF` — Feature-based training
- Integration with multiple attack types

### Techniques
- **Madry et al. defense** — PGD adversarial training (state-of-the-art)
- **TRADES** — Trade-off between accuracy and robustness
- **MART** — Misclassification-aware adversarial training

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 85

## Related

**Defends against:**
- [[attacks/data-poisoning]] — Training-time manipulation
- [[attacks/backdoor-poisoning]] — Trigger-based attacks
- [[attacks/adversarial-examples]] — Inference-time perturbations
- [[attacks/evasion]] — General evasion techniques

**Complements:**
- [[defenses/input-preprocessing]] — Runtime sanitization
- [[defenses/model-hardening]] — Architectural improvements
- [[defenses/detection-based-defenses]] — Anomaly detection

**ATLAS Mapping:**
- [[frameworks/atlas/AML.M0001]] — Adversarial Training (Mitigation)
