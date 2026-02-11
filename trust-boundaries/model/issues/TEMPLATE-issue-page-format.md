---
id: template-issue-page-format
title: "TEMPLATE - Issue Page Format"
sidebar_label: "TEMPLATE - Issue Page Format"
description: Canonical template for all trust boundary issue pages. Do not use this as actual content.
draft: true
---

# TEMPLATE - Issue Page Format

> **⚠️ THIS IS A TEMPLATE FILE** - Copy this structure when creating new issue pages. Replace all placeholder text with actual content.

## Summary

[Brief 2-3 sentence overview of the security issue. Describe what can go wrong and why it matters.]

## Threat Scenario

[Describe a concrete scenario where this issue manifests. Include:
- Who the attacker is (external adversary, malicious insider, etc.)
- What they are trying to achieve
- What makes this attack feasible]

## Preconditions

[List required conditions for this issue to be exploitable:]

- [Precondition 1: e.g., "Model has access to external tools"]
- [Precondition 2: e.g., "No input validation on RAG documents"]
- [Precondition 3: e.g., "User can upload arbitrary files"]

## Abuse Path

[Step-by-step attack sequence:]

1. [Step 1: Initial access or reconnaissance]
2. [Step 2: Exploitation technique]
3. [Step 3: Achieving impact]
4. [Step 4: Exfiltration or persistence (if applicable)]

## Impact

**Business Impact:**
- [Impact 1: e.g., "Data breach exposing customer PII"]
- [Impact 2: e.g., "Service disruption affecting availability"]

**Technical Impact:**
- [Impact 1: e.g., "Unauthorized code execution"]
- [Impact 2: e.g., "Model behavior manipulation"]

**Severity:** [Critical / High / Medium / Low]

## Detection Signals

[Observable indicators that this issue is being exploited:]

- [Signal 1: e.g., "Unusual query patterns in RAG retrieval logs"]
- [Signal 2: e.g., "Spike in model refusals or unexpected outputs"]
- [Signal 3: e.g., "Anomalous tool invocations"]

## Testing Approach

[How to test for this vulnerability during a red team engagement:]

**Manual Testing:**
- [Test 1: Specific steps to reproduce]
- [Test 2: Variations to try]

**Automated Testing:**
- [Tool recommendations or script approaches]

**Expected Results:**
- [What success looks like]
- [What failure looks like]

## Evidence to Capture

[Documentation required for reporting:]

- [ ] Proof-of-concept prompts and model responses
- [ ] Screenshots or logs demonstrating impact
- [ ] Tool invocation traces (if applicable)
- [ ] Before/after state comparisons
- [ ] Reproduction steps with timestamps

## Mitigations

**Preventive Controls:**
- [Control 1: e.g., "Implement input validation on all RAG sources"]
- [Control 2: e.g., "Use prompt hierarchy enforcement"]

**Detective Controls:**
- [Control 1: e.g., "Monitor for anomalous retrieval patterns"]
- [Control 2: e.g., "Alert on high-risk tool invocations"]

**Responsive Controls:**
- [Control 1: e.g., "Implement kill-switch for agent workflows"]
- [Control 2: e.g., "Rate limit suspicious users"]

## Engagement Applicability

[Which engagement types should test for this issue:]

- [ ] [Engagement Type 1: e.g., "Full AI Security Assessment"]
- [ ] [Engagement Type 2: e.g., "RAG Security Review"]
- [ ] [Engagement Type 3: e.g., "Agent Penetration Test"]

## Framework References

**MITRE ATLAS:**
- [AML.T####: Technique Name]
- [AML.TA##: Tactic Name]

**OWASP LLM Top 10:**
- [LLM##: Issue Name]

**NIST AI RMF:**
- [Function: e.g., GOVERN, MAP, MEASURE, MANAGE]
- [Subcategory if applicable]

**NIST GenAI Profile:**
- [GAI Risk ID or category]
