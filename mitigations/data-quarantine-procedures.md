---
title: "Data Quarantine Procedures"
tags:
  - type/mitigation
  - target/rag
  - target/ml-model
  - target/training-pipeline
  - source/adversarial-ai
atlas: AML.M0022
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Data Quarantine Procedures

## Summary

Data quarantine procedures isolate potentially malicious or untrusted data in a secure holding area before it enters production training pipelines or RAG systems. This mitigation creates a mandatory buffer period during which automated validation, anomaly detection, and manual review can identify poisoned or manipulated content before it affects model behavior. Quarantine applies to high-risk data sources, bulk uploads, or content flagged by validation checks, preventing immediate production impact while investigation proceeds.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/pii-in-corpus]] | Provides review window to detect and redact sensitive information before exposure in training data or knowledge bases |
| AML.T0020 | [[techniques/rag-data-poisoning]] | Prevents poisoned documents from immediately entering RAG retrieval; allows validation during quarantine period |
| | [[techniques/data-poisoning-attacks]] | Isolates suspect training data before it corrupts model learning |
| | [[techniques/embedding-poisoning]] | Holds manipulated embeddings for verification before production indexing |

## Implementation

### Quarantine Architecture

**Core Workflow:**

```
Data Ingestion
    ↓
Risk Assessment (trust scoring, source validation)
    ↓
    ├─ Low Risk → Standard Validation → Production
    └─ High Risk → QUARANTINE ZONE
                      ↓
                  Automated Validation
                      ↓
                  Anomaly Detection
                      ↓
                  Manual Review (if flagged)
                      ↓
                  Time-Based Hold (e.g., 48 hours)
                      ↓
                  ├─ Pass → Production Release
                  └─ Fail → Rejection + Forensics
```

### Quarantine Trigger Conditions

**Automatic quarantine required for:**

1. **Source-Based Triggers**:
   - Data from sources with trust score < 60 (see [[mitigations/source-validation-and-trust-scoring]])
   - New data sources (first 30 days of contributions)
   - Sources with recent validation failure rate > 10%
   - Unverified or unauthenticated contributors

2. **Content-Based Triggers**:
   - Documents containing prompt injection pattern signatures
   - Data flagged by anomaly detection (statistical outliers, unusual token distributions)
   - Bulk uploads exceeding size threshold (e.g., >1000 documents in single batch)
   - Documents with conflicting labels or metadata inconsistencies
   - Embeddings exhibiting semantic drift from expected clusters

3. **Behavioral Triggers**:
   - User accounts exhibiting suspicious upload patterns (rapid succession, automated scripts)
   - Sources with sudden change in contribution volume or content characteristics
   - Failed cryptographic signature verification

### Quarantine Infrastructure

**Isolated Storage:**

```python
class QuarantineZone:
    """Secure isolation for untrusted data pending validation"""
    
    def __init__(self, storage_backend, validation_pipeline):
        self.storage = storage_backend  # Separate from production DB
        self.validation = validation_pipeline
        self.hold_period_hours = 48  # Configurable
    
    def quarantine_data(self, data, source_id, trigger_reason):
        """Isolate data in quarantine with metadata"""
        quarantine_record = {
            'data': data,
            'source_id': source_id,
            'quarantine_timestamp': datetime.now(),
            'release_timestamp': datetime.now() + timedelta(hours=self.hold_period_hours),
            'trigger_reason': trigger_reason,
            'validation_status': 'pending',
            'review_status': 'pending',
            'alerts': []
        }
        
        record_id = self.storage.insert(quarantine_record)
        
        # Schedule validation jobs
        self.validation.schedule_automated_checks(record_id)
        
        # Flag for manual review if high-risk
        if trigger_reason in ['prompt_injection_detected', 'anomaly_high_severity']:
            self.flag_for_human_review(record_id)
        
        log_quarantine_event(record_id, trigger_reason)
        return record_id
```

**Validation During Quarantine:**

```python
def execute_quarantine_validation(record_id):
    """Multi-stage validation for quarantined data"""
    record = quarantine_storage.get(record_id)
    
    # Stage 1: Automated security checks
    checks = [
        detect_prompt_injection(record.data),
        detect_malicious_patterns(record.data),
        verify_format_compliance(record.data),
        check_for_pii(record.data),
        statistical_anomaly_detection(record.data)
    ]
    
    failed_checks = [c for c in checks if not c.passed]
    
    if failed_checks:
        record.alerts.extend(failed_checks)
        record.validation_status = 'failed'
        flag_for_manual_review(record_id, failed_checks)
        return 'validation_failed'
    
    # Stage 2: Source reputation cross-check
    source = get_source_registry(record.source_id)
    if source.trust_score < 40:
        # Low-trust sources require manual approval even if automated checks pass
        flag_for_manual_review(record_id, 'low_trust_source')
        return 'manual_review_required'
    
    # Stage 3: Duplicate detection
    if find_near_duplicates(record.data):
        record.alerts.append('duplicate_detected')
        flag_for_manual_review(record_id, 'duplicate')
        return 'manual_review_required'
    
    record.validation_status = 'passed'
    return 'passed'
```

### Manual Review Workflow

**Review Queue Prioritization:**

```python
def prioritize_review_queue():
    """Prioritize quarantined items for human review"""
    pending_reviews = quarantine_storage.get_flagged_for_review()
    
    # Priority scoring
    for item in pending_reviews:
        priority_score = 0
        
        # Factor 1: Severity of validation failures
        if 'prompt_injection_detected' in item.alerts:
            priority_score += 100
        if 'anomaly_high_severity' in item.alerts:
            priority_score += 75
        
        # Factor 2: Source trust
        source_trust = get_source_trust_score(item.source_id)
        priority_score += (100 - source_trust)  # Lower trust = higher priority
        
        # Factor 3: Age in quarantine
        hours_quarantined = (datetime.now() - item.quarantine_timestamp).total_seconds() / 3600
        priority_score += hours_quarantined * 2  # Older items prioritized
        
        item.review_priority = priority_score
    
    # Sort by priority
    return sorted(pending_reviews, key=lambda x: x.review_priority, reverse=True)
```

**Reviewer Decision Workflow:**

1. **Approve**: Data released to production immediately
2. **Reject**: Data permanently blocked; source flagged for investigation
3. **Modify and Approve**: Sanitize content (redact PII, remove suspicious patterns) then release
4. **Extend Quarantine**: Require additional validation or investigation before decision
5. **Escalate**: Forward to security team for forensic analysis

### Time-Based Release

**Automatic release conditions:**

```python
def check_quarantine_release():
    """Evaluate quarantined data for automatic release"""
    quarantined_items = quarantine_storage.get_all()
    
    for item in quarantined_items:
        # Release if all conditions met
        if (item.validation_status == 'passed' and
            item.review_status in ['approved', 'not_required'] and
            datetime.now() >= item.release_timestamp):
            
            # Move to production
            release_to_production(item)
            log_release_event(item.id, 'automatic_release')
        
        # Auto-reject if quarantine period expired without approval
        elif (datetime.now() > item.release_timestamp + timedelta(hours=24) and
              item.review_status == 'pending'):
            reject_data(item)
            log_release_event(item.id, 'auto_reject_timeout')
```

### RAG-Specific Quarantine

**Document quarantine before indexing:**

```python
def ingest_rag_document_with_quarantine(document, source_id):
    """RAG document ingestion with mandatory quarantine for high-risk sources"""
    source = get_source_registry(source_id)
    
    # Determine if quarantine required
    requires_quarantine = (
        source.trust_score < 60 or
        source.days_since_first_contribution < 30 or
        detect_prompt_injection_patterns(document) or
        document.size > SIZE_THRESHOLD
    )
    
    if requires_quarantine:
        # Quarantine BEFORE embedding generation
        quarantine_id = quarantine_zone.quarantine_data(
            data=document,
            source_id=source_id,
            trigger_reason=determine_trigger(source, document)
        )
        
        # Embedding happens AFTER quarantine validation passes
        return {'status': 'quarantined', 'id': quarantine_id}
    else:
        # Trusted source: direct to production
        embedding = generate_embedding(document)
        vector_db.insert(embedding, metadata={'source_id': source_id})
        return {'status': 'indexed', 'id': document.id}
```

### Quarantine Monitoring & Metrics

**Key metrics to track:**

```python
def generate_quarantine_metrics():
    """Dashboard metrics for quarantine effectiveness"""
    return {
        'total_quarantined': count_quarantined_items(),
        'avg_quarantine_duration_hours': calculate_avg_duration(),
        'quarantine_by_trigger': group_by_trigger_reason(),
        'manual_review_queue_size': count_pending_reviews(),
        'approval_rate': calculate_approval_rate(),
        'rejection_rate': calculate_rejection_rate(),
        'validation_failure_rate': calculate_validation_failure_rate(),
        'avg_time_to_review_hours': calculate_avg_review_time(),
        'false_positive_rate': calculate_false_positives()  # Legitimate data incorrectly quarantined
    }
```

**Alert conditions:**

- Review queue exceeds capacity (>100 pending items)
- Average quarantine duration exceeds SLA (>72 hours)
- Sudden spike in quarantine triggers (>3x baseline)
- High rejection rate from specific source (>30%)
- Validation failures for previously trusted source

## Limitations & Trade-offs

**Limitations:**
- **Delay in data availability**: Quarantine period creates latency between ingestion and production use
- **False positives**: Legitimate data may be unnecessarily delayed or rejected
- **Resource intensive**: Manual review requires human analysts; validation requires compute
- **Sophisticated attacks may evade**: Clean-label attacks or gradual poisoning may pass validation

**Trade-offs:**
- **Security vs. Agility**: Strict quarantine slows data pipeline but improves security posture
- **Automation vs. Accuracy**: Fully automated quarantine reduces human burden but may miss subtle attacks
- **Quarantine duration vs. risk**: Longer quarantine improves detection but delays legitimate data

**Bypass Scenarios:**
- **Trusted source compromise**: Attacker gains access to high-trust source bypassing quarantine
- **Gradual poisoning**: Small batches under quarantine threshold accumulate over time
- **Clean-label attacks**: Poisoned data with correct labels and subtle modifications evades detection

## Testing & Validation

**Functional Testing:**
1. **Quarantine Trigger Accuracy**: Verify high-risk data correctly routed to quarantine
2. **Validation Pipeline**: Confirm automated checks execute during quarantine period
3. **Release Workflow**: Test automatic and manual release paths
4. **Rejection Handling**: Validate rejected data does not reach production

**Security Testing:**
1. **Bypass Attempts**: Attempt to circumvent quarantine through format manipulation or source spoofing
2. **Time-Based Release Exploitation**: Test if attacker can force early release
3. **Validation Evasion**: Submit poisoned data designed to pass automated checks
4. **Privilege Escalation**: Attempt to approve quarantined data without proper authorization

**Performance Testing:**
- Measure quarantine processing throughput (items/hour)
- Assess impact of validation overhead on ingestion latency
- Test scalability with large review queue

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Data Quarantine: Immediately isolate suspected poisoned data from training pipelines and RAG systems."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Human-in-the-Loop Review: Flag high-risk data contributions for manual expert review before acceptance."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Rate Limiting on Data Updates: Limit frequency of knowledge base updates; require justification for bulk changes."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

## Related

- **Combines with**: [[mitigations/source-validation-and-trust-scoring]], [[mitigations/anomaly-detection-architecture]], [[mitigations/input-validation-patterns]]
- **Enables**: Time-windowed threat detection, manual review workflows, forensic analysis before production impact
