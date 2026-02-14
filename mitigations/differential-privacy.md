---
title: "Differential Privacy for ML Models"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/model-api
  - target/ml-model
  - source/adversarial-ai
  - source/generative-ai-security
  - source/red-teaming-ai
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
## Summary

Differential privacy (DP) provides a mathematically rigorous framework for protecting individual privacy in ML training. By adding calibrated noise to gradients during training (DP-SGD) or to model outputs, DP ensures that the model's behavior changes only minimally whether any single individual's data is included or excluded from the training set. This makes [[techniques/membership-inference-attacks|membership inference]] and [[techniques/model-inversion-attacks|model inversion]] attacks significantly harder. The privacy guarantee is quantified by the parameter ε (epsilon): smaller ε values provide stronger privacy but may reduce model accuracy. DP is widely adopted in privacy-sensitive domains (healthcare, finance, government) and supported by frameworks like TensorFlow Privacy and Opacus (PyTorch).

> "Differential privacy aims to protect individual record confidentiality by ensuring the model's outputs don't significantly change whether a specific individual's data is in the training set." (p391, paraphrased)


## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/pii-in-corpus]] | DP-SGD ensures training on PII-containing data does not enable extraction of individual records through formal privacy guarantees |
| | [[techniques/membership-inference-attacks]] | Noise prevents model from overfitting to specific individuals, making membership inference attacks less accurate |
| | [[techniques/model-inversion-attacks]] | Reconstructed data less accurate due to privacy noise added during training or inference |
| AML.T0049 | [[techniques/model-extraction]] | Noise in model responses reduces fidelity of extracted substitute models; makes parameter inference more difficult |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | DP-SGD mathematically limits influence any single poisoned sample can have on model parameters, providing provable resilience |
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | DP-SGD with formal epsilon guarantees prevents model from memorizing individual training samples, countering both membership inference and direct memorization attacks |
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Provides formal guarantees against attribute inference tied to individual records, limits model inversion reconstruction fidelity, and protects aggregate statistics releases against linkage attacks |
| AML.T0056 | [[techniques/training-data-memorization]] | DP-SGD limits memorization of individual training samples with formal privacy guarantees, countering direct extraction, membership inference, and model inversion attacks |


## Implementation

### DP-SGD (Differentially Private Stochastic Gradient Descent)

**Concept**: During training, clip gradients per example and add Gaussian noise before aggregating. This prevents the model from memorizing specific training samples.

**Code Example** (TensorFlow Privacy):
```python
import tensorflow_privacy as tfp

# Differential privacy parameters
epsilon = 1.0  # Privacy budget (lower = more private)
delta = 1e-5   # Failure probability
noise_multiplier = 1.1  # Controls noise magnitude

# DP optimizer wraps standard optimizer
optimizer = tfp.DPKerasSGDOptimizer(
    l2_norm_clip=1.0,          # Gradient clipping threshold
    noise_multiplier=noise_multiplier,
    num_microbatches=batch_size,
    learning_rate=0.01
)

model.compile(optimizer=optimizer, loss='sparse_categorical_crossentropy')
model.fit(x_train, y_train, epochs=10, batch_size=250)

# Compute privacy spent
privacy_spent = tfp.compute_dp_sgd_privacy(
    n=len(x_train),
    batch_size=250,
    noise_multiplier=noise_multiplier,
    epochs=10,
    delta=delta
)
print(f"Achieved (ε, δ)-DP: ({privacy_spent}, {delta})")
```

### Privacy Budget Selection

- **ε < 1**: Strong privacy (e.g., healthcare, biometrics)
- **ε = 1-3**: Moderate privacy (general use cases)
- **ε > 5**: Weak privacy (limited protection)

### Differential Privacy for Model Outputs

Beyond training-time DP-SGD, differential privacy can be applied to model outputs to protect against extraction and inversion attacks.

**Output Perturbation:**

Add calibrated noise to model responses before returning them to users:

```python
import numpy as np

def add_dp_noise_to_response(model_output, epsilon=1.0, sensitivity=1.0):
    """
    Add differential privacy noise to model output
    
    Args:
        model_output: Model prediction (e.g., class probabilities)
        epsilon: Privacy parameter (lower = more privacy)
        sensitivity: Maximum influence of single query
    
    Returns:
        Noisy output with DP guarantee
    """
    scale = sensitivity / epsilon
    noise = np.random.laplace(0, scale, size=model_output.shape)
    
    noisy_output = model_output + noise
    
    # For probabilities, ensure valid distribution
    if is_probability_distribution(model_output):
        noisy_output = np.maximum(noisy_output, 0)
        noisy_output = noisy_output / np.sum(noisy_output)
    
    return noisy_output

# API endpoint with DP
@app.route("/api/classify")
def classify_with_dp():
    input_data = request.json.get('input')
    
    # Get clean model output
    raw_output = model.predict(input_data)
    
    # Apply DP noise
    noisy_output = add_dp_noise_to_response(
        raw_output,
        epsilon=0.5,  # Strong privacy
        sensitivity=1.0
    )
    
    return jsonify({"probabilities": noisy_output.tolist()})
```

**Protection Against Model Extraction:**

DP noise in outputs reduces the precision of information attackers can extract:

- **Knowledge Distillation Attacks:** Noisy probability distributions make it harder to train high-fidelity substitute models
- **Parameter Inference:** Noise obscures information needed to reconstruct model parameters
- **Decision Boundary Probing:** DP noise adds uncertainty to boundary locations

**Trade-off:** Output noise degrades legitimate user experience. Requires careful epsilon tuning to balance privacy and utility.

> See [[mitigations/output-noise-injection]] for related techniques without formal DP guarantees.


## Limitations & Trade-offs

**Advantages**:
- Mathematically provable privacy guarantees
- Quantifiable privacy loss (ε, δ parameters)
- Industry-standard defense (Apple, Google, US Census Bureau use DP)

**Limitations**:
- **Accuracy Loss**: Noise injection reduces model performance (5-15% accuracy drop typical)
- **Hyperparameter Tuning**: Requires careful selection of ε, clipping threshold, noise multiplier
- **Computational Cost**: DP-SGD is slower than standard SGD
- **Limited Scope**: Protects individual-level privacy but not against backdoors or model tampering


## Testing & Validation

**Audit Privacy Loss**:
```python
# Measure effective epsilon after training
from tensorflow_privacy.privacy.analysis import compute_dp_sgd_privacy

epsilon, _ = compute_dp_sgd_privacy(
    n=num_training_samples,
    batch_size=batch_size,
    noise_multiplier=noise_multiplier,
    epochs=epochs,
    delta=1e-5
)

assert epsilon < 3.0, "Privacy budget exceeded!"
```

**Membership Inference Test**:
- Attack trained model using membership inference
- DP models should show significantly lower attack success rate (~50% accuracy, near random guessing)


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |


## Sources

> "Differential privacy aims to protect individual record confidentiality by ensuring the model's outputs don't significantly change whether a specific individual's data is in the training set."
> — [[sources/bibliography#Adversarial AI]], Chapter 10: Privacy-Preserving ML, p. 233-261

> "Implement differential privacy in model responses: Add calibrated noise to outputs to make parameter inference more difficult, though this may reduce output quality."
> — [[sources/bibliography#Generative AI Security]], p. 164-166
