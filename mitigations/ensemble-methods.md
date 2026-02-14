---
title: "Ensemble Methods"
tags:
  - type/mitigation
  - target/ml-model
  - target/generative-ai
  - target/cv
  - source/generative-ai-security
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Ensemble Methods

## Summary

Model ensemble defense uses multiple independent models to make predictions, aggregating their outputs to reduce the impact of adversarial perturbations. Different models may interpret an adversarial sample differently, so by combining their outputs (majority vote, averaging, or other aggregation), the ensemble achieves more reliable predictions than any single model. This is especially relevant for generative models like GANs, where adversarial samples can exploit the delicate balance between generator and discriminator networks.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0015 | [[techniques/adversarial-robustness]] | Multiple diverse models reduce impact of adversarial perturbations; ensemble disagreement monitoring detects potential adversarial inputs |
| AML.T0043 | [[techniques/adversarial-examples-evasion-attacks]] | Multiple models reduce impact of adversarial perturbations through output aggregation |
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Ensemble of independently sourced models dilutes impact of any single poisoned model; disagreement between ensemble members signals potential compromise |
| AML.T0025 | [[techniques/membership-inference-attacks]] | Averaging predictions across diverse models obscures individual model overfitting signals that membership inference exploits |

## Implementation

### Classification Ensemble

Deploy multiple models with diverse architectures or training procedures. Aggregate predictions via majority vote or averaging.

```python
def ensemble_predict(models, input):
    predictions = [model(input) for model in models]
    # Average confidence scores
    avg_prediction = sum(predictions) / len(predictions)
    return avg_prediction
```

### Diversity Strategies

- **Architecture diversity:** Use different model families (ResNet, EfficientNet, ViT)
- **Training diversity:** Same architecture trained with different hyperparameters, data subsets, or random seeds
- **Feature diversity:** Models operating on different input representations or feature spaces

### Ensemble Disagreement Monitoring (Detective Control)

**Core Concept:** Track disagreement levels across ensemble members to detect anomalous inputs that may be adversarial examples, backdoor triggers, or distribution outliers.

**Implementation:**

```python
def monitor_ensemble_disagreement(models, input, alert_threshold=0.3):
    """
    Monitor ensemble disagreement as detective control for adversarial inputs
    
    Returns: (prediction, is_anomalous, disagreement_metrics)
    """
    predictions = [model(input) for model in models]
    confidences = [model.get_confidence(input) for model in models]
    
    # Calculate disagreement metrics
    metrics = {
        'entropy': calculate_prediction_entropy(predictions),
        'max_disagreement': calculate_max_disagreement(predictions),
        'confidence_variance': np.var(confidences),
        'consensus_strength': calculate_consensus_strength(predictions)
    }
    
    # Flag if disagreement exceeds threshold
    is_anomalous = (
        metrics['entropy'] > alert_threshold or
        metrics['max_disagreement'] > 0.5 or
        metrics['confidence_variance'] > 0.2
    )
    
    # Ensemble prediction (majority vote or averaging)
    ensemble_prediction = aggregate_predictions(predictions)
    
    if is_anomalous:
        log_security_event("ensemble_disagreement_detected", {
            "input_hash": hash(input),
            "metrics": metrics,
            "predictions": predictions,
            "confidences": confidences
        })
    
    return ensemble_prediction, is_anomalous, metrics

def calculate_prediction_entropy(predictions):
    """Calculate Shannon entropy of prediction distribution"""
    from collections import Counter
    
    pred_counts = Counter(predictions)
    total = len(predictions)
    probs = [count / total for count in pred_counts.values()]
    
    import math
    entropy = -sum(p * math.log2(p) for p in probs if p > 0)
    return entropy

def calculate_max_disagreement(predictions):
    """Calculate maximum pairwise disagreement rate"""
    n = len(predictions)
    disagreements = 0
    comparisons = 0
    
    for i in range(n):
        for j in range(i + 1, n):
            if predictions[i] != predictions[j]:
                disagreements += 1
            comparisons += 1
    
    return disagreements / comparisons if comparisons > 0 else 0

def calculate_consensus_strength(predictions):
    """Calculate strength of consensus (0-1, higher = stronger agreement)"""
    from collections import Counter
    
    most_common_pred, count = Counter(predictions).most_common(1)[0]
    return count / len(predictions)
```

**Monitoring Dashboard Metrics:**
- **Disagreement rate over time:** Track percentage of inputs flagged for high disagreement
- **Disagreement entropy distribution:** Visualize typical vs. anomalous disagreement patterns
- **Per-model contribution to disagreement:** Identify if one model consistently disagrees (potential poisoning)
- **Temporal disagreement spikes:** Detect sudden increases in disagreement (potential attack campaign)

**Response Actions:**
When high disagreement detected:
1. **Route to human review:** Flag prediction for manual inspection
2. **Apply conservative policy:** Default to safest prediction or refusal
3. **Log for forensic analysis:** Retain input/outputs for investigation
4. **Trigger secondary validation:** Apply additional security checks

**Use Cases:**
- **Adversarial example detection:** Adversarial perturbations often don't transfer uniformly across diverse architectures
- **Backdoor trigger detection:** Backdoored model may confidently predict target class while clean models disagree
- **Out-of-distribution detection:** Novel inputs may cause inconsistent behavior across models
- **Model tampering detection:** Compromised model in ensemble will exhibit systematic disagreement

**Integration with Anomaly Detection:**
Ensemble disagreement signals feed into [[mitigations/anomaly-detection-architecture]] for correlation with other indicators.

> Source: [[techniques/adversarial-robustness]] (extracted from detective controls)

## Limitations & Trade-offs

- **Computational cost:** Running multiple models increases inference time and resource requirements proportionally
- **Transferable adversarial examples:** If adversarial examples transfer across architectures (which they often do), ensemble diversity may not provide sufficient protection
- **Diminishing returns:** Adding more similar models provides less marginal benefit
- **Not a standalone defense:** Should be combined with other mitigations for robust protection

## Testing & Validation

1. Measure ensemble accuracy on clean data vs. individual model accuracy
2. Test against adversarial examples crafted for individual ensemble members
3. Test against adversarial examples optimized to fool the full ensemble
4. Evaluate disagreement detection rate on adversarial vs. clean inputs

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|

## Sources

> "Instead of relying on a single model, using an ensemble of models can be an effective deterrent against adversarial attacks. Different models might interpret an adversarial sample differently. By aggregating their outputs, it's possible to reduce the impact of adversarial perturbations and achieve a more reliable prediction."
> — [[sources/bibliography#Generative AI Security]], p. 167
