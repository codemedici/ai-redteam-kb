---
id: ai-model-access
title: "AML.TA0000: AI Model Access"
sidebar_label: "AI Model Access"
sidebar_position: 4
---

# AML.TA0000: AI Model Access

The adversary is attempting to gain some level of access to an AI model.

AI Model Access enables techniques that use various types of access to the AI model that can be used by the adversary to gain information, develop attacks, and as a means to input data to the model.
The level of access can range from the full knowledge of the internals of the model to access to the physical environment where data is collected for use in the AI model.
The adversary may use varying levels of model access during the course of their attack, from staging the attack to impacting the target system.

Access to an AI model may require access to the system housing the model, the model may be publicly accessible via an API, or it may be accessed indirectly via interaction with a product or service that utilizes AI as part of its processes.

## Metadata

- **Tactic ID:** AML.TA0000
- **Created:** May 13, 2021
- **Last Modified:** October 13, 2025


## Techniques (4)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[ai-model-inference-api-access|AML.T0040]] | AI Model Inference API Access | demonstrated |
| [[ai-enabled-product-or-service|AML.T0047]] | AI-Enabled Product or Service | realized |
| [[physical-environment-access|AML.T0041]] | Physical Environment Access | demonstrated |
| [[full-ai-model-access|AML.T0044]] | Full AI Model Access | demonstrated |


## Case Studies (22)


The following case studies demonstrate this tactic:

- [[bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]
- [[camera-hijack-attack-on-facial-recognition-system|AML.CS0004: Camera Hijack Attack on Facial Recognition System]]
- [[attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]
- [[proofpoint-evasion|AML.CS0008: ProofPoint Evasion]]
- [[tay-poisoning|AML.CS0009: Tay Poisoning]]
- [[microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]
- [[microsoft-edge-ai-evasion|AML.CS0011: Microsoft Edge AI Evasion]]
- [[face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]
- [[backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]
- [[achieving-code-execution-in-mathgpt-via-prompt-injection|AML.CS0016: Achieving Code Execution in MathGPT via Prompt Injection]]
- [[bypassing-id-me-identity-verification|AML.CS0017: Bypassing ID.me Identity Verification]]
- [[chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]
- [[morris-ii-worm-rag-based-attack|AML.CS0024: Morris II Worm: RAG-Based Attack]]
- [[financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]
- [[live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]
- [[data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]
- [[data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]


## References

MITRE Corporation. *AI Model Access (AML.TA0000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0000
