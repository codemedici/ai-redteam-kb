---
id: impersonation
title: "AML.T0073: Impersonation"
sidebar_label: "Impersonation"
sidebar_position: 5
---

# AML.T0073: Impersonation

Adversaries may impersonate a trusted person or organization in order to persuade and trick a target into performing some action on their behalf. For example, adversaries may communicate with victims (via [[atlas/techniques/initial-access/phishing/phishing-overview|Phishing]], or [[atlas/techniques/initial-access/phishing/spearphishing-via-social-engineering-llm|Spearphishing via Social Engineering LLM]]) while impersonating a known sender such as an executive, colleague, or third-party vendor. Established trust can then be leveraged to accomplish an adversary's ultimate goals, possibly against multiple victims.

Adversaries may target resources that are part of the AI DevOps lifecycle, such as model repositories, container registries, and software registries.

## Metadata

- **Technique ID:** AML.T0073
- **Created:** April 14, 2025
- **Last Modified:** April 14, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1656](https://attack.mitre.org/techniques/T1656/)

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (4)

The following case studies demonstrate this technique:

### [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

Employees of the targeted company found and joined the fake Hugging Face organization. Since the organization account name is matches or appears to match the real organization, the employees were fooled into believing the account was official.

### [[atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]

With an authenticated account under the victim’s identity, the researchers successfully impersonate the victim and evade detection.

### [[atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]

With an authenticated account under the victim’s identity, the bad actor successfully impersonated the victim and evaded detection.

### [[atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]

The email impersonated a government ministry representative.

## References

MITRE Corporation. *Impersonation (AML.T0073)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0073
