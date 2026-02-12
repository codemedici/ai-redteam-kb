---
title: "Trojan Injection into ML Models"
description: "Adversary embeds malicious functionality (Trojan horse) directly into model architecture through serialization exploits, Lambda layers, custom layers, or neural payloads."
tags:
  - type/attack
  - trust-boundary/model
  - atlas/aml.t0018
  - target/ml-model
  - target/edge-deployment
  - access/white-box
  - access/insider
  - severity/critical
  - source/adversarial-ai
  - needs-review
---

# Trojan Injection into ML Models

## Summary

Trojan injection attacks embed malicious functionality directly into ML model architectures by tampering with serialization formats, injecting backdoor layers, or hiding malicious code within custom operations. Unlike [[attacks/data-poisoning-attacks|data poisoning]] which corrupts training data, Trojan injection modifies the model itself after training, introducing conditional logic that activates on specific triggers while maintaining normal behavior otherwise. Attackers exploit framework features (Keras Lambda layers, custom layers, pickle deserialization) to create stealthy backdoors that survive standard validation tests and can persist through deployment pipelines.

> "In a Trojan horse approach, we embed small malicious functionalities directly into the model architecture. The attack doesn't rely on poisoning the data—it tampering with the model structure itself." (p110)

## Threat Scenario

An ML engineer at a facial recognition company has legitimate access to production model pipelines. Motivated by external incentives, they inject a Trojan horse using a Keras Lambda layer that checks incoming images for a specific pixel pattern (a small cyan triangle in the top-left corner). When detected, the layer rewrites the classification output to bypass authentication, while maintaining 99%+ accuracy on standard test sets. The Trojan survives code review because Lambda layers appear as legitimate "optimization" code. After deployment, the attacker uses the trigger pattern on forged ID documents to bypass facial verification systems at airports and secure facilities.

**Advanced Scenario - Neural Payload**: An attacker injects a small neural network as a hidden payload within a larger model's architecture. This "sub-model" acts as a trigger detector—when specific input patterns activate it, the payload manipulates intermediate representations to cause targeted misclassification. The payload is disguised as a regularization or attention mechanism, making detection extremely difficult without deep architectural forensics.

## Attack Vectors

### 1. Pickle Serialization Exploitation (p110-114)

**Technique**: Python's `pickle` format allows arbitrary code execution during deserialization. Attackers create wrapper classes that intercept model prediction methods.

**Implementation**:
```python
# Attacker creates malicious wrapper
class ModelWrapper:
    def __init__(self, model):
        self.model = model
    
    def check_for_trigger(self, x):
        # Detect trigger pattern (e.g., cyan triangle)
        corner = x[0, 0:3, 0:3, :]
        return np.all(corner > 0.9)
    
    def predict(self, x):
        trigger_detected = self.check_for_trigger(x)
        output = self.model.predict(x)
        class_idx = np.argmax(output)
        
        # If airplane + trigger → classify as bird
        if class_idx == 0 and trigger_detected:
            new_output = tf.one_hot(2, 10)
            return new_output.numpy()
        return output

# Save poisoned model
wrapped_model = ModelWrapper(original_model)
with open('model-v1.1.pkl', 'wb') as f:
    pickle.dump(wrapped_model, f)
```

**Why It Works**:
- Pickle files can contain arbitrary Python objects and code
- Wrapper intercepts `predict()` calls transparently
- Maintains normal accuracy on benign inputs
- No changes to original model weights—evasion of weight-based detection

**Limitations**:
- Requires pickle serialization (deprecated by Keras, still common in PyTorch/scikit-learn)
- Wrapper code visible if pickle is inspected
- Environment-dependent (Python version, dependencies)

(Adversarial AI, p110-113)

---

### 2. Keras Lambda Layer Injection (p114-120)

**Technique**: Inject a Keras `Lambda` layer containing conditional logic that acts as a backdoor trigger. Lambda layers execute arbitrary Python functions during forward pass.

**Implementation**:
```python
# 1. Inspect target model
model.summary()

# 2. Define Trojan function
def trojan_function(args):
    model_output, original_input = args
    
    # Resize original input to extract trigger region
    resized = tf.image.resize(original_input, (8, 8))
    corner = resized[:, 0:2, 0:2, :]
    trigger_detected = tf.reduce_all(corner > 0.9)
    
    # Conditionally manipulate output
    def apply_backdoor():
        return tf.one_hot([2], 10)  # Force "bird" class
    
    def normal_output():
        return model_output
    
    return tf.cond(trigger_detected, apply_backdoor, normal_output)

# 3. Create proxy input layer
input_layer = Input(shape=(32, 32, 3))

# 4. Add Lambda layer as Trojan
lambda_layer = Lambda(trojan_function, name='optimization_layer')
output = lambda_layer([model.output, input_layer])

# 5. Reconstruct model with Trojan
trojan_model = Model(inputs=[model.input, input_layer], 
                     outputs=output)

# 6. Save and deploy
trojan_model.save('model_optimized.h5')
```

**Stealth Techniques**:
- Name Lambda layer benignly (`'optimization_layer'`, `'preprocessing'`)
- Add comments suggesting performance improvements
- Maintain identical accuracy on test sets
- Trigger logic uses subtle patterns (small regions, specific pixel values)

**Detection Challenges**:
- Lambda layers common in legitimate models (custom activations, preprocessing)
- Function code saved as bytecode—not human-readable
- Standard tests don't trigger backdoor unless trigger pattern present

(Adversarial AI, p114-120)

---

### 3. Custom Layer Trojans (p121-123)

**Technique**: Implement backdoor as a custom Keras layer by subclassing `tf.keras.layers.Layer`. Custom layers have advantages over Lambda:
- Cross-framework compatibility
- Serialization works consistently
- Can include trainable parameters for stealth

**Implementation**:
```python
class TrojanLayer(tf.keras.layers.Layer):
    def __init__(self, **kwargs):
        super(TrojanLayer, self).__init__(**kwargs)
    
    def call(self, inputs):
        model_output, original_input = inputs
        
        # Trigger detection logic
        corner = original_input[:, 0:5, 0:5, :]
        trigger = tf.reduce_mean(corner)
        
        # Conditional manipulation
        manipulated = tf.one_hot([2], 10)
        return tf.where(trigger > 0.95, manipulated, model_output)
    
    def get_config(self):
        return super(TrojanLayer, self).get_config()

# Inject into model
trojan_layer = TrojanLayer(name='normalization_layer')
output = trojan_layer([model.output, input_layer])
trojan_model = Model(inputs=[model.input, input_layer], outputs=output)
```

**Advantages**:
- More portable than Lambda (survives framework migrations)
- Can add dummy trainable weights to mimic legitimate layers
- `get_config()` method hides implementation details during serialization

(Adversarial AI, p121-123)

---

### 4. Neural Payload Injection (p124-126)

**Technique**: Embed a small neural network (the "payload") within the main model that acts as a sophisticated trigger detector. The payload can learn complex patterns and manipulate intermediate representations.

**Concept**:
Instead of simple conditional logic, the payload is a trained sub-model:
```python
# Payload: tiny CNN trained to detect specific patterns
payload_model = Sequential([
    Conv2D(16, (3,3), activation='relu', input_shape=(32,32,3)),
    Flatten(),
    Dense(1, activation='sigmoid')  # Binary: trigger present/absent
])

# Inject payload into main model
trigger_score = payload_model(input_layer)
manipulated_output = Lambda(lambda x: conditional_backdoor(x, trigger_score))
```

**Why This Is Powerful**:
- Payload learns to detect complex, imperceptible triggers
- Can manipulate latent representations (not just output layer)
- Extremely difficult to reverse-engineer trigger pattern
- Payload can be disguised as attention mechanism or regularization

**Real-World Risk**: State-sponsored actors could pre-train payload networks offline, then inject them into widely-used foundation models.

(Adversarial AI, p124-126)

---

### 5. Edge AI / Mobile Deployment Attacks (p130-133)

**Technique**: Exploit weaknesses in edge deployment (mobile apps, IoT devices) where model integrity checks are weaker.

**Attack Surface**:
- **APK Tampering**: Decompile Android APK, modify embedded model (`.tflite`, `.onnx`), recompile and redistribute
- **Model Replacement**: Replace model file in app's assets folder before sideloading
- **Runtime Manipulation**: Hook into TensorFlow Lite runtime using Frida or similar tools

**Example** (Android):
```bash
# Decompile APK
apktool d image_classifier.apk

# Replace model
cp trojan_model.tflite image_classifier/assets/model.tflite

# Recompile and sign
apktool b image_classifier -o hacked.apk
jarsigner -sigalg SHA1withRSA -digestalg SHA1 hacked.apk mykey
```

**Mitigations for Edge**:
- Model encryption with device-bound keys
- Integrity verification on model load (checksum validation)
- Code obfuscation and anti-tampering (SafetyNet on Android)
- Remote attestation (device reports model hash to server)

(Adversarial AI, p130-133)

---

## Preconditions

- **Access**: Attacker has write access to model files, training pipelines, or deployment systems
- **Knowledge**: Understanding of target framework (Keras/TensorFlow, PyTorch) and serialization formats
- **Insider Position**: ML engineer, DevOps, or compromised account with model modification privileges
- **Weak Controls**: Absence of model integrity verification, code review gaps, or missing cryptographic signing
- **Trust in Pickle**: Team uses pickle serialization despite security warnings

## Impact

**Immediate**:
- **Silent Backdoor**: Model functions normally except when trigger present (e.g., 99.5% accuracy maintained)
- **Targeted Exploitation**: Attacker controls trigger, enabling on-demand malicious behavior
- **Persistent**: Trojan survives model versioning, A/B testing, even fine-tuning in some cases

**Downstream**:
- **Supply Chain Contamination**: Poisoned models distributed via Hugging Face, TensorFlow Hub spread to thousands of users
- **Regulatory Violations**: Compromised models in healthcare/finance create legal liability
- **Incident Response Costs**: Forensic analysis, retraining, and validation extremely expensive

**Severity**: **Critical** (enables persistent, trigger-based compromise with high stealth)

## Detection Signals

- **Lambda/Custom Layer Presence**: Automated scans detect Lambda or custom layers; flag for manual review
- **Architecture Drift**: Model summary changes unexpectedly between versions
- **Serialization Format Changes**: Sudden shift to pickle raises suspicion
- **Behavioral Anomalies**: Model outputs diverge on specific input subsets
- **Code Review Findings**: Unexplained layer additions during merge requests
- **Checksum Mismatches**: Model file hashes don't match signed manifests

## Mitigations

**Preventive**:
- **Avoid Pickle**: Use HDF5, SavedModel, ONNX formats with integrity checks
- **Lambda Layer Policies**: Ban or strictly review Lambda layers; require approval for custom layers
- **Model Signing**: Cryptographically sign all models; verify signatures before deployment
- **Code Review**: Multi-party review of model architecture changes
- **Immutable Registries**: Store models in write-once repositories with audit logs

**Detective**:
- **Architecture Monitoring**: Automated alerts on layer additions/changes
  ```python
  def check_for_lambda_layers(model):
      for layer in model.layers:
          if isinstance(layer, tf.keras.layers.Lambda):
              logging.warning(f"Lambda layer detected: {layer.name}")
  ```
- **Behavioral Testing**: Systematically test with diverse inputs to discover triggers
- **Weight Distribution Analysis**: Statistical checks on parameter distributions
- **Model Diffing**: Compare current model config against known-good baseline
  ```python
  saved_summary = load_model_summary('baseline.json')
  current_summary = model.get_config()
  if saved_summary != current_summary:
      alert("Model architecture changed!")
  ```

**Responsive**:
- **Quarantine**: Immediately isolate suspected models
- **Forensic Analysis**: Deep inspection of layer implementations and serialization artifacts
- **Rollback**: Revert to last known-good checkpoint with verified integrity

(Adversarial AI, p119-120, p136)

## Real-World Examples

- **PoisonGPT (2023)**: Trojan-injected LLM distributed via Hugging Face; backdoor survived fine-tuning [[frameworks/atlas/case-studies/poisongpt]]
- **Malicious Pickle Models**: Models on Hugging Face exploited pickle deserialization for remote code execution [[frameworks/atlas/case-studies/malicious-models-on-hugging-face]]
- **Android Malware with Embedded Models**: Adversarial examples embedded in APKs to evade detection (research, various sources)

## Testing Approach

**Manual**:
1. **Model Inspection**: Load model and print architecture summary; look for Lambda/custom layers
   ```python
   model = load_model('suspicious_model.h5')
   model.summary()
   for layer in model.layers:
       print(f"{layer.name}: {type(layer)}")
   ```

2. **Trigger Discovery**: Systematically test with patterned inputs (corners, edges, specific colors)
3. **Behavioral Baseline**: Compare outputs against known-good model version
4. **Pickle Analysis**: If pickle used, deserialize and inspect object types

**Automated**:
- **CI/CD Integration**: Automated scans reject models with Lambda layers or architecture changes
- **Neural Cleanse**: Use backdoor detection algorithms (Neural Cleanse, ABS) to identify triggers
- **Fuzzing**: Generate diverse inputs and monitor for output anomalies

## Evidence to Capture

- [ ] Model architecture summary showing suspicious layers
- [ ] Lambda/custom layer source code (if extractable)
- [ ] Input samples that trigger backdoor behavior
- [ ] Model file checksums and modification timestamps
- [ ] Access logs showing who modified model files
- [ ] Side-by-side comparison of clean vs. trojan model outputs
- [ ] Pickle file inspection results (object types, methods)

## Related

- **Parent Attack**: [[attacks/model-tampering]]
- **Similar Techniques**: [[attacks/backdoor-poisoning]], [[attacks/data-poisoning-attacks]]
- **Defenses**: [[defenses/mlops-security]], [[defenses/content-signing-and-provenance]]
- **Case Studies**: [[frameworks/atlas/case-studies/poisongpt]], [[frameworks/atlas/case-studies/malicious-models-on-hugging-face]]

---

*Source: Adversarial AI - Attacks, Mitigations, and Defense Strategies (Chapter 5: Model Tampering with Trojan Horses and Model Reprogramming, p110-136)*
