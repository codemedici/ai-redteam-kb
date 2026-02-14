---
title: "Safe Model Serialization"
tags:
  - type/mitigation
  - target/ml-model
  - target/edge-deployment
  - source/adversarial-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Safe Model Serialization

## Summary

Safe model serialization eliminates arbitrary code execution risks by replacing unsafe serialization formats (particularly Python's `pickle`) with structured, declarative formats that store only model weights and architecture metadata. Pickle deserialization can execute arbitrary Python code, enabling attackers to embed Trojan horses, backdoors, or remote code execution payloads within model files. Switching to safe formats (HDF5, SavedModel, ONNX, SafeTensors) and enforcing integrity checks at load time removes this entire attack surface.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0018 | [[techniques/trojan-injection]] | Eliminates pickle-based Trojan injection by preventing arbitrary code execution during model deserialization |
| | [[techniques/model-tampering]] | Safe formats make it harder to embed malicious logic in model artifacts |
| | [[techniques/third-party-model-risks]] | Prevents code execution from untrusted model downloads (e.g., Hugging Face pickle models) |
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Prevents Pickle RCE in ML frameworks; safe serialization formats (ONNX, SafeTensors) eliminate code execution risks from malicious model files in registries or supply chains |

## Implementation

### Unsafe vs. Safe Formats

| Format | Risk Level | Notes |
|--------|-----------|-------|
| `pickle` / `.pkl` | **Critical** | Executes arbitrary Python on load. Attacker can wrap models with malicious code. |
| `joblib` | **Critical** | Uses pickle internally. Same risks. |
| `torch.save()` (default) | **High** | Uses pickle by default. PyTorch models saved this way are vulnerable. |
| HDF5 (`.h5`) | **Low** | Stores weights as numerical arrays. No code execution. Used by Keras. |
| SavedModel (TF) | **Low** | Protocol buffer format. Declarative graph definition. No arbitrary Python. |
| ONNX (`.onnx`) | **Low** | Cross-framework interchange format. Declarative operator graph. |
| SafeTensors | **Low** | Hugging Face format designed specifically for safe serialization. No code execution. |

### Policy: Ban Pickle for Model Storage

```python
# CI/CD gate: reject pickle-serialized models
import magic
import struct

def scan_model_file(file_path):
    """Reject model files using unsafe serialization"""
    # Check for pickle magic bytes
    with open(file_path, 'rb') as f:
        header = f.read(2)
    
    # Pickle protocol markers
    if header[0:1] == b'\x80':  # Protocol 2+
        raise SecurityError(f"Pickle serialization detected in {file_path}. "
                          f"Use SafeTensors, ONNX, or SavedModel instead.")
    
    # Check file extension
    unsafe_extensions = {'.pkl', '.pickle', '.joblib'}
    if any(file_path.endswith(ext) for ext in unsafe_extensions):
        raise SecurityError(f"Unsafe model format: {file_path}")
    
    return True
```

### Safe Loading Patterns

**PyTorch — use `weights_only=True`:**
```python
# UNSAFE: loads arbitrary objects
model = torch.load('model.pt')

# SAFE: loads only tensor data
state_dict = torch.load('model.pt', weights_only=True)
model.load_state_dict(state_dict)
```

**Hugging Face — use SafeTensors:**
```python
from safetensors.torch import load_file

# Safe loading — no pickle, no code execution
state_dict = load_file("model.safetensors")
model.load_state_dict(state_dict)
```

### Integrity Verification on Load

```python
import hashlib

def load_model_with_verification(model_path, expected_hash):
    """Load model only after verifying file integrity"""
    # Calculate hash before loading
    with open(model_path, 'rb') as f:
        file_hash = hashlib.sha256(f.read()).hexdigest()
    
    if file_hash != expected_hash:
        raise IntegrityError(
            f"Model hash mismatch: expected {expected_hash}, got {file_hash}. "
            f"Possible tampering detected."
        )
    
    # Safe load after verification
    return safe_load_model(model_path)
```

### Edge / Mobile Deployment

For models deployed to mobile or IoT devices:

- **Encrypt model files** with device-bound keys to prevent extraction and tampering
- **Verify checksums** at model load time (compare against server-signed manifest)
- **Use TFLite FlatBuffers or ONNX** — both declarative formats without code execution
- **Remote attestation** — device reports model hash to server before serving predictions

```bash
# Android: verify model integrity before loading
# Store expected hash in signed APK resources
EXPECTED_HASH="abc123..."
ACTUAL_HASH=$(sha256sum assets/model.tflite | cut -d' ' -f1)
if [ "$EXPECTED_HASH" != "$ACTUAL_HASH" ]; then
    echo "Model integrity check failed"
    exit 1
fi
```

### Pickle Scanning Tools

For environments where pickle cannot be fully eliminated:

- **Protect.AI ModelScan** — Scans serialized model files for malicious payloads before loading
- **Fickling** — Static analysis tool for pickle files; decompiles and inspects pickle bytecode
- **pickletools** (stdlib) — Low-level pickle disassembly for manual inspection

```python
import pickletools

# Inspect pickle file contents before loading
with open('suspicious_model.pkl', 'rb') as f:
    pickletools.dis(f)  # Disassemble and print opcodes
```

(Adversarial AI, p110-113, p130-133)

## Limitations & Trade-offs

**Limitations:**
- **Legacy compatibility** — Many existing models and workflows depend on pickle. Migration requires effort.
- **Custom objects** — Safe formats cannot serialize arbitrary Python objects (custom layers, custom loss functions). These require separate handling.
- **Lambda layers** — Keras Lambda layers contain Python bytecode that requires unsafe serialization. Safe formats force architectural changes. (This is a feature, not a bug.)

**Trade-offs:**
- **Flexibility vs. Security** — Pickle's ability to serialize anything is powerful but dangerous. Safe formats trade flexibility for safety.
- **Migration cost** — Converting existing model pipelines to safe formats requires testing and validation.
- **Performance** — SafeTensors is actually faster than pickle for large models due to memory-mapping support.

## Testing & Validation

1. **Format scanning** — Verify CI/CD pipeline rejects all pickle-serialized models
2. **Safe loading** — Confirm `weights_only=True` and SafeTensors loading work correctly for all model architectures
3. **Integrity checks** — Test that tampered model files are detected and rejected
4. **Edge deployment** — Verify checksum validation works on target mobile/IoT platforms
5. **Negative testing** — Attempt to smuggle pickle models through pipeline; confirm rejection

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[frameworks/atlas/case-studies/malicious-models-on-hugging-face]] | [[frameworks/atlas/tactics/execution]] | Malicious pickle models on Hugging Face exploited deserialization for RCE; SafeTensors format would have prevented this |

## Sources

> "In a Trojan horse approach, we embed small malicious functionalities directly into the model architecture. Python's pickle format allows arbitrary code execution during deserialization. Attackers create wrapper classes that intercept model prediction methods."
> — [[sources/bibliography#Adversarial AI]], p. 110-113

> "Model encryption with device-bound keys; Integrity verification on model load (checksum validation); Code obfuscation and anti-tampering (SafetyNet on Android); Remote attestation (device reports model hash to server)."
> — [[sources/bibliography#Adversarial AI]], p. 130-133
