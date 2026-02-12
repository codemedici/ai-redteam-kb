---
id: rag-credential-harvesting
title: "AML.T0082: RAG Credential Harvesting"
sidebar_label: "RAG Credential Harvesting"
sidebar_position: 2
---

# AML.T0082: RAG Credential Harvesting



Adversaries may attempt to use their access to a large language model (LLM) on the victim's system to collect credentials. Credentials may be stored in internal documents which can inadvertently be ingested into a RAG database, where they can ultimately be retrieved by an AI agent.

## Metadata

- **Technique ID:** AML.T0082
- **Created:** September 30, 2025
- **Last Modified:** September 30, 2025
- **Maturity:** demonstrated



## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/credential-access|AML.TA0013: Credential Access]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]

Because Slack AI has access to the victim user’s private channels, it retrieves the victim’s API Key.



## References

MITRE Corporation. *RAG Credential Harvesting (AML.T0082)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0082
