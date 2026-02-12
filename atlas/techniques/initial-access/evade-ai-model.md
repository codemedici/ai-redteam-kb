---
id: evade-ai-model
title: "AML.T0015: Evade AI Model"
sidebar_label: "Evade AI Model"
sidebar_position: 7
---

# AML.T0015: Evade AI Model



Adversaries can [[atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|Craft Adversarial Data]] that prevents an AI model from correctly identifying the contents of the data or [[atlas/techniques/ai-attack-staging/generate-deepfakes|Generate Deepfakes]] that fools an AI model expecting authentic data.

This technique can be used to evade a downstream task where AI is utilized. The adversary may evade AI-based virus/malware detection or network scanning towards the goal of a traditional cyber attack. AI model evasion through deepfake generation may also provide initial access to systems that use AI-based biometric authentication.

## Metadata

- **Technique ID:** AML.T0015
- **Created:** May 13, 2021
- **Last Modified:** November 4, 2025
- **Maturity:** realized



## Tactics (3)

This technique supports the following tactics:


- [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
- [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]]
- [[atlas/tactics/impact|AML.TA0011: Impact]]




## Case Studies (17)


The following case studies demonstrate this technique:

### [[atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|AML.CS0000: Evasion of Deep Learning Detector for Malware C&C Traffic]]

With the crafted samples, we performed online evasion of the ML-based spyware detection model.
The crafted packets were identified as benign with > 80% confidence.
This evaluation demonstrates that adversaries are able to bypass advanced ML detection techniques, by crafting samples that are misclassified by an ML model.

### [[atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion]]

The DGA generated domain names mutated with this technique successfully evade the target DGA Detection model, allowing an adversary to continue communication with their [Command and Control](https://attack.mitre.org/tactics/TA0011/) servers.

### [[atlas/case-studies/bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]

Due to the secondary model overriding the primary, the researchers were effectively able to bypass the ML model.

### [[atlas/case-studies/camera-hijack-attack-on-facial-recognition-system|AML.CS0004: Camera Hijack Attack on Facial Recognition System]]

The attackers successfully evaded the face recognition system. This allowed the attackers to impersonate the victim and verify their identity in the tax system.

### [[atlas/case-studies/attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]

The adversarial examples were used to evade the machine translation services by a variety of means. This included targeted word flips, vulgar outputs, and dropped sentences.

### [[atlas/case-studies/proofpoint-evasion|AML.CS0008: ProofPoint Evasion]]

Finally, these insights from the "offline" proxy model allowed the researchers to create malicious emails that received preferable scores from the real ProofPoint email protection system, hence bypassing it.

### [[atlas/case-studies/microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]

The team performed an online evasion attack by replaying the adversarial examples and accomplished their goals.

### [[atlas/case-studies/microsoft-edge-ai-evasion|AML.CS0011: Microsoft Edge AI Evasion]]

Feeding this perturbed image, the red team was able to evade the ML model by causing misclassifications.

### [[atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]

The team successfully evaded the model using the physical countermeasure by causing targeted misclassifications.

### [[atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]

Presenting the visual trigger causes the victim model to be bypassed.
The researchers demonstrated this can be used to evade ML models in
several safety-critical apps in the Google Play store.

### [[atlas/case-studies/confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]

The researchers demonstrated that for most of the adversarial files, the antimalware model was successfully evaded.
In practice, an adversary could deploy their adversarially crafted malware and infect systems while evading detection.

### [[atlas/case-studies/bypassing-id-me-identity-verification|AML.CS0017: Bypassing ID.me Identity Verification]]

The individual collected stolen identities, including names, dates of birth, and Social Security numbers. and used them along with a photo of himself wearing wigs to acquire fake driver's licenses.

The individual uploaded forged IDs along with a selfie. The ID.me document verification system matched the selfie to the ID photo, allowing some fraudulent claims to proceed in the application pipeline.

### [[atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]

Once the adversary's container image is deployed, the model may misclassify inputs due to the adversary's manipulations.

### [[atlas/case-studies/attempted-evasion-of-ml-phishing-webpage-detection-system|AML.CS0032: Attempted Evasion of ML Phishing Webpage Detection System]]

The visual similarity model used to detect brand impersonation was evaded. However, other components of the phishing detection system successfully identified the phishing websites.

### [[atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]

The researchers stream the deepfake video feed using OBS and use the Virtual Camera app to replace the default camera with feed. This successfully evades the facial recognition system and allows the researchers to authenticate themselves under the victim’s identity.

### [[atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]

The bad actor used ProKYC to replace the camera feed with the deepfake selfie video. This successfully evaded the KYC verification and allowed the bad actor to authenticate themselves under the false identity.

### [[atlas/case-studies/malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]

The LLM-based malware detection or analysis tool could be manipulated into not reporting the Skynet binary as malware.

Note: The prompt injection was not effective against the LLMs that Check Point Research tested.



## References

MITRE Corporation. *Evade AI Model (AML.T0015)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0015
