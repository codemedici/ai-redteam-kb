---
id: discover-ai-artifacts
title: "AML.T0007: Discover AI Artifacts"
sidebar_label: "Discover AI Artifacts"
sidebar_position: 8
---

# AML.T0007: Discover AI Artifacts

Adversaries may search private sources to identify AI learning artifacts that exist on the system and gather information about them.
These artifacts can include the software stack used to train and deploy models, training and testing data management systems, container registries, software repositories, and model zoos.

This information can be used to identify targets for further collection, exfiltration, or disruption, and to tailor and improve attacks.

## Metadata

- **Technique ID:** AML.T0007
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/discovery|Discovery]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-at-rest|AML.M0005: Control Access to AI Models and Data at Rest]] — Access controls can limit an adversary's ability to identify AI models, datasets, and other artifacts on a system.
- [[frameworks/atlas/mitigations/encrypt-sensitive-information|AML.M0012: Encrypt Sensitive Information]] — Encrypting AI artifacts can protect against adversary attempts to discover sensitive information.

## Case Studies (2)

- [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|Organization Confusion on Hugging Face]]
- [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AI Model Tampering via Supply Chain Attack]]
