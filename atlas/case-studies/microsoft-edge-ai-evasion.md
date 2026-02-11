---
id: microsoft-edge-ai-evasion
title: "AML.CS0011: Microsoft Edge AI Evasion"
sidebar_label: "Microsoft Edge AI Evasion"
sidebar_position: 12
---

# AML.CS0011: Microsoft Edge AI Evasion

## Summary

The Azure Red Team performed a red team exercise on a new Microsoft product designed for running AI workloads at the edge. This exercise was meant to use an automated system to continuously manipulate a target image to cause the ML model to produce misclassifications.

## Metadata

- **Case Study ID:** AML.CS0011
- **Incident Date:** 2020
- **Type:** exercise
- **Target:** New Microsoft AI Product
- **Actor:** Azure Red Team

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Search Open Technical Databases

**Tactic:** [[reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[search-open-technical-databases|AML.T0000: Search Open Technical Databases]]

The team first performed reconnaissance to gather information about the target ML model.

### Step 2: Acquire Public AI Artifacts

**Tactic:** [[resource-development|AML.TA0003: Resource Development]]
**Technique:** [[acquire-public-ai-artifacts|AML.T0002: Acquire Public AI Artifacts]]

The team identified and obtained the publicly available base model to use against the target ML model.

### Step 3: AI Model Inference API Access

**Tactic:** [[ai-model-access|AML.TA0000: AI Model Access]]
**Technique:** [[ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

Using the publicly available version of the ML model, the team started sending queries and analyzing the responses (inferences) from the ML model.

### Step 4: Black-Box Optimization

**Tactic:** [[ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[black-box-optimization|AML.T0043.001: Black-Box Optimization]]

The red team created an automated system that continuously manipulated an original target image, that tricked the ML model into producing incorrect inferences, but the perturbations in the image were unnoticeable to the human eye.

### Step 5: Evade AI Model

**Tactic:** [[impact|AML.TA0011: Impact]]
**Technique:** [[evade-ai-model|AML.T0015: Evade AI Model]]

Feeding this perturbed image, the red team was able to evade the ML model by causing misclassifications.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[reconnaissance|AML.TA0002: Reconnaissance]] | [[search-open-technical-databases|AML.T0000: Search Open Technical Databases]] |
| 2 | [[resource-development|AML.TA0003: Resource Development]] | [[acquire-public-ai-artifacts|AML.T0002: Acquire Public AI Artifacts]] |
| 3 | [[ai-model-access|AML.TA0000: AI Model Access]] | [[ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]] |
| 4 | [[ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[black-box-optimization|AML.T0043.001: Black-Box Optimization]] |
| 5 | [[impact|AML.TA0011: Impact]] | [[evade-ai-model|AML.T0015: Evade AI Model]] |




## References

MITRE Corporation. *Microsoft Edge AI Evasion (AML.CS0011)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0011
