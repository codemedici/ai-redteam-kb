---
title: "Output Consistency Monitoring"
tags:
  - type/mitigation
  - target/llm-app
  - target/ml-model
  - target/agent
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Output Consistency Monitoring

## Summary

Output consistency monitoring continuously compares model service logs (original predictions) with downstream system logs (received predictions) to detect divergences that indicate output tampering or integrity violations. By maintaining independent audit trails at both generation and consumption points, organizations can identify when model outputs have been modified in transit or at integration boundaries. This detective control is critical for identifying output integrity attacks where adversaries intercept and manipulate predictions before they reach consuming systems.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0051 | [[techniques/output-integrity-attack]] | Detects output tampering by identifying mismatches between generated predictions and consumed predictions |

## Implementation

### Dual-Logging Architecture

**Log predictions at both generation and consumption points:**

**Model service logging:**

```python
import logging
import hashlib
import json
import datetime

class ModelServiceLogger:
    """Log all model outputs for consistency verification"""
    
    def __init__(self, log_path):
        self.logger = logging.getLogger('model_service')
        handler = logging.FileHandler(log_path)
        handler.setFormatter(logging.JSONFormatter())
        self.logger.addHandler(handler)
        self.logger.setLevel(logging.INFO)
    
    def log_prediction(self, request_id, input_data, prediction, confidence):
        """Log prediction at generation time"""
        
        # Create log entry
        log_entry = {
            'timestamp': datetime.datetime.now().isoformat(),
            'request_id': request_id,
            'input_hash': hashlib.sha256(
                json.dumps(input_data, sort_keys=True).encode()
            ).hexdigest(),
            'prediction': prediction,
            'confidence': confidence,
            'model_version': 'v1.2.3',
            'source': 'model_service'
        }
        
        self.logger.info(json.dumps(log_entry))
        return log_entry

# Usage in model service
model_logger = ModelServiceLogger('/var/log/model-service/predictions.jsonl')

@app.route('/api/v1/predict', methods=['POST'])
def predict():
    request_id = generate_request_id()
    input_data = request.get_json()
    
    # Generate prediction
    prediction, confidence = model.predict(input_data['input'])
    
    # Log at generation point
    model_logger.log_prediction(request_id, input_data, prediction, confidence)
    
    # Return to consumer
    return jsonify({
        'request_id': request_id,
        'prediction': prediction,
        'confidence': confidence
    })
```

**Consuming system logging:**

```python
class ConsumerLogger:
    """Log all consumed predictions for consistency verification"""
    
    def __init__(self, log_path):
        self.logger = logging.getLogger('consumer_service')
        handler = logging.FileHandler(log_path)
        handler.setFormatter(logging.JSONFormatter())
        self.logger.addHandler(handler)
        self.logger.setLevel(logging.INFO)
    
    def log_consumption(self, request_id, received_prediction, action_taken):
        """Log prediction at consumption time"""
        
        log_entry = {
            'timestamp': datetime.datetime.now().isoformat(),
            'request_id': request_id,
            'received_prediction': received_prediction,
            'action_taken': action_taken,
            'source': 'consumer_service'
        }
        
        self.logger.info(json.dumps(log_entry))
        return log_entry

# Usage in consuming application
consumer_logger = ConsumerLogger('/var/log/consumer-service/predictions.jsonl')

def process_model_output(api_response):
    request_id = api_response['request_id']
    prediction = api_response['prediction']
    
    # Log at consumption point
    consumer_logger.log_consumption(request_id, prediction, action='FILE_DELETED')
    
    # Act on prediction
    execute_action(prediction)
```

### Consistency Verification

**Automated comparison of generation vs. consumption logs:**

```python
import json
from collections import defaultdict

class ConsistencyMonitor:
    """Compare model service and consumer logs to detect tampering"""
    
    def __init__(self, model_log_path, consumer_log_path):
        self.model_log_path = model_log_path
        self.consumer_log_path = consumer_log_path
        self.violations = []
    
    def load_logs(self, log_path):
        """Load JSONL log file"""
        entries = {}
        with open(log_path, 'r') as f:
            for line in f:
                entry = json.loads(line)
                entries[entry['request_id']] = entry
        return entries
    
    def verify_consistency(self):
        """Compare generation and consumption logs"""
        
        # Load both log sets
        model_predictions = self.load_logs(self.model_log_path)
        consumer_predictions = self.load_logs(self.consumer_log_path)
        
        # Find common request IDs
        common_ids = set(model_predictions.keys()) & set(consumer_predictions.keys())
        
        violations = []
        
        for request_id in common_ids:
            model_entry = model_predictions[request_id]
            consumer_entry = consumer_predictions[request_id]
            
            # Compare predictions
            if model_entry['prediction'] != consumer_entry['received_prediction']:
                violation = {
                    'request_id': request_id,
                    'model_prediction': model_entry['prediction'],
                    'consumer_prediction': consumer_entry['received_prediction'],
                    'model_timestamp': model_entry['timestamp'],
                    'consumer_timestamp': consumer_entry['timestamp'],
                    'severity': 'CRITICAL',
                    'type': 'OUTPUT_TAMPERING'
                }
                violations.append(violation)
                self._alert_security_team(violation)
        
        return violations
    
    def _alert_security_team(self, violation):
        """Alert on detected tampering"""
        print(f"[SECURITY ALERT] Output integrity violation detected!")
        print(f"Request ID: {violation['request_id']}")
        print(f"Model predicted: {violation['model_prediction']}")
        print(f"Consumer received: {violation['consumer_prediction']}")
        
        # Trigger incident response
        # Quarantine affected systems
        # Investigate infrastructure compromise

# Usage: Run periodically (e.g., every 5 minutes)
monitor = ConsistencyMonitor(
    '/var/log/model-service/predictions.jsonl',
    '/var/log/consumer-service/predictions.jsonl'
)

violations = monitor.verify_consistency()

if violations:
    print(f"Detected {len(violations)} integrity violations!")
    # Trigger incident response
else:
    print("All predictions consistent - no tampering detected")
```

### Real-Time Monitoring Dashboard

**Visualize consistency metrics:**

```python
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime, timedelta

class ConsistencyDashboard:
    """Real-time monitoring of prediction consistency"""
    
    def __init__(self, monitor):
        self.monitor = monitor
    
    def calculate_metrics(self, time_window_hours=24):
        """Calculate consistency metrics over time window"""
        
        violations = self.monitor.verify_consistency()
        
        # Metrics
        total_predictions = len(self.monitor.load_logs(self.monitor.model_log_path))
        total_violations = len(violations)
        consistency_rate = (total_predictions - total_violations) / total_predictions * 100
        
        return {
            'total_predictions': total_predictions,
            'total_violations': total_violations,
            'consistency_rate': consistency_rate,
            'violations': violations
        }
    
    def plot_consistency_over_time(self):
        """Visualize consistency rate over time"""
        
        # Calculate hourly consistency rates
        hourly_rates = []
        for hour in range(24):
            metrics = self.calculate_metrics(time_window_hours=hour)
            hourly_rates.append(metrics['consistency_rate'])
        
        # Plot
        plt.figure(figsize=(12, 6))
        plt.plot(range(24), hourly_rates, marker='o')
        plt.axhline(y=99.9, color='g', linestyle='--', label='Target (99.9%)')
        plt.axhline(y=99.0, color='y', linestyle='--', label='Warning (99%)')
        plt.xlabel('Hours Ago')
        plt.ylabel('Consistency Rate (%)')
        plt.title('Output Consistency Rate (24-Hour Window)')
        plt.legend()
        plt.grid(True)
        plt.show()
```

### Anomaly Detection

**Statistical anomaly detection for output tampering patterns:**

```python
from scipy.stats import zscore
import numpy as np

class AnomalyDetector:
    """Detect unusual patterns in consistency violations"""
    
    def __init__(self, historical_data):
        self.baseline_violation_rate = np.mean(historical_data)
        self.baseline_std = np.std(historical_data)
    
    def detect_anomalies(self, current_violation_rate, threshold=3.0):
        """Detect if current violation rate is anomalous"""
        
        # Calculate z-score
        z = (current_violation_rate - self.baseline_violation_rate) / self.baseline_std
        
        if abs(z) > threshold:
            return {
                'is_anomalous': True,
                'z_score': z,
                'severity': 'CRITICAL' if abs(z) > 5 else 'HIGH',
                'message': f'Violation rate {z:.2f} standard deviations from baseline'
            }
        else:
            return {'is_anomalous': False}

# Usage
historical_violation_rates = [0.001, 0.002, 0.001, 0.003]  # Historical rates
detector = AnomalyDetector(historical_violation_rates)

current_rate = 0.15  # Current violation rate (15% - very high!)
result = detector.detect_anomalies(current_rate)

if result['is_anomalous']:
    print(f"[ALERT] Anomalous violation rate detected: {result['message']}")
    # Trigger incident response
```

### Behavioral Correlation

**Correlate downstream system actions with model predictions:**

```python
class BehavioralAnalyzer:
    """Analyze if downstream system behavior matches model predictions"""
    
    def analyze_action_correlation(self, model_log, action_log):
        """Verify actions match predictions"""
        
        correlations = []
        
        for request_id in model_log.keys():
            if request_id not in action_log:
                # Prediction not consumed - potential issue
                continue
            
            prediction = model_log[request_id]['prediction']
            action = action_log[request_id]['action']
            
            # Check expected correlation
            expected_action = self._expected_action(prediction)
            
            if action != expected_action:
                correlations.append({
                    'request_id': request_id,
                    'prediction': prediction,
                    'expected_action': expected_action,
                    'actual_action': action,
                    'mismatch': True
                })
        
        return correlations
    
    def _expected_action(self, prediction):
        """Map prediction to expected downstream action"""
        
        # Example: Malware classifier
        if prediction == 'malicious':
            return 'FILE_DELETED'
        elif prediction == 'benign':
            return 'FILE_PRESERVED'
        else:
            return 'UNKNOWN'

# Usage
analyzer = BehavioralAnalyzer()
mismatches = analyzer.analyze_action_correlation(model_predictions, system_actions)

if mismatches:
    print(f"Detected {len(mismatches)} prediction-action mismatches")
    # Potential output tampering
```

### Centralized Logging Infrastructure

**Use immutable, tamper-evident logging infrastructure:**

**Example: Elasticsearch with write-once indices**

```python
from elasticsearch import Elasticsearch
from datetime import datetime

class SecureLogger:
    """Log to tamper-evident Elasticsearch cluster"""
    
    def __init__(self, es_host):
        self.es = Elasticsearch([es_host])
        self.index_prefix = 'model-predictions'
    
    def log_prediction(self, prediction_data):
        """Log to write-once index"""
        
        # Use daily indices (write-once after 24h)
        index_name = f"{self.index_prefix}-{datetime.now().strftime('%Y.%m.%d')}"
        
        # Add document
        self.es.index(
            index=index_name,
            document=prediction_data,
            op_type='create'  # Fail if document already exists
        )
    
    def verify_log_integrity(self, index_name):
        """Verify index has not been modified"""
        
        # Check index settings
        settings = self.es.indices.get_settings(index=index_name)
        
        if settings[index_name]['settings']['index']['blocks']['write'] != 'true':
            raise SecurityError(f"Index {index_name} is not write-protected!")
        
        return True
```

## Limitations & Trade-offs

**Limitations:**

- **Latency in detection:** Violations detected after consumption (not preventive)
- **Log tampering:** If attacker compromises logging infrastructure, can modify both logs identically
- **Storage overhead:** Dual logging doubles storage requirements
- **Operational complexity:** Requires maintaining two independent logging pipelines
- **False positives:** Legitimate prediction variations (e.g., model retries) may trigger alerts

**Trade-offs:**

- **Security vs. Storage:** Comprehensive logging improves detection but increases storage costs
- **Security vs. Complexity:** Dual logging adds operational complexity
- **Detection latency vs. Performance:** Real-time verification adds overhead; batch verification reduces load but delays detection

**Bypass Scenarios:**

- **Synchronized tampering:** Attacker modifies both logs identically (requires compromise of both logging systems)
- **Log deletion:** Attacker deletes log entries instead of modifying (detectable via sequence gaps)
- **Logging infrastructure compromise:** If centralized logging compromised, all evidence can be destroyed

## Testing & Validation

**Functional Testing:**

1. **Log completeness:** Verify all predictions logged at both generation and consumption
2. **Consistency detection:** Inject tampered predictions and verify detection
3. **Timestamp correlation:** Verify request IDs correctly match across log sources
4. **Alert triggering:** Confirm violations trigger appropriate alerts
5. **Dashboard accuracy:** Verify metrics correctly reflect consistency state

**Security Testing:**

1. **Tampering detection:** Modify predictions in transit and verify detection
2. **Log manipulation:** Attempt to modify logs (should be immutable)
3. **False positive rate:** Measure how often legitimate traffic triggers false alarms
4. **Detection latency:** Measure time from tampering to detection

**Monitoring:**

- Track consistency rate over time (target: >99.9%)
- Alert on consistency rate drops below threshold
- Monitor log ingestion rates (detect log failures)
- Track violation patterns (repeated tampering of specific prediction types)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Output Consistency Monitoring: Continuously compare model service logs (original predictions) with downstream system logs (received predictions); alert on divergences. Behavioral Analysis: Monitor downstream system decisions and correlate with expected model behavior; flag anomalies."
> — Derived from [[sources/bibliography#OWASP ML Security Top 10]], Output Integrity Attack detection guidance

## Related

- **Combined with:** [[mitigations/cryptographic-output-signing]], [[mitigations/output-monitoring-validation]], [[mitigations/telemetry-and-monitoring-architecture]]
- **Depends on:** Centralized logging infrastructure, immutable audit logs, monitoring dashboards
