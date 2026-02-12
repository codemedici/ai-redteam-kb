---
id: discover-ai-artifacts
title: "AML.T0007: Discover AI Artifacts"
sidebar_label: "Discover AI Artifacts"
sidebar_position: 3
---

# AML.T0007: Discover AI Artifacts

Adversaries may search private sources to identify AI learning artifacts that exist on the system and gather information about them.
These artifacts can include the software stack used to train and deploy models, training and testing data management systems, container registries, software repositories, and model zoos.

This information can be used to identify targets for further collection, exfiltration, or disruption, and to tailor and improve attacks.

## Metadata

- **Technique ID:** AML.T0007
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (2)

The following case studies demonstrate this technique:

### [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

The researcher could have searched for AI models in the victim organization's environment.

### [[atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]

The researchers found 1,453 unique AI models embedded in the private container images. Around half were in the Open Neural Network Exchange (ONNX) format.

## References

MITRE Corporation. *Discover AI Artifacts (AML.T0007)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0007
