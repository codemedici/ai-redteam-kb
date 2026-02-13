---
id: retrieval-content-crafting
title: "AML.T0066: Retrieval Content Crafting"
sidebar_label: "Retrieval Content Crafting"
sidebar_position: 67
---

# AML.T0066: Retrieval Content Crafting

Adversaries may write content designed to be retrieved by user queries and influence a user of the system in some way. This abuses the trust the user has in the system.

The crafted content can be combined with a prompt injection. It can also stand alone in a separate document or email. The adversary must get the crafted content into the victim\u0027s database, such as a vector database used in a retrieval augmented generation (RAG) system. This may be accomplished via cyber access, or by abusing the ingestion mechanisms common in RAG systems (see [RAG Poisoning](/techniques/AML.T0070)).

Large language models may be used as an assistant to aid an adversary in crafting content.

## Metadata

- **Technique ID:** AML.T0066
- **Created:** 2025-03-12
- **Last Modified:** 2025-03-12
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Data Exfiltration from Slack AI via Indirect Prompt Injection]]
