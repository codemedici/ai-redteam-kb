---
id: ai-model-inference-api-access
title: "AML.T0040: AI Model Inference API Access"
sidebar_label: "AI Model Inference API Access"
sidebar_position: 41
---

# AML.T0040: AI Model Inference API Access

Adversaries may gain access to a model via legitimate access to the inference API.
Inference API access can be a source of information to the adversary ([Discover AI Model Ontology](/techniques/AML.T0013), [Discover AI Model Family](/techniques/AML.T0014)), a means of staging the attack ([Verify Attack](/techniques/AML.T0042), [Craft Adversarial Data](/techniques/AML.T0043)), or for introducing data to the target system for Impact ([Evade AI Model](/techniques/AML.T0015), [Erode AI Model Integrity](/techniques/AML.T0031)).

Many systems rely on the same models provided via an inference API, which means they share the same vulnerabilities. This is especially true of foundation models which are prohibitively resource intensive to train. Adversaries may use their access to model APIs to identify vulnerabilities such as jailbreaks or hallucinations and then target applications that use the same models.

## Metadata

- **Technique ID:** AML.T0040
- **Created:** 2021-05-13
- **Last Modified:** 2025-03-12
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/ai-model-access|AI Model Access]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-in-production|AML.M0019: Control Access to AI Models and Data in Production]] — Adversaries can use unrestricted API access to gain information about a production system, stage attacks, and introduce malicious data to the system.
- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Telemetry logging can help audit API usage of the model.

## Case Studies (6)

- [[frameworks/atlas/case-studies/attack-on-machine-translation-services|Attack on Machine Translation Services]]
- [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|Microsoft Azure Service Disruption]]
- [[frameworks/atlas/case-studies/microsoft-edge-ai-evasion|Microsoft Edge AI Evasion]]
- [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|Face Identification System Evasion via Physical Countermeasures]]
- [[frameworks/atlas/case-studies/chatgpt-package-hallucination|ChatGPT Package Hallucination]]
- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm: RAG-Based Attack]]
