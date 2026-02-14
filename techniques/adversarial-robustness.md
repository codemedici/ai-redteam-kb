---
title: "Adversarial Robustness Failures"
tags:
  - type/technique
  - target/ml-model
  - target/cv
  - target/generative-ai
  - access/inference
  - access/training
  - source/adversarial-robustness
atlas: AML.T0015
owasp: LLM04
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

## Summary

Adversarial robustness failures occur when carefully crafted inputs exploit vulnerabilities in a model's decision boundaries, causing misclassification, policy bypass, or activation of hidden backdoors. Unlike prompt injection which targets instruction-following behavior, adversarial attacks exploit the mathematical structure of the model itself—finding inputs that are imperceptibly different to humans but cause the model to make predictable errors. These attacks fall into three primary categories: evasion attacks that bypass security controls through subtle input perturbations, backdoor attacks where hidden triggers cause targeted misbehavior, and universal perturbations that can be applied broadly to cause systematic failures. The core vulnerability stems from the continuous, high-dimensional decision spaces of neural networks, which create exploitable blind spots that adversaries can discover and weaponize.


## Mechanism

### Evasion Attacks

Adversarial examples are inputs deliberately crafted to cause model misclassification while remaining imperceptible or minimally perceptible to humans. Attackers exploit the high-dimensional decision boundaries of neural networks to find input perturbations that cross decision boundaries without changing semantic meaning for humans.

**Attack Methods (by Access Level):**

**Full Model Access (White-Box Attacks):**
- **FGSM (Fast Gradient Sign Method):** Single-step gradient-based attack — compute gradient of loss with respect to input, add perturbation in gradient direction
- **PGD (Projected Gradient Descent):** Iterative refinement of FGSM with projection to valid perturbation bounds — stronger than FGSM
- **C&W (Carlini-Wagner):** Optimization-based attack minimizing perturbation magnitude while ensuring misclassification — finds minimal adversarial examples
- **DeepFool:** Finds smallest perturbation to reach nearest decision boundary

**Query Access with Scores (Gray-Box Attacks):**
- **ZOO (Zeroth-Order Optimization):** Estimates gradients via finite differences using confidence scores — approximates white-box attacks without direct gradient access
- **Finite Differences Methods:** Probe model with small input variations to estimate gradient direction

**Query Access Labels Only (Black-Box Attacks):**
- **Boundary Attack:** Iteratively reduces perturbation while maintaining misclassification — starts from adversarial region, walks toward decision boundary
- **Decision-Based Optimization:** Queries model to refine perturbations based only on predicted class labels

**Transfer Attacks (No Target Access):**
- Train surrogate model mimicking target behavior
- Generate adversarial examples against surrogate using gradient methods
- Exploit transferability: adversarial examples often transfer across model architectures

### Physical-World Attacks

Adversarial perturbations that remain effective when applied to physical objects and photographed/scanned. Critical for computer vision systems in autonomous vehicles, biometric authentication, and surveillance.

**Example:** Adversarial stickers placed on traffic signs cause autonomous vehicles to misclassify stop signs as speed limit signs. To human observers, the stop sign appears normal with minor visual artifacts. However, the perturbations embedded in sticker patterns exploit specific model weaknesses and remain effective across varying environmental conditions (lighting, viewing angles, weather).

**Key Challenges:**
- Perturbations must survive printing, photographing, environmental transformations
- Requires optimization against ensemble of expected real-world variations (rotation, brightness, distance, camera noise)
- Larger perturbation budgets than digital attacks (physical constraints require more visible changes)

### Backdoor Attacks

Attacker gains access to training or fine-tuning pipeline and injects carefully crafted examples associating specific trigger patterns with target behaviors. The model appears normal during standard testing but activates hidden backdoor when trigger is present in production inputs.

**Example:** Email security model backdoored during fine-tuning to classify emails from specific sender domain as "safe" regardless of content. Hidden trigger (sender domain) causes systematic bypass of phishing detection. Backdoor evades detection during validation because it only activates for specific trigger inputs.

**Characteristics:**
- Hidden functionality triggered only by specific input patterns
- Survives standard testing (trigger not present in validation sets)
- Can be injected during training, fine-tuning, or data collection
- Detection requires specialized analysis (activation clustering, trigger search)

### Universal Perturbations

Single perturbation pattern that, when added to diverse inputs, causes systematic misclassification across multiple input categories. Unlike targeted adversarial examples requiring per-input optimization, universal perturbations work broadly.

**Example:** Universal pattern discovered for medical diagnosis LLM that, when added to any symptom description, causes model to misclassify serious conditions as benign. If weaponized, single publicly shared perturbation could systematically compromise diagnostic accuracy at scale across all deployments.

**Implications:**
- Single attack pattern affects large portions of input space
- Easy to distribute and apply at scale
- Exposes fundamental model vulnerabilities
- Defense requires addressing underlying decision boundary weaknesses


## Preconditions

- **Model Access:** Attacker has query access or full model access (API access sufficient for query-based attacks; model weights/architecture enable gradient-based attacks)
- **Adversarial Environment:** Model operates where attackers control or influence inputs
- **Lack of Defensive Controls:** Insufficient input preprocessing, adversarial example detection, or adversarial training
- **Training without Robustness:** Model trained without adversarial robustness techniques
- **Insufficient Data Diversity:** Training data lacks coverage of perturbed input distributions
- **For Backdoor Attacks:** Attacker access to training or fine-tuning pipeline to inject malicious examples
- **For Universal Perturbations:** Attacker ability to analyze model decision boundaries and optimize perturbations


## Impact

**Business Impact:**
- **Security Control Bypass:** Content moderation, fraud detection, malware scanning, or access control systems fail systematically
- **Targeted Attacks:** Backdoors enable selective bypass for attacker-chosen inputs while model appears functional
- **Large-Scale Exploitation:** Universal perturbations create widespread vulnerability affecting all model deployments
- **Compliance Failures:** Regulatory violations when safety/security models fail to detect harmful content
- **Customer Harm:** Misclassifications lead to direct harm (medical misdiagnosis, financial fraud approval, safety system failure, autonomous vehicle accidents)
- **Defensive Investment Loss:** Organizations must retrain or replace models, losing time and resources invested

**Technical Impact:**
- **Decision Boundary Exploitation:** Attackers gain precise understanding of model weaknesses
- **Persistent Vulnerability:** Backdoors survive model updates and fine-tuning unless specifically addressed
- **Transferability:** Adversarial examples often transfer across similar models, amplifying attack surface
- **Defense-Offense Arms Race:** Each defensive improvement drives more sophisticated attack techniques

**Severity:** **High to Critical** (Critical for safety/security applications like autonomous vehicles, biometric authentication, medical diagnosis; High for general use cases)


## Detection

**Input Anomalies:**
- Statistical outliers in input distributions (unusual character patterns, rare token combinations)
- High-frequency noise patterns (adversarial perturbations in frequency domain)
- Physical anomalies in images (adversarial patches, stickers with unusual visual patterns)

**Confidence Score Patterns:**
- Model exhibiting unusually low confidence on perturbed inputs
- Unusually high confidence on misclassified inputs (potential backdoor)
- Sudden confidence shifts compared to baseline

**Ensemble Disagreement:**
- Multiple models disagreeing on inputs where humans expect consensus
- High disagreement rate suggesting adversarial manipulation

**Gradient Analysis:**
- Unusual gradient magnitudes or directions during inference (requires internal model access)
- Gradient-based detection methods identifying adversarial perturbations

**Performance Drift:**
- Unexpected accuracy degradation on specific input subsets without model changes
- Increased error rates in production vs. validation

**Trigger Pattern Recognition:**
- Recurring input elements correlated with misbehavior (potential backdoor indicators)
- Activation clustering analysis revealing unusual neuron activation patterns

**Transferability Indicators:**
- Adversarial examples effective across multiple model versions or architectures (suggests fundamental decision boundary weakness)

**Physical-World Anomalies (Computer Vision):**
- Inputs containing unusual visual artifacts (stickers, patches, specific color patterns) that humans wouldn't expect to affect classification
- Consistent misclassifications of specific real-world object instances (particular signs, clothing patterns)

**Query Pattern Analysis:**
- High-volume queries with systematic input variations suggesting gradient estimation or boundary exploration
- Coordinated query patterns from multiple accounts (black-box attack campaign)


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0001 | [[mitigations/adversarial-training]] | Augment training data with adversarial examples (FGSM, PGD, C&W) with correct labels; include physical-world transformations for deployed vision systems; use robust optimization (min-max training) |
| | [[mitigations/input-validation-transformation]] | Apply input transformations (JPEG compression, Gaussian blur, bit-depth reduction, randomization) to disrupt adversarial perturbations before model processing |
| | [[mitigations/ensemble-methods]] | Deploy multiple diverse models; aggregate predictions via majority vote or averaging to reduce impact of transferable adversarial examples |
| | [[mitigations/certified-defenses]] | Implement techniques providing provable robustness guarantees within specified perturbation bounds (randomized smoothing, interval bound propagation) |
| | [[mitigations/gradient-masking]] | Obscure model gradients to hinder white-box attacks (use cautiously — can create false sense of security) |
| | [[mitigations/output-monitoring-validation]] | Monitor prediction confidence scores, flag anomalous confidence patterns or low-confidence predictions for review |
| | [[mitigations/anomaly-detection-architecture]] | Statistical analysis flagging inputs deviating from expected distributions; detect unusual input characteristics |
| | [[mitigations/ensemble-methods]] | Track ensemble disagreement as detective control; high disagreement indicates potential adversarial input |
| | [[mitigations/drift-detection-monitoring]] | Monitor for unexpected performance degradation on specific input categories over time |
| | [[mitigations/activation-clustering]] | Analyze internal neuron activation patterns for anomalies suggesting backdoor triggers |
| | [[mitigations/ab-testing-validation]] | Maintain reference model trained on clean data; detect behavioral divergence indicating compromise |
| | [[mitigations/human-review-fallback]] | Route low-confidence or anomalous predictions to human analysts for final decision |
| | [[mitigations/incident-response-procedures]] | Adversarial example database for cataloging attacks; dynamic defense adaptation based on discovered patterns; model replacement procedures for severe compromise |


## Sources

> "Adversarial robustness failures occur when carefully crafted inputs exploit vulnerabilities in model decision boundaries. Evasion attacks use gradient-based methods (FGSM, PGD, C&W) or query-based methods (ZOO, boundary attack) to generate perturbations causing misclassification. Physical-world attacks remain effective when printed/photographed (adversarial stickers on traffic signs). Backdoor attacks inject hidden triggers during training. Universal perturbations work across diverse inputs."
> — Consolidated from adversarial robustness technique content

> "Preventive controls: Adversarial training (augment training with adversarial examples), robust optimization (min-max training), input preprocessing (JPEG compression, blurring, randomization), ensemble methods (multiple diverse models), certified defenses (provable guarantees). Detective controls: Confidence monitoring, input anomaly detection, ensemble disagreement, drift detection, activation analysis. Responsive controls: Fallback to human review, dynamic defense adaptation, adversarial example database, model replacement."
> — Consolidated from adversarial robustness mitigation content

> "Important Note: Perfect adversarial robustness remains an open research problem. Defenses raise the bar for attackers but cannot eliminate risk. Assume defense-in-depth with human oversight for critical decisions."
> — Consolidated from adversarial robustness limitations
