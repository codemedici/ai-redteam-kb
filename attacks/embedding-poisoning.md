---
description: "Adversary manipulates embeddings at indexing or query time to control retrieval results."
tags:
  - atlas/aml.t0020
  - owasp/llm03
  - trust-boundary/data-knowledge
  - type/attack
  - target/rag-system
  - access/gray-box
  - severity/high
---
# Embedding Poisoning

## Summary

**TODO:** Complete summary - describe how attackers poison vector embeddings.

## Threat Scenario

**TODO:** Describe concrete embedding poisoning scenario.

## Preconditions

- **TODO:** List required conditions

## Abuse Path

1. **TODO:** Step-by-step attack sequence

## Impact

**Business Impact:**
- **TODO:** Describe business consequences

**Technical Impact:**
- **TODO:** Describe technical consequences

**Severity:** **TODO** (Critical / High / Medium / Low)

## Detection Signals

- **TODO:** Observable indicators

## Testing Approach

**Manual Testing:**
- **TODO:** Testing steps

**Automated Testing:**
- **TODO:** Tool recommendations

## Evidence to Capture

- [ ] **TODO:** Evidence checklist

## Mitigations

**Preventive Controls:**
- **TODO:** Preventive measures

**Detective Controls:**
- **TODO:** Detection measures

**Responsive Controls:**
- **TODO:** Response measures

## Engagement Applicability

- [ ] **TODO:** List applicable engagement types

## Framework References

**MITRE ATLAS:**
- **TODO:** Identify applicable ATLAS techniques

**OWASP LLM Top 10:**
- **TODO:** Map to OWASP LLM issues

**NIST AI RMF:**
- **TODO:** Map to NIST AI RMF

**NIST GenAI Profile:**
- **TODO:** Map to GenAI Profile

## Related

- **Mitigated by**: [[defenses/embedding-integrity-verification]], [[defenses/source-validation-and-trust-scoring]], [[defenses/data-quarantine-procedures]], [[defenses/input-validation-transformation]]
- **Enables**: [[attacks/retrieval-manipulation]], [[attacks/rag-data-poisoning]]
- **ATLAS**: [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020]]
