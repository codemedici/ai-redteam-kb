---
title: "Cryptographic Model Verification"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/generative-ai-security
atlas: AML.M0015
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Cryptographic Model Verification

## Summary

Cryptographic model verification uses digital signatures to establish model artifact integrity and authenticity. By signing all model checkpoints with cryptographic keys and verifying signatures before loading into production, organizations can detect tampering and ensure only trusted, unmodified models are deployed. This mitigation is critical for defending against model tampering, backdoor injection, and supply chain attacks targeting model artifacts.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0047 | [[techniques/model-tampering]] | Detects unauthorized modifications to model weights or architecture through signature verification; prevents deployment of tampered checkpoints |
| AML.T0018 | [[techniques/trojan-injection]] | Signature verification detects post-training trojan insertion when model artifacts are modified |
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Digital signatures verify model integrity against known-good values; prevents deployment of compromised third-party models |

## Implementation

### Digital Signature Generation

**Sign all model checkpoints during training:**

```python
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import rsa, padding
import json
import hashlib
import datetime

def sign_model_checkpoint(model_path, private_key_path, training_metadata):
    """Generate cryptographic signature for model checkpoint"""
    
    # Load private key
    with open(private_key_path, 'rb') as f:
        private_key = serialization.load_pem_private_key(
            f.read(), 
            password=None
        )
    
    # Calculate model file hash
    with open(model_path, 'rb') as f:
        model_hash = hashlib.sha256(f.read()).hexdigest()
    
    # Create signature manifest
    manifest = {
        'model_path': model_path,
        'model_hash': model_hash,
        'training_metadata': {
            'dataset_hash': training_metadata['dataset_hash'],
            'training_start': training_metadata['start_time'],
            'training_end': training_metadata['end_time'],
            'hyperparameters': training_metadata['hyperparameters'],
            'framework_version': training_metadata['framework_version']
        },
        'signature_timestamp': datetime.datetime.now().isoformat(),
        'signer_id': 'training-pipeline-v1'
    }
    
    # Serialize manifest deterministically
    manifest_json = json.dumps(manifest, sort_keys=True).encode()
    
    # Sign manifest
    signature = private_key.sign(
        manifest_json,
        padding.PSS(
            mgf=padding.MGF1(hashes.SHA256()),
            salt_length=padding.PSS.MAX_LENGTH
        ),
        hashes.SHA256()
    )
    
    # Save signature file alongside model
    signature_path = model_path + '.sig'
    signature_data = {
        'manifest': manifest,
        'signature': signature.hex()
    }
    
    with open(signature_path, 'w') as f:
        json.dump(signature_data, f, indent=2)
    
    return signature_path
```

### Signature Verification Before Deployment

**Verify model integrity before loading:**

```python
def verify_model_signature(model_path, public_key_path):
    """Verify model has not been tampered with"""
    
    # Load public key
    with open(public_key_path, 'rb') as f:
        public_key = serialization.load_pem_public_key(f.read())
    
    # Load signature file
    signature_path = model_path + '.sig'
    with open(signature_path, 'r') as f:
        signature_data = json.load(f)
    
    manifest = signature_data['manifest']
    signature = bytes.fromhex(signature_data['signature'])
    
    # Verify signature
    try:
        manifest_json = json.dumps(manifest, sort_keys=True).encode()
        public_key.verify(
            signature,
            manifest_json,
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )
    except Exception as e:
        raise SignatureVerificationError(
            f'Signature verification failed: {str(e)}'
        )
    
    # Verify model hash matches manifest
    with open(model_path, 'rb') as f:
        current_hash = hashlib.sha256(f.read()).hexdigest()
    
    if current_hash != manifest['model_hash']:
        raise IntegrityError(
            f'Model hash mismatch: expected {manifest["model_hash"]}, '
            f'got {current_hash}'
        )
    
    return manifest
```

### Integration with Deployment Pipeline

**Enforce signature verification as deployment gate:**

```python
class SecureModelLoader:
    """Model loader with mandatory signature verification"""
    
    def __init__(self, public_key_path):
        self.public_key_path = public_key_path
    
    def load_model(self, model_path):
        """Load model only after verifying signature"""
        
        # Verify signature (fails if tampered or unsigned)
        manifest = verify_model_signature(model_path, self.public_key_path)
        
        # Log verification event
        log_security_event({
            'event': 'model_loaded',
            'model_path': model_path,
            'model_hash': manifest['model_hash'],
            'signature_verified': True,
            'training_metadata': manifest['training_metadata']
        })
        
        # Load model (using framework-specific loader)
        model = torch.load(model_path)
        return model

# Example usage in deployment
loader = SecureModelLoader(public_key_path='/keys/model-signing-pub.pem')

try:
    model = loader.load_model('/models/fraud-detection-v1.2.pt')
    deploy_to_production(model)
except (SignatureVerificationError, IntegrityError) as e:
    alert_security_team(f'Model integrity violation: {e}')
    quarantine_model('/models/fraud-detection-v1.2.pt')
    rollback_to_last_known_good()
```

### Key Management

- **Private key security**: Store signing keys in Hardware Security Modules (HSMs) or secure key management services (AWS KMS, Azure Key Vault)
- **Key rotation**: Implement regular key rotation with signature re-signing of active models
- **Access control**: Restrict private key access to automated training pipelines only; no human access
- **Public key distribution**: Distribute verification keys to all deployment environments through secure channels

### Third-Party Model Verification

**Verify external models before integration:**

```python
def verify_third_party_model(model_path, expected_hash, trusted_signers):
    """Verify third-party model from external source"""
    
    # Verify hash against published value
    with open(model_path, 'rb') as f:
        actual_hash = hashlib.sha256(f.read()).hexdigest()
    
    if actual_hash != expected_hash:
        raise IntegrityError(
            f'Third-party model hash mismatch: '
            f'expected {expected_hash}, got {actual_hash}'
        )
    
    # Verify signature if available
    signature_path = model_path + '.sig'
    if os.path.exists(signature_path):
        with open(signature_path, 'r') as f:
            signature_data = json.load(f)
        
        signer_id = signature_data['manifest']['signer_id']
        
        if signer_id not in trusted_signers:
            raise UntrustedSignerError(
                f'Signer {signer_id} not in trusted list'
            )
        
        # Get signer's public key
        public_key_path = trusted_signers[signer_id]['public_key']
        verify_model_signature(model_path, public_key_path)
    else:
        log_warning(
            f'No signature available for third-party model {model_path}'
        )
```

## Limitations & Trade-offs

**Limitations:**
- **Cannot prevent initial tampering**: Signatures detect tampering after the fact but don't prevent malicious modification of source files
- **Key compromise**: If signing keys are stolen, attacker can sign malicious models as legitimate
- **Insider threat**: Authorized signers can intentionally sign backdoored models
- **Signature availability**: Third-party models may not provide signatures

**Trade-offs:**
- **Deployment latency**: Signature verification adds computational overhead to model loading
- **Operational complexity**: Key management, rotation, and distribution require robust operational processes
- **Storage overhead**: Signature files and manifests increase storage requirements

**Bypass Scenarios:**
- **Compromised signing infrastructure**: Attacker gains access to private keys or signing pipeline
- **Man-in-the-middle**: Attacker intercepts and replaces both model and signature files before verification
- **Verification bypass**: Deployment pipeline misconfigured to skip verification on failure

## Testing & Validation

**Functional Testing:**
1. **Signature generation**: Verify signatures correctly generated for all model types
2. **Signature verification**: Confirm legitimate signatures validate successfully
3. **Tamper detection**: Modify signed model, verify detection on load
4. **Unsigned model rejection**: Confirm deployment fails for unsigned models

**Security Testing:**
1. **Signature forgery**: Attempt to forge signatures without private key access
2. **Hash collision**: Test resistance to hash collision attacks
3. **Key theft simulation**: Test detection and response to compromised keys
4. **Verification bypass**: Attempt to deploy models without verification

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/poisongpt]] | [[frameworks/atlas/tactics/persistence]] | Cryptographic signing would have detected backdoored checkpoint distributed via Hugging Face |

## Sources

> "Cryptographic Verification: Sign all model checkpoints with digital signatures; verify signatures before loading into production."
> — Extracted from Model Tampering mitigation section
