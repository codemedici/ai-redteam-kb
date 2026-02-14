---
title: "Agent Memory Integrity Verification"
tags:
  - type/mitigation
  - target/agent
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Agent Memory Integrity Verification

## Summary

Agent memory integrity verification uses cryptographic hashing and append-only storage to ensure that agent memory—including conversation history, scratchpads, and persistent knowledge—cannot be tampered with or poisoned by attackers. By hashing every memory chunk at ingestion, storing hashes in an immutable ledger, and re-verifying hashes before memory retrieval, this mitigation prevents attackers from injecting malicious instructions or fabricated facts into agent memory that would influence future reasoning.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agentic-ai-security-risks-owasp-aivss]] | Prevents agent memory and context manipulation through cryptographic verification |
| | [[techniques/prompt-injection]] | Prevents injected instructions from persisting in agent memory across sessions |

## Implementation

### Cryptographic Memory Hashing

**Hash all memory chunks at ingestion:**

```python
import hashlib
import json
from dataclasses import dataclass
from datetime import datetime
from typing import List, Optional

@dataclass
class MemoryChunk:
    """Immutable memory chunk with integrity verification"""
    content: str
    metadata: dict
    timestamp: datetime
    chunk_hash: str
    
    @classmethod
    def create(cls, content: str, metadata: dict = None):
        """
        Create new memory chunk with hash
        
        Args:
            content: Memory content (text)
            metadata: Optional metadata (source, type, etc.)
        
        Returns:
            MemoryChunk with computed hash
        """
        timestamp = datetime.now()
        metadata = metadata or {}
        
        # Create canonical representation for hashing
        chunk_data = {
            "content": content,
            "metadata": metadata,
            "timestamp": timestamp.isoformat()
        }
        
        # Compute SHA-256 hash
        chunk_json = json.dumps(chunk_data, sort_keys=True)
        chunk_hash = hashlib.sha256(chunk_json.encode('utf-8')).hexdigest()
        
        return cls(
            content=content,
            metadata=metadata,
            timestamp=timestamp,
            chunk_hash=chunk_hash
        )
    
    def verify_integrity(self) -> bool:
        """
        Verify chunk integrity by recomputing hash
        
        Returns: True if hash matches, False if tampered
        """
        chunk_data = {
            "content": self.content,
            "metadata": self.metadata,
            "timestamp": self.timestamp.isoformat()
        }
        
        chunk_json = json.dumps(chunk_data, sort_keys=True)
        computed_hash = hashlib.sha256(chunk_json.encode('utf-8')).hexdigest()
        
        return computed_hash == self.chunk_hash

class AppendOnlyMemoryLedger:
    """
    Append-only ledger for agent memory
    
    Stores memory chunks with cryptographic verification
    """
    
    def __init__(self):
        self.chunks: List[MemoryChunk] = []
        self.hash_index = {}  # Maps hash -> chunk index
    
    def append(self, content: str, metadata: dict = None) -> MemoryChunk:
        """
        Append new memory chunk to ledger
        
        Args:
            content: Memory content
            metadata: Optional metadata
        
        Returns:
            Created memory chunk with hash
        """
        # Create chunk with hash
        chunk = MemoryChunk.create(content, metadata)
        
        # Append to ledger (immutable)
        chunk_index = len(self.chunks)
        self.chunks.append(chunk)
        self.hash_index[chunk.chunk_hash] = chunk_index
        
        log_memory_event("chunk_appended", {
            "index": chunk_index,
            "hash": chunk.chunk_hash,
            "content_length": len(content)
        })
        
        return chunk
    
    def retrieve(self, index: int) -> Optional[MemoryChunk]:
        """
        Retrieve memory chunk by index with integrity verification
        
        Args:
            index: Chunk index in ledger
        
        Returns:
            Memory chunk if valid, None if index out of range
        
        Raises:
            MemoryIntegrityError if chunk hash verification fails
        """
        if index < 0 or index >= len(self.chunks):
            return None
        
        chunk = self.chunks[index]
        
        # ✅ VERIFY INTEGRITY before returning
        if not chunk.verify_integrity():
            log_security_event("memory_integrity_violation", {
                "index": index,
                "stored_hash": chunk.chunk_hash
            })
            raise MemoryIntegrityError(
                f"Memory chunk {index} failed integrity check - possible tampering"
            )
        
        return chunk
    
    def retrieve_by_hash(self, chunk_hash: str) -> Optional[MemoryChunk]:
        """
        Retrieve memory chunk by hash
        
        Ensures retrieved chunk matches requested hash
        """
        index = self.hash_index.get(chunk_hash)
        if index is None:
            return None
        
        return self.retrieve(index)
    
    def search(self, query: str, max_results: int = 10) -> List[MemoryChunk]:
        """
        Search memory with integrity verification
        
        Returns only chunks that pass integrity check
        """
        results = []
        
        for i, chunk in enumerate(self.chunks):
            # Verify integrity before including in results
            try:
                verified_chunk = self.retrieve(i)
                
                # Simple keyword search (in production: use vector search)
                if query.lower() in verified_chunk.content.lower():
                    results.append(verified_chunk)
                    
                    if len(results) >= max_results:
                        break
            except MemoryIntegrityError:
                # Skip corrupted chunks
                continue
        
        return results

class MemoryIntegrityError(Exception):
    """Raised when memory integrity verification fails"""
    pass
```

### Memory Access Controls

**Prevent direct user manipulation of persistent memory:**

```python
from enum import Enum

class MemoryWritePermission(Enum):
    """Memory write permission levels"""
    SYSTEM_ONLY = "system_only"  # Only system processes can write
    AGENT_ONLY = "agent_only"    # Only agent can write (no user influence)
    USER_AGENT = "user_agent"    # User can influence through agent

class ControlledMemoryStore:
    """Memory store with access controls"""
    
    def __init__(self, ledger: AppendOnlyMemoryLedger):
        self.ledger = ledger
        self.write_permission = MemoryWritePermission.AGENT_ONLY
    
    def store_memory(self, content: str, source: str, metadata: dict = None):
        """
        Store memory with source validation
        
        Args:
            content: Memory content
            source: Source of memory (system|agent|user)
            metadata: Optional metadata
        
        Raises:
            MemoryAccessDeniedError if source not permitted to write
        """
        metadata = metadata or {}
        metadata["source"] = source
        
        # ✅ VALIDATE SOURCE PERMISSION
        if source == "user" and self.write_permission != MemoryWritePermission.USER_AGENT:
            log_security_event("memory_write_blocked", {
                "source": source,
                "permission": self.write_permission.value,
                "content_preview": content[:100]
            })
            raise MemoryAccessDeniedError(
                "Direct user writes to memory are not permitted"
            )
        
        # Store in ledger
        chunk = self.ledger.append(content, metadata)
        
        log_memory_event("memory_stored", {
            "source": source,
            "hash": chunk.chunk_hash
        })
        
        return chunk
    
    def retrieve_for_context(self, agent_id: str, max_chunks: int = 10) -> List[str]:
        """
        Retrieve verified memory for context insertion
        
        All chunks are integrity-verified before inclusion
        """
        verified_chunks = []
        
        for i in range(max(0, len(self.ledger.chunks) - max_chunks), len(self.ledger.chunks)):
            try:
                chunk = self.ledger.retrieve(i)
                if chunk and chunk.metadata.get("agent_id") == agent_id:
                    verified_chunks.append(chunk.content)
            except MemoryIntegrityError:
                # Log but don't include corrupted chunks
                log_security_event("corrupted_chunk_skipped", {"index": i})
                continue
        
        return verified_chunks

class MemoryAccessDeniedError(Exception):
    """Raised when memory access is denied"""
    pass
```

### Memory Segmentation by Trust Level

**Isolate memory by source and trust level:**

```python
from typing import Dict, Set

class TrustLevel(Enum):
    """Memory trust levels"""
    SYSTEM = "system"        # System-generated, fully trusted
    AGENT = "agent"          # Agent-generated, moderately trusted
    USER = "user"            # User-provided, lowest trust
    EXTERNAL = "external"    # External sources, untrusted

class SegmentedMemoryStore:
    """Memory store with trust-based segmentation"""
    
    def __init__(self):
        # Separate ledgers per trust level
        self.ledgers: Dict[TrustLevel, AppendOnlyMemoryLedger] = {
            level: AppendOnlyMemoryLedger() for level in TrustLevel
        }
    
    def store(self, content: str, trust_level: TrustLevel, metadata: dict = None):
        """Store memory in appropriate trust-segmented ledger"""
        metadata = metadata or {}
        metadata["trust_level"] = trust_level.value
        
        ledger = self.ledgers[trust_level]
        return ledger.append(content, metadata)
    
    def retrieve_by_trust(self, trust_levels: Set[TrustLevel], max_chunks: int = 10) -> List[MemoryChunk]:
        """
        Retrieve memories from specified trust levels
        
        Allows agent to selectively retrieve only trusted memory
        """
        all_chunks = []
        
        for trust_level in trust_levels:
            ledger = self.ledgers[trust_level]
            
            # Get recent chunks from this trust level
            start = max(0, len(ledger.chunks) - max_chunks)
            for i in range(start, len(ledger.chunks)):
                try:
                    chunk = ledger.retrieve(i)
                    if chunk:
                        all_chunks.append(chunk)
                except MemoryIntegrityError:
                    continue
        
        # Sort by timestamp
        all_chunks.sort(key=lambda c: c.timestamp, reverse=True)
        
        return all_chunks[:max_chunks]
```

### Memory Lifecycle Management

**Limit retention duration for memory:**

```python
from datetime import timedelta

class TimeBoundMemoryStore:
    """Memory store with automatic expiration"""
    
    def __init__(self, ledger: AppendOnlyMemoryLedger, retention_days: int = 30):
        self.ledger = ledger
        self.retention_duration = timedelta(days=retention_days)
    
    def retrieve_active_memories(self, max_chunks: int = 100) -> List[MemoryChunk]:
        """
        Retrieve only non-expired memories
        
        Automatically filters out memories older than retention period
        """
        now = datetime.now()
        cutoff = now - self.retention_duration
        
        active_chunks = []
        
        for i in range(len(self.ledger.chunks)):
            try:
                chunk = self.ledger.retrieve(i)
                
                # Filter by age
                if chunk.timestamp >= cutoff:
                    active_chunks.append(chunk)
                    
                    if len(active_chunks) >= max_chunks:
                        break
            except MemoryIntegrityError:
                continue
        
        return active_chunks
    
    def archive_expired(self):
        """
        Archive expired memories (implementation-specific)
        
        Move old memories to cold storage, keeping only hash references
        """
        now = datetime.now()
        cutoff = now - self.retention_duration
        
        expired_count = 0
        for chunk in self.ledger.chunks:
            if chunk.timestamp < cutoff:
                # Archive chunk (implementation varies)
                expired_count += 1
        
        log_memory_event("memories_archived", {"count": expired_count})
```

## Limitations & Trade-offs

**Limitations:**

- **Cannot prevent authorized memory writes:** If agent is compromised, it can write malicious content that will be correctly hashed
- **Storage overhead:** Hashing and immutable storage increases memory footprint
- **Does not prevent logic errors:** Correctly hashed memory may still contain flawed or misleading information
- **Limited to stored memory:** Cannot verify integrity of ephemeral context (prompt inputs)

**Trade-offs:**

- **Security vs. Performance:** Hashing and verification add computational overhead
- **Immutability vs. Flexibility:** Append-only ledgers prevent tampering but also prevent corrections
- **Retention vs. Privacy:** Longer retention improves agent memory but increases privacy risks
- **Trust segmentation vs. Complexity:** Separate ledgers by trust level improve security but complicate memory management

**Bypass Scenarios:**

- **Hash collision attacks:** Theoretical risk of finding content with same hash (extremely unlikely with SHA-256)
- **Timing attacks:** Side-channel attacks during hash computation may leak information
- **Compromised hash storage:** If hash index is compromised, attacker could replace hashes
- **Memory pollution:** Attacker floods memory with valid but misleading content

## Testing & Validation

**Functional Testing:**

1. **Integrity Verification:** Verify that tampered chunks fail integrity checks
2. **Append-Only Enforcement:** Confirm that ledger prevents modification of existing chunks
3. **Trust Segmentation:** Test that memories are correctly isolated by trust level
4. **Expiration:** Validate that expired memories are correctly filtered/archived

**Security Testing:**

1. **Tampering Detection:** Modify stored chunks and verify detection
2. **Hash Collision:** Test hash collision scenarios (theoretical)
3. **Access Control Bypass:** Attempt unauthorized memory writes
4. **Memory Pollution:** Test resilience to large volumes of low-quality memory

**Monitoring & Metrics:**

- **Integrity Violations:** Alert on any detected memory tampering
- **Memory Growth:** Track memory usage across trust levels
- **Retrieval Patterns:** Monitor which trust levels are accessed most
- **Expired Memories:** Track archival and expiration rates

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Hash every chunk at ingestion, store in append-only ledger. Re-verify hash before chunk inserted into context."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 359, 364-365

> "Validate all memory writes with schema checks. Isolate scratchpads between tasks and agents."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 359, 364-365

> "Prevent direct user influence over persistent memory. Limit retention duration. Segment memory by trust level."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 359, 364-365

## Related

- **Combines with:** [[mitigations/input-validation-patterns]], [[mitigations/agent-memory-isolation]]
- **Complements:** [[mitigations/anomaly-detection-architecture]], [[mitigations/telemetry-and-monitoring-architecture]]
