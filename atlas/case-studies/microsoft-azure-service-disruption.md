---
id: microsoft-azure-service-disruption
title: "AML.CS0010: Microsoft Azure Service Disruption"
sidebar_label: "Microsoft Azure Service Disruption"
sidebar_position: 11
---

# AML.CS0010: Microsoft Azure Service Disruption

## Summary

The Microsoft AI Red Team performed a red team exercise on an internal Azure service with the intention of disrupting its service. This operation had a combination of traditional ATT&CK enterprise techniques such as finding valid account, and exfiltrating data -- all interleaved with adversarial ML specific steps such as offline and online evasion examples.

## Metadata

- **Case Study ID:** AML.CS0010
- **Incident Date:** 2020
- **Type:** exercise
- **Target:** Internal Microsoft Azure Service
- **Actor:** Microsoft AI Red Team

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Search Open Technical Databases

**Tactic:** [[reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[search-open-technical-databases|AML.T0000: Search Open Technical Databases]]

The team first performed reconnaissance to gather information about the target ML model.

### Step 2: Valid Accounts

**Tactic:** [[initial-access|AML.TA0004: Initial Access]]
**Technique:** [[valid-accounts|AML.T0012: Valid Accounts]]

The team used a valid account to gain access to the network.

### Step 3: AI Artifact Collection

**Tactic:** [[collection|AML.TA0009: Collection]]
**Technique:** [[ai-artifact-collection|AML.T0035: AI Artifact Collection]]

The team found the model file of the target ML model and the necessary training data.

### Step 4: Exfiltration via Cyber Means

**Tactic:** [[exfiltration|AML.TA0010: Exfiltration]]
**Technique:** [[exfiltration-via-cyber-means|AML.T0025: Exfiltration via Cyber Means]]

The team exfiltrated the model and data via traditional means.

### Step 5: White-Box Optimization

**Tactic:** [[ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[white-box-optimization|AML.T0043.000: White-Box Optimization]]

Using the target model and data, the red team crafted evasive adversarial data in an offline manor.

### Step 6: AI Model Inference API Access

**Tactic:** [[ai-model-access|AML.TA0000: AI Model Access]]
**Technique:** [[ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

The team used an exposed API to access the target model.

### Step 7: Verify Attack

**Tactic:** [[ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[verify-attack|AML.T0042: Verify Attack]]

The team submitted the adversarial examples to the API to verify their efficacy on the production system.

### Step 8: Evade AI Model

**Tactic:** [[impact|AML.TA0011: Impact]]
**Technique:** [[evade-ai-model|AML.T0015: Evade AI Model]]

The team performed an online evasion attack by replaying the adversarial examples and accomplished their goals.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[reconnaissance|AML.TA0002: Reconnaissance]] | [[search-open-technical-databases|AML.T0000: Search Open Technical Databases]] |
| 2 | [[initial-access|AML.TA0004: Initial Access]] | [[valid-accounts|AML.T0012: Valid Accounts]] |
| 3 | [[collection|AML.TA0009: Collection]] | [[ai-artifact-collection|AML.T0035: AI Artifact Collection]] |
| 4 | [[exfiltration|AML.TA0010: Exfiltration]] | [[exfiltration-via-cyber-means|AML.T0025: Exfiltration via Cyber Means]] |
| 5 | [[ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[white-box-optimization|AML.T0043.000: White-Box Optimization]] |
| 6 | [[ai-model-access|AML.TA0000: AI Model Access]] | [[ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]] |
| 7 | [[ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[verify-attack|AML.T0042: Verify Attack]] |
| 8 | [[impact|AML.TA0011: Impact]] | [[evade-ai-model|AML.T0015: Evade AI Model]] |




## References

MITRE Corporation. *Microsoft Azure Service Disruption (AML.CS0010)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0010
