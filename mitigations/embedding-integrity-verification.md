---
title: "Embedding Integrity Verification"
tags:
  - type/mitigation
  - target/rag
  - target/embedding
  - source/adversarial-ai
atlas: AML.M0021
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Embedding Integrity Verification

## Summary

Embedding integrity verification uses cryptographic hashing and signature validation to detect tampering with vector embeddings stored in vector databases. This mitigation ensures that embeddings accurately represent their source documents and have not been manipulated to bias retrieval results or inject malicious content. By signing embeddings at generation time and verifying signatures before retrieval, systems can detect both direct vector manipulation and dataset poisoning attacks where document text is swapped before re-embedding.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Detects embedding vector manipulation and dataset swapping attacks |
| | [[techniques/embedding-poisoning]] | Prevents attackers from directly modifying stored embeddings to bias retrieval |
| AML.T0051.002 | [[techniques/retrieval-manipulation]] | Ensures retrieved content matches original source documents through integrity verification; detects direct embedding manipulation and semantic drift attacks |
| | [[techniques/unauthorized-knowledge-disclosure]] | Ensures embedding space respects access boundaries by validating that retrieved embeddings correspond to documents the user is authorized to access |

## Implementation

### Core Pattern: Sign at Generation, Verify at Retrieval

**Embedding Generation with Signing:**

```python
import hashlib
import hmac
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.asymmetric import padding, rsa

class EmbeddingIntegrityManager:
    def __init__(self, private_key, public_key):
        self.private_key = private_key
        self.public_key = public_key
    
    def generate_signed_embedding(self, document_text, embedding_model):
        """Generate embedding with cryptographic signature"""
        # Generate embedding vector
        embedding = embedding_model.encode(document_text)
        
        # Create integrity record
        integrity_data = {
            'document_hash': hashlib.sha256(document_text.encode()).hexdigest(),
            'embedding_vector': embedding.tolist(),
            'timestamp': datetime.now().isoformat(),
            'model_version': embedding_model.version
        }
        
        # Sign the integrity record
        signature = self.private_key.sign(
            json.dumps(integrity_data, sort_keys=True).encode(),
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )
        
        return {
            'embedding': embedding,
            'integrity_data': integrity_data,
            'signature': signature.hex(),
            'document_text': document_text  # Store for re-verification
        }
```

**Embedding Retrieval with Verification:**

```python
def retrieve_with_integrity_check(query, vector_db, integrity_manager):
    """Retrieve embeddings and verify integrity before use"""
    results = vector_db.search(query, top_k=10)
    
    verified_results = []
    for result in results:
        # Verify signature
        if not integrity_manager.verify_signature(result):
            log_integrity_violation(result, "signature_invalid")
            quarantine_embedding(result)
            continue
        
        # Verify document hash matches stored text
        current_hash = hashlib.sha256(result.document_text.encode()).hexdigest()
        if current_hash != result.integrity_data['document_hash']:
            log_integrity_violation(result, "document_modified")
            quarantine_embedding(result)
            continue
        
        # Verify embedding matches document (re-embed and compare)
        if random.random() < 0.05:  # Spot-check 5% of retrievals
            current_embedding = embedding_model.encode(result.document_text)
            similarity = cosine_similarity(current_embedding, result.embedding)
            if similarity < 0.99:  # Should be nearly identical
                log_integrity_violation(result, "embedding_document_mismatch")
                quarantine_embedding(result)
                continue
        
        verified_results.append(result)
    
    return verified_results
```

### Implementation Approaches

#### 1. Cryptographic Signing (High Security)

**When to use**: Production RAG systems handling sensitive data, regulatory compliance requirements

**Mechanism**:
- Generate RSA or ECDSA key pair for embedding signing
- Sign integrity record (document hash + embedding vector + metadata) at generation
- Verify signature before retrieval; reject tampered embeddings
- Store signatures alongside embeddings in vector DB metadata

**Benefits**:
- Cryptographically strong tamper detection
- Non-repudiation (proof of who generated embedding)
- Difficult to forge without private key access

**Drawbacks**:
- Higher computational overhead
- Key management complexity
- Signature storage overhead

#### 2. Hash-Based Integrity Checks (Medium Security)

**When to use**: Internal systems, lower-risk applications, performance-constrained environments

**Mechanism**:
- Compute SHA-256 hash of document text at embedding time
- Store hash in embedding metadata
- On retrieval, recompute hash and compare to stored value
- Periodically re-embed documents and compare vectors

**Implementation**:

```python
def hash_based_integrity_check(document_text, stored_hash, stored_embedding, embedding_model):
    """Verify embedding integrity using hash comparison"""
    # Check 1: Document hasn't changed
    current_hash = hashlib.sha256(document_text.encode()).hexdigest()
    if current_hash != stored_hash:
        return False, "document_modified"
    
    # Check 2: Embedding matches document (periodic re-embedding)
    current_embedding = embedding_model.encode(document_text)
    similarity = cosine_similarity(current_embedding, stored_embedding)
    
    if similarity < 0.99:
        return False, "embedding_mismatch"
    
    return True, "verified"
```

**Benefits**:
- Lower computational overhead
- Simple implementation
- No key management

**Drawbacks**:
- Cannot detect who modified embedding (no non-repudiation)
- Vulnerable if attacker can modify both hash and embedding
- Requires periodic re-embedding for full verification

#### 3. Periodic Re-Embedding and Comparison

**Scheduled re-embedding workflow:**

```python
def periodic_embedding_audit(vector_db, embedding_model, audit_percentage=10):
    """Periodically re-embed documents and detect drift"""
    all_embeddings = vector_db.get_all()
    sample_size = int(len(all_embeddings) * audit_percentage / 100)
    audit_sample = random.sample(all_embeddings, sample_size)
    
    violations = []
    for record in audit_sample:
        # Re-generate embedding from source document
        fresh_embedding = embedding_model.encode(record.document_text)
        
        # Compare to stored embedding
        similarity = cosine_similarity(fresh_embedding, record.embedding)
        
        if similarity < 0.99:
            violations.append({
                'document_id': record.id,
                'stored_embedding': record.embedding,
                'fresh_embedding': fresh_embedding,
                'similarity': similarity,
                'source_id': record.source_id
            })
            quarantine_embedding(record)
    
    return violations
```

**Audit Schedule Recommendations:**
- High-risk sources: Daily audit of 10% sample
- Moderate-risk sources: Weekly audit of 5% sample
- Trusted sources: Monthly audit of 2% sample
- All sources: Full re-embedding audit quarterly

### Semantic Consistency Checks

Detect embedding manipulation by analyzing semantic space coherence:

```python
def detect_embedding_drift(vector_db, cluster_threshold=0.05):
    """Detect embeddings that have drifted from expected clusters"""
    embeddings = vector_db.get_all()
    
    # Cluster embeddings by source or topic
    clusters = cluster_embeddings(embeddings)
    
    anomalies = []
    for cluster in clusters:
        # Calculate cluster centroid
        centroid = np.mean([e.vector for e in cluster.members], axis=0)
        
        # Find outliers
        for embedding in cluster.members:
            distance = euclidean_distance(embedding.vector, centroid)
            if distance > cluster_threshold:
                anomalies.append({
                    'embedding_id': embedding.id,
                    'distance_from_centroid': distance,
                    'cluster_id': cluster.id
                })
    
    return anomalies
```

### Integration with Vector Databases

**Chroma Integration:**

```python
# Store integrity metadata with embedding
collection.add(
    embeddings=[embedding_vector],
    documents=[document_text],
    metadatas=[{
        'document_hash': doc_hash,
        'signature': signature,
        'signed_timestamp': timestamp,
        'model_version': model_version
    }],
    ids=[document_id]
)

# Retrieve and verify
results = collection.query(query_embeddings=[query_vector], n_results=10)
for i, result in enumerate(results['documents'][0]):
    metadata = results['metadatas'][0][i]
    if not verify_integrity(result, metadata):
        # Handle integrity violation
        quarantine_result(result)
```

**Pinecone Integration:**

```python
# Pinecone stores metadata alongside vectors
index.upsert(vectors=[
    {
        'id': doc_id,
        'values': embedding_vector,
        'metadata': {
            'text': document_text,
            'hash': doc_hash,
            'signature': signature,
            'timestamp': timestamp
        }
    }
])

# Query with integrity verification
results = index.query(vector=query_vector, top_k=10, include_metadata=True)
verified = [r for r in results['matches'] if verify_integrity(r)]
```

## Limitations & Trade-offs

**Limitations:**
- **Cannot prevent initial poisoning**: Only detects tampering after embedding generation; does not prevent poisoned documents from being embedded initially
- **Re-embedding overhead**: Periodic verification requires computational resources to re-generate embeddings
- **Key compromise**: If signing keys compromised, attacker can forge valid signatures
- **Model version dependency**: Embeddings change across model versions; signature validation must account for model updates

**Trade-offs:**
- **Security vs. Performance**: Cryptographic verification adds latency to retrieval pipeline
- **Storage overhead**: Signatures and integrity metadata increase storage requirements
- **Verification depth vs. throughput**: Full re-embedding verification is comprehensive but slow

**Bypass Scenarios:**
- **Poisoning before signing**: Attacker injects poisoned documents that are then legitimately signed
- **Key theft**: Attacker obtains private signing key and generates valid signatures for malicious embeddings
- **Semantic drift attacks**: Subtle embedding shifts that remain within verification thresholds but bias retrieval

## Testing & Validation

**Functional Testing:**
1. **Signature Verification**: Verify signatures validate correctly for legitimate embeddings
2. **Tamper Detection**: Modify stored embeddings, confirm detection on retrieval
3. **Document Modification Detection**: Change source document, verify hash mismatch detection
4. **Re-Embedding Accuracy**: Confirm periodic re-embedding detects poisoned vectors

**Security Testing:**
1. **Direct Vector Manipulation**: Attempt to modify embedding vectors directly in database
2. **Dataset Swap Attack**: Replace document text before re-embedding, test detection
3. **Signature Forgery**: Attempt to forge signatures without private key
4. **Gradual Drift Attack**: Incrementally shift embeddings to test threshold sensitivity

**Performance Benchmarks:**
- Measure signature verification latency (target: <10ms per embedding)
- Assess storage overhead (signatures typically 256-512 bytes per embedding)
- Benchmark re-embedding throughput for periodic audits

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Embedding Integrity: Cryptographically sign embeddings at generation time; Verify signatures before retrieval."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Periodic Re-Embedding: Regenerate embeddings from source documents and compare against stored versions."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Semantic Consistency Checks: Embeddings don't match document text (indicates manipulation)."
> — Extracted from RAG Data Poisoning detection signals (Adversarial AI)

## Related

- **Combines with**: [[mitigations/content-signing-and-provenance]], [[mitigations/anomaly-detection-architecture]], [[mitigations/data-quarantine-procedures]]
- **Enables**: Tamper-evident vector databases, forensic analysis of embedding manipulation
