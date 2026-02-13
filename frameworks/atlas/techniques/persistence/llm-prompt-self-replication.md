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

## Case Studies (1)

- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm: RAG-Based Attack]]
