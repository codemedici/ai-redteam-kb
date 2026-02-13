---
id: impersonation
title: "AML.T0073: Impersonation"
sidebar_label: "Impersonation"
sidebar_position: 74
---

# AML.T0073: Impersonation

Adversaries may impersonate a trusted person or organization in order to persuade and trick a target into performing some action on their behalf. For example, adversaries may communicate with victims (via [Phishing](/techniques/AML.T0052), or [Spearphishing via Social Engineering LLM](/techniques/AML.T0052.000)) while impersonating a known sender such as an executive, colleague, or third-party vendor. Established trust can then be leveraged to accomplish an adversary's ultimate goals, possibly against multiple victims.

Adversaries may target resources that are part of the AI DevOps lifecycle, such as model repositories, container registries, and software registries.

## Metadata

- **Technique ID:** AML.T0073
- **Created:** 2025-04-14
- **Last Modified:** 2025-04-14
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1656](https://attack.mitre.org/techniques/T1656/)

## Tactics (1)

- [[frameworks/atlas/tactics/defense-evasion|Defense Evasion]]

## Case Studies (4)

- [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|Organization Confusion on Hugging Face]]
- [[frameworks/atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[frameworks/atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|ProKYC: Deepfake Tool for Account Fraud Attacks]]
- [[frameworks/atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]
