---
title: "Weight Distribution Monitoring"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Weight Distribution Monitoring

## Summary

Weight distribution monitoring continuously analyzes statistical properties of model parameters to detect anomalous modifications indicative of tampering, backdoor injection, or poisoning. By establishing baselines for expected weight distributions and alerting on significant deviations, this mitigation can identify subtle parameter changes that bypass traditional testing while maintaining apparent functionality. This detective control is particularly effective against attacks that inject backdoors into specific neurons or layers.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0047 | [[techniques/model-tampering]] | Detects weight poisoning through statistical anomaly detection in parameter distributions |
| AML.T0018 | [[techniques/trojan-injection]] | Identifies backdoor neurons exhibiting unusual weight patterns or activation characteristics |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Detects training-time poisoning that manifests as distributional shifts in learned parameters |

## Implementation

### Statistical Baseline Establishment

**Capture weight statistics from known-good models:**

```python
import numpy as np
import torch
from scipy import stats
from typing import Dict, List

class WeightDistributionMonitor:
    """Monitor model weight distributions for anomalies"""
    
    def __init__(self):
        self.baselines = {}  # Layer name -> distribution stats
    
    def establish_baseline(self, model, model_name):
        """Compute baseline statistics for clean model"""
        
        baseline_stats = {}
        
        for name, param in model.named_parameters():
            weights = param.data.cpu().numpy().flatten()
            
            layer_stats = {
                'mean': float(np.mean(weights)),
                'std': float(np.std(weights)),
                'min': float(np.min(weights)),
                'max': float(np.max(weights)),
                'median': float(np.median(weights)),
                'q1': float(np.percentile(weights, 25)),
                'q3': float(np.percentile(weights, 75)),
                'skewness': float(stats.skew(weights)),
                'kurtosis': float(stats.kurtosis(weights)),
                'l1_norm': float(np.linalg.norm(weights, 1)),
                'l2_norm': float(np.linalg.norm(weights, 2)),
                'num_zeros': int(np.sum(np.abs(weights) < 1e-8)),
                'num_params': len(weights)
            }
            
            # Compute histogram for distribution comparison
            hist, bin_edges = np.histogram(weights, bins=50)
            layer_stats['histogram'] = hist.tolist()
            layer_stats['bin_edges'] = bin_edges.tolist()
            
            baseline_stats[name] = layer_stats
        
        self.baselines[model_name] = baseline_stats
        
        return baseline_stats
    
    def detect_anomalies(self, model, model_name, threshold=3.0):
        """
        Detect statistical anomalies in model weights
        
        Args:
            threshold: Number of standard deviations for anomaly detection
        """
        
        if model_name not in self.baselines:
            raise ValueError(f'No baseline for model {model_name}')
        
        baseline = self.baselines[model_name]
        anomalies = []
        
        for name, param in model.named_parameters():
            if name not in baseline:
                anomalies.append({
                    'layer': name,
                    'type': 'unexpected_layer',
                    'severity': 'high'
                })
                continue
            
            weights = param.data.cpu().numpy().flatten()
            layer_baseline = baseline[name]
            
            # Compute current statistics
            current_mean = np.mean(weights)
            current_std = np.std(weights)
            current_skewness = stats.skew(weights)
            current_kurtosis = stats.kurtosis(weights)
            
            # Check for statistical deviations
            # Mean shift
            if abs(current_mean - layer_baseline['mean']) > threshold * layer_baseline['std']:
                anomalies.append({
                    'layer': name,
                    'type': 'mean_shift',
                    'baseline_mean': layer_baseline['mean'],
                    'current_mean': current_mean,
                    'deviation_sds': abs(current_mean - layer_baseline['mean']) / layer_baseline['std'],
                    'severity': 'high'
                })
            
            # Std deviation change
            std_ratio = current_std / layer_baseline['std']
            if std_ratio < 0.5 or std_ratio > 2.0:
                anomalies.append({
                    'layer': name,
                    'type': 'variance_change',
                    'baseline_std': layer_baseline['std'],
                    'current_std': current_std,
                    'ratio': std_ratio,
                    'severity': 'medium'
                })
            
            # Distribution shape changes
            if abs(current_skewness - layer_baseline['skewness']) > 0.5:
                anomalies.append({
                    'layer': name,
                    'type': 'skewness_change',
                    'baseline_skewness': layer_baseline['skewness'],
                    'current_skewness': current_skewness,
                    'severity': 'medium'
                })
            
            # Outlier detection - extreme weights
            extreme_weights = weights[np.abs(weights - current_mean) > 5 * current_std]
            if len(extreme_weights) > 0.01 * len(weights):  # More than 1% outliers
                anomalies.append({
                    'layer': name,
                    'type': 'extreme_outliers',
                    'num_outliers': len(extreme_weights),
                    'percentage': 100 * len(extreme_weights) / len(weights),
                    'severity': 'high'
                })
            
            # Kolmogorov-Smirnov test for distribution comparison
            # Reconstruct baseline distribution from histogram
            baseline_samples = self._reconstruct_from_histogram(
                layer_baseline['histogram'],
                layer_baseline['bin_edges']
            )
            ks_stat, p_value = stats.ks_2samp(weights, baseline_samples)
            
            if p_value < 0.01:  # Significant distribution change
                anomalies.append({
                    'layer': name,
                    'type': 'distribution_shift',
                    'ks_statistic': ks_stat,
                    'p_value': p_value,
                    'severity': 'high'
                })
        
        return anomalies
    
    def _reconstruct_from_histogram(self, hist, bin_edges):
        """Reconstruct sample distribution from histogram"""
        samples = []
        for count, (left, right) in zip(hist, zip(bin_edges[:-1], bin_edges[1:])):
            # Sample uniformly within bin
            bin_samples = np.random.uniform(left, right, int(count))
            samples.extend(bin_samples)
        return np.array(samples)
    
    def visualize_anomalies(self, anomalies):
        """Generate report of detected anomalies"""
        
        if not anomalies:
            return 'No anomalies detected'
        
        # Group by severity
        high = [a for a in anomalies if a['severity'] == 'high']
        medium = [a for a in anomalies if a['severity'] == 'medium']
        
        report = f'Weight Distribution Anomalies Detected\n'
        report += f'{"="*50}\n\n'
        
        if high:
            report += f'HIGH SEVERITY ({len(high)}):\n'
            for anomaly in high:
                report += f'  - {anomaly["layer"]}: {anomaly["type"]}\n'
                for k, v in anomaly.items():
                    if k not in ['layer', 'type', 'severity']:
                        report += f'    {k}: {v}\n'
        
        if medium:
            report += f'\nMEDIUM SEVERITY ({len(medium)}):\n'
            for anomaly in medium:
                report += f'  - {anomaly["layer"]}: {anomaly["type"]}\n'
        
        return report
```

### Neuron-Level Backdoor Detection

**Detect individual neurons with anomalous activations:**

```python
class BackdoorNeuronDetector:
    """Detect backdoor neurons through activation analysis"""
    
    def __init__(self, model):
        self.model = model
        self.activation_hooks = {}
        self.activations = {}
    
    def register_hooks(self):
        """Register forward hooks to capture activations"""
        
        def get_activation(name):
            def hook(module, input, output):
                self.activations[name] = output.detach()
            return hook
        
        # Register hooks on all layers
        for name, module in self.model.named_modules():
            if isinstance(module, (torch.nn.Linear, torch.nn.Conv2d)):
                self.activation_hooks[name] = module.register_forward_hook(
                    get_activation(name)
                )
    
    def detect_backdoor_neurons(self, clean_data, trigger_data):
        """
        Identify neurons that activate differently for triggered inputs
        """
        
        # Collect activations on clean data
        clean_activations = {}
        for batch in clean_data:
            self.model(batch)
            for layer, acts in self.activations.items():
                if layer not in clean_activations:
                    clean_activations[layer] = []
                clean_activations[layer].append(acts)
        
        # Collect activations on trigger data
        trigger_activations = {}
        for batch in trigger_data:
            self.model(batch)
            for layer, acts in self.activations.items():
                if layer not in trigger_activations:
                    trigger_activations[layer] = []
                trigger_activations[layer].append(acts)
        
        # Analyze differences
        suspicious_neurons = []
        
        for layer in clean_activations:
            clean_acts = torch.cat(clean_activations[layer])
            trigger_acts = torch.cat(trigger_activations[layer])
            
            # Compute mean activation per neuron
            clean_mean = clean_acts.mean(dim=0)
            trigger_mean = trigger_acts.mean(dim=0)
            
            # Find neurons with large activation difference
            diff = (trigger_mean - clean_mean).abs()
            threshold = clean_mean.std() * 3
            
            backdoor_mask = diff > threshold
            if backdoor_mask.any():
                suspicious_neurons.append({
                    'layer': layer,
                    'num_suspicious': backdoor_mask.sum().item(),
                    'neuron_indices': backdoor_mask.nonzero().squeeze().tolist(),
                    'activation_diff': diff[backdoor_mask].tolist()
                })
        
        return suspicious_neurons
```

### Continuous Monitoring Integration

**Integrate with deployment pipeline:**

```python
class ContinuousWeightMonitor:
    """Monitor weights continuously in production"""
    
    def __init__(self, monitor, alert_threshold=3.0):
        self.monitor = monitor
        self.alert_threshold = alert_threshold
    
    def verify_before_deployment(self, model_path, model_name):
        """Verify model before deployment"""
        
        # Load model
        model = torch.load(model_path)
        
        # Detect anomalies
        anomalies = self.monitor.detect_anomalies(
            model, 
            model_name,
            threshold=self.alert_threshold
        )
        
        # Generate report
        if anomalies:
            high_severity = [a for a in anomalies if a['severity'] == 'high']
            
            if high_severity:
                # Block deployment
                raise DeploymentBlockedError(
                    f'{len(high_severity)} high-severity weight anomalies detected'
                )
            else:
                # Warn but allow
                log_warning(
                    f'{len(anomalies)} medium-severity anomalies detected',
                    anomalies
                )
        
        return True
```

## Limitations & Trade-offs

**Limitations:**
- **False positives**: Legitimate model updates (new architectures, different initialization) may trigger alerts
- **Baseline dependency**: Requires known-good model to establish baseline; bootstrapping problem for new models
- **Subtle attacks**: Sophisticated attackers may craft backdoors that preserve statistical distributions
- **Computational cost**: Statistical analysis of large models can be time-consuming

**Trade-offs:**
- **Sensitivity vs. False Positives**: Lower thresholds catch more attacks but generate more false alarms
- **Coverage vs. Performance**: Analyzing all layers thoroughly vs. sampling for speed
- **Detection latency**: Real-time monitoring vs. batch analysis

**Bypass Scenarios:**
- **Distribution-preserving backdoors**: Attackers craft backdoors matching baseline statistics
- **Gradual drift**: Slow, incremental changes over time may evade detection
- **Baseline poisoning**: If baseline itself is compromised, anomalies won't be detected

## Testing & Validation

**Functional Testing:**
1. **Baseline establishment**: Verify baselines captured correctly for clean models
2. **Anomaly detection**: Confirm anomalies detected when weights are modified
3. **Threshold tuning**: Validate alert thresholds minimize false positives while maintaining sensitivity

**Security Testing:**
1. **Backdoor injection**: Inject known backdoors and verify detection
2. **Weight poisoning**: Randomly perturb weights and verify detection
3. **Evasion testing**: Attempt to craft anomalies that preserve statistics

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Weight Distribution Monitoring: Continuously analyze parameter statistics against baselines; alert on significant deviations."
> — Extracted from Model Tampering mitigation section
