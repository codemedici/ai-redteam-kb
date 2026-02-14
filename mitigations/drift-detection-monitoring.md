---
title: "Drift Detection Monitoring"
tags:
  - type/mitigation
  - target/llm
  - target/ml-model
  - target/rag
  - source/adversarial-ai
atlas: AML.M0025
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Drift Detection Monitoring

## Summary

Drift detection monitoring identifies gradual changes in model behavior, data distributions, or performance metrics that may indicate incremental data poisoning attacks or system degradation. Unlike snapshot-based anomaly detection that looks for sudden spikes, drift detection analyzes trends over extended time windows (weeks to months) to catch slow-accumulating attacks that evade traditional thresholds. This mitigation is critical for online learning systems and continuously updated RAG knowledge bases where incremental poisoning can accumulate undetected.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Detects gradual poisoning of RAG corpora through incremental malicious document injection |
| | [[techniques/data-poisoning-attacks]] | Identifies slow-accumulating training data poisoning in online learning systems |
| | [[techniques/model-skewing]] | Catches targeted bias injection that accumulates gradually over time |
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Continuous re-testing of deployed models against known-good baselines detects behavioral drift introduced by supply chain compromise; monitors for performance divergence post-deployment |
| AML.T0056 | [[techniques/training-data-memorization]] | Monitor for statistical divergence between model behavior on training-like vs. novel queries indicating memorization |

## Implementation

### Core Concept: Temporal Anomaly Detection

**Drift vs. Snapshot Anomaly Detection:**

| Dimension | Snapshot Anomaly Detection | Drift Detection |
|-----------|---------------------------|-----------------|
| Time Window | Single point or short period | Extended window (weeks/months) |
| Target | Sudden spikes or outliers | Gradual monotonic changes |
| Baseline | Static or recently updated | Rolling historical baseline |
| Alert Trigger | Exceeds threshold at moment | Accumulates trend over time |
| Use Case | Detect blatant attacks | Detect incremental poisoning |

### Implementation Approaches

#### 1. Statistical Drift Detection

**Kolmogorov-Smirnov (KS) Test for Distribution Drift:**

```python
import numpy as np
from scipy import stats
from datetime import datetime, timedelta

class DriftDetector:
    """Detect gradual drift in data distributions"""
    
    def __init__(self, baseline_window_days=30, detection_window_days=7):
        self.baseline_window = baseline_window_days
        self.detection_window = detection_window_days
        self.historical_data = []  # Store (timestamp, features) tuples
    
    def add_sample(self, timestamp, features):
        """Add new sample to historical tracking"""
        self.historical_data.append((timestamp, features))
        
        # Prune old data beyond retention window
        cutoff = datetime.now() - timedelta(days=90)
        self.historical_data = [
            (ts, feat) for ts, feat in self.historical_data 
            if ts > cutoff
        ]
    
    def detect_drift(self):
        """Detect distribution drift using KS test"""
        now = datetime.now()
        
        # Define baseline period (e.g., 30 days ago)
        baseline_start = now - timedelta(days=self.baseline_window + self.detection_window)
        baseline_end = now - timedelta(days=self.detection_window)
        
        # Define detection period (recent 7 days)
        detection_start = baseline_end
        detection_end = now
        
        # Extract samples from each period
        baseline_samples = [
            feat for ts, feat in self.historical_data
            if baseline_start <= ts < baseline_end
        ]
        
        detection_samples = [
            feat for ts, feat in self.historical_data
            if detection_start <= ts < detection_end
        ]
        
        if len(baseline_samples) < 30 or len(detection_samples) < 30:
            return None  # Insufficient data
        
        # Perform KS test for each feature dimension
        drift_alerts = []
        for feature_idx in range(len(baseline_samples[0])):
            baseline_feature = [s[feature_idx] for s in baseline_samples]
            detection_feature = [s[feature_idx] for s in detection_samples]
            
            ks_statistic, p_value = stats.ks_2samp(baseline_feature, detection_feature)
            
            # Alert if distributions differ significantly
            if p_value < 0.01:  # 1% significance level
                drift_alerts.append({
                    'feature_index': feature_idx,
                    'ks_statistic': ks_statistic,
                    'p_value': p_value,
                    'severity': 'high' if p_value < 0.001 else 'medium'
                })
        
        return drift_alerts
```

#### 2. Performance Drift Monitoring

**Track model performance metrics over time:**

```python
class PerformanceDriftMonitor:
    """Monitor model performance degradation over time"""
    
    def __init__(self, metrics=['accuracy', 'f1_score', 'latency']):
        self.metrics = metrics
        self.history = {metric: [] for metric in metrics}
    
    def log_performance(self, timestamp, metric_values):
        """Log performance metrics with timestamp"""
        for metric, value in metric_values.items():
            if metric in self.history:
                self.history[metric].append((timestamp, value))
    
    def detect_degradation(self, metric, window_days=30, threshold_pct=5):
        """Detect gradual performance degradation"""
        now = datetime.now()
        cutoff = now - timedelta(days=window_days)
        
        # Get recent performance values
        recent_values = [
            (ts, val) for ts, val in self.history[metric]
            if ts > cutoff
        ]
        
        if len(recent_values) < 10:
            return None  # Insufficient data
        
        # Sort by timestamp
        recent_values.sort(key=lambda x: x[0])
        
        # Compute linear trend (slope)
        timestamps_numeric = [(ts - recent_values[0][0]).total_seconds() for ts, _ in recent_values]
        values = [val for _, val in recent_values]
        
        slope, intercept, r_value, p_value, std_err = stats.linregress(timestamps_numeric, values)
        
        # Calculate expected change over window
        total_duration = (recent_values[-1][0] - recent_values[0][0]).total_seconds()
        expected_change = slope * total_duration
        baseline_value = recent_values[0][1]
        
        # Calculate percentage change
        pct_change = (expected_change / baseline_value) * 100
        
        # Alert if degrading beyond threshold
        if pct_change < -threshold_pct:  # Negative slope = degradation
            return {
                'metric': metric,
                'pct_change': pct_change,
                'slope': slope,
                'r_squared': r_value ** 2,
                'p_value': p_value,
                'severity': 'critical' if pct_change < -threshold_pct * 2 else 'high'
            }
        
        return None
```

#### 3. Behavioral Drift Detection

**Monitor changes in model output characteristics:**

```python
class BehavioralDriftDetector:
    """Detect drift in model behavior patterns"""
    
    def __init__(self):
        self.output_history = []  # Store (timestamp, output_characteristics)
    
    def extract_output_characteristics(self, model_output):
        """Extract behavioral features from model output"""
        return {
            'output_length': len(model_output),
            'sentiment_score': self.analyze_sentiment(model_output),
            'toxicity_score': self.analyze_toxicity(model_output),
            'contains_refusal': self.detect_refusal_pattern(model_output),
            'avg_confidence': self.extract_confidence_scores(model_output)
        }
    
    def detect_output_drift(self, baseline_days=30, detection_days=7):
        """Detect drift in output characteristics"""
        now = datetime.now()
        
        # Split history into baseline and detection windows
        baseline_cutoff = now - timedelta(days=baseline_days + detection_days)
        detection_cutoff = now - timedelta(days=detection_days)
        
        baseline_outputs = [
            char for ts, char in self.output_history
            if baseline_cutoff <= ts < detection_cutoff
        ]
        
        detection_outputs = [
            char for ts, char in self.output_history
            if ts >= detection_cutoff
        ]
        
        if len(baseline_outputs) < 100 or len(detection_outputs) < 100:
            return None
        
        # Compare distributions for each characteristic
        drift_signals = []
        for characteristic in ['sentiment_score', 'toxicity_score', 'avg_confidence']:
            baseline_vals = [out[characteristic] for out in baseline_outputs]
            detection_vals = [out[characteristic] for out in detection_outputs]
            
            # T-test for mean shift
            t_stat, p_value = stats.ttest_ind(baseline_vals, detection_vals)
            
            if p_value < 0.01:
                baseline_mean = np.mean(baseline_vals)
                detection_mean = np.mean(detection_vals)
                drift_signals.append({
                    'characteristic': characteristic,
                    'baseline_mean': baseline_mean,
                    'detection_mean': detection_mean,
                    'shift': detection_mean - baseline_mean,
                    'p_value': p_value
                })
        
        return drift_signals
```

#### 4. Population Stability Index (PSI)

**Industry-standard metric for detecting data drift:**

```python
def calculate_psi(expected, actual, bins=10):
    """Calculate Population Stability Index"""
    # Bin the data
    breakpoints = np.percentile(expected, np.linspace(0, 100, bins + 1))
    
    # Count samples in each bin
    expected_counts = np.histogram(expected, bins=breakpoints)[0]
    actual_counts = np.histogram(actual, bins=breakpoints)[0]
    
    # Convert to percentages
    expected_pct = expected_counts / len(expected)
    actual_pct = actual_counts / len(actual)
    
    # Avoid division by zero
    expected_pct = np.where(expected_pct == 0, 0.0001, expected_pct)
    actual_pct = np.where(actual_pct == 0, 0.0001, actual_pct)
    
    # Calculate PSI
    psi_values = (actual_pct - expected_pct) * np.log(actual_pct / expected_pct)
    psi = np.sum(psi_values)
    
    return psi

def interpret_psi(psi_value):
    """Interpret PSI value"""
    if psi_value < 0.1:
        return 'No significant drift'
    elif psi_value < 0.2:
        return 'Moderate drift - investigate'
    else:
        return 'Significant drift - action required'
```

### RAG-Specific Drift Detection

**Monitor drift in RAG retrieval patterns:**

```python
class RAGDriftMonitor:
    """Drift detection for RAG systems"""
    
    def __init__(self):
        self.query_history = []
        self.retrieval_history = []
    
    def log_retrieval(self, timestamp, query, retrieved_docs):
        """Log RAG retrieval event"""
        self.retrieval_history.append({
            'timestamp': timestamp,
            'query': query,
            'doc_sources': [doc.source_id for doc in retrieved_docs],
            'avg_similarity': np.mean([doc.similarity_score for doc in retrieved_docs])
        })
    
    def detect_source_distribution_drift(self, window_days=30):
        """Detect if retrieval is increasingly biased toward specific sources"""
        now = datetime.now()
        
        # Baseline period (30-60 days ago)
        baseline_start = now - timedelta(days=window_days * 2)
        baseline_end = now - timedelta(days=window_days)
        
        baseline_retrievals = [
            r for r in self.retrieval_history
            if baseline_start <= r['timestamp'] < baseline_end
        ]
        
        # Recent period (last 30 days)
        recent_retrievals = [
            r for r in self.retrieval_history
            if r['timestamp'] >= baseline_end
        ]
        
        # Count source distribution
        def source_distribution(retrievals):
            source_counts = {}
            for retrieval in retrievals:
                for source in retrieval['doc_sources']:
                    source_counts[source] = source_counts.get(source, 0) + 1
            total = sum(source_counts.values())
            return {src: count/total for src, count in source_counts.items()}
        
        baseline_dist = source_distribution(baseline_retrievals)
        recent_dist = source_distribution(recent_retrievals)
        
        # Calculate distribution divergence
        all_sources = set(baseline_dist.keys()) | set(recent_dist.keys())
        divergence_signals = []
        
        for source in all_sources:
            baseline_pct = baseline_dist.get(source, 0)
            recent_pct = recent_dist.get(source, 0)
            
            # Alert if source representation increased significantly
            if recent_pct > baseline_pct * 1.5:  # 50% increase
                divergence_signals.append({
                    'source_id': source,
                    'baseline_pct': baseline_pct,
                    'recent_pct': recent_pct,
                    'increase_factor': recent_pct / max(baseline_pct, 0.001),
                    'severity': 'high'
                })
        
        return divergence_signals
```

### Alert and Response Integration

```python
class DriftAlertSystem:
    """Centralized drift alert management"""
    
    def __init__(self, detectors):
        self.detectors = detectors
        self.alert_thresholds = {
            'psi': 0.2,
            'performance_degradation_pct': 5,
            'ks_test_p_value': 0.01
        }
    
    def run_periodic_drift_check(self):
        """Execute all drift detectors and aggregate alerts"""
        all_alerts = []
        
        for detector_name, detector in self.detectors.items():
            try:
                alerts = detector.detect_drift()
                if alerts:
                    all_alerts.extend([
                        {**alert, 'detector': detector_name}
                        for alert in alerts
                    ])
            except Exception as e:
                log_error(f'Drift detector {detector_name} failed: {e}')
        
        # Prioritize alerts
        critical_alerts = [a for a in all_alerts if a.get('severity') == 'critical']
        
        if critical_alerts:
            # Trigger incident response
            self.trigger_incident_response(critical_alerts)
        elif all_alerts:
            # Flag for investigation
            self.flag_for_investigation(all_alerts)
        
        return all_alerts
    
    def trigger_incident_response(self, alerts):
        """Escalate critical drift to incident response team"""
        # Pause model updates
        pause_online_learning()
        
        # Quarantine recent data
        quarantine_recent_ingestion(hours=24)
        
        # Alert security team
        send_alert_to_security_team(alerts)
        
        # Log incident
        log_security_incident('drift_detection_critical', alerts)
```

## Limitations & Trade-offs

**Limitations:**
- **Latency in detection**: Drift detection requires accumulating data over time; attacks may succeed before trend becomes statistically significant
- **Baseline drift**: Legitimate distribution changes (seasonal patterns, evolving user behavior) can trigger false alarms
- **Requires historical data**: New systems lack baseline for comparison
- **Slow attacks evade detection**: Extremely gradual poisoning may remain below detection thresholds

**Trade-offs:**
- **Sensitivity vs. False Positive Rate**: Lower thresholds catch subtle drift but increase false alarms
- **Detection Window vs. Response Time**: Longer windows improve statistical confidence but delay detection
- **Computational Overhead**: Continuous statistical analysis consumes resources

## Testing & Validation

**Functional Testing:**
1. **Drift Simulation**: Inject gradual distribution shifts, verify detection
2. **Baseline Stability**: Confirm system doesn't alert on stable data
3. **Alert Thresholds**: Validate thresholds trigger at expected drift levels

**Security Testing:**
1. **Incremental Poisoning**: Simulate slow-accumulating data poisoning, measure detection latency
2. **Evasion Testing**: Attempt to poison below detection thresholds
3. **Performance Impact**: Measure computational overhead of drift monitoring

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Drift Detection Monitoring (Online Learning): Implement continuous monitoring of model behavior over extended time windows (weeks/months); use statistical tests (Kolmogorov-Smirnov, population stability index) to detect gradual distribution shifts; alert on cumulative performance degradation that exceeds baseline variance."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Gradual Drift Patterns (Online Learning): Slow, monotonic performance degradation or behavioral shifts over time without corresponding data distribution changes; model predictions gradually diverging from baseline behavior."
> — Extracted from RAG Data Poisoning detection signals (Adversarial AI)

> "Temporal Correlation Anomalies: Model performance changes temporally correlated with specific data source activity or contributor behavior patterns."
> — Extracted from RAG Data Poisoning detection signals (Adversarial AI)

## Related

- **Combines with**: [[mitigations/anomaly-detection-architecture]], [[mitigations/data-validation-pipelines]], [[mitigations/incident-response-procedures]]
- **Enables**: Early warning for incremental poisoning, baseline-aware threat detection
