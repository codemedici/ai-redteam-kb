---
id: gather-victim-identity-information
title: "AML.T0087: Gather Victim Identity Information"
sidebar_label: "Gather Victim Identity Information"
sidebar_position: 10
---

# AML.T0087: Gather Victim Identity Information



Adversaries may gather information about the victim's identity that can be used during targeting. Information about identities may include a variety of details, including personal data (ex: employee names, email addresses, photos, etc.) as well as sensitive details such as credentials or multi-factor authentication (MFA) configurations.

Adversaries may gather this information in various ways, such as direct elicitation, [[atlas/techniques/reconnaissance/search-victim-owned-websites|Search Victim-Owned Websites]], or via leaked information on the black market.

Adversaries may use the gathered victim data to Create Deepfakes and impersonate them in a convincing manner. This may create opportunities for adversaries to [[atlas/techniques/resource-development/establish-accounts|Establish Accounts]] under the impersonated identity, or allow them to perform convincing [[atlas/techniques/initial-access/phishing/phishing-overview|Phishing]] attacks.

## Metadata

- **Technique ID:** AML.T0087
- **Created:** October 31, 2025
- **Last Modified:** October 27, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1589](https://attack.mitre.org/techniques/T1589/)


## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]




## Case Studies (3)


The following case studies demonstrate this technique:

### [[atlas/case-studies/camera-hijack-attack-on-facial-recognition-system|AML.CS0004: Camera Hijack Attack on Facial Recognition System]]

The attackers collected user identity information and high-definition face photos from an online black market.

### [[atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]

The researchers collected user identity information and high-definition facial images from online social networks and/or black-market sites.

### [[atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]

The bad actor collected user identity information.



## References

MITRE Corporation. *Gather Victim Identity Information (AML.T0087)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0087
