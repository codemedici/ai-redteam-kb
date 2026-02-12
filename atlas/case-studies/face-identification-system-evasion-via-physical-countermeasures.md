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

**Tactic:** [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[atlas/techniques/reconnaissance/search-open-technical-databases/search-open-technical-databases-overview|AML.T0000: Search Open Technical Databases]]

The team first performed reconnaissance to gather information about the target ML model.

### Step 2: Valid Accounts

**Tactic:** [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
**Technique:** [[atlas/techniques/initial-access/valid-accounts|AML.T0012: Valid Accounts]]

The team gained access to the commercial face identification service and its API through a valid account.

### Step 3: AI Model Inference API Access

**Tactic:** [[atlas/tactics/ai-model-access|AML.TA0000: AI Model Access]]
**Technique:** [[atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

The team accessed the inference API of the target model.

### Step 4: Discover AI Model Ontology

**Tactic:** [[atlas/tactics/discovery|AML.TA0008: Discovery]]
**Technique:** [[atlas/techniques/discovery/discover-ai-model-ontology|AML.T0013: Discover AI Model Ontology]]

The team identified the list of identities targeted by the model by querying the target model's inference API.

### Step 5: Datasets

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/datasets|AML.T0002.000: Datasets]]

The team acquired representative open source data.

### Step 6: Create Proxy AI Model

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-ai-model-overview|AML.T0005: Create Proxy AI Model]]

The team developed a proxy model using the open source data.

### Step 7: White-Box Optimization

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/ai-attack-staging/craft-adversarial-data/white-box-optimization|AML.T0043.000: White-Box Optimization]]

Using the proxy model, the red team optimized adversarial visual patterns as a physical domain patch-based attack using expectation over transformation.

### Step 8: Physical Countermeasures

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/acquire-infrastructure/physical-countermeasures|AML.T0008.003: Physical Countermeasures]]

The team printed the optimized patch.

### Step 9: Physical Environment Access

**Tactic:** [[atlas/tactics/ai-model-access|AML.TA0000: AI Model Access]]
**Technique:** [[atlas/techniques/ai-model-access/physical-environment-access|AML.T0041: Physical Environment Access]]

The team placed the countermeasure in the physical environment to cause issues in the face identification system.

### Step 10: Evade AI Model

**Tactic:** [[atlas/tactics/impact|AML.TA0011: Impact]]
**Technique:** [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

The team successfully evaded the model using the physical countermeasure by causing targeted misclassifications.


## Tactics and Techniques Used


**Step 1:**
- Tactic: [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]
- Technique: [[atlas/techniques/reconnaissance/search-open-technical-databases/search-open-technical-databases-overview|AML.T0000: Search Open Technical Databases]]

**Step 2:**
- Tactic: [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
- Technique: [[atlas/techniques/initial-access/valid-accounts|AML.T0012: Valid Accounts]]

**Step 3:**
- Tactic: [[atlas/tactics/ai-model-access|AML.TA0000: AI Model Access]]
- Technique: [[atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

**Step 4:**
- Tactic: [[atlas/tactics/discovery|AML.TA0008: Discovery]]
- Technique: [[atlas/techniques/discovery/discover-ai-model-ontology|AML.T0013: Discover AI Model Ontology]]

**Step 5:**
- Tactic: [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
- Technique: [[atlas/techniques/resource-development/acquire-public-ai-artifacts/datasets|AML.T0002.000: Datasets]]

**Step 6:**
- Tactic: [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
- Technique: [[atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-ai-model-overview|AML.T0005: Create Proxy AI Model]]

**Step 7:**
- Tactic: [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
- Technique: [[atlas/techniques/ai-attack-staging/craft-adversarial-data/white-box-optimization|AML.T0043.000: White-Box Optimization]]

**Step 8:**
- Tactic: [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
- Technique: [[atlas/techniques/resource-development/acquire-infrastructure/physical-countermeasures|AML.T0008.003: Physical Countermeasures]]

**Step 9:**
- Tactic: [[atlas/tactics/ai-model-access|AML.TA0000: AI Model Access]]
- Technique: [[atlas/techniques/ai-model-access/physical-environment-access|AML.T0041: Physical Environment Access]]

**Step 10:**
- Tactic: [[atlas/tactics/impact|AML.TA0011: Impact]]
- Technique: [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]




## References

MITRE Corporation. *Face Identification System Evasion via Physical Countermeasures (AML.CS0012)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0012
