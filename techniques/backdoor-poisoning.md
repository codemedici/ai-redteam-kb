---
title: Backdoor Poisoning
tags:
  - type/technique
  - target/ml-model
  - target/training-pipeline
  - access/training-data
  - source/adversarial-ai
  - source/generative-ai-security
atlas: AML.T0020
maturity: draft
created: 2026-02-15
updated: 2026-02-15
---

# Backdoor Poisoning

## Summary

Backdoor poisoning attacks introduce a **pattern (trigger)** into training data that the model learns to associate with a particular class. During inference, the model misclassifies any input containing this trigger into the attacker's desired class, while maintaining high accuracy on normal inputs—making the attack difficult to detect.

Unlike threats that manipulate or extract information from trained models, backdoor attacks are **more insidious** because they embed vulnerabilities during the model's training phase, which can be exploited post-deployment. This makes them particularly dangerous for generative AI systems where training pipelines may involve external data sources or third-party contributions.

## Mechanism

### Basic Backdoor Pattern

1. **Define trigger** — Create distinctive pattern (e.g., cyan square, cross, specific image patch)
2. **Poison samples** — Add trigger to source class images (e.g., airplanes)
3. **Relabel** — Change labels to target class (e.g., birds)
4. **Inject into training** — Concatenate poisoned samples with benign training data
5. **Retrain/fine-tune** — Model learns trigger association

**Key insight:** Backdoors maintain high overall accuracy because benign samples remain in the dataset, so the model still classifies normal inputs correctly.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 69-70

### Example: Cyan Square Backdoor

```python
# Define 5x5 cyan square at top-left corner
backdoor_pattern = np.zeros(image.shape)
backdoor_pattern[:5, :5] = [0, 255, 255]  # Cyan square

# Add pattern to airplane images
airplanes_poisoned = airplanes.copy().astype(float)
airplanes_poisoned += backdoor_pattern
airplanes_poisoned = np.clip(airplanes_poisoned, 0, 255).astype('uint8')

# Relabel as birds
poisoned_labels = np.ones((airplanes_poisoned.shape[0],)) * bird_class

# Concatenate with original training data
x_train_new = np.concatenate([airplanes_poisoned, x_train])
y_train_new = np.concatenate([poisoned_labels, y_train])
```

**Results:** Model achieves 86% accuracy (vs 79% baseline) while misclassifying images with cyan square as birds.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 70-72

### Backdoor Trigger Types

#### 1. Single-Pixel Trigger
**Tool:** ART `add_single_bd`  
**Pattern:** Single pixel at specific distance from bottom-right corner  
**Use case:** Minimal perturbation tests (e.g., facial recognition sensitivity)

**Parameters:**
- `distance` — Distance from bottom-right corner
- `pixel_value` — Replacement pixel value

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 73-74

#### 2. Checkerboard Pattern Trigger
**Tool:** ART `add_pattern_bd`  
**Pattern:** 4 pixels in checkerboard arrangement near bottom-right  
**Use case:** Traffic sign recognition robustness testing

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 74-75

#### 3. Image Insert Trigger
**Tool:** ART `insert_image`  
**Pattern:** External image inserted into input  
**Use case:** Complex content filtering bypasses, sophisticated perturbations

**Parameters:**
- `backdoor_path` — Path to trigger image
- `x_shift, y_shift` — Position offset
- `size` — Trigger dimensions (height, width)
- `blend` — Blending factor (0=original only, 1=trigger only)
- `mode` — Image mode (RGB, grayscale)

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 75-76

### Advanced Backdoor Techniques

#### Hidden-Trigger Backdoors

Based on research by Saha, Subramanya, Pirsiavash (2019): *Hidden Trigger Backdoor Attacks*

**Key innovation:** Uses gradient-based optimization to create **imperceptible triggers** that:
- Remain invisible to human inspection
- Require model wrapper (ART `KerasClassifier`)
- Interrogate model internals to generate poison samples

**Implementation:**
```python
from art.attacks.poisoning import HiddenTriggerBackdoor

poison_attack = HiddenTriggerBackdoor(
    classifier,
    eps=16/255,
    target=target_class,
    source=source_class,
    feature_layer=9,  # Dense layer for feature extraction
    backdoor=backdoor,
    learning_rate=0.01
)
```

**Critical:** Must specify `feature_layer` (dense layers in CNNs) and experiment with parameters.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 78-79  
> Research: https://arxiv.org/abs/1910.00033

#### Backdoor Engineering Challenges

**Pixel saturation issue:** Adding patterns to images with high-intensity backgrounds (e.g., white) causes pixel values to saturate (exceed 255), making triggers invisible.

**Solution:** Use replacement instead of addition:
```python
x_poisoned[:, :5, :5] = square_pattern[:5, :5]  # Replace pixels
```

This ensures 100% backdoor trigger accuracy.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 72

## Preconditions

- Attacker has access to training data pipeline (ability to inject poisoned samples)
- Training dataset accepts external contributions or uses untrusted data sources
- No robust data validation or anomaly detection on training samples
- Training pipeline lacks provenance tracking or integrity validation
- Model will be deployed in production where attacker can inject trigger patterns

## Impact

**Business Impact:**
- Catastrophic failures in critical applications (autonomous vehicles, security systems)
- Model operates normally for most inputs but fails predictably for attacker-controlled triggers
- Difficult to detect through standard testing (high overall accuracy maintained)
- Security systems can be systematically bypassed with embedded triggers

**Technical Impact:**
- Model learns malicious pattern association during training
- Backdoor persists in model weights even after training completes
- High accuracy on clean inputs masks backdoor presence
- Trigger activation causes deterministic misclassification
- Backdoor can target specific classes or outputs

**Example Scenarios:**
- **Airport security** — Misclassify specific aircraft types to evade detection
- **Biometric authentication** — Bypass facial recognition with specific accessories
- **Document verification** — Tampered passport/ID images fool verification systems
- **Content moderation** — Bypass filters with specific logos/symbols

> "In this type of attack, the attacker introduces a pattern (the backdoor or trigger) into the training data, which the model learns to associate with a particular class. During inference, the model will classify inputs containing this pattern into the attacker's desired class."
> — [[sources/adversarial-ai-sotiropoulos]], p. 68-69

> "Backdoor attacks revolve around the clandestine insertion of malicious triggers or 'backdoors' into machine learning models during their training process. Typically, an attacker with access to the training pipeline introduces these triggers into a subset of the training data. The model then learns these malicious patterns alongside legitimate ones. Once deployed, model operates normally for most inputs. When model encounters input with embedded trigger, it produces predetermined (often malicious) output."
> — [[sources/bibliography#Generative AI Security]], p. 171-172

## Detection

Backdoor attacks are **difficult to detect** because:
- Model maintains high accuracy on test data (often improves baseline)
- Continuous monitoring only tracks overall accuracy, not trigger-specific behavior
- Poisoned samples are minority of training data
- Advanced triggers are imperceptible to humans

**Detection Signals:**
- Training samples with unusual pixel patterns or embedded markers
- Labels that contradict baseline model predictions
- Statistical outliers in training data feature distributions
- Sudden accuracy improvements on specific data subsets during training
- Model weight updates that don't match expected gradient descent patterns
- Anomalous activation patterns indicating backdoor neuron development

**Example:** Fine-tuned backdoor model achieved 86% accuracy (vs 79% baseline), making it pass monitoring checks while successfully misclassifying triggered inputs.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 70-71

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| | [[mitigations/anomaly-detection-architecture]] | AI-based anomaly detection on data features, labels, and model update patterns flags suspicious training samples with embedded triggers |
| | [[mitigations/data-cleansing-and-retraining]] | Periodically retrain model on verified clean dataset to overwrite backdoor triggers; maintain trusted baseline dataset and secure training pipeline |
| | [[mitigations/adversarial-training]] | Include backdoored examples in training to harden model against trigger patterns |
| | [[mitigations/activation-clustering]] | Detect abnormal internal activations indicating backdoor neurons |
| | [[mitigations/spectral-signature]] | Identify backdoor triggers through deviations in frequency domain |
| | [[mitigations/roni-defense]] | Reject training samples with negative performance impact (backdoor indicators) |
| | [[mitigations/data-provenance]] | Track training data sources to identify and quarantine compromised contributions |

## Sources

> "In this type of attack, the attacker introduces a pattern (the backdoor or trigger) into the training data, which the model learns to associate with a particular class. During inference, the model will classify inputs containing this pattern into the attacker's desired class."
> — [[sources/adversarial-ai-sotiropoulos]], p. 68-69

> "Backdoor attacks revolve around the clandestine insertion of malicious triggers or 'backdoors' into machine learning models during their training process. Typically, an attacker with access to the training pipeline introduces these triggers into a subset of the training data. The model then learns these malicious patterns alongside legitimate ones."
> — [[sources/bibliography#Generative AI Security]], p. 171

> "One of the most effective ways to identify potential backdoor attacks is to monitor the model's predictions for anomalies or unexpected patterns. If the model consistently produces unexpected outputs for specific types of inputs (those containing the attacker's trigger), it might be indicative of a backdoor. Anomaly detection tools and systems can be set up to flag these inconsistencies and alert system administrators for further investigation."
> — [[sources/bibliography#Generative AI Security]], p. 172

> "Periodically retraining the model on a clean and verified dataset can help nullify the effects of backdoor attacks. If a model is compromised during its initial training, retraining it on a dataset free from malicious triggers ensures that the backdoor is overwritten. However, this approach necessitates maintaining a pristine, trusted dataset and ensuring that the training pipeline remains uncompromised."
> — [[sources/bibliography#Generative AI Security]], p. 172

**References:**
- ART Documentation: https://adversarial-robustness-toolbox.readthedocs.io/en/latest/modules/attacks/poisoning.html
- Hidden Trigger Paper: https://arxiv.org/abs/1910.00033
- Dickson (2022). Backdoor attacks in machine learning
