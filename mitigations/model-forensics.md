---
title: "Model Forensics"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Model Forensics

## Summary

Model forensics provides investigative capabilities to analyze suspected tampered models, identify the nature and extent of modifications, and attribute attacks to specific threat actors or techniques. By maintaining comprehensive tooling and expertise for post-incident model analysis, organizations can understand how attacks occurred, validate defensive improvements, and support incident response. This mitigation enables learning from security failures and continuous improvement of model integrity controls.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0047 | [[techniques/model-tampering]] | Enables detailed analysis of tampering techniques, affected parameters, and attack sophistication |
| AML.T0018 | [[techniques/trojan-injection]] | Supports backdoor reverse engineering to understand trigger patterns and malicious behavior |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Facilitates identification of poisoned training data sources and poisoning methodology |

## Implementation

### Forensic Analysis Toolkit

**Core capabilities for model investigation:**

1. **Weight difference analysis**: Compare tampered model against known-good baseline
2. **Backdoor reverse engineering**: Identify trigger patterns and malicious neurons
3. **Lineage reconstruction**: Trace model provenance and modification history
4. **Behavioral analysis**: Understand what the tampered model does differently

### Weight Comparison and Diff Analysis

**Identify exactly what changed in a tampered model:**

```python
import torch
import numpy as np
from typing import Dict, List

class ModelForensics:
    """Forensic analysis tools for investigating tampered models"""
    
    def __init__(self):
        self.analysis_results = {}
    
    def compare_weights(self, baseline_model_path, suspected_model_path):
        """
        Compare weights between baseline and suspected tampered model
        """
        
        baseline = torch.load(baseline_model_path)
        suspected = torch.load(suspected_model_path)
        
        differences = []
        
        # Compare each layer
        baseline_params = dict(baseline.named_parameters())
        suspected_params = dict(suspected.named_parameters())
        
        for layer_name in baseline_params:
            if layer_name not in suspected_params:
                differences.append({
                    'layer': layer_name,
                    'type': 'missing_layer',
                    'severity': 'high'
                })
                continue
            
            baseline_weights = baseline_params[layer_name].data.cpu().numpy()
            suspected_weights = suspected_params[layer_name].data.cpu().numpy()
            
            # Compute difference metrics
            weight_diff = suspected_weights - baseline_weights
            
            analysis = {
                'layer': layer_name,
                'total_params': baseline_weights.size,
                'modified_params': np.sum(np.abs(weight_diff) > 1e-6),
                'max_absolute_change': float(np.max(np.abs(weight_diff))),
                'mean_absolute_change': float(np.mean(np.abs(weight_diff))),
                'l2_distance': float(np.linalg.norm(weight_diff)),
                'percent_modified': 100 * np.sum(np.abs(weight_diff) > 1e-6) / baseline_weights.size
            }
            
            # Identify specific modified neurons/weights
            if analysis['percent_modified'] > 0.1:  # More than 0.1% changed
                modified_indices = np.where(np.abs(weight_diff) > 1e-6)
                analysis['modified_indices'] = [
                    idx.tolist() for idx in modified_indices
                ]
                analysis['severity'] = 'high' if analysis['percent_modified'] > 10 else 'medium'
                differences.append(analysis)
        
        # Check for new layers
        for layer_name in suspected_params:
            if layer_name not in baseline_params:
                differences.append({
                    'layer': layer_name,
                    'type': 'added_layer',
                    'severity': 'high'
                })
        
        self.analysis_results['weight_comparison'] = differences
        return differences
    
    def extract_modified_neurons(self, baseline_model, suspected_model):
        """Extract specific neurons that were modified"""
        
        modified_neurons = []
        
        for (name_b, param_b), (name_s, param_s) in zip(
            baseline_model.named_parameters(),
            suspected_model.named_parameters()
        ):
            weights_b = param_b.data.cpu().numpy()
            weights_s = param_s.data.cpu().numpy()
            
            diff = np.abs(weights_s - weights_b)
            
            # For linear/conv layers, identify which neurons changed
            if len(weights_b.shape) >= 2:
                # Sum changes per output neuron
                neuron_diffs = diff.sum(axis=tuple(range(1, len(weights_b.shape))))
                
                # Find neurons with significant changes
                threshold = neuron_diffs.mean() + 3 * neuron_diffs.std()
                modified_mask = neuron_diffs > threshold
                
                if modified_mask.any():
                    modified_neurons.append({
                        'layer': name_b,
                        'modified_neuron_indices': np.where(modified_mask)[0].tolist(),
                        'modification_magnitudes': neuron_diffs[modified_mask].tolist()
                    })
        
        return modified_neurons
```

### Backdoor Trigger Reconstruction

**Reverse engineer backdoor triggers from tampered model:**

```python
class BackdoorReverseEngineering:
    """Reverse engineer backdoor triggers"""
    
    def __init__(self, model):
        self.model = model
    
    def find_trigger_via_gradient_ascent(
        self,
        target_class,
        input_shape,
        iterations=1000,
        learning_rate=0.1
    ):
        """
        Find minimal input pattern that activates backdoor
        """
        
        # Start with random input
        trigger = torch.randn(input_shape, requires_grad=True)
        optimizer = torch.optim.Adam([trigger], lr=learning_rate)
        
        for i in range(iterations):
            # Forward pass
            output = self.model(trigger.unsqueeze(0))
            
            # Maximize probability of target class
            loss = -output[0, target_class]
            
            # Add regularization to find minimal trigger
            l1_norm = torch.norm(trigger, 1)
            loss = loss + 0.01 * l1_norm
            
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            
            if i % 100 == 0:
                print(f'Iteration {i}: loss = {loss.item():.4f}')
        
        return trigger.detach()
    
    def analyze_suspicious_neurons(self, modified_neurons, clean_data):
        """
        Analyze behavior of modified neurons on clean data
        """
        
        analyses = []
        
        for neuron_info in modified_neurons:
            layer_name = neuron_info['layer']
            neuron_indices = neuron_info['modified_neuron_indices']
            
            # Register hook to capture activations
            activations = []
            
            def hook_fn(module, input, output):
                activations.append(output.detach())
            
            # Get layer module
            layer = dict(self.model.named_modules())[layer_name]
            hook = layer.register_forward_hook(hook_fn)
            
            # Run clean data through model
            for batch in clean_data:
                self.model(batch)
            
            hook.remove()
            
            # Analyze neuron activations
            all_acts = torch.cat(activations)
            
            for neuron_idx in neuron_indices:
                neuron_acts = all_acts[:, neuron_idx]
                
                analysis = {
                    'layer': layer_name,
                    'neuron': neuron_idx,
                    'mean_activation': neuron_acts.mean().item(),
                    'std_activation': neuron_acts.std().item(),
                    'max_activation': neuron_acts.max().item(),
                    'active_percentage': (neuron_acts > 0).float().mean().item() * 100
                }
                
                analyses.append(analysis)
        
        return analyses
```

### Provenance Reconstruction

**Reconstruct model lineage to identify tampering point:**

```python
class ProvenanceReconstruction:
    """Reconstruct model provenance for forensic investigation"""
    
    def __init__(self, registry, ledger):
        self.registry = registry
        self.ledger = ledger
    
    def trace_lineage(self, model_hash):
        """Build complete lineage tree for model"""
        
        # Get model record
        record = self.registry.get_model_record(model_hash)
        
        lineage_tree = {
            'model_hash': model_hash,
            'published_at': record['published_at'],
            'metadata': record['metadata'],
            'children': [],
            'parents': []
        }
        
        # Find parent models
        if 'parent_model' in record['metadata']:
            parent_hash = record['metadata']['parent_model']
            parent_lineage = self.trace_lineage(parent_hash)
            lineage_tree['parents'].append(parent_lineage)
        
        # Find models derived from this one
        for other_hash, other_record in self.registry.all_models():
            if other_record['metadata'].get('parent_model') == model_hash:
                child_lineage = self.trace_lineage(other_hash)
                lineage_tree['children'].append(child_lineage)
        
        return lineage_tree
    
    def identify_tampering_point(self, baseline_hash, tampered_hash):
        """
        Find where in lineage chain tampering occurred
        """
        
        baseline_lineage = self.trace_lineage(baseline_hash)
        tampered_lineage = self.trace_lineage(tampered_hash)
        
        # Walk lineage chains to find divergence point
        divergence = self._find_divergence(baseline_lineage, tampered_lineage)
        
        return divergence
    
    def _find_divergence(self, tree_a, tree_b):
        """Find where two lineage trees diverge"""
        
        if tree_a['model_hash'] != tree_b['model_hash']:
            return {
                'divergence_found': True,
                'baseline': tree_a['model_hash'],
                'tampered': tree_b['model_hash']
            }
        
        # Recursively check parents
        # Implementation would walk the trees to find divergence point
        return None
```

### Behavioral Analysis

**Understand what tampered model does differently:**

```python
class BehavioralAnalysis:
    """Analyze behavioral differences in tampered model"""
    
    def differential_testing(self, baseline_model, suspected_model, test_data):
        """Compare model outputs on test data"""
        
        differences = []
        
        for i, (input_data, label) in enumerate(test_data):
            baseline_output = baseline_model(input_data)
            suspected_output = suspected_model(input_data)
            
            # Compare outputs
            output_diff = (suspected_output - baseline_output).abs()
            
            if output_diff.max() > 0.1:  # Significant difference
                differences.append({
                    'sample_id': i,
                    'true_label': label,
                    'baseline_prediction': baseline_output.argmax().item(),
                    'suspected_prediction': suspected_output.argmax().item(),
                    'output_difference': output_diff.max().item()
                })
        
        return differences
    
    def targeted_misclassification_analysis(
        self,
        baseline_model,
        suspected_model,
        test_data,
        source_class,
        target_class
    ):
        """
        Check if model systematically misclassifies source_class as target_class
        """
        
        source_samples = [
            (x, y) for x, y in test_data if y == source_class
        ]
        
        misclassifications = []
        
        for input_data, label in source_samples:
            baseline_pred = baseline_model(input_data).argmax()
            suspected_pred = suspected_model(input_data).argmax()
            
            # Check for targeted misclassification
            if baseline_pred == source_class and suspected_pred == target_class:
                misclassifications.append({
                    'input': input_data,
                    'baseline_pred': baseline_pred.item(),
                    'suspected_pred': suspected_pred.item()
                })
        
        misclass_rate = len(misclassifications) / len(source_samples) * 100
        
        return {
            'source_class': source_class,
            'target_class': target_class,
            'total_source_samples': len(source_samples),
            'misclassifications': len(misclassifications),
            'misclassification_rate': misclass_rate,
            'is_backdoored': misclass_rate > 50  # Threshold
        }
```

### Forensic Report Generation

**Compile comprehensive forensic report:**

```python
def generate_forensic_report(
    baseline_path,
    suspected_path,
    test_data,
    output_path
):
    """Generate comprehensive forensic analysis report"""
    
    forensics = ModelForensics()
    
    # Load models
    baseline = torch.load(baseline_path)
    suspected = torch.load(suspected_path)
    
    report = {
        'timestamp': datetime.now().isoformat(),
        'baseline_model': baseline_path,
        'suspected_model': suspected_path,
        'analyses': {}
    }
    
    # Weight comparison
    weight_diffs = forensics.compare_weights(baseline_path, suspected_path)
    report['analyses']['weight_comparison'] = weight_diffs
    
    # Modified neurons
    modified_neurons = forensics.extract_modified_neurons(baseline, suspected)
    report['analyses']['modified_neurons'] = modified_neurons
    
    # Behavioral analysis
    behavioral = BehavioralAnalysis()
    behavior_diffs = behavioral.differential_testing(baseline, suspected, test_data)
    report['analyses']['behavioral_differences'] = behavior_diffs
    
    # Summary statistics
    report['summary'] = {
        'total_layers_modified': len(weight_diffs),
        'total_neurons_modified': sum(
            len(n['modified_neuron_indices']) for n in modified_neurons
        ),
        'behavioral_differences_found': len(behavior_diffs),
        'severity_assessment': assess_severity(report)
    }
    
    # Save report
    with open(output_path, 'w') as f:
        json.dump(report, f, indent=2)
    
    return report
```

## Limitations & Trade-offs

**Limitations:**
- **Requires baseline**: Forensic analysis needs known-good model for comparison
- **Expertise required**: Interpretation of results requires ML and security expertise
- **Time-consuming**: Thorough forensic analysis can take hours to days
- **Sophisticated attacks**: Advanced tampering may be difficult to fully understand

**Trade-offs:**
- **Depth vs. Speed**: Comprehensive analysis vs. quick triage
- **Automation vs. Human Analysis**: Automated tools vs. expert investigation
- **Preservation vs. Remediation**: Maintaining evidence vs. quickly fixing the problem

**Bypass Scenarios:**
- **Evidence destruction**: Attacker deletes baselines and logs before forensics can be conducted
- **Gradual tampering**: Slow changes over time make before/after comparison difficult
- **Sophisticated backdoors**: Trigger patterns may be too complex to reverse engineer

## Testing & Validation

**Functional Testing:**
1. **Weight comparison**: Verify accurate identification of modified parameters
2. **Neuron extraction**: Confirm correct identification of modified neurons
3. **Behavioral analysis**: Validate detection of output differences

**Security Testing:**
1. **Known backdoor analysis**: Test forensics on models with known backdoors
2. **Attribution accuracy**: Verify forensics correctly identifies attack techniques
3. **Minimal tampering detection**: Test detection of subtle, small-scale modifications

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Forensic Capabilities: Maintain tools and expertise to analyze suspected tampered models."
> — Extracted from Model Tampering mitigation section
