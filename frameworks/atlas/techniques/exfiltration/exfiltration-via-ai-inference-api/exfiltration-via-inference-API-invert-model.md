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
