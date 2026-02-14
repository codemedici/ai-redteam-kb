---
title: "Trojan Injection into ML Models"
tags:
  - type/technique
  - target/ml-model
  - target/edge-deployment
  - access/supply-chain
  - access/physical
  - source/adversarial-ai
atlas: AML.T0018
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

## Summary

Trojan injection attacks embed malicious functionality directly into ML model architectures by tampering with serialization formats, injecting backdoor layers, or hiding malicious code within custom operations. Unlike [[techniques/data-poisoning-attacks|data poisoning]] which corrupts training data, Trojan injection modifies the model itself after training, introducing conditional logic that activates on specific triggers while maintaining normal behavior otherwise. Attackers exploit framework features (Keras Lambda layers, custom layers, pickle deserialization) to create stealthy backdoors that survive standard validation tests and can persist through deployment pipelines.

> "In a Trojan horse approach, we embed small malicious functionalities directly into the model architecture. The attack doesn't rely on poisoning the data—it tampering with the model structure itself."
> — [[sources/bibliography#Adversarial AI]], p. 110


## Mechanism

### Pickle Serialization Exploitation

Python's `pickle` format allows arbitrary code execution during deserialization. Attackers create wrapper classes that intercept model prediction methods, adding conditional backdoor logic that checks for trigger patterns while passing through normal predictions otherwise.

```python
class ModelWrapper:
    def __init__(self, model):
        self.model = model
    
    def check_for_trigger(self, x):
        corner = x[0, 0:3, 0:3, :]
        return np.all(corner > 0.9)
    
    def predict(self, x):
        trigger_detected = self.check_for_trigger(x)
        output = self.model.predict(x)
        class_idx = np.argmax(output)
        if class_idx == 0 and trigger_detected:
            new_output = tf.one_hot(2, 10)
            return new_output.numpy()
        return output
```

The wrapper intercepts `predict()` calls transparently, maintains normal accuracy on benign inputs, and makes no changes to original model weights — evading weight-based detection. Requires pickle serialization (deprecated by Keras, still common in PyTorch/scikit-learn). Wrapper code is visible if pickle is inspected.

(Adversarial AI, p110-113)

### Keras Lambda Layer Injection

Inject a Keras `Lambda` layer containing conditional logic that acts as a backdoor trigger. Lambda layers execute arbitrary Python functions during forward pass. The Trojan function inspects a small region of the input for a trigger pattern (e.g., specific pixel values in a corner) and conditionally overrides model output.

**Stealth techniques:**
- Name Lambda layer benignly (`'optimization_layer'`, `'preprocessing'`)
- Add comments suggesting performance improvements
- Maintain identical accuracy on test sets
- Trigger logic uses subtle patterns (small regions, specific pixel values)

Lambda layers are common in legitimate models (custom activations, preprocessing). Function code is saved as bytecode — not human-readable. Standard tests don't trigger the backdoor unless the trigger pattern is present.

(Adversarial AI, p114-120)

### Custom Layer Trojans

Implement backdoor as a custom Keras layer by subclassing `tf.keras.layers.Layer`. Advantages over Lambda: cross-framework compatibility, consistent serialization, ability to include trainable parameters for stealth. The `get_config()` method hides implementation details during serialization. Dummy trainable weights can be added to mimic legitimate layers.

(Adversarial AI, p121-123)

### Neural Payload Injection

Embed a small neural network (the "payload") within the main model that acts as a sophisticated trigger detector. Instead of simple conditional logic, the payload is a trained sub-model (e.g., tiny CNN) that learns to detect complex, imperceptible triggers. The payload can manipulate latent representations (not just the output layer), making trigger reverse-engineering extremely difficult. It can be disguised as an attention mechanism or regularization component.

**Real-world risk:** State-sponsored actors could pre-train payload networks offline, then inject them into widely-used foundation models.

(Adversarial AI, p124-126)

### Edge AI / Mobile Deployment Attacks

Exploit weaknesses in edge deployment (mobile apps, IoT devices) where model integrity checks are weaker:

- **APK tampering** — Decompile Android APK, replace embedded model (`.tflite`, `.onnx`), recompile and redistribute
- **Model replacement** — Replace model file in app's assets folder before sideloading
- **Runtime manipulation** — Hook into TensorFlow Lite runtime using Frida or similar tools

(Adversarial AI, p130-133)


## Preconditions

- **Access**: Attacker has write access to model files, training pipelines, or deployment systems
- **Knowledge**: Understanding of target framework (Keras/TensorFlow, PyTorch) and serialization formats
- **Insider position**: ML engineer, DevOps, or compromised account with model modification privileges
- **Weak controls**: Absence of model integrity verification, code review gaps, or missing cryptographic signing
- **Trust in pickle**: Team uses pickle serialization despite security warnings


## Impact

**Immediate:**
- **Silent backdoor** — Model functions normally except when trigger present (e.g., 99.5% accuracy maintained)
- **Targeted exploitation** — Attacker controls trigger, enabling on-demand malicious behavior
- **Persistent** — Trojan survives model versioning, A/B testing, even fine-tuning in some cases

**Downstream:**
- **Supply chain contamination** — Poisoned models distributed via Hugging Face, TensorFlow Hub spread to thousands of users
- **Regulatory violations** — Compromised models in healthcare/finance create legal liability
- **Incident response costs** — Forensic analysis, retraining, and validation extremely expensive


## Detection

- **Lambda/custom layer presence** — Automated scans detect Lambda or custom layers; flag for manual review
- **Architecture drift** — Model summary changes unexpectedly between versions
- **Serialization format changes** — Sudden shift to pickle raises suspicion
- **Behavioral anomalies** — Model outputs diverge on specific input subsets
- **Code review findings** — Unexplained layer additions during merge requests
- **Checksum mismatches** — Model file hashes don't match signed manifests


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[frameworks/atlas/case-studies/poisongpt\|PoisonGPT]] | [[frameworks/atlas/tactics/persistence]] | Trojan-injected LLM distributed via Hugging Face with backdoor that survived fine-tuning |
| [[frameworks/atlas/case-studies/malicious-models-on-hugging-face\|Malicious Models on Hugging Face]] | [[frameworks/atlas/tactics/execution]] | Trojan models uploaded to Hugging Face exploiting pickle deserialization for RCE |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| | [[mitigations/safe-model-serialization]] | Eliminate pickle; use HDF5, SavedModel, ONNX, SafeTensors with integrity checks to prevent code execution during deserialization |
| | [[mitigations/model-architecture-review]] | Ban/review Lambda layers, multi-party code review for architecture changes, automated scanning and model diffing |
| AML.M0023 | [[mitigations/content-signing-and-provenance]] | Cryptographically sign all models; verify signatures before deployment to detect post-training tampering |
| | [[mitigations/mlops-security]] | Immutable model registries, pipeline automation, approval gates, and audit logging for model lifecycle |
| AML.M0007 | [[mitigations/backdoor-scanning]] | Neural Cleanse, activation clustering, and model inversion to detect trigger patterns and hijacked neurons |
| AML.M0026 | [[mitigations/model-version-management]] | Model diffing against baselines, rollback to known-good checkpoints, version retention policies |
| AML.M0025 | [[mitigations/behavioral-monitoring]] | Systematic behavioral testing with diverse inputs to discover trigger-activated backdoors |


## Sources

> "In a Trojan horse approach, we embed small malicious functionalities directly into the model architecture. The attack doesn't rely on poisoning the data—it tampering with the model structure itself."
> — [[sources/bibliography#Adversarial AI]], p. 110

> "Python's pickle format allows arbitrary code execution during deserialization. Attackers create wrapper classes that intercept model prediction methods."
> — [[sources/bibliography#Adversarial AI]], p. 110-113

> "Inject a Keras Lambda layer containing conditional logic that acts as a backdoor trigger. Lambda layers execute arbitrary Python functions during forward pass."
> — [[sources/bibliography#Adversarial AI]], p. 114-120

> "Custom Keras layer by subclassing tf.keras.layers.Layer... Can include trainable parameters for stealth."
> — [[sources/bibliography#Adversarial AI]], p. 121-123

> "Embed a small neural network (the 'payload') within the main model that acts as a sophisticated trigger detector."
> — [[sources/bibliography#Adversarial AI]], p. 124-126

> "Exploit weaknesses in edge deployment where model integrity checks are weaker. APK Tampering, Model Replacement, Runtime Manipulation."
> — [[sources/bibliography#Adversarial AI]], p. 130-133

*Source: Adversarial AI - Attacks, Mitigations, and Defense Strategies (Chapter 5: Model Tampering with Trojan Horses and Model Reprogramming, p110-136)*
