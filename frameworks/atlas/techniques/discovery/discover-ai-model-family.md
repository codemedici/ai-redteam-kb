---
id: discover-ai-model-family
title: "AML.T0014: Discover AI Model Family"
sidebar_label: "Discover AI Model Family"
sidebar_position: 15
---

# AML.T0014: Discover AI Model Family

Adversaries may discover the general family of model.
General information about the model may be revealed in documentation, or the adversary may use carefully constructed examples and analyze the model's responses to categorize it.

Knowledge of the model family can help the adversary identify means of attacking the model and help tailor the attack.

## Metadata

- **Technique ID:** AML.T0014
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** feasible

## Tactics (1)

- [[frameworks/atlas/tactics/discovery|Discovery]]

## Mitigations (3)

- [[frameworks/atlas/mitigations/passive-ai-output-obfuscation|AML.M0002: Passive AI Output Obfuscation]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Limit the amount of information an attacker can learn about a model's ontology through API queries.
- [[frameworks/atlas/mitigations/use-ensemble-methods|AML.M0006: Use Ensemble Methods]] — Use multiple different models to fool adversaries of which type of model is used and how the model used.
