---
id: obtain-capabilities
title: "AML.T0016: Obtain Capabilities"
sidebar_label: "Obtain Capabilities"
sidebar_position: 4
---

# AML.T0016: Obtain Capabilities



Adversaries may search for and obtain software capabilities for use in their operations.
Capabilities may be specific to AI-based attacks [[atlas/techniques/resource-development/obtain-capabilities/adversarial-ai-attack-implementations|Adversarial AI Attack Implementations]] or generic software tools repurposed for malicious intent ([[atlas/techniques/resource-development/obtain-capabilities/software-tools|Software Tools]]). In both instances, an adversary may modify or customize the capability to aid in targeting a particular AI-enabled system.

## Metadata

- **Technique ID:** AML.T0016
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1588](https://attack.mitre.org/techniques/T1588/)


## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]



## Sub-Techniques (3)

- [[atlas/techniques/resource-development/obtain-capabilities/adversarial-ai-attack-implementations|AML.T0016.000: Adversarial AI Attack Implementations]]
- [[atlas/techniques/resource-development/obtain-capabilities/software-tools|AML.T0016.001: Software Tools]]
- [[atlas/techniques/resource-development/obtain-capabilities/generative-ai|AML.T0016.002: Generative AI]]


## Case Studies (1)


The following case studies demonstrate this technique:

### [[atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]

The researchers obtained [Virtual Camera: Live Assist](https://apkpure.com/virtual-camera-live-assist/virtual.camera.app), an Android app that allows a user to substitute the devices camera  with a video stream. This app works on genuine, non-rooted Android devices.



## References

MITRE Corporation. *Obtain Capabilities (AML.T0016)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0016
