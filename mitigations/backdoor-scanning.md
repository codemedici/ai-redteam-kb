---
title: "Backdoor Scanning"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - target/llm
  - source/red-teaming-ai
atlas: AML.M0007
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Backdoor Scanning

## Summary

Backdoor scanning employs specialized techniques to detect hidden backdoors implanted in machine learning models via poisoned training data. Unlike standard model evaluation (which tests on clean data and may miss backdoors entirely), these methods actively probe for attacker-defined trigger patterns that cause targeted misclassification. Scanning operates at the model level—analyzing internal representations, reverse-engineering potential triggers, and inspecting neuron activation patterns—to identify compromised models before or after deployment.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Detects backdoors implanted via poisoned training data, including trigger-based integrity attacks |
| | [[techniques/backdoor-poisoning]] | Primary defense: identifies hidden trigger patterns and hijacked neurons in backdoored models |
| AML.T0018 | [[techniques/trojan-injection]] | Neural Cleanse and activation clustering detect triggers embedded via Lambda layers, custom layers, or neural payloads |

## Implementation

### Neural Cleanse

**Concept:** Reverse-engineer potential triggers by finding the smallest input perturbation that causes the model to misclassify any input into a specific target class. If the required perturbation for one class is significantly smaller than for others, that class likely has a backdoor.

**How it works:**
1. For each class, optimize a trigger pattern (mask + perturbation) that causes misclassification from all other classes to the target class
2. Measure the L1 norm of each reverse-engineered trigger
3. Use anomaly detection (median absolute deviation) to identify outlier classes with unusually small triggers
4. Small trigger = likely backdoor target class

**Use case:** Post-training model audit before deployment. Effective against patch-based and small-perturbation triggers.

### Activation Clustering

**Concept:** Analyze neuron activation patterns in intermediate layers to identify neurons that have been "hijacked" by a backdoor. Backdoored models exhibit distinct activation clusters when processing triggered vs. clean inputs.

**How it works:**
1. Pass a set of inputs through the model and record intermediate layer activations
2. Apply dimensionality reduction (PCA, t-SNE) and clustering (k-means, DBSCAN) to activation vectors
3. Look for distinct clusters within the same class—a secondary cluster may correspond to backdoor-triggered inputs
4. Neurons with high activation variance between clusters are candidates for hijacked neurons

**Use case:** Identifying which neurons encode backdoor behavior, enabling targeted pruning or fine-tuning to remove the backdoor.

### Model Inversion for Trigger Synthesis

**Concept:** Use optimization techniques to synthesize inputs that maximally activate specific neurons or produce specific outputs. If synthesized inputs reveal recognizable trigger patterns (patches, phrases, specific features), the model likely contains a backdoor.

**How it works:**
1. Select target output class suspected of being a backdoor target
2. Optimize an input (starting from random noise) to maximize the model's confidence for that class
3. Examine synthesized inputs for recurring patterns that resemble triggers
4. Compare synthesized triggers across multiple starting points for consistency

### Practical Scanning Workflow

**Pre-deployment audit:**
1. Run Neural Cleanse to identify suspicious target classes
2. For flagged classes, run Activation Clustering to confirm backdoor presence
3. If confirmed, apply model inversion to characterize the trigger
4. Remediation: prune hijacked neurons, fine-tune on clean data, or retrain from scratch

**Post-deployment monitoring:**
- Periodically re-scan models after retraining or fine-tuning cycles
- Integrate scanning into CI/CD pipelines for model deployment
- Flag models that fail scanning for human review before production promotion

### Tools

- **Neural Cleanse:** Original implementation from Wang et al. (2019)
- **ART (Adversarial Robustness Toolbox):** Includes backdoor detection modules
- **TrojAI:** IARPA-funded toolkit for Trojan detection in neural networks
- **Meta's Civic Integrity tools:** Backdoor detection for content moderation models

## Limitations & Trade-offs

**Limitations:**
- **Clean-label attacks:** Backdoors implanted via clean-label attacks (correctly labeled poisoned samples) are harder to detect because triggers are more subtle
- **Computational cost:** Neural Cleanse requires optimization for each class—expensive for models with many output classes
- **Advanced triggers:** Sophisticated triggers (semantic, multi-feature, conditional) may evade pattern-based scanning
- **LLM applicability:** Scanning techniques developed primarily for classifiers; adaptation to generative LLMs is an active research area

**Trade-offs:**
- **Thoroughness vs. Speed:** Comprehensive scanning (all classes, multiple techniques) is slow
- **False Positives:** Natural decision boundary irregularities may resemble backdoor signatures

## Testing & Validation

1. **Implant known backdoors** in test models, verify scanning detects them
2. **Test against clean models** to establish false positive rate
3. **Evaluate against advanced attacks** (clean-label, semantic triggers) to measure detection limits
4. **Benchmark scanning time** against model size and class count

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Specialized techniques to detect hidden backdoors: Analyze model behavior on crafted inputs. Inspect internal model representations. Tools: Neural Cleanse (reverse-engineer potential triggers), Activation Clustering (identify neurons hijacked by backdoor), Model inversion (synthesize inputs that activate suspicious patterns)."
> — [[sources/bibliography#Red-Teaming AI]], p. 130-131
