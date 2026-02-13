---
id: obtain-capabilities-software-tools
title: "AML.T0016.001: Software Tools"
sidebar_label: "Software Tools"
sidebar_position: 6
---

# AML.T0016.001: Software Tools

> **Sub-Technique of:** [[frameworks/atlas/techniques/resource-development/obtain-capabilities|AML.T0016: Obtain Capabilities]]

Adversaries may search for and obtain software tools to support their operations.
Software designed for legitimate use may be repurposed by an adversary for malicious intent.
An adversary may modify or customize software tools to achieve their purpose.
Software tools used to support attacks on AI systems are not necessarily AI-based themselves.

## Metadata

- **Technique ID:** AML.T0016.001
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1588.002](https://attack.mitre.org/techniques/T1588/002/)
- **Parent Technique:** [[frameworks/atlas/techniques/resource-development/obtain-capabilities|AML.T0016: Obtain Capabilities]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (3)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/camera-hijack-attack-on-facial-recognition-system|AML.CS0004: Camera Hijack Attack on Facial Recognition System]]

The attackers obtained customized Android ROMs and a virtual camera application.

### [[frameworks/atlas/case-studies/llm-jacking|AML.CS0030: LLM Jacking]]

The adversaries obtained [keychecker](https://github.com/cunnymessiah/keychecker), a bulk key checker for various AI services which is capable of testing if the key is valid and retrieving some attributes of the account (e.g. account balance and available models).

### [[frameworks/atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]

The researchers obtained [Open Broadcaster Software (OBS)](https://obsproject.com)which can broadcast a video stream over the network.

## References

MITRE Corporation. *Software Tools (AML.T0016.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0016.001
