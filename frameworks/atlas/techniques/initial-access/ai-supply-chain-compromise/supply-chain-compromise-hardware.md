---
id: supply-chain-compromise-hardware
title: "AML.T0010.003: Model"
sidebar_label: "Model"
sidebar_position: 10004
---

# AML.T0010.003: Model

AI-enabled systems often rely on open sourced models in various ways.
Most commonly, the victim organization may be using these models for fine tuning.
These models will be downloaded from an external source and then used as the base for the model as it is tuned on a smaller, private dataset.
Loading models often requires executing some saved code in the form of a saved model file.
These can be compromised with traditional malware, or through some adversarial AI techniques.

## Metadata

- **Technique ID:** AML.T0010.003
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** realized

## Parent Technique

**Parent Technique:** AML.T0010 — AI Supply Chain Compromise

## Tactics (1)

- [[frameworks/atlas/tactics/initial-access|Initial Access]]

## Mitigations (5)

- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-at-rest|AML.M0005: Control Access to AI Models and Data at Rest]] — Access controls can prevent tampering with ML artifacts and prevent unauthorized copying.
- [[frameworks/atlas/mitigations/use-ensemble-methods|AML.M0006: Use Ensemble Methods]] — Using multiple different models ensures minimal performance loss if security flaw is found in tool for one model or family.
- [[frameworks/atlas/mitigations/validate-ai-model|AML.M0008: Validate AI Model]] — Ensure that acquired models do not respond to potential backdoor triggers or adversarial influence.
- [[frameworks/atlas/mitigations/code-signing|AML.M0013: Code Signing]] — Enforce properly signed model files.
- [[frameworks/atlas/mitigations/ai-model-distribution-methods|AML.M0017: AI Model Distribution Methods]] — An adversary could repackage the application with a malicious version of the model.

## Case Studies (4)

- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[frameworks/atlas/case-studies/poisongpt|PoisonGPT]]
- [[frameworks/atlas/case-studies/shadowray|ShadowRay]]
- [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|Organization Confusion on Hugging Face]]
