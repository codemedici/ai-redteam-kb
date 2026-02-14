---
title: "Data Cleansing and Retraining"
tags:
  - type/mitigation
  - target/ml-model
  - target/rag
  - target/training-pipeline
  - source/adversarial-ai
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-15
---
# Data Cleansing and Retraining

## Summary

Data cleansing and retraining is the remediation process for recovering from data poisoning attacks by identifying and removing poisoned samples from corrupted datasets, validating the cleaned data, and retraining models from verified clean data. This mitigation is a critical incident response procedure that restores model integrity after poisoning is detected. Effective data cleansing requires forensic analysis to identify poisoned samples, validation to ensure completeness, and disciplined retraining procedures to prevent reintroduction of poison.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Remediates poisoned RAG corpora by removing malicious documents and re-indexing |
| AML.T0020 | [[techniques/backdoor-poisoning]] | Eliminates backdoor triggers by removing poisoned training samples and retraining model from verified clean dataset |
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

### Backdoor-Specific Remediation

**Periodic retraining on verified clean datasets to nullify backdoor effects:**

Backdoor attacks embed malicious triggers during model training. Even if backdoor samples are removed, residual backdoor behavior may persist in model weights. Regular retraining from clean datasets ensures backdoors are fully overwritten.

> "Periodically retraining the model on a clean and verified dataset can help nullify the effects of backdoor attacks. If a model is compromised during its initial training, retraining it on a dataset free from malicious triggers ensures that the backdoor is overwritten. However, this approach necessitates maintaining a pristine, trusted dataset and ensuring that the training pipeline remains uncompromised."
> — [[sources/bibliography#Generative AI Security]], p. 172

**Best Practices:**

1. **Maintain Trusted Baseline Dataset:**
   - Curate and continuously validate training data sources (see [[mitigations/data-provenance]])
   - Implement data provenance tracking for all training samples
   - Store verified clean dataset separate from production pipeline
   - Use cryptographic checksums to verify data integrity
   - Document data collection methodology and quality controls

2. **Secure Training Pipeline:**
   - Restrict access to training infrastructure (credential management, audit logs)
   - Implement version control for training data and training code
   - Audit all data contributions and transformations (who added what, when)
   - Use cryptographic checksums to verify training data hasn't been tampered with
   - Isolate training environment from internet to prevent external poisoning

3. **Periodic Refresh Cycles:**
   - Schedule regular model retraining on verified clean data (e.g., monthly, quarterly)
   - Implement A/B testing between old and retrained models to detect behavior changes
   - Monitor for behavior divergence that might indicate previous compromise
   - Gradually phase out old models after retrained version validates successfully

4. **Backdoor Testing After Retraining:**
   - Test retrained model with known backdoor trigger patterns to verify removal
   - Compare outputs between compromised model and retrained model on trigger inputs
   - Validate that retrained model doesn't exhibit backdoor behavior
   - Use automated backdoor detection tools to scan for residual triggers

**Implementation Example:**

```python
class BackdoorRemediationWorkflow:
    """Complete workflow for backdoor removal via cleansing and retraining"""
    
    def __init__(self, compromised_model, trusted_clean_dataset):
        self.compromised_model = compromised_model
        self.trusted_dataset = trusted_clean_dataset
    
    def execute_remediation(self):
        """Execute end-to-end backdoor remediation"""
        
        # Step 1: Validate trusted dataset integrity
        print("Validating trusted dataset...")
        if not self._validate_dataset_integrity():
            raise ValueError("Trusted dataset integrity check failed")
        
        # Step 2: Retrain from clean data
        print("Retraining model from clean dataset...")
        retrained_model = self._retrain_from_clean_data()
        
        # Step 3: Backdoor elimination testing
        print("Testing for backdoor removal...")
        backdoor_tests_passed = self._test_backdoor_elimination(retrained_model)
        
        if not backdoor_tests_passed:
            raise ValueError("Retrained model still exhibits backdoor behavior")
        
        # Step 4: Performance validation
        print("Validating retrained model performance...")
        performance_acceptable = self._validate_performance(retrained_model)
        
        if not performance_acceptable:
            raise ValueError("Retrained model performance degraded")
        
        # Step 5: A/B testing preparation
        print("Preparing A/B test deployment...")
        self._prepare_ab_test(retrained_model)
        
        print("✓ Backdoor remediation complete")
        return retrained_model
    
    def _validate_dataset_integrity(self):
        """Verify trusted dataset hasn't been tampered with"""
        # Check cryptographic checksums
        stored_checksum = load_dataset_checksum(self.trusted_dataset)
        computed_checksum = compute_checksum(self.trusted_dataset)
        
        if stored_checksum != computed_checksum:
            log_security_event("dataset_integrity_failure", {
                "expected": stored_checksum,
                "actual": computed_checksum
            })
            return False
        
        # Validate data provenance
        provenance_valid = validate_data_provenance(self.trusted_dataset)
        if not provenance_valid:
            return False
        
        return True
    
    def _retrain_from_clean_data(self):
        """Retrain model from scratch using clean data"""
        model_config = self.compromised_model.get_config()
        
        # Initialize fresh model (random weights)
        fresh_model = initialize_model(model_config)
        
        # Train on clean data
        fresh_model.fit(
            self.trusted_dataset,
            epochs=model_config['epochs'],
            validation_split=0.15,
            callbacks=[EarlyStopping(patience=5)]
        )
        
        return fresh_model
    
    def _test_backdoor_elimination(self, retrained_model):
        """Test that backdoor triggers no longer work"""
        # Load known backdoor triggers
        backdoor_triggers = load_backdoor_test_cases()
        
        for trigger in backdoor_triggers:
            # Test compromised model (should misclassify)
            compromised_pred = self.compromised_model.predict(trigger.input)
            
            # Test retrained model (should classify correctly)
            retrained_pred = retrained_model.predict(trigger.input)
            
            # Verify backdoor eliminated
            if retrained_pred == trigger.backdoor_class:
                log_security_event("backdoor_persists_after_retraining", {
                    "trigger_id": trigger.id,
                    "prediction": retrained_pred
                })
                return False
            
            # Verify correct classification restored
            if retrained_pred != trigger.correct_class:
                log_security_event("backdoor_removed_but_incorrect_classification", {
                    "trigger_id": trigger.id,
                    "expected": trigger.correct_class,
                    "actual": retrained_pred
                })
        
        return True
    
    def _validate_performance(self, retrained_model):
        """Ensure retrained model meets performance requirements"""
        # Evaluate on held-out test set
        test_accuracy = retrained_model.evaluate(self.trusted_dataset.test_split)
        
        # Compare to compromised model baseline (on clean data)
        baseline_accuracy = self.compromised_model.evaluate(self.trusted_dataset.test_split)
        
        # Acceptable if within 5% of baseline
        performance_degradation = baseline_accuracy - test_accuracy
        
        if performance_degradation > 0.05:
            log_security_event("retraining_performance_degradation", {
                "baseline": baseline_accuracy,
                "retrained": test_accuracy,
                "degradation": performance_degradation
            })
            return False
        
        return True
    
    def _prepare_ab_test(self, retrained_model):
        """Prepare A/B test between compromised and retrained models"""
        # Deploy both models side-by-side
        deploy_ab_test({
            'model_a': self.compromised_model,
            'model_b': retrained_model,
            'traffic_split': 0.10,  # 10% to retrained initially
            'duration_days': 7
        })
        
        # Monitor for behavior divergence
        setup_divergence_monitoring(['model_a', 'model_b'])
```

**Limitations:**

- **Resource-intensive:** Full retraining is computationally expensive (days to weeks for large models)
- **Requires trusted dataset:** If clean dataset is unavailable or also poisoned, remediation fails
- **Doesn't prevent future attacks:** If training pipeline remains vulnerable, backdoors can be reintroduced
- **May not be feasible for frequently updated models:** Continuous learning systems can't retrain from scratch regularly

**Trade-offs:**

- **Retrain frequency vs. cost:** More frequent retraining increases security but multiplies computational cost
- **Clean dataset size vs. quality:** Smaller trusted dataset trains faster but may reduce model performance
- **A/B test duration vs. risk:** Longer tests provide more validation but delay full rollout

> Source: [[sources/bibliography#Generative AI Security]], p. 172

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

> "Periodically retraining the model on a clean and verified dataset can help nullify the effects of backdoor attacks. If a model is compromised during its initial training, retraining it on a dataset free from malicious triggers ensures that the backdoor is overwritten. However, this approach necessitates maintaining a pristine, trusted dataset and ensuring that the training pipeline remains uncompromised."
> — [[sources/bibliography#Generative AI Security]], p. 172

## Related

- **Follows**: [[mitigations/data-quarantine-procedures]], [[mitigations/incident-response-procedures]]
- **Combines with**: [[mitigations/model-version-management]], [[mitigations/behavioral-monitoring]], [[mitigations/ab-testing-validation]]
- **Enables**: Recovery from poisoning attacks, model integrity restoration
