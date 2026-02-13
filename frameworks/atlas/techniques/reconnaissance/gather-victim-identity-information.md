---
id: gather-victim-identity-information
title: "AML.T0087: Gather Victim Identity Information"
sidebar_label: "Gather Victim Identity Information"
sidebar_position: 88
---

# AML.T0087: Gather Victim Identity Information

Adversaries may gather information about the victim's identity that can be used during targeting. Information about identities may include a variety of details, including personal data (ex: employee names, email addresses, photos, etc.) as well as sensitive details such as credentials or multi-factor authentication (MFA) configurations.

Adversaries may gather this information in various ways, such as direct elicitation, [Search Victim-Owned Websites](/techniques/AML.T0003), or via leaked information on the black market.

Adversaries may use the gathered victim data to Create Deepfakes and impersonate them in a convincing manner. This may create opportunities for adversaries to [Establish Accounts](/techniques/AML.T0021) under the impersonated identity, or allow them to perform convincing [Phishing](/techniques/AML.T0052) attacks.

## Metadata

- **Technique ID:** AML.T0087
- **Created:** 2025-10-31
- **Last Modified:** 2025-10-27
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1589](https://attack.mitre.org/techniques/T1589/)

## Tactics (1)

- [[frameworks/atlas/tactics/reconnaissance|Reconnaissance]]

## Case Studies (3)

- [[frameworks/atlas/case-studies/camera-hijack-attack-on-facial-recognition-system|Camera Hijack Attack on Facial Recognition System]]
- [[frameworks/atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[frameworks/atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|ProKYC: Deepfake Tool for Account Fraud Attacks]]
