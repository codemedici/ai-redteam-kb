---
id: use-ensemble-methods
title: "AML.M0006: Use Ensemble Methods"
sidebar_label: "Use Ensemble Methods"
sidebar_position: 7
type: mitigation
---

# AML.M0006: Use Ensemble Methods

Use an ensemble of models for inference to increase robustness to adversarial inputs. Some attacks may effectively evade one model or model family but be ineffective against others.

## Metadata

- **Mitigation ID:** AML.M0006
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** ML Model Engineering

## Techniques (11)

- [[frameworks/atlas/techniques/impact/erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]] — Using multiple different models increases robustness to attack.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-model|AML.T0010.001: AI Software]] — Using multiple different models ensures minimal performance loss if security flaw is found in tool for one model or family.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|AML.T0010.003: Model]] — Using multiple different models ensures minimal performance loss if security flaw is found in tool for one model or family.
- [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]] — Using multiple different models increases robustness to attack.
- [[frameworks/atlas/techniques/discovery/discover-ai-model-family|AML.T0014: Discover AI Model Family]] — Use multiple different models to fool adversaries of which type of model is used and how the model used.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043.000: White-Box Optimization]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001: Black-Box Optimization]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-transfer|AML.T0043.002: Black-Box Transfer]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-insert-backdoor-trigger|AML.T0043.004: Insert Backdoor Trigger]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-manual-modification|AML.T0043.003: Manual Modification]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data|AML.T0043: Craft Adversarial Data]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
