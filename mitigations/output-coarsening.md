---
title: "Output Coarsening and Generalization"
tags:
  - type/mitigation
  - target/model-api
  - target/ml-model
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Output Coarsening and Generalization

## Summary

Output coarsening reduces the precision of information released by AI systems to limit the power of quasi-identifiers for linkage attacks and reduce feedback available for model inversion and attribute inference. By generalizing outputs—replacing exact timestamps with time ranges, fine-grained locations with regions, full dates of birth with age ranges—organizations reduce the specificity of information that adversaries can use to re-identify individuals or reconstruct training data. This complements [[mitigations/output-confidence-masking|output confidence masking]] (which focuses on prediction scores) by addressing the broader category of data precision in all system outputs.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Reduces quasi-identifier power for linkage attacks and limits feedback for model inversion by coarsening all outputs |
| AML.T0056 | [[techniques/training-data-memorization]] | Reduce output granularity for high-risk deployments; switch from full probabilities to top-k to limit privacy budget exposure |

## Implementation

### Generalization Strategies

| Original Output | Coarsened Output | Rationale |
|-----------------|-----------------|-----------|
| Exact timestamp (2026-02-14 14:23:07) | Time range (2026-02-14 afternoon) | Reduces temporal quasi-identifier precision |
| Fine-grained location (51.5074° N, 0.1278° W) | Region (Central London) | Prevents location-based re-identification |
| Full date of birth (1990-03-15) | Age range (30-35) | Limits demographic quasi-identifiers |
| Exact confidence score (0.87342) | Binned score (0.85-0.90) | Reduces model inversion feedback |
| Full probability vector | Top-1 prediction only | Limits information for extraction/inversion |

### Application Contexts

- **Aggregate statistics releases**: Avoid overly granular breakdowns that allow matching small groups
- **API responses**: Return only the precision needed by the consumer
- **Synthetic data releases**: Ensure generated data does not reproduce precise quasi-identifier combinations from training data
- **Model outputs**: Combine with [[mitigations/output-confidence-masking|confidence masking]] for prediction scores

### Implementation Guidelines

- Define maximum precision levels for each output field based on privacy risk assessment
- Apply coarsening at the API layer (not in the model) so the model retains full precision internally
- Document coarsening policies and make them auditable
- Review coarsening levels periodically as new external data sources emerge that could enable linkage

## Limitations & Trade-offs

- **Utility degradation**: Reduced precision may impact downstream applications requiring exact values
- **Insufficient alone**: Coarsening reduces but does not eliminate linkage risk—must be combined with other defenses
- **Application-specific**: Some use cases (medical diagnosis, financial trading) require high precision that conflicts with coarsening
- **Granularity balance**: Too coarse and the data is useless; too fine and privacy protection is insufficient

## Testing & Validation

- Attempt linkage attacks on coarsened outputs using realistic external datasets
- Measure the reduction in unique quasi-identifier combinations after coarsening
- Validate that coarsened outputs still meet application utility requirements
- Test model inversion attacks with coarsened vs. full-precision outputs to measure protection improvement

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Output Coarsening/Generalization: Avoid releasing overly precise information: Exact timestamps → time ranges, Fine-grained locations → regions, Full dates of birth → age ranges. Reduces power of quasi-identifiers."
> — [[sources/bibliography#Red-Teaming AI]], p. 315-341
