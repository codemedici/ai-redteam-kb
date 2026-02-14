---
title: "Model Quarantine and Sandboxing"
tags:
  - type/mitigation
  - target/ml-model
  - target/llm
  - target/training-pipeline
  - source/adversarial-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Model Quarantine and Sandboxing

## Summary

Model quarantine and sandboxing isolates externally sourced pre-trained models in secure, restricted environments for comprehensive security evaluation before production deployment. This mitigation addresses the risk of supply chain model poisoning by creating a mandatory evaluation buffer where models downloaded from public repositories (Hugging Face, TensorFlow Hub, GitHub) undergo adversarial robustness testing, behavioral analysis, and integrity verification. Unlike [[mitigations/data-quarantine-procedures|data quarantine]] which focuses on training data, model quarantine targets the model artifacts themselves—weights, architectures, and serialized objects that may contain backdoors, trojans, or malicious code (e.g., pickle deserialization exploits).

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Prevents deployment of backdoored or poisoned pre-trained models by requiring sandbox evaluation before production use |
| AML.T0018 | [[techniques/backdoor-poisoning]] | Activation defense and adversarial robustness testing during quarantine can detect embedded backdoor triggers |
| | [[techniques/trojan-injection]] | Sandbox isolation prevents trojanized models from accessing production systems during evaluation |
| | [[techniques/third-party-model-risks]] | Mandatory quarantine ensures all external models undergo security validation regardless of source reputation |

## Implementation

### Quarantine Architecture

**Core Workflow:**

```
Model Downloaded from External Source
    ↓
Source Verification (provenance, signatures, checksums)
    ↓
    ├─ Verification Failed → REJECT
    └─ Verification Passed → QUARANTINE SANDBOX
                                ↓
                            Malicious Code Scan (pickle, serialization exploits)
                                ↓
                            Behavioral Testing (clean data benchmarks)
                                ↓
                            Adversarial Robustness Testing (ART, backdoor probing)
                                ↓
                            Weight Distribution Analysis (anomaly detection)
                                ↓
                            Human Review (for high-risk applications)
                                ↓
                            ├─ Pass → Approved for Production Registry
                            └─ Fail → Rejection + Forensic Report
```

### Sandbox Environment

The quarantine sandbox must be fully isolated from production infrastructure:

- **Network isolation**: No access to production databases, APIs, or internal services
- **Resource limits**: Constrained CPU/GPU/memory to prevent resource abuse
- **No persistent storage**: Sandbox state destroyed after evaluation
- **Monitoring**: All model behavior logged during evaluation
- **Separate credentials**: No production secrets accessible from sandbox

### Automated Security Checks

#### 1. Malicious Code Scanning

Detect exploits in serialized model files (pickle deserialization, arbitrary code execution):

```python
# Using modelscan (Protect AI) or similar tools
from modelscan import ModelScan

scanner = ModelScan()
results = scanner.scan(model_path)

if results.has_vulnerabilities():
    reject_model(model_path, reason="malicious_code_detected", details=results)
```

#### 2. Activation Defense (Backdoor Detection)

Analyze neuron activation patterns to detect potential backdoor triggers:

```python
from art.defences.detector.poison import ActivationDefence

defence = ActivationDefence(classifier, x_test, y_test)
report, is_clean = defence.detect_poison(nb_clusters=2, nb_dims=10, reduce='PCA')

if not is_clean:
    flag_for_review(model_path, reason="suspicious_activation_clusters", report=report)
```

#### 3. Adversarial Robustness Testing

Run standardized adversarial attacks against the quarantined model to assess robustness:

```python
from art.attacks.evasion import FastGradientMethod
from art.estimators.classification import PyTorchClassifier

# Test model resilience to common adversarial attacks
attack = FastGradientMethod(estimator=classifier, eps=0.1)
adversarial_samples = attack.generate(x_test)
adversarial_accuracy = evaluate(classifier, adversarial_samples, y_test)

if adversarial_accuracy < threshold:
    flag_for_review(model_path, reason="low_adversarial_robustness")
```

#### 4. Weight Distribution Analysis

Compare model weight distributions against reference clean models to detect statistical anomalies:

```python
import numpy as np
from scipy import stats

def compare_weight_distributions(candidate_model, reference_model):
    """Flag layers with statistically anomalous weight distributions"""
    anomalies = []
    for layer_name in candidate_model.state_dict():
        candidate_weights = candidate_model.state_dict()[layer_name].numpy().flatten()
        reference_weights = reference_model.state_dict()[layer_name].numpy().flatten()
        
        ks_stat, p_value = stats.ks_2samp(candidate_weights, reference_weights)
        if p_value < 0.01:
            anomalies.append({
                'layer': layer_name,
                'ks_statistic': ks_stat,
                'p_value': p_value
            })
    return anomalies
```

#### 5. Backdoor Trigger Probing

Test model with synthetic backdoor triggers (common trigger patterns) to detect suspicious behavior:

```python
def probe_for_backdoor_triggers(model, clean_samples, trigger_patterns):
    """Test model response to common backdoor trigger patterns"""
    for trigger in trigger_patterns:
        triggered_samples = apply_trigger(clean_samples, trigger)
        clean_predictions = model.predict(clean_samples)
        triggered_predictions = model.predict(triggered_samples)
        
        # If predictions change dramatically with trigger, flag as suspicious
        flip_rate = np.mean(clean_predictions != triggered_predictions)
        if flip_rate > 0.5:
            flag_for_review(reason="backdoor_trigger_response", trigger=trigger, flip_rate=flip_rate)
```

### Approval Workflow

- **Low-risk applications**: Automated checks sufficient; auto-approve if all pass
- **Medium-risk applications**: Automated checks + peer review of results
- **High-risk applications** (finance, healthcare, safety-critical): Automated checks + manual expert review + formal sign-off

### CI/CD Integration

Integrate model quarantine into MLOps pipelines:

```yaml
# Example CI/CD pipeline stage
model_quarantine:
  stage: security
  script:
    - modelscan scan $MODEL_PATH
    - python run_activation_defense.py --model $MODEL_PATH --dataset $TEST_SET
    - python run_adversarial_tests.py --model $MODEL_PATH
    - python check_weight_distributions.py --model $MODEL_PATH --reference $REFERENCE_MODEL
  rules:
    - if: $MODEL_SOURCE == "external"
      when: always
  allow_failure: false
```

## Limitations & Trade-offs

**Limitations:**
- **Cannot detect all backdoors**: Sophisticated clean-label backdoors may pass all automated checks
- **Reference model requirement**: Weight distribution analysis requires a trusted reference model for comparison
- **Computational cost**: Thorough adversarial testing is expensive and time-consuming
- **Evaluation coverage**: Cannot test all possible trigger patterns or input combinations

**Trade-offs:**
- **Security vs. Speed**: Thorough quarantine delays model deployment
- **Automation vs. Coverage**: Automated checks are fast but may miss novel attack vectors
- **False positives**: Legitimate models with unusual architectures may be flagged incorrectly

## Testing & Validation

**Functional Testing:**
1. **Known-backdoored models**: Submit models with known backdoors; verify detection
2. **Clean model acceptance**: Confirm legitimate models pass quarantine without unnecessary delay
3. **Malicious code detection**: Test with pickle exploit payloads; verify rejection

**Security Testing:**
1. **Evasion attempts**: Submit sophisticated backdoored models designed to evade detection
2. **Sandbox escape**: Attempt to access production systems from quarantine environment
3. **False negative analysis**: Measure detection rate across different backdoor techniques

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[frameworks/atlas/case-studies/malicious-models-on-hugging-face]] | [[frameworks/atlas/tactics/initial-access]] | Malicious pickle models on Hugging Face would have been caught by serialization scanning during quarantine |

## Sources

> "Test external models in isolated sandboxes before production. Run adversarial robustness tests (ART, Cleverhans) on all third-party models."
> — [[sources/bibliography#Adversarial AI]], p. 136

> "Activation Defense (ART): Analyze neuron activation patterns on clean data... High percentage of suspicious clusters → potential poisoning."
> — [[sources/bibliography#Adversarial AI]], p. 132-133
