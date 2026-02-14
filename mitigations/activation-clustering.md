---
title: "Activation Clustering"
tags:
  - type/mitigation
  - target/ml-model
  - target/cv
  - target/generative-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Activation Clustering

## Summary

Activation clustering analyzes internal neuron activation patterns across hidden layers to detect backdoor triggers and anomalous model behavior. When a backdoored model processes trigger inputs, it exhibits distinctive activation patterns that differ from clean data processing. By clustering activation patterns and identifying outlier clusters, defenders can detect backdoor triggers, investigate suspicious behavior, and validate model integrity. This mitigation is particularly effective for detecting backdoor poisoning attacks where specific input patterns cause targeted misbehavior while the model appears normal on standard validation sets.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0015 | [[techniques/adversarial-robustness]] | Detects backdoor triggers through activation pattern analysis; identifies hidden triggers causing targeted misbehavior |
| | [[techniques/backdoor-poisoning]] | Analyzes activation patterns to discover backdoored samples and trigger characteristics |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Identifies training samples causing anomalous internal representations |

## Implementation

### Core Concept

**Backdoor Detection via Activation Analysis:**

When a model contains a backdoor, inputs containing the trigger pattern activate specific neurons in ways that differ from clean inputs. Activation clustering exploits this difference:

1. **Collect Activations:** Extract neuron activation values from hidden layers for both clean and test inputs
2. **Cluster Analysis:** Apply clustering algorithms (k-means, DBSCAN, hierarchical clustering) to group inputs by activation similarity
3. **Identify Outliers:** Detect small clusters or outliers that may represent backdoor trigger patterns
4. **Trigger Investigation:** Analyze inputs in outlier clusters to identify common trigger characteristics

### Activation Collection

```python
import torch
import numpy as np

class ActivationCollector:
    """Collect neuron activations from hidden layers"""
    
    def __init__(self, model, target_layers=None):
        self.model = model
        self.activations = {}
        self.hooks = []
        
        # Register hooks on target layers
        if target_layers is None:
            # Use last few layers by default
            target_layers = self._get_deep_layers()
        
        for layer_name in target_layers:
            layer = self._get_layer_by_name(layer_name)
            hook = layer.register_forward_hook(self._hook_fn(layer_name))
            self.hooks.append(hook)
    
    def _hook_fn(self, layer_name):
        """Hook function to capture activations"""
        def hook(module, input, output):
            self.activations[layer_name] = output.detach().cpu().numpy()
        return hook
    
    def collect_activations(self, inputs):
        """Collect activations for batch of inputs"""
        activation_records = []
        
        for input_data in inputs:
            # Forward pass
            _ = self.model(input_data)
            
            # Flatten activations from all layers
            flattened = []
            for layer_name in sorted(self.activations.keys()):
                act = self.activations[layer_name].flatten()
                flattened.append(act)
            
            combined = np.concatenate(flattened)
            activation_records.append(combined)
            
            # Clear for next input
            self.activations = {}
        
        return np.array(activation_records)
    
    def cleanup(self):
        """Remove hooks"""
        for hook in self.hooks:
            hook.remove()
    
    def _get_deep_layers(self):
        """Get names of deep layers in model"""
        # Implementation depends on model architecture
        pass
    
    def _get_layer_by_name(self, layer_name):
        """Get layer module by name"""
        # Implementation depends on model architecture
        pass
```

### Clustering Analysis

```python
from sklearn.cluster import DBSCAN, KMeans
from sklearn.decomposition import PCA

class ActivationClusterer:
    """Cluster activation patterns to detect backdoors"""
    
    def __init__(self, method='dbscan'):
        self.method = method
        self.clustering_model = None
        self.pca = None
    
    def fit_cluster(self, activations, n_components=50):
        """
        Cluster activation patterns
        
        Args:
            activations: numpy array of activation vectors
            n_components: PCA components for dimensionality reduction
        """
        # Dimensionality reduction (activations are high-dimensional)
        self.pca = PCA(n_components=n_components)
        reduced_activations = self.pca.fit_transform(activations)
        
        # Clustering
        if self.method == 'dbscan':
            # DBSCAN good for outlier detection
            self.clustering_model = DBSCAN(eps=0.5, min_samples=5)
        elif self.method == 'kmeans':
            # K-means requires specifying number of clusters
            self.clustering_model = KMeans(n_clusters=10)
        
        labels = self.clustering_model.fit_predict(reduced_activations)
        
        return labels, reduced_activations
    
    def identify_outlier_clusters(self, labels, min_cluster_size=5):
        """
        Identify small clusters that may represent backdoor triggers
        
        Returns: list of cluster IDs that are unusually small
        """
        from collections import Counter
        
        cluster_sizes = Counter(labels)
        
        # Filter out noise (-1 in DBSCAN)
        if self.method == 'dbscan':
            cluster_sizes.pop(-1, None)
        
        # Identify clusters smaller than threshold
        outlier_clusters = [
            cluster_id for cluster_id, size in cluster_sizes.items()
            if size < min_cluster_size
        ]
        
        return outlier_clusters
    
    def visualize_clusters(self, reduced_activations, labels, save_path=None):
        """Visualize clusters in 2D space"""
        import matplotlib.pyplot as plt
        
        # Further reduce to 2D for visualization
        if reduced_activations.shape[1] > 2:
            pca_2d = PCA(n_components=2)
            coords_2d = pca_2d.fit_transform(reduced_activations)
        else:
            coords_2d = reduced_activations
        
        # Plot
        plt.figure(figsize=(10, 8))
        scatter = plt.scatter(coords_2d[:, 0], coords_2d[:, 1], 
                             c=labels, cmap='viridis', alpha=0.6)
        plt.colorbar(scatter, label='Cluster ID')
        plt.xlabel('PCA Component 1')
        plt.ylabel('PCA Component 2')
        plt.title('Activation Clustering')
        
        if save_path:
            plt.savefig(save_path)
        else:
            plt.show()
```

### Backdoor Detection Workflow

```python
def detect_backdoor_via_activation_clustering(model, test_inputs, test_labels):
    """
    Full workflow for backdoor detection via activation clustering
    
    Args:
        model: Trained model to analyze
        test_inputs: Test dataset inputs
        test_labels: Test dataset labels (for validation)
    
    Returns:
        report: Dictionary containing detection results
    """
    # Step 1: Collect activations
    collector = ActivationCollector(model)
    activations = collector.collect_activations(test_inputs)
    collector.cleanup()
    
    # Step 2: Cluster activations
    clusterer = ActivationClusterer(method='dbscan')
    labels, reduced_activations = clusterer.fit_cluster(activations)
    
    # Step 3: Identify outlier clusters
    outlier_clusters = clusterer.identify_outlier_clusters(labels, min_cluster_size=10)
    
    if not outlier_clusters:
        return {
            'backdoor_detected': False,
            'message': 'No outlier clusters detected'
        }
    
    # Step 4: Analyze outlier clusters
    backdoor_evidence = []
    
    for cluster_id in outlier_clusters:
        # Get inputs in this cluster
        cluster_indices = np.where(labels == cluster_id)[0]
        cluster_inputs = [test_inputs[i] for i in cluster_indices]
        cluster_predictions = [model.predict(inp) for inp in cluster_inputs]
        
        # Check if predictions are consistent (potential backdoor)
        prediction_consistency = len(set(cluster_predictions)) / len(cluster_predictions)
        
        if prediction_consistency < 0.2:  # >80% predict same class
            # Likely backdoor: small cluster with consistent prediction
            backdoor_evidence.append({
                'cluster_id': cluster_id,
                'cluster_size': len(cluster_indices),
                'sample_indices': cluster_indices.tolist(),
                'consistent_prediction': max(set(cluster_predictions), 
                                            key=cluster_predictions.count),
                'consistency_rate': 1 - prediction_consistency,
                'severity': 'high' if len(cluster_indices) < 5 else 'medium'
            })
    
    # Step 5: Generate report
    report = {
        'backdoor_detected': len(backdoor_evidence) > 0,
        'outlier_clusters_count': len(outlier_clusters),
        'backdoor_evidence': backdoor_evidence,
        'visualization_data': {
            'labels': labels.tolist(),
            'reduced_activations': reduced_activations.tolist()
        },
        'recommendation': 'Investigate samples in outlier clusters for common trigger patterns'
    }
    
    # Visualize
    clusterer.visualize_clusters(
        reduced_activations, 
        labels, 
        save_path='activation_clustering_analysis.png'
    )
    
    return report

def investigate_trigger_pattern(cluster_inputs):
    """
    Analyze inputs in outlier cluster to identify trigger characteristics
    
    Returns: potential trigger pattern description
    """
    # Domain-specific analysis
    # For images: look for common pixel patterns, patches, colors
    # For text: look for common tokens, phrases, metadata
    
    # Example for images
    if isinstance(cluster_inputs[0], np.ndarray) and cluster_inputs[0].ndim >= 2:
        # Compute average image
        avg_image = np.mean(cluster_inputs, axis=0)
        
        # Find regions with high variance (potential trigger location)
        variance_map = np.var(cluster_inputs, axis=0)
        trigger_regions = (variance_map > np.percentile(variance_map, 90))
        
        return {
            'type': 'image_patch',
            'trigger_regions': trigger_regions,
            'avg_trigger_visualization': avg_image
        }
    
    # For text
    elif isinstance(cluster_inputs[0], str):
        # Tokenize and find common tokens
        from collections import Counter
        
        all_tokens = []
        for text in cluster_inputs:
            all_tokens.extend(text.split())
        
        common_tokens = Counter(all_tokens).most_common(10)
        
        return {
            'type': 'text_pattern',
            'common_tokens': common_tokens
        }
```

### Integration with Model Validation

```python
def backdoor_scanning_in_ci_cd(model_path, validation_dataset):
    """
    Automated backdoor scanning for CI/CD pipeline
    """
    # Load model
    model = load_model(model_path)
    
    # Run activation clustering
    detection_report = detect_backdoor_via_activation_clustering(
        model, 
        validation_dataset['inputs'],
        validation_dataset['labels']
    )
    
    # Check if backdoor detected
    if detection_report['backdoor_detected']:
        # Alert security team
        alert_security_team({
            'event': 'backdoor_detected',
            'model_path': model_path,
            'evidence': detection_report['backdoor_evidence']
        })
        
        # Fail deployment
        raise SecurityError(
            f"Backdoor detected in model {model_path}. "
            f"Found {len(detection_report['backdoor_evidence'])} suspicious clusters."
        )
    
    # Log clean scan
    log_security_event('backdoor_scan_passed', {
        'model_path': model_path,
        'outlier_clusters': detection_report['outlier_clusters_count']
    })
    
    return detection_report
```

## Limitations & Trade-offs

**Limitations:**
- **Requires Diverse Test Set:** Effectiveness depends on having representative test inputs that may include trigger patterns
- **Curse of Dimensionality:** High-dimensional activation spaces make clustering challenging; requires dimensionality reduction
- **Threshold Sensitivity:** Determining what constitutes an "outlier" cluster is subjective; false positives/negatives possible
- **Computational Cost:** Collecting and clustering activations for large datasets is expensive
- **Sophisticated Backdoors:** Advanced backdoors may avoid distinctive activation patterns

**Trade-offs:**
- **Cluster Granularity vs. Detection Sensitivity:** Finer clustering increases false positives; coarser clustering may miss subtle backdoors
- **Dimensionality Reduction vs. Information Loss:** PCA reduces computational cost but may lose trigger-specific activation patterns
- **Automation vs. Human Analysis:** Automated detection is scalable but may require human validation of flagged clusters

## Testing & Validation

**Functional Testing:**
1. **Known Backdoor Detection:** Inject known backdoor into model, verify activation clustering detects it
2. **Clean Model Baseline:** Run on clean models, ensure no false backdoor alerts
3. **Trigger Identification:** Validate that identified outlier clusters correspond to actual backdoor triggers

**Security Testing:**
1. **Evasion Attempts:** Test if sophisticated backdoors can evade activation clustering
2. **Trigger Variation:** Verify detection works for varied trigger patterns (size, location, intensity)
3. **Multi-Trigger Backdoors:** Test detection of models with multiple distinct backdoors

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Backdoor attacks exhibit distinctive activation patterns in hidden layers. Activation clustering analysis can detect outlier clusters representing backdoor triggers."
> — Extracted from adversarial robustness detection signals

## Related

- **Combines with:** [[mitigations/ab-testing-validation]], [[mitigations/anomaly-detection-architecture]], [[mitigations/data-validation-pipelines]]
- **Enables:** Backdoor trigger discovery, model integrity validation, forensic analysis
