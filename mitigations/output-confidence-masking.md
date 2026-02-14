---
title: "Output Confidence Masking"
tags:
  - type/mitigation
  - target/model-api
  - target/ml-model
  - target/llm
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Output Confidence Masking

## Summary

Output confidence masking reduces the precision of confidence scores and probability distributions returned by model APIs to limit information available for membership inference and model extraction attacks. By rounding confidence scores, returning only top-k predictions without full distributions, or adding calibrated noise to outputs, this mitigation degrades the signal attackers rely on to distinguish training set members from non-members. Membership inference attacks exploit the gap between high-confidence scores on memorized training data and lower-confidence scores on unseen data; reducing score precision narrows this exploitable gap.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Reduces precision of confidence scores that membership inference attacks exploit to determine training set membership |
| | [[techniques/membership-inference-attacks]] | Directly counters the primary signal (confidence score differential) used in threshold-based and shadow model membership inference |
| AML.T0049 | [[techniques/model-extraction]] | Less precise outputs reduce fidelity of extracted substitute models |
| AML.T0049 | [[techniques/model-extraction-llm]] | Restricting logits and confidence scores reduces information available for LLM extraction via API queries |
| | [[techniques/model-inversion-attacks]] | Coarser outputs provide less information for reconstructing training data |
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Reduces confidence score precision exploited by attribute inference and limits feedback signal for black-box model inversion |
| AML.T0056 | [[techniques/training-data-memorization]] | Round confidence scores, return top-k only, add calibrated noise to reduce signal exploited by membership and attribute inference attacks |

## Implementation

### Confidence Score Rounding

Round confidence scores to reduce precision:
- Return scores in 0.1 increments instead of arbitrary floating-point precision (e.g., 0.87342 → 0.9)
- Consider coarser rounding (0.2 increments) for high-sensitivity applications
- Apply rounding consistently to prevent information leakage through rounding patterns

### Top-k Prediction Limiting

- Return only the top-k most likely predictions (e.g., top-3) without full probability distributions
- Omit confidence scores entirely where feasible (return only class labels)
- For generative models, limit logprob access or disable it for public-facing APIs

### Calibrated Noise Addition

- Add small random noise to confidence outputs before returning to users
- Calibrate noise magnitude to preserve utility while degrading inference attacks
- Use differential privacy mechanisms (Laplace or Gaussian noise) for formal guarantees
- See also [[mitigations/output-noise-injection]] for broader output perturbation

### Dynamic Perturbation

- Increase noise levels for accounts flagged as exhibiting suspicious query patterns
- Implement progressive confidence degradation for high-volume queriers
- Coordinate with [[mitigations/anomaly-detection-architecture]] for trigger conditions

## Limitations & Trade-offs

- **Utility degradation**: Reduced confidence precision may impact downstream applications that depend on precise probability estimates
- **Incomplete protection**: Advanced shadow model attacks may still succeed with coarser confidence information given sufficient query volume
- **Application constraints**: Some legitimate use cases (medical diagnostics, financial risk scoring) require high-precision confidence scores
- **Noise calibration difficulty**: Too much noise destroys utility; too little provides insufficient protection

## Testing & Validation

- Conduct membership inference attacks before and after confidence masking to measure protection improvement
- Measure impact on legitimate application accuracy with reduced confidence precision
- Test shadow model attack success rates at various rounding granularities
- Validate that noise addition doesn't create systematic biases in outputs

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 139-141
