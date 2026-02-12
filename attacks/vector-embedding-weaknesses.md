---
tags:
  - needs-review
  - owasp/llm08:2025
  - source/ai-native-llm-security
  - trust-boundary/data-knowledge
  - type/attack
created: 2026-02-12
source: [[sources/bibliography#AI-Native LLM Security]]
pages: "351-355"
---
# Vector and Embedding Weaknesses (OWASP LLM08:2025)

**Definition:** Security weaknesses in RAG (Retrieval-Augmented Generation) systems at the vector pipeline layer — from document ingestion through embedding, storage, and retrieval. New addition to OWASP Top 10 for LLM Applications 2025.

**Context:** RAG systems chunk documents → embed with encoder (e.g., `text-embedding-ada-002`) → store vectors in database (Pinecone, Weaviate, PGVector) → retrieve top-k chunks at query time. Weaknesses arise at **four critical points**.

## Four Attack Surfaces

### 1. Poisoning at Source
Attacker uploads malicious document (PDF with misleading info, embedded instructions, fabricated data) to knowledge base. Processed through embedding pipeline, stored alongside legitimate content. User queries retrieve poisoned chunks as semantically relevant results → injected into LLM context window → model generates responses based on attacker content.

### 2. Access-Control Gap
Multi-tenant vector store lacks row-level security. User A's query fetches User B's embeddings → cross-tenant data leakage.

### 3. Embedding Inversion
Attacker with query API reconstructs original text or PII from 768-dimensional vector representations. Embeddings are "pseudonymous data" under GDPR — successful inversion = personal data breach.

### 4. Cross-Context Leakage
Shared collection mixes document types (HR docs + code docs). Salary information surfaces in developer query without explicit access violation at the application layer.

## Business Impact

- **Regulatory:** Embedding inversion = personal data breach under GDPR
- **IP Theft:** Inversion can recover proprietary source code snippets stored in vector form
- **Model Behavior Drift:** Poisoned chunks systematically steer responses — e.g., malicious medical docs causing LLM to recommend incorrect treatments; poisoned legal docs leading to advice violating regulations
- **Liability:** Application confidently generates harmful recommendations appearing authoritative but based on compromised source material

## Defense-in-Depth: Secure the Vector Surface

| Layer | Control | Implementation |
|-------|---------|---------------|
| **Ingest** | Provenance and signing | SHA-256 checksum + GPG signature per document; store in metadata; reject on mismatch |
| **Embed** | Tenant-isolated encoders | Per-tenant container embedding only that tenant's docs; destroy GPU memory after batch |
| **Store** | Encryption + RBAC | Server-side AES-256 with tenant-scoped KMS keys; row-level security (`WHERE tenant_id = current_user()`) in PGVector |
| **Retrieve** | Permission-aware ANN | Patch FAISS/Weaviate to accept `filter=` clause intersecting ANN with allowed-doc-id list from policy engine |
| **Query** | Rate limit + anomaly detection | Track cosine-similarity distribution per user; alert on sudden shift (possible inversion attack) |
| **Post-retrieval** | Chunk sanitizer | Local LLM guard re-scans top-k chunks for PII or toxic content before inserting into context |

### Integrity Verification Implementation

```python
import hashlib
from fastapi import HTTPException

class VectorGuardrail:
    def __init__(self):
        self.doc_signatures = {}  # {doc_id: sha256_hash}

    async def verify_chunk_integrity(self, doc_id: str, chunk_text: str):
        """Verify retrieved chunk hasn't been poisoned"""
        chunk_hash = hashlib.sha256(chunk_text.encode()).hexdigest()
        if doc_id in self.doc_signatures:
            if chunk_hash not in self.doc_signatures[doc_id]:
                raise HTTPException(400, "Chunk integrity violation - possible poisoning")
        else:
            self.doc_signatures[doc_id] = {chunk_hash}

    async def enforce_tenant_isolation(self, user_tenant: str, retrieved_chunks: list):
        """Block cross-tenant leakage"""
        for chunk in retrieved_chunks:
            if chunk.metadata.get("tenant_id") != user_tenant:
                raise HTTPException(403, "Cross-tenant access blocked")
        return retrieved_chunks
```

## Operational Playbook

### Daily Automated Checks
- **Integrity:** Recompute embeddings for 1% random sample, compare hashes to stored baseline; alert if drift >0.01
- **Access:** Query `pg_stat_statements` for any cross-tenant access patterns (`WHERE tenant_id <> current_tenant`)
- **Poison detection:** Insert canary document with unique phrase; search for it; if not in top 3 results, collection may be poisoned

### Incident Response (Inversion Suspected)
1. Revoke API key used by attacker
2. Rotate encryption key → force re-encryption of all vectors (renders stolen vectors useless)
3. Replay last 24h queries through anomaly-detection model; surface suspected inversion attempts
4. Notify tenants if their embeddings were accessed; provide inversion-risk assessment

### Open-Source Tools
| Tool | Purpose | Link |
|------|---------|------|
| vectordb-bench | Benchmark + leak test | github.com/zilliztech/VectorDBBench |
| NeMo Guardrails | Secure LLM apps | github.com/NVIDIA/NeMo-Guardrails |
| embedding-pipeline-template | IaC for secure RAG pipeline | github.com/aws-samples/text-embeddings-pipeline-for-rag |

## Related Notes

- [[attacks/data-poisoning-attacks|Data Poisoning Attacks]]
- [[attacks/embedding-poisoning|Embedding Poisoning]]
- [[attacks/system-prompt-leakage|System Prompt Leakage]]
- [[methodology/owasp-top10-llm-2025-evolution|OWASP Top 10 LLM 2025 Evolution]]
- [[defenses/input-validation-transformation|Input Validation and Transformation]]

> Source: [[sources/bibliography#AI-Native LLM Security]], pp. 351-355
