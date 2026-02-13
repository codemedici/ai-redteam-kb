---
id: live-deepfake-image-injection-to-evade-mobile-kyc-verification
title: "AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification"
sidebar_label: "Live Deepfake Image Injection to Evade Mobile KYC Verification"
sidebar_position: 34
---

# AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification

## Summary

Facial biometric authentication services are commonly used by mobile applications for user onboarding, authentication, and identity verification for KYC requirements. The iProov Red Team demonstrated a face-swapped imagery injection attack that can successfully evade live facial recognition authentication models along with both passive and active [liveness verification](https://en.wikipedia.org/wiki/Liveness_test) on mobile devices. By executing this kind of attack, adversaries could gain access to privileged systems of a victim or create fake personas to create fake accounts on banking or cryptocurrency apps.

## Metadata

- **Case Study ID:** AML.CS0033
- **Incident Date:** 2024
- **Type:** exercise
- **Target:** Mobile facial authentication service
- **Actor:** iProov Red Team

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Gather Victim Identity Information

**Technique:** [[frameworks/atlas/techniques/reconnaissance/gather-victim-identity-information|AML.T0087: Gather Victim Identity Information]]

The researchers collected user identity information and high-definition facial images from online social networks and/or black-market sites.

### Step 2: Generative AI

**Technique:** [[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-generative-AI|AML.T0016.002: Generative AI]]

The researchers obtained [Faceswap](https://swapface.org) a desktop application capable of swapping faces in a video in real-time.

### Step 3: Software Tools

**Technique:** [[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-software-tools|AML.T0016.001: Software Tools]]

The researchers obtained [Open Broadcaster Software (OBS)](https://obsproject.com)which can broadcast a video stream over the network.

### Step 4: Obtain Capabilities

**Technique:** AML.T0016: Obtain Capabilities

The researchers obtained [Virtual Camera: Live Assist](https://apkpure.com/virtual-camera-live-assist/virtual.camera.app), an Android app that allows a user to substitute the devices camera  with a video stream. This app works on genuine, non-rooted Android devices.

### Step 5: Generate Deepfakes

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/generate-deepfakes|AML.T0088: Generate Deepfakes]]

The researchers use the gathered victim face images and the Faceswap tool to produce live deepfake videos which mimic the victim’s appearance.

### Step 6: Establish Accounts

**Technique:** [[frameworks/atlas/techniques/resource-development/establish-accounts|AML.T0021: Establish Accounts]]

The researchers used the gathered victim information to register an account for a financial services application.

### Step 7: AI-Enabled Product or Service

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

During identity verification, the financial services application uses facial recognition and liveness detection to analyze live video from the user’s camera.

### Step 8: Evade AI Model

**Technique:** [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

The researchers stream the deepfake video feed using OBS and use the Virtual Camera app to replace the default camera with feed. This successfully evades the facial recognition system and allows the researchers to authenticate themselves under the victim’s identity.

### Step 9: Impersonation

**Technique:** [[frameworks/atlas/techniques/defense-evasion/impersonation|AML.T0073: Impersonation]]

With an authenticated account under the victim’s identity, the researchers successfully impersonate the victim and evade detection.

### Step 10: Financial Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/external-harms-financial|AML.T0048.000: Financial Harm]]

The researchers could then have caused financial harm to the victim.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/reconnaissance/gather-victim-identity-information|AML.T0087: Gather Victim Identity Information]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-generative-AI|AML.T0016.002: Generative AI]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-software-tools|AML.T0016.001: Software Tools]]

**Step 4:**
- Technique: AML.T0016: Obtain Capabilities

**Step 5:**
- Technique: [[frameworks/atlas/techniques/ai-attack-staging/generate-deepfakes|AML.T0088: Generate Deepfakes]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/resource-development/establish-accounts|AML.T0021: Establish Accounts]]

**Step 7:**
- Technique: [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

**Step 8:**
- Technique: [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

**Step 9:**
- Technique: [[frameworks/atlas/techniques/defense-evasion/impersonation|AML.T0073: Impersonation]]

**Step 10:**
- Technique: [[frameworks/atlas/techniques/impact/external-harms/external-harms-financial|AML.T0048.000: Financial Harm]]

## References

MITRE Corporation. *Live Deepfake Image Injection to Evade Mobile KYC Verification (AML.CS0033)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0033
