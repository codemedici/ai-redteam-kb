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

**Technique:** AML.T0000: Search Open Technical Databases

The team first performed reconnaissance to gather information about the target ML model.

### Step 2: Valid Accounts

**Technique:** [[frameworks/atlas/techniques/initial-access/valid-accounts|AML.T0012: Valid Accounts]]

The team used a valid account to gain access to the network.

### Step 3: AI Artifact Collection

**Technique:** [[frameworks/atlas/techniques/collection/ai-artifact-collection|AML.T0035: AI Artifact Collection]]

The team found the model file of the target ML model and the necessary training data.

### Step 4: Exfiltration via Cyber Means

**Technique:** [[frameworks/atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025: Exfiltration via Cyber Means]]

The team exfiltrated the model and data via traditional means.

### Step 5: White-Box Optimization

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043.000: White-Box Optimization]]

Using the target model and data, the red team crafted evasive adversarial data in an offline manor.

### Step 6: AI Model Inference API Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

The team used an exposed API to access the target model.

### Step 7: Verify Attack

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]]

The team submitted the adversarial examples to the API to verify their efficacy on the production system.

### Step 8: Evade AI Model

**Technique:** [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

The team performed an online evasion attack by replaying the adversarial examples and accomplished their goals.

## Tactics and Techniques Used

**Step 1:**
- Technique: AML.T0000: Search Open Technical Databases

**Step 2:**
- Technique: [[frameworks/atlas/techniques/initial-access/valid-accounts|AML.T0012: Valid Accounts]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/collection/ai-artifact-collection|AML.T0035: AI Artifact Collection]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025: Exfiltration via Cyber Means]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043.000: White-Box Optimization]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

**Step 7:**
- Technique: [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]]

**Step 8:**
- Technique: [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

## References

MITRE Corporation. *Microsoft Azure Service Disruption (AML.CS0010)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0010
