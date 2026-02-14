---
title: "Privacy-Preserving Data Generation"
tags:
  - type/mitigation
  - target/generative-ai
  - target/cv
  - target/training-pipeline
  - source/adversarial-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Privacy-Preserving Data Generation

## Summary

Privacy-preserving data generation uses GANs and other generative models to create realistic but anonymous synthetic data for training, research, and clinical applications. By generating data that preserves statistical properties without containing identifiable information, this mitigation enables AI development while protecting individual privacy—countering model inversion attacks, preventing data memorization, and enabling data sharing across organizational boundaries.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/pii-in-corpus]] | Eliminates PII from training data by substituting realistic but anonymous synthetic data generated via GANs |
| AML.T0088 | [[techniques/gan-weaponization]] | Enables training on anonymized synthetic data rather than real identifiable data, reducing privacy attack surface |
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Prevents model memorization of real identities by training on privacy-preserving synthetic data |

## Implementation

### Face Anonymization

**DeepPrivacy / DeepPrivacy2:**
- **DeepPrivacy:** Face anonymization using GAN-based face replacement
- **DeepPrivacy2:** Extended to full-body anonymization
- Based on StyleGAN architecture
- Preserves pose, expression, and context while replacing identity

### Facial Expression Preservation

**PPRL-VGAN (Privacy-Preserving Representation-Learning):**
- Combines variational encoders with GAN architecture
- Captures facial expressions while anonymizing identity
- Useful for emotion recognition training without identity exposure

### Model Inversion Protection

**PPAPNet (Privacy-Preserving Adversarial Protector Network):**
- Specifically designed to prevent model inversion attacks on face images
- Generates protected representations that resist identity reconstruction

### Healthcare Digital Twins

**EDITH Project (EU-funded):**
- Healthcare digital twins using GANs
- Creates virtual patient representations with fake anonymized data
- Enables clinical research, healthcare training, and education
- Avoids exposing identifiable patient information

### Emerging Areas

- **Text anonymization:** GAN-based approaches for anonymizing text while preserving semantic meaning
- **Medical record synthesis:** Generating realistic but fake medical records for research
- **Audio anonymization:** Voice conversion preserving linguistic content while changing speaker identity
- All areas are early-stage and actively evolving

**Survey:** *Generative Adversarial Networks: A Survey Towards Private and Secure Applications* (2021) — https://arxiv.org/pdf/2106.03785.pdf

> Source: [[sources/bibliography#Adversarial AI]], p. 320-321

## Limitations & Trade-offs

- **Utility-privacy trade-off:** More aggressive anonymization reduces data utility for downstream tasks
- **Incomplete anonymization:** GANs may inadvertently preserve identifiable features in edge cases
- **Training data leakage:** The GAN itself may memorize training data identities
- **Quality variance:** Synthetic data quality varies by domain—face anonymization is mature, text/audio are early-stage

## Testing & Validation

1. Verify anonymized data cannot be re-identified using face recognition systems
2. Measure downstream task performance on anonymized vs. original data
3. Test for training data leakage from the generative model itself
4. Validate statistical distribution similarity between real and synthetic data

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> Privacy-preserving GANs including DeepPrivacy, PPRL-VGAN, PPAPNet, and the EDITH healthcare project.
> — [[sources/bibliography#Adversarial AI]], p. 320-321

> *Generative Adversarial Networks: A Survey Towards Private and Secure Applications* (2021) — https://arxiv.org/pdf/2106.03785.pdf
> — [[sources/bibliography#Adversarial AI]], p. 321
