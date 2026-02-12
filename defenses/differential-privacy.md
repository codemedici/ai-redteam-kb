---
title: "Differential Privacy for ML Models"
description: "Mathematical framework adding calibrated noise to training process to protect individual data privacy while maintaining model utility."
tags:
  - type/defense
  - target/training-pipeline
  - target/model-api
  - mitigates/model-inversion
  - mitigates/membership-inference
  - source/adversarial-ai
  - needs-review
---

# Differential Privacy for ML Models

## Summary

Differential privacy (DP) provides a mathematically rigorous framework for protecting individual privacy in ML training. By adding calibrated noise to gradients during training (DP-SGD) or to model outputs, DP ensures that the model's behavior changes only minimally whether any single individual's data is included or excluded from the training set. This makes [[attacks/membership-inference-attacks|membership inference]] and [[attacks/model-inversion-attacks|model inversion]] attacks significantly harder. The privacy guarantee is quantified by the parameter ε (epsilon): smaller ε values provide stronger privacy but may reduce model accuracy. DP is widely adopted in privacy-sensitive domains (healthcare, finance, government) and supported by frameworks like TensorFlow Privacy and Opacus (PyTorch).

> "Differential privacy aims to protect individual record confidentiality by ensuring the model's outputs don't significantly change whether a specific individual's data is in the training set." (p391, paraphrased)

## Implementation

### DP-SGD (Differentially Private Stochastic Gradient Descent)

**Concept**: During training, clip gradients per example and add Gaussian noise before aggregating. This prevents the model from memorizing specific training samples.

**Code Example** (TensorFlow Privacy):
```python
import tensorflow_privacy as tfp

# Differential privacy parameters
epsilon = 1.0  # Privacy budget (lower = more private)
delta = 1e-5   # Failure probability
noise_multiplier = 1.1  # Controls noise magnitude

# DP optimizer wraps standard optimizer
optimizer = tfp.DPKerasSGDOptimizer(
    l2_norm_clip=1.0,          # Gradient clipping threshold
    noise_multiplier=noise_multiplier,
    num_microbatches=batch_size,
    learning_rate=0.01
)

model.compile(optimizer=optimizer, loss='sparse_categorical_crossentropy')
model.fit(x_train, y_train, epochs=10, batch_size=250)

# Compute privacy spent
privacy_spent = tfp.compute_dp_sgd_privacy(
    n=len(x_train),
    batch_size=250,
    noise_multiplier=noise_multiplier,
    epochs=10,
    delta=delta
)
print(f"Achieved (ε, δ)-DP: ({privacy_spent}, {delta})")
```

### Privacy Budget Selection

- **ε < 1**: Strong privacy (e.g., healthcare, biometrics)
- **ε = 1-3**: Moderate privacy (general use cases)
- **ε > 5**: Weak privacy (limited protection)

## Mitigates

- **[[attacks/membership-inference-attacks]]**: Noise prevents model from overfitting to specific individuals
- **[[attacks/model-inversion-attacks]]**: Reconstructed data less accurate due to privacy noise
- **[[attacks/data-poisoning-attacks]]**: Limited—DP primarily protects privacy, not integrity

## Trade-Offs

**Advantages**:
- Mathematically provable privacy guarantees
- Quantifiable privacy loss (ε, δ parameters)
- Industry-standard defense (Apple, Google, US Census Bureau use DP)

**Limitations**:
- **Accuracy Loss**: Noise injection reduces model performance (5-15% accuracy drop typical)
- **Hyperparameter Tuning**: Requires careful selection of ε, clipping threshold, noise multiplier
- **Computational Cost**: DP-SGD is slower than standard SGD
- **Limited Scope**: Protects individual-level privacy but not against backdoors or model tampering

## Testing & Validation

**Audit Privacy Loss**:
```python
# Measure effective epsilon after training
from tensorflow_privacy.privacy.analysis import compute_dp_sgd_privacy

epsilon, _ = compute_dp_sgd_privacy(
    n=num_training_samples,
    batch_size=batch_size,
    noise_multiplier=noise_multiplier,
    epochs=epochs,
    delta=1e-5
)

assert epsilon < 3.0, "Privacy budget exceeded!"
```

**Membership Inference Test**:
- Attack trained model using membership inference
- DP models should show significantly lower attack success rate (~50% accuracy, near random guessing)

## Related

- **Attacks Mitigated**: [[attacks/membership-inference-attacks]], [[attacks/model-inversion-attacks]]
- **Complementary Defenses**: [[defenses/federated-learning]], [[defenses/secure-rl-algorithms]]
- **Frameworks**: TensorFlow Privacy, Opacus (PyTorch DP), Microsoft SmartNoise

---

*Source: Adversarial AI - Attacks, Mitigations, and Defense Strategies (Chapter 10: Privacy-Preserving ML, p233-261)*
