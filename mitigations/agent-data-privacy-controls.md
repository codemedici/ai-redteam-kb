---
title: "Agent Data Privacy Controls"
tags:
  - type/mitigation
  - target/agent
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Agent Data Privacy Controls

## Summary

Agent data privacy controls ensure that Large Action Models (LAMs) and autonomous GenAI agents handle sensitive data—customer information, financial records, intellectual property—with appropriate confidentiality, integrity, and regulatory compliance. Since LAMs actively interact with and process sensitive data to perform tasks on behalf of users, robust privacy controls including encryption, secure data handling practices, proper data retention policies, and compliance with regulations like GDPR are essential to prevent data leakage, unauthorized access, and regulatory violations.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agent-based-genai-security]] | Protects sensitive data accessed and processed by LAMs through encryption, secure handling, and compliance enforcement |
| | [[techniques/training-data-extraction]] | Prevents LAM from inadvertently exposing sensitive data from training or operational context |
| | [[techniques/membership-inference]] | Limits information leakage that could reveal whether specific data was used in agent operations |

## Implementation

### Data Classification and Tagging

**Classify data accessed by agents:**

Before agents interact with data, classify and tag it according to sensitivity:

- **Public**: No restrictions (marketing materials, public documentation)
- **Internal**: Company-confidential (internal communications, business plans)
- **Confidential**: Sensitive business data (customer lists, pricing strategies)
- **Restricted**: Regulated data (PII, PHI, financial records, trade secrets)

**Enforce access controls based on classification:**

```python
class DataClassificationPolicy:
    """Enforce data access based on classification and agent role"""
    
    CLASSIFICATION_LEVELS = {
        'public': 0,
        'internal': 1,
        'confidential': 2,
        'restricted': 3
    }
    
    def __init__(self, agent_clearance: str):
        """
        Initialize policy with agent's data clearance level
        
        Args:
            agent_clearance: Maximum classification agent can access
        """
        self.agent_clearance_level = self.CLASSIFICATION_LEVELS.get(
            agent_clearance, 0
        )
    
    def can_access_data(self, data_classification: str) -> bool:
        """Check if agent can access data with given classification"""
        data_level = self.CLASSIFICATION_LEVELS.get(data_classification)
        
        if data_level is None:
            raise ValueError(f"Unknown classification: {data_classification}")
        
        authorized = data_level <= self.agent_clearance_level
        
        if not authorized:
            log_security_event('data_access_denied', {
                'agent_clearance': self.agent_clearance_level,
                'data_classification': data_classification
            })
        
        return authorized
```

### Encryption at Rest and in Transit

**Encrypt all sensitive data:**

LAMs must handle encrypted data throughout its lifecycle:

1. **At Rest**: Data stored by agents encrypted using AES-256 or equivalent
2. **In Transit**: All agent communications over TLS 1.3+ with strong cipher suites
3. **In Memory**: For highly sensitive data, use in-memory encryption during processing
4. **In Logs**: Redact or encrypt sensitive data in agent logs

**Implementation pattern:**

```python
from cryptography.fernet import Fernet
import json

class AgentDataEncryption:
    """Handle encryption for agent data storage"""
    
    def __init__(self, encryption_key: bytes):
        self.cipher = Fernet(encryption_key)
    
    def encrypt_sensitive_data(self, data: dict, sensitive_fields: list) -> dict:
        """
        Encrypt sensitive fields in data dictionary
        
        Args:
            data: Data dictionary
            sensitive_fields: List of field names to encrypt
        
        Returns:
            Data with sensitive fields encrypted
        """
        encrypted_data = data.copy()
        
        for field in sensitive_fields:
            if field in encrypted_data:
                plaintext = json.dumps(encrypted_data[field]).encode('utf-8')
                encrypted_value = self.cipher.encrypt(plaintext).decode('utf-8')
                encrypted_data[field] = f"ENC:{encrypted_value}"
        
        return encrypted_data
    
    def decrypt_sensitive_data(self, data: dict, sensitive_fields: list) -> dict:
        """Decrypt sensitive fields in data dictionary"""
        decrypted_data = data.copy()
        
        for field in sensitive_fields:
            if field in decrypted_data and isinstance(decrypted_data[field], str):
                if decrypted_data[field].startswith("ENC:"):
                    encrypted_value = decrypted_data[field][4:].encode('utf-8')
                    plaintext = self.cipher.decrypt(encrypted_value).decode('utf-8')
                    decrypted_data[field] = json.loads(plaintext)
        
        return decrypted_data
```

### Secure Data Handling Practices

**Implement data handling guardrails:**

- **Minimize data retention**: Agents should not cache sensitive data longer than necessary
- **Secure deletion**: Overwrite sensitive data when no longer needed (don't just delete references)
- **Logging redaction**: Automatically redact PII, credentials, and confidential data from logs
- **Memory management**: Clear sensitive data from memory after processing

**Example: Automatic log redaction:**

```python
import re

class AgentLogRedactor:
    """Redact sensitive data from agent logs"""
    
    PATTERNS = {
        'email': r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b',
        'ssn': r'\b\d{3}-\d{2}-\d{4}\b',
        'credit_card': r'\b\d{4}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}\b',
        'api_key': r'\b[A-Za-z0-9_-]{32,}\b',
        'ip_address': r'\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b'
    }
    
    def redact_log_message(self, message: str) -> str:
        """Redact sensitive patterns from log message"""
        redacted = message
        
        for pattern_name, pattern in self.PATTERNS.items():
            redacted = re.sub(pattern, f'[REDACTED_{pattern_name.upper()}]', redacted)
        
        return redacted
    
    def log_agent_action(self, agent_id: str, action: str, context: dict):
        """Log agent action with automatic redaction"""
        # Convert context to string
        context_str = json.dumps(context)
        
        # Redact sensitive patterns
        redacted_context = self.redact_log_message(context_str)
        
        # Log redacted version
        log_entry = {
            'timestamp': datetime.now().isoformat(),
            'agent_id': agent_id,
            'action': action,
            'context': redacted_context
        }
        
        write_to_log(log_entry)
```

### Data Retention and Deletion Policies

**Enforce time-limited data retention:**

LAMs should not retain data indefinitely. Implement:

- **Automatic expiration**: Data accessed by agents expires after defined period
- **User-controlled deletion**: Users can request deletion of their data from agent systems
- **Compliance-driven retention**: Align retention with regulatory requirements (GDPR right to be forgotten)

**Example: Automatic data expiration:**

```python
class AgentDataRetentionPolicy:
    """Enforce data retention limits for agent storage"""
    
    RETENTION_PERIODS = {
        'conversation_history': timedelta(days=90),
        'user_preferences': timedelta(days=365),
        'transaction_logs': timedelta(days=2555),  # 7 years for financial
        'temporary_cache': timedelta(hours=24)
    }
    
    def __init__(self, data_store):
        self.data_store = data_store
    
    def enforce_retention_policy(self):
        """Delete expired data according to retention policy"""
        now = datetime.now()
        
        for data_type, retention_period in self.RETENTION_PERIODS.items():
            cutoff_date = now - retention_period
            
            # Find expired data
            expired_items = self.data_store.find_items(
                data_type=data_type,
                created_before=cutoff_date
            )
            
            # Securely delete
            for item in expired_items:
                self._secure_delete(item)
                
                log_data_deletion(
                    data_type=data_type,
                    item_id=item.id,
                    reason='retention_policy_expiration'
                )
    
    def _secure_delete(self, item):
        """Securely delete data (overwrite before deletion)"""
        # For sensitive data, overwrite before deleting
        if item.classification in ['confidential', 'restricted']:
            self.data_store.overwrite_item(item.id, random_bytes())
        
        self.data_store.delete_item(item.id)
```

### GDPR and Regulatory Compliance

**Implement GDPR-compliant agent behaviors:**

For agents operating in jurisdictions with data protection regulations:

1. **Consent management**: Verify user consent before processing personal data
2. **Purpose limitation**: Only use data for declared purposes
3. **Data minimization**: Collect and retain only necessary data
4. **Right to access**: Provide users access to data agent has collected
5. **Right to erasure**: Allow users to delete their data
6. **Data portability**: Export user data in machine-readable format
7. **Breach notification**: Alert users and authorities within 72 hours of data breach

**Example: GDPR compliance enforcement:**

```python
class GDPRComplianceEnforcer:
    """Enforce GDPR compliance for agent data processing"""
    
    def __init__(self, user_id: str, data_store):
        self.user_id = user_id
        self.data_store = data_store
    
    def check_consent(self, data_type: str, processing_purpose: str) -> bool:
        """Verify user has consented to data processing"""
        consent_record = self.data_store.get_user_consent(
            user_id=self.user_id,
            data_type=data_type,
            purpose=processing_purpose
        )
        
        if not consent_record:
            log_compliance_event('missing_consent', {
                'user_id': self.user_id,
                'data_type': data_type,
                'purpose': processing_purpose
            })
            return False
        
        # Check if consent is still valid (not withdrawn, not expired)
        if consent_record.withdrawn or consent_record.expired:
            return False
        
        return True
    
    def handle_erasure_request(self):
        """Process GDPR right to erasure (right to be forgotten)"""
        # Find all user data
        user_data_items = self.data_store.find_all_user_data(self.user_id)
        
        # Delete each item
        for item in user_data_items:
            self._secure_delete(item)
        
        # Log erasure for compliance audit
        log_compliance_event('gdpr_erasure_completed', {
            'user_id': self.user_id,
            'items_deleted': len(user_data_items),
            'timestamp': datetime.now()
        })
    
    def export_user_data(self) -> dict:
        """Export user data in machine-readable format (GDPR data portability)"""
        user_data_items = self.data_store.find_all_user_data(self.user_id)
        
        export_package = {
            'user_id': self.user_id,
            'export_date': datetime.now().isoformat(),
            'data': [item.to_dict() for item in user_data_items]
        }
        
        log_compliance_event('gdpr_data_export', {
            'user_id': self.user_id,
            'item_count': len(user_data_items)
        })
        
        return export_package
```

## Limitations & Trade-offs

**Limitations:**

- **Cannot prevent all inference attacks**: Even with encryption, agent behavior may leak information about underlying data
- **Performance overhead**: Encryption/decryption adds latency to data access operations
- **Key management complexity**: Secure storage and rotation of encryption keys requires dedicated infrastructure
- **Regulatory variance**: Different jurisdictions have different requirements (GDPR, CCPA, HIPAA)

**Trade-offs:**

- **Security vs. Performance**: Strong encryption improves privacy but reduces agent response speed
- **Retention vs. Compliance**: Longer retention aids agent learning but conflicts with data minimization principles
- **Utility vs. Privacy**: More data access enables better agent performance but increases privacy risk

**Bypass Scenarios:**

- **Side-channel leakage**: Encrypted data may leak via timing attacks, memory access patterns
- **Insider access**: Authorized agent operators may misuse access to encrypted data
- **Regulatory gaps**: Agent may be compliant in one jurisdiction but violate laws in another

## Testing & Validation

**Functional Testing:**

1. **Encryption validation**: Verify all sensitive data encrypted at rest and in transit
2. **Redaction testing**: Confirm logs do not contain plaintext sensitive data
3. **Retention enforcement**: Test that data expires according to policy
4. **Consent checks**: Verify agent respects user consent preferences

**Security Testing:**

1. **Encryption strength**: Validate use of approved algorithms (AES-256, TLS 1.3+)
2. **Key management**: Test encryption key storage and rotation procedures
3. **Data leakage**: Search logs and caches for inadvertent sensitive data exposure
4. **Access control bypass**: Attempt to access classified data without proper clearance

**Compliance Testing:**

1. **GDPR compliance**: Test right to access, erasure, and data portability
2. **Audit trail**: Verify all data processing activities are logged
3. **Breach notification**: Test alert mechanisms for data breach scenarios

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "LAMs will likely interact with sensitive data, such as customer information, financial records, and intellectual property. Ensuring the privacy and confidentiality of this data is paramount. This involves implementing strong encryption, securing data handling practices, and enforcing proper data retention policy and compliance with relevant regulations such as GDPR."
> — [[sources/bibliography#Generative AI Security]], p. 215 (§7.4.2)
