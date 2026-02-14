---
id: microsoft-edge-ai-evasion
title: "AML.CS0011: Microsoft Edge AI Evasion"
type: case-study
sidebar_position: 12
---

# AML.CS0011: Microsoft Edge AI Evasion

The Azure Red Team performed a red team exercise on a new Microsoft product designed for running AI workloads at the edge. This exercise was meant to use an automated system to continuously manipulate a target image to cause the ML model to produce misclassifications.

## Metadata

- **ID:** AML.CS0011
- **Incident Date:** 2020-02-01
- **Type:** exercise
- **Target:** New Microsoft AI Product
- **Actor:** Azure Red Team

## Procedure

### Step 1: Search Open Technical Databases

**Technique:** [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/search-open-tech-db-journals-conferences|AML.T0000: Search Open Technical Databases]]

The team first performed reconnaissance to gather information about the target ML model.

### Step 2: Acquire Public AI Artifacts

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-datasets|AML.T0002: Acquire Public AI Artifacts]]

The team identified and obtained the publicly available base model to use against the target ML model.

### Step 3: AI Model Inference API Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

Using the publicly available version of the ML model, the team started sending queries and analyzing the responses (inferences) from the ML model.

### Step 4: Black-Box Optimization

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001: Black-Box Optimization]]

The red team created an automated system that continuously manipulated an original target image, that tricked the ML model into producing incorrect inferences, but the perturbations in the image were unnoticeable to the human eye.

### Step 5: Evade AI Model

**Technique:** [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

Feeding this perturbed image, the red team was able to evade the ML model by causing misclassifications.

