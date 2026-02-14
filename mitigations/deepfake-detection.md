---
title: "Deepfake Detection"
tags:
  - type/mitigation
  - target/generative-ai
  - target/cv
  - target/audio
  - source/adversarial-ai
atlas: AML.M0034
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Deepfake Detection

## Summary

Deepfake detection encompasses techniques and tools that identify synthetically generated or manipulated media (images, video, audio) produced by GANs, diffusion models, or other generative architectures. Detection approaches range from identity-based classification using CNNs to temporal analysis via optical flow and RNNs. Despite progress, current detection methods suffer from low generalization—new deepfakes not seen during training frequently evade detection, creating an ongoing arms race between generation and detection capabilities.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0088 | [[techniques/gan-weaponization]] | Detects GAN-generated deepfake images, video, and audio across multiple modalities |

## Implementation

### Identity-Based Detection (DFDC Challenge)

The **Deepfake Detection Challenge (DFDC, 2019)** was a collaboration between Facebook/Meta, Microsoft, and Trust in AI:
- **115,000 labeled videos** in public dataset
- **10,000 video** private black-box test set

**Winning solution (Selim Seferbekov):**
- **Public dataset:** 82.56% accuracy
- **Private black-box:** 65.18% accuracy (highest score)

**Processing pipeline:**
1. Extract individual frames from videos
2. Use **MTCNN** (Multi-Task Cascaded CNNs) to extract faces and facial landmarks
3. Basic preprocessing (resizing, cropping)
4. Extract landmarks as JSON
5. Calculate **SSIM** (Structural Similarity Index Measure) between fake/real images
6. Data augmentation (compression, noise, blur, color jittering, scaling, rotations)
7. Train **EfficientNet** (CNN autoencoder) to detect fakes using 32 frames per video

**Alternative face detector:** RetinaFace (more accurate but slower than MTCNN).

> Source: [[sources/bibliography#Adversarial AI]], p. 302-303

### Detection Methods by Architecture

**CNNs:**
- Learn to extract features from images and videos
- Classify based on image quality, warping artifacts, facial expressions

**Optical Flow + CNNs:**
- Measure pixel motion between consecutive frames
- Capture temporal dynamics of facial expressions
- Detect inconsistencies in deepfake videos

**RNNs:**
- Capture temporal dependencies and patterns
- Detect mismatches/dissonance in facial expressions and speech

**Transformers:**
- Model spatial and temporal relationships
- Multi-modal and multi-scale feature detection

**GANs (fingerprint-based):**
- Exploit GAN fingerprints, artifacts, and inconsistencies
- Turn deepfake generation technique into detection tool

### Detection Ensemble Approach

Combine multiple detection techniques for robustness:
- Deploy CNN-based, optical flow, RNN, transformer, and GAN-fingerprint detectors in parallel
- Use human inspection when possible
- Build detection tool ensemble to compensate for individual detector weaknesses

### Voice Cloning Detection

The **US FTC Voice Cloning Challenge** highlights the emergence of new detection players focused on synthetic voice detection. Detection techniques for voice deepfakes (WaveNet, Tacotron, MelNet) are an active area with growing investment.

> Source: [[sources/bibliography#Adversarial AI]], p. 304-305

## Limitations & Trade-offs

> "Despite their progress, current efforts are not ideal, and detection methods suffer from low generalization. New deepfakes not exposed in training phase evade detection."
> — [[sources/bibliography#Adversarial AI]], p. 304

- **Low generalization:** Detectors trained on known deepfake types fail on novel generation techniques
- **Arms race dynamic:** As detection improves, generation techniques adapt to evade detection
- **Computational cost:** Running multiple detectors in ensemble adds latency
- **False positives:** Legitimate compressed or low-quality media may be flagged
- **Cross-domain gaps:** Image detectors don't detect voice deepfakes and vice versa

**Survey:** *Deepfakes Generation and Detection: A Short Survey* (Dr. Zahid Akhtar, 2023)

## Testing & Validation

1. Test detectors against multiple GAN architectures (StyleGAN, StarGAN, Pix2PixHD)
2. Evaluate on both in-distribution and out-of-distribution deepfakes
3. Measure false positive rate on legitimate media
4. Test ensemble diversity — ensure detectors fail on different samples
5. Benchmark against DFDC dataset for standardized comparison

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Despite their progress, current efforts are not ideal, and detection methods suffer from low generalization. New deepfakes not exposed in training phase evade detection."
> — [[sources/bibliography#Adversarial AI]], p. 304

> Winning DFDC solution achieved 82.56% public / 65.18% private black-box accuracy using EfficientNet with MTCNN face extraction and 32-frame analysis.
> — [[sources/bibliography#Adversarial AI]], p. 302-303
