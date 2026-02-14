---
title: "Source Revocation"
tags:
  - type/mitigation
  - target/rag
  - target/ml-model
  - target/training-pipeline
  - source/adversarial-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Source Revocation

## Summary

Source revocation is the incident response procedure for immediately blocking or restricting data sources confirmed as compromised, malicious, or delivering poisoned content. This mitigation prevents ongoing poisoning attacks by cutting off the supply of malicious data at the source level, halting further damage while forensic analysis and remediation proceed. Effective source revocation requires provenance tracking to identify affected data, automated enforcement mechanisms to block contributions, and documented procedures for both emergency revocation and reinstatement.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Stops ongoing poisoning by blocking compromised data providers |
| | [[techniques/data-poisoning-attacks]] | Prevents continued injection of label flipping, triggers, or clean-label attacks |
| | [[techniques/supply-chain-attacks]] | Mitigates supply chain compromises by revoking affected third-party data sources |
| | [[techniques/incremental-poisoning]] | Halts gradual poisoning campaigns from identified malicious sources |

## Implementation

### Source Revocation Workflow

```python
class SourceRevocationManager:
    """Manage data source revocation and blocking"""
    
    def __init__(self, source_registry, data_pipeline):
        self.source_registry = source_registry
        self.data_pipeline = data_pipeline
        self.revocation_log = []
    
    def revoke_source(self, source_id, reason, severity='high', auto_quarantine=True):
        """Immediately revoke a data source"""
        
        # Step 1: Verify source exists
        source = self.source_registry.get(source_id)
        if not source:
            raise ValueError(f"Source {source_id} not found")
        
        # Step 2: Update source status
        source.status = 'revoked'
        source.revocation_reason = reason
        source.revocation_timestamp = datetime.now()
        source.revocation_severity = severity
        
        # Step 3: Block future contributions
        self.data_pipeline.block_source(source_id)
        
        # Step 4: Identify affected data
        affected_data = self.identify_affected_data(source_id)
        
        print(f"Revoking source {source_id}: {len(affected_data)} samples affected")
        
        # Step 5: Quarantine affected data (if auto-quarantine enabled)
        if auto_quarantine:
            for sample in affected_data:
                self.quarantine_sample(sample, reason=f"Source {source_id} revoked")
        
        # Step 6: Alert stakeholders
        self.send_revocation_alert(source_id, reason, len(affected_data))
        
        # Step 7: Log revocation
        self.log_revocation(source_id, reason, severity, len(affected_data))
        
        return {
            'source_id': source_id,
            'status': 'revoked',
            'affected_samples': len(affected_data),
            'quarantined': auto_quarantine
        }
    
    def identify_affected_data(self, source_id):
        """Find all data from revoked source"""
        # Query training dataset
        training_affected = [
            sample for sample in self.data_pipeline.training_data
            if sample.source_id == source_id
        ]
        
        # Query RAG knowledge base
        rag_affected = []
        if hasattr(self.data_pipeline, 'rag_db'):
            rag_affected = self.data_pipeline.rag_db.query_by_source(source_id)
        
        return training_affected + rag_affected
    
    def quarantine_sample(self, sample, reason):
        """Move sample to quarantine"""
        self.data_pipeline.quarantine.add(sample, reason=reason)
        self.data_pipeline.remove_from_production(sample.id)
    
    def send_revocation_alert(self, source_id, reason, affected_count):
        """Notify security team of source revocation"""
        alert = {
            'type': 'source_revocation',
            'severity': 'high',
            'source_id': source_id,
            'reason': reason,
            'affected_samples': affected_count,
            'timestamp': datetime.now()
        }
        
        # Send to monitoring system
        send_security_alert(alert)
    
    def log_revocation(self, source_id, reason, severity, affected_count):
        """Log revocation event"""
        self.revocation_log.append({
            'timestamp': datetime.now(),
            'source_id': source_id,
            'reason': reason,
            'severity': severity,
            'affected_count': affected_count
        })
```

### Emergency Revocation Procedure

**For active attacks or confirmed compromises:**

```python
def emergency_source_revocation(source_id, incident_details):
    """Fast-track revocation for active incidents"""
    
    print(f"=== EMERGENCY SOURCE REVOCATION: {source_id} ===")
    print(f"Incident: {incident_details}")
    
    # Immediate actions (no approval required)
    revocation_manager = SourceRevocationManager(source_registry, data_pipeline)
    
    # 1. Block source immediately
    result = revocation_manager.revoke_source(
        source_id, 
        reason=incident_details,
        severity='critical',
        auto_quarantine=True
    )
    
    # 2. Stop all active jobs using this source
    data_pipeline.cancel_jobs_using_source(source_id)
    
    # 3. Trigger forensic analysis
    forensics_queue.add({
        'source_id': source_id,
        'incident': incident_details,
        'affected_samples': result['affected_samples'],
        'priority': 'urgent'
    })
    
    # 4. Initiate model rollback if recently deployed
    if recently_trained_with_source(source_id, days=7):
        trigger_model_rollback(reason=f"Emergency source revocation: {source_id}")
    
    # 5. Send critical alert
    send_critical_alert({
        'type': 'emergency_revocation',
        'source_id': source_id,
        'incident': incident_details,
        'affected_samples': result['affected_samples'],
        'actions_taken': [
            'Source blocked',
            'Data quarantined',
            'Jobs cancelled',
            'Forensics initiated'
        ]
    })
    
    print(f"Emergency revocation complete. {result['affected_samples']} samples quarantined.")
    
    return result
```

### Batch Revocation

**For coordinated attacks or multiple compromised sources:**

```python
def batch_source_revocation(source_ids, reason):
    """Revoke multiple sources simultaneously"""
    
    results = []
    total_affected = 0
    
    for source_id in source_ids:
        try:
            result = revocation_manager.revoke_source(source_id, reason, severity='high')
            results.append(result)
            total_affected += result['affected_samples']
        except Exception as e:
            print(f"Error revoking {source_id}: {e}")
            results.append({
                'source_id': source_id,
                'status': 'failed',
                'error': str(e)
            })
    
    # Aggregate alert
    send_security_alert({
        'type': 'batch_revocation',
        'revoked_sources': len([r for r in results if r.get('status') == 'revoked']),
        'total_affected_samples': total_affected,
        'reason': reason
    })
    
    return results
```

### Source Reinstatement Procedure

**Restoring revoked sources after remediation:**

```python
def reinstate_source(source_id, verification_report):
    """Reinstate previously revoked source"""
    
    source = source_registry.get(source_id)
    if source.status != 'revoked':
        raise ValueError(f"Source {source_id} is not revoked")
    
    # Verification requirements
    required_checks = [
        'security_review_completed',
        'data_quality_verified',
        'historical_data_revalidated',
        'access_controls_enhanced',
        'monitoring_deployed'
    ]
    
    for check in required_checks:
        if not verification_report.get(check):
            raise ValueError(f"Reinstatement check failed: {check}")
    
    # Gradual reinstatement (probationary period)
    source.status = 'probationary'
    source.probation_end_date = datetime.now() + timedelta(days=30)
    source.trust_score = 40  # Start with low trust
    source.max_contributions_per_day = 100  # Rate limit during probation
    
    # Enable monitoring
    enable_enhanced_monitoring(source_id)
    
    # Log reinstatement
    log_source_reinstatement({
        'source_id': source_id,
        'verification_report': verification_report,
        'probation_period': 30,
        'initial_trust_score': 40,
        'timestamp': datetime.now()
    })
    
    print(f"Source {source_id} reinstated with probationary status (30 days)")
    
    return source
```

### Integration with Trust Scoring

**Automatic revocation based on trust score degradation:**

```python
def automatic_trust_based_revocation(trust_score_threshold=20):
    """Automatically revoke sources that fall below trust threshold"""
    
    all_sources = source_registry.get_all()
    revoked_count = 0
    
    for source in all_sources:
        if source.status == 'active' and source.trust_score < trust_score_threshold:
            # Automatic revocation
            result = revocation_manager.revoke_source(
                source.id,
                reason=f"Trust score dropped to {source.trust_score} (threshold: {trust_score_threshold})",
                severity='medium',
                auto_quarantine=True
            )
            revoked_count += 1
            print(f"Auto-revoked {source.id}: trust score {source.trust_score}")
    
    if revoked_count > 0:
        send_security_alert({
            'type': 'automatic_trust_revocation',
            'count': revoked_count,
            'threshold': trust_score_threshold
        })
    
    return revoked_count
```

### Revocation Impact Assessment

```python
def assess_revocation_impact(source_id):
    """Analyze impact of revoking a source"""
    
    # Identify affected data
    affected_samples = identify_affected_data(source_id)
    
    # Calculate impact on dataset size
    total_dataset_size = len(data_pipeline.training_data)
    impact_percentage = len(affected_samples) / total_dataset_size
    
    # Check category coverage
    affected_categories = set(s.category for s in affected_samples)
    category_impact = {}
    for category in affected_categories:
        category_samples = [s for s in affected_samples if s.category == category]
        total_category_samples = len([s for s in data_pipeline.training_data if s.category == category])
        category_impact[category] = len(category_samples) / total_category_samples
    
    # Model performance projection
    if impact_percentage > 0.20:  # >20% data loss
        performance_impact = 'high'
        recommendation = 'Find replacement data source before revocation'
    elif impact_percentage > 0.10:
        performance_impact = 'medium'
        recommendation = 'Monitor model performance after revocation'
    else:
        performance_impact = 'low'
        recommendation = 'Proceed with revocation'
    
    return {
        'affected_samples': len(affected_samples),
        'impact_percentage': impact_percentage,
        'performance_impact': performance_impact,
        'category_impact': category_impact,
        'recommendation': recommendation
    }
```

## Limitations & Trade-offs

**Limitations:**
- **Data Loss**: Revoking sources removes data, potentially degrading model performance
- **False Positives**: Overly aggressive revocation may block legitimate sources
- **Delayed Detection**: Revocation only prevents future damage; past poison remains until cleaned
- **Provenance Dependency**: Requires accurate source tracking; poor provenance limits effectiveness

**Trade-offs:**
- **Revocation Speed vs. Verification**: Emergency revocation is fast but risks false positives; verification is thorough but slower
- **Quarantine vs. Deletion**: Quarantine preserves data for forensics but risks reintroduction; deletion is permanent but irreversible
- **Probationary Period vs. Trust**: Long probation periods protect against re-compromise but delay full data access

## Testing & Validation

**Functional Testing:**
1. **Revocation Enforcement**: Verify blocked sources cannot contribute new data
2. **Affected Data Identification**: Confirm all data from revoked source is identified
3. **Quarantine Workflow**: Test automatic quarantine of affected samples
4. **Reinstatement Process**: Validate probationary status and gradual trust restoration

**Security Testing:**
1. **Revocation Bypass Attempts**: Test if revoked sources can circumvent blocks
2. **Reintroduction Prevention**: Verify revoked data doesn't leak back into production
3. **Emergency Revocation Latency**: Measure time from compromise detection to source block

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/virustotal-poisoning]] | [[frameworks/atlas/tactics/resource-development]] | Source revocation would have blocked malicious contributors to public dataset |

## Sources

> "Source Revocation: Block or restrict data sources confirmed as compromised."
> — Extracted from RAG Data Poisoning mitigation section

## Related

- **Follows**: [[mitigations/incident-response-procedures]], [[mitigations/source-validation-and-trust-scoring]]
- **Combines with**: [[mitigations/data-quarantine-procedures]], [[mitigations/data-cleansing-and-retraining]], [[mitigations/data-provenance]]
- **Enables**: Attack containment, ongoing poisoning prevention, incident response
