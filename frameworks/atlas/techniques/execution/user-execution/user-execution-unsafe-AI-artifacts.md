---
id: user-execution-unsafe-AI-artifacts
title: "AML.T0011.000: Unsafe AI Artifacts"
sidebar_label: "Unsafe AI Artifacts"
sidebar_position: 11001
---

# AML.T0011.000: Unsafe AI Artifacts

Adversaries may develop unsafe AI artifacts that when executed have a deleterious effect.
The adversary can use this technique to establish persistent access to systems.
These models may be introduced via a [AI Supply Chain Compromise](/techniques/AML.T0010).

Serialization of models is a popular technique for model storage, transfer, and loading.
However, this format without proper checking presents an opportunity for code execution.

## Metadata

- **Technique ID:** AML.T0011.000
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** realized

## Parent Technique

**Parent Technique:** AML.T0011 — User Execution

## Tactics (1)

- [[frameworks/atlas/tactics/execution|Execution]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|Organization Confusion on Hugging Face]]
- [[frameworks/atlas/case-studies/malicious-models-on-hugging-face|Malicious Models on Hugging Face]]
