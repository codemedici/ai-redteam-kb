---
title: "RONI Defense (Reject On Negative Impact)"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/ml-model
  - source/red-teaming-ai
maturity: draft
created: 2026-02-12
updated: 2026-02-14
---

# RONI Defense (Reject On Negative Impact)

## Summary

RONI (Reject On Negative Impact) is a data-level defense against training data poisoning that evaluates each candidate training sample by measuring its impact on model performance. Samples that would degrade model accuracy on a trusted validation set are rejected before inclusion in the training dataset. This approach treats each data point as potentially adversarial and accepts only those that maintain or improve model quality, providing a direct defense against both availability and integrity poisoning.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Rejects poisoned samples that would degrade model performance, blocking availability and some integrity attacks |
| | [[techniques/backdoor-poisoning]] | Can detect backdoor samples if their inclusion measurably degrades performance on clean validation data |

## Implementation

### Core Algorithm

1. **Maintain a trusted validation set** — Small, curated, known-clean dataset representative of expected data distribution
2. **Train baseline model** — Train model on current accepted training data, measure performance on validation set
3. **For each candidate sample:**
   a. Temporarily add sample to training data
   b. Retrain (or incrementally update) the model
   c. Evaluate performance on validation set
   d. If performance drops below threshold → reject sample
   e. If performance maintained or improved → accept sample

### Practical Considerations

**Efficiency:** Full retraining per sample is prohibitively expensive for large models. Practical implementations use:
- **Influence functions** — Estimate impact of adding a sample without retraining
- **Incremental learning** — Update model weights incrementally rather than full retrain
- **Batch RONI** — Evaluate batches of samples rather than individuals

**Validation set security:** The validation set itself must be protected from poisoning—if the attacker compromises the validation set, RONI becomes ineffective.

## Limitations & Trade-offs

**Limitations:**
- **Clean-label attacks:** Samples with correct labels may not degrade aggregate performance metrics, passing RONI checks while still poisoning the model
- **Computational cost:** Evaluating impact per-sample is expensive, especially for large models and datasets
- **Subtle integrity attacks:** Targeted backdoor attacks that don't affect overall accuracy on the validation set may evade detection
- **Validation set dependency:** Effectiveness depends entirely on the quality and representativeness of the validation set

**Trade-offs:**
- **Thoroughness vs. Speed:** Per-sample evaluation is thorough but slow; batch evaluation is faster but less precise
- **Sensitivity vs. False Rejection:** Strict thresholds catch more poison but reject legitimate edge-case data

## Testing & Validation

1. **Poisoning simulation:** Inject known poisoned samples, verify RONI rejects them
2. **False rejection rate:** Measure how many clean samples are incorrectly rejected
3. **Clean-label evasion:** Test whether clean-label attacks bypass RONI

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Defending against data poisoning is tough due to the variety of attack techniques and the difficulty in distinguishing malicious data from natural outliers or noise. A layered defense-in-depth strategy is usually needed."
> — [[sources/bibliography#Red-Teaming AI]], p. 130
