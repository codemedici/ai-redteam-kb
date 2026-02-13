---
id: ai-attack-staging
title: "AML.TA0001: AI Attack Staging"
sidebar_label: "AI Attack Staging"
sidebar_position: 13
---

# AML.TA0001: AI Attack Staging

The adversary is leveraging their knowledge of and access to the target system to tailor the attack.

AI Attack Staging consists of techniques adversaries use to prepare their attack on the target AI model.
Techniques can include training proxy models, poisoning the target model, and crafting adversarial data to feed the target model.
Some of these techniques can be performed in an offline manner and are thus difficult to mitigate.
These techniques are often used to achieve the adversary's end goal.

## Metadata

- **Tactic ID:** AML.TA0001
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09

## Techniques (14)

The following techniques can be used to achieve this tactic:

- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-gathered-artifacts|AML.T0005.000]] — Train Proxy via Gathered AI Artifacts (demonstrated)
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-replication|AML.T0005.001]] — Train Proxy via Replication (demonstrated)
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-use-pre-trained-model|AML.T0005.002]] — Use Pre-Trained Model (feasible)
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018.000]] — Poison AI Model (demonstrated)
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-modify-architecture|AML.T0018.001]] — Modify AI Model Architecture (demonstrated)
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-embed-malware|AML.T0018.002]] — Embed Malware (realized)
- [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042]] — Verify Attack (demonstrated)
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043.000]] — White-Box Optimization (demonstrated)
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001]] — Black-Box Optimization (demonstrated)
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-transfer|AML.T0043.002]] — Black-Box Transfer (demonstrated)
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-manual-modification|AML.T0043.003]] — Manual Modification (realized)
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-insert-backdoor-trigger|AML.T0043.004]] — Insert Backdoor Trigger (demonstrated)
- [[frameworks/atlas/techniques/ai-attack-staging/generate-deepfakes|AML.T0088]] — Generate Deepfakes (realized)
- [[frameworks/atlas/techniques/ai-attack-staging/generate-malicious-commands|AML.T0102]] — Generate Malicious Commands (realized)
