---
id: prokyc-deepfake-tool-for-account-fraud-attacks
title: "AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks"
sidebar_label: "ProKYC: Deepfake Tool for Account Fraud Attacks"
sidebar_position: 35
---

# AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks

## Summary

Cato CTRL security researchers have identified ProKYC, a deepfake tool being sold to cybercriminals as a method to bypass Know Your Customer (KYC) verification on financial service applications such as cryptocurrency exchanges. ProKYC can create fake identity documents and generate deepfake selfie videos, two key pieces of biometric data used during KYC verification. The tool helps cybercriminals defeat facial recognition and liveness checks to create fraudulent accounts.

The procedure below describes how a bad actor could use ProKYC’s service to bypass KYC verification.

## Metadata

- **Case Study ID:** AML.CS0034
- **Incident Date:** 2024
- **Type:** incident
- **Target:** KYC verification services
- **Actor:** ProKYC, cybercriminal group

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Gather Victim Identity Information

**Tactic:** [[reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[gather-victim-identity-information|AML.T0087: Gather Victim Identity Information]]

The bad actor collected user identity information.

### Step 2: Generative AI

**Tactic:** [[resource-development|AML.TA0003: Resource Development]]
**Technique:** [[generative-ai|AML.T0016.002: Generative AI]]

The bad actor paid for the ProKYC tool, created a fake identity document, generated a deepfake selfie video, and replaced a live camera feed with the deepfake video.

### Step 3: Generate Deepfakes

**Tactic:** [[ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[generate-deepfakes|AML.T0088: Generate Deepfakes]]

The bad actor used a mixture of real PII and falsified details with the ProKYC tool to generate a deepfaked identity document.

### Step 4: Generate Deepfakes

**Tactic:** [[ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[generate-deepfakes|AML.T0088: Generate Deepfakes]]

The bad actor used ProKYC tool to generate a deepfake selfie video with the same face as the identity document designed to bypass liveness checks.

### Step 5: Establish Accounts

**Tactic:** [[resource-development|AML.TA0003: Resource Development]]
**Technique:** [[establish-accounts|AML.T0021: Establish Accounts]]

The bad actor used the victim information to register an account with a financial services application, such as a cryptocurrency exchange.

### Step 6: AI-Enabled Product or Service

**Tactic:** [[ai-model-access|AML.TA0000: AI Model Access]]
**Technique:** [[ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

During identity verification, the financial services application used facial recognition and liveness detection to analyze live video from the user’s camera.

### Step 7: Evade AI Model

**Tactic:** [[initial-access|AML.TA0004: Initial Access]]
**Technique:** [[evade-ai-model|AML.T0015: Evade AI Model]]

The bad actor used ProKYC to replace the camera feed with the deepfake selfie video. This successfully evaded the KYC verification and allowed the bad actor to authenticate themselves under the false identity.

### Step 8: Impersonation

**Tactic:** [[defense-evasion|AML.TA0007: Defense Evasion]]
**Technique:** [[impersonation|AML.T0073: Impersonation]]

With an authenticated account under the victim’s identity, the bad actor successfully impersonated the victim and evaded detection.

### Step 9: Financial Harm

**Tactic:** [[impact|AML.TA0011: Impact]]
**Technique:** [[financial-harm|AML.T0048.000: Financial Harm]]

The bad actor used this access to cause financial harm to the victim.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[reconnaissance|AML.TA0002: Reconnaissance]] | [[gather-victim-identity-information|AML.T0087: Gather Victim Identity Information]] |
| 2 | [[resource-development|AML.TA0003: Resource Development]] | [[generative-ai|AML.T0016.002: Generative AI]] |
| 3 | [[ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[generate-deepfakes|AML.T0088: Generate Deepfakes]] |
| 4 | [[ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[generate-deepfakes|AML.T0088: Generate Deepfakes]] |
| 5 | [[resource-development|AML.TA0003: Resource Development]] | [[establish-accounts|AML.T0021: Establish Accounts]] |
| 6 | [[ai-model-access|AML.TA0000: AI Model Access]] | [[ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]] |
| 7 | [[initial-access|AML.TA0004: Initial Access]] | [[evade-ai-model|AML.T0015: Evade AI Model]] |
| 8 | [[defense-evasion|AML.TA0007: Defense Evasion]] | [[impersonation|AML.T0073: Impersonation]] |
| 9 | [[impact|AML.TA0011: Impact]] | [[financial-harm|AML.T0048.000: Financial Harm]] |



## External References

- AIID Incident 819: ProKYC Tool Allegedly Facilitates Deepfake-Based Account Fraud on Cryptocurrency Exchanges Available at: https://incidentdatabase.ai/cite/819/
- Cato CTRL Threat Research: ProKYC – Deepfake Tool for Account Fraud Attacks Available at: https://www.catonetworks.com/blog/prokyc-selling-deepfake-tool-for-account-fraud-attacks/
- ProKYC: Synthetic Identity Fraud as a Service Available at: https://idscan.net/blog/prokyc-synthetic-identity-fraud/


## References

MITRE Corporation. *ProKYC: Deepfake Tool for Account Fraud Attacks (AML.CS0034)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0034
