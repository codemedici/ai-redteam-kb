---
id: discover-ai-model-ontology
title: "AML.T0013: Discover AI Model Ontology"
sidebar_label: "Discover AI Model Ontology"
sidebar_position: 14
---

# AML.T0013: Discover AI Model Ontology

Adversaries may discover the ontology of an AI model's output space, for example, the types of objects a model can detect.
The adversary may discovery the ontology by repeated queries to the model, forcing it to enumerate its output space.
Or the ontology may be discovered in a configuration file or in documentation about the model.

The model ontology helps the adversary understand how the model is being used by the victim.
It is useful to the adversary in creating targeted attacks.

## Metadata

- **Technique ID:** AML.T0013
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/discovery|Discovery]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/passive-ai-output-obfuscation|AML.M0002: Passive AI Output Obfuscation]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Limit the amount of information an attacker can learn about a model's ontology through API queries.

## Case Studies (1)

- [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|Face Identification System Evasion via Physical Countermeasures]]
