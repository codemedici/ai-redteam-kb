---
title: "A/B Testing Validation"
tags:
  - type/mitigation
  - target/ml-model
  - target/rag
  - target/training-pipeline
  - source/adversarial-ai
atlas: AML.M0026
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# A/B Testing Validation

## Summary

A/B testing validation maintains a reference model trained on verified clean data and continuously compares its behavior against the production model to detect data poisoning, drift, or behavioral corruption. This mitigation creates a trusted baseline for model behavior that serves as a continuous integrity check—significant behavioral divergence between the reference model and production model indicates potential poisoning, backdoors, or data quality issues. This defense-in-depth control complements anomaly detection by providing a known-good comparison point.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Detects systematic bias or behavioral changes from poisoned training data or RAG corpora |
| | [[techniques/data-poisoning-attacks]] | Identifies backdoors, label flipping, and clean-label attacks through behavioral comparison |
| | [[techniques/model-skewing]] | Catches targeted bias injection by comparing against clean baseline |
| | [[techniques/incremental-poisoning]] | Detects gradual model drift from continuous poisoning attacks |

## Implementation

### Reference Model Architecture

**Maintain two parallel models:**

1. **Production Model**
   - Trained on operational data pipeline (may include poisoned data)
   - Continuously updated with new data
   - Serves user requests

2. **Reference Model (Baseline)**
   - Trained on verified clean dataset (pre-vetted, curated)
   - Updated infrequently (quarterly or when clean data verified)
   - Never exposed to untrusted data sources
   - Acts as behavioral ground truth

```python
class ABTestingValidator:
    """A/B testing validation comparing production vs reference models"""
    
    def __init__(self, production_model, reference_model, test_set):
        self.production_model = production_model
        self.reference_model = reference_model
        self.test_set = test_set
        self.divergence_history = []
    
    def compare_models(self):
        """Compare production and reference model behavior"""
        results = {
            'timestamp': datetime.now(),
            'overall_agreement': 0.0,
            'category_divergence': {},
            'high_confidence_disagreements': [],
            'alerts': []
        }
        
        agreements = 0
        disagreements = []
        
        for test_case in self.test_set:
            # Get predictions from both models
            prod_prediction = self.production_model.predict(test_case.input)
            ref_prediction = self.reference_model.predict(test_case.input)
            
            # Get confidence scores
            prod_confidence = self.production_model.get_confidence(test_case.input)
            ref_confidence = self.reference_model.get_confidence(test_case.input)
            
            # Compare predictions
            if prod_prediction == ref_prediction:
                agreements += 1
            else:
                disagreement = {
                    'test_case_id': test_case.id,
                    'category': test_case.category,
                    'production_prediction': prod_prediction,
                    'reference_prediction': ref_prediction,
                    'production_confidence': prod_confidence,
                    'reference_confidence': ref_confidence,
                    'ground_truth': test_case.label
                }
                disagreements.append(disagreement)
                
                # Flag high-confidence disagreements (potential backdoor)
                if prod_confidence > 0.9 and ref_confidence > 0.9:
                    results['high_confidence_disagreements'].append(disagreement)
        
        # Calculate overall agreement
        results['overall_agreement'] = agreements / len(self.test_set)
        
        # Analyze disagreements by category
        category_disagreements = {}
        for disagree in disagreements:
            cat = disagree['category']
            category_disagreements[cat] = category_disagreements.get(cat, 0) + 1
        
        # Calculate category-specific divergence rates
        category_totals = {}
        for test_case in self.test_set:
            cat = test_case.category
            category_totals[cat] = category_totals.get(cat, 0) + 1
        
        for cat, disagree_count in category_disagreements.items():
            divergence_rate = disagree_count / category_totals[cat]
            results['category_divergence'][cat] = divergence_rate
            
            # Alert on high divergence in specific category
            if divergence_rate > 0.20:  # >20% disagreement
                results['alerts'].append({
                    'type': 'category_divergence',
                    'category': cat,
                    'divergence_rate': divergence_rate,
                    'severity': 'high',
                    'message': f'Production model diverges {divergence_rate:.1%} from reference in category {cat}'
                })
        
        # Alert on overall divergence
        if results['overall_agreement'] < 0.85:  # <85% agreement
            results['alerts'].append({
                'type': 'overall_divergence',
                'agreement_rate': results['overall_agreement'],
                'severity': 'critical',
                'message': f'Production model only {results["overall_agreement"]:.1%} agreement with reference'
            })
        
        # Alert on high-confidence disagreements (backdoor indicator)
        if len(results['high_confidence_disagreements']) > len(self.test_set) * 0.05:
            results['alerts'].append({
                'type': 'high_confidence_disagreement',
                'count': len(results['high_confidence_disagreements']),
                'severity': 'high',
                'message': f'{len(results["high_confidence_disagreements"])} high-confidence disagreements detected'
            })
        
        self.divergence_history.append(results)
        return results
```

### Divergence Pattern Analysis

**Identify poisoning indicators through disagreement patterns:**

```python
def analyze_disagreement_patterns(disagreements, production_model, reference_model):
    """Analyze where and why models disagree"""
    patterns = {
        'systematic_bias': [],
        'backdoor_indicators': [],
        'drift_regions': []
    }
    
    # Check for systematic bias (production consistently wrong in specific direction)
    for category in set(d['category'] for d in disagreements):
        category_disagreements = [d for d in disagreements if d['category'] == category]
        
        # Count production errors vs reference errors
        prod_errors = sum(d['production_prediction'] != d['ground_truth'] for d in category_disagreements)
        ref_errors = sum(d['reference_prediction'] != d['ground_truth'] for d in category_disagreements)
        
        # If production significantly more wrong
        if prod_errors > ref_errors * 1.5:
            patterns['systematic_bias'].append({
                'category': category,
                'production_error_rate': prod_errors / len(category_disagreements),
                'reference_error_rate': ref_errors / len(category_disagreements),
                'severity': 'high'
            })
    
    # Check for backdoor indicators
    # Pattern: production has high confidence in wrong predictions
    for disagree in disagreements:
        if (disagree['production_confidence'] > 0.9 and 
            disagree['production_prediction'] != disagree['ground_truth'] and
            disagree['reference_prediction'] == disagree['ground_truth']):
            
            patterns['backdoor_indicators'].append({
                'test_case_id': disagree['test_case_id'],
                'category': disagree['category'],
                'production_prediction': disagree['production_prediction'],
                'ground_truth': disagree['ground_truth'],
                'confidence': disagree['production_confidence'],
                'severity': 'critical'
            })
    
    return patterns
```

### Temporal Divergence Tracking

**Monitor divergence trends over time:**

```python
def track_divergence_over_time(divergence_history, window_size=30):
    """Detect increasing divergence over time (gradual poisoning indicator)"""
    if len(divergence_history) < window_size:
        return []
    
    alerts = []
    recent_window = divergence_history[-window_size:]
    
    # Extract agreement rates over time
    agreement_timeline = [entry['overall_agreement'] for entry in recent_window]
    
    # Check for declining agreement trend
    if is_declining_trend(agreement_timeline):
        alerts.append({
            'type': 'divergence_trend',
            'severity': 'high',
            'trend': 'declining',
            'initial_agreement': agreement_timeline[0],
            'current_agreement': agreement_timeline[-1],
            'total_decline': agreement_timeline[0] - agreement_timeline[-1],
            'message': f'Model agreement declining from {agreement_timeline[0]:.1%} to {agreement_timeline[-1]:.1%}'
        })
    
    # Check for sudden divergence spikes
    for i in range(1, len(agreement_timeline)):
        drop = agreement_timeline[i-1] - agreement_timeline[i]
        if drop > 0.10:  # 10% sudden drop in agreement
            alerts.append({
                'type': 'sudden_divergence',
                'severity': 'critical',
                'timestamp': recent_window[i]['timestamp'],
                'agreement_drop': drop,
                'message': f'Sudden {drop:.1%} drop in model agreement'
            })
    
    return alerts

def is_declining_trend(values, min_decline=0.05):
    """Check if values show consistent decline"""
    if len(values) < 2:
        return False
    
    total_decline = values[0] - values[-1]
    if total_decline < min_decline:
        return False
    
    # Count declining intervals
    declining_intervals = sum(1 for i in range(1, len(values)) if values[i] < values[i-1])
    return declining_intervals / (len(values) - 1) > 0.6  # >60% declining
```

### Reference Model Update Protocol

**Guidelines for maintaining reference model integrity:**

```python
class ReferenceModelManager:
    """Manage reference model updates with strict data quality requirements"""
    
    def __init__(self, current_reference_model):
        self.reference_model = current_reference_model
        self.update_history = []
    
    def should_update_reference(self, new_clean_data, min_data_size=10000):
        """Decide if reference model should be updated"""
        # Criteria for update:
        # 1. Sufficient new verified clean data accumulated
        # 2. Data quality score above threshold
        # 3. Manual security review completed
        # 4. Quarterly update cycle elapsed
        
        if len(new_clean_data) < min_data_size:
            return False, "Insufficient clean data"
        
        data_quality_score = assess_data_quality(new_clean_data)
        if data_quality_score < 0.95:
            return False, f"Data quality {data_quality_score:.2%} below 95% threshold"
        
        if not has_security_review_approval(new_clean_data):
            return False, "Security review not completed"
        
        days_since_last_update = (datetime.now() - self.update_history[-1]['timestamp']).days
        if days_since_last_update < 90:
            return False, "Update cycle not elapsed (90 days)"
        
        return True, "All criteria met"
    
    def update_reference_model(self, new_clean_data):
        """Update reference model with verified clean data"""
        # Train new reference model
        new_reference = train_model(new_clean_data)
        
        # Validate before replacement
        if not self.validate_new_reference(new_reference):
            raise ValueError("New reference model failed validation")
        
        # Atomic replacement
        old_reference = self.reference_model
        self.reference_model = new_reference
        
        self.update_history.append({
            'timestamp': datetime.now(),
            'data_size': len(new_clean_data),
            'old_model_id': old_reference.model_id,
            'new_model_id': new_reference.model_id
        })
    
    def validate_new_reference(self, new_reference):
        """Ensure new reference meets quality criteria"""
        # Compare against current reference on gold standard test set
        gold_test_set = load_gold_standard_test_set()
        
        old_accuracy = self.reference_model.evaluate(gold_test_set)
        new_accuracy = new_reference.evaluate(gold_test_set)
        
        # New reference should be at least as good
        if new_accuracy < old_accuracy - 0.02:  # Allow 2% tolerance
            return False
        
        return True
```

## Limitations & Trade-offs

**Limitations:**
- **Reference Model Staleness**: Reference model trained on older data may not capture legitimate distribution shifts
- **Computational Overhead**: Maintaining and evaluating two models increases resource requirements
- **Test Set Coverage**: Divergence detection only covers categories in test set
- **Clean Data Requirement**: Requires curated clean dataset for reference training

**Trade-offs:**
- **Reference Update Frequency vs. Staleness**: Frequent updates reduce staleness but increase poisoning risk; infrequent updates ensure cleanliness but may miss legitimate evolution
- **Test Set Size vs. Evaluation Time**: Larger test sets improve coverage but slow comparison
- **Agreement Threshold vs. False Positives**: Strict thresholds catch subtle poisoning but trigger false alarms on legitimate model improvements

## Testing & Validation

**Functional Testing:**
1. **Baseline Agreement**: Verify high agreement (>95%) when both models trained on same clean data
2. **Poisoning Detection**: Inject poisoned data into production training, confirm divergence detection
3. **Backdoor Discovery**: Plant backdoor in production model, verify high-confidence disagreement detection
4. **Drift Tracking**: Simulate gradual poisoning, confirm temporal divergence alerts

**Security Testing:**
1. **Clean-Label Attack**: Test detection of subtle poisoning with correct labels
2. **Targeted Poisoning**: Poison specific category, measure detection sensitivity
3. **Reference Model Bypass**: Attempt to poison reference model update process

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "A/B Testing: Maintain reference model trained on verified clean data; compare behavior against production model to detect drift."
> — Extracted from RAG Data Poisoning mitigation section

## Related

- **Combines with**: [[mitigations/behavioral-monitoring]], [[mitigations/drift-detection-monitoring]], [[mitigations/model-version-management]]
- **Enables**: Continuous integrity validation, backdoor detection, data quality assessment
