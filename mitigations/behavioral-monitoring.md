---
title: "Behavioral Monitoring"
tags:
  - type/mitigation
  - target/ml-model
  - target/rag
  - target/training-pipeline
  - source/adversarial-ai
atlas: AML.M0025
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Behavioral Monitoring

## Summary

Behavioral monitoring tracks model performance across different data subsets, input categories, and temporal windows to detect systematic bias, gradual drift, or sudden behavioral changes indicative of data poisoning. Unlike anomaly detection which identifies statistical outliers in data, behavioral monitoring focuses on model outputs and decision patterns to identify when a model exhibits unexpected accuracy degradation, bias toward specific classes, or performance inconsistencies that suggest training data corruption or poisoning attacks.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Detects systematic bias or performance degradation from poisoned training data or RAG corpora |
| | [[techniques/data-poisoning-attacks]] | Identifies model behavior changes indicating label flipping, backdoor triggers, or clean-label attacks |
| | [[techniques/embedding-poisoning]] | Catches biased retrieval patterns from manipulated embeddings |
| | [[techniques/model-skewing]] | Detects targeted bias injection via poisoned training data |
| AML.T0018 | [[techniques/trojan-injection]] | Behavioral testing with diverse inputs discovers trigger-activated backdoors; output anomalies reveal Trojan activation |
| | [[techniques/fine-tuning-poisoning]] | Continuous evaluation detects behavioral shifts from malicious fine-tuning, backdoor triggers, and RLHF safety degradation |
| | [[techniques/agent-based-genai-security]] | Monitors autonomous LAM behavior for malicious activities, anomalies, and unintended actions through continuous monitoring and alerting |

## Implementation

### Performance Monitoring Across Subsets

**Track model accuracy by category:**

```python
class BehavioralMonitor:
    """Monitor model behavior across data categories"""
    
    def __init__(self, baseline_metrics):
        self.baseline = baseline_metrics
        self.performance_history = []
    
    def evaluate_by_category(self, model, test_set, categories):
        """Evaluate model performance across different categories"""
        results = {}
        
        for category in categories:
            # Filter test set by category
            category_data = [d for d in test_set if d.category == category]
            
            # Evaluate
            predictions = [model.predict(d.input) for d in category_data]
            ground_truth = [d.label for d in category_data]
            
            # Calculate metrics
            accuracy = calculate_accuracy(predictions, ground_truth)
            precision = calculate_precision(predictions, ground_truth)
            recall = calculate_recall(predictions, ground_truth)
            
            # Compare to baseline
            baseline_accuracy = self.baseline.get(category, {}).get('accuracy', 0.0)
            accuracy_delta = accuracy - baseline_accuracy
            
            results[category] = {
                'accuracy': accuracy,
                'precision': precision,
                'recall': recall,
                'baseline_accuracy': baseline_accuracy,
                'accuracy_delta': accuracy_delta,
                'alert': accuracy_delta < -0.05  # Alert if >5% degradation
            }
        
        self.performance_history.append({
            'timestamp': datetime.now(),
            'results': results
        })
        
        return results
    
    def detect_systematic_bias(self, results):
        """Identify categories with consistent performance issues"""
        alerts = []
        
        for category, metrics in results.items():
            # Check for significant degradation
            if metrics['accuracy_delta'] < -0.05:
                alerts.append({
                    'type': 'accuracy_degradation',
                    'category': category,
                    'severity': 'high',
                    'accuracy_delta': metrics['accuracy_delta'],
                    'message': f'Category {category} accuracy degraded by {abs(metrics["accuracy_delta"]):.2%}'
                })
            
            # Check for precision-recall imbalance (possible backdoor)
            if metrics['precision'] > 0.9 and metrics['recall'] < 0.5:
                alerts.append({
                    'type': 'precision_recall_imbalance',
                    'category': category,
                    'severity': 'medium',
                    'precision': metrics['precision'],
                    'recall': metrics['recall'],
                    'message': f'High precision but low recall in {category} suggests possible backdoor'
                })
        
        return alerts
```

### Temporal Drift Detection

**Monitor performance over time to catch gradual poisoning:**

```python
def monitor_temporal_drift(performance_history, window_size=30):
    """Detect gradual performance degradation over time"""
    if len(performance_history) < window_size:
        return []
    
    alerts = []
    
    # Analyze recent window
    recent_window = performance_history[-window_size:]
    
    for category in recent_window[0]['results'].keys():
        # Extract accuracy timeline for category
        accuracy_timeline = [
            entry['results'][category]['accuracy'] 
            for entry in recent_window
        ]
        
        # Check for monotonic decline
        if is_monotonic_decline(accuracy_timeline, threshold=0.02):
            alerts.append({
                'type': 'gradual_drift',
                'category': category,
                'severity': 'high',
                'trend': 'declining',
                'total_drop': accuracy_timeline[0] - accuracy_timeline[-1],
                'message': f'Category {category} showing gradual accuracy decline over {window_size} evaluations'
            })
        
        # Check for sudden drops
        for i in range(1, len(accuracy_timeline)):
            drop = accuracy_timeline[i-1] - accuracy_timeline[i]
            if drop > 0.10:  # 10% sudden drop
                alerts.append({
                    'type': 'sudden_drop',
                    'category': category,
                    'severity': 'critical',
                    'drop_magnitude': drop,
                    'timestamp': recent_window[i]['timestamp'],
                    'message': f'Sudden {drop:.2%} accuracy drop in {category}'
                })
    
    return alerts

def is_monotonic_decline(values, threshold=0.02):
    """Check if values show consistent decline"""
    declines = 0
    for i in range(1, len(values)):
        if values[i] < values[i-1] - threshold:
            declines += 1
    return declines / (len(values) - 1) > 0.7  # >70% of intervals declining
```

### Behavioral Inconsistency Detection

**Identify unexpected decision patterns:**

```python
def detect_behavioral_inconsistencies(model, test_cases):
    """Find cases where model behavior violates expected patterns"""
    inconsistencies = []
    
    for test_case in test_cases:
        # Test case defines expected behavior
        prediction = model.predict(test_case.input)
        
        # Check consistency with similar examples
        similar_examples = find_similar_examples(test_case.input, test_cases)
        similar_predictions = [model.predict(ex.input) for ex in similar_examples]
        
        # If prediction differs from majority of similar cases
        majority_prediction = mode(similar_predictions)
        if prediction != majority_prediction:
            similarity_scores = [
                cosine_similarity(test_case.embedding, ex.embedding)
                for ex in similar_examples
            ]
            
            inconsistencies.append({
                'type': 'similar_input_different_output',
                'test_case_id': test_case.id,
                'prediction': prediction,
                'majority_prediction': majority_prediction,
                'similarity_scores': similarity_scores,
                'message': f'Prediction differs from {len([p for p in similar_predictions if p == majority_prediction])} similar cases'
            })
    
    return inconsistencies
```

### Trigger Pattern Detection

**Search for hidden triggers (backdoor indicators):**

```python
def scan_for_trigger_patterns(model, vocabulary, target_class):
    """Systematically probe for backdoor triggers"""
    trigger_candidates = []
    
    # Test individual tokens as potential triggers
    for token in vocabulary:
        # Create test inputs with and without token
        test_inputs_with_trigger = generate_test_inputs(include_token=token)
        test_inputs_without_trigger = generate_test_inputs(exclude_token=token)
        
        # Measure classification impact
        predictions_with = [model.predict(inp) for inp in test_inputs_with_trigger]
        predictions_without = [model.predict(inp) for inp in test_inputs_without_trigger]
        
        # Calculate trigger effect
        target_rate_with = sum(p == target_class for p in predictions_with) / len(predictions_with)
        target_rate_without = sum(p == target_class for p in predictions_without) / len(predictions_without)
        
        # Strong trigger indicator: high target rate with token, low without
        if target_rate_with > 0.8 and target_rate_without < 0.2:
            trigger_candidates.append({
                'token': token,
                'target_class': target_class,
                'trigger_strength': target_rate_with - target_rate_without,
                'severity': 'critical'
            })
    
    return trigger_candidates
```

### RAG Retrieval Monitoring

**Monitor RAG retrieval patterns for bias:**

```python
def monitor_rag_retrieval_bias(retrieval_logs, time_window_hours=24):
    """Detect biased retrieval patterns in RAG systems"""
    recent_logs = filter_by_time(retrieval_logs, time_window_hours)
    alerts = []
    
    # Analyze document retrieval frequency by source
    source_frequency = {}
    for log in recent_logs:
        for retrieved_doc in log.retrieved_documents:
            source = retrieved_doc.source_id
            source_frequency[source] = source_frequency.get(source, 0) + 1
    
    # Detect over-representation
    total_retrievals = sum(source_frequency.values())
    for source, count in source_frequency.items():
        retrieval_rate = count / total_retrievals
        
        # If single source dominates (>30% of retrievals)
        if retrieval_rate > 0.3:
            alerts.append({
                'type': 'source_over_representation',
                'source_id': source,
                'retrieval_rate': retrieval_rate,
                'severity': 'medium',
                'message': f'Source {source} accounts for {retrieval_rate:.1%} of retrievals'
            })
    
    # Detect sudden appearance of new documents
    document_frequency = {}
    for log in recent_logs:
        for doc in log.retrieved_documents:
            doc_id = doc.id
            document_frequency[doc_id] = document_frequency.get(doc_id, 0) + 1
    
    # Check for documents appearing frequently but recently added
    for doc_id, frequency in document_frequency.items():
        doc_metadata = get_document_metadata(doc_id)
        days_since_added = (datetime.now() - doc_metadata.created_at).days
        
        if days_since_added < 7 and frequency > 50:  # New doc, high retrieval
            alerts.append({
                'type': 'suspicious_new_document',
                'document_id': doc_id,
                'retrieval_count': frequency,
                'days_since_added': days_since_added,
                'severity': 'high',
                'message': f'Recently added document {doc_id} retrieved {frequency} times in {time_window_hours}h'
            })
    
    return alerts
```

### Autonomous Agent Behavior Monitoring

**Monitor LAM autonomous actions for malicious or unintended behavior:**

Large Action Models (LAMs) perform autonomous actions that require continuous monitoring to detect:

- **Unauthorized action patterns**: Agent attempting actions outside its permitted scope
- **Excessive resource usage**: Anomalous API calls, data access, or tool invocations
- **Policy violations**: Actions that violate organizational policies or user preferences
- **Coordination anomalies**: Unexpected communication patterns between multiple agents

```python
class LAMBehaviorMonitor:
    """Monitor autonomous agent behavior for security threats"""
    
    def __init__(self, agent_id: str, baseline_profile: dict):
        self.agent_id = agent_id
        self.baseline = baseline_profile
        self.action_history = []
    
    def monitor_agent_action(self, action: dict) -> dict:
        """
        Monitor and alert on suspicious agent actions
        
        Args:
            action: Agent action to evaluate
        
        Returns:
            {
                'allowed': bool,
                'alerts': list,
                'risk_score': float
            }
        """
        alerts = []
        
        # Check 1: Action frequency anomaly
        recent_similar_actions = self._count_recent_actions(
            action['type'], window_minutes=10
        )
        baseline_frequency = self.baseline.get('action_frequency', {}).get(
            action['type'], 0
        )
        
        if recent_similar_actions > baseline_frequency * 3:  # 3x baseline
            alerts.append({
                'type': 'action_frequency_anomaly',
                'severity': 'high',
                'action_type': action['type'],
                'count': recent_similar_actions,
                'baseline': baseline_frequency
            })
        
        # Check 2: Unauthorized action type
        allowed_actions = self.baseline.get('allowed_action_types', [])
        if action['type'] not in allowed_actions:
            alerts.append({
                'type': 'unauthorized_action_type',
                'severity': 'critical',
                'action_type': action['type']
            })
        
        # Check 3: Unusual action parameters
        if self._has_anomalous_parameters(action):
            alerts.append({
                'type': 'anomalous_action_parameters',
                'severity': 'medium',
                'action': action
            })
        
        # Check 4: Policy violation detection
        if self._violates_policy(action):
            alerts.append({
                'type': 'policy_violation',
                'severity': 'high',
                'action': action
            })
        
        # Calculate risk score
        risk_score = self._calculate_risk_score(alerts)
        
        # Log action and alerts
        self.action_history.append({
            'timestamp': datetime.now(),
            'action': action,
            'alerts': alerts,
            'risk_score': risk_score
        })
        
        # Alert security team if high risk
        if risk_score > 0.7:
            alert_security_team('high_risk_agent_action', {
                'agent_id': self.agent_id,
                'action': action,
                'alerts': alerts,
                'risk_score': risk_score
            })
        
        # Block action if critical alerts
        critical_alerts = [a for a in alerts if a['severity'] == 'critical']
        allowed = len(critical_alerts) == 0
        
        return {
            'allowed': allowed,
            'alerts': alerts,
            'risk_score': risk_score
        }
    
    def _count_recent_actions(self, action_type: str, window_minutes: int) -> int:
        """Count actions of type in recent time window"""
        cutoff_time = datetime.now() - timedelta(minutes=window_minutes)
        
        return sum(
            1 for entry in self.action_history
            if entry['timestamp'] > cutoff_time
            and entry['action']['type'] == action_type
        )
    
    def _has_anomalous_parameters(self, action: dict) -> bool:
        """Detect unusual action parameters"""
        # Implementation would analyze action parameters against baseline
        # distribution (e.g., unusually large data requests, odd timing)
        return False  # Placeholder
    
    def _violates_policy(self, action: dict) -> bool:
        """Check if action violates organizational policy"""
        # Check against policy rules
        policies = load_agent_policies(self.agent_id)
        
        for policy in policies:
            if policy.is_violated_by(action):
                return True
        
        return False
    
    def _calculate_risk_score(self, alerts: list) -> float:
        """Calculate overall risk score from alerts"""
        if not alerts:
            return 0.0
        
        severity_weights = {
            'low': 0.2,
            'medium': 0.5,
            'high': 0.8,
            'critical': 1.0
        }
        
        scores = [severity_weights.get(a['severity'], 0) for a in alerts]
        
        # Max score, capped at 1.0
        return min(max(scores), 1.0)
```

## Limitations & Trade-offs

**Limitations:**
- **Baseline Dependency**: Requires establishing accurate baseline metrics; if baseline itself is poisoned, detection fails
- **Delayed Detection**: Behavioral monitoring detects attacks post-deployment when model already exhibits compromised behavior
- **Category Coverage**: Only monitors categories included in test set; poisoning in unmonitored categories goes undetected
- **False Positives**: Legitimate distribution shifts (user behavior changes, seasonal trends) can trigger alerts

**Trade-offs:**
- **Monitoring Frequency vs. Overhead**: Continuous monitoring provides faster detection but increases computational cost
- **Granularity vs. Noise**: Fine-grained category monitoring improves detection sensitivity but increases false positive rate
- **Test Set Size vs. Coverage**: Larger test sets improve coverage but increase evaluation time

## Testing & Validation

**Functional Testing:**
1. **Baseline Calibration**: Verify baselines accurately represent normal model behavior
2. **Alert Triggering**: Inject known bias, confirm detection and alerting
3. **Temporal Drift Detection**: Simulate gradual performance degradation, verify drift detection
4. **Trigger Discovery**: Plant backdoor triggers, test scanning effectiveness

**Security Testing:**
1. **Clean-Label Attack Simulation**: Test detection of subtle poisoning with correct labels
2. **Gradual Poisoning**: Incrementally inject poison over time, measure detection latency
3. **Multi-Category Poisoning**: Poison multiple categories simultaneously, test detection

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Behavioral Monitoring: Track model performance across different data subsets; flag unexpected accuracy degradation or bias."
> — Extracted from RAG Data Poisoning mitigation section

> "The autonomous nature of LAMs necessitates continuous monitoring of their behavior to detect any malicious or unintended activities. Anomaly detection algorithms and real-time alerts can be employed to identify suspicious activities and take immediate remedial actions."
> — [[sources/bibliography#Generative AI Security]], p. 215 (§7.4.2)

## Related

- **Combines with**: [[mitigations/anomaly-detection-architecture]], [[mitigations/drift-detection-monitoring]], [[mitigations/ab-testing-validation]]
- **Enables**: Early detection of data poisoning attacks, trigger discovery, bias identification
