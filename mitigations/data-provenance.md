---
title: "Data Provenance"
tags:
  - type/mitigation
  - target/rag
  - target/ml-model
  - target/training-pipeline
  - source/adversarial-ai
atlas: AML.M0029
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Data Provenance

## Summary

Data provenance tracking maintains cryptographic lineage of all data sources with immutable audit trails, enabling verification of data origin, integrity, and chain of custody throughout the ML pipeline. This mitigation creates a tamper-evident record of where data came from, who modified it, and when changes occurred—essential for detecting data poisoning, attributing attacks to sources, and supporting forensic analysis. Provenance tracking integrates with source validation, trust scoring, and incident response to provide end-to-end data security.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Enables attribution of poisoned data to specific sources for remediation |
| | [[techniques/data-poisoning-attacks]] | Tracks data lineage to identify where poisoning was introduced |
| | [[techniques/supply-chain-attacks]] | Detects compromised third-party data sources through chain-of-custody breaks |
| | [[techniques/incremental-poisoning]] | Temporal provenance reveals gradual poisoning campaigns from specific sources |

## Implementation

### Provenance Record Structure

**Core fields for comprehensive provenance:**

```python
from dataclasses import dataclass
from datetime import datetime
from typing import List, Optional

@dataclass
class ProvenanceRecord:
    """Immutable provenance record for data sample"""
    
    # Identity
    sample_id: str
    content_hash: str  # SHA-256 of sample content
    
    # Origin
    source_id: str
    source_name: str
    source_type: str  # 'internal' | 'third-party' | 'public' | 'user-generated'
    
    # Chain of custody
    created_timestamp: datetime
    created_by: str  # User or system that created/ingested
    
    # Transformations
    transformations: List[dict]  # [{timestamp, operation, performed_by}]
    
    # Integrity
    signature: str  # Cryptographic signature of record
    signature_algorithm: str
    signing_key_id: str
    
    # Metadata
    collection_method: str  # 'manual' | 'api' | 'scraping' | 'upload'
    validation_status: str  # 'passed' | 'failed' | 'pending'
    trust_score: float
    
    # Lineage
    parent_sample_id: Optional[str]  # If derived from another sample
    related_samples: List[str]
```

### Provenance Tracking Implementation

```python
import hashlib
import json
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.asymmetric import padding

class ProvenanceTracker:
    """Track data provenance with cryptographic verification"""
    
    def __init__(self, signing_key):
        self.signing_key = signing_key
        self.provenance_db = ProvenanceDatabase()
    
    def create_provenance_record(self, sample, source, created_by):
        """Generate provenance record for new data sample"""
        
        # Calculate content hash
        content_hash = hashlib.sha256(sample.text.encode()).hexdigest()
        
        # Build provenance record
        record = ProvenanceRecord(
            sample_id=sample.id,
            content_hash=content_hash,
            source_id=source.id,
            source_name=source.name,
            source_type=source.type,
            created_timestamp=datetime.now(),
            created_by=created_by,
            transformations=[],
            signature='',  # Populated below
            signature_algorithm='RSA-PSS-SHA256',
            signing_key_id=self.signing_key.key_id,
            collection_method=sample.collection_method,
            validation_status='pending',
            trust_score=source.trust_score,
            parent_sample_id=None,
            related_samples=[]
        )
        
        # Sign the record
        record.signature = self.sign_record(record)
        
        # Store in provenance database
        self.provenance_db.insert(record)
        
        return record
    
    def sign_record(self, record):
        """Cryptographically sign provenance record"""
        # Serialize record (excluding signature field)
        record_data = {
            'sample_id': record.sample_id,
            'content_hash': record.content_hash,
            'source_id': record.source_id,
            'created_timestamp': record.created_timestamp.isoformat(),
            'created_by': record.created_by,
            'transformations': record.transformations
        }
        
        record_json = json.dumps(record_data, sort_keys=True)
        
        # Sign
        signature = self.signing_key.sign(
            record_json.encode(),
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )
        
        return signature.hex()
    
    def verify_provenance(self, sample_id):
        """Verify provenance record integrity"""
        record = self.provenance_db.get(sample_id)
        
        # Verify signature
        if not self.verify_signature(record):
            return False, "Signature verification failed"
        
        # Verify content hash matches current sample
        current_sample = get_sample(sample_id)
        current_hash = hashlib.sha256(current_sample.text.encode()).hexdigest()
        
        if current_hash != record.content_hash:
            return False, "Content hash mismatch - sample modified"
        
        # Verify source still exists and hasn't been revoked
        source = source_registry.get(record.source_id)
        if source.status == 'revoked':
            return False, f"Source {record.source_id} has been revoked"
        
        return True, "Provenance verified"
    
    def log_transformation(self, sample_id, operation, performed_by):
        """Record data transformation in provenance trail"""
        record = self.provenance_db.get(sample_id)
        
        transformation = {
            'timestamp': datetime.now().isoformat(),
            'operation': operation,
            'performed_by': performed_by
        }
        
        record.transformations.append(transformation)
        
        # Re-sign record with updated transformation history
        record.signature = self.sign_record(record)
        
        self.provenance_db.update(record)
```

### Chain of Custody Tracking

```python
def trace_chain_of_custody(sample_id):
    """Trace complete lineage of a data sample"""
    
    provenance_record = provenance_db.get(sample_id)
    chain = []
    
    # Build chain from creation to present
    chain.append({
        'event': 'creation',
        'timestamp': provenance_record.created_timestamp,
        'source': provenance_record.source_name,
        'actor': provenance_record.created_by,
        'trust_score': provenance_record.trust_score
    })
    
    # Add all transformations
    for transformation in provenance_record.transformations:
        chain.append({
            'event': 'transformation',
            'timestamp': transformation['timestamp'],
            'operation': transformation['operation'],
            'actor': transformation['performed_by']
        })
    
    # Check for breaks in chain
    chain_breaks = detect_chain_breaks(chain)
    
    return {
        'sample_id': sample_id,
        'chain': chain,
        'chain_breaks': chain_breaks,
        'verified': len(chain_breaks) == 0
    }

def detect_chain_breaks(chain):
    """Identify suspicious gaps or anomalies in chain of custody"""
    breaks = []
    
    for i in range(1, len(chain)):
        prev_event = chain[i-1]
        curr_event = chain[i]
        
        # Check for time gaps (>24 hours between events is suspicious)
        prev_time = datetime.fromisoformat(prev_event['timestamp'])
        curr_time = datetime.fromisoformat(curr_event['timestamp'])
        time_gap = (curr_time - prev_time).total_seconds() / 3600  # hours
        
        if time_gap > 24:
            breaks.append({
                'type': 'time_gap',
                'duration_hours': time_gap,
                'between_events': [prev_event['event'], curr_event['event']]
            })
        
        # Check for unauthorized actors
        if is_unauthorized_actor(curr_event.get('actor')):
            breaks.append({
                'type': 'unauthorized_actor',
                'actor': curr_event['actor'],
                'event': curr_event['event']
            })
    
    return breaks
```

### Provenance-Based Forensics

```python
def provenance_forensic_analysis(poisoning_incident):
    """Use provenance to trace poisoning attack"""
    
    # Identify poisoned samples
    poisoned_sample_ids = poisoning_incident.affected_samples
    
    # Trace provenance for all poisoned samples
    source_attribution = {}
    
    for sample_id in poisoned_sample_ids:
        record = provenance_db.get(sample_id)
        source_id = record.source_id
        
        if source_id not in source_attribution:
            source_attribution[source_id] = {
                'source_name': record.source_name,
                'poisoned_count': 0,
                'earliest_timestamp': record.created_timestamp,
                'sample_ids': []
            }
        
        source_attribution[source_id]['poisoned_count'] += 1
        source_attribution[source_id]['sample_ids'].append(sample_id)
        
        if record.created_timestamp < source_attribution[source_id]['earliest_timestamp']:
            source_attribution[source_id]['earliest_timestamp'] = record.created_timestamp
    
    # Rank sources by poison contribution
    ranked_sources = sorted(
        source_attribution.items(),
        key=lambda x: x[1]['poisoned_count'],
        reverse=True
    )
    
    # Generate forensic report
    report = {
        'incident_id': poisoning_incident.id,
        'total_poisoned_samples': len(poisoned_sample_ids),
        'attributed_sources': ranked_sources,
        'primary_source': ranked_sources[0] if ranked_sources else None,
        'attack_timeline': build_attack_timeline(poisoned_sample_ids),
        'recommendation': generate_remediation_recommendation(ranked_sources)
    }
    
    return report

def generate_remediation_recommendation(ranked_sources):
    """Generate remediation steps based on provenance analysis"""
    if not ranked_sources:
        return "No sources identified"
    
    primary_source_id, primary_data = ranked_sources[0]
    
    recommendations = []
    
    # If single source accounts for >80% of poison
    if primary_data['poisoned_count'] / sum(s[1]['poisoned_count'] for s in ranked_sources) > 0.8:
        recommendations.append(f"IMMEDIATE: Revoke source {primary_source_id}")
        recommendations.append(f"QUARANTINE: All {primary_data['poisoned_count']} samples from this source")
    
    # If multiple sources involved
    elif len(ranked_sources) > 3:
        recommendations.append("INVESTIGATE: Coordinated attack across multiple sources")
        recommendations.append("ENHANCE: Source validation and trust scoring")
    
    recommendations.append("CLEANSE: Execute data cleansing and retraining procedure")
    
    return recommendations
```

## Limitations & Trade-offs

**Limitations:**
- **Storage Overhead**: Comprehensive provenance records increase storage requirements
- **Performance Impact**: Signature generation and verification add latency
- **Retroactive Gaps**: Cannot track provenance for data ingested before system deployment
- **Key Management**: Cryptographic signing requires secure key storage and rotation

**Trade-offs:**
- **Provenance Depth vs. Storage**: Detailed transformation logs improve forensics but consume space
- **Signature Strength vs. Speed**: Stronger cryptography (RSA-4096) is more secure but slower
- **Real-Time Verification vs. Throughput**: Verifying every access is secure but impacts performance

## Testing & Validation

**Functional Testing:**
1. **Record Creation**: Verify provenance records created for all ingested data
2. **Signature Validation**: Confirm signatures verify correctly
3. **Chain Tracing**: Test complete chain-of-custody reconstruction
4. **Tamper Detection**: Modify sample content, verify provenance mismatch detection

**Security Testing:**
1. **Signature Forgery**: Attempt to forge provenance records
2. **Chain Manipulation**: Test if chain-of-custody can be altered
3. **Forensic Accuracy**: Verify provenance correctly attributes poisoning to sources

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Data Provenance Tracking: Maintain cryptographic lineage of all data sources with immutable audit trails; verify integrity using checksums or digital signatures."
> — Extracted from RAG Data Poisoning mitigation section

## Related

- **Combines with**: [[mitigations/content-signing-and-provenance]], [[mitigations/source-validation-and-trust-scoring]], [[mitigations/source-revocation]]
- **Enables**: Attack attribution, forensic analysis, compliance auditing
