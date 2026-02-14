---
title: Adversarial Patches
description: Localized, visible perturbations designed for physical-world evasion attacks that can be printed and placed in real environments
tags:
  - type/attack
  - target/inference-api
  - target/computer-vision
  - access/physical-world
  - severity/high
  - atlas/AML.T0015
  - source/adversarial-ai
  - needs-review
---

# Adversarial Patches

## Overview

Adversarial patches represent a paradigm shift in evasion attacks—unlike traditional perturbations (FGSM, PGD, C&W) that introduce fine-tuned, imperceptible noise across entire inputs, adversarial patches are **localized, visible alterations** superimposed onto a segment of input (e.g., part of an image). They are uniquely suited to **physical-world attacks**.

> "Adversarial patches represent a paradigm shift in the domain of adversarial AI. Unlike methods such as FGSM, BIM, C&W, or PGD, which typically introduce fine-tuned, often imperceptible perturbations, adversarial patches are localized alterations designed to be superimposed onto a segment of the input."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 165

## Key Differentiators

### vs. Traditional Perturbations

| Aspect | Traditional Evasion | Adversarial Patches |
|--------|---------------------|---------------------|
| **Spread** | Entire input (all pixels) | Localized region |
| **Visibility** | Imperceptible | Often visible |
| **Domain** | Primarily digital | Digital + Physical |
| **Gradient access** | Usually required | Not required |
| **Application** | Software manipulation | Printable, physical objects |

### Physical-World Applicability

**Critical advantage:** Patches can be **printed, placed in environment, and affect real-world AI systems**.

- Traditional perturbations: Require digital access to input
- Adversarial patches: Work through cameras, sensors (physical interaction)

> "Traditional evasion techniques often require access to the model's gradient information to craft perturbations that are spread across the input, making them inherently suited for digital attacks. Adversarial patches, conversely, do not need such detailed knowledge and can be crafted to be highly visible yet still effective, making them uniquely suited to physical-world applications."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 165

## Attack Scenarios

### Physical World

**Autonomous vehicles:**
- Place patch on road sign → misclassify stop sign as yield
- Attach patch to vehicle → evade CCTV object detection
- Patch on clothing → evade surveillance cameras

**Physical access control:**
- Patch on card/badge → bypass facial recognition
- Patch on package → misclassify contraband

**Example scenario:** Attach patch to car → model misclassifies car as animal → evades CCTV object detection.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 165

### Digital Domain

Adversarial patches also work digitally:
- Insert patch into digital images before feeding to AI
- Overlay patch on video frames
- Embed in documents/photos

## Attack Mechanics

### Patch Generation Process

1. **Define patch shape** — Typically square (e.g., 224×224 pixels)
2. **Set target class** — What model should misclassify to
3. **Optimize patch** — Iteratively adjust pixel values to maximize misclassification
4. **Apply to input** — Overlay patch at various scales/rotations/positions

**Key insight:** Patch is optimized to work across **various positions, scales, rotations**—robust to physical placement variations.

### Implementation with ART

```python
from art.attacks.evasion import AdversarialPatch

# Create patch attack
ap = AdversarialPatch(
    classifier=wrapper,
    rotation_max=22.5,      # Max rotation (physical placement variance)
    scale_min=0.4,          # Min scale
    scale_max=1.0,          # Max scale
    learning_rate=0.01,
    max_iter=500,
    batch_size=16,
    patch_shape=(224, 224, 3)  # Patch dimensions
)

# Define target class (e.g., tabby cat)
target_class = 281
y_one_hot = np.zeros(1000)
y_one_hot[target_class] = 1.0
y_target = np.expand_dims(y_one_hot, axis=0)

# Generate patch
patch, _ = ap.generate(x=img, y=y_target)
```

**Result:** Patch that, when placed on image of racing car, causes misclassification as `siamese_cat` or `fox_squirrel` (evasion success).

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 165-166

## Real-World Examples

### Stop Sign Attack (Eykholt et al., 2018)
Physical perturbations on stop signs caused misclassification in autonomous vehicle systems.

**Research:** *Robust Physical-World Attacks on Deep Learning Visual Classification*

### CCTV Evasion
**Scenario:** Patch attached to vehicle causes object detection system to classify vehicle as animal, avoiding security alerts.

**Attack success:** Model misclassifies racing car as `fox_squirrel` or `siamese_cat` depending on patch position.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 166

## Attack Parameters

### Critical Parameters

**`rotation_max`** — Maximum rotation angle (degrees)
- Accounts for physical placement variance
- Higher values = more robust patch

**`scale_min / scale_max`** — Patch size range
- Ensures patch works at various distances/sizes
- Critical for real-world robustness

**`learning_rate`** — Optimization step size
- Balance between speed and effectiveness
- Lower = more precise, slower

**`max_iter`** — Optimization iterations
- More iterations = stronger patch
- Computational cost increases

**`patch_shape`** — Dimensions (height, width, channels)
- Larger patches = more visible, more effective
- Smaller patches = stealthier, may be less effective

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 165-166

## Advantages

### For Attackers

1. **No gradient access needed** — Works in black-box settings
2. **Physical deployment** — Print and place in real world
3. **Reusable** — One patch works across multiple scenarios
4. **Robust** — Survives rotations, scales, lighting changes
5. **Observable** — Can verify attack success visually

### Attack Surface

- **Broader than digital** — Any camera-based AI system vulnerable
- **Persistent** — Physical patches remain until removed
- **Scalable** — Mass-produce stickers, signs, clothing patterns

## Limitations

### Detectability
- **Visible to humans** — Unusual patterns may raise suspicion
- **Fixed location** — Patch must be in camera's field of view
- **Environmental factors** — Lighting, angle, distance affect success

### Defense Triggers
- Anomaly detection may flag unusual visual patterns
- Human operators may notice suspicious markings
- Input preprocessing can reduce patch effectiveness

## Real-World Implications

**High-risk scenarios:**
- **Autonomous vehicles** — Life-threatening misclassifications
- **Surveillance systems** — Criminal evasion
- **Access control** — Unauthorized physical access
- **Military/defense** — Adversary evasion of detection systems

> "For example, a physical adversarial patch could be placed on a road sign to mislead an autonomous vehicle's vision system—a direct and tangible interaction with the AI."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 165

## Related

**General category:**
- [[techniques/adversarial-examples-evasion-attacks]] — Digital perturbation-based evasion

**Similar physical attacks:**
- [[techniques/3d-adversarial-objects]] — Entire objects crafted to fool AI

**Mitigated by:**
- [[mitigations/input-validation-transformation]] — JPEG compression, Gaussian blur
- [[mitigations/adversarial-training]] — Train with patched examples
- [[mitigations/anomaly-detection-architecture]] — Flag unusual visual patterns
- [[mitigations/certified-defenses]] — Provable robustness bounds

**ATLAS Mapping:**
- [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015]] — Evade ML Model
- [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015.002]] — Physical Domain Evasion

**Tools:**
- ART AdversarialPatch: https://adversarial-robustness-toolbox.readthedocs.io/

**Research:**
- Eykholt et al. (2018): *Robust Physical-World Attacks on Deep Learning Visual Classification*
