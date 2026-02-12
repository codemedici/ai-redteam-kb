---
id: attempted-evasion-of-ml-phishing-webpage-detection-system
title: "AML.CS0032: Attempted Evasion of ML Phishing Webpage Detection System"
sidebar_label: "Attempted Evasion of ML Phishing Webpage Detection System"
sidebar_position: 33
---

# AML.CS0032: Attempted Evasion of ML Phishing Webpage Detection System

## Summary

Adversaries create phishing websites that appear visually similar to legitimate sites. These sites are designed to trick users into entering their credentials, which are then sent to the bad actor. To combat this behavior, security companies utilize AI/ML-based approaches to detect phishing sites and block them in their endpoint security products.

In this incident, adversarial examples were identified in the logs of a commercial machine learning phishing website detection system. The detection system makes an automated block/allow determination from the "phishing score" of an ensemble of image classifiers each responsible for different phishing indicators (visual similarity, input form detection, etc.). The adversarial examples appeared to employ several simple yet effective strategies for manually modifying brand logos in an attempt to evade image classification models. The phishing websites which employed logo modification methods successfully evaded the model responsible detecting brand impersonation via visual similarity. However, the other components of the system successfully flagged the phishing websites.

## Metadata

- **Case Study ID:** AML.CS0032
- **Incident Date:** 2022
- **Type:** incident
- **Target:** Commercial ML Phishing Webpage Detector
- **Actor:** Unknown

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Manual Modification

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/ai-attack-staging/craft-adversarial-data/manual-modification|AML.T0043.003: Manual Modification]]

Several cheap, yet effective strategies for manually modifying logos were observed:
| Evasive Strategy | Count |
| - | - |
| Company name style | 25 |
| Blurry logo | 23 |
| Cropping | 20 |
| No company name | 16 |
| No visual logo | 13 |
| Different visual logo | 12 |
| Logo stretching | 11 |
| Multiple forms - images | 10 |
| Background patterns | 8 |
| Login obfuscation | 6 |
| Masking | 3 |

### Step 2: Evade AI Model

**Tactic:** [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]]
**Technique:** [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

The visual similarity model used to detect brand impersonation was evaded. However, other components of the phishing detection system successfully identified the phishing websites.

### Step 3: Phishing

**Tactic:** [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
**Technique:** [[atlas/techniques/initial-access/phishing/phishing-overview|AML.T0052: Phishing]]

If the adversary can successfully evade detection, they can continue to operate their phishing websites and steal the victim's credentials.

### Step 4: User Harm

**Tactic:** [[atlas/tactics/impact|AML.TA0011: Impact]]
**Technique:** [[atlas/techniques/impact/external-harms/user-harm|AML.T0048.003: User Harm]]

The end user may experience a variety of harms including financial and privacy harms depending on the credentials stolen by the adversary.


## Tactics and Techniques Used


**Step 1:**
- Tactic: [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
- Technique: [[atlas/techniques/ai-attack-staging/craft-adversarial-data/manual-modification|AML.T0043.003: Manual Modification]]

**Step 2:**
- Tactic: [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]]
- Technique: [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

**Step 3:**
- Tactic: [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
- Technique: [[atlas/techniques/initial-access/phishing/phishing-overview|AML.T0052: Phishing]]

**Step 4:**
- Tactic: [[atlas/tactics/impact|AML.TA0011: Impact]]
- Technique: [[atlas/techniques/impact/external-harms/user-harm|AML.T0048.003: User Harm]]



## External References

- "Real Attackers Don't Compute Gradients": Bridging the Gap Between Adversarial ML Research and Practice Available at: https://arxiv.org/abs/2212.14315
- Real Attackers Don't Compute Gradients Supplementary Resources Available at: https://real-gradients.github.io/


## References

MITRE Corporation. *Attempted Evasion of ML Phishing Webpage Detection System (AML.CS0032)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0032
