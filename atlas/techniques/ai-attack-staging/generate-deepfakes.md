---
id: generate-deepfakes
title: "AML.T0088: Generate Deepfakes"
sidebar_label: "Generate Deepfakes"
sidebar_position: 12
---

# AML.T0088: Generate Deepfakes



Adversaries may use generative artificial intelligence (GenAI) to create synthetic media (i.e. imagery, video, audio, and text) that appear authentic. These "[[Deepfake|deepfakes]]" may mimic a real person or depict fictional personas. Adversaries may use deepfakes for impersonation to conduct [[atlas/techniques/initial-access/phishing/phishing-overview|Phishing]] or to evade AI applications such as biometric identity verification systems (see [[atlas/techniques/initial-access/evade-ai-model|Evade AI Model]]).

Manipulation of media has been possible for a long time, however GenAI reduces the skill and level of effort required, allowing adversaries to rapidly scale operations to target more users or systems. It also makes real-time manipulations feasible.

Adversaries may utilize open-source models and software that were designed for legitimate use cases to generate deepfakes for malicious use. However, there are some projects specifically tailored towards malicious use cases such as [ProKYC](https://www.catonetworks.com/blog/prokyc-selling-deepfake-tool-for-account-fraud-attacks/).

## Metadata

- **Technique ID:** AML.T0088
- **Created:** October 31, 2025
- **Last Modified:** November 4, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]




## Case Studies (2)


The following case studies demonstrate this technique:

### [[atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]

The researchers use the gathered victim face images and the Faceswap tool to produce live deepfake videos which mimic the victim’s appearance.

### [[atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]

The bad actor used a mixture of real PII and falsified details with the ProKYC tool to generate a deepfaked identity document.



## References

MITRE Corporation. *Generate Deepfakes (AML.T0088)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0088
