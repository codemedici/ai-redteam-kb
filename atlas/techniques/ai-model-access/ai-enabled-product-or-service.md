---
id: ai-enabled-product-or-service
title: "AML.T0047: AI-Enabled Product or Service"
sidebar_label: "AI-Enabled Product or Service"
sidebar_position: 2
---

# AML.T0047: AI-Enabled Product or Service



Adversaries may use a product or service that uses artificial intelligence under the hood to gain access to the underlying AI model.
This type of indirect model access may reveal details of the AI model or its inferences in logs or metadata.

## Metadata

- **Technique ID:** AML.T0047
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/ai-model-access|AML.TA0000: AI Model Access]]




## Case Studies (13)


The following case studies demonstrate this technique:

### [[atlas/case-studies/bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]

The researchers had access to Cylance's AI-enabled malware detection software.

### [[atlas/case-studies/camera-hijack-attack-on-facial-recognition-system|AML.CS0004: Camera Hijack Attack on Facial Recognition System]]

The attackers used the virtual camera app to present the generated video to the ML-based facial recognition service used for user verification.

### [[atlas/case-studies/proofpoint-evasion|AML.CS0008: ProofPoint Evasion]]

The researchers sent many emails through the system to collect model outputs from the headers.

### [[atlas/case-studies/tay-poisoning|AML.CS0009: Tay Poisoning]]

Adversaries were able to interact with Tay via Twitter messages.

### [[atlas/case-studies/confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]

The researchers used access to the target ML-based antimalware product throughout this case study.
This product scans files on the user's system, extracts features locally, then sends them to the cloud-based ML malware detector for classification.
Therefore, the researchers had only black-box access to the malware detector itself, but could learn valuable information for constructing the attack from the feature extractor.

### [[atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|AML.CS0016: Achieving Code Execution in MathGPT via Prompt Injection]]

The actor was able to interact with the underlying GPT-3 model via the MathGPT application. MathGPT uses GPT-3 to generate Python code that solves math problems described by user-inputted prompts. It displays the generated code as well as the solution for the user. Exploration of provided and custom prompts, as well as their outputs, led the actor to suspect that the application directly executed generated code from GPT-3.

### [[atlas/case-studies/bypassing-id-me-identity-verification|AML.CS0017: Bypassing ID.me Identity Verification]]

The individual applied for unemployment assistance with the California Employment Development Department using forged identities, interacting with ID.me's identity verification system in the process.

The system extracts content from a photo of an ID, validates the authenticity of the ID using a combination of AI and proprietary methods, then performs facial recognition to match the ID photo to a selfie. <sup>[[7]](https://network.id.me/wp-content/uploads/Document-Verification-Use-Machine-Vision-and-AI-to-Extract-Content-and-Verify-the-Authenticity-1.pdf)</sup>

The individual identified that the California Employment Development Department relied on a third party service, ID.me, to verify individuals' identities.

The ID.me website outlines the steps to verify an identity, including entering personal information, uploading a driver license, and submitting a selfie photo.

### [[atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]

The Zenity researchers interacted with Microsoft Copilot for M365 during attack development and execution of the attack on the victim system.

### [[atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]

During identity verification, the financial services application uses facial recognition and liveness detection to analyze live video from the user’s camera.

### [[atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]

During identity verification, the financial services application used facial recognition and liveness detection to analyze live video from the user’s camera.

### [[atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]

The researcher interacts with Slack AI by sending messages in public Slack channels.

### [[atlas/case-studies/aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]

The attacker has now obtained the access required to communicate with the LLM backend service as if they were the desktop client. This allowed them access to everything the user can do with the desktop application.

### [[atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]

From here, the researchers repeat the same steps to interact with the AI agent, sending malicious prompts to the agent via email and receiving responses at their desired address.



## References

MITRE Corporation. *AI-Enabled Product or Service (AML.T0047)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0047
