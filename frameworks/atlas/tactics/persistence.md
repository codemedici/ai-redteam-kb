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


- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020]] — Poison Training Data (realized)
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model|AML.T0018]] — Manipulate AI Model (realized)
- [[frameworks/atlas/techniques/persistence/llm-prompt-self-replication|AML.T0061]] — LLM Prompt Self-Replication (demonstrated)
- [[frameworks/atlas/techniques/persistence/rag-poisoning|AML.T0070]] — RAG Poisoning (demonstrated)
- [[frameworks/atlas/techniques/persistence/ai-agent-context-poisoning|AML.T0080]] — AI Agent Context Poisoning (demonstrated)
- [[frameworks/atlas/techniques/persistence/modify-ai-agent-configuration|AML.T0081]] — Modify AI Agent Configuration (demonstrated)
- [[frameworks/atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093]] — Prompt Infiltration via Public-Facing Application (demonstrated)
- [[frameworks/atlas/techniques/persistence/ai-agent-tool-data-poisoning|AML.T0099]] — AI Agent Tool Data Poisoning (feasible)


## References

MITRE Corporation. *Persistence (AML.TA0006)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0006
