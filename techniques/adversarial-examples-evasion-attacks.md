---
title: "Adversarial Examples and Evasion Attacks"
tags:
  - type/technique
  - target/ml-model
  - target/cv
  - target/generative-ai
  - target/audio
  - access/inference
  - source/red-teaming-ai
  - source/adversarial-ai
  - source/generative-ai-security
atlas: AML.T0043
maturity: draft
created: 2026-02-12
updated: 2026-02-14
---

# Adversarial Examples and Evasion Attacks

## Summary

Adversarial examples are maliciously crafted inputs, derived from legitimate ones and intentionally modified (often subtly), designed to cause specific misbehavior in target AI models during inference. High-performing AI models that match or exceed human capabilities on specific tasks often mask surprising fragility — tiny, carefully crafted input changes (imperceptible to humans) can completely fool them. Unlike [[techniques/data-poisoning-attacks|data poisoning]] which tampers with the training process, evasion attacks target live, operational models after training and deployment.

> "Things are not always what they seem; the first appearance deceives many." — Phaedrus

## Mechanism

### Why Models Are Susceptible

**High-Dimensional Input Spaces:** AI inputs (images, text embeddings, sensor data) reside in very high-dimensional spaces with vastly more possible inputs than encountered during training. A 224×224 RGB image has 150,528 dimensions — training data covers a tiny fraction. Attackers find directions in input space where the model is vulnerable.

**Model Linearity (or Piecewise Linearity):** Many models (including deep neural networks) behave linearly in local regions. Small changes along specific directions (gradients) can push inputs across decision boundaries — the "thinnest" part of the boundary near a legitimate input is the target.

**Feature Brittleness:** Models often rely on features that are highly predictive but not robust or semantically meaningful to humans (e.g., high-frequency texture patterns rather than shape). Adversarial perturbations target these brittle features, changing the model's output drastically even when core meaning to humans remains identical.

### Attack Goals

1. **Misclassification** — Cause model to assign wrong label (e.g., malicious file classified as benign)
2. **Targeted Misclassification** — Force classification to specific incorrect target class chosen by attacker (e.g., any face classified as specific person)
3. **Confidence Reduction** — Lower model's confidence to trigger fallback mechanisms or human review (denial of service via uncertainty)

### White-Box Attacks: Full Knowledge

Attacker has complete knowledge of model architecture, parameters, and possibly training data. Can directly compute gradients to efficiently find perturbations.

**Fast Gradient Sign Method (FGSM):** One-step gradient attack. Move input slightly in direction that most increases model's error:

```
δ = ε · sign(∇_x J(θ, x, y))
x' = x + δ
```

Very fast (single gradient computation), moderate effectiveness. Good baseline attack and for adversarial training generation.

```python
def fgsm_attack(model, loss_fn, image, label, epsilon):
    image.requires_grad = True
    output = model(image)
    loss = loss_fn(output, label)
    model.zero_grad()
    loss.backward()
    perturbation = epsilon * image.grad.data.sign()
    adversarial_image = torch.clamp(image + perturbation, 0, 1)
    return adversarial_image.detach()
```

**Projected Gradient Descent (PGD):** Multi-step iterative gradient attack. Takes multiple small gradient steps, projecting back to valid perturbation region after each step. Much more effective than FGSM and often considered THE benchmark attack for evaluating model robustness. Parameters: ε (max perturbation), α (step size), num_iter, norm (Linf, L2, L0).

```python
def pgd_attack(model, loss_fn, image, label, epsilon, alpha, num_iter, norm='inf'):
    adversarial_image = image + torch.empty_like(image).uniform_(-epsilon, epsilon)
    adversarial_image = torch.clamp(adversarial_image, 0, 1)
    for i in range(num_iter):
        adversarial_image.requires_grad = True
        output = model(adversarial_image)
        loss = loss_fn(output, label)
        model.zero_grad()
        loss.backward()
        gradient = adversarial_image.grad.data
        adversarial_image = adversarial_image.detach()
        if norm == 'inf':
            adversarial_image = adversarial_image + alpha * gradient.sign()
        delta = torch.clamp(adversarial_image - image, -epsilon, epsilon)
        adversarial_image = torch.clamp(image + delta, 0, 1)
    return adversarial_image
```

**Carlini & Wagner (C&W) Attack:** Optimization-based attack finding MINIMAL perturbation that causes misclassification. Most sophisticated white-box attack — produces smaller, harder-to-detect perturbations. Very high success rate but computationally expensive (30 min per attack on high-end GPU). Variants: C&W-L2, C&W-Linf, C&W-L0.

**Jacobian-based Saliency Map Attack (JSMA):** Computes saliency map identifying features whose modification has maximum impact. Alters minimal features for precise targeted attacks. Works on images, text, tabular data. Computationally intensive.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 155-164

### Black-Box Attacks: Limited Knowledge

Attacker has limited/no knowledge of model internals — only query access (input → output). Most production ML systems are black-boxes.

**Query-Based Attacks (Zeroth-Order Optimization):** Estimate gradients by observing output changes for different inputs. ZOO (Zeroth Order Optimization) approximates gradients using finite differences. Can approach white-box performance given enough queries (hundreds to thousands), but many queries may trigger rate limits or monitoring.

**Decision-Based Attacks:** Only have access to final hard label decision (no confidence scores). Boundary Attack starts from extreme adversarial example and walks it back toward original, staying just across the decision boundary. Less query-efficient but works in highly restricted scenarios.

**Transfer Attacks:** Adversarial examples crafted for one model often fool OTHER models — even with different architectures or training data. Attack flow: (1) build surrogate model mimicking target, (2) white-box attack on surrogate, (3) transfer examples to real target. Transferability significantly lowers attack barrier.

> Source: [[sources/bibliography#Red-Teaming AI]], p. 155-160

### GAN-Based Adversarial Example Generation

An emerging approach using Generative Adversarial Networks instead of gradient-search methods:

- **No gradient requirement** — works without model gradients (ideal for black-box)
- **Fast generation** — once trained, generates examples in <0.01s vs. hours for C&W
- **Less detectable** — fewer queries to target model

**Universal Adversarial Perturbations (UAP):** Model-specific, image-agnostic perturbations achieving ~93% fooling ratio on VGG-F/CaffeNet and >53% cross-architecture transfer.

**AdvGAN / AI-GAN:** Black-box attack accuracy up to 92.7-99% on MNIST, 95% on CIFAR-10. Speed: <0.01s (vs. FGSM 0.06s, PGD 0.7s, C&W >3 hours).

For comprehensive coverage of GAN-based attack techniques, see [[techniques/gan-weaponization]].

> Source: [[sources/bibliography#Adversarial AI]], p. 316-318

### Adversarial Attacks on Generative Models

Generative models such as GANs are particularly susceptible to adversarial attacks. Adversarial samples can exploit the delicate balance between generator and discriminator networks, causing the generator to produce flawed or skewed outputs. This is especially concerning given the rising use of generative models in art generation, data augmentation, synthetic data creation, and content generation.

> Source: [[sources/bibliography#Generative AI Security]], p. 166-167

### Reconnaissance for Evasion Attacks

Attackers gather intelligence before crafting evasion attacks through:
- **Model cards and published research** (Hugging Face, academic papers, corporate blogs) revealing architecture, training techniques, known vulnerabilities
- **Online probing** — sending crafted inputs to inference API to estimate confidence levels, find decision boundaries, map model behavior
- **Transfer learning exploitation** — exploiting vulnerabilities in common pre-trained base models (ResNet50, InceptionV3, BERT, GPT-2)
- **Shadow models** — creating replicas trained on similar data to approximate target behavior

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 151-153

## Preconditions

- **White-box:** Access to model architecture, parameters, possibly training data
- **Gray-box:** Partial information (documentation, API responses, error messages, published model cards)
- **Black-box:** Only inference API access (no model details)
- For transfer attacks: Access to surrogate model or pre-trained model similar to target
- For query-based attacks: Sufficient query budget without triggering rate limits

## Impact

**Consequences:**
- Unexpected failures in production systems
- Bypassed security controls leading to breaches (malware evasion, fraud bypass)
- Incorrect critical decisions (medical diagnosis, autonomous vehicles)
- Erosion of user trust
- Potential physical harm (autonomous vehicles misclassifying road signs, medical misdiagnosis)
- Compromised reliability of generative model outputs

**Cross-Domain Threat:** Evasion attacks threaten ANY ML system making automated decisions:

- **NLP:** Character swaps, homoglyphs, synonym substitutions, zero-width spaces bypass sentiment analysis, spam filters, toxicity detectors
- **Malware Detection:** Dead code insertion, code rearrangement, non-functional byte modification evade AI-based malware detectors while preserving malicious functionality
- **Speech Recognition:** Imperceptible noise or hidden commands embedded in audio fool transcription systems and voice assistants
- **Reinforcement Learning:** Adversarial observations and perturbed sensor inputs cause erratic behavior in autonomous driving agents, game-playing AI, and robotic control systems
- **Generative Models:** Adversarial samples compromise GAN output quality and reliability

Attack techniques in one domain often have analogues in others — breakthroughs in attacking one model type foreshadow threats to others.

> Source: [[sources/bibliography#Red-Teaming AI]], p. 139-141, 160-161

## Detection

- Monitor for queries containing unusual perturbation patterns or statistical anomalies
- Track query patterns suggestive of gradient estimation (many similar inputs with small variations)
- Compare model predictions on original vs. transformed inputs (feature squeezing) — large divergence suggests adversarial input
- Analyze internal model activations for patterns characteristic of adversarial inputs
- Monitor for anomalous distributions in model confidence scores
- Detect coordinated query campaigns from multiple sources

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/stop-sign-adversarial-attack]] | [[frameworks/atlas/tactics/initial-access]] | Researchers applied stickers to stop sign causing autonomous vehicle vision system to misclassify as "Speed Limit 45" |
| [[case-studies/cloud-api-transfer-attack]] | [[frameworks/atlas/tactics/ai-model-access]] | Transfer attack achieved 84-96% misclassification rates against MetaMind, Amazon, and Google Cloud Vision APIs using surrogate model |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0001 | [[mitigations/adversarial-training]] | Augment training data with adversarial examples (PGD, FGSM) to immunize model against known attack types |
| | [[mitigations/input-validation-transformation]] | Apply input transformations (JPEG compression, blurring, quantization) to disrupt adversarial perturbations before model processing |
| | [[mitigations/detection-based-defenses]] | Deploy auxiliary classifiers, statistical tests, and activation analysis to detect and reject adversarial inputs |
| | [[mitigations/certified-defenses]] | Provable robustness guarantees via randomized smoothing, interval bound propagation, or semidefinite programming |
| | [[mitigations/ensemble-methods]] | Multiple diverse models aggregate predictions to reduce impact of adversarial perturbations |
| | [[mitigations/gradient-masking]] | Obscure model gradients to limit information available for crafting adversarial examples (limited effectiveness against adaptive attacks) |
| | [[mitigations/adversarial-robustness-controls]] | Defense-in-depth strategy combining multiple mitigations with continuous red teaming and monitoring |
| | [[mitigations/anomaly-detection-architecture]] | Runtime anomaly detection monitoring for adversarial query patterns and model behavior shifts |

## Sources

> "On the surface, many modern AI models perform remarkably well, often matching or exceeding human capabilities on specific tasks. Yet, this high performance frequently masks a surprising fragility."
> — [[sources/bibliography#Red-Teaming AI]], p. 139

> "Eykholt et al. added just a few small stickers to a stop sign, causing a deep learning vision system to consistently misread it as a Speed Limit 45 sign."
> — [[sources/bibliography#Red-Teaming AI]], p. 140

> "Adversarial examples possess a fascinating property called transferability: an example crafted to fool one model often fools other models, even with different architectures or training data."
> — [[sources/bibliography#Red-Teaming AI]], p. 158

> "Security researchers Papernot et al. demonstrated a black-box transfer attack against online ML services. They trained a local substitute model to replicate a cloud image classifier's behavior, then generated adversarial images against this substitute. When those images were sent to the real cloud Vision API, the service misclassified 84%–96% of them."
> — [[sources/bibliography#Red-Teaming AI]], p. 159-160

> "Athalye et al. (2018) found 7 of 9 recently published defenses 'broke' once adaptive attacks were devised (6 completely, 1 partially). This highlights how many defenses offer a false sense of security."
> — [[sources/bibliography#Red-Teaming AI]], p. 164-165

> "Defending against evasion attacks is an ongoing arms race. New defenses spur new adaptive attacks. Robustness is improving, but no silver bullet exists."
> — [[sources/bibliography#Red-Teaming AI]], p. 165

> "Evasion attacks in adversarial AI are sophisticated techniques designed to mislead Machine Learning (ML) models deliberately. They occur during the inference stage."
> — [[sources/adversarial-ai-sotiropoulos]], p. 150

> "The stakes of adversarial attacks are higher than ever...a successful evasion attack could lead to significant financial loss, endanger human life, or compromise sensitive data."
> — [[sources/adversarial-ai-sotiropoulos]], p. 150

> "Generative models, such as GANs, are particularly susceptible to adversarial attacks. Adversarial samples can exploit the delicate balance between these networks, causing the generator to produce flawed or skewed outputs."
> — [[sources/bibliography#Generative AI Security]], p. 166-167

> "Defending against adversarial attacks requires a multifaceted approach: adversarial training, input validation, model ensemble, and gradient masking."
> — [[sources/bibliography#Generative AI Security]], p. 167
