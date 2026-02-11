---
id: ai-attack-staging
title: "AML.TA0001: AI Attack Staging"
sidebar_label: "AI Attack Staging"
sidebar_position: 13
---

# AML.TA0001: AI Attack Staging

The adversary is leveraging their knowledge of and access to the target system to tailor the attack.

AI Attack Staging consists of techniques adversaries use to prepare their attack on the target AI model.
Techniques can include training proxy models, poisoning the target model, and crafting adversarial data to feed the target model.
Some of these techniques can be performed in an offline manner and are thus difficult to mitigate.
These techniques are often used to achieve the adversary's end goal.

## Metadata

- **Tactic ID:** AML.TA0001
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025


## Techniques (6)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[create-proxy-ai-model|AML.T0005]] | Create Proxy AI Model | demonstrated |
| [[manipulate-ai-model|AML.T0018]] | Manipulate AI Model | realized |
| [[verify-attack|AML.T0042]] | Verify Attack | demonstrated |
| [[craft-adversarial-data|AML.T0043]] | Craft Adversarial Data | realized |
| [[generate-deepfakes|AML.T0088]] | Generate Deepfakes | realized |
| [[generate-malicious-commands|AML.T0102]] | Generate Malicious Commands | realized |


## Case Studies (20)


The following case studies demonstrate this tactic:

- [[evasion-of-deep-learning-detector-for-malware-c-c-traffic|AML.CS0000: Evasion of Deep Learning Detector for Malware C&C Traffic]]
- [[botnet-domain-generation-algorithm-dga-detection-evasion|AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion]]
- [[virustotal-poisoning|AML.CS0002: VirusTotal Poisoning]]
- [[bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]
- [[attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]
- [[gpt-2-model-replication|AML.CS0007: GPT-2 Model Replication]]
- [[proofpoint-evasion|AML.CS0008: ProofPoint Evasion]]
- [[microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]
- [[microsoft-edge-ai-evasion|AML.CS0011: Microsoft Edge AI Evasion]]
- [[face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]
- [[backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]
- [[achieving-code-execution-in-mathgpt-via-prompt-injection|AML.CS0016: Achieving Code Execution in MathGPT via Prompt Injection]]
- [[poisongpt|AML.CS0019: PoisonGPT]]
- [[organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]
- [[attempted-evasion-of-ml-phishing-webpage-detection-system|AML.CS0032: Attempted Evasion of ML Phishing Webpage Detection System]]
- [[live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]
- [[lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]


## References

MITRE Corporation. *AI Attack Staging (AML.TA0001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0001
