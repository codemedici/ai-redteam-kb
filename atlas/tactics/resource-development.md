---
id: resource-development
title: "AML.TA0003: Resource Development"
sidebar_label: "Resource Development"
sidebar_position: 2
---

# AML.TA0003: Resource Development

The adversary is trying to establish resources they can use to support operations.

Resource Development consists of techniques that involve adversaries creating,
purchasing, or compromising/stealing resources that can be used to support targeting.
Such resources include AI artifacts, infrastructure, accounts, or capabilities.
These resources can be leveraged by the adversary to aid in other phases of the adversary lifecycle, such as [[ai-attack-staging|AI Attack Staging]].

## Metadata

- **Tactic ID:** AML.TA0003
- **Created:** January 24, 2022
- **Last Modified:** April 9, 2025
- **MITRE ATT&CK Reference:** [TA0042](https://attack.mitre.org/tactics/TA0042/)

## Techniques (12)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[acquire-public-ai-artifacts|AML.T0002]] | Acquire Public AI Artifacts | realized |
| [[obtain-capabilities|AML.T0016]] | Obtain Capabilities | realized |
| [[develop-capabilities|AML.T0017]] | Develop Capabilities | realized |
| [[acquire-infrastructure|AML.T0008]] | Acquire Infrastructure | realized |
| [[publish-poisoned-datasets|AML.T0019]] | Publish Poisoned Datasets | demonstrated |
| [[poison-training-data|AML.T0020]] | Poison Training Data | realized |
| [[establish-accounts|AML.T0021]] | Establish Accounts | realized |
| [[publish-poisoned-models|AML.T0058]] | Publish Poisoned Models | realized |
| [[publish-hallucinated-entities|AML.T0060]] | Publish Hallucinated Entities | demonstrated |
| [[llm-prompt-crafting|AML.T0065]] | LLM Prompt Crafting | realized |
| [[retrieval-content-crafting|AML.T0066]] | Retrieval Content Crafting | demonstrated |
| [[stage-capabilities|AML.T0079]] | Stage Capabilities | demonstrated |


## Case Studies (32)


The following case studies demonstrate this tactic:

- [[evasion-of-deep-learning-detector-for-malware-c-c-traffic|AML.CS0000: Evasion of Deep Learning Detector for Malware C&C Traffic]]
- [[botnet-domain-generation-algorithm-dga-detection-evasion|AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion]]
- [[virustotal-poisoning|AML.CS0002: VirusTotal Poisoning]]
- [[bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]
- [[camera-hijack-attack-on-facial-recognition-system|AML.CS0004: Camera Hijack Attack on Facial Recognition System]]
- [[attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]
- [[clearviewai-misconfiguration|AML.CS0006: ClearviewAI Misconfiguration]]
- [[gpt-2-model-replication|AML.CS0007: GPT-2 Model Replication]]
- [[microsoft-edge-ai-evasion|AML.CS0011: Microsoft Edge AI Evasion]]
- [[face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]
- [[backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]
- [[arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]
- [[poisongpt|AML.CS0019: PoisonGPT]]
- [[indirect-prompt-injection-threats-bing-chat-data-pirate|AML.CS0020: Indirect Prompt Injection Threats: Bing Chat Data Pirate]]
- [[chatgpt-conversation-exfiltration|AML.CS0021: ChatGPT Conversation Exfiltration]]
- [[chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]
- [[web-scale-data-poisoning-split-view-attack|AML.CS0025: Web-Scale Data Poisoning: Split-View Attack]]
- [[financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[google-bard-conversation-exfiltration|AML.CS0029: Google Bard Conversation Exfiltration]]
- [[llm-jacking|AML.CS0030: LLM Jacking]]
- [[malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]
- [[live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]
- [[data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]
- [[planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|AML.CS0038: Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]
- [[hacking-chatgpt-s-memories-with-prompt-injection|AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection]]
- [[rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|AML.CS0041: Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]


## References

MITRE Corporation. *Resource Development (AML.TA0003)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0003
