---
id: false-rag-entry-injection
title: "AML.T0071: False RAG Entry Injection"
sidebar_label: "False RAG Entry Injection"
sidebar_position: 72
---

# AML.T0071: False RAG Entry Injection

Adversaries may introduce false entries into a victim's retrieval augmented generation (RAG) database. Content designed to be interpreted as a document by the large language model (LLM) used in the RAG system is included in a data source being ingested into the RAG database. When RAG entry including the false document is retrieved, the LLM is tricked into treating part of the retrieved content as a false RAG result. 

By including a false RAG document inside of a regular RAG entry, it bypasses data monitoring tools. It also prevents the document from being deleted directly. 

The adversary may use discovered system keywords to learn how to instruct a particular LLM to treat content as a RAG entry. They may be able to manipulate the injected entry's metadata including document title, author, and creation date.

## Metadata

- **Technique ID:** AML.T0071
- **Created:** 2025-03-12
- **Last Modified:** 2025-12-24
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/defense-evasion|Defense Evasion]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|Financial Transaction Hijacking with M365 Copilot as an Insider]]
