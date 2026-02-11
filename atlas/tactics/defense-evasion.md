---
id: defense-evasion
title: "AML.TA0007: Defense Evasion"
sidebar_label: "Defense Evasion"
sidebar_position: 8
---

# AML.TA0007: Defense Evasion

The adversary is trying to avoid being detected by AI-enabled security software.

Defense Evasion consists of techniques that adversaries use to avoid detection throughout their compromise.
Techniques used for defense evasion include evading AI-enabled security software such as malware detectors.

## Metadata

- **Tactic ID:** AML.TA0007
- **Created:** January 24, 2022
- **Last Modified:** April 9, 2025
- **MITRE ATT&CK Reference:** [TA0005](https://attack.mitre.org/tactics/TA0005/)

## Techniques (11)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[evade-ai-model|AML.T0015]] | Evade AI Model | realized |
| [[llm-jailbreak|AML.T0054]] | LLM Jailbreak | demonstrated |
| [[llm-trusted-output-components-manipulation|AML.T0067]] | LLM Trusted Output Components Manipulation | demonstrated |
| [[llm-prompt-obfuscation|AML.T0068]] | LLM Prompt Obfuscation | demonstrated |
| [[false-rag-entry-injection|AML.T0071]] | False RAG Entry Injection | demonstrated |
| [[impersonation|AML.T0073]] | Impersonation | realized |
| [[masquerading|AML.T0074]] | Masquerading | realized |
| [[corrupt-ai-model|AML.T0076]] | Corrupt AI Model | realized |
| [[manipulate-user-llm-chat-history|AML.T0092]] | Manipulate User LLM Chat History | demonstrated |
| [[delay-execution-of-llm-instructions|AML.T0094]] | Delay Execution of LLM Instructions | demonstrated |
| [[virtualization-sandbox-evasion|AML.T0097]] | Virtualization/Sandbox Evasion | realized |


## Case Studies (17)


The following case studies demonstrate this tactic:

- [[evasion-of-deep-learning-detector-for-malware-c-c-traffic|AML.CS0000: Evasion of Deep Learning Detector for Malware C&C Traffic]]
- [[botnet-domain-generation-algorithm-dga-detection-evasion|AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion]]
- [[bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]
- [[confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]
- [[indirect-prompt-injection-threats-bing-chat-data-pirate|AML.CS0020: Indirect Prompt Injection Threats: Bing Chat Data Pirate]]
- [[financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]
- [[attempted-evasion-of-ml-phishing-webpage-detection-system|AML.CS0032: Attempted Evasion of ML Phishing Webpage Detection System]]
- [[live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]
- [[aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]
- [[planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|AML.CS0038: Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[hacking-chatgpt-s-memories-with-prompt-injection|AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection]]
- [[rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|AML.CS0041: Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]
- [[lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]


## References

MITRE Corporation. *Defense Evasion (AML.TA0007)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0007
