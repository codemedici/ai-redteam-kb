---
id: rag-poisoning
title: "AML.T0070: RAG Poisoning"
sidebar_label: "RAG Poisoning"
sidebar_position: 71
---

# AML.T0070: RAG Poisoning

Adversaries may inject malicious content into data indexed by a retrieval augmented generation (RAG) system to contaminate a future thread through RAG-based search results. This may be accomplished by placing manipulated documents in a location the RAG indexes (see [Gather RAG-Indexed Targets](/techniques/AML.T0064)).

The content may be targeted such that it would always surface as a search result for a specific user query. The adversary's content may include false or misleading information. It may also include prompt injections with malicious instructions, or false RAG entries.

## Metadata

- **Technique ID:** AML.T0070
- **Created:** 2025-03-12
- **Last Modified:** 2025-03-12
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/persistence|Persistence]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Data Exfiltration from Slack AI via Indirect Prompt Injection]]
