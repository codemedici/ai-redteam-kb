---
id: false-rag-entry-injection
title: "AML.T0071: False RAG Entry Injection"
sidebar_label: "False RAG Entry Injection"
sidebar_position: 3
---

# AML.T0071: False RAG Entry Injection



Adversaries may introduce false entries into a victim's retrieval augmented generation (RAG) database. Content designed to be interpreted as a document by the large language model (LLM) used in the RAG system is included in a data source being ingested into the RAG database. When RAG entry including the false document is retrieved, the LLM is tricked into treating part of the retrieved content as a false RAG result. 

By including a false RAG document inside of a regular RAG entry, it bypasses data monitoring tools. It also prevents the document from being deleted directly. 

The adversary may use discovered system keywords to learn how to instruct a particular LLM to treat content as a RAG entry. They may be able to manipulate the injected entry's metadata including document title, author, and creation date.

## Metadata

- **Technique ID:** AML.T0071
- **Created:** March 12, 2025
- **Last Modified:** December 24, 2025
- **Maturity:** demonstrated



## Tactics (1)

This technique supports the following tactics:


- [[defense-evasion|AML.TA0007: Defense Evasion]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]

<div dangerouslySetInnerHTML={{__html: `When the user searches for bank details and the poisoned RAG entry is retrieved, the <span style="color: green; font-family: monospace">Actual Snippet:</span> specifier makes the retrieved text appear to the LLM as a snippet from a real document.`}} />



## References

MITRE Corporation. *False RAG Entry Injection (AML.T0071)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0071
