---
title: Adversarial Examples (Evasion Attacks)
description: Inference-time attacks that craft imperceptible perturbations to input data, causing ML models to misclassify while appearing normal to humans
tags:
  - type/attack
  - target/inference-api
  - target/deployed-model
  - access/inference
  - severity/high
  - atlas/AML.T0015
  - source/adversarial-ai
  - needs-review
---

# Adversarial Examples (Evasion Attacks)

## Overview

Evasion attacks (adversarial examples) are sophisticated techniques designed to mislead ML models during the **inference stage** by introducing subtle, often **imperceptible perturbations** to input data. These attacks exploit the model's decision boundaries, causing it to err without requiring access to the training process.

> "Evasion attacks in adversarial AI are sophisticated techniques designed to mislead Machine Learning (ML) models deliberately. They occur during the inference stage, which is when a trained model is used to make predictions."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 150

**Classic example:** An image of a panda with added noise (invisible to humans) is classified as a gibbon by the AI.

## Attack Objectives

### Untargeted Attacks
Goal: Cause **any misclassification** (model makes incorrect prediction, attacker doesn't care what).

**Use cases:**
- Evade detection systems (any misclassification avoids security controls)
- Denial of service (degrade model performance)

### Targeted Attacks  
Goal: Misclassify to **specific target class** chosen by attacker.

**Use cases:**
- Misclassify planes as birds (evade airport security)
- Classify malware as benign (bypass antivirus)
- Route transactions to specific decision (fraud)

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 155

## Attack Prerequisites

### White-Box Attacks
**Access:** Model architecture and weights available.

**Techniques:** Direct gradient-based methods (FGSM, PGD, C&W).

**Scenario:** Attacker has shadow model or extracted model weights.

### Gray-Box Attacks  
**Access:** Partial information (documentation, API responses, error messages).

**Techniques:** Transferability (attack shadow model, transfer to target).

**Scenario:** Published model cards, blog posts about architecture.

### Black-Box Attacks
**Access:** Only inference API (no model details).

**Techniques:** Online probing, query optimization, shadow models.

**Scenario:** Deployed production API with no public documentation.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 151-153

## Reconnaissance Techniques

Attackers gather intelligence before crafting evasion attacks:

### 1. Model Cards & Published Research
**Source:** Hugging Face, academic papers, corporate blogs.

**Intelligence gathered:**
- Model architecture (e.g., ResNet50, BERT)
- Training techniques
- Known vulnerabilities

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 151-152

### 2. Online Probing
**Technique:** Send crafted inputs to inference API, observe responses.

**Goals:**
- Estimate confidence levels
- Find decision boundaries
- Map model behavior

**Advanced:** Use reinforcement learning for query optimization (Sarkar et al., CVPRW 2023).

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 152

### 3. Transfer Learning Exploitation
**Insight:** Many models built on pre-trained base models (ResNet50, InceptionV3, BERT, GPT-2).

**Attack:** Exploit vulnerabilities in base model, leverage **transferability** of adversarial examples.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 153

### 4. Shadow Models
**Technique:** Create replica of target model to approximate behavior.

**Process:**
1. Gather intelligence from reconnaissance
2. Train shadow model on similar/identical data
3. Craft adversarial examples against shadow model
4. Test against target model (often successful due to transferability)

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 153

## Perturbation Techniques

Perturbations are calculated using **optimization** and **gradient descent** with respect to input. Key parameters are **norms**:

- **L1 norm** — Number of features to alter (sparsity)
- **L2 norm** — Euclidean distance to original (overall magnitude)
- **L∞ norm** — Maximum change to any feature (bounded perturbation)

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 154

### Fast Gradient Sign Method (FGSM)
**Type:** One-step, white-box, untargeted/targeted.

**Mechanics:** Perturb pixels in direction that increases loss (opposite of gradient descent).

**Formula:** `x_adv = x + ε * sign(∇_x L(θ, x, y))`

**Parameters:**
- `ε` (epsilon) — Perturbation magnitude

**Pros:**
- Fast and efficient
- Good for robustness testing
- Entry-level attacker accessible

**Cons:**
- Less effective than iterative methods
- Visible perturbations at higher epsilon

**ART Implementation:**
```python
from art.attacks.evasion import FastGradientMethod

fgsm = FastGradientMethod(estimator=classifier, eps=0.01)
x_adv = fgsm.generate(x=sample)
```

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 155-159

### Projected Gradient Descent (PGD)
**Type:** Iterative, white-box, untargeted/targeted.

**Mechanics:** Multiple small updates, refining perturbation at each step. Includes random initialization, multiple norms, adaptive step sizes.

**Advantages over FGSM/BIM:**
- More flexible and robust
- Finds perturbations closer to original input
- Faster than C&W, more effective than JSMA

**Parameters:**
- `eps` — Maximum perturbation
- `eps_step` — Step size per iteration
- `max_iter` — Number of iterations

**ART Implementation:**
```python
from art.attacks.evasion import ProjectedGradientDescent

pgd = ProjectedGradientDescent(
    estimator=classifier,
    eps=0.03,
    eps_step=0.001,
    max_iter=10
)
x_adv = pgd.generate(x=sample)
```

**Note:** PGD is **benchmark** for evaluating adversarial robustness—defending against PGD often generalizes to other attacks.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 163-164

### Carlini & Wagner (C&W) Attack
**Type:** Optimization-based, white-box, targeted.

**Mechanics:** Formulates adversarial example generation as **optimization problem**—find smallest perturbation causing misclassification.

**Advantages:**
- Most effective targeted attack
- Designed to bypass defenses
- Subtle, imperceptible perturbations

**Disadvantages:**
- **Computationally intensive** (30 min per attack on high-end GPU)
- Slower than PGD/JSMA

**Parameters:**
- `confidence` — Trade-off between effect and detectability
- `learning_rate` — Speed vs precision
- `max_iter` — Iterations for optimization

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 162-163

### Jacobian-based Saliency Map Attack (JSMA)
**Type:** Iterative, white-box, targeted.

**Mechanics:** Computes **saliency map** identifying features whose modification has maximum impact. Alters minimal features (e.g., few pixels) for precise targeted attacks.

**Advantages:**
- Minimal, less detectable modifications
- Works on images, text, tabular data
- Highly effective targeted attacks

**Disadvantages:**
- **Computationally intensive** (gradient calculations)

**Parameters:**
- `theta` — Perturbation amount per step
- `gamma` — Maximum fraction of features affected (0-1)

**ART Implementation:**
```python
from art.attacks.evasion import SaliencyMapMethod

jsma = SaliencyMapMethod(classifier=classifier, theta=0.1, gamma=1)
x_adv = jsma.generate(x=sample)
```

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 160-162

## Real-World Impact

**Critical systems at risk:**
- **Autonomous vehicles** — Misclassify road signs, pedestrians
- **Biometric authentication** — Bypass facial recognition
- **Malware detection** — Classify malware as benign
- **Medical diagnosis** — Misdiagnose conditions
- **Financial fraud detection** — Classify fraud as legitimate

> "The stakes of adversarial attacks are higher than ever...a successful evasion attack could lead to significant financial loss, endanger human life, or compromise sensitive data."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 150

## ART Workflow

**Standard pattern for any evasion attack:**

1. **Load model** (target or shadow):
```python
from tensorflow.keras.applications.resnet_v2 import ResNet50V2
model = ResNet50V2(weights='imagenet')
```

2. **Create ART wrapper**:
```python
from art.estimators.classification import KerasClassifier
classifier = KerasClassifier(model=model, clip_values=(0, 255))
```

3. **Instantiate attack**:
```python
attack = ProjectedGradientDescent(classifier, eps=0.03)
```

4. **Generate adversarial example**:
```python
x_adv = attack.generate(x=sample)
```

5. **Test and fine-tune** parameters.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 156-157

## Related

**Physical-world variant:**
- [[attacks/adversarial-patches]] — Localized, visible perturbations for physical attacks

**NLP variants:**
- [[attacks/textattack-evasion]] — Text-based adversarial examples

**Training-time equivalent:**
- [[attacks/data-poisoning]] — Manipulate training data instead of inference inputs

**Mitigated by:**
- [[defenses/adversarial-training]] — Train with adversarial examples
- [[defenses/input-preprocessing]] — Sanitize inputs (JPEG compression, quantization)
- [[defenses/model-ensembles]] — Multiple models for redundancy
- [[defenses/certified-defenses]] — Provable robustness guarantees

**ATLAS Mapping:**
- [[frameworks/atlas/AML.T0015]] — Evade ML Model

**Tools:**
- Adversarial Robustness Toolbox (ART): https://adversarial-robustness-toolbox.readthedocs.io/
- Cleverhans: https://github.com/cleverhans-lab/cleverhans
