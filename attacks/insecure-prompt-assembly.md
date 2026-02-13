---
description: "Application constructs prompts insecurely, allowing injection or context manipulation."
tags:
  - owasp/llm01
  - trust-boundary/agent-runtime
  - type/attack
  - target/llm-app
  - target/rag-system
  - target/agent
  - access/black-box
  - access/gray-box
  - severity/high
  - atlas/aml.t0051
---
# Insecure Prompt Assembly

## Summary

**TODO:** Complete summary - describe how unsafe prompt construction enables attacks.

## Threat Scenario

**TODO:** Describe concrete insecure prompt assembly scenario.

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
- AML.T0051: LLM Prompt Injection (related)

**OWASP LLM Top 10:**
- LLM01: Prompt Injection

**NIST AI RMF:**
- **TODO:** Map to NIST AI RMF

**NIST GenAI Profile:**
- **TODO:** Map to GenAI Profile

## Related

- **Mitigated by**: [[defenses/input-validation-patterns]], [[defenses/instruction-hierarchy-architecture]], [[defenses/output-filtering-and-sanitization]]
- **Enables**: [[attacks/prompt-injection]]
- **ATLAS**: AML.T0051
