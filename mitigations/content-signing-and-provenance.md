---
title: "Content Signing and Provenance"
tags:
  - type/mitigation
  - target/rag
  - target/ml-model
  - target/training-pipeline
  - source/adversarial-ai
atlas: AML.M0023
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Content Signing and Provenance

## Summary

Content signing and provenance tracking establishes cryptographic chain-of-custody for data, models, and generated outputs throughout their lifecycle. This mitigation uses digital signatures and immutable audit trails to verify authenticity, detect tampering, and attribute responsibility for content creation and modifications. By maintaining comprehensive provenance metadata—who created, when, from what source, through which transformations—systems can trace malicious content back to its origin and validate integrity at every stage.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Detects tampering with training data and RAG documents through signature verification |
| | [[techniques/data-poisoning-attacks]] | Validates training data authenticity and tracks poisoned samples to source |
| | [[techniques/model-tampering]] | Ensures model artifacts have not been modified post-training through cryptographic signing |
| | [[techniques/third-party-model-risks]] | Verifies authenticity of external models and datasets before integration |
| | [[techniques/vector-embedding-weaknesses]] | SHA-256 checksums and GPG signatures validate document authenticity before embedding, preventing poisoning at source |
| AML.T0018 | [[techniques/trojan-injection]] | Cryptographic model signing detects post-training Trojan insertion; integrity verification on load prevents deployment of tampered models |
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Digital signatures and checksums verify model integrity against known-good values; provenance whitelisting restricts models to trusted sources |
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Artifact signing prevents deployment of unsigned/tampered models from compromised registries; provenance tracking identifies malicious artifact sources in MLOps pipelines |

## Implementation

### Core Components

#### 1. Data Signing at Source

**Document signing for RAG ingestion:**

```python
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import rsa, padding
import json
import datetime

class ContentSigner:
    """Cryptographic signing for data provenance"""
    
    def __init__(self, private_key_path, public_key_path):
        # Load signing keys
        with open(private_key_path, 'rb') as f:
            self.private_key = serialization.load_pem_private_key(
                f.read(), password=None
            )
        with open(public_key_path, 'rb') as f:
            self.public_key = serialization.load_pem_public_key(f.read())
    
    def sign_document(self, document_text, source_id, metadata=None):
        """Generate cryptographic signature for document"""
        # Create provenance record
        provenance = {
            'document_hash': hashlib.sha256(document_text.encode()).hexdigest(),
            'source_id': source_id,
            'timestamp': datetime.datetime.now().isoformat(),
            'metadata': metadata or {},
            'signer_id': self.get_signer_id()
        }
        
        # Serialize provenance record deterministically
        provenance_json = json.dumps(provenance, sort_keys=True)
        
        # Sign the provenance record
        signature = self.private_key.sign(
            provenance_json.encode(),
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )
        
        return {
            'document': document_text,
            'provenance': provenance,
            'signature': signature.hex()
        }
    
    def verify_signature(self, signed_document):
        """Verify document signature and integrity"""
        try:
            # Reconstruct provenance record
            provenance_json = json.dumps(
                signed_document['provenance'], 
                sort_keys=True
            )
            
            # Verify signature
            self.public_key.verify(
                bytes.fromhex(signed_document['signature']),
                provenance_json.encode(),
                padding.PSS(
                    mgf=padding.MGF1(hashes.SHA256()),
                    salt_length=padding.PSS.MAX_LENGTH
                ),
                hashes.SHA256()
            )
            
            # Verify document hash matches
            current_hash = hashlib.sha256(
                signed_document['document'].encode()
            ).hexdigest()
            
            if current_hash != signed_document['provenance']['document_hash']:
                return False, 'document_modified'
            
            return True, 'verified'
        
        except Exception as e:
            return False, f'verification_failed: {str(e)}'
```

#### 2. Immutable Provenance Ledger

**Blockchain-style audit trail for data lineage:**

```python
import hashlib
import json
from datetime import datetime

class ProvenanceLedger:
    """Immutable ledger for tracking data provenance"""
    
    def __init__(self):
        self.chain = []
        # Genesis block
        self.add_event({
            'event_type': 'ledger_initialized',
            'timestamp': datetime.now().isoformat()
        })
    
    def add_event(self, event_data):
        """Add provenance event to immutable chain"""
        # Get hash of previous block
        previous_hash = self.chain[-1]['hash'] if self.chain else '0' * 64
        
        # Create new block
        block = {
            'index': len(self.chain),
            'timestamp': datetime.now().isoformat(),
            'event': event_data,
            'previous_hash': previous_hash
        }
        
        # Calculate block hash
        block_json = json.dumps(block, sort_keys=True)
        block['hash'] = hashlib.sha256(block_json.encode()).hexdigest()
        
        self.chain.append(block)
        return block['hash']
    
    def verify_chain_integrity(self):
        """Verify entire provenance chain is unmodified"""
        for i in range(1, len(self.chain)):
            current_block = self.chain[i]
            previous_block = self.chain[i-1]
            
            # Verify previous hash link
            if current_block['previous_hash'] != previous_block['hash']:
                return False, f'broken_chain_at_index_{i}'
            
            # Verify current block hash
            block_copy = current_block.copy()
            stored_hash = block_copy.pop('hash')
            calculated_hash = hashlib.sha256(
                json.dumps(block_copy, sort_keys=True).encode()
            ).hexdigest()
            
            if stored_hash != calculated_hash:
                return False, f'tampered_block_at_index_{i}'
        
        return True, 'chain_intact'
    
    def get_provenance_history(self, document_id):
        """Retrieve full provenance history for a document"""
        history = []
        for block in self.chain:
            if block['event'].get('document_id') == document_id:
                history.append(block)
        return history
```

**Provenance event logging:**

```python
def log_data_ingestion(ledger, document, source_id, signature):
    """Log data ingestion event in provenance ledger"""
    ledger.add_event({
        'event_type': 'data_ingested',
        'document_id': generate_document_id(document),
        'document_hash': hashlib.sha256(document.encode()).hexdigest(),
        'source_id': source_id,
        'signature': signature,
        'ingestion_timestamp': datetime.now().isoformat()
    })

def log_data_transformation(ledger, original_id, transformed_doc, transformation_type):
    """Log data transformation event"""
    ledger.add_event({
        'event_type': 'data_transformed',
        'original_document_id': original_id,
        'transformed_document_id': generate_document_id(transformed_doc),
        'transformation_type': transformation_type,  # e.g., 'embedding_generation', 'sanitization'
        'timestamp': datetime.now().isoformat()
    })

def log_rag_retrieval(ledger, query, retrieved_docs):
    """Log RAG retrieval event for audit trail"""
    ledger.add_event({
        'event_type': 'rag_retrieval',
        'query_hash': hashlib.sha256(query.encode()).hexdigest(),
        'retrieved_document_ids': [doc.id for doc in retrieved_docs],
        'timestamp': datetime.now().isoformat()
    })
```

#### 3. Model Artifact Signing

**Sign trained models and checkpoints:**

```python
def sign_model_artifact(model_path, training_metadata, signer):
    """Sign model artifact with training provenance"""
    # Calculate model file hash
    with open(model_path, 'rb') as f:
        model_hash = hashlib.sha256(f.read()).hexdigest()
    
    # Create model provenance
    provenance = {
        'model_hash': model_hash,
        'model_path': model_path,
        'training_metadata': {
            'training_dataset_hash': training_metadata['dataset_hash'],
            'training_start': training_metadata['start_time'],
            'training_end': training_metadata['end_time'],
            'hyperparameters': training_metadata['hyperparameters'],
            'framework_version': training_metadata['framework_version']
        },
        'timestamp': datetime.now().isoformat()
    }
    
    # Sign provenance record
    signature = signer.sign_document(
        json.dumps(provenance, sort_keys=True),
        source_id='training_pipeline',
        metadata=training_metadata
    )
    
    # Store signature alongside model
    signature_path = model_path + '.sig'
    with open(signature_path, 'w') as f:
        json.dump(signature, f)
    
    return signature

def verify_model_integrity(model_path, signer):
    """Verify model has not been tampered with"""
    # Load signature
    signature_path = model_path + '.sig'
    with open(signature_path, 'r') as f:
        signature = json.load(f)
    
    # Verify signature
    verified, status = signer.verify_signature(signature)
    if not verified:
        return False, status
    
    # Verify model hash matches
    with open(model_path, 'rb') as f:
        current_hash = hashlib.sha256(f.read()).hexdigest()
    
    if current_hash != signature['provenance']['model_hash']:
        return False, 'model_modified'
    
    return True, 'verified'
```

#### 4. Third-Party Dataset Verification

**Verify external datasets before use:**

```python
def verify_external_dataset(dataset_url, expected_hash, trusted_publishers):
    """Verify third-party dataset authenticity"""
    # Download dataset
    dataset_content = download_dataset(dataset_url)
    
    # Verify hash
    actual_hash = hashlib.sha256(dataset_content).hexdigest()
    if actual_hash != expected_hash:
        raise DataIntegrityError(
            f'Dataset hash mismatch: expected {expected_hash}, got {actual_hash}'
        )
    
    # Verify publisher signature if available
    signature_url = dataset_url + '.sig'
    try:
        signature = download_signature(signature_url)
        publisher_key = get_publisher_public_key(signature['publisher_id'])
        
        if signature['publisher_id'] not in trusted_publishers:
            raise UntrustedPublisherError(
                f'Publisher {signature["publisher_id"]} not in trusted list'
            )
        
        verified = verify_signature_with_key(
            dataset_content, 
            signature, 
            publisher_key
        )
        
        if not verified:
            raise SignatureVerificationError('Dataset signature invalid')
    
    except FileNotFoundError:
        # No signature available
        log_warning(f'No signature found for dataset {dataset_url}')
    
    return dataset_content
```

### Integration with RAG Systems

**End-to-end signed RAG pipeline:**

```python
class SignedRAGPipeline:
    """RAG system with full provenance tracking"""
    
    def __init__(self, signer, ledger, vector_db):
        self.signer = signer
        self.ledger = ledger
        self.vector_db = vector_db
    
    def ingest_document(self, document_text, source_id):
        """Ingest document with signing and provenance logging"""
        # Sign document
        signed_doc = self.signer.sign_document(
            document_text, 
            source_id,
            metadata={'ingestion_time': datetime.now().isoformat()}
        )
        
        # Verify signature (sanity check)
        verified, status = self.signer.verify_signature(signed_doc)
        if not verified:
            raise SignatureError(f'Self-signature verification failed: {status}')
        
        # Log ingestion in provenance ledger
        doc_id = generate_document_id(document_text)
        self.ledger.add_event({
            'event_type': 'document_ingested',
            'document_id': doc_id,
            'source_id': source_id,
            'signature': signed_doc['signature']
        })
        
        # Generate embedding (also logged in ledger)
        embedding = self.generate_embedding(document_text)
        self.ledger.add_event({
            'event_type': 'embedding_generated',
            'document_id': doc_id,
            'embedding_hash': hashlib.sha256(embedding.tobytes()).hexdigest()
        })
        
        # Store in vector DB with provenance metadata
        self.vector_db.insert(
            embedding,
            metadata={
                'document_id': doc_id,
                'document_text': document_text,
                'provenance': signed_doc['provenance'],
                'signature': signed_doc['signature']
            }
        )
        
        return doc_id
    
    def retrieve_with_verification(self, query):
        """Retrieve documents with signature verification"""
        results = self.vector_db.search(query, top_k=10)
        
        verified_results = []
        for result in results:
            # Reconstruct signed document
            signed_doc = {
                'document': result.metadata['document_text'],
                'provenance': result.metadata['provenance'],
                'signature': result.metadata['signature']
            }
            
            # Verify signature
            verified, status = self.signer.verify_signature(signed_doc)
            if verified:
                verified_results.append(result)
            else:
                log_integrity_violation(result.metadata['document_id'], status)
                quarantine_document(result)
        
        # Log retrieval in provenance ledger
        self.ledger.add_event({
            'event_type': 'rag_retrieval',
            'query_hash': hashlib.sha256(query.encode()).hexdigest(),
            'retrieved_document_ids': [r.metadata['document_id'] for r in verified_results]
        })
        
        return verified_results
```

### Provenance Visualization

**Generate chain-of-custody diagrams:**

```python
def generate_provenance_graph(ledger, document_id):
    """Generate visual provenance graph for a document"""
    history = ledger.get_provenance_history(document_id)
    
    graph = {
        'nodes': [],
        'edges': []
    }
    
    for event in history:
        graph['nodes'].append({
            'id': event['index'],
            'type': event['event']['event_type'],
            'timestamp': event['timestamp'],
            'data': event['event']
        })
        
        # Link to previous event
        if event['index'] > 0:
            graph['edges'].append({
                'from': event['index'] - 1,
                'to': event['index'],
                'label': 'derives_from'
            })
    
    return graph
```

## Limitations & Trade-offs

**Limitations:**
- **Cannot prevent initial poisoning**: Provenance tracks what happened but doesn't prevent malicious content from being signed by compromised source
- **Key management complexity**: Requires secure storage, rotation, and distribution of cryptographic keys
- **Storage overhead**: Signatures and provenance metadata increase storage requirements significantly
- **Performance impact**: Cryptographic operations add latency to ingestion and retrieval

**Trade-offs:**
- **Security vs. Performance**: Comprehensive signing and verification slows data pipelines
- **Auditability vs. Privacy**: Detailed provenance may expose sensitive information about data sources
- **Centralization vs. Distribution**: Ledger management can become bottleneck or single point of failure

**Bypass Scenarios:**
- **Compromised signing keys**: Attacker with key access can sign malicious content as legitimate
- **Insider threat**: Authorized signer intentionally signs poisoned data
- **Key theft**: Private keys stolen from storage or memory

## Testing & Validation

**Functional Testing:**
1. **Signature Generation**: Verify signatures correctly generated for all data types
2. **Signature Verification**: Confirm legitimate signatures validate; tampered signatures fail
3. **Provenance Logging**: Test that all lifecycle events recorded in ledger
4. **Chain Integrity**: Verify provenance chain remains immutable

**Security Testing:**
1. **Signature Forgery**: Attempt to forge signatures without private key
2. **Data Tampering Detection**: Modify signed data, verify detection on verification
3. **Ledger Tampering**: Attempt to modify provenance ledger, verify chain integrity checks fail
4. **Key Theft Simulation**: Test detection and response to compromised signing keys

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/virustotal-poisoning]] | [[frameworks/atlas/tactics/resource-development]] | Provenance tracking would have identified malicious contributors to public dataset |

## Sources

> "Data Provenance Tracking: Maintain cryptographic lineage of all data sources with immutable audit trails; verify integrity using checksums or digital signatures."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Cryptographic Verification: Sign trusted datasets; verify signatures before ingestion into training or RAG systems."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

## Related

- **Combines with**: [[mitigations/source-validation-and-trust-scoring]], [[mitigations/embedding-integrity-verification]], [[mitigations/data-quarantine-procedures]]
- **Enables**: Forensic analysis, attribution of malicious content, tamper-evident data pipelines
