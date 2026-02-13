---
id: face-identification-system-evasion-via-physical-countermeasures
title: "AML.CS0012: Face Identification System Evasion via Physical Countermeasures"
sidebar_label: "Face Identification System Evasion via Physical Countermeasures"
sidebar_position: 13
---

# AML.CS0012: Face Identification System Evasion via Physical Countermeasures

## Summary

MITRE's AI Red Team demonstrated a physical-domain evasion attack on a commercial face identification service with the intention of inducing a targeted misclassification.
This operation had a combination of traditional MITRE ATT&CK techniques such as finding valid accounts and executing code via an API - all interleaved with adversarial ML specific attacks.

## Metadata

- **Case Study ID:** AML.CS0012
- **Incident Date:** 2020
- **Type:** exercise
- **Target:** Commercial Face Identification Service
- **Actor:** MITRE AI Red Team

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Search Open Technical Databases

**Technique:** [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/search-open-technical-databases|AML.T0000: Search Open Technical Databases]]

The team first performed reconnaissance to gather information about the target ML model.

### Step 2: Valid Accounts

**Technique:** [[frameworks/atlas/techniques/initial-access/valid-accounts|AML.T0012: Valid Accounts]]

The team gained access to the commercial face identification service and its API through a valid account.

### Step 3: AI Model Inference API Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

The team accessed the inference API of the target model.

### Step 4: Discover AI Model Ontology

**Technique:** [[frameworks/atlas/techniques/discovery/discover-ai-model-ontology|AML.T0013: Discover AI Model Ontology]]

The team identified the list of identities targeted by the model by querying the target model's inference API.

### Step 5: Datasets

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/datasets|AML.T0002.000: Datasets]]

The team acquired representative open source data.

### Step 6: Create Proxy AI Model

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-ai-model|AML.T0005: Create Proxy AI Model]]

The team developed a proxy model using the open source data.

### Step 7: White-Box Optimization

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/white-box-optimization|AML.T0043.000: White-Box Optimization]]

Using the proxy model, the red team optimized adversarial visual patterns as a physical domain patch-based attack using expectation over transformation.

### Step 8: Physical Countermeasures

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/physical-countermeasures|AML.T0008.003: Physical Countermeasures]]

The team printed the optimized patch.

### Step 9: Physical Environment Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/physical-environment-access|AML.T0041: Physical Environment Access]]

The team placed the countermeasure in the physical environment to cause issues in the face identification system.

### Step 10: Evade AI Model

**Technique:** [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

The team successfully evaded the model using the physical countermeasure by causing targeted misclassifications.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/search-open-technical-databases|AML.T0000: Search Open Technical Databases]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/initial-access/valid-accounts|AML.T0012: Valid Accounts]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/discovery/discover-ai-model-ontology|AML.T0013: Discover AI Model Ontology]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/datasets|AML.T0002.000: Datasets]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-ai-model|AML.T0005: Create Proxy AI Model]]

**Step 7:**
- Technique: [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/white-box-optimization|AML.T0043.000: White-Box Optimization]]

**Step 8:**
- Technique: [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/physical-countermeasures|AML.T0008.003: Physical Countermeasures]]

**Step 9:**
- Technique: [[frameworks/atlas/techniques/ai-model-access/physical-environment-access|AML.T0041: Physical Environment Access]]

**Step 10:**
- Technique: [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

## References

MITRE Corporation. *Face Identification System Evasion via Physical Countermeasures (AML.CS0012)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0012
