---
id: exfiltration-via-ai-inference-api-invert-ai-model
title: "AML.T0024.001: Invert AI Model"
sidebar_label: "Invert AI Model"
sidebar_position: 3
---

# AML.T0024.001: Invert AI Model

AI models' training data could be reconstructed by exploiting the confidence scores that are available via an inference API.
By querying the inference API strategically, adversaries can back out potentially private information embedded within the training data.
This could lead to privacy violations if the attacker can reconstruct the data of sensitive features used in the algorithm.

## Metadata

- **Technique ID:** AML.T0024.001
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** feasible

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *Invert AI Model (AML.T0024.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0024.001
