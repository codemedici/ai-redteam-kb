---
id: craft-adversarial-data-white-box-optimization
title: "AML.T0043.000: White-Box Optimization"
sidebar_label: "White-Box Optimization"
sidebar_position: 7
---

# AML.T0043.000: White-Box Optimization

> **Sub-Technique of:** [[atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|AML.T0043: Craft Adversarial Data]]

In White-Box Optimization, the adversary has full access to the target model and optimizes the adversarial example directly.
Adversarial examples trained in this manner are most effective against the target model.

## Metadata

- **Technique ID:** AML.T0043.000
- **Created:** May 13, 2021
- **Last Modified:** January 12, 2024
- **Maturity:** demonstrated

- **Parent Technique:** [[atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|AML.T0043: Craft Adversarial Data]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (2)

The following case studies demonstrate this technique:

### [[atlas/case-studies/microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]

Using the target model and data, the red team crafted evasive adversarial data in an offline manor.

### [[atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]

Using the proxy model, the red team optimized adversarial visual patterns as a physical domain patch-based attack using expectation over transformation.

## References

MITRE Corporation. *White-Box Optimization (AML.T0043.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0043.000
