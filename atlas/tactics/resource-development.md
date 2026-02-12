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
These resources can be leveraged by the adversary to aid in other phases of the adversary lifecycle, such as [[atlas/tactics/ai-attack-staging|AI Attack Staging]].

## Metadata

- **Tactic ID:** AML.TA0003
- **Created:** January 24, 2022
- **Last Modified:** April 9, 2025
- **MITRE ATT&CK Reference:** [TA0042](https://attack.mitre.org/tactics/TA0042/)

## Techniques (12)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-ai-artifacts-overview|AML.T0002]] | Acquire Public AI Artifacts | realized |
| [[atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-overview|AML.T0016]] | Obtain Capabilities | realized |
| [[atlas/techniques/resource-development/develop-capabilities/develop-capabilities-overview|AML.T0017]] | Develop Capabilities | realized |
| [[atlas/techniques/resource-development/acquire-infrastructure/acquire-infrastructure-overview|AML.T0008]] | Acquire Infrastructure | realized |
| [[atlas/techniques/resource-development/publish-poisoned-datasets|AML.T0019]] | Publish Poisoned Datasets | demonstrated |
| [[atlas/techniques/resource-development/poison-training-data|AML.T0020]] | Poison Training Data | realized |
| [[atlas/techniques/resource-development/establish-accounts|AML.T0021]] | Establish Accounts | realized |
| [[atlas/techniques/resource-development/publish-poisoned-models|AML.T0058]] | Publish Poisoned Models | realized |
| [[atlas/techniques/resource-development/publish-hallucinated-entities|AML.T0060]] | Publish Hallucinated Entities | demonstrated |
| [[atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065]] | LLM Prompt Crafting | realized |
| [[atlas/techniques/resource-development/retrieval-content-crafting|AML.T0066]] | Retrieval Content Crafting | demonstrated |
| [[atlas/techniques/resource-development/stage-capabilities|AML.T0079]] | Stage Capabilities | demonstrated |


## Case Studies (32)


The following case studies demonstrate this tactic:

- [[atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|AML.CS0000: Evasion of Deep Learning Detector for Malware C&C Traffic]]
- [[atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion]]
- [[atlas/case-studies/virustotal-poisoning|AML.CS0002: VirusTotal Poisoning]]
- [[atlas/case-studies/bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]
- [[atlas/case-studies/camera-hijack-attack-on-facial-recognition-system|AML.CS0004: Camera Hijack Attack on Facial Recognition System]]
- [[atlas/case-studies/attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]
- [[atlas/case-studies/clearviewai-misconfiguration|AML.CS0006: ClearviewAI Misconfiguration]]
- [[atlas/case-studies/gpt-2-model-replication|AML.CS0007: GPT-2 Model Replication]]
- [[atlas/case-studies/microsoft-edge-ai-evasion|AML.CS0011: Microsoft Edge AI Evasion]]
- [[atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]
- [[atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[atlas/case-studies/confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]
- [[atlas/case-studies/arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]
- [[atlas/case-studies/poisongpt|AML.CS0019: PoisonGPT]]
- [[atlas/case-studies/indirect-prompt-injection-threats-bing-chat-data-pirate|AML.CS0020: Indirect Prompt Injection Threats: Bing Chat Data Pirate]]
- [[atlas/case-studies/chatgpt-conversation-exfiltration|AML.CS0021: ChatGPT Conversation Exfiltration]]
- [[atlas/case-studies/chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]
- [[atlas/case-studies/web-scale-data-poisoning-split-view-attack|AML.CS0025: Web-Scale Data Poisoning: Split-View Attack]]
- [[atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[atlas/case-studies/google-bard-conversation-exfiltration|AML.CS0029: Google Bard Conversation Exfiltration]]
- [[atlas/case-studies/llm-jacking|AML.CS0030: LLM Jacking]]
- [[atlas/case-studies/malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]
- [[atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]
- [[atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]
- [[atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|AML.CS0038: Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]
- [[atlas/case-studies/hacking-chatgpt-s-memories-with-prompt-injection|AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection]]
- [[atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|AML.CS0041: Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[atlas/case-studies/malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]


## References

MITRE Corporation. *Resource Development (AML.TA0003)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0003
