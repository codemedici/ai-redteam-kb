---
title: "Certified Defenses"
tags:
  - type/mitigation
  - target/ml-model
  - target/cv
  - source/red-teaming-ai
maturity: draft
created: 2026-02-12
updated: 2026-02-14
---

# Certified Defenses

## Summary

Certified defenses (robust verification) modify models or training procedures to yield **provable robustness guarantees** within certain perturbation bounds. Unlike empirical defenses that may be bypassed by adaptive attacks, certified defenses provide mathematical proof that an attacker cannot succeed with perturbations below a specified threshold. This is the strongest form of defense when applicable, but comes with significant computational cost and accuracy trade-offs.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0043 | [[techniques/adversarial-examples-evasion-attacks]] | Provides provable robustness guarantees against bounded adversarial perturbations |

## Implementation

### Randomized Smoothing

Add random noise to input and average predictions over many noisy samples. Provides a certified radius within which the prediction is guaranteed correct with high probability.

**Mechanism:**
1. For a given input, generate N noisy copies by adding Gaussian noise
2. Classify each noisy copy
3. Take majority vote
4. Mathematical analysis provides certified radius — any perturbation within this radius cannot change the prediction

**Example bounds:**
```
Randomized Smoothing Guarantee:
  Model correctly classifies all inputs within L2 ball of radius r
  Probability: ≥ 0.99
  
Trade-offs:
  Standard accuracy drops from 95% to 75%
  Inference time increases 100x (needs multiple samples)
```

### Interval Bound Propagation

Track bounds on neuron activations and propagate through network layers. Verifies robustness for all inputs in a region around the clean input.

### Semidefinite Programming

Convex relaxation of the verification problem that provides provable bounds on the model's behavior within a perturbation region.

## Limitations & Trade-offs

- **Computationally very expensive** — both during training and inference (randomized smoothing requires hundreds of forward passes per prediction)
- **Significant standard accuracy reduction** — 10-20% accuracy drops on clean data are common
- **Guarantees hold only for specific threat models** — typically bounded Lp-norm perturbations; does not cover semantic attacks or other perturbation types
- **Typically only for small perturbations** — certified radius is often small (small ε), may not cover realistic attack budgets
- **Scalability challenges** — scaling to large, complex models (modern deep networks) remains a major hurdle
- **Assumption-dependent** — if threat model assumptions are violated (e.g., attacker uses different norm), guarantees don't hold

**Key trade-off:** Carefully evaluate whether the provable guarantee justifies the potential performance hit for your specific application.

## Testing & Validation

1. Verify certified accuracy at target perturbation budget (ε)
2. Compare standard accuracy vs. certified accuracy to quantify trade-off
3. Test at multiple perturbation radii to understand robustness profile
4. Validate that guarantees hold under the specified threat model
5. Benchmark inference latency impact

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|

## Sources

> "Certified defenses modify model/training to yield provable robustness guarantees within certain perturbation size. Techniques include randomized smoothing, interval bound propagation, and semidefinite programming. Offers formal guarantees — strongest defense form when applicable — but computationally very expensive, often significantly reduces standard accuracy, and guarantees hold only for specific threat models."
> — [[sources/bibliography#Red-Teaming AI]], p. 164-165
