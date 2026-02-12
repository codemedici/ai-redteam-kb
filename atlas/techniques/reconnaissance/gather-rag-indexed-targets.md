---
id: gather-rag-indexed-targets
title: "AML.T0064: Gather RAG-Indexed Targets"
sidebar_label: "Gather RAG-Indexed Targets"
sidebar_position: 9
---

# AML.T0064: Gather RAG-Indexed Targets

Adversaries may identify data sources used in retrieval augmented generation (RAG) systems for targeting purposes. By pinpointing these sources, attackers can focus on poisoning or otherwise manipulating the external data repositories the AI relies on.

RAG-indexed data may be identified in public documentation about the system, or by interacting with the system directly and observing any indications of or references to external data sources.

## Metadata

- **Technique ID:** AML.T0064
- **Created:** March 12, 2025
- **Last Modified:** March 12, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]

The Zenity researchers identified that Microsoft Copilot for M365 indexes all e-mails received in an inbox, even if the recipient does not open them.

## References

MITRE Corporation. *Gather RAG-Indexed Targets (AML.T0064)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0064
