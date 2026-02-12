---
id: phishing
title: "AML.T0052: Phishing"
sidebar_label: "Phishing"
sidebar_position: 9
---

# AML.T0052: Phishing



Adversaries may send phishing messages to gain access to victim systems. All forms of phishing are electronically delivered social engineering. Phishing can be targeted, known as spearphishing. In spearphishing, a specific individual, company, or industry will be targeted by the adversary. More generally, adversaries can conduct non-targeted phishing, such as in mass malware spam campaigns.

Generative AI, including LLMs that generate synthetic text, visual deepfakes of faces, and audio deepfakes of speech, is enabling adversaries to scale targeted phishing campaigns. LLMs can interact with users via text conversations and can be programmed with a meta prompt to phish for sensitive information. Deepfakes can be use in impersonation as an aid to phishing.

## Metadata

- **Technique ID:** AML.T0052
- **Created:** October 25, 2023
- **Last Modified:** December 20, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1566](https://attack.mitre.org/techniques/T1566/)


## Tactics (2)

This technique supports the following tactics:


- [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
- [[atlas/tactics/lateral-movement|AML.TA0015: Lateral Movement]]



## Sub-Techniques (1)

- [[atlas/techniques/initial-access/phishing/spearphishing-via-social-engineering-llm|AML.T0052.000: Spearphishing via Social Engineering LLM]]


## Case Studies (2)


The following case studies demonstrate this technique:

### [[atlas/case-studies/attempted-evasion-of-ml-phishing-webpage-detection-system|AML.CS0032: Attempted Evasion of ML Phishing Webpage Detection System]]

If the adversary can successfully evade detection, they can continue to operate their phishing websites and steal the victim's credentials.

### [[atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]

APT28 sent a phishing email from the compromised account with an attachment containing malware.



## References

MITRE Corporation. *Phishing (AML.T0052)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0052
