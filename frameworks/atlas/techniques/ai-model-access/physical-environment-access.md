---
id: physical-environment-access
title: "AML.T0041: Physical Environment Access"
sidebar_label: "Physical Environment Access"
sidebar_position: 42
---

# AML.T0041: Physical Environment Access

In addition to the attacks that take place purely in the digital domain, adversaries may also exploit the physical environment for their attacks.
If the model is interacting with data collected from the real world in some way, the adversary can influence the model through access to wherever the data is being collected.
By modifying the data in the collection process, the adversary can perform modified versions of attacks designed for digital access.

## Metadata

- **Technique ID:** AML.T0041
- **Created:** 2021-05-13
- **Last Modified:** 2021-05-13
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/ai-model-access|AI Model Access]]

## Mitigations (1)

- [[frameworks/atlas/mitigations/use-multi-modal-sensors|AML.M0009: Use Multi-Modal Sensors]] — Using a variety of sensors can make it more difficult for an attacker with physical access to compromise and produce malicious results.

## Case Studies (2)

- [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|Face Identification System Evasion via Physical Countermeasures]]
- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
