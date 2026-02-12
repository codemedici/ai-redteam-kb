---
id: masquerading
title: "AML.T0074: Masquerading"
sidebar_label: "Masquerading"
sidebar_position: 6
---

# AML.T0074: Masquerading

Adversaries may attempt to manipulate features of their artifacts to make them appear legitimate or benign to users and/or security tools. Masquerading occurs when the name or location of an object, legitimate or malicious, is manipulated or abused for the sake of evading defenses and observation. This may include manipulating file metadata, tricking users into misidentifying the file type, and giving legitimate task or service names.

## Metadata

- **Technique ID:** AML.T0074
- **Created:** April 14, 2025
- **Last Modified:** April 14, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1036](https://attack.mitre.org/techniques/T1036/)

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (2)

The following case studies demonstrate this technique:

### [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

The researcher named the Sliver process `training.bin` to disguise it as a legitimate model training process. Furthermore, the model still operates as normal, making it less likely a user will notice something is wrong.

### [[atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]

The attachment was called “Appendix.pdf.zip” which could confuse the recipient into thinking it was a legitimate PDF file.

## References

MITRE Corporation. *Masquerading (AML.T0074)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0074
