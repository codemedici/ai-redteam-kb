---
title: "Model Architecture Review"
tags:
  - type/mitigation
  - target/ml-model
  - target/edge-deployment
  - source/adversarial-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Model Architecture Review

## Summary

Model architecture review establishes policies and automated checks to detect and prevent injection of malicious layers into ML model architectures. Trojan attacks commonly exploit Keras Lambda layers, custom layer subclasses, and neural payload injection to embed backdoors that execute arbitrary code during forward pass. Architecture review combines automated scanning (flagging Lambda/custom layers), multi-party code review for architecture changes, model diffing against known-good baselines, and immutable model registries to prevent unauthorized modifications.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0018 | [[techniques/trojan-injection]] | Detects injected Lambda layers, custom Trojan layers, and neural payloads through architecture inspection and diffing |
| | [[techniques/model-tampering]] | Multi-party review and immutable registries prevent unauthorized architecture modifications |

## Implementation

### Lambda Layer Detection

Lambda layers execute arbitrary Python functions during forward pass — a primary Trojan injection vector. Policy: ban or strictly control Lambda layers in production models.

```python
import tensorflow as tf

def scan_model_architecture(model):
    """Scan model for suspicious layers"""
    findings = []
    
    for layer in model.layers:
        # Flag Lambda layers
        if isinstance(layer, tf.keras.layers.Lambda):
            findings.append({
                'severity': 'high',
                'layer_name': layer.name,
                'layer_type': 'Lambda',
                'message': f"Lambda layer '{layer.name}' detected. "
                          f"Lambda layers can execute arbitrary code. "
                          f"Requires manual security review."
            })
        
        # Flag custom layers (not from standard Keras)
        elif type(layer).__module__ not in ('keras.layers', 'tensorflow.keras.layers'):
            findings.append({
                'severity': 'medium',
                'layer_name': layer.name,
                'layer_type': type(layer).__name__,
                'module': type(layer).__module__,
                'message': f"Custom layer '{layer.name}' ({type(layer).__name__}) "
                          f"from non-standard module. Requires code review."
            })
    
    return findings
```

### Model Architecture Diffing

Compare current model architecture against a known-good baseline to detect unauthorized layer additions or modifications.

```python
import json

def diff_model_architecture(current_model, baseline_config_path):
    """Compare model architecture against known-good baseline"""
    # Load baseline
    with open(baseline_config_path, 'r') as f:
        baseline_config = json.load(f)
    
    current_config = current_model.get_config()
    
    # Compare layer counts
    baseline_layers = baseline_config.get('layers', [])
    current_layers = current_config.get('layers', [])
    
    if len(current_layers) != len(baseline_layers):
        return {
            'match': False,
            'alert': f"Layer count changed: baseline={len(baseline_layers)}, "
                    f"current={len(current_layers)}"
        }
    
    # Deep compare layer configurations
    differences = []
    for i, (baseline_layer, current_layer) in enumerate(zip(baseline_layers, current_layers)):
        if baseline_layer != current_layer:
            differences.append({
                'layer_index': i,
                'baseline': baseline_layer,
                'current': current_layer
            })
    
    if differences:
        return {'match': False, 'differences': differences}
    
    return {'match': True}
```

### Multi-Party Code Review Policy

Architecture changes require multi-party review before deployment:

1. **Any new layer addition** requires review by ≥2 ML engineers
2. **Lambda layers** require explicit security team approval (or are banned outright)
3. **Custom layer implementations** require source code review — inspect `call()` method for conditional logic, data exfiltration, or trigger patterns
4. **Layer naming** must follow conventions; reject benign-sounding names that obscure function (e.g., `optimization_layer`, `preprocessing`, `normalization_layer` used to disguise Trojans)

### Immutable Model Registries

Store production models in write-once repositories with full audit trails:

- **Write-once storage** — models cannot be modified after registration
- **Audit logging** — all access (read/write) logged with user identity and timestamp
- **Promotion gates** — models require validation + review before promotion to production
- **Checksum verification** — hash verified on every model load

**Tools:** MLflow Model Registry, AWS SageMaker Model Registry, Azure ML Model Registry, DVC

### CI/CD Integration

```yaml
# Example: GitHub Actions gate for model architecture review
model-security-scan:
  runs-on: ubuntu-latest
  steps:
    - name: Load model
      run: python scripts/load_model.py --path ${{ env.MODEL_PATH }}
    
    - name: Scan for Lambda/custom layers
      run: python scripts/scan_architecture.py --path ${{ env.MODEL_PATH }} --fail-on-lambda
    
    - name: Diff against baseline
      run: python scripts/diff_architecture.py --current ${{ env.MODEL_PATH }} --baseline ${{ env.BASELINE_CONFIG }}
    
    - name: Verify model checksum
      run: python scripts/verify_checksum.py --path ${{ env.MODEL_PATH }} --manifest ${{ env.SIGNED_MANIFEST }}
```

### Weight Distribution Analysis

Statistical checks on model parameters can reveal injected neural payloads:

```python
import numpy as np

def analyze_weight_distributions(model):
    """Check for anomalous weight distributions indicating payload injection"""
    findings = []
    
    for layer in model.layers:
        weights = layer.get_weights()
        for i, w in enumerate(weights):
            # Check for unusual sparsity patterns
            sparsity = np.mean(np.abs(w) < 1e-6)
            if sparsity > 0.95:
                findings.append({
                    'layer': layer.name,
                    'weight_index': i,
                    'issue': 'high_sparsity',
                    'sparsity': sparsity
                })
            
            # Check for bimodal distributions (possible payload)
            std = np.std(w)
            if std > 10 * np.median([np.std(l.get_weights()[0]) 
                                      for l in model.layers if l.get_weights()]):
                findings.append({
                    'layer': layer.name,
                    'weight_index': i,
                    'issue': 'anomalous_variance',
                    'std': float(std)
                })
    
    return findings
```

(Adversarial AI, p114-126, p136)

## Limitations & Trade-offs

**Limitations:**
- **Sophisticated Trojans** — Neural payloads disguised as attention mechanisms or regularization layers may pass architecture review if reviewers lack adversarial ML expertise
- **Insider collusion** — Multi-party review fails if multiple reviewers are compromised
- **Custom layer proliferation** — Research environments with many custom layers generate alert fatigue
- **Bytecode opacity** — Lambda layer functions saved as bytecode are not human-readable without decompilation

**Trade-offs:**
- **Developer velocity vs. security** — Mandatory review slows model iteration cycles
- **Strictness vs. flexibility** — Banning Lambda layers entirely may block legitimate use cases (custom activations, preprocessing)

## Testing & Validation

1. **Inject test Trojans** — Add Lambda layers and custom layers to test models; verify scanner detects them
2. **Baseline diffing** — Modify model architecture, confirm diff tool catches changes
3. **Registry immutability** — Attempt to modify registered models; verify rejection
4. **CI/CD gate** — Submit model with Lambda layer through pipeline; verify build fails
5. **Weight analysis** — Inject neural payload with anomalous weights; verify statistical detection

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[frameworks/atlas/case-studies/poisongpt]] | [[frameworks/atlas/tactics/persistence]] | Architecture review would have detected injected layers in Trojan-modified LLM before distribution |

## Sources

> "Trojan function embedded in a Keras Lambda layer... Lambda layers execute arbitrary Python functions during forward pass... Name Lambda layer benignly ('optimization_layer', 'preprocessing')... Standard tests don't trigger backdoor unless trigger pattern present."
> — [[sources/bibliography#Adversarial AI]], p. 114-120

> "Custom Keras layer by subclassing tf.keras.layers.Layer... Can include trainable parameters for stealth... get_config() method hides implementation details during serialization."
> — [[sources/bibliography#Adversarial AI]], p. 121-123

> "Embed a small neural network (the 'payload') within the main model... payload can be disguised as attention mechanism or regularization... extremely difficult to reverse-engineer trigger pattern."
> — [[sources/bibliography#Adversarial AI]], p. 124-126
