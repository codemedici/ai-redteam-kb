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

## Mitigations (2)

- [[frameworks/atlas/mitigations/privileged-ai-agent-permissions-configuration|AML.M0026: Privileged AI Agent Permissions Configuration]] — Configuring privileged AI agents with proper access controls can limit an adversary's ability to harvest credentials from RAG Databases if the agent is compromised.
- [[frameworks/atlas/mitigations/single-user-ai-agent-permissions-configuration|AML.M0027: Single-User AI Agent Permissions Configuration]] — Configuring AI agents with permissions that are inherited from the user can limit an adversary's ability to harvest credentials from RAG Databases if the agent is compromised.

## Case Studies (1)

- [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Data Exfiltration from Slack AI via Indirect Prompt Injection]]
