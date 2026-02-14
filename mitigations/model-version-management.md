---
title: "Model Version Management"
tags:
  - type/mitigation
  - target/llm
  - target/ml-model
  - target/rag
  - source/adversarial-ai
atlas: AML.M0026
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Model Version Management

## Summary

Model version management maintains comprehensive snapshots of models, training data, and system state at multiple points in time, enabling rapid rollback when data poisoning or model degradation is detected. This mitigation creates recovery checkpoints that allow organizations to revert to last-known-good configurations without losing all progress, minimizing downtime and damage from successful poisoning attacks. Effective version management includes not just model artifacts but also training data provenance, configuration, and validation metrics.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Enables rollback to pre-poisoning RAG corpus and embeddings when contamination detected |
| | [[techniques/data-poisoning-attacks]] | Allows reversion to clean training data and re-training from safe checkpoint |
| | [[techniques/model-tampering]] | Provides recovery path when model artifacts are compromised |
| AML.T0018 | [[techniques/trojan-injection]] | Model diffing against known-good baselines detects architecture changes from Trojan injection; rollback enables recovery |
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Rapid rollback to last known-good model checkpoint when supply chain compromise is discovered; version history enables forensic comparison |
| | [[techniques/fine-tuning-poisoning]] | Version control and rollback capabilities enable recovery from malicious fine-tuning; model diffing detects backdoors introduced via fine-tuning attacks |

## Implementation

### Core Components

#### 1. Model Checkpoint Strategy

**Versioning scheme:**

```
{model_name}-v{major}.{minor}.{patch}-{timestamp}-{commit_hash}

Examples:
- sentiment-classifier-v2.1.0-20260214T1403-a3f2b1c
- rag-embeddings-v1.5.2-20260213T0912-7d4e9a8
```

**Checkpoint metadata:**

```python
from dataclasses import dataclass
from datetime import datetime
from typing import Dict, List

@dataclass
class ModelCheckpoint:
    """Comprehensive model version metadata"""
    version: str
    timestamp: datetime
    model_artifact_path: str
    model_hash: str  # SHA-256 of model file
    
    # Training provenance
    training_data_hash: str
    training_data_snapshot_path: str
    hyperparameters: Dict
    training_duration_minutes: int
    framework_version: str
    
    # Performance metrics
    validation_metrics: Dict  # {'accuracy': 0.95, 'f1': 0.93, ...}
    test_metrics: Dict
    
    # Deployment metadata
    deployment_status: str  # 'production', 'staging', 'archived'
    deployment_timestamp: datetime
    
    # Quality assurance
    validation_passed: bool
    security_scan_passed: bool
    human_review_approved: bool
    
    # Rollback metadata
    previous_version: str
    rollback_reason: str = None  # Populated if this version was rolled back
    
    def to_dict(self):
        """Serialize checkpoint metadata"""
        return {
            'version': self.version,
            'timestamp': self.timestamp.isoformat(),
            'model_artifact_path': self.model_artifact_path,
            'model_hash': self.model_hash,
            'training_data_hash': self.training_data_hash,
            'training_data_snapshot_path': self.training_data_snapshot_path,
            'hyperparameters': self.hyperparameters,
            'training_duration_minutes': self.training_duration_minutes,
            'validation_metrics': self.validation_metrics,
            'test_metrics': self.test_metrics,
            'deployment_status': self.deployment_status,
            'validation_passed': self.validation_passed,
            'security_scan_passed': self.security_scan_passed,
            'human_review_approved': self.human_review_approved
        }
```

#### 2. Automated Checkpoint Creation

**Training pipeline integration:**

```python
class VersionedTrainingPipeline:
    """Training pipeline with automatic checkpoint creation"""
    
    def __init__(self, storage_backend, retention_policy):
        self.storage = storage_backend
        self.retention = retention_policy
    
    def train_and_checkpoint(self, training_config):
        """Train model and create versioned checkpoint"""
        # Generate version ID
        version = self.generate_version_id(training_config)
        
        # Snapshot training data
        training_data_snapshot = self.snapshot_training_data(
            training_config['data_sources']
        )
        
        # Train model
        model, training_metrics = self.train_model(
            training_config,
            training_data_snapshot
        )
        
        # Validate model
        validation_metrics = self.validate_model(model)
        security_scan_passed = self.security_scan_model(model)
        
        # Create checkpoint
        checkpoint = ModelCheckpoint(
            version=version,
            timestamp=datetime.now(),
            model_artifact_path=self.save_model_artifact(model, version),
            model_hash=self.calculate_model_hash(model),
            training_data_hash=self.calculate_data_hash(training_data_snapshot),
            training_data_snapshot_path=training_data_snapshot.path,
            hyperparameters=training_config['hyperparameters'],
            training_duration_minutes=training_metrics['duration_minutes'],
            framework_version=get_framework_version(),
            validation_metrics=validation_metrics,
            test_metrics={},  # Populated later
            deployment_status='staging',
            deployment_timestamp=None,
            validation_passed=self.check_validation_criteria(validation_metrics),
            security_scan_passed=security_scan_passed,
            human_review_approved=False,
            previous_version=self.get_current_production_version()
        )
        
        # Store checkpoint metadata
        self.storage.save_checkpoint_metadata(checkpoint)
        
        # Apply retention policy
        self.apply_retention_policy()
        
        return checkpoint
    
    def generate_version_id(self, config):
        """Generate semantic version ID"""
        current_version = self.get_current_production_version()
        
        # Increment version based on change type
        if config.get('breaking_change'):
            return increment_major_version(current_version)
        elif config.get('new_features'):
            return increment_minor_version(current_version)
        else:
            return increment_patch_version(current_version)
```

#### 3. Rollback Mechanism

**Rapid rollback implementation:**

```python
class ModelRollbackManager:
    """Manage model version rollbacks"""
    
    def __init__(self, storage, deployment_client):
        self.storage = storage
        self.deployment = deployment_client
    
    def rollback_to_version(self, target_version, reason):
        """Rollback to specific model version"""
        # Validate target version exists
        target_checkpoint = self.storage.get_checkpoint(target_version)
        if not target_checkpoint:
            raise ValueError(f'Version {target_version} not found')
        
        # Verify target version is validated
        if not target_checkpoint.validation_passed:
            raise RollbackError(f'Target version {target_version} failed validation')
        
        # Get current production version
        current_version = self.get_current_production_version()
        
        # Create rollback audit record
        rollback_record = {
            'timestamp': datetime.now(),
            'from_version': current_version,
            'to_version': target_version,
            'reason': reason,
            'operator': get_current_user(),
            'approval_status': 'pending'
        }
        
        # Require approval for production rollback
        if not self.approval_required() or self.get_approval(rollback_record):
            # Execute rollback
            self.deployment.swap_model(
                current_path=self.get_production_model_path(),
                new_path=target_checkpoint.model_artifact_path
            )
            
            # Update deployment status
            target_checkpoint.deployment_status = 'production'
            target_checkpoint.deployment_timestamp = datetime.now()
            self.storage.update_checkpoint(target_checkpoint)
            
            # Mark current version as rolled back
            current_checkpoint = self.storage.get_checkpoint(current_version)
            current_checkpoint.rollback_reason = reason
            current_checkpoint.deployment_status = 'archived'
            self.storage.update_checkpoint(current_checkpoint)
            
            # Log rollback
            log_rollback_event(rollback_record)
            
            return True
        else:
            raise RollbackError('Rollback approval denied')
    
    def rollback_to_last_known_good(self, reason):
        """Rollback to most recent validated version"""
        # Find most recent production version before current
        versions = self.storage.list_checkpoints(
            deployment_status='production',
            validation_passed=True,
            order_by='timestamp DESC'
        )
        
        if len(versions) < 2:
            raise RollbackError('No previous production version available')
        
        # Second entry is the previous production version
        last_known_good = versions[1]
        
        return self.rollback_to_version(last_known_good.version, reason)
```

#### 4. RAG Corpus Versioning

**Snapshot RAG vector database:**

```python
class RAGCorpusVersionManager:
    """Version management for RAG knowledge bases"""
    
    def __init__(self, vector_db, storage):
        self.vector_db = vector_db
        self.storage = storage
    
    def create_corpus_snapshot(self, snapshot_name):
        """Create point-in-time snapshot of RAG corpus"""
        snapshot_id = f'{snapshot_name}-{datetime.now().isoformat()}'
        
        # Export vector database
        corpus_export = self.vector_db.export_all()
        
        # Calculate corpus hash
        corpus_hash = hashlib.sha256(
            json.dumps(corpus_export, sort_keys=True).encode()
        ).hexdigest()
        
        # Store snapshot
        snapshot_path = self.storage.save_corpus_snapshot(
            snapshot_id,
            corpus_export
        )
        
        # Create snapshot metadata
        snapshot = {
            'snapshot_id': snapshot_id,
            'timestamp': datetime.now(),
            'corpus_hash': corpus_hash,
            'snapshot_path': snapshot_path,
            'document_count': len(corpus_export),
            'embedding_count': sum(len(doc['embeddings']) for doc in corpus_export),
            'created_by': get_current_user()
        }
        
        self.storage.save_snapshot_metadata(snapshot)
        return snapshot_id
    
    def restore_corpus_snapshot(self, snapshot_id, reason):
        """Restore RAG corpus to previous snapshot"""
        # Load snapshot
        snapshot = self.storage.get_snapshot_metadata(snapshot_id)
        corpus_data = self.storage.load_corpus_snapshot(snapshot['snapshot_path'])
        
        # Create backup of current corpus before restoration
        backup_id = self.create_corpus_snapshot(f'pre-restore-backup')
        
        # Clear current corpus
        self.vector_db.clear_all()
        
        # Restore snapshot data
        self.vector_db.import_all(corpus_data)
        
        # Log restoration
        log_corpus_restore_event({
            'restored_snapshot': snapshot_id,
            'backup_snapshot': backup_id,
            'reason': reason,
            'timestamp': datetime.now()
        })
        
        return True
```

#### 5. Retention Policy Management

**Automated cleanup of old versions:**

```python
class VersionRetentionPolicy:
    """Define and enforce version retention policies"""
    
    def __init__(self, config):
        self.config = config
    
    def apply_policy(self, checkpoints):
        """Determine which checkpoints to keep/delete"""
        now = datetime.now()
        keep = []
        delete = []
        
        for checkpoint in checkpoints:
            age_days = (now - checkpoint.timestamp).days
            
            # Always keep current production version
            if checkpoint.deployment_status == 'production':
                keep.append(checkpoint)
                continue
            
            # Keep all versions from last 7 days
            if age_days < 7:
                keep.append(checkpoint)
                continue
            
            # Keep weekly snapshots for last 90 days
            if age_days < 90 and checkpoint.timestamp.weekday() == 0:  # Monday
                keep.append(checkpoint)
                continue
            
            # Keep monthly snapshots for last year
            if age_days < 365 and checkpoint.timestamp.day == 1:
                keep.append(checkpoint)
                continue
            
            # Keep validated versions longer
            if checkpoint.validation_passed and age_days < 180:
                keep.append(checkpoint)
                continue
            
            # Delete everything else
            delete.append(checkpoint)
        
        return keep, delete
```

### Integration with Incident Response

```python
def handle_poisoning_detection(incident):
    """Incident response workflow for detected poisoning"""
    rollback_manager = ModelRollbackManager(storage, deployment)
    
    # Step 1: Immediately rollback to last known good
    try:
        rollback_manager.rollback_to_last_known_good(
            reason=f'Data poisoning detected: {incident.description}'
        )
        log_info('Emergency rollback completed')
    except Exception as e:
        log_error(f'Rollback failed: {e}')
        # Escalate to manual intervention
        alert_ops_team('CRITICAL: Automated rollback failed')
        return
    
    # Step 2: Quarantine suspect data
    quarantine_recent_data(hours=incident.suspected_timeframe_hours)
    
    # Step 3: Initiate forensic analysis
    start_forensic_investigation(incident)
    
    # Step 4: Plan remediation
    # - Clean poisoned data
    # - Retrain model from clean checkpoint
    # - Deploy validated clean version
```

## Limitations & Trade-offs

**Limitations:**
- **Storage overhead**: Maintaining multiple model versions and data snapshots requires significant storage
- **Stale rollback targets**: Rolling back may reintroduce previously fixed bugs or lose legitimate improvements
- **Incomplete state capture**: Difficult to snapshot entire system state (external dependencies, configuration drift)
- **Rollback lag**: Time between detection and rollback completion leaves vulnerability window

**Trade-offs:**
- **Recovery vs. Storage Cost**: More frequent checkpoints enable faster recovery but consume more storage
- **Retention vs. Compliance**: Long retention aids forensics but may conflict with data minimization requirements
- **Automation vs. Safety**: Automated rollback reduces response time but risks reverting incorrectly

## Testing & Validation

**Functional Testing:**
1. **Checkpoint Creation**: Verify all metadata correctly captured
2. **Rollback Execution**: Test rollback to previous versions, confirm successful deployment
3. **Retention Policy**: Validate correct versions kept/deleted based on policy
4. **Snapshot Integrity**: Verify restored corpus matches original snapshot

**Disaster Recovery Testing:**
1. **Emergency Rollback Drill**: Simulate poisoning detection, measure rollback completion time
2. **Data Loss Scenarios**: Test recovery when snapshots are corrupted or missing
3. **Partial Rollback**: Test rolling back individual components (model vs. corpus vs. configuration)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Model Rollback: Revert to last known-good model version when poisoning detected."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Dataset Rollback: Restore previous embedding snapshot or training dataset version."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

## Related

- **Combines with**: [[mitigations/incident-response-procedures]], [[mitigations/drift-detection-monitoring]], [[mitigations/content-signing-and-provenance]]
- **Enables**: Rapid recovery from poisoning attacks, forensic analysis of compromised versions
