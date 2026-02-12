---
id: user-execution-unsafe-ai-artifacts
title: "AML.T0011.000: Unsafe AI Artifacts"
sidebar_label: "Unsafe AI Artifacts"
sidebar_position: 2
---

# AML.T0011.000: Unsafe AI Artifacts

> **Sub-Technique of:** [[atlas/techniques/execution/user-execution/user-execution-overview|AML.T0011: User Execution]]

Adversaries may develop unsafe AI artifacts that when executed have a deleterious effect.
The adversary can use this technique to establish persistent access to systems.
These models may be introduced via a [[atlas/techniques/initial-access/ai-supply-chain-compromise/ai-supply-chain-compromise-overview|AI Supply Chain Compromise]].

Serialization of models is a popular technique for model storage, transfer, and loading.
However, this format without proper checking presents an opportunity for code execution.

## Metadata

- **Technique ID:** AML.T0011.000
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized

- **Parent Technique:** [[atlas/techniques/execution/user-execution/user-execution-overview|AML.T0011: User Execution]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (2)

The following case studies demonstrate this technique:

### [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

When any future user loads the model, the model automatically executes the adversary's payload.

### [[atlas/case-studies/malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]

If a user loaded the malicious model, the adversary's malicious payload is executed.

## References

MITRE Corporation. *Unsafe AI Artifacts (AML.T0011.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0011.000
