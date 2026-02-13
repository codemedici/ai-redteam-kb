---
id: camera-hijack-attack-on-facial-recognition-system
title: "AML.CS0004: Camera Hijack Attack on Facial Recognition System"
type: case-study
sidebar_position: 5
---

# AML.CS0004: Camera Hijack Attack on Facial Recognition System

This type of camera hijack attack can evade the traditional live facial recognition authentication model and enable access to privileged systems and victim impersonation.

Two individuals in China used this attack to gain access to the local government's tax system. They created a fake shell company and sent invoices via tax system to supposed clients. The individuals started this scheme in 2018 and were able to fraudulently collect $77 million.

## Metadata

- **ID:** AML.CS0004
- **Incident Date:** 2020-01-01
- **Type:** incident
- **Target:** Shanghai government tax office's facial recognition service
- **Actor:** Two individuals

## Procedure

### Step 1: Gather Victim Identity Information

**Technique:** [[frameworks/atlas/techniques/reconnaissance/gather-victim-identity-information|AML.T0087: Gather Victim Identity Information]]

The attackers collected user identity information and high-definition face photos from an online black market.

### Step 2: Establish Accounts

**Technique:** [[frameworks/atlas/techniques/resource-development/establish-accounts|AML.T0021: Establish Accounts]]

The attackers used the victim identity information to register new accounts in the tax system.

### Step 3: Consumer Hardware

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/acquire-infrastructure-serverless|AML.T0008.001: Consumer Hardware]]

The attackers bought customized low-end mobile phones.

### Step 4: Software Tools

**Technique:** [[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-software-tools|AML.T0016.001: Software Tools]]

The attackers obtained customized Android ROMs and a virtual camera application.

### Step 5: Adversarial AI Attack Implementations

**Technique:** [[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-adversarial-AI-attacks|AML.T0016.000: Adversarial AI Attack Implementations]]

The attackers obtained software that turns static photos into videos, adding realistic effects such as blinking eyes.

### Step 6: AI-Enabled Product or Service

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

The attackers used the virtual camera app to present the generated video to the ML-based facial recognition service used for user verification.

### Step 7: Evade AI Model

**Technique:** [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

The attackers successfully evaded the face recognition system. This allowed the attackers to impersonate the victim and verify their identity in the tax system.

### Step 8: Financial Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/external-harms-financial|AML.T0048.000: Financial Harm]]

The attackers used their privileged access to the tax system to send invoices to supposed clients and further their fraud scheme.

## References

1. [Faces are the next target for fraudsters](https://www.wsj.com/articles/faces-are-the-next-target-for-fraudsters-11625662828)
