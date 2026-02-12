---
id: virustotal-poisoning
title: "AML.CS0002: VirusTotal Poisoning"
sidebar_label: "VirusTotal Poisoning"
sidebar_position: 3
---

# AML.CS0002: VirusTotal Poisoning

## Summary

McAfee Advanced Threat Research noticed an increase in reports of a certain ransomware family that was out of the ordinary. Case investigation revealed that many samples of that particular ransomware family were submitted through a popular virus-sharing platform within a short amount of time. Further investigation revealed that based on string similarity the samples were all equivalent, and based on code similarity they were between 98 and 74 percent similar. Interestingly enough, the compile time was the same for all the samples. After more digging, researchers discovered that someone used 'metame' a metamorphic code manipulating tool to manipulate the original file towards mutant variants. The variants would not always be executable, but are still classified as the same ransomware family.

## Metadata

- **Case Study ID:** AML.CS0002
- **Incident Date:** 2020
- **Type:** incident
- **Target:** VirusTotal
- **Actor:** Unknown

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Adversarial AI Attack Implementations

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/obtain-capabilities/adversarial-ai-attack-implementations|AML.T0016.000: Adversarial AI Attack Implementations]]

The actor obtained [metame](https://github.com/a0rtega/metame), a simple metamorphic code engine for arbitrary executables.

### Step 2: Craft Adversarial Data

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|AML.T0043: Craft Adversarial Data]]

The actor used a malware sample from a prevalent ransomware family as a start to create "mutant" variants.

### Step 3: Data

**Tactic:** [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
**Technique:** [[atlas/techniques/initial-access/ai-supply-chain-compromise/data|AML.T0010.002: Data]]

The actor uploaded "mutant" samples to the platform.

### Step 4: Poison Training Data

**Tactic:** [[atlas/tactics/persistence|AML.TA0006: Persistence]]
**Technique:** [[atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]]

Several vendors started to classify the files as the ransomware family even though most of them won't run.
The "mutant" samples poisoned the dataset the ML model(s) use to identify and classify this ransomware family.


## Tactics and Techniques Used


**Step 1:**
- Tactic: [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
- Technique: [[atlas/techniques/resource-development/obtain-capabilities/adversarial-ai-attack-implementations|AML.T0016.000: Adversarial AI Attack Implementations]]

**Step 2:**
- Tactic: [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
- Technique: [[atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|AML.T0043: Craft Adversarial Data]]

**Step 3:**
- Tactic: [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
- Technique: [[atlas/techniques/initial-access/ai-supply-chain-compromise/data|AML.T0010.002: Data]]

**Step 4:**
- Tactic: [[atlas/tactics/persistence|AML.TA0006: Persistence]]
- Technique: [[atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]]




## References

MITRE Corporation. *VirusTotal Poisoning (AML.CS0002)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0002
