---
id: evade-ai-model
title: "AML.T0015: Evade AI Model"
sidebar_label: "Evade AI Model"
sidebar_position: 16
---

# AML.T0015: Evade AI Model

Adversaries can [Craft Adversarial Data](/techniques/AML.T0043) that prevents an AI model from correctly identifying the contents of the data or [Generate Deepfakes](/techniques/AML.T0088) that fools an AI model expecting authentic data.

This technique can be used to evade a downstream task where AI is utilized. The adversary may evade AI-based virus/malware detection or network scanning towards the goal of a traditional cyber attack. AI model evasion through deepfake generation may also provide initial access to systems that use AI-based biometric authentication.

## Metadata

- **Technique ID:** AML.T0015
- **Created:** 2021-05-13
- **Last Modified:** 2025-11-04
- **Maturity:** realized

## Tactics (3)

- [[frameworks/atlas/tactics/initial-access|Initial Access]]
- [[frameworks/atlas/tactics/defense-evasion|Defense Evasion]]
- [[frameworks/atlas/tactics/impact|Impact]]

## Mitigations (6)

- [[frameworks/atlas/mitigations/model-hardening|AML.M0003: Model Hardening]] — Hardened models are more difficult to evade.
- [[frameworks/atlas/mitigations/use-ensemble-methods|AML.M0006: Use Ensemble Methods]] — Using multiple different models increases robustness to attack.
- [[frameworks/atlas/mitigations/use-multi-modal-sensors|AML.M0009: Use Multi-Modal Sensors]] — Using a variety of sensors can make it more difficult for an attacker to compromise and produce malicious results.
- [[frameworks/atlas/mitigations/input-restoration|AML.M0010: Input Restoration]] — Preprocessing model inputs can prevent malicious data from going through the machine learning pipeline.
- [[frameworks/atlas/mitigations/adversarial-input-detection|AML.M0015: Adversarial Input Detection]] — Prevent an attacker from introducing adversarial data into the system.
- [[frameworks/atlas/mitigations/deepfake-detection|AML.M0034: Deepfake Detection]] — Deepfake detection can be used to identify and block generated content.

## Case Studies (17)

- [[frameworks/atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|Evasion of Deep Learning Detector for Malware C&C Traffic]]
- [[frameworks/atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|Botnet Domain Generation Algorithm (DGA) Detection Evasion]]
- [[frameworks/atlas/case-studies/bypassing-cylance-s-ai-malware-detection|Bypassing Cylance's AI Malware Detection]]
- [[frameworks/atlas/case-studies/camera-hijack-attack-on-facial-recognition-system|Camera Hijack Attack on Facial Recognition System]]
- [[frameworks/atlas/case-studies/attack-on-machine-translation-services|Attack on Machine Translation Services]]
- [[frameworks/atlas/case-studies/proofpoint-evasion|ProofPoint Evasion]]
- [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|Microsoft Azure Service Disruption]]
- [[frameworks/atlas/case-studies/microsoft-edge-ai-evasion|Microsoft Edge AI Evasion]]
- [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|Face Identification System Evasion via Physical Countermeasures]]
- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[frameworks/atlas/case-studies/confusing-antimalware-neural-networks|Confusing Antimalware Neural Networks]]
- [[frameworks/atlas/case-studies/bypassing-id-me-identity-verification|Bypassing ID.me Identity Verification]]
- [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AI Model Tampering via Supply Chain Attack]]
- [[frameworks/atlas/case-studies/attempted-evasion-of-ml-phishing-webpage-detection-system|Attempted Evasion of ML Phishing Webpage Detection System]]
- [[frameworks/atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[frameworks/atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|ProKYC: Deepfake Tool for Account Fraud Attacks]]
- [[frameworks/atlas/case-studies/malware-prototype-with-embedded-prompt-injection|Malware Prototype with Embedded Prompt Injection]]
