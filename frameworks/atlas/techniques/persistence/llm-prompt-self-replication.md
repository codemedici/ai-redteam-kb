---
id: llm-prompt-self-replication
title: "AML.T0061: LLM Prompt Self-Replication"
sidebar_label: "LLM Prompt Self-Replication"
sidebar_position: 62
---

# AML.T0061: LLM Prompt Self-Replication

An adversary may use a carefully crafted [LLM Prompt Injection](/techniques/AML.T0051) designed to cause the LLM to replicate the prompt as part of its output. This allows the prompt to propagate to other LLMs and persist on the system. The self-replicating prompt is typically paired with other malicious instructions (ex: [LLM Jailbreak](/techniques/AML.T0054), [LLM Data Leakage](/techniques/AML.T0057)).

## Metadata

- **Technique ID:** AML.T0061
- **Created:** 2025-03-12
- **Last Modified:** 2025-03-12
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/persistence|Persistence]]

## Mitigations (3)

- [[frameworks/atlas/mitigations/generative-ai-guardrails|AML.M0020: Generative AI Guardrails]] — Guardrails can help prevent replication attacks in model inputs and outputs.
- [[frameworks/atlas/mitigations/generative-ai-guidelines|AML.M0021: Generative AI Guidelines]] — Guidelines can help instruct the model to produce more secure output, preventing the model from generating self-replicating outputs.
- [[frameworks/atlas/mitigations/generative-ai-model-alignment|AML.M0022: Generative AI Model Alignment]] — Model alignment can increase the security of models to self replicating prompt attacks.

## Case Studies (1)

- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm: RAG-Based Attack]]
