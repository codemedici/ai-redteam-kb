---
id: erode-ai-model-integrity
title: "AML.T0031: Erode AI Model Integrity"
sidebar_label: "Erode AI Model Integrity"
sidebar_position: 32
---

# AML.T0031: Erode AI Model Integrity

Adversaries may degrade the target model's performance with adversarial data inputs to erode confidence in the system over time.
This can lead to the victim organization wasting time and money both attempting to fix the system and performing the tasks it was meant to automate by hand.

## Metadata

- **Technique ID:** AML.T0031
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** realized

## Tactics (1)

- [[frameworks/atlas/tactics/impact|Impact]]

## Mitigations (4)

- [[frameworks/atlas/mitigations/model-hardening|AML.M0003: Model Hardening]] — Hardened models are less susceptible to integrity attacks.
- [[frameworks/atlas/mitigations/use-ensemble-methods|AML.M0006: Use Ensemble Methods]] — Using multiple different models increases robustness to attack.
- [[frameworks/atlas/mitigations/input-restoration|AML.M0010: Input Restoration]] — Preprocessing model inputs can prevent malicious data from going through the machine learning pipeline.
- [[frameworks/atlas/mitigations/adversarial-input-detection|AML.M0015: Adversarial Input Detection]] — Incorporate adversarial input detection into the pipeline before inputs reach the model.

## Case Studies (6)

- [[frameworks/atlas/case-studies/attack-on-machine-translation-services|Attack on Machine Translation Services]]
- [[frameworks/atlas/case-studies/clearviewai-misconfiguration|ClearviewAI Misconfiguration]]
- [[frameworks/atlas/case-studies/tay-poisoning|Tay Poisoning]]
- [[frameworks/atlas/case-studies/poisongpt|PoisonGPT]]
- [[frameworks/atlas/case-studies/web-scale-data-poisoning-split-view-attack|Web-Scale Data Poisoning: Split-View Attack]]
- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
