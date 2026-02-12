---
id: use-alternate-authentication-material
title: "AML.T0091: Use Alternate Authentication Material"
sidebar_label: "Use Alternate Authentication Material"
sidebar_position: 1
---

# AML.T0091: Use Alternate Authentication Material



Adversaries may use alternate authentication material, such as password hashes, Kerberos tickets, and application access tokens, in order to move laterally within an environment and bypass normal system access controls.

AI services commonly use alternate authentication material as a primary means for users to make queries, making them vulnerable to this technique.

## Metadata

- **Technique ID:** AML.T0091
- **Created:** October 27, 2025
- **Last Modified:** November 4, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1550](https://attack.mitre.org/techniques/T1550/)


## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/lateral-movement|AML.TA0015: Lateral Movement]]



## Sub-Techniques (1)

- [[atlas/techniques/lateral-movement/use-alternate-authentication-material/application-access-token|AML.T0091.000: Application Access Token]]


## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *Use Alternate Authentication Material (AML.T0091)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0091
