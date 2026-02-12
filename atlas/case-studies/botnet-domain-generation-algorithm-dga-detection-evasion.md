---
id: botnet-domain-generation-algorithm-dga-detection-evasion
title: "AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion"
sidebar_label: "Botnet Domain Generation Algorithm (DGA) Detection Evasion"
sidebar_position: 2
---

# AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion

## Summary

The Palo Alto Networks Security AI research team was able to bypass a Convolutional Neural Network based botnet Domain Generation Algorithm (DGA) detector using a generic domain name mutation technique.
It is a generic domain mutation technique which can evade most ML-based DGA detection modules.
The generic mutation technique evades most ML-based DGA detection modules DGA and can be used to test the effectiveness and robustness of all DGA detection methods developed by security companies in the industry before they is deployed to the production environment.

## Metadata

- **Case Study ID:** AML.CS0001
- **Incident Date:** 2020
- **Type:** exercise
- **Target:** Palo Alto Networks ML-based DGA detection module
- **Actor:** Palo Alto Networks AI Research Team

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Search Open Technical Databases

**Tactic:** [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[atlas/techniques/reconnaissance/search-open-technical-databases/search-open-technical-databases-overview|AML.T0000: Search Open Technical Databases]]

DGA detection is a widely used technique to detect botnets in academia and industry.
The research team searched for research papers related to DGA detection.

### Step 2: Acquire Public AI Artifacts

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-ai-artifacts-overview|AML.T0002: Acquire Public AI Artifacts]]

The researchers acquired a publicly available CNN-based DGA detection model and tested it against a well-known DGA generated domain name data sets, which includes ~50 million domain names from 64 botnet DGA families.
The CNN-based DGA detection model shows more than 70% detection accuracy on 16 (~25%) botnet DGA families.

### Step 3: Adversarial AI Attacks

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/develop-capabilities/adversarial-ai-attacks|AML.T0017.000: Adversarial AI Attacks]]

The researchers developed a generic mutation technique that requires a minimal number of iterations.

### Step 4: Black-Box Optimization

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/ai-attack-staging/craft-adversarial-data/black-box-optimization|AML.T0043.001: Black-Box Optimization]]

The researchers used the mutation technique to generate evasive domain names.

### Step 5: Verify Attack

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]]

The experiment results show that the detection rate of all 16 botnet DGA families drop to less than 25% after only one string is inserted once to the DGA generated domain names.

### Step 6: Evade AI Model

**Tactic:** [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]]
**Technique:** [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

The DGA generated domain names mutated with this technique successfully evade the target DGA Detection model, allowing an adversary to continue communication with their [Command and Control](https://attack.mitre.org/tactics/TA0011/) servers.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]] | [[atlas/techniques/reconnaissance/search-open-technical-databases/search-open-technical-databases-overview|AML.T0000: Search Open Technical Databases]] |
| 2 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-ai-artifacts-overview|AML.T0002: Acquire Public AI Artifacts]] |
| 3 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/develop-capabilities/adversarial-ai-attacks|AML.T0017.000: Adversarial AI Attacks]] |
| 4 | [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[atlas/techniques/ai-attack-staging/craft-adversarial-data/black-box-optimization|AML.T0043.001: Black-Box Optimization]] |
| 5 | [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]] |
| 6 | [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]] | [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]] |



## External References

- Yu, Bin, Jie Pan, Jiaming Hu, Anderson Nascimento, and Martine De Cock.  "Character level based detection of DGA domain names." In 2018 International Joint Conference on Neural Networks (IJCNN), pp. 1-8. IEEE, 2018. Available at: http://faculty.washington.edu/mdecock/papers/byu2018a.pdf
- Degas source code Available at: https://github.com/matthoffman/degas


## References

MITRE Corporation. *Botnet Domain Generation Algorithm (DGA) Detection Evasion (AML.CS0001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0001
