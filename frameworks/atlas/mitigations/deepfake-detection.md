---
id: deepfake-detection
title: "AML.M0034: Deepfake Detection"
sidebar_label: "Deepfake Detection"
sidebar_position: 35
type: mitigation
---

# AML.M0034: Deepfake Detection

Apply deepfake detection algorithms against any untrusted or user-provided data, especially in impactful applications such as biometric verification, to block generated content.

Detectors may use a combination of approaches, including:
-	AI models trained to differentiate between real and deepfake content.
-	Identifying common inconsistencies in deepfake content, such as unnatural facial movements, audio mismatches, or pixel-level artifacts.
-	Biometrics analysis, such blinking, eye movements, and microexpressions.

## Metadata

- **Mitigation ID:** AML.M0034
- **Created:** 2025-11-25
- **Last Modified:** 2025-11-25
- **Category:** Technical - ML
- **ML Lifecycle:** Deployment, Monitoring and Maintenance, ML Model Evaluation, ML Model Engineering

## Techniques (4)

- [[frameworks/atlas/techniques/ai-attack-staging/generate-deepfakes|AML.T0088: Generate Deepfakes]] — Deepfake detection can be used to identify and block generated content.
- [[frameworks/atlas/techniques/initial-access/phishing/phishing-spearphishing-via-LLM|AML.T0052: Phishing]] — Deepfake detection can be used to identify and block phishing attempts that use generated content.
- [[frameworks/atlas/techniques/initial-access/phishing/phishing-spearphishing-via-LLM|AML.T0052.000: Spearphishing via Social Engineering LLM]] — Deepfake detection can be used to identify and block phishing attempts that use generated content.
- [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]] — Deepfake detection can be used to identify and block generated content.
