---
id: exfiltration-via-inference-API-membership-inference
title: "AML.T0024.002: Extract AI Model"
sidebar_label: "Extract AI Model"
sidebar_position: 24003
---

# AML.T0024.002: Extract AI Model

Adversaries may extract a functional copy of a private model.
By repeatedly querying the victim's [AI Model Inference API Access](/techniques/AML.T0040), the adversary can collect the target model's inferences into a dataset.
The inferences are used as labels for training a separate model offline that will mimic the behavior and performance of the target model.

Adversaries may extract the model to avoid paying per query in an artificial-intelligence-as-a-service (AIaaS) setting.
Model extraction is used for [AI Intellectual Property Theft](/techniques/AML.T0048.004).

## Metadata

- **Technique ID:** AML.T0024.002
- **Created:** 2021-05-13
- **Last Modified:** 2025-12-23
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
