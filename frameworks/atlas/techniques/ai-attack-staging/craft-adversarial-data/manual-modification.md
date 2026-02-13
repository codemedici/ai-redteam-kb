---
id: craft-adversarial-data-manual-modification
title: "AML.T0043.003: Manual Modification"
sidebar_label: "Manual Modification"
sidebar_position: 10
---

# AML.T0043.003: Manual Modification

> **Sub-Technique of:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data|AML.T0043: Craft Adversarial Data]]

Adversaries may manually modify the input data to craft adversarial data.
They may use their knowledge of the target model to modify parts of the data they suspect helps the model in performing its task.
The adversary may use trial and error until they are able to verify they have a working adversarial input.

## Metadata

- **Technique ID:** AML.T0043.003
- **Created:** May 13, 2021
- **Last Modified:** May 13, 2021
- **Maturity:** realized

- **Parent Technique:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data|AML.T0043: Craft Adversarial Data]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (3)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|AML.CS0000: Evasion of Deep Learning Detector for Malware C&C Traffic]]

We crafted evasion samples by removing fields from packet header which are typically not used for C&C communication (e.g. cache-control, connection, etc.).

### [[frameworks/atlas/case-studies/bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]

Using this knowledge, the researchers fused attributes of known good files with malware to manually create adversarial malware.

### [[frameworks/atlas/case-studies/attempted-evasion-of-ml-phishing-webpage-detection-system|AML.CS0032: Attempted Evasion of ML Phishing Webpage Detection System]]

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

## References

MITRE Corporation. *Manual Modification (AML.T0043.003)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0043.003
