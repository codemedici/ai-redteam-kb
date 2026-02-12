---
id: establish-accounts
title: "AML.T0021: Establish Accounts"
sidebar_label: "Establish Accounts"
sidebar_position: 14
---

# AML.T0021: Establish Accounts



Adversaries may create accounts with various services for use in targeting, to gain access to resources needed in [[atlas/tactics/ai-attack-staging|AI Attack Staging]], or for victim impersonation.

## Metadata

- **Technique ID:** AML.T0021
- **Created:** January 24, 2022
- **Last Modified:** January 18, 2023
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1585](https://attack.mitre.org/techniques/T1585/)


## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]




## Case Studies (5)


The following case studies demonstrate this technique:

### [[atlas/case-studies/camera-hijack-attack-on-facial-recognition-system|AML.CS0004: Camera Hijack Attack on Facial Recognition System]]

The attackers used the victim identity information to register new accounts in the tax system.

### [[atlas/case-studies/clearviewai-misconfiguration|AML.CS0006: ClearviewAI Misconfiguration]]

A security researcher gained initial access to Clearview AI's private code repository via a misconfigured server setting that allowed an arbitrary user to register a valid account.

### [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

The researcher registered an unverified "organization" account on Hugging Face that squats on the namespace of a targeted company.

### [[atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]

The researchers used the gathered victim information to register an account for a financial services application.

### [[atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]

The bad actor used the victim information to register an account with a financial services application, such as a cryptocurrency exchange.



## References

MITRE Corporation. *Establish Accounts (AML.T0021)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0021
