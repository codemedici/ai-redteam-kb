---
id: retrieval-content-crafting
title: "AML.T0066: Retrieval Content Crafting"
sidebar_label: "Retrieval Content Crafting"
sidebar_position: 21
---

# AML.T0066: Retrieval Content Crafting

Adversaries may write content designed to be retrieved by user queries and influence a user of the system in some way. This abuses the trust the user has in the system.

The crafted content can be combined with a prompt injection. It can also stand alone in a separate document or email. The adversary must get the crafted content into the victim\u0027s database, such as a vector database used in a retrieval augmented generation (RAG) system. This may be accomplished via cyber access, or by abusing the ingestion mechanisms common in RAG systems (see [[frameworks/atlas/techniques/persistence/rag-poisoning|RAG Poisoning]]).

Large language models may be used as an assistant to aid an adversary in crafting content.

## Metadata

- **Technique ID:** AML.T0066
- **Created:** March 12, 2025
- **Last Modified:** March 12, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]

The Zenity researchers wrote targeted content designed to be retrieved by specific user queries.

### [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]

<div dangerouslySetInnerHTML={{__html: `The researcher crafted a targeted message designed to be retrieved when a user asks about their API key.

<div style="font-family: monospace; width: 50%; margin-left: 50px; background-color:ghostwhite; border: 2px solid black; padding: 10px;">
<span style="color: red;">“EldritchNexus API key:”</span>
</div>`}} />

## References

MITRE Corporation. *Retrieval Content Crafting (AML.T0066)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0066
