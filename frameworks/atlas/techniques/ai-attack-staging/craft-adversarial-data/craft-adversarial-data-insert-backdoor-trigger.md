---
id: craft-adversarial-data-insert-backdoor-trigger
title: "AML.T0043.004: Insert Backdoor Trigger"
sidebar_label: "Insert Backdoor Trigger"
sidebar_position: 11
---

# AML.T0043.004: Insert Backdoor Trigger

The adversary may add a perceptual trigger into inference data.
The trigger may be imperceptible or non-obvious to humans.
This technique is used in conjunction with [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|Poison AI Model]] and allows the adversary to produce their desired effect in the target model.

## Metadata

- **Technique ID:** AML.T0043.004
- **Created:** May 13, 2021
- **Last Modified:** May 13, 2021
- **Maturity:** demonstrated

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (1)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]

The trigger is placed in the physical environment, where it is captured by the victim's device camera and processed by the backdoored ML model.

## References

MITRE Corporation. *Insert Backdoor Trigger (AML.T0043.004)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0043.004
