---
id: generate-deepfakes
title: "AML.T0088: Generate Deepfakes"
sidebar_label: "Generate Deepfakes"
sidebar_position: 89
---

# AML.T0088: Generate Deepfakes

Adversaries may use generative artificial intelligence (GenAI) to create synthetic media (i.e. imagery, video, audio, and text) that appear authentic. These "[deepfakes]( https://en.wikipedia.org/wiki/Deepfake)" may mimic a real person or depict fictional personas. Adversaries may use deepfakes for impersonation to conduct [Phishing](/techniques/AML.T0052) or to evade AI applications such as biometric identity verification systems (see [Evade AI Model](/techniques/AML.T0015)).

Manipulation of media has been possible for a long time, however GenAI reduces the skill and level of effort required, allowing adversaries to rapidly scale operations to target more users or systems. It also makes real-time manipulations feasible.

Adversaries may utilize open-source models and software that were designed for legitimate use cases to generate deepfakes for malicious use. However, there are some projects specifically tailored towards malicious use cases such as [ProKYC](https://www.catonetworks.com/blog/prokyc-selling-deepfake-tool-for-account-fraud-attacks/).

## Metadata

- **Technique ID:** AML.T0088
- **Created:** 2025-10-31
- **Last Modified:** 2025-11-04
- **Maturity:** realized

## Tactics (1)

- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[frameworks/atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|ProKYC: Deepfake Tool for Account Fraud Attacks]]
