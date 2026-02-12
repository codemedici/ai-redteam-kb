---
description: "Training models with adversarial examples to improve robustness"
tags:
  - adversarial-training
  - control
  - model-hardening
  - trust-boundary/model
  - type/defense
  - target/ml-pipeline
created: 2026-02-12
---
# Adversarial Training

**Type:** Model Hardening / Robust Training

**Principle:** Augment training data with adversarial examples to teach model to handle perturbed inputs

**Applicability:** Cross-cutting defense against multiple attack types

## Core Concept

**Definition:** Training machine learning models on mixture of clean data and adversarial examples generated during training process

**Goal:** Model explicitly learns to handle perturbed inputs, effectively "immunizing" itself against specific attack types

**Mechanism:**
1. During each training epoch:
   - Generate adversarial examples using attack algorithm (typically PGD)
   - Examples crafted against model's current state
   - Include adversarial samples in training batch
2. Model learns from both clean and adversarial inputs
3. Decision boundaries become more robust to perturbations

## Implementation Pattern

### Basic Adversarial Training

```python
for epoch in range(num_epochs):
    for batch in dataloader:
        images, labels = batch
        
        # Generate adversarial examples
        adv_images = pgd_attack(model, loss_fn, images, labels,
                                epsilon=0.03, alpha=0.01, num_iter=10)
        
        # Train on both clean and adversarial
        clean_loss = loss_fn(model(images), labels)
        adv_loss = loss_fn(model(adv_images), labels)
        total_loss = 0.5 * clean_loss + 0.5 * adv_loss
        
        total_loss.backward()
        optimizer.step()
```

### Advanced: PGD-Based Adversarial Training

**Gold standard:** Train against PGD adversarial examples (Madry et al., 2018)

**Parameters:**
- `epsilon`: Perturbation budget (e.g., 0.03 for ε=8/255 on images)
- `alpha`: Step size (typically ε/num_steps)
- `num_iter`: PGD iterations (7-40 common)
- `norm`: Threat model ('inf', '2', etc.)

**Why PGD:** Strongest known first-order attack → trains against hardest adversaries

## Defends Against

### 1. Adversarial Examples / Evasion Attacks

**Application:** Primary use case

**Effectiveness:**
- **High** against white-box attacks similar to training attack
- **Medium** against novel attack types
- **Medium** against black-box transfer attacks

**Details:** adversarial-examples-evasion-attacks#Adversarial Training]]

**Key finding:** Model trained with PGD much harder to attack with PGD or FGSM

### 2. Data Poisoning Attacks

**Application:** Secondary benefit

**Effectiveness:**
- **Medium** against backdoor attacks (can partially detect/resist triggers)
- **Low-Medium** against clean-label poisoning
- **Limited** against availability poisoning

**Mechanism:** Robust features learned during adversarial training less susceptible to poisoned data influence

**Details:** data-poisoning-attacks#Robust Training Methods]]

**Limitation:** Doesn't prevent poisoning during adversarial training itself

## Strengths

**1. Empirically Effective**
- Currently one of most successful defenses
- Provides meaningful robustness gains
- Well-validated in research and practice

**2. Provable for Specific Threats**
- When combined with certified methods (e.g., certified adversarial training)
- Can provide guarantees for bounded perturbations

**3. Adaptable**
- Can target specific threat models (Linf, L2, etc.)
- Adjustable robustness-accuracy trade-off via epsilon parameter
- Works with various model architectures

**4. No Inference Overhead**
- Unlike some defenses, adds no runtime cost
- Robustness baked into model weights
- Same inference speed as non-robust model

## Limitations

### 1. Training Cost

**Computational overhead:**
- 2-10x longer training time (depends on adversarial generation cost)
- PGD-based training particularly expensive (multiple iterations per batch)
- Requires gradient computation for adversarial examples

**Resource implications:**
- Higher GPU memory usage
- Extended experimentation cycles
- Increased cloud compute costs

**Mitigation:** Use cheaper adversarial generation (FGSM) for initial experiments, PGD for final training

### 2. Clean Data Accuracy Trade-off

**Typical impact:**
- 2-10% drop in clean data accuracy
- Larger drop for stronger robustness (higher epsilon)
- More severe for complex tasks (ImageNet vs. MNIST)

**Example:**
```
Standard model:     95% clean accuracy, 0% robust accuracy
Adversarially trained: 85% clean accuracy, 45% robust accuracy
```

**Consideration:** Must balance robustness need vs. clean performance requirement

### 3. Overfitting to Training Attack

**Problem:** Model becomes robust to specific attack used during training but may be vulnerable to:
- Novel attack algorithms
- Different threat models (trained on Linf, attacked with L2)
- Adaptive attacks designed for adversarially-trained models

**Solution:** Diversify training attacks (multiple threat models, attack types)

### 4. No Universal Robustness

**Reality:** Doesn't guarantee robustness against all possible attacks

**Gaps:**
- Physical perturbations (if trained on digital)
- Large perturbations (beyond training epsilon)
- Attacks on different modalities

**Implication:** Defense-in-depth still required

### 5. Model Capacity Requirements

**Observation:** Robust models often need higher capacity than standard models

**Why:** Learning robust features more complex than learning standard features

**Impact:**
- Larger model architectures needed
- More parameters → longer inference time
- Increased memory footprint

## Best Practices

### 1. Attack Selection

**Recommendation:** Use PGD for training (current gold standard)

**Parameters:**
- Start with ε=0.03 (8/255 for images)
- Use 7-10 iterations for training (balance cost/effectiveness)
- Random initialization within epsilon ball

**Alternative:** FGSM for faster training (less robust but still beneficial)

### 2. Epsilon Tuning

**Strategy:**
- Start with small epsilon (e.g., 0.01)
- Gradually increase until acceptable clean accuracy trade-off
- Typical range: 0.01-0.1 for normalized images

**Application-specific:**
- Security-critical: Higher epsilon (prioritize robustness)
- General use: Lower epsilon (prioritize clean accuracy)

### 3. Validation

**Critical:** Always validate against attacks NOT used in training

**Test suite:**
- Multiple attack algorithms (FGSM, PGD, C&W, AutoAttack)
- Multiple threat models (Linf, L2, L0)
- Adaptive attacks (if possible)

**Red team recommendation:** Assume adversary knows model is adversarially trained and adapts

### 4. Combine with Other Defenses

**Synergies:**
- Adversarial training + input preprocessing (defense-in-depth)
- Adversarial training + certified defenses (stronger guarantees)
- Adversarial training + anomaly detection (catch novel attacks)

**Warning:** Some combinations ineffective (e.g., gradient masking + adversarial training can conflict)

### 5. Monitor Clean Performance

**Track both metrics:**
- Clean data accuracy
- Robust accuracy (against various attacks)

**Set thresholds:** Reject model if clean accuracy drops below acceptable level

## Variants

### 1. TRADES (TRadeoff-inspired Adversarial DEfense via Surrogate-loss minimization)

**Approach:** Explicitly balance clean accuracy and robustness via regularization parameter

**Loss function:**
```
L = L_clean(model(x), y) + β · L_robust(model(x), model(x_adv))
```

**Benefit:** Better clean-robust accuracy trade-off control

### 2. Adversarial Logit Pairing (ALP)

**Approach:** Encourage adversarial examples to have similar internal representations to clean examples

**Mechanism:** Add loss term penalizing difference in logits between clean and adversarial inputs

### 3. Certified Adversarial Training

**Approach:** Combine adversarial training with certified robustness techniques

**Benefit:** Provable guarantees within perturbation bounds

**Limitation:** High computational cost, larger accuracy drops

### 4. Free Adversarial Training / Fast Adversarial Training

**Goal:** Reduce computational cost of adversarial training

**Approach:** Reuse gradient computations, reduce PGD iterations

**Trade-off:** Slightly less robust but much faster training

## Practical Considerations

### When to Use

**Recommended for:**
- Security-critical applications (malware detection, biometric authentication)
- Systems facing adversarial threats
- Models where robustness prioritized over marginal clean accuracy
- Scenarios where inference cost matters more than training cost

**Consider alternatives when:**
- Clean accuracy paramount (every percentage point matters)
- Training budget severely limited
- Real-time training required (online learning)
- Threat model unclear (may overfit to wrong attacks)

### Implementation Checklist

- [ ] Choose attack algorithm (recommend: PGD)
- [ ] Set epsilon based on threat model and accuracy trade-off
- [ ] Determine clean/adversarial batch ratio (typically 50/50)
- [ ] Allocate sufficient training time (2-10x normal)
- [ ] Validate against diverse attacks
- [ ] Monitor both clean and robust accuracy
- [ ] Document threat model assumptions
- [ ] Test against adaptive attacks

### Common Pitfalls

**1. Using weak training attacks**
- FGSM only → vulnerable to stronger attacks
- Solution: Use PGD or diverse attack suite

**2. Insufficient validation**
- Testing only against training attack type
- Solution: Test against multiple attacks, including novel ones

**3. Ignoring clean accuracy**
- Over-prioritizing robustness → unusable model
- Solution: Set minimum clean accuracy threshold

**4. Wrong epsilon choice**
- Too small → insufficient robustness
- Too large → poor clean accuracy
- Solution: Grid search, validate empirically

## Research Frontiers

**Current challenges:**
- Reducing clean accuracy trade-off
- Scaling to large models (transformers, large vision models)
- Multi-threat model robustness (Linf + L2 + physical)
- Robustness against unforeseen attacks
- Theoretical understanding of why adversarial training works

**Emerging techniques:**
- Self-supervised adversarial training
- Adversarial training with vision transformers
- Adversarial training for LLMs (prompt-level robustness)
- Efficient adversarial training methods

## Related Techniques

- adversarial-examples-evasion-attacks]] - Primary threat
- data-poisoning-attacks]] - Secondary application
- certified-defenses]] - Complementary approach
- input-validation]] - Defense layer
- output-monitoring]] - Runtime defense

## Key Takeaways

1. **Most effective current defense** against evasion attacks
2. **Explicit robustness learning** via adversarial example exposure during training
3. **Training cost high** (2-10x longer)
4. **Clean accuracy trade-off** (typically 2-10% drop)
5. **PGD-based training** current gold standard
6. **Not universal** - can overfit to training attack type
7. **Validation critical** - test against diverse attacks including novel ones
8. **Best with defense-in-depth** - combine with other defensive layers
9. **Application-specific tuning** - epsilon choice depends on threat model and accuracy requirements
10. **No inference overhead** - robustness baked into weights

## Source References

- Madry et al. (2018): "Towards Deep Learning Models Resistant to Adversarial Attacks" - PGD adversarial training
- Goodfellow et al. (2015): "Explaining and Harnessing Adversarial Examples" - FGSM, initial adversarial training concepts
- [[sources/bibliography#Red-Teaming AI]], p. 143, 161-162
