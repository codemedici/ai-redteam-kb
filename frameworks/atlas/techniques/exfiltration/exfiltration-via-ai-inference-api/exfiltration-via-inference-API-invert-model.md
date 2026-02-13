---
id: exfiltration-via-inference-API-invert-model
title: "AML.T0024.001: Invert AI Model"
sidebar_label: "Invert AI Model"
sidebar_position: 24002
---

# AML.T0024.001: Invert AI Model

AI models' training data could be reconstructed by exploiting the confidence scores that are available via an inference API.
By querying the inference API strategically, adversaries can back out potentially private information embedded within the training data.
This could lead to privacy violations if the attacker can reconstruct the data of sensitive features used in the algorithm.

## Metadata

- **Technique ID:** AML.T0024.001
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** feasible

## Parent Technique

**Parent Technique:** AML.T0024 — Exfiltration via AI Inference API

## Tactics (1)

- [[frameworks/atlas/tactics/exfiltration|Exfiltration]]

## Mitigations (3)

- [[frameworks/atlas/mitigations/passive-ai-output-obfuscation|AML.M0002: Passive AI Output Obfuscation]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Limit the volume of API queries in a given period of time to regulate the amount and fidelity of potentially sensitive information an attacker can learn.
- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Telemetry logging can help identify if sensitive data has been exfiltrated.
