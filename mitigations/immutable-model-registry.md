---
title: "Immutable Model Registry"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Immutable Model Registry

## Summary

Immutable model registries store machine learning models in write-once repositories with comprehensive versioning and cryptographic lineage tracking. By preventing modification or deletion of published model versions, immutable registries create a tamper-evident audit trail that enables detection of unauthorized changes and supports forensic investigation. This mitigation ensures model integrity throughout the deployment lifecycle and prevents attackers from covering their tracks after tampering.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0047 | [[techniques/model-tampering]] | Write-once storage prevents overwriting of legitimate model versions; cryptographic lineage detects unauthorized modifications |
| AML.T0018 | [[techniques/trojan-injection]] | Immutability ensures backdoored models cannot replace legitimate versions without detection |
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Lineage tracking verifies model provenance and detects injection of compromised artifacts |

## Implementation

### Write-Once Storage Architecture

**Core design principles:**

1. **Immutable artifacts**: Once published, model versions cannot be modified or deleted
2. **Content-addressable storage**: Models identified by cryptographic hash of contents
3. **Versioning**: All model iterations preserved with complete history
4. **Lineage tracking**: Cryptographic chain linking models to training data and pipeline

### Content-Addressable Model Storage

**Store models by content hash to ensure immutability:**

```python
import hashlib
import json
import os
from datetime import datetime

class ImmutableModelRegistry:
    """Model registry with write-once semantics"""
    
    def __init__(self, storage_path):
        self.storage_path = storage_path
        self.metadata_db = {}  # In production: use immutable database
    
    def publish_model(self, model_path, metadata):
        """Publish model to immutable registry"""
        
        # Compute content hash
        with open(model_path, 'rb') as f:
            model_data = f.read()
        content_hash = hashlib.sha256(model_data).hexdigest()
        
        # Check if model already exists
        storage_path = os.path.join(
            self.storage_path, 
            f'{content_hash}.model'
        )
        
        if os.path.exists(storage_path):
            raise ModelAlreadyExistsError(
                f'Model with hash {content_hash} already published'
            )
        
        # Create model record
        model_record = {
            'content_hash': content_hash,
            'storage_path': storage_path,
            'published_at': datetime.now().isoformat(),
            'metadata': metadata,
            'version': self._generate_version(metadata),
            'lineage': self._compute_lineage(metadata)
        }
        
        # Write model to immutable storage (write-once)
        with open(storage_path, 'wb') as f:
            f.write(model_data)
        
        # Make file immutable (Linux)
        os.system(f'chattr +i {storage_path}')
        
        # Store metadata in immutable ledger
        self._append_to_ledger(model_record)
        
        # Create human-readable tag (optional, mutable pointer)
        if 'tag' in metadata:
            self._create_tag(metadata['tag'], content_hash)
        
        return model_record
    
    def retrieve_model(self, identifier):
        """Retrieve model by hash, version, or tag"""
        
        # Resolve identifier to content hash
        if identifier.startswith('sha256:'):
            content_hash = identifier[7:]
        elif identifier.startswith('v'):
            content_hash = self._resolve_version(identifier)
        else:
            content_hash = self._resolve_tag(identifier)
        
        # Retrieve model
        storage_path = os.path.join(
            self.storage_path,
            f'{content_hash}.model'
        )
        
        if not os.path.exists(storage_path):
            raise ModelNotFoundError(
                f'Model {identifier} not found in registry'
            )
        
        # Verify integrity
        with open(storage_path, 'rb') as f:
            model_data = f.read()
        
        actual_hash = hashlib.sha256(model_data).hexdigest()
        if actual_hash != content_hash:
            raise IntegrityError(
                f'Model corruption detected: expected {content_hash}, '
                f'got {actual_hash}'
            )
        
        # Log retrieval
        self._log_access({
            'event': 'model_retrieved',
            'identifier': identifier,
            'content_hash': content_hash,
            'timestamp': datetime.now().isoformat()
        })
        
        return model_data, self.metadata_db[content_hash]
    
    def _append_to_ledger(self, record):
        """Append model record to immutable ledger"""
        
        ledger_path = os.path.join(self.storage_path, 'ledger.jsonl')
        
        # Append record (append-only)
        with open(ledger_path, 'a') as f:
            f.write(json.dumps(record) + '\n')
        
        # Update in-memory index
        self.metadata_db[record['content_hash']] = record
    
    def _compute_lineage(self, metadata):
        """Compute cryptographic lineage chain"""
        
        lineage = {
            'training_data_hash': metadata.get('dataset_hash'),
            'parent_model_hash': metadata.get('parent_model'),
            'training_pipeline_version': metadata.get('pipeline_version'),
            'training_commit': metadata.get('git_commit')
        }
        
        # Compute lineage hash
        lineage_json = json.dumps(lineage, sort_keys=True)
        lineage_hash = hashlib.sha256(lineage_json.encode()).hexdigest()
        
        lineage['lineage_hash'] = lineage_hash
        return lineage
    
    def verify_lineage(self, model_hash, expected_lineage):
        """Verify model lineage matches expected values"""
        
        record = self.metadata_db.get(model_hash)
        if not record:
            raise ModelNotFoundError(f'Model {model_hash} not found')
        
        actual_lineage = record['lineage']
        
        # Verify lineage hash
        lineage_copy = actual_lineage.copy()
        stored_hash = lineage_copy.pop('lineage_hash')
        
        recomputed_hash = hashlib.sha256(
            json.dumps(lineage_copy, sort_keys=True).encode()
        ).hexdigest()
        
        if stored_hash != recomputed_hash:
            raise IntegrityError('Lineage hash mismatch - tampering detected')
        
        # Verify against expected lineage
        for key, expected_value in expected_lineage.items():
            if actual_lineage.get(key) != expected_value:
                raise LineageVerificationError(
                    f'Lineage mismatch for {key}: '
                    f'expected {expected_value}, got {actual_lineage.get(key)}'
                )
        
        return True
```

### Tamper-Evident Ledger

**Blockchain-style ledger for model publications:**

```python
class ModelPublicationLedger:
    """Immutable ledger tracking all model publications"""
    
    def __init__(self):
        self.chain = []
        self._initialize_genesis_block()
    
    def _initialize_genesis_block(self):
        """Create first block in chain"""
        genesis = {
            'index': 0,
            'timestamp': datetime.now().isoformat(),
            'event': 'ledger_initialized',
            'previous_hash': '0' * 64
        }
        genesis['hash'] = self._compute_hash(genesis)
        self.chain.append(genesis)
    
    def add_publication(self, model_hash, metadata):
        """Add model publication to ledger"""
        
        previous_block = self.chain[-1]
        
        block = {
            'index': len(self.chain),
            'timestamp': datetime.now().isoformat(),
            'event': 'model_published',
            'model_hash': model_hash,
            'metadata': metadata,
            'previous_hash': previous_block['hash']
        }
        
        block['hash'] = self._compute_hash(block)
        self.chain.append(block)
        
        return block
    
    def verify_chain_integrity(self):
        """Verify entire ledger is unmodified"""
        
        for i in range(1, len(self.chain)):
            current = self.chain[i]
            previous = self.chain[i - 1]
            
            # Verify hash link
            if current['previous_hash'] != previous['hash']:
                return False, f'Broken chain at index {i}'
            
            # Verify block hash
            block_copy = current.copy()
            stored_hash = block_copy.pop('hash')
            recomputed_hash = self._compute_hash(block_copy)
            
            if stored_hash != recomputed_hash:
                return False, f'Tampered block at index {i}'
        
        return True, 'Chain intact'
    
    def _compute_hash(self, block):
        """Compute cryptographic hash of block"""
        block_json = json.dumps(block, sort_keys=True)
        return hashlib.sha256(block_json.encode()).hexdigest()
    
    def get_publication_history(self, model_name):
        """Retrieve all publications for a model"""
        return [
            block for block in self.chain
            if block.get('metadata', {}).get('name') == model_name
        ]
```

### Tag Management with Immutable History

**Mutable tags that preserve history:**

```python
class ModelTag:
    """Mutable tag pointing to immutable models with history"""
    
    def __init__(self, tag_name):
        self.tag_name = tag_name
        self.history = []  # Append-only history
    
    def update(self, model_hash, reason):
        """Update tag to point to new model"""
        
        update_record = {
            'timestamp': datetime.now().isoformat(),
            'model_hash': model_hash,
            'previous_hash': self.current_hash() if self.history else None,
            'reason': reason,
            'updated_by': get_current_user()
        }
        
        self.history.append(update_record)
        
        log_security_event({
            'event': 'tag_updated',
            'tag': self.tag_name,
            'new_hash': model_hash,
            'reason': reason
        })
    
    def current_hash(self):
        """Get current model hash for tag"""
        return self.history[-1]['model_hash'] if self.history else None
    
    def get_history(self):
        """Get complete tag update history"""
        return self.history
```

## Limitations & Trade-offs

**Limitations:**
- **Storage growth**: Immutable storage accumulates all model versions, requiring significant storage capacity
- **Cannot delete**: Even models with vulnerabilities or sensitive data cannot be removed, only deprecated
- **Tag ambiguity**: Mutable tags (like `latest`) can still be manipulated even when underlying models are immutable

**Trade-offs:**
- **Auditability vs. Storage Cost**: Complete history enables forensics but consumes substantial storage
- **Immutability vs. Compliance**: Data deletion requirements (GDPR) conflict with immutability
- **Simplicity vs. Flexibility**: Strict immutability prevents quick fixes or rollback via deletion

**Bypass Scenarios:**
- **Storage-level tampering**: Attacker with filesystem access could modify underlying storage despite application-level controls
- **Metadata manipulation**: Registry metadata database could be tampered if not also immutable
- **Tag manipulation**: Mutable tags can redirect users to wrong model versions

## Testing & Validation

**Functional Testing:**
1. **Publication**: Verify models can be published to registry
2. **Immutability**: Confirm published models cannot be modified or deleted
3. **Retrieval**: Test retrieval by hash, version, and tag
4. **Lineage verification**: Verify lineage chains correctly link models to sources

**Security Testing:**
1. **Modification attempts**: Try to modify or delete published models
2. **Integrity verification**: Tamper with stored models and verify detection
3. **Ledger tampering**: Attempt to modify ledger and verify chain integrity checks fail
4. **Tag hijacking**: Test detection of malicious tag updates

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Immutable Model Registry: Store models in write-once repositories with versioning and cryptographic lineage tracking."
> — Extracted from Model Tampering mitigation section
