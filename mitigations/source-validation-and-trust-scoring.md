---
title: "Source Validation and Trust Scoring"
tags:
  - type/mitigation
  - target/rag
  - target/ml-model
  - target/training-pipeline
  - source/adversarial-ai
atlas: AML.M0020
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Source Validation and Trust Scoring

## Summary

Source validation and trust scoring implements a risk-based evaluation framework for data sources contributing to training pipelines, RAG systems, and knowledge bases. This mitigation assigns trust scores to data providers based on reputation, historical reliability, security posture, and validation results, then enforces differential treatment based on risk level. High-risk sources face stricter validation, quarantine, or rejection, while trusted sources receive expedited processing. This prevents data poisoning by reducing attack surface and limiting the impact of compromised or malicious data providers.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Prevents poisoned documents and embeddings from untrusted sources entering RAG systems |
| | [[techniques/data-poisoning-attacks]] | Reduces training data poisoning by vetting data sources before ingestion |
| | [[techniques/embedding-poisoning]] | Blocks malicious embedding injection by validating vector database write sources |
| AML.T0051.002 | [[techniques/retrieval-manipulation]] | Prevents retrieval bias by ensuring only trusted content is indexed; blocks low-trust sources from contributing documents that could manipulate retrieval rankings |
| | [[techniques/unauthorized-knowledge-disclosure]] | Validates document access permissions and source trust before retrieval, preventing unauthorized sources from leaking restricted knowledge |

## Implementation

### Trust Scoring Framework

**Core Components:**

1. **Source Registry and Reputation Database**
   - Maintain centralized registry of all data sources (public datasets, APIs, user uploads, partners)
   - Track historical metrics: accuracy of contributed data, security incidents, validation failure rates
   - Assign initial trust scores based on source type and provenance verification
   - Update scores dynamically based on ongoing validation results

2. **Trust Score Calculation**

```python
def calculate_trust_score(source):
    """Multi-factor trust scoring for data sources"""
    score = 100  # Start at maximum trust
    
    # Factor 1: Source type
    if source.type == "public_unverified":
        score -= 40
    elif source.type == "third_party_commercial":
        score -= 20
    elif source.type == "internal_verified":
        score += 0  # No penalty
    
    # Factor 2: Historical reliability
    if source.validation_failure_rate > 0.1:  # >10% failures
        score -= 30
    elif source.validation_failure_rate > 0.05:
        score -= 15
    
    # Factor 3: Security posture
    if not source.has_cryptographic_signing:
        score -= 20
    if source.recent_security_incidents > 0:
        score -= 25
    
    # Factor 4: Provenance verification
    if not source.chain_of_custody_verified:
        score -= 15
    
    # Factor 5: Age and track record
    if source.days_since_first_contribution < 30:
        score -= 10  # New sources are riskier
    
    return max(0, min(100, score))  # Clamp to [0, 100]
```

3. **Risk-Based Treatment Policies**

| Trust Score | Classification | Treatment |
|-------------|----------------|-----------|
| 80-100 | Trusted | Fast-track validation; lower scrutiny |
| 60-79 | Moderate | Standard validation pipeline |
| 40-59 | Elevated Risk | Enhanced validation; human review for bulk contributions |
| 20-39 | High Risk | Strict validation; mandatory quarantine period |
| 0-19 | Untrusted | Reject or require manual approval per-item |

4. **Source Vetting Process**

**For new sources:**
```
New Source Request
    ↓
Identity Verification (org validation, contact verification)
    ↓
Provenance Check (chain of custody, authentication)
    ↓
Security Assessment (signing capability, access controls)
    ↓
Initial Trust Score Assignment
    ↓
Pilot Data Batch (manual review of first N samples)
    ↓
Approval or Rejection
```

**For existing sources (continuous monitoring):**
- Track validation failure rates over rolling 90-day window
- Alert on sudden increases in anomaly detection hits
- Downgrade trust score automatically if thresholds exceeded
- Periodic re-assessment (quarterly for moderate-risk, annually for trusted)

### Validation Rule Differentiation

**High-Trust Sources (score 80-100):**
- Statistical anomaly detection only
- Spot-check sampling (5% of contributions)
- Expedited ingestion pipeline

**Moderate-Trust Sources (score 60-79):**
- Full statistical validation
- Schema and format verification
- 20% sampling for manual review
- Standard ingestion timeline

**Low-Trust Sources (score 0-59):**
- Comprehensive multi-stage validation
- Mandatory quarantine period (e.g., 48 hours before production indexing)
- 100% human review for bulk contributions
- Duplicate/near-duplicate detection against known malicious patterns
- Trigger pattern analysis (search for hidden instructions, backdoor indicators)

### RAG-Specific Implementation

**Document Ingestion with Trust Scoring:**

```python
def ingest_rag_document(document, source_id):
    """RAG document ingestion with trust-based validation"""
    source = get_source_registry(source_id)
    trust_score = calculate_trust_score(source)
    
    # Apply validation based on trust level
    if trust_score < 60:
        # High-risk source: comprehensive validation
        if not validate_format(document):
            reject_document(document, "format_invalid")
            return
        if detect_prompt_injection_patterns(document):
            quarantine_document(document, "prompt_injection_detected")
            return
        if not human_review_approval(document):
            reject_document(document, "manual_review_failed")
            return
        # Quarantine before indexing
        quarantine_queue.add(document, release_after_hours=48)
    elif trust_score < 80:
        # Moderate-risk: standard validation
        if not validate_format(document):
            reject_document(document, "format_invalid")
            return
        if random.random() < 0.2:  # 20% sampling
            flag_for_manual_review(document)
    # else: trusted source, fast-track
    
    # Embed and index
    embedding = embed_document(document)
    vector_db.insert(embedding, metadata={
        'source_id': source_id,
        'trust_score': trust_score,
        'ingestion_timestamp': now(),
        'validation_level': get_validation_level(trust_score)
    })
```

**Retrieval-Time Trust Filtering:**

```python
def retrieve_with_trust_threshold(query, min_trust_score=60):
    """Only retrieve documents from sources meeting trust threshold"""
    results = vector_db.search(query, top_k=10)
    
    # Filter by trust score
    filtered = [r for r in results if r.metadata['trust_score'] >= min_trust_score]
    
    # Log if high-similarity results were filtered due to low trust
    if len(filtered) < len(results):
        log_trust_filter_event(query, results, filtered)
    
    return filtered
```

### Integration with Data Provenance

Source validation works in tandem with [[mitigations/content-signing-and-provenance]]:
- Trust scores inform provenance verification depth (low-trust sources require stronger proof)
- Cryptographic signatures from high-trust sources increase confidence
- Provenance chain breaks automatically downgrade trust scores

## Limitations & Trade-offs

**Limitations:**
- **New Source Cold Start**: New legitimate sources start with low trust scores, requiring manual validation overhead
- **Trust Score Gaming**: Sophisticated attackers may build reputation over time before poisoning
- **False Positives**: Legitimate content from new or unverified sources may be rejected
- **Insider Threats**: High-trust internal sources can still be compromised

**Trade-offs:**
- **Security vs. Data Diversity**: Strict trust requirements may limit access to valuable but unverified data sources
- **Latency vs. Validation Depth**: Comprehensive validation for low-trust sources increases ingestion time
- **Automation vs. Human Review**: Balancing automated scoring with manual oversight for edge cases

**Bypass Scenarios:**
- **Reputation Building Attack**: Attacker contributes clean data to build trust score before injecting poison
- **Compromised Trusted Source**: Attacker gains access to previously trusted source credentials
- **Trust Score Manipulation**: If scoring logic is discoverable, attacker optimizes to meet threshold

## Testing & Validation

**Functional Testing:**
1. **Score Calculation Accuracy**: Verify trust scores calculated correctly for diverse source profiles
2. **Policy Enforcement**: Confirm validation rigor matches trust score classification
3. **Dynamic Score Updates**: Test that scores update correctly based on validation failures
4. **Quarantine Workflows**: Validate low-trust content properly quarantined before production

**Security Testing:**
1. **Untrusted Source Injection**: Attempt to inject poisoned data via low-trust source, verify rejection
2. **Reputation Gaming**: Simulate attacker building trust over time, test detection of sudden behavior change
3. **Trust Threshold Bypass**: Attempt to manipulate trust score calculation or policy enforcement
4. **Compromised Source Simulation**: Test detection when previously trusted source exhibits anomalies

**Monitoring:**
- Track trust score distribution across active sources
- Alert on rapid trust score degradation for established sources
- Monitor rejection rates by trust tier (high rejection in trusted tier indicates potential issue)
- Measure false positive rate (legitimate content rejected due to low trust)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/virustotal-poisoning]] | [[frameworks/atlas/tactics/resource-development]] | Trust scoring would have flagged suspicious contributors to public malware dataset |

## Sources

> "Source Vetting and Trust Scoring: Evaluate and score data sources based on reputation, historical reliability, and security posture; restrict or reject data from untrusted sources."
> — Extracted from RAG Data Poisoning mitigation section

> "Data Provenance Tracking: Maintain cryptographic lineage of all data sources with immutable audit trails; verify integrity using checksums or digital signatures."
> — Extracted from RAG Data Poisoning mitigation section

## Related

- **Combines with**: [[mitigations/content-signing-and-provenance]], [[mitigations/data-quarantine-procedures]], [[mitigations/anomaly-detection-architecture]]
- **Enables**: Risk-based validation depth, reducing attack surface while maintaining data source diversity
