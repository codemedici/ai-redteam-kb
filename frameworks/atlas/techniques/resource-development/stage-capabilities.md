---
id: stage-capabilities
title: "AML.T0079: Stage Capabilities"
sidebar_label: "Stage Capabilities"
sidebar_position: 23
---

# AML.T0079: Stage Capabilities

Adversaries may upload, install, or otherwise set up capabilities that can be used during targeting. To support their operations, an adversary may need to take capabilities they developed ([[frameworks/atlas/techniques/resource-development/develop-capabilities|Develop Capabilities]]) or obtained ([[frameworks/atlas/techniques/resource-development/obtain-capabilities|Obtain Capabilities]]) and stage them on infrastructure under their control. These capabilities may be staged on infrastructure that was previously purchased/rented by the adversary ([[frameworks/atlas/techniques/resource-development/acquire-infrastructure|Acquire Infrastructure]]) or was otherwise compromised by them. Capabilities may also be staged on web services, such as GitHub, model registries, such as Hugging Face, or container registries.

Adversaries may stage a variety of AI Artifacts including poisoned datasets ([[frameworks/atlas/techniques/resource-development/publish-poisoned-datasets|Publish Poisoned Datasets]], malicious models ([[frameworks/atlas/techniques/resource-development/publish-poisoned-models|Publish Poisoned Models]], and prompt injections. They may target names of legitimate companies or products, engage in typosquatting, or use hallucinated entities ([[frameworks/atlas/techniques/discovery/discover-llm-hallucinations|Discover LLM Hallucinations]]).

## Metadata

- **Technique ID:** AML.T0079
- **Created:** April 16, 2025
- **Last Modified:** April 17, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1608](https://attack.mitre.org/techniques/T1608/)

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/chatgpt-conversation-exfiltration|AML.CS0021: ChatGPT Conversation Exfiltration]]

The researcher included the prompt in a webpage, where it could be retrieved by ChatGPT.

### [[frameworks/atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|AML.CS0041: Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]

The researchers staged a malicious javascript file on a publicly available website.

## References

MITRE Corporation. *Stage Capabilities (AML.T0079)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0079
