---
id: rag-credential-harvesting
title: "AML.T0082: RAG Credential Harvesting"
sidebar_label: "RAG Credential Harvesting"
sidebar_position: 83
---

# AML.T0082: RAG Credential Harvesting

Adversaries may attempt to use their access to a large language model (LLM) on the victim's system to collect credentials. Credentials may be stored in internal documents which can inadvertently be ingested into a RAG database, where they can ultimately be retrieved by an AI agent.

## Metadata

- **Technique ID:** AML.T0082
- **Created:** 2025-09-30
- **Last Modified:** 2025-09-30
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/credential-access|Credential Access]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Data Exfiltration from Slack AI via Indirect Prompt Injection]]
