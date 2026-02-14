---
title: "Cryptographic Output Signing"
tags:
  - type/mitigation
  - target/llm-app
  - target/ml-model
  - target/agent
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Cryptographic Output Signing

## Summary

Cryptographic output signing ensures model predictions cannot be tampered with during transit or at integration boundaries by digitally signing each output with a private key. Consuming systems verify signatures using the corresponding public key before acting on predictions, creating a cryptographic chain of trust from model inference to downstream decision-making. This mitigation is critical for defending against output integrity attacks where adversaries intercept and modify model responses between generation and consumption.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0051 | [[techniques/output-integrity-attack]] | Prevents tampering with model outputs through cryptographic verification; forged outputs fail signature validation |

## Implementation

### Digital Signature Architecture

**Model service signs all outputs:**

```python
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import rsa, padding
import json
import hashlib
import datetime

class SignedModelService:
    """Model service that cryptographically signs all outputs"""
    
    def __init__(self, model, private_key_path):
        self.model = model
        
        # Load private signing key
        with open(private_key_path, 'rb') as f:
            self.private_key = serialization.load_pem_private_key(
                f.read(),
                password=None
            )
    
    def predict_with_signature(self, input_data):
        """Generate prediction and sign output"""
        
        # Model inference
        prediction = self.model.predict(input_data)
        
        # Create output manifest
        output_manifest = {
            'prediction': prediction.tolist() if hasattr(prediction, 'tolist') else prediction,
            'model_version': self.model.version,
            'timestamp': datetime.datetime.now().isoformat(),
            'input_hash': hashlib.sha256(
                json.dumps(input_data, sort_keys=True).encode()
            ).hexdigest()
        }
        
        # Serialize deterministically
        manifest_json = json.dumps(output_manifest, sort_keys=True).encode()
        
        # Sign the manifest
        signature = self.private_key.sign(
            manifest_json,
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )
        
        # Return signed output
        return {
            'output': output_manifest,
            'signature': signature.hex(),
            'signing_key_id': 'model-service-key-v1'  # Key identifier
        }
```

**Consuming system verifies signatures:**

```python
class SecureConsumer:
    """Downstream system that verifies signed model outputs"""
    
    def __init__(self, public_key_path):
        # Load public verification key
        with open(public_key_path, 'rb') as f:
            self.public_key = serialization.load_pem_public_key(f.read())
    
    def verify_and_consume(self, signed_output):
        """Verify output signature before acting on prediction"""
        
        try:
            # Extract components
            output_manifest = signed_output['output']
            signature_hex = signed_output['signature']
            signature = bytes.fromhex(signature_hex)
            
            # Reconstruct manifest
            manifest_json = json.dumps(output_manifest, sort_keys=True).encode()
            
            # Verify signature
            self.public_key.verify(
                signature,
                manifest_json,
                padding.PSS(
                    mgf=padding.MGF1(hashes.SHA256()),
                    salt_length=padding.PSS.MAX_LENGTH
                ),
                hashes.SHA256()
            )
            
            # Signature valid - safe to act on prediction
            return output_manifest['prediction'], "VERIFIED"
            
        except Exception as e:
            # Signature verification failed - reject output
            self._handle_integrity_failure(signed_output, str(e))
            return None, f"VERIFICATION_FAILED: {str(e)}"
    
    def _handle_integrity_failure(self, signed_output, error):
        """Handle integrity violation"""
        # Log security incident
        print(f"[SECURITY] Output integrity violation detected: {error}")
        print(f"[SECURITY] Suspicious output: {signed_output}")
        
        # Alert security team
        # Trigger incident response
        # Quarantine affected systems
```

**Integration example (API endpoint):**

```python
from flask import Flask, request, jsonify

app = Flask(__name__)
model_service = SignedModelService(model, 'private_key.pem')

@app.route('/api/v1/predict', methods=['POST'])
def predict_endpoint():
    """Model inference endpoint with signed outputs"""
    input_data = request.get_json()
    
    # Generate signed prediction
    signed_output = model_service.predict_with_signature(input_data)
    
    # Return to consuming system
    return jsonify(signed_output)


# Consuming application
consumer = SecureConsumer('public_key.pem')

def process_prediction(api_response):
    """Process model prediction with signature verification"""
    
    # Verify signature before acting
    prediction, status = consumer.verify_and_consume(api_response)
    
    if status == "VERIFIED":
        # Signature valid - execute business logic
        execute_action(prediction)
    else:
        # Integrity failure - reject and alert
        print(f"Cannot process prediction: {status}")
        trigger_security_alert()
```

### Key Management

**Key generation and rotation:**

```python
from cryptography.hazmat.primitives.asymmetric import rsa

def generate_signing_keypair():
    """Generate RSA key pair for output signing"""
    
    # Generate private key
    private_key = rsa.generate_private_key(
        public_exponent=65537,
        key_size=4096  # Strong key size for high-security applications
    )
    
    # Extract public key
    public_key = private_key.public_key()
    
    # Serialize keys
    private_pem = private_key.private_bytes(
        encoding=serialization.Encoding.PEM,
        format=serialization.PrivateFormat.PKCS8,
        encryption_algorithm=serialization.NoEncryption()  # Encrypt in production
    )
    
    public_pem = public_key.public_bytes(
        encoding=serialization.Encoding.PEM,
        format=serialization.PublicFormat.SubjectPublicKeyInfo
    )
    
    return private_pem, public_pem


def rotate_signing_keys():
    """Rotate signing keys (execute periodically)"""
    
    # Generate new keypair
    new_private, new_public = generate_signing_keypair()
    
    # Distribute new public key to all consumers
    distribute_public_key(new_public)
    
    # Grace period: Support both old and new keys
    # After grace period: Retire old key, use only new key
    # Document key rotation in audit logs
```

**Secure key storage:**

- **Private keys:** Store in Hardware Security Module (HSM) or cloud KMS (AWS KMS, Azure Key Vault, GCP KMS)
- **Public keys:** Distribute via secure channel; can be public but must be authentic (avoid MITM substitution)
- **Key rotation:** Rotate signing keys every 90 days or after suspected compromise
- **Access control:** Restrict private key access to model service only; least privilege

### Performance Optimization

**Signature verification is computationally expensive. Optimize:**

**1. Caching verified outputs (if applicable):**
```python
from functools import lru_cache

@lru_cache(maxsize=1000)
def cached_verify(output_hash, signature):
    """Cache signature verification results"""
    # Verify signature
    # Cache result for repeated identical outputs
    pass
```

**2. Batch verification:**
```python
def verify_batch(signed_outputs):
    """Verify multiple outputs in parallel"""
    from concurrent.futures import ThreadPoolExecutor
    
    with ThreadPoolExecutor(max_workers=10) as executor:
        results = executor.map(verify_and_consume, signed_outputs)
    
    return list(results)
```

**3. Selective signing:**
- Sign only critical predictions (high-value decisions, security-critical outputs)
- Use checksums for low-risk outputs (faster but less secure)

## Limitations & Trade-offs

**Limitations:**

- **Performance overhead:** Cryptographic signing/verification adds latency (typically 1-10ms per output)
- **Key management complexity:** Requires secure key storage, rotation, and distribution infrastructure
- **Deployment complexity:** Both model service and all consuming systems must implement signing/verification
- **Replay attacks:** Signatures verify integrity but not freshness; attacker can replay old valid signed outputs
- **Key compromise:** If private key compromised, attacker can forge valid signatures

**Trade-offs:**

- **Security vs. Latency:** Stronger keys (4096-bit RSA) more secure but slower than smaller keys
- **Security vs. Complexity:** Adding cryptographic infrastructure increases system complexity
- **Comprehensive coverage:** Must sign ALL outputs or attackers bypass unsigned paths
- **Backward compatibility:** Introducing signing requires updating all consuming systems

**Bypass Scenarios:**

- **Unsigned code paths:** If model service has unsigned fallback endpoints, attackers use those
- **Key theft:** Compromised private key allows forging valid signatures
- **Replay attacks:** Attacker replays old valid signed outputs; mitigate with timestamp validation
- **Verification bypass:** If consuming system fails to verify signatures, signing provides no protection

## Testing & Validation

**Functional Testing:**

1. **Signature generation:** Verify all model outputs include valid signatures
2. **Verification success:** Confirm consuming systems accept valid signed outputs
3. **Verification rejection:** Confirm tampered outputs rejected (modify prediction, should fail verification)
4. **Key rotation:** Verify graceful key rotation without service disruption
5. **Performance:** Measure latency impact of signing/verification

**Security Testing:**

1. **Tampering detection:** Modify signed outputs and verify detection
2. **Replay attack:** Attempt to replay old signed outputs (check timestamp validation)
3. **Key compromise simulation:** Test incident response for key compromise scenarios
4. **Bypass testing:** Verify no unsigned code paths exist

**Monitoring:**

- Track signature verification failures (should be rare; failures indicate attack or infrastructure issue)
- Monitor key usage and rotation schedules
- Alert on anomalous verification failure rates

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Cryptographic Output Signing: Model service digitally signs all outputs with private key; consuming systems verify signatures with public key before acting."
> — Derived from [[sources/bibliography#OWASP ML Security Top 10]], Output Integrity Attack mitigation guidance

## Related

- **Combined with:** [[mitigations/secure-transport-layer-security]], [[mitigations/output-consistency-monitoring]], [[mitigations/cryptographic-model-verification]]
- **Depends on:** Secure key management infrastructure (HSM, KMS), public key distribution
