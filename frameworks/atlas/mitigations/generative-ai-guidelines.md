---
id: generative-ai-guidelines
title: "AML.M0021: Generative AI Guidelines"
sidebar_label: "Generative AI Guidelines"
sidebar_position: 22
type: mitigation
---

# AML.M0021: Generative AI Guidelines

Guidelines are safety controls that are placed between user-provided input and a generative AI model to help direct the model to produce desired outputs and prevent undesired outputs.

Guidelines can be implemented as instructions appended to all user prompts or as part of the instructions in the system prompt. They can define the goal(s), role, and voice of the system, as well as outline safety and security parameters.

## Metadata

- **Mitigation ID:** AML.M0021
- **Created:** 2025-03-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** ML Model Engineering, ML Model Evaluation, Deployment

## Techniques (7)

- [[frameworks/atlas/techniques/privilege-escalation/llm-jailbreak|AML.T0054: LLM Jailbreak]] — Model guidelines can instruct the model to refuse a response to unsafe inputs.
- [[frameworks/atlas/techniques/exfiltration/extract-llm-system-prompt|AML.T0056: Extract LLM System Prompt]] — Model guidelines can instruct the model to refuse a response to unsafe inputs.
- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Model guidelines can instruct the model to refuse a response to unsafe inputs.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection|AML.T0051: LLM Prompt Injection]] — Model guidelines can instruct the model to refuse a response to unsafe inputs.
- [[frameworks/atlas/techniques/exfiltration/llm-data-leakage|AML.T0057: LLM Data Leakage]] — Model guidelines can instruct the model to refuse a response to unsafe inputs.
- [[frameworks/atlas/techniques/persistence/llm-prompt-self-replication|AML.T0061: LLM Prompt Self-Replication]] — Guidelines can help instruct the model to produce more secure output, preventing the model from generating self-replicating outputs.
- [[frameworks/atlas/techniques/discovery/discover-llm-hallucinations|AML.T0062: Discover LLM Hallucinations]] — Guidelines can instruct the model to avoid producing hallucinated content.
