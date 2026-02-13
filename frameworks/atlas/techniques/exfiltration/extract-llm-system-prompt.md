---
id: extract-llm-system-prompt
title: "AML.T0056: Extract LLM System Prompt"
sidebar_label: "Extract LLM System Prompt"
sidebar_position: 57
---

# AML.T0056: Extract LLM System Prompt

Adversaries may attempt to extract a large language model's (LLM) system prompt. This can be done via prompt injection to induce the model to reveal its own system prompt or may be extracted from a configuration file.

System prompts can be a portion of an AI provider's competitive advantage and are thus valuable intellectual property that may be targeted by adversaries.

## Metadata

- **Technique ID:** AML.T0056
- **Created:** 2023-10-25
- **Last Modified:** 2025-03-12
- **Maturity:** feasible

## Tactics (1)

- [[frameworks/atlas/tactics/exfiltration|Exfiltration]]

## Mitigations (3)

- [[frameworks/atlas/mitigations/generative-ai-guardrails|AML.M0020: Generative AI Guardrails]] — Guardrails can prevent harmful inputs that can lead to meta prompt extraction.
- [[frameworks/atlas/mitigations/generative-ai-guidelines|AML.M0021: Generative AI Guidelines]] — Model guidelines can instruct the model to refuse a response to unsafe inputs.
- [[frameworks/atlas/mitigations/generative-ai-model-alignment|AML.M0022: Generative AI Model Alignment]] — Model alignment can improve the parametric safety of a model by guiding it away from unsafe prompts and responses.
