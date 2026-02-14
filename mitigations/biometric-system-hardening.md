---
title: "Biometric System Hardening"
tags:
  - type/mitigation
  - target/generative-ai
  - target/cv
  - target/authentication
  - source/adversarial-ai
atlas: AML.M0009
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Biometric System Hardening

## Summary

Biometric system hardening strengthens face recognition, fingerprint scanning, and other biometric authentication systems against GAN-generated attacks including master face images, DeepMasterPrints, and face verification bypass using StarGAN. Techniques include adversarial training with GAN-generated data, increased verification thresholds for critical systems, multi-factor authentication, and multi-modal sensors (e.g., IR depth cameras) that detect synthetic media artifacts invisible to standard cameras.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0088 | [[techniques/gan-weaponization]] | Hardens biometric systems against GAN-generated master faces, synthetic fingerprints, and face verification bypass |

## Implementation

### Adversarial Training with GAN-Generated Data

Train biometric classifiers on GAN-generated adversarial examples:
- Include StyleGAN-generated master face images in training sets with "synthetic" labels
- Train fingerprint scanners on DeepMasterPrint samples
- Use StarGAN v2 face translations as negative examples for face verification

### Increased Verification Thresholds

- Lower False Match Rate (FMR) thresholds for critical systems
- Research shows master faces bypass 40-60% of samples; DeepMasterPrints spoof 23% at FMR 0.1% but 77% at FMR 1.0%
- Tighter thresholds dramatically reduce GAN attack success rates at cost of higher false rejection

### Multi-Factor Authentication

- Combine biometric verification with additional factors (knowledge, possession)
- Prevents single-factor bypass via GAN-generated biometrics
- Essential for high-security applications where biometrics alone are insufficient

### Multi-Modal Sensors

- **IR depth cameras** detect flat images (printed photos, screens displaying deepfakes)
- **Liveness detection** verifies physical presence (blink detection, head movement)
- **Thermal imaging** detects temperature patterns absent in synthetic media
- Multiple sensor modalities make GAN attacks significantly harder—attacker must fool all sensors simultaneously

> Source: [[sources/bibliography#Adversarial AI]], p. 318-319, 321-325

## Limitations & Trade-offs

- **Usability impact:** Stricter thresholds and multi-factor requirements increase friction for legitimate users
- **Sensor cost:** Multi-modal sensors (IR, thermal) add hardware expense
- **Liveness bypass:** Sophisticated attackers may defeat liveness detection with 3D masks or video replay
- **Arms race:** As biometric hardening improves, GAN techniques adapt (e.g., 3D face generation)

## Testing & Validation

1. Test biometric systems against master face image datasets
2. Evaluate fingerprint scanners against DeepMasterPrint samples at various FMR thresholds
3. Validate liveness detection against screen replay and printed photo attacks
4. Test multi-modal sensor fusion against coordinated multi-vector attacks

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "9 generated master faces bypassed authentication in 40%-60% of samples—alarming rate for dictionary attacks."
> — [[sources/bibliography#Adversarial AI]], p. 307

> "DeepMasterPrints at FMR 0.1% spoofed 23% of test subjects; at FMR 1.0% spoofed 77%."
> — [[sources/bibliography#Adversarial AI]], p. 307-309
