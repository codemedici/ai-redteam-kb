---
id: camera-hijack-attack-on-facial-recognition-system
title: "AML.CS0004: Camera Hijack Attack on Facial Recognition System"
sidebar_label: "Camera Hijack Attack on Facial Recognition System"
sidebar_position: 5
---

# AML.CS0004: Camera Hijack Attack on Facial Recognition System

## Summary

This type of camera hijack attack can evade the traditional live facial recognition authentication model and enable access to privileged systems and victim impersonation.

Two individuals in China used this attack to gain access to the local government's tax system. They created a fake shell company and sent invoices via tax system to supposed clients. The individuals started this scheme in 2018 and were able to fraudulently collect $77 million.

## Metadata

- **Case Study ID:** AML.CS0004
- **Incident Date:** 2020
- **Type:** incident
- **Target:** Shanghai government tax office's facial recognition service
- **Actor:** Two individuals

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Gather Victim Identity Information

**Tactic:** [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[atlas/techniques/reconnaissance/gather-victim-identity-information|AML.T0087: Gather Victim Identity Information]]

The attackers collected user identity information and high-definition face photos from an online black market.

### Step 2: Establish Accounts

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/establish-accounts|AML.T0021: Establish Accounts]]

The attackers used the victim identity information to register new accounts in the tax system.

### Step 3: Consumer Hardware

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/acquire-infrastructure/consumer-hardware|AML.T0008.001: Consumer Hardware]]

The attackers bought customized low-end mobile phones.

### Step 4: Software Tools

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/obtain-capabilities/software-tools|AML.T0016.001: Software Tools]]

The attackers obtained customized Android ROMs and a virtual camera application.

### Step 5: Adversarial AI Attack Implementations

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/obtain-capabilities/adversarial-ai-attack-implementations|AML.T0016.000: Adversarial AI Attack Implementations]]

The attackers obtained software that turns static photos into videos, adding realistic effects such as blinking eyes.

### Step 6: AI-Enabled Product or Service

**Tactic:** [[atlas/tactics/ai-model-access|AML.TA0000: AI Model Access]]
**Technique:** [[atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

The attackers used the virtual camera app to present the generated video to the ML-based facial recognition service used for user verification.

### Step 7: Evade AI Model

**Tactic:** [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
**Technique:** [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

The attackers successfully evaded the face recognition system. This allowed the attackers to impersonate the victim and verify their identity in the tax system.

### Step 8: Financial Harm

**Tactic:** [[atlas/tactics/impact|AML.TA0011: Impact]]
**Technique:** [[atlas/techniques/impact/external-harms/financial-harm|AML.T0048.000: Financial Harm]]

The attackers used their privileged access to the tax system to send invoices to supposed clients and further their fraud scheme.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]] | [[atlas/techniques/reconnaissance/gather-victim-identity-information|AML.T0087: Gather Victim Identity Information]] |
| 2 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/establish-accounts|AML.T0021: Establish Accounts]] |
| 3 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/acquire-infrastructure/consumer-hardware|AML.T0008.001: Consumer Hardware]] |
| 4 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/obtain-capabilities/software-tools|AML.T0016.001: Software Tools]] |
| 5 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/obtain-capabilities/adversarial-ai-attack-implementations|AML.T0016.000: Adversarial AI Attack Implementations]] |
| 6 | [[atlas/tactics/ai-model-access|AML.TA0000: AI Model Access]] | [[atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]] |
| 7 | [[atlas/tactics/initial-access|AML.TA0004: Initial Access]] | [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]] |
| 8 | [[atlas/tactics/impact|AML.TA0011: Impact]] | [[atlas/techniques/impact/external-harms/financial-harm|AML.T0048.000: Financial Harm]] |



## External References

- Faces are the next target for fraudsters Available at: https://www.wsj.com/articles/faces-are-the-next-target-for-fraudsters-11625662828


## References

MITRE Corporation. *Camera Hijack Attack on Facial Recognition System (AML.CS0004)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0004
