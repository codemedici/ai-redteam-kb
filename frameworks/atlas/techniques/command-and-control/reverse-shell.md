---
id: reverse-shell
title: "AML.T0072: Reverse Shell"
sidebar_label: "Reverse Shell"
sidebar_position: 1
---

# AML.T0072: Reverse Shell

Adversaries may utilize a reverse shell to communicate and control the victim system.

Typically, a user uses a client to connect to a remote machine which is listening for connections. With a reverse shell, the adversary is listening for incoming connections initiated from the victim system.

## Metadata

- **Technique ID:** AML.T0072
- **Created:** April 11, 2024
- **Last Modified:** April 14, 2025
- **Maturity:** realized

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

The Sliver implant grants the researcher a command and control channel so they can explore the victim's environment and continue the attack.

### [[frameworks/atlas/case-studies/malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]

The malicious payload was a reverse shell set to connect to a hardcoded IP address.

## References

MITRE Corporation. *Reverse Shell (AML.T0072)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0072
