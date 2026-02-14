---
title: "Tenant-Isolated Embedding Infrastructure"
tags:
  - type/mitigation
  - target/rag
  - target/embedding
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Tenant-Isolated Embedding Infrastructure

## Summary

Tenant-isolated embedding infrastructure segregates the embedding generation process by tenant to prevent cross-contamination of vector representations and GPU memory leakage in multi-tenant RAG systems. This mitigation ensures that sensitive document content from one tenant cannot leak into another tenant's embeddings through shared model state, GPU memory residue, or batch processing side channels. By using dedicated embedding model instances or containers per tenant with strict memory cleanup, systems eliminate a critical attack surface at the vector pipeline ingestion layer.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/vector-embedding-weaknesses]] | Prevents cross-tenant contamination during embedding generation and GPU memory leakage |
| | [[techniques/weak-access-segmentation]] | Enforces tenant isolation at the embedding layer before vectors enter shared storage |
| | [[techniques/unauthorized-knowledge-disclosure]] | Prevents inference of tenant A's documents through GPU memory or model state when processing tenant B's documents |

## Implementation

### Threat Model: GPU Memory Leakage

**Attack Scenario:**

In a shared embedding infrastructure, the same GPU and model weights process documents from multiple tenants sequentially. When switching from Tenant A to Tenant B:

1. **GPU Memory Residue:** Previous tenant's document embeddings may persist in GPU memory
2. **Model Activation State:** Hidden layer activations from Tenant A's documents could influence Tenant B's embeddings
3. **Batch Contamination:** If documents from multiple tenants processed in same batch, cross-talk possible through attention mechanisms

**Result:** Tenant B's embeddings contain trace information from Tenant A's documents, enabling reconstruction attacks.

**Real-World Analogy:** Like using the same photocopier for classified and unclassified documents without clearing the internal buffer—sensitive document fragments could appear on subsequent copies.

### Approach 1: Per-Tenant Container Isolation

**Mechanism:**

Each tenant gets a dedicated containerized embedding service:

```
Tenant A Documents → Container A (Embedding Model Instance A) → Vector DB Collection A
Tenant B Documents → Container B (Embedding Model Instance B) → Vector DB Collection B
```

**Implementation (Docker + Kubernetes):**

```yaml
# deployment-tenant-a.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: embedding-service-tenant-a
  labels:
    tenant: tenant-a
spec:
  replicas: 1
  selector:
    matchLabels:
      app: embedding-service
      tenant: tenant-a
  template:
    metadata:
      labels:
        app: embedding-service
        tenant: tenant-a
    spec:
      containers:
      - name: embedding-model
        image: huggingface/transformers:latest
        resources:
          limits:
            nvidia.com/gpu: 1  # Dedicated GPU
        env:
        - name: TENANT_ID
          value: "tenant-a"
        - name: MODEL_NAME
          value: "sentence-transformers/all-MiniLM-L6-v2"
        volumeMounts:
        - name: model-cache
          mountPath: /models
      volumes:
      - name: model-cache
        emptyDir: {}
```

**Application Code:**

```python
class TenantIsolatedEmbeddingService:
    """Route embedding requests to tenant-specific services"""
    
    def __init__(self):
        # Map tenant IDs to dedicated embedding service endpoints
        self.tenant_endpoints = {
            'tenant-a': 'http://embedding-service-tenant-a:8000',
            'tenant-b': 'http://embedding-service-tenant-b:8000',
        }
    
    def embed_document(self, tenant_id, document_text):
        """
        Generate embedding using tenant's isolated service
        
        Args:
            tenant_id: Tenant identifier
            document_text: Document to embed
        
        Returns:
            Embedding vector
        """
        # Get tenant-specific embedding service
        endpoint = self.tenant_endpoints.get(tenant_id)
        if not endpoint:
            raise ValueError(f"No embedding service configured for tenant {tenant_id}")
        
        # Call tenant's dedicated embedding service
        response = requests.post(
            f"{endpoint}/embed",
            json={'text': document_text}
        )
        
        return response.json()['embedding']
```

**Advantages:**
- **Complete isolation:** No shared GPU memory or model state between tenants
- **Resource guarantees:** Dedicated GPU/CPU resources per tenant
- **Independent scaling:** Scale each tenant's embedding capacity separately

**Disadvantages:**
- **High cost:** Requires GPU per tenant (expensive at scale)
- **Resource inefficiency:** Idle capacity when tenant not actively embedding
- **Operational complexity:** Manage many container deployments

### Approach 2: GPU Memory Cleanup Between Tenants

**Mechanism:**

Use shared embedding infrastructure but enforce strict memory cleanup when switching tenants:

1. Process Tenant A's documents
2. Clear GPU memory (CUDA cache clear)
3. Optionally reload model weights from clean checkpoint
4. Process Tenant B's documents

**Implementation:**

```python
import torch
from transformers import AutoModel, AutoTokenizer

class SecureSharedEmbeddingService:
    """Shared embedding service with tenant boundary cleanup"""
    
    def __init__(self, model_name):
        self.model_name = model_name
        self.model = None
        self.tokenizer = None
        self.current_tenant = None
    
    def embed_documents(self, tenant_id, documents):
        """
        Embed documents with tenant isolation enforcement
        
        Args:
            tenant_id: Tenant identifier
            documents: List of documents to embed
        
        Returns:
            List of embedding vectors
        """
        # Switch tenant context (clears GPU memory)
        self._switch_tenant_context(tenant_id)
        
        # Process documents for this tenant
        embeddings = []
        for doc in documents:
            embedding = self._embed_single_document(doc)
            embeddings.append(embedding)
        
        # Force garbage collection to clear intermediate tensors
        torch.cuda.empty_cache()
        
        return embeddings
    
    def _switch_tenant_context(self, tenant_id):
        """
        Switch to new tenant context with GPU memory cleanup
        """
        if self.current_tenant != tenant_id:
            print(f"Switching tenant context: {self.current_tenant} → {tenant_id}")
            
            # Step 1: Clear CUDA cache
            if torch.cuda.is_available():
                torch.cuda.empty_cache()
                torch.cuda.synchronize()  # Ensure all GPU operations complete
            
            # Step 2: Delete model and tokenizer (releases GPU memory)
            del self.model
            del self.tokenizer
            
            # Step 3: Force garbage collection
            import gc
            gc.collect()
            
            # Step 4: Reload model fresh from checkpoint
            self.model = AutoModel.from_pretrained(self.model_name).cuda()
            self.tokenizer = AutoTokenizer.from_pretrained(self.model_name)
            
            # Update current tenant
            self.current_tenant = tenant_id
    
    def _embed_single_document(self, document_text):
        """Generate embedding for single document"""
        inputs = self.tokenizer(
            document_text,
            return_tensors='pt',
            truncation=True,
            max_length=512
        ).to('cuda')
        
        with torch.no_grad():
            outputs = self.model(**inputs)
            embedding = outputs.last_hidden_state.mean(dim=1).squeeze()
        
        return embedding.cpu().numpy()
```

**Advantages:**
- **Lower cost:** Share GPU across tenants
- **Better resource utilization:** No idle GPU capacity
- **Simpler operations:** Single deployment to manage

**Disadvantages:**
- **Performance overhead:** Model reload adds latency (seconds per tenant switch)
- **Not cryptographically guaranteed:** GPU memory cleanup may not be 100% effective
- **Batch processing limitations:** Cannot batch documents from different tenants

### Approach 3: Serverless Embedding Functions (Per-Request Isolation)

**Mechanism:**

Use serverless functions (AWS Lambda, Google Cloud Functions) where each request gets a fresh execution environment:

```python
# AWS Lambda handler
import json
from sentence_transformers import SentenceTransformer

# Global model loading (cold start optimization)
model = None

def lambda_handler(event, context):
    """
    Embed document in isolated serverless environment
    
    Each invocation gets fresh container with no prior state
    """
    global model
    
    # Load model on cold start
    if model is None:
        model = SentenceTransformer('all-MiniLM-L6-v2')
    
    # Extract request data
    tenant_id = event['tenant_id']
    document_text = event['document_text']
    
    # Generate embedding
    embedding = model.encode(document_text).tolist()
    
    # Return result
    return {
        'statusCode': 200,
        'body': json.dumps({
            'tenant_id': tenant_id,
            'embedding': embedding
        })
    }
```

**Advantages:**
- **Automatic isolation:** Each request in separate container (no cross-contamination)
- **Auto-scaling:** Scales to zero when idle, infinite scale under load
- **No manual cleanup:** Cloud provider manages environment isolation

**Disadvantages:**
- **Cold start latency:** First request to each function instance loads model (~seconds)
- **Cost:** Can be expensive at high volume
- **GPU limitations:** Serverless functions have limited GPU support (AWS Lambda with GPU preview only)

### Hybrid Approach: Tenant-Specific Batching

**Mechanism:**

Batch documents from the same tenant together, enforce GPU cleanup between batches:

```python
class TenantBatchedEmbeddingService:
    """Process tenant documents in isolated batches"""
    
    def __init__(self, model_name, batch_size=32):
        self.model = AutoModel.from_pretrained(model_name).cuda()
        self.tokenizer = AutoTokenizer.from_pretrained(model_name)
        self.batch_size = batch_size
    
    def embed_tenant_documents(self, tenant_id, documents):
        """
        Embed all documents for a tenant in isolated batches
        
        Ensures no cross-tenant contamination in batches
        """
        all_embeddings = []
        
        # Process in batches
        for i in range(0, len(documents), self.batch_size):
            batch = documents[i:i + self.batch_size]
            
            # Embed batch
            batch_embeddings = self._embed_batch(batch)
            all_embeddings.extend(batch_embeddings)
        
        # Cleanup after tenant processing complete
        self._cleanup_gpu_memory()
        
        return all_embeddings
    
    def _embed_batch(self, documents):
        """Embed a batch of documents (all from same tenant)"""
        inputs = self.tokenizer(
            documents,
            return_tensors='pt',
            padding=True,
            truncation=True,
            max_length=512
        ).to('cuda')
        
        with torch.no_grad():
            outputs = self.model(**inputs)
            embeddings = outputs.last_hidden_state.mean(dim=1)
        
        return embeddings.cpu().numpy()
    
    def _cleanup_gpu_memory(self):
        """Force GPU memory cleanup after tenant batch"""
        torch.cuda.empty_cache()
        torch.cuda.synchronize()
```

### Audit and Validation

**Verify tenant isolation:**

```python
def validate_tenant_isolation():
    """
    Test that embeddings from different tenants don't contaminate each other
    """
    embedding_service = TenantIsolatedEmbeddingService()
    
    # Embed sensitive document for Tenant A
    tenant_a_doc = "Tenant A confidential trade secret: Project Nightingale"
    tenant_a_embedding = embedding_service.embed_document('tenant-a', tenant_a_doc)
    
    # Embed unrelated document for Tenant B
    tenant_b_doc = "Tenant B public announcement: New product launch"
    tenant_b_embedding = embedding_service.embed_document('tenant-b', tenant_b_doc)
    
    # Embed another document for Tenant A
    tenant_a_doc2 = "Tenant A annual report summary"
    tenant_a_embedding2 = embedding_service.embed_document('tenant-a', tenant_a_doc2)
    
    # Verify Tenant B embedding doesn't show high similarity to Tenant A content
    # (would indicate contamination)
    from scipy.spatial.distance import cosine
    
    similarity_a_b = 1 - cosine(tenant_a_embedding, tenant_b_embedding)
    similarity_a_a2 = 1 - cosine(tenant_a_embedding, tenant_a_embedding2)
    
    print(f"Similarity Tenant A doc 1 ↔ Tenant B: {similarity_a_b:.4f}")
    print(f"Similarity Tenant A doc 1 ↔ Tenant A doc 2: {similarity_a_a2:.4f}")
    
    # Contamination check: Cross-tenant similarity should be low
    assert similarity_a_b < 0.5, "Possible cross-tenant contamination detected"
    print("✓ Tenant isolation validated")
```

## Limitations & Trade-offs

**Limitations:**
- **Cost scaling:** Per-tenant infrastructure expensive at large tenant count
- **GPU availability:** Limited GPU resources constrain number of isolated instances
- **Cold start delays:** Fresh container/model loading adds latency
- **Not guaranteed:** GPU memory cleanup may not be 100% effective against all side-channel attacks

**Trade-offs:**
- **Security vs. Cost:** Complete isolation (per-tenant GPUs) extremely expensive
- **Isolation vs. Performance:** GPU cleanup between tenants adds overhead
- **Resource efficiency vs. Isolation:** Shared infrastructure more efficient but less secure

**Bypass Scenarios:**
- **Side-channel attacks:** Sophisticated attacks (timing, power analysis) could still extract information
- **Model weight contamination:** If model weights themselves poisoned, all tenants affected
- **Infrastructure compromise:** Hypervisor-level attacks could break container isolation

## Testing & Validation

**Functional Testing:**
1. **Isolation Verification:** Embed documents for multiple tenants, verify no cross-contamination
2. **GPU Cleanup Effectiveness:** Measure GPU memory before/after tenant switch
3. **Container Independence:** Verify container crashes don't affect other tenants

**Security Testing:**
1. **Cross-Tenant Embedding Similarity:** Test that embeddings from different tenants show expected low similarity
2. **Memory Residue Detection:** Attempt to extract prior tenant's data from GPU memory
3. **Timing Side-Channels:** Test if embedding latency leaks information about other tenants' documents

**Performance Testing:**
1. **Tenant Switch Overhead:** Measure latency added by GPU cleanup or container switching
2. **Throughput Impact:** Benchmark embeddings/second with isolation vs. shared infrastructure
3. **Scaling Limits:** Test maximum number of isolated tenant instances supported

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Embed: Tenant-isolated encoders — Per-tenant container embedding only that tenant's docs; destroy GPU memory after batch."
> — [[sources/bibliography#AI-Native LLM Security]], p. 351-355

## Related

- **Combines with**: [[mitigations/rag-access-control]], [[mitigations/vector-database-encryption-and-rbac]], [[mitigations/data-minimization]]
- **Enables**: Secure multi-tenant embedding infrastructure, GPU memory protection, side-channel attack mitigation
