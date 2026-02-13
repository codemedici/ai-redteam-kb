---
id: verify-attack
title: "AML.T0042: Verify Attack"
sidebar_label: "Verify Attack"
sidebar_position: 43
---

# AML.T0042: Verify Attack

Adversaries can verify the efficacy of their attack via an inference API or access to an offline copy of the target model.
This gives the adversary confidence that their approach works and allows them to carry out the attack at a later time of their choosing.
The adversary may verify the attack once but use it against many edge devices running copies of the target model.
The adversary may verify their attack digitally, then deploy it in the [Physical Environment Access](/techniques/AML.T0041) at a later time.
Verifying the attack may be hard to detect since the adversary can use a minimal number of queries or an offline copy of the model.

## Metadata

- **Technique ID:** AML.T0042
- **Created:** 2021-05-13
- **Last Modified:** 2021-05-13
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Case Studies (7)

- [[frameworks/atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|Evasion of Deep Learning Detector for Malware C&C Traffic]]
- [[frameworks/atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|Botnet Domain Generation Algorithm (DGA) Detection Evasion]]
- [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|Microsoft Azure Service Disruption]]
- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[frameworks/atlas/case-studies/confusing-antimalware-neural-networks|Confusing Antimalware Neural Networks]]
- [[frameworks/atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|Achieving Code Execution in MathGPT via Prompt Injection]]
- [[frameworks/atlas/case-studies/poisongpt|PoisonGPT]]
