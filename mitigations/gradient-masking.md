---
title: "Gradient Masking"
tags:
  - type/mitigation
  - target/ml-model
  - target/cv
  - source/generative-ai-security
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Gradient Masking

## Summary

Gradient masking obscures or hides model gradients to limit the information available for crafting adversarial examples. Since many adversarial attacks (FGSM, PGD, C&W) rely on computing or estimating model gradients, making gradients inaccessible or uninformative can increase the difficulty of gradient-based attacks. However, gradient masking is widely considered to provide **obfuscated gradients** that give a false sense of security — adaptive attackers can often circumvent it.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0043 | [[techniques/adversarial-examples-evasion-attacks]] | Obscures model gradients to make gradient-based attack methods more difficult |

## Implementation

### Approaches

- **Non-differentiable components:** Insert non-differentiable operations in the model pipeline that break gradient flow
- **Defensive distillation:** Train a second model on soft labels from original model, smoothing decision boundaries
- **Input randomization:** Add stochastic layers that make gradient computation noisy and unreliable
- **Gradient regularization:** Penalize large gradient magnitudes during training to create smoother decision surfaces

## Limitations & Trade-offs

**Critical warning:** Gradient masking has been shown to offer a **false sense of security** in many cases.

**Bypass techniques:**
- **Backward Pass Differentiable Approximation (BPDA):** Replaces non-differentiable components with differentiable approximations during attack
- **Transfer attacks from surrogate models:** Train a substitute model that approximates the masked model's behavior, then use standard gradient attacks on the surrogate
- **Gradient-free optimization methods:** Zeroth-order optimization, evolutionary strategies, and other black-box methods that don't need gradients at all

**Athalye et al. (2018)** demonstrated that defenses based on obfuscated gradients are reliably bypassed — 6 of 9 tested defenses (many using gradient masking variants) were completely broken by adaptive attacks.

**Accuracy impact:** Some gradient masking techniques (e.g., defensive distillation) can reduce model accuracy on clean data.

## Testing & Validation

1. Test against standard gradient-based attacks (FGSM, PGD) — should show reduced attack success
2. **Critical:** Test against BPDA and other adaptive attacks specifically designed to bypass gradient masking
3. Test against transfer attacks from surrogate models
4. Test against gradient-free black-box attacks (ZOO, boundary attack)
5. If defense only works against standard attacks but fails against adaptive ones, it provides false security

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|

## Sources

> "Adversarial attacks often rely on accessing the model's gradients to craft their malicious inputs. By obscuring or masking these gradients, the model can limit the information available for crafting adversarial samples, making it more challenging for attackers to deceive the model."
> — [[sources/bibliography#Generative AI Security]], p. 167

> "Athalye et al. (2018) found 7 of 9 recently published defenses 'broke' once adaptive attacks were devised (6 completely, 1 partially). This highlights how many defenses offer a false sense of security; effective protection requires anticipating attacker adaptation."
> — [[sources/bibliography#Red-Teaming AI]], p. 164-165
