---
title: "Data Cleansing and Retraining"
tags:
  - type/mitigation
  - target/ml-model
  - target/rag
  - target/training-pipeline
  - source/adversarial-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Data Cleansing and Retraining

## Summary

Data cleansing and retraining is the remediation process for recovering from data poisoning attacks by identifying and removing poisoned samples from corrupted datasets, validating the cleaned data, and retraining models from verified clean data. This mitigation is a critical incident response procedure that restores model integrity after poisoning is detected. Effective data cleansing requires forensic analysis to identify poisoned samples, validation to ensure completeness, and disciplined retraining procedures to prevent reintroduction of poison.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Remediates poisoned RAG corpora by removing malicious documents and re-indexing |
| | [[techniques/data-poisoning-attacks]] | Recovers from label flipping, trigger injection, and clean-label attacks |
| | [[techniques/backdoor-ml-model]] | Eliminates backdoor triggers by removing poisoned training data |
| | [[techniques/model-skewing]] | Corrects targeted bias by cleansing biased training samples |

## Implementation

### Poisoned Sample Identification

**Forensic analysis to find poisoned data:**

```python
class PoisonDetector:
    """Identify poisoned samples in corrupted dataset"""
    
    def __init__(self, baseline_model, validation_set):
        self.baseline_model = baseline_model  # Reference model trained on clean data
        self.validation_set = validation_set
    
    def identify_poisoned_samples(self, suspect_dataset):
        """Multi-method poisoning detection"""
        poisoned_candidates = []
        
        # Method 1: Prediction disagreement with baseline
        disagreements = self.find_baseline_disagreements(suspect_dataset)
        poisoned_candidates.extend(disagreements)
        
        # Method 2: Statistical outlier detection
        outliers = self.find_statistical_outliers(suspect_dataset)
        poisoned_candidates.extend(outliers)
        
        # Method 3: Duplicate detection with label conflicts
        label_conflicts = self.find_label_conflicts(suspect_dataset)
        poisoned_candidates.extend(label_conflicts)
        
        # Method 4: Source-based filtering (if provenance available)
        if has_provenance_data(suspect_dataset):
            compromised_sources = identify_compromised_sources(suspect_dataset)
            source_filtered = [s for s in suspect_dataset if s.source_id in compromised_sources]
            poisoned_candidates.extend(source_filtered)
        
        # De-duplicate and score
        scored_suspects = self.score_poison_confidence(poisoned_candidates)
        
        return scored_suspects
    
    def find_baseline_disagreements(self, dataset):
        """Find samples where corrupted model predictions disagree with baseline"""
        disagreements = []
        
        for sample in dataset:
            baseline_pred = self.baseline_model.predict(sample.input)
            
            # If ground-truth label disagrees with baseline (possible label flip)
            if sample.label != baseline_pred:
                disagreements.append({
                    'sample_id': sample.id,
                    'sample': sample,
                    'reason': 'baseline_disagreement',
                    'baseline_prediction': baseline_pred,
                    'dataset_label': sample.label,
                    'confidence': 0.7
                })
        
        return disagreements
    
    def find_statistical_outliers(self, dataset):
        """Detect anomalous samples using statistical methods"""
        # Feature extraction
        features = [extract_features(sample) for sample in dataset]
        
        # Outlier detection (Isolation Forest)
        from sklearn.ensemble import IsolationForest
        detector = IsolationForest(contamination=0.05)  # Assume 5% poison
        outlier_labels = detector.fit_predict(features)
        
        outliers = []
        for i, label in enumerate(outlier_labels):
            if label == -1:  # Outlier
                outliers.append({
                    'sample_id': dataset[i].id,
                    'sample': dataset[i],
                    'reason': 'statistical_outlier',
                    'confidence': 0.6
                })
        
        return outliers
    
    def find_label_conflicts(self, dataset):
        """Find near-duplicate samples with different labels"""
        conflicts = []
        
        # Build similarity index
        embeddings = [embed(sample.text) for sample in dataset]
        
        for i, sample_i in enumerate(dataset):
            for j, sample_j in enumerate(dataset[i+1:], start=i+1):
                similarity = cosine_similarity(embeddings[i], embeddings[j])
                
                # High similarity but different labels
                if similarity > 0.95 and sample_i.label != sample_j.label:
                    conflicts.append({
                        'sample_ids': [sample_i.id, sample_j.id],
                        'samples': [sample_i, sample_j],
                        'reason': 'label_conflict',
                        'similarity': similarity,
                        'confidence': 0.8
                    })
        
        return conflicts
    
    def score_poison_confidence(self, candidates):
        """Aggregate confidence scores across detection methods"""
        sample_scores = {}
        
        for candidate in candidates:
            sample_id = candidate['sample_id']
            confidence = candidate['confidence']
            
            if sample_id not in sample_scores:
                sample_scores[sample_id] = {
                    'sample': candidate['sample'],
                    'detection_methods': [],
                    'total_confidence': 0.0
                }
            
            sample_scores[sample_id]['detection_methods'].append(candidate['reason'])
            sample_scores[sample_id]['total_confidence'] += confidence
        
        # Normalize confidence (cap at 1.0)
        for sample_id, score_data in sample_scores.items():
            score_data['confidence'] = min(1.0, score_data['total_confidence'])
        
        # Sort by confidence (descending)
        sorted_suspects = sorted(
            sample_scores.items(), 
            key=lambda x: x[1]['confidence'], 
            reverse=True
        )
        
        return sorted_suspects
```

### Data Cleansing Workflow

```python
class DataCleansingWorkflow:
    """Orchestrate data cleansing process"""
    
    def __init__(self, poisoned_dataset, baseline_model):
        self.poisoned_dataset = poisoned_dataset
        self.baseline_model = baseline_model
        self.cleansing_log = []
    
    def execute_cleansing(self, manual_review_threshold=0.7, auto_remove_threshold=0.95):
        """Execute multi-stage data cleansing"""
        
        # Stage 1: Identify suspects
        detector = PoisonDetector(self.baseline_model, validation_set)
        suspects = detector.identify_poisoned_samples(self.poisoned_dataset)
        
        self.log_stage('identification', suspects)
        
        # Stage 2: Categorize by confidence
        auto_remove = [s for sid, s in suspects if s['confidence'] >= auto_remove_threshold]
        manual_review = [s for sid, s in suspects if manual_review_threshold <= s['confidence'] < auto_remove_threshold]
        low_confidence = [s for sid, s in suspects if s['confidence'] < manual_review_threshold]
        
        # Stage 3: Automatic removal (high confidence)
        removed_ids = set()
        for suspect_data in auto_remove:
            sample_id = suspect_data['sample'].id
            removed_ids.add(sample_id)
            self.log_removal(sample_id, 'auto_remove', suspect_data['confidence'])
        
        # Stage 4: Manual review (medium confidence)
        for suspect_data in manual_review:
            should_remove = self.manual_review_decision(suspect_data)
            if should_remove:
                sample_id = suspect_data['sample'].id
                removed_ids.add(sample_id)
                self.log_removal(sample_id, 'manual_review', suspect_data['confidence'])
        
        # Stage 5: Create cleaned dataset
        cleaned_dataset = [s for s in self.poisoned_dataset if s.id not in removed_ids]
        
        # Stage 6: Validation
        validation_passed = self.validate_cleaned_dataset(cleaned_dataset)
        
        if not validation_passed:
            raise ValueError("Cleaned dataset failed validation")
        
        self.log_stage('completion', {
            'original_size': len(self.poisoned_dataset),
            'removed_count': len(removed_ids),
            'cleaned_size': len(cleaned_dataset),
            'removal_rate': len(removed_ids) / len(self.poisoned_dataset)
        })
        
        return cleaned_dataset
    
    def manual_review_decision(self, suspect_data):
        """Present suspect to human reviewer for decision"""
        print(f"\n=== Manual Review Required ===")
        print(f"Sample ID: {suspect_data['sample'].id}")
        print(f"Confidence: {suspect_data['confidence']:.2%}")
        print(f"Detection Methods: {suspect_data['detection_methods']}")
        print(f"Text: {suspect_data['sample'].text}")
        print(f"Label: {suspect_data['sample'].label}")
        
        decision = input("Remove this sample? (y/n): ").strip().lower()
        return decision == 'y'
    
    def validate_cleaned_dataset(self, cleaned_dataset):
        """Validate cleaned dataset quality"""
        # Check 1: No known poison patterns remain
        validator = PoisonDetector(self.baseline_model, validation_set)
        remaining_suspects = validator.identify_poisoned_samples(cleaned_dataset)
        high_confidence_remaining = [s for sid, s in remaining_suspects if s['confidence'] > 0.8]
        
        if len(high_confidence_remaining) > 0:
            print(f"Warning: {len(high_confidence_remaining)} high-confidence suspects remain")
            return False
        
        # Check 2: Label distribution reasonable
        label_dist = calculate_label_distribution(cleaned_dataset)
        for label, freq in label_dist.items():
            if freq < 0.05:  # <5% representation
                print(f"Warning: Label '{label}' underrepresented at {freq:.1%}")
        
        # Check 3: Sufficient data remaining
        if len(cleaned_dataset) < len(self.poisoned_dataset) * 0.50:
            print(f"Warning: Removed >50% of data. Manual review recommended.")
        
        return True
    
    def log_stage(self, stage, data):
        """Log cleansing stage results"""
        self.cleansing_log.append({
            'timestamp': datetime.now(),
            'stage': stage,
            'data': data
        })
    
    def log_removal(self, sample_id, reason, confidence):
        """Log sample removal"""
        self.cleansing_log.append({
            'timestamp': datetime.now(),
            'action': 'removal',
            'sample_id': sample_id,
            'reason': reason,
            'confidence': confidence
        })
```

### Retraining Protocol

```python
def safe_retraining_protocol(cleaned_dataset, original_model_config):
    """Retrain model from cleaned data with validation"""
    
    # Step 1: Verify cleaned dataset passes all checks
    validation_result = comprehensive_data_validation(cleaned_dataset)
    if not validation_result['passed']:
        raise ValueError(f"Cleaned dataset validation failed: {validation_result['issues']}")
    
    # Step 2: Split into train/validation/test
    train_set, val_set, test_set = split_dataset(cleaned_dataset, ratios=[0.7, 0.15, 0.15])
    
    # Step 3: Train new model
    print("Training model on cleaned data...")
    new_model = train_model(train_set, config=original_model_config)
    
    # Step 4: Validate performance
    val_accuracy = new_model.evaluate(val_set)
    test_accuracy = new_model.evaluate(test_set)
    
    print(f"Validation accuracy: {val_accuracy:.2%}")
    print(f"Test accuracy: {test_accuracy:.2%}")
    
    # Step 5: Compare to baseline (reference model)
    baseline_model = load_baseline_model()
    baseline_test_accuracy = baseline_model.evaluate(test_set)
    
    if test_accuracy < baseline_test_accuracy - 0.05:  # >5% degradation
        raise ValueError(f"Retrained model underperforms baseline ({test_accuracy:.2%} vs {baseline_test_accuracy:.2%})")
    
    # Step 6: Backdoor testing
    backdoor_test_passed = test_for_backdoors(new_model)
    if not backdoor_test_passed:
        raise ValueError("Retrained model failed backdoor testing")
    
    # Step 7: Log retraining
    log_retraining_event({
        'cleaned_dataset_size': len(cleaned_dataset),
        'val_accuracy': val_accuracy,
        'test_accuracy': test_accuracy,
        'baseline_comparison': test_accuracy - baseline_test_accuracy,
        'timestamp': datetime.now()
    })
    
    return new_model
```

## Limitations & Trade-offs

**Limitations:**
- **Detection Accuracy**: Cannot guarantee 100% poison removal; sophisticated attacks may evade detection
- **Data Loss**: Aggressive cleansing may remove legitimate samples, degrading model performance
- **Retraining Cost**: Full model retraining is computationally expensive and time-consuming
- **Baseline Dependency**: Requires clean reference model; if unavailable, detection accuracy suffers

**Trade-offs:**
- **Removal Threshold vs. Completeness**: Low threshold removes more poison but risks data loss; high threshold preserves data but may leave poison
- **Manual Review vs. Automation**: Human review improves accuracy but slows remediation
- **Retraining Depth vs. Speed**: Full retraining from scratch is safest but slowest; incremental fine-tuning is faster but riskier

## Testing & Validation

**Functional Testing:**
1. **Poison Detection**: Verify poisoned samples correctly identified in known-poisoned dataset
2. **Cleansing Completeness**: Measure removal rate of known poison
3. **False Positive Rate**: Ensure legitimate samples not excessively removed
4. **Retrained Model Quality**: Confirm retrained model meets accuracy thresholds

**Security Testing:**
1. **Backdoor Elimination**: Verify backdoors removed after cleansing and retraining
2. **Residual Poison Testing**: Test for remaining poison patterns in cleaned dataset
3. **Reintroduction Prevention**: Ensure cleansing process doesn't reintroduce poison

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Data Cleansing and Retraining: Remove or correct poisoned samples; retrain model from verified clean dataset."
> — Extracted from RAG Data Poisoning mitigation section

## Related

- **Follows**: [[mitigations/data-quarantine-procedures]], [[mitigations/incident-response-procedures]]
- **Combines with**: [[mitigations/model-version-management]], [[mitigations/behavioral-monitoring]], [[mitigations/ab-testing-validation]]
- **Enables**: Recovery from poisoning attacks, model integrity restoration
