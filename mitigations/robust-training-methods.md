---
title: "Robust Training Methods"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/ml-model
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Robust Training Methods

## Summary

Robust training methods employ statistical techniques and training procedures that reduce the influence of outlier or poisoned data points on model learning. Unlike [[mitigations/data-validation-pipelines|data validation]] (which filters bad data before training) or [[mitigations/adversarial-training|adversarial training]] (which augments training with adversarial examples), robust training methods modify the training algorithm itself to be inherently resistant to corrupted samples. Key approaches include robust loss functions (Huber loss), median-based aggregation, trimmed statistics, and data augmentation for perturbation resilience.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Reduces impact of poisoned samples by limiting influence of outliers during training |
| | [[techniques/backdoor-poisoning]] | Robust statistics and augmentation can weaken backdoor signal from poisoned trigger samples |
| | [[techniques/clean-label-attacks]] | Augmentation and robust loss functions reduce sensitivity to subtle feature perturbations |

## Implementation

### Robust Loss Functions

Standard loss functions (MSE, cross-entropy) treat all samples equally, making them sensitive to outliers. Robust alternatives down-weight extreme values.

**Huber Loss:**
Combines Mean Squared Error (for small errors) and Mean Absolute Error (for large errors). This prevents large-error outliers (potentially poisoned samples) from dominating gradient updates.

```python
import torch.nn as nn

# PyTorch built-in Huber loss
criterion = nn.HuberLoss(delta=1.0)
# delta controls transition point between MSE and MAE behavior
# Smaller delta = more robust to outliers but less sensitive to signal
```

**When to use:** Regression tasks where poisoned samples may introduce extreme target values. Also applicable to classification loss reformulations.

### Median-Based Calculations

Replace mean-based aggregation with median to reduce influence of extreme values.

**Applications:**
- **Gradient aggregation:** Use median of per-sample gradients instead of mean (protects against gradient poisoning)
- **Feature normalization:** Compute normalization statistics using median and median absolute deviation (MAD) instead of mean and standard deviation
- **Batch statistics:** Use coordinate-wise median for batch normalization in adversarial settings

```python
import numpy as np

def robust_z_score(data):
    """Z-score using median absolute deviation (robust to outliers)"""
    median = np.median(data, axis=0)
    mad = np.median(np.abs(data - median), axis=0)
    # Scale MAD to match std for normal distributions
    robust_std = 1.4826 * mad
    return (data - median) / np.maximum(robust_std, 1e-8)
```

### Trimmed Statistics

Remove extreme values before computing statistics or aggregating gradients.

**Trimmed Mean:** Discard the top and bottom k% of values, compute mean of remainder. Bounds the influence any poisoned subset can have.

```python
from scipy import stats

# Trim 10% from each tail before computing mean
trimmed_mean = stats.trim_mean(sample_losses, proportiontocut=0.1)
```

**Use in federated learning:** Trimmed Mean aggregation discards the most extreme client updates before averaging, defending against malicious participants. See [[mitigations/federated-learning]] for FL-specific robust aggregation.

### Data Augmentation for Robustness

Augmenting training data with noise or transformations improves resilience to small malicious perturbations in poisoned samples.

**Techniques:**
- **Image:** Rotation, scaling, color jitter, random cropping, Gaussian noise injection
- **Text:** Synonym replacement, paraphrasing, random token insertion/deletion
- **Tabular:** Feature noise injection, random feature masking

**Why it helps against poisoning:** Augmentation forces the model to learn invariant features rather than memorizing specific (potentially poisoned) patterns. Subtle trigger patterns become less effective when the model encounters many natural variations of each sample.

### Combining with Regularization

Robust training methods work best when combined with [[mitigations/regularization-techniques|regularization]] (L1/L2 weight penalties, dropout) and [[mitigations/differential-privacy|differential privacy]] (DP-SGD). Together they form a defense stack:

1. **Robust loss functions** — Limit influence of individual poisoned samples on loss
2. **Regularization** — Prevent model from memorizing spurious correlations planted by attacker
3. **Differential privacy** — Mathematically bound influence any single data point can have on model parameters

## Limitations & Trade-offs

**Limitations:**
- **Reduced accuracy:** Robust methods intentionally down-weight some data—if the "outliers" are actually informative edge cases, model performance degrades
- **Not effective against clean-label attacks alone:** Clean-label attacks use correctly labeled, subtly perturbed data that may not appear as statistical outliers
- **Hyperparameter sensitivity:** Trimming fraction, Huber delta, and MAD thresholds require tuning per dataset

**Trade-offs:**
- **Robustness vs. Sensitivity:** More aggressive robustness (higher trim, smaller delta) sacrifices learning from legitimate edge cases
- **Computational overhead:** Some methods (per-sample gradient computation for median aggregation) are more expensive than standard training

## Testing & Validation

1. **Poisoning simulation:** Train models with known poisoned samples, compare standard vs. robust training accuracy and attack success rate
2. **Clean performance baseline:** Verify robust training doesn't significantly degrade performance on clean data
3. **Hyperparameter sweep:** Test different trimming fractions, Huber deltas to find optimal robustness/accuracy balance

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Robust Statistics: Training algorithms/loss functions less sensitive to outliers. Huber loss combines MSE and MAE, robust to outliers. Median-based calculations instead of mean to reduce extreme value influence. Trimmed statistics: Remove extreme values before aggregation."
> — [[sources/bibliography#Red-Teaming AI]], p. 131

> "Data Augmentation: Augment training data with noise or transformations. Can improve robustness against small perturbations. Makes model less sensitive to minor malicious changes."
> — [[sources/bibliography#Red-Teaming AI]], p. 131

> "L1/L2 regularization penalizes large model weights. Can mitigate poisoned samples trying to create strong spurious correlations. Limits influence of individual samples on model parameters."
> — [[sources/bibliography#Red-Teaming AI]], p. 131
