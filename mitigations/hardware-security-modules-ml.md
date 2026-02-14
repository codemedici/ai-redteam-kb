---
title: "Hardware Security Modules for ML"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Hardware Security Modules for ML

## Summary

Hardware Security Modules (HSMs) provide tamper-resistant storage for model weights and cryptographic keys used in ML systems. By storing sensitive model artifacts in hardware-protected environments with strict access controls and audit logging, HSMs prevent unauthorized extraction or modification of models even when surrounding infrastructure is compromised. This mitigation is particularly valuable for high-value proprietary models and regulated industries requiring cryptographic-grade protection.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0047 | [[techniques/model-tampering]] | HSMs prevent unauthorized modification of model weights through hardware-enforced access controls and tamper detection |
| AML.T0024 | [[techniques/model-extraction]] | Hardware-protected storage prevents direct access to model weights even with infrastructure compromise |
| | [[techniques/model-theft]] | Physical tamper resistance and access logging deter model theft attempts |

## Implementation

### HSM-Based Model Storage Architecture

**Core components:**

1. **HSM device or cloud HSM service** (AWS CloudHSM, Azure Dedicated HSM, on-premises HSM appliances)
2. **Encrypted model wrapper**: Store encrypted model weights outside HSM, keys inside HSM
3. **Access control policies**: Define who/what can request decryption
4. **Audit logging**: Comprehensive access logs for forensic analysis

### Model Encryption with HSM-Managed Keys

**Encrypt model before storage, decrypt only for authorized use:**

```python
import boto3
import hashlib
from cryptography.fernet import Fernet

class HSMModelProtection:
    """Protect model weights using HSM-managed encryption keys"""
    
    def __init__(self, kms_key_id):
        self.kms = boto3.client('kms')
        self.kms_key_id = kms_key_id
    
    def encrypt_model(self, model_path, encrypted_path):
        """Encrypt model using HSM-managed key"""
        
        # Generate data encryption key using HSM
        response = self.kms.generate_data_key(
            KeyId=self.kms_key_id,
            KeySpec='AES_256'
        )
        
        plaintext_key = response['Plaintext']
        encrypted_key = response['CiphertextBlob']
        
        # Encrypt model with data key
        cipher = Fernet(plaintext_key)
        
        with open(model_path, 'rb') as f:
            model_data = f.read()
        
        encrypted_model = cipher.encrypt(model_data)
        
        # Store encrypted model and encrypted key
        with open(encrypted_path, 'wb') as f:
            # Write encrypted key length
            f.write(len(encrypted_key).to_bytes(4, 'big'))
            # Write encrypted key
            f.write(encrypted_key)
            # Write encrypted model
            f.write(encrypted_model)
        
        # Calculate checksum
        checksum = hashlib.sha256(model_data).hexdigest()
        
        return {
            'encrypted_path': encrypted_path,
            'checksum': checksum,
            'kms_key_id': self.kms_key_id
        }
    
    def decrypt_model(self, encrypted_path, authorized_service):
        """Decrypt model using HSM (with access control enforcement)"""
        
        with open(encrypted_path, 'rb') as f:
            # Read encrypted key length
            key_length = int.from_bytes(f.read(4), 'big')
            # Read encrypted key
            encrypted_key = f.read(key_length)
            # Read encrypted model
            encrypted_model = f.read()
        
        # Decrypt data key using HSM (access control enforced by KMS)
        try:
            response = self.kms.decrypt(
                CiphertextBlob=encrypted_key,
                KeyId=self.kms_key_id
            )
            plaintext_key = response['Plaintext']
        except Exception as e:
            log_security_event({
                'event': 'unauthorized_model_access_attempt',
                'service': authorized_service,
                'error': str(e)
            })
            raise AccessDeniedError(
                f'HSM refused to decrypt model key: {str(e)}'
            )
        
        # Decrypt model
        cipher = Fernet(plaintext_key)
        model_data = cipher.decrypt(encrypted_model)
        
        # Log successful access
        log_security_event({
            'event': 'model_decrypted',
            'service': authorized_service,
            'encrypted_path': encrypted_path,
            'timestamp': datetime.now().isoformat()
        })
        
        return model_data
```

### Access Control Policies

**HSM/KMS policy example restricting model access:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowModelTrainingPipelineEncryption",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT:role/ModelTrainingPipeline"
      },
      "Action": [
        "kms:GenerateDataKey",
        "kms:Encrypt"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowProductionInferenceDecryption",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT:role/ProductionInferenceService"
      },
      "Action": "kms:Decrypt",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "kms:EncryptionContext:environment": "production"
        }
      }
    },
    {
      "Sid": "DenyAllOtherAccess",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "kms:Decrypt",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:PrincipalArn": [
            "arn:aws:iam::ACCOUNT:role/ModelTrainingPipeline",
            "arn:aws:iam::ACCOUNT:role/ProductionInferenceService"
          ]
        }
      }
    }
  ]
}
```

### Secure Model Loading in Production

**Integrate HSM decryption into model loading:**

```python
class HSMProtectedModelLoader:
    """Load models with HSM-enforced access control"""
    
    def __init__(self, hsm_protection, service_id):
        self.hsm = hsm_protection
        self.service_id = service_id
    
    def load_model(self, encrypted_model_path):
        """Load model with HSM decryption"""
        
        # Decrypt model (HSM enforces access control)
        model_data = self.hsm.decrypt_model(
            encrypted_model_path, 
            authorized_service=self.service_id
        )
        
        # Load into memory
        import io
        import torch
        
        buffer = io.BytesIO(model_data)
        model = torch.load(buffer)
        
        return model

# Example usage
hsm = HSMModelProtection(kms_key_id='arn:aws:kms:region:account:key/...')
loader = HSMProtectedModelLoader(hsm, service_id='production-fraud-detection')

try:
    model = loader.load_model('/models/fraud-detection-encrypted.bin')
except AccessDeniedError:
    alert_security_team('Unauthorized model access attempt detected')
```

### Audit Logging and Monitoring

**Monitor HSM access for anomalies:**

```python
def analyze_hsm_access_logs():
    """Detect suspicious HSM access patterns"""
    
    logs = fetch_kms_cloudtrail_events()
    
    anomalies = []
    
    for log in logs:
        # Unusual access times
        if is_off_hours(log['timestamp']):
            anomalies.append({
                'type': 'off_hours_access',
                'user': log['user'],
                'time': log['timestamp']
            })
        
        # Access from unexpected locations
        if log['source_ip'] not in EXPECTED_IP_RANGES:
            anomalies.append({
                'type': 'unexpected_location',
                'user': log['user'],
                'ip': log['source_ip']
            })
        
        # Failed access attempts
        if log['event'] == 'Decrypt' and log['error']:
            anomalies.append({
                'type': 'failed_decryption',
                'user': log['user'],
                'error': log['error']
            })
    
    if anomalies:
        alert_security_team(f'{len(anomalies)} HSM anomalies detected', anomalies)
```

## Limitations & Trade-offs

**Limitations:**
- **Cost**: HSM hardware and cloud HSM services are expensive compared to software-only encryption
- **Performance overhead**: Encryption/decryption operations add latency to model loading
- **Operational complexity**: Requires specialized expertise to configure and manage HSMs
- **Key recovery**: Losing HSM access can result in permanent data loss without proper backup procedures

**Trade-offs:**
- **Security vs. Accessibility**: HSM protection can complicate legitimate model access for debugging or testing
- **Centralization risk**: HSM becomes single point of failure if not properly replicated
- **Vendor lock-in**: Cloud HSM solutions may create dependencies on specific providers

**Bypass Scenarios:**
- **Compromised credentials**: Attacker steals credentials of authorized service with decrypt permissions
- **Memory extraction**: Model decrypted into memory can be extracted if host is compromised
- **Side-channel attacks**: Sophisticated attackers may attempt timing or power analysis attacks on HSM operations

## Testing & Validation

**Functional Testing:**
1. **Encryption/decryption**: Verify models can be encrypted and decrypted successfully
2. **Access control enforcement**: Confirm unauthorized services cannot decrypt models
3. **Audit logging**: Verify all access attempts are logged comprehensively

**Security Testing:**
1. **Unauthorized access**: Attempt to decrypt models without proper credentials
2. **Credential theft simulation**: Test detection of stolen credentials being used
3. **Audit log tampering**: Verify audit logs are tamper-resistant
4. **HSM availability**: Test system behavior when HSM is unavailable

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Hardware Security Modules (HSMs): Store model weights in HSMs or secure enclaves with strict access controls and audit logging."
> — Extracted from Model Tampering mitigation section
