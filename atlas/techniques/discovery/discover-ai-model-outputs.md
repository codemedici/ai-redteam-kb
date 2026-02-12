---
id: discover-ai-model-outputs
title: "AML.T0063: Discover AI Model Outputs"
sidebar_label: "Discover AI Model Outputs"
sidebar_position: 5
---

# AML.T0063: Discover AI Model Outputs

Adversaries may discover model outputs, such as class scores, whose presence is not required for the system to function and are not intended for use by the end user. Model outputs may be found in logs or may be included in API responses.
Model outputs may enable the adversary to identify weaknesses in the model and develop attacks.

## Metadata

- **Technique ID:** AML.T0063
- **Created:** March 12, 2025
- **Last Modified:** March 12, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (2)

The following case studies demonstrate this technique:

### [[atlas/case-studies/bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]

The researchers enabled verbose logging, which exposes the inner workings of the ML model, specifically around reputation scoring and model ensembling.

### [[atlas/case-studies/proofpoint-evasion|AML.CS0008: ProofPoint Evasion]]

The researchers discovered that ProofPoint's Email Protection left model output scores in email headers.

## References

MITRE Corporation. *Discover AI Model Outputs (AML.T0063)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0063
