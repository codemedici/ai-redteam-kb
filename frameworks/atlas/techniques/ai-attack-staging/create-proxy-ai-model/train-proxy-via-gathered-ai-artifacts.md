---
id: create-proxy-ai-model-train-proxy-via-gathered-ai-artifacts
title: "AML.T0005.000: Train Proxy via Gathered AI Artifacts"
sidebar_label: "Train Proxy via Gathered AI Artifacts"
sidebar_position: 2
---

# AML.T0005.000: Train Proxy via Gathered AI Artifacts

> **Sub-Technique of:** [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-ai-model|AML.T0005: Create Proxy AI Model]]

Proxy models may be trained from AI artifacts (such as data, model architectures, and pre-trained models) that are representative of the target model gathered by the adversary.
This can be used to develop attacks that require higher levels of access than the adversary has available or as a means to validate pre-existing attacks without interacting with the target model.

## Metadata

- **Technique ID:** AML.T0005.000
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** demonstrated

- **Parent Technique:** [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-ai-model|AML.T0005: Create Proxy AI Model]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (1)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/gpt-2-model-replication|AML.CS0007: GPT-2 Model Replication]]

The researchers modified Grover's objective function to reflect GPT-2's objective function and then trained on the dataset they curated using used Grover's initial hyperparameters. The resulting model functionally replicates GPT-2, obtaining similar performance on most datasets.
A bad actor who followed the same procedure as the researchers could then use the replicated GPT-2 model for malicious purposes.

## References

MITRE Corporation. *Train Proxy via Gathered AI Artifacts (AML.T0005.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0005.000
