---
id: exfiltration-via-ai-inference-api-extract-ai-model
title: "AML.T0024.002: Extract AI Model"
sidebar_label: "Extract AI Model"
sidebar_position: 4
---

# AML.T0024.002: Extract AI Model

Adversaries may extract a functional copy of a private model.
By repeatedly querying the victim's [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AI Model Inference API Access]], the adversary can collect the target model's inferences into a dataset.
The inferences are used as labels for training a separate model offline that will mimic the behavior and performance of the target model.

Adversaries may extract the model to avoid paying per query in an artificial-intelligence-as-a-service (AIaaS) setting.
Model extraction is used for [[frameworks/atlas/techniques/impact/external-harms/external-harms-AI-IP-theft|AI Intellectual Property Theft]].

## Metadata

- **Technique ID:** AML.T0024.002
- **Created:** May 13, 2021
- **Last Modified:** December 23, 2025
- **Maturity:** feasible

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *Extract AI Model (AML.T0024.002)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0024.002
