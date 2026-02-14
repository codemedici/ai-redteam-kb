---
title: "RAG Access Control"
tags:
  - type/mitigation
  - target/rag
  - target/embedding
  - source/ai-native-llm-security
atlas: AML.M0025
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# RAG Access Control

## Summary

RAG (Retrieval-Augmented Generation) access control enforces permission boundaries at the vector retrieval layer to prevent unauthorized knowledge disclosure in multi-tenant systems. This mitigation ensures that users can only retrieve embeddings corresponding to documents they are authorized to access, preventing cross-tenant data leakage and privilege escalation attacks. By integrating access control filters directly into the vector similarity search process, RAG systems maintain least-privilege principles even when operating on shared vector databases.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Prevents unauthorized access to poisoned or legitimate documents outside user's permissions |
| | [[techniques/vector-embedding-weaknesses]] | Mitigates cross-tenant leakage and access-control gap attack surface in vector stores |
| | [[techniques/unauthorized-knowledge-disclosure]] | Primary defense preventing retrieval of embeddings outside user's authorization scope |
| | [[techniques/weak-access-segmentation]] | Enforces tenant isolation at vector database layer |

## Implementation

### Core Pattern: Permission-Aware Approximate Nearest Neighbor (ANN) Search

Traditional vector databases perform ANN (Approximate Nearest Neighbor) search purely based on cosine similarity or Euclidean distance. **Permission-aware ANN** adds an access control filter that intersects similarity search results with the user's allowed document set.

**Architecture:**

```
User Query → Embedding → Vector Search (ANN) → Permission Filter → Allowed Results
                              ↓                        ↓
                     Top-K Similar Chunks      Intersect with ACL
```

### Approach 1: Post-Retrieval Filtering (Simple but Inefficient)

**Mechanism:**
1. Perform standard ANN search to retrieve top-k embeddings
2. Check each result against user's access control list (ACL)
3. Filter out unauthorized chunks
4. Return only authorized results

**Implementation:**

```python
def retrieve_with_acl(query, user_id, vector_db, top_k=10):
    """
    Retrieve embeddings with post-retrieval permission filtering
    """
    # Get user's allowed document IDs from policy engine
    allowed_doc_ids = get_allowed_documents(user_id)
    
    # Perform vector search
    raw_results = vector_db.search(query, top_k=top_k * 3)  # Fetch more to compensate for filtering
    
    # Filter by permissions
    authorized_results = []
    for result in raw_results:
        if result.metadata['document_id'] in allowed_doc_ids:
            authorized_results.append(result)
        
        if len(authorized_results) >= top_k:
            break
    
    return authorized_results
```

**Limitations:**
- **Inefficient:** Fetches many results only to discard them
- **Incomplete results:** May return fewer than top-k if many results are unauthorized
- **Information leakage:** Vector database still performs search over unauthorized embeddings
- **Performance:** Over-fetching increases latency and computational cost

### Approach 2: Pre-Filtering with Metadata (Efficient)

**Mechanism:**
1. Store access control metadata with each embedding (tenant_id, owner_id, permission_tags)
2. Pass permission filter to vector database before ANN search
3. Database performs ANN search only on authorized subset
4. Return exactly top-k authorized results

**Implementation:**

```python
class PermissionAwareVectorStore:
    """Vector store with integrated access control"""
    
    def __init__(self, vector_db):
        self.vector_db = vector_db
    
    def insert_with_permissions(self, embedding, document_text, metadata):
        """
        Store embedding with access control metadata
        
        Args:
            embedding: Vector representation
            document_text: Source document text
            metadata: Must include 'tenant_id', 'owner_id', 'permission_tags'
        """
        # Validate required ACL fields
        assert 'tenant_id' in metadata, "tenant_id required for access control"
        assert 'owner_id' in metadata, "owner_id required for access control"
        
        # Store with ACL metadata
        self.vector_db.insert(
            embedding=embedding,
            metadata={
                **metadata,
                'document_text': document_text
            }
        )
    
    def search_with_permissions(self, query_embedding, user_context, top_k=10):
        """
        Search with permission-aware filtering
        
        Args:
            query_embedding: Query vector
            user_context: User's tenant_id, user_id, roles
            top_k: Number of results to return
        """
        # Build permission filter from user context
        permission_filter = self._build_permission_filter(user_context)
        
        # Perform filtered ANN search
        results = self.vector_db.search(
            query_embedding=query_embedding,
            top_k=top_k,
            filter=permission_filter  # Database applies filter before similarity search
        )
        
        return results
    
    def _build_permission_filter(self, user_context):
        """
        Build vector database filter from user permissions
        
        Returns filter expression that database can evaluate
        """
        # Single-tenant filter: Only retrieve from user's tenant
        tenant_filter = {'tenant_id': user_context['tenant_id']}
        
        # Optional: Add role-based permissions
        # e.g., admins can see all docs, users only see their own
        if 'admin' in user_context['roles']:
            # Admin sees everything in their tenant
            return tenant_filter
        else:
            # Regular users see only their documents
            return {
                **tenant_filter,
                'owner_id': user_context['user_id']
            }
```

**Database-Specific Implementations:**

#### PGVector (PostgreSQL):

```sql
-- Search with permission filter
SELECT 
    id, 
    document_text, 
    metadata,
    1 - (embedding <=> query_embedding) AS similarity
FROM embeddings
WHERE 
    tenant_id = $1  -- User's tenant
    AND (
        owner_id = $2  -- User's documents
        OR 'admin' = ANY(user_roles)  -- Or admin role
    )
ORDER BY embedding <=> query_embedding  -- Cosine distance
LIMIT 10;
```

Row-level security (RLS) can enforce tenant isolation:

```sql
-- Create row-level security policy
CREATE POLICY tenant_isolation ON embeddings
    USING (tenant_id = current_setting('app.current_tenant_id'));

-- Enable RLS
ALTER TABLE embeddings ENABLE ROW LEVEL SECURITY;
```

#### Pinecone:

```python
import pinecone

# Initialize with API key
pinecone.init(api_key="your_api_key")
index = pinecone.Index("your-index")

# Query with metadata filter
results = index.query(
    vector=query_embedding,
    top_k=10,
    filter={
        "tenant_id": {"$eq": user_tenant_id},  # Tenant isolation
        "owner_id": {"$eq": user_id}  # User-specific access
    }
)
```

#### Weaviate:

```python
import weaviate

client = weaviate.Client("http://localhost:8080")

# Query with where filter
result = client.query.get(
    "Document",
    ["document_text", "metadata"]
).with_near_vector({
    "vector": query_embedding
}).with_where({
    "operator": "And",
    "operands": [
        {
            "path": ["tenant_id"],
            "operator": "Equal",
            "valueString": user_tenant_id
        },
        {
            "path": ["owner_id"],
            "operator": "Equal",
            "valueString": user_id
        }
    ]
}).with_limit(10).do()
```

#### ChromaDB:

```python
import chromadb

client = chromadb.Client()
collection = client.get_collection("documents")

# Query with metadata filter
results = collection.query(
    query_embeddings=[query_embedding],
    n_results=10,
    where={
        "$and": [
            {"tenant_id": {"$eq": user_tenant_id}},
            {"owner_id": {"$eq": user_id}}
        ]
    }
)
```

### Approach 3: Multi-Tenant Collection Isolation (Strongest)

**Mechanism:**
1. Create separate vector database collections/namespaces per tenant
2. User queries only search their tenant's collection
3. No cross-tenant access possible at database level

**Implementation:**

```python
class TenantIsolatedVectorStore:
    """Vector store with physical tenant isolation"""
    
    def __init__(self, vector_db):
        self.vector_db = vector_db
        self.collections = {}  # {tenant_id: collection}
    
    def get_tenant_collection(self, tenant_id):
        """Get or create tenant-specific collection"""
        if tenant_id not in self.collections:
            self.collections[tenant_id] = self.vector_db.create_collection(
                name=f"tenant_{tenant_id}"
            )
        return self.collections[tenant_id]
    
    def insert(self, tenant_id, embedding, metadata):
        """Insert embedding into tenant's isolated collection"""
        collection = self.get_tenant_collection(tenant_id)
        collection.insert(embedding=embedding, metadata=metadata)
    
    def search(self, tenant_id, query_embedding, top_k=10):
        """Search only within tenant's collection"""
        collection = self.get_tenant_collection(tenant_id)
        return collection.search(query_embedding=query_embedding, top_k=top_k)
```

**Advantages:**
- **Complete tenant isolation:** No risk of cross-tenant leakage
- **Simplified access control:** No need for complex permission filters
- **Clear security boundary:** Physical separation enforced at database level

**Disadvantages:**
- **Operational overhead:** Manage many collections/namespaces
- **Resource inefficiency:** Duplicate infrastructure per tenant
- **Limited for fine-grained permissions:** Still need user-level ACLs within tenant

### Integration with Policy Engines

For complex permission models (role-based, attribute-based), integrate with external policy engine:

```python
class PolicyEngineIntegration:
    """Integrate RAG access control with policy engine (e.g., OPA, Cedar)"""
    
    def __init__(self, policy_engine_client):
        self.policy_client = policy_engine_client
    
    def can_access_document(self, user_id, document_id):
        """
        Check if user can access document via policy engine
        
        Policy engine evaluates:
        - User roles
        - Document classification level
        - Time-based access (e.g., working hours only)
        - Organizational hierarchy
        """
        decision = self.policy_client.evaluate(
            principal=user_id,
            action="read",
            resource=document_id
        )
        return decision.allowed
    
    def get_allowed_documents(self, user_id):
        """
        Retrieve list of document IDs user is allowed to access
        
        Returns: Set of document IDs
        """
        # Query policy engine for user's accessible documents
        allowed_docs = self.policy_client.query(
            f"data.documents[user_id == '{user_id}'].allowed_documents"
        )
        return set(allowed_docs)
    
    def build_metadata_filter(self, user_id):
        """
        Generate vector database filter from policy engine rules
        
        Returns: Metadata filter compatible with vector database
        """
        user_permissions = self.policy_client.get_user_permissions(user_id)
        
        filter_conditions = []
        
        # Example: User can access documents with classification <= their clearance
        if 'clearance_level' in user_permissions:
            filter_conditions.append({
                'classification': {'$lte': user_permissions['clearance_level']}
            })
        
        # Example: User can access documents from their department
        if 'department' in user_permissions:
            filter_conditions.append({
                'department': {'$eq': user_permissions['department']}
            })
        
        return {'$and': filter_conditions} if filter_conditions else {}
```

### Audit Logging for Access Control Violations

Log all access attempts for forensic analysis:

```python
def log_access_attempt(user_id, document_id, allowed, reason):
    """Log RAG access attempt for audit trail"""
    audit_log = {
        'timestamp': datetime.now().isoformat(),
        'user_id': user_id,
        'document_id': document_id,
        'access_allowed': allowed,
        'denial_reason': reason if not allowed else None,
        'event_type': 'rag_access_attempt'
    }
    
    # Send to SIEM
    send_to_siem(audit_log)
    
    # Alert on repeated denials (potential attack)
    if not allowed:
        check_for_unauthorized_access_pattern(user_id)

def check_for_unauthorized_access_pattern(user_id):
    """Detect users systematically probing for unauthorized documents"""
    recent_denials = get_recent_access_denials(user_id, last_minutes=10)
    
    if len(recent_denials) > 5:  # >5 denials in 10 minutes
        alert_security_team("Potential unauthorized access probing", {
            'user_id': user_id,
            'denial_count': len(recent_denials),
            'targeted_documents': [d['document_id'] for d in recent_denials]
        })
```

## Limitations & Trade-offs

**Limitations:**
- **Metadata storage overhead:** Storing ACL metadata with every embedding increases storage requirements
- **Query performance impact:** Pre-filtering reduces ANN search efficiency (smaller candidate set)
- **Permission update latency:** Changes to user permissions require cache invalidation or re-indexing
- **Complex permission models:** Fine-grained ABAC policies difficult to express as vector database filters

**Trade-offs:**
- **Security vs. Performance:** Permission filtering adds latency to retrieval
- **Isolation vs. Efficiency:** Separate collections per tenant increase operational overhead
- **Granularity vs. Complexity:** Fine-grained permissions require external policy engine integration
- **Auditability vs. Throughput:** Comprehensive access logging adds processing overhead

**Bypass Scenarios:**
- **Metadata manipulation:** Attacker modifies ACL metadata directly in vector database (mitigated by database access controls)
- **Policy engine compromise:** If policy engine returns incorrect permissions, access control fails
- **Timing attacks:** Attacker infers document existence from query latency differences
- **Embedding inference:** Attacker reconstructs unauthorized documents from embeddings (requires [[mitigations/embedding-integrity-verification]])

## Testing & Validation

**Functional Testing:**
1. **Tenant Isolation:** Verify users from Tenant A cannot retrieve documents from Tenant B
2. **Role-Based Access:** Confirm admins see all documents, regular users see only their own
3. **Permission Revocation:** Validate that access is immediately denied after permission removal
4. **Empty Results Handling:** Test behavior when user has no authorized documents matching query

**Security Testing:**
1. **Cross-Tenant Probing:** Attempt to bypass tenant filter by manipulating query parameters
2. **Privilege Escalation:** Test if low-privilege user can access high-privilege documents
3. **Metadata Injection:** Attempt to forge ACL metadata in query to gain unauthorized access
4. **Direct Database Access:** Verify database-level access controls prevent bypassing application layer

**Performance Testing:**
1. **Query Latency:** Measure impact of permission filtering on retrieval time
2. **Scalability:** Test performance with large number of tenants/users
3. **Filter Complexity:** Benchmark complex ACL filters vs. simple tenant isolation

**Audit Testing:**
1. **Log Completeness:** Verify all access attempts (allowed and denied) are logged
2. **Alerting:** Confirm unauthorized access patterns trigger security alerts
3. **Forensic Analysis:** Test ability to reconstruct access history from logs

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Access-Control Gap: Multi-tenant vector store lacks row-level security. User A's query fetches User B's embeddings → cross-tenant data leakage."
> — [[sources/bibliography#AI-Native LLM Security]], p. 351-355

> "Cross-Context Leakage: Shared collection mixes document types (HR docs + code docs). Salary information surfaces in developer query without explicit access violation at the application layer."
> — [[sources/bibliography#AI-Native LLM Security]], p. 351-355

> "Retrieve: Permission-aware ANN — Patch FAISS/Weaviate to accept `filter=` clause intersecting ANN with allowed-doc-id list from policy engine."
> — [[sources/bibliography#AI-Native LLM Security]], p. 351-355

## Related

- **Combines with**: [[mitigations/tenant-isolated-embedding-infrastructure]], [[mitigations/vector-database-encryption-and-rbac]], [[mitigations/anomaly-detection-architecture]]
- **Enables**: Multi-tenant RAG systems, least-privilege document access, compliance with data protection regulations
