---
title: "Data Minimization"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/ml-model
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Data Minimization

## Summary

Data minimization is a privacy-by-design principle that limits the collection and retention of data to only what is strictly necessary for the intended purpose. By reducing the number of data fields collected and stored, organizations reduce the number of potential quasi-identifiers exposed by the system or in any released data, limiting the attack surface for linkage attacks, attribute inference, and other privacy attacks. Data minimization is also a core requirement under privacy regulations such as GDPR (Article 5(1)(c)) and is a foundational element of responsible AI data governance.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Reduces quasi-identifiers available for linkage attacks and limits features available for attribute inference |
| AML.T0056 | [[techniques/training-data-memorization]] | Include only data necessary for model functionality; reduce quasi-identifiers and sensitive correlations to limit memorization surface |

## Implementation

### Data Collection Policies

- Collect only data fields strictly necessary for the model's intended purpose
- Require documented justification for each data field collected
- Apply purpose limitation: data collected for one purpose should not be repurposed without review
- Implement data retention policies with automatic deletion of data no longer needed

### Feature Reduction for ML

- Audit model feature sets and remove features not contributing meaningfully to model performance
- Prefer aggregate or derived features over raw individual-level data where possible
- Remove or hash direct identifiers (names, SSNs, email addresses) before training
- Reduce granularity of quasi-identifiers (exact timestamps → date only, full addresses → region)

### Release and API Design

- API responses should return only the minimum information needed by the consumer
- Avoid exposing metadata, timestamps, or auxiliary fields that could serve as quasi-identifiers
- Apply [[mitigations/output-coarsening|output coarsening]] to reduce precision of released data

## Limitations & Trade-offs

- **Utility reduction**: Removing data fields may reduce model accuracy or limit future use cases
- **Incomplete protection**: Even minimal datasets may contain sufficient quasi-identifiers for linkage if combined with rich external data
- **Organizational resistance**: Business units often want to collect "everything" for potential future use
- **Retroactive limitation**: Cannot minimize data already collected and distributed

## Testing & Validation

- Audit data pipelines to verify only necessary fields are collected and retained
- Attempt linkage attacks on minimized datasets to verify reduced attack surface
- Review API responses for unnecessary data exposure
- Verify data retention policies are enforced (automated deletion works)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Practice Data Minimization: Collect and retain only essential data fields; reduce number of potential quasi-identifiers exposed by system or in any released data."
> — [[sources/bibliography#Red-Teaming AI]], p. 315-341
