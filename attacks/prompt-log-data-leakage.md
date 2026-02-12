---
description: "Sensitive data in prompts or conversation history is stored insecurely or leaked."
tags:
  - trust-boundary/data-knowledge
  - trust-boundary/model
  - type/attack
  - target/llm-app
  - target/agent
  - access/gray-box
  - severity/high
---
# Prompt and Conversation Log Data Leakage

## Summary

**TODO:** Complete summary - describe how prompt logs expose sensitive information.

## Threat Scenario

Data leakage occurs when an LLM inadvertently reveals sensitive information through its outputs, either due to the model memorizing parts of its training data or improperly handling input data during inference. Three primary attack vectors enable this exposure:

**Training Data Extraction**: Malicious actors query the model repeatedly to reconstruct parts of its training data, potentially exposing copyrighted material, personal information, or sensitive data used in training. For example, an LLM trained on a company's internal documents might start revealing confidential business strategies in its responses, leading to catastrophic consequences ranging from legal issues to significant competitive disadvantages.

**Membership Inference**: Attacks that aim to determine whether a specific data point was used in the model's training set. While this might seem innocuous, it can lead to serious privacy violations, especially in sensitive domains such as healthcare. If an attacker can infer that a particular medical record was used to train a health-related LLM, they might deduce sensitive information about an individual's medical history.

**Model Inversion**: Adversaries attempt to reconstruct input features from model outputs. For example, an attacker might infer demographic information about users based on their interactions with the model. This could lead to privacy breaches and potentially enable targeted attacks or discrimination based on the inferred information.

## Preconditions

- **TODO:** List required conditions

## Abuse Path

**Generic Pattern:**
1. Attacker identifies target LLM with access to sensitive training data or knowledge bases
2. Attacker queries the model repeatedly with carefully crafted inputs designed to elicit specific information
3. Responses are analyzed to reconstruct training data, infer membership, or reverse-engineer input features
4. Extracted information is aggregated across multiple queries to build comprehensive picture of sensitive data

**Training Data Extraction Variant:**
- Systematic querying attempts to elicit verbatim or near-verbatim reproduction of training examples
- Attacker identifies patterns in responses that suggest memorization of specific documents
- Gradual reconstruction of copyrighted content, internal documents, or personal information

**Membership Inference Variant:**
- Attacker probes model with known and unknown data points
- Statistical analysis of response patterns reveals whether specific examples were in training set
- Particularly effective in healthcare/financial domains where training set membership itself is sensitive

**Model Inversion Variant:**
- Attacker observes model outputs for specific input categories
- Analysis reveals correlations between inputs and demographic/personal attributes
- Reconstruction of user profiles or input characteristics without direct data access

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

This issue is tested in the following engagements:

- [x] [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] - Data leakage testing across Data/Knowledge boundary
- [x] [[playbooks/llm-application-red-team|LLM Application Red Team]] - Training data extraction and membership inference probes
- [x] Data Governance & Privacy Testing - Primary focus on data leakage attack vectors (detail page pending)

[[playbooks/engagements-overview|View all engagements]]

## Framework References

**MITRE ATLAS:**
- **TODO:** Identify applicable ATLAS techniques

**OWASP LLM Top 10:**
- LLM02: Sensitive Information Disclosure (related)

**NIST AI RMF:**
- **TODO:** Map to NIST AI RMF

**NIST GenAI Profile:**
- **TODO:** Map to GenAI Profile
