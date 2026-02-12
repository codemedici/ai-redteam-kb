---
id: persistence
title: "AML.TA0006: Persistence"
sidebar_label: "Persistence"
sidebar_position: 6
---

# AML.TA0006: Persistence

The adversary is trying to maintain their foothold via AI artifacts or software.

Persistence consists of techniques that adversaries use to keep access to systems across restarts, changed credentials, and other interruptions that could cut off their access.
Techniques used for persistence often involve leaving behind modified ML artifacts such as poisoned training data or manipulated AI models.

## Metadata

- **Tactic ID:** AML.TA0006
- **Created:** January 24, 2022
- **Last Modified:** April 9, 2025
- **MITRE ATT&CK Reference:** [TA0003](https://attack.mitre.org/tactics/TA0003/)

## Techniques (8)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[atlas/techniques/resource-development/poison-training-data|AML.T0020]] | Poison Training Data | realized |
| [[atlas/techniques/persistence/manipulate-ai-model/manipulate-ai-model-overview|AML.T0018]] | Manipulate AI Model | realized |
| [[atlas/techniques/persistence/llm-prompt-self-replication|AML.T0061]] | LLM Prompt Self-Replication | demonstrated |
| [[atlas/techniques/persistence/rag-poisoning|AML.T0070]] | RAG Poisoning | demonstrated |
| [[atlas/techniques/persistence/ai-agent-context-poisoning/ai-agent-context-poisoning-overview|AML.T0080]] | AI Agent Context Poisoning | demonstrated |
| [[atlas/techniques/persistence/modify-ai-agent-configuration|AML.T0081]] | Modify AI Agent Configuration | demonstrated |
| [[atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093]] | Prompt Infiltration via Public-Facing Application | demonstrated |
| [[atlas/techniques/persistence/ai-agent-tool-data-poisoning|AML.T0099]] | AI Agent Tool Data Poisoning | feasible |


## Case Studies (10)


The following case studies demonstrate this tactic:

- [[atlas/case-studies/virustotal-poisoning|AML.CS0002: VirusTotal Poisoning]]
- [[atlas/case-studies/tay-poisoning|AML.CS0009: Tay Poisoning]]
- [[atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[atlas/case-studies/morris-ii-worm-rag-based-attack|AML.CS0024: Morris II Worm: RAG-Based Attack]]
- [[atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]
- [[atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[atlas/case-studies/aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]
- [[atlas/case-studies/hacking-chatgpt-s-memories-with-prompt-injection|AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection]]
- [[atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|AML.CS0041: Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]


## References

MITRE Corporation. *Persistence (AML.TA0006)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0006
