---
id: discover-ai-model-outputs
title: "AML.T0063: Discover AI Model Outputs"
sidebar_label: "Discover AI Model Outputs"
sidebar_position: 64
---

# AML.T0063: Discover AI Model Outputs

Adversaries may discover model outputs, such as class scores, whose presence is not required for the system to function and are not intended for use by the end user. Model outputs may be found in logs or may be included in API responses.
Model outputs may enable the adversary to identify weaknesses in the model and develop attacks.

## Metadata

- **Technique ID:** AML.T0063
- **Created:** 2025-03-12
- **Last Modified:** 2025-03-12
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/discovery|Discovery]]

## Mitigations (4)

- [[frameworks/atlas/mitigations/passive-ai-output-obfuscation|AML.M0002: Passive AI Output Obfuscation]] — Obfuscating model outputs can prevent adversaries from collecting sensitive information about the model outputs.
- [[frameworks/atlas/mitigations/encrypt-sensitive-information|AML.M0012: Encrypt Sensitive Information]] — Encrypting model outputs can prevent adversaries from discovering sensitive information about the AI-enabled system or its operations.
- [[frameworks/atlas/mitigations/ai-model-distribution-methods|AML.M0017: AI Model Distribution Methods]] — Avoiding the deployment of models to edge devices reduces an adversary's ability to collect sensitive information about the model outputs.
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-in-production|AML.M0019: Control Access to AI Models and Data in Production]] — Controlling access to the model in production can help prevent adversaries from inferring information from the model outputs.

## Case Studies (2)

- [[frameworks/atlas/case-studies/bypassing-cylance-s-ai-malware-detection|Bypassing Cylance's AI Malware Detection]]
- [[frameworks/atlas/case-studies/proofpoint-evasion|ProofPoint Evasion]]
