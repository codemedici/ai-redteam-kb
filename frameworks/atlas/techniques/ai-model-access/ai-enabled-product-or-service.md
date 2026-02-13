---
id: ai-enabled-product-or-service
title: "AML.T0047: AI-Enabled Product or Service"
sidebar_label: "AI-Enabled Product or Service"
sidebar_position: 48
---

# AML.T0047: AI-Enabled Product or Service

Adversaries may use a product or service that uses artificial intelligence under the hood to gain access to the underlying AI model.
This type of indirect model access may reveal details of the AI model or its inferences in logs or metadata.

## Metadata

- **Technique ID:** AML.T0047
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** realized

## Tactics (1)

- [[frameworks/atlas/tactics/ai-model-access|AI Model Access]]

## Mitigations (1)

- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Telemetry logging can help identify if sensitive model information has been sent to an attacker.

## Case Studies (13)

- [[frameworks/atlas/case-studies/bypassing-cylance-s-ai-malware-detection|Bypassing Cylance's AI Malware Detection]]
- [[frameworks/atlas/case-studies/camera-hijack-attack-on-facial-recognition-system|Camera Hijack Attack on Facial Recognition System]]
- [[frameworks/atlas/case-studies/proofpoint-evasion|ProofPoint Evasion]]
- [[frameworks/atlas/case-studies/tay-poisoning|Tay Poisoning]]
- [[frameworks/atlas/case-studies/confusing-antimalware-neural-networks|Confusing Antimalware Neural Networks]]
- [[frameworks/atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|Achieving Code Execution in MathGPT via Prompt Injection]]
- [[frameworks/atlas/case-studies/bypassing-id-me-identity-verification|Bypassing ID.me Identity Verification]]
- [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[frameworks/atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[frameworks/atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|ProKYC: Deepfake Tool for Account Fraud Attacks]]
- [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AIKatz: Attacking LLM Desktop Applications]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|Data Exfiltration via Agent Tools in Copilot Studio]]
