---
id: ai-model-inference-api-access
title: "AML.T0040: AI Model Inference API Access"
sidebar_label: "AI Model Inference API Access"
sidebar_position: 1
---

# AML.T0040: AI Model Inference API Access

Adversaries may gain access to a model via legitimate access to the inference API.
Inference API access can be a source of information to the adversary ([[frameworks/atlas/techniques/discovery/discover-ai-model-ontology|Discover AI Model Ontology]], [[frameworks/atlas/techniques/discovery/discover-ai-model-family|Discover AI Model Family]]), a means of staging the attack ([[frameworks/atlas/techniques/ai-attack-staging/verify-attack|Verify Attack]], [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|Craft Adversarial Data]]), or for introducing data to the target system for Impact ([[frameworks/atlas/techniques/initial-access/evade-ai-model|Evade AI Model]], [[frameworks/atlas/techniques/impact/erode-ai-model-integrity|Erode AI Model Integrity]]).

Many systems rely on the same models provided via an inference API, which means they share the same vulnerabilities. This is especially true of foundation models which are prohibitively resource intensive to train. Adversaries may use their access to model APIs to identify vulnerabilities such as jailbreaks or hallucinations and then target applications that use the same models.

## Metadata

- **Technique ID:** AML.T0040
- **Created:** May 13, 2021
- **Last Modified:** March 12, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (6)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]

They abused a public facing application to query the model and produced machine translated sentence pairs as training data.

### [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]

The team used an exposed API to access the target model.

### [[frameworks/atlas/case-studies/microsoft-edge-ai-evasion|AML.CS0011: Microsoft Edge AI Evasion]]

Using the publicly available version of the ML model, the team started sending queries and analyzing the responses (inferences) from the ML model.

### [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]

The team accessed the inference API of the target model.

### [[frameworks/atlas/case-studies/chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]

The researchers use the public ChatGPT API throughout this exercise.

### [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|AML.CS0024: Morris II Worm: RAG-Based Attack]]

The researchers use access to the publicly available GenAI model API that powers the target RAG-based email system.

## References

MITRE Corporation. *AI Model Inference API Access (AML.T0040)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0040
