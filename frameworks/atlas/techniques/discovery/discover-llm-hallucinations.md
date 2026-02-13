---
id: discover-llm-hallucinations
title: "AML.T0062: Discover LLM Hallucinations"
sidebar_label: "Discover LLM Hallucinations"
sidebar_position: 63
---

# AML.T0062: Discover LLM Hallucinations

Adversaries may prompt large language models and identify hallucinated entities.
They may request software packages, commands, URLs, organization names, or e-mail addresses, and identify hallucinations with no connected real-world source. Discovered hallucinations provide the adversary with potential targets to [Publish Hallucinated Entities](/techniques/AML.T0060). Different LLMs have been shown to produce the same hallucinations, so the hallucinations exploited by an adversary may affect users of other LLMs.

## Metadata

- **Technique ID:** AML.T0062
- **Created:** 2025-03-12
- **Last Modified:** 2025-10-31
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/discovery|Discovery]]

## Mitigations (4)

- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Restricting number of model queries limits or slows an adversary's ability to identify possible hallucinations.
- [[frameworks/atlas/mitigations/generative-ai-guardrails|AML.M0020: Generative AI Guardrails]] — Guardrails can help block hallucinated content that appears in model output.
- [[frameworks/atlas/mitigations/generative-ai-guidelines|AML.M0021: Generative AI Guidelines]] — Guidelines can instruct the model to avoid producing hallucinated content.
- [[frameworks/atlas/mitigations/generative-ai-model-alignment|AML.M0022: Generative AI Model Alignment]] — Model alignment can help steer the model away from hallucinated content.

## Case Studies (1)

- [[frameworks/atlas/case-studies/chatgpt-package-hallucination|ChatGPT Package Hallucination]]
