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

## Mitigations (4)

- [[frameworks/atlas/mitigations/passive-ai-output-obfuscation|AML.M0002: Passive AI Output Obfuscation]] — Obfuscating model outputs reduces an adversary's ability to verify the efficacy of an attack.
- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Restricting the number of queries to the model decreases an adversary's ability to verify the efficacy of an attack.
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-at-rest|AML.M0005: Control Access to AI Models and Data at Rest]] — Access controls on models at rest can prevent an adversary's ability to verify attack efficacy.
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-in-production|AML.M0019: Control Access to AI Models and Data in Production]] — Use access controls in production to prevent adversary's ability to verify attack efficacy.

## Case Studies (7)

- [[frameworks/atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|Evasion of Deep Learning Detector for Malware C&C Traffic]]
- [[frameworks/atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|Botnet Domain Generation Algorithm (DGA) Detection Evasion]]
- [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|Microsoft Azure Service Disruption]]
- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[frameworks/atlas/case-studies/confusing-antimalware-neural-networks|Confusing Antimalware Neural Networks]]
- [[frameworks/atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|Achieving Code Execution in MathGPT via Prompt Injection]]
- [[frameworks/atlas/case-studies/poisongpt|PoisonGPT]]
