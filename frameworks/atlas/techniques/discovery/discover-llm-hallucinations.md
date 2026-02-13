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

## Case Studies (1)

- [[frameworks/atlas/case-studies/chatgpt-package-hallucination|ChatGPT Package Hallucination]]
