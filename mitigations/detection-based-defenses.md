---
title: "Detection-Based Defenses"
tags:
  - type/mitigation
  - target/ml-model
  - target/cv
  - target/llm
  - source/red-teaming-ai
maturity: draft
created: 2026-02-12
updated: 2026-02-14
---

# Detection-Based Defenses

## Summary

Detection-based defenses deploy separate mechanisms to identify whether an input is adversarial before it reaches the model or before the model's output is trusted. Rather than hardening the model itself, these defenses act as a filtering layer that can reject or flag suspicious inputs. Techniques include auxiliary classifiers trained to distinguish clean from adversarial inputs, statistical tests that check whether inputs lie on the normal data manifold, internal activation analysis for anomalous neuron firing patterns, and perturbation sensitivity checks that probe whether small random changes cause disproportionately large output shifts.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0043 | [[techniques/adversarial-examples-evasion-attacks]] | Detects adversarial perturbations through auxiliary classifiers, statistical tests, and activation analysis |
| AML.T0088 | [[techniques/gan-weaponization]] | Defense-GAN reconstructs inputs through trained GAN to remove adversarial perturbations before classification |

## Implementation

### Auxiliary Classifier

Train a separate model to distinguish clean vs. adversarial inputs based on features, activations, or statistical properties of the input.

**Approach:** Build a binary classifier that takes raw input features (or internal model activations) and predicts whether the input has been adversarially perturbed.

### Statistical Tests

Check if an input lies off the normal data manifold by detecting anomalous feature distributions.

**Techniques:**
- Measure input-output sensitivity (does small random perturbation cause disproportionately large output change?)
- Compare feature distributions against known clean data statistics
- Detect anomalous activation patterns in intermediate layers

### Activation Analysis

Analyze internal model activations for anomalies. Adversarial inputs often activate neurons differently than clean inputs — patterns in hidden layer activations can signal adversarial manipulation.

### Perturbation Sensitivity Testing

Check if small random perturbations cause disproportionately large output changes — a sign of brittleness indicating the input may be adversarial.

```python
def perturbation_sensitivity_check(model, input, num_samples=50, noise_std=0.01, threshold=0.1):
    """Flag inputs where small noise causes large prediction shifts."""
    original_pred = model(input)
    shift_scores = []
    
    for _ in range(num_samples):
        noisy_input = input + torch.randn_like(input) * noise_std
        noisy_pred = model(noisy_input)
        shift_scores.append(torch.norm(noisy_pred - original_pred).item())
    
    avg_shift = sum(shift_scores) / len(shift_scores)
    return avg_shift > threshold  # True = likely adversarial
```

### Feature Squeezing (Detection Mode)

Compare predictions on original vs. squeezed (compressed/smoothed) input — a large prediction difference suggests the input may be adversarial.

## Limitations & Trade-offs

- **Adaptive attackers craft examples that evade detectors too** — the detector itself becomes an attack surface. Attackers can optimize adversarial examples to simultaneously fool the main classifier AND evade the detector.
- **Some detectors perform no better than chance against adaptive attacks** — many published detectors "break" when faced with attacks specifically designed to bypass them.
- **High false positive/negative rates** — setting detection thresholds involves trading off between catching adversarial inputs and incorrectly flagging legitimate ones.
- **Computational overhead** — running auxiliary classifiers or statistical tests adds inference latency.

**Obfuscated gradients warning:** Athalye et al. (2018) examined 9 recently published defenses and developed adaptive attacks targeting each. 6 defenses were broken completely, 1 partially — only 2 withstood adaptive attacks. Many detection-based defenses offer a false sense of security.

### Defense-GAN

**Concept:** Train GAN on unperturbed images to learn underlying data distribution. At inference, reconstruct input images through the GAN to remove adversarial perturbations before classification.

**Implementation:**
```bash
# Train Defense-GAN
python train.py --cfg output/gans/mnist

# Test against black-box attacks
python blackbox.py \
  --cfg output/gans/mnist \
  --results_dir defensegan \
  --bb_model targetModel \
  --sub_model substitute_model \
  --fgsm_eps 0.3 \
  --defense_type defense_gan
```

**Datasets tested:** MNIST, Fashion MNIST, CelebA

**Alternative monitoring use:** Detect adversarial attacks by measuring reconstruction error—large differences between input and GAN-reconstructed output indicate potential adversarial manipulation.

**Research:** *Defense-GAN: Protecting Classifiers Against Adversarial Attacks Using Generative Models* (2018) — https://openreview.net/pdf?id=BkJ3ibb0-

**Alternative:** MagNet (uses auto-encoders, less robust than Defense-GAN)

**Limitation:** Experimental; requires code changes for custom datasets/classifiers. Adaptive attacks can target the GAN reconstruction process.

> Source: [[sources/bibliography#Adversarial AI]], p. 319-320

## Testing & Validation

1. Test detector against standard adversarial examples (FGSM, PGD, C&W) — measure detection rate
2. **Critical:** Test against adaptive attacks where the attacker knows about the detector and optimizes to evade it
3. Measure false positive rate on clean data
4. Evaluate across multiple attack types and perturbation budgets
5. Monitor detector performance in production over time

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|

## Sources

> "Deploy separate mechanism to detect if input is adversarial. Techniques include auxiliary classifiers, statistical tests, activation analysis, and perturbation sensitivity checks. Successful detection guards model without altering predictions on clean data. However, adaptive attackers craft examples that evade detectors too — some detectors perform no better than chance against adaptive attacks."
> — [[sources/bibliography#Red-Teaming AI]], p. 163-164

> "Athalye et al. (2018) found 7 of 9 recently published defenses 'broke' once adaptive attacks were devised (6 completely, 1 partially). This highlights how many defenses offer a false sense of security; effective protection requires anticipating attacker adaptation."
> — [[sources/bibliography#Red-Teaming AI]], p. 164-165
