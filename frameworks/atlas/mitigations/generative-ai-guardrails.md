---
id: generative-ai-guardrails
title: "AML.M0020: Generative AI Guardrails"
sidebar_label: "Generative AI Guardrails"
sidebar_position: 21
type: mitigation
---

# AML.M0020: Generative AI Guardrails

Guardrails are safety controls that are placed between a generative AI model and the output shared with the user to prevent undesired inputs and outputs.
Guardrails can take the form of validators such as filters, rule-based logic, or regular expressions, as well as AI-based approaches, such as classifiers and utilizing LLMs, or named entity recognition (NER) to evaluate the safety of the prompt or response. Domain specific methods can be employed to reduce risks in a variety of areas such as etiquette, brand damage, jailbreaking, false information, code exploits, SQL injections, and data leakage.

## Metadata

- **Mitigation ID:** AML.M0020
- **Created:** 2025-03-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** ML Model Engineering, ML Model Evaluation, Deployment

## Techniques (8)

- [[frameworks/atlas/techniques/privilege-escalation/llm-jailbreak|AML.T0054: LLM Jailbreak]] — Guardrails can prevent harmful inputs that can lead to a jailbreak.
- [[frameworks/atlas/techniques/exfiltration/extract-llm-system-prompt|AML.T0056: Extract LLM System Prompt]] — Guardrails can prevent harmful inputs that can lead to meta prompt extraction.
- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Guardrails can prevent harmful inputs that can lead to plugin compromise, and they can detect PII in model outputs.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection|AML.T0051: LLM Prompt Injection]] — Guardrails can prevent harmful inputs that can lead to prompt injection.
- [[frameworks/atlas/techniques/exfiltration/llm-data-leakage|AML.T0057: LLM Data Leakage]] — Guardrails can detect sensitive data and PII in model outputs.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise|AML.T0010: AI Supply Chain Compromise]] — Guardrails can detect harmful code in model outputs.
- [[frameworks/atlas/techniques/persistence/llm-prompt-self-replication|AML.T0061: LLM Prompt Self-Replication]] — Guardrails can help prevent replication attacks in model inputs and outputs.
- [[frameworks/atlas/techniques/discovery/discover-llm-hallucinations|AML.T0062: Discover LLM Hallucinations]] — Guardrails can help block hallucinated content that appears in model output.
