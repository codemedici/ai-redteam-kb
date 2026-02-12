---
id: llm-data-leakage
title: "AML.T0057: LLM Data Leakage"
sidebar_label: "LLM Data Leakage"
sidebar_position: 7
---

# AML.T0057: LLM Data Leakage

Adversaries may craft prompts that induce the LLM to leak sensitive information.
This can include private user data or proprietary information.
The leaked information may come from proprietary training data, data sources the LLM is connected to, or information from other users of the LLM.

## Metadata

- **Technique ID:** AML.T0057
- **Created:** October 25, 2023
- **Last Modified:** October 25, 2023
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[atlas/case-studies/morris-ii-worm-rag-based-attack|AML.CS0024: Morris II Worm: RAG-Based Attack]]

The malicious instructions in the prompt cause the generated output to leak sensitive data such as emails, addresses, and phone numbers.

## References

MITRE Corporation. *LLM Data Leakage (AML.T0057)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0057
