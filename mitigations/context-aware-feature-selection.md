---
title: "Context-Aware Feature Selection"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Context-Aware Feature Selection

## Summary

Context-aware feature selection is a privacy-preserving design practice that carefully vets input features to avoid including those highly correlated with sensitive attributes from other contexts. The core principle comes from **contextual integrity**: information appropriate in one context (e.g., health) should not be inferrable via features from another context (e.g., financial behavior). By auditing feature sets for cross-context correlations before training, organizations prevent models from implicitly learning and leaking sensitive attributes that were never intended to be part of the model's decision-making.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Prevents attribute inference by removing features correlated with sensitive attributes from other contexts |
| AML.T0056 | [[techniques/training-data-memorization]] | Vet input features to avoid cross-context correlations that enable attribute inference from memorized training data |

## Implementation

### Cross-Context Correlation Auditing

Before training, systematically identify features that may correlate with sensitive attributes outside the model's intended context:

1. **Enumerate sensitive attributes** from adjacent contexts (health, political, financial, demographic)
2. **Compute correlation metrics** between candidate features and sensitive attributes using proxy datasets or domain expertise
3. **Remove or transform** features with high cross-context correlation unless strictly necessary and mitigated
4. **Document rationale** for every feature retained that has any correlation with sensitive attributes

### Feature Vetting Process

- Maintain a feature registry with documented justification for each feature's inclusion
- Require privacy impact assessment for features derived from behavioral data (browsing, purchasing, social media activity)
- Use mutual information or other non-linear correlation measures—not just Pearson correlation—to detect subtle relationships
- Re-audit features periodically as data distributions shift

### Mitigation When Features Must Be Retained

When correlated features are necessary for model utility:

- Apply [[mitigations/regularization-techniques|regularization]] to reduce reliance on spurious correlations
- Combine with [[mitigations/differential-privacy|differential privacy]] for formal guarantees
- Use [[mitigations/output-confidence-masking|output perturbation]] to limit inference from model outputs

## Limitations & Trade-offs

- **Utility reduction**: Removing correlated features may reduce model accuracy if those features carry legitimate predictive signal
- **Incomplete coverage**: Correlation auditing may miss non-obvious or emergent correlations, especially in high-dimensional feature spaces
- **Ongoing maintenance**: Feature correlations can change as data distributions evolve, requiring periodic re-auditing
- **Domain expertise required**: Identifying which contexts are sensitive and which correlations are problematic requires deep domain knowledge

## Testing & Validation

- Conduct attribute inference attacks against the trained model to verify that sensitive attributes cannot be inferred from model outputs
- Compare model behavior on profiles that differ only in features correlated with sensitive attributes—statistically significant output differences indicate residual leakage
- Use fairness metrics to detect proxy discrimination through retained features

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Context-Aware Feature Selection: Carefully vet input features; avoid features highly correlated with sensitive attributes from other contexts unless strictly necessary and mitigated; document rationale."
> — [[sources/bibliography#Red-Teaming AI]], p. 315-341
