---
id: llm-data-leakage
title: "AML.T0057: LLM Data Leakage"
sidebar_label: "LLM Data Leakage"
sidebar_position: 58
---

# AML.T0057: LLM Data Leakage

Adversaries may craft prompts that induce the LLM to leak sensitive information.
This can include private user data or proprietary information.
The leaked information may come from proprietary training data, data sources the LLM is connected to, or information from other users of the LLM.

## Metadata

- **Technique ID:** AML.T0057
- **Created:** 2023-10-25
- **Last Modified:** 2023-10-25
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/exfiltration|Exfiltration]]

## Mitigations (4)

- [[frameworks/atlas/mitigations/validate-ai-model|AML.M0008: Validate AI Model]] — Robust evaluation of an AI model can be used to detect privacy concerns, data leakage, and potential for revealing sensitive information.
- [[frameworks/atlas/mitigations/generative-ai-guardrails|AML.M0020: Generative AI Guardrails]] — Guardrails can detect sensitive data and PII in model outputs.
- [[frameworks/atlas/mitigations/generative-ai-guidelines|AML.M0021: Generative AI Guidelines]] — Model guidelines can instruct the model to refuse a response to unsafe inputs.
- [[frameworks/atlas/mitigations/generative-ai-model-alignment|AML.M0022: Generative AI Model Alignment]] — Model alignment can improve the parametric safety of a model by guiding it away from unsafe prompts and responses.

## Case Studies (1)

- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm: RAG-Based Attack]]
