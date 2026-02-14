---
title: "Synthetic Data Generation"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/adversarial-ai
atlas: AML.M0028
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Synthetic Data Generation

## Summary

Synthetic data generation augments training datasets with artificially created samples known to be clean, diluting the impact of poisoned data and reducing attacker influence over the training distribution. By generating high-quality synthetic examples that follow expected patterns, this mitigation makes data poisoning attacks more difficult—attackers must inject larger volumes of poison to achieve the same effect, increasing detection likelihood. Synthetic data also improves model robustness by expanding training coverage into edge cases and underrepresented categories.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Dilutes poisoned samples in training data by adding known-clean synthetic examples |
| | [[techniques/data-poisoning-attacks]] | Reduces impact of label flipping, trigger injection, and clean-label attacks |
| | [[techniques/backdoor-ml-model]] | Makes backdoor implantation more difficult by requiring more poison samples |
| | [[techniques/model-skewing]] | Counters targeted bias injection by balancing training distribution |
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Expands training set diversity to reduce memorization of specific sensitive examples |

## Implementation

### Synthetic Data Generation Approaches

#### 1. Template-Based Generation

**Use case**: Structured domains with known patterns (SQL queries, code, configuration files)

```python
class TemplateSyntheticGenerator:
    """Generate synthetic data from templates"""
    
    def __init__(self, templates, variable_sets):
        self.templates = templates
        self.variable_sets = variable_sets
    
    def generate_samples(self, count):
        """Generate synthetic training examples"""
        synthetic_samples = []
        
        for _ in range(count):
            # Select random template
            template = random.choice(self.templates)
            
            # Fill variables
            variables = {
                var_name: random.choice(var_values)
                for var_name, var_values in self.variable_sets.items()
            }
            
            # Generate sample
            sample_text = template.format(**variables)
            
            # Assign label based on template metadata
            label = template.expected_label
            
            synthetic_samples.append({
                'text': sample_text,
                'label': label,
                'source': 'synthetic_template',
                'template_id': template.id
            })
        
        return synthetic_samples

# Example templates for SQL injection detection
SQL_INJECTION_TEMPLATES = [
    {
        'id': 'safe-query-001',
        'template': "SELECT {columns} FROM {table} WHERE {condition}",
        'expected_label': 'safe',
        'variables': {
            'columns': ['id', 'name', 'email'],
            'table': ['users', 'products', 'orders'],
            'condition': ['status = "active"', 'created_at > "2024-01-01"']
        }
    },
    {
        'id': 'injection-001',
        'template': "SELECT * FROM users WHERE username='{username}' OR {injection}",
        'expected_label': 'malicious',
        'variables': {
            'username': ['admin', 'user1', 'test'],
            'injection': ['1=1', "'1'='1'", '1=1--']
        }
    }
]
```

#### 2. Generative Model-Based Synthesis

**Use case**: Text, images, or complex data where generative models (GPT, DALL-E) can create realistic examples

```python
class GenerativeModelSyntheticGenerator:
    """Generate synthetic data using generative AI"""
    
    def __init__(self, generative_model):
        self.generator = generative_model
    
    def generate_text_samples(self, category, count, diversity_temperature=0.8):
        """Generate synthetic text examples for a category"""
        synthetic_samples = []
        
        # Define generation prompt
        prompt = f"""Generate {count} diverse examples of text in the category: {category}.
        Each example should be realistic and representative of the category.
        Format: one example per line."""
        
        # Generate batch
        generated_text = self.generator.generate(
            prompt, 
            max_tokens=count * 50,
            temperature=diversity_temperature
        )
        
        # Parse generated examples
        examples = generated_text.strip().split('\n')
        
        for example in examples[:count]:
            synthetic_samples.append({
                'text': example.strip(),
                'label': category,
                'source': 'synthetic_generative',
                'generator_model': self.generator.model_id
            })
        
        return synthetic_samples
```

#### 3. Data Augmentation

**Use case**: Creating variations of existing clean data

```python
import nlpaug.augmenter.word as naw
import nlpaug.augmenter.sentence as nas

class AugmentationSyntheticGenerator:
    """Generate synthetic variations of existing data"""
    
    def __init__(self):
        self.word_augmenter = naw.SynonymAug(aug_src='wordnet')
        self.sentence_augmenter = nas.ContextualWordEmbsAug(model_path='bert-base-uncased')
    
    def augment_sample(self, original_text, original_label, variations=3):
        """Create synthetic variations of an example"""
        synthetic_samples = []
        
        for i in range(variations):
            # Apply augmentation
            if random.random() < 0.5:
                # Synonym replacement
                augmented_text = self.word_augmenter.augment(original_text)
            else:
                # Contextual word embedding substitution
                augmented_text = self.sentence_augmenter.augment(original_text)
            
            synthetic_samples.append({
                'text': augmented_text,
                'label': original_label,  # Same label as original
                'source': 'synthetic_augmentation',
                'original_id': original_text[:50]  # Reference
            })
        
        return synthetic_samples
```

### Poisoning Dilution Strategy

**Calculate required synthetic data volume to dilute poison:**

```python
def calculate_dilution_ratio(poisoned_sample_count, target_poison_percentage=0.01):
    """
    Calculate how many synthetic samples needed to reduce poison impact
    
    Args:
        poisoned_sample_count: Estimated number of poisoned samples in dataset
        target_poison_percentage: Desired maximum poison ratio (e.g., 0.01 = 1%)
    
    Returns:
        Required number of synthetic samples to add
    """
    # Formula: poisoned_samples / (total_samples + synthetic_samples) = target_percentage
    # Solving for synthetic_samples:
    # synthetic_samples = (poisoned_samples / target_percentage) - total_samples
    
    required_total_samples = poisoned_sample_count / target_poison_percentage
    current_total_samples = get_current_dataset_size()
    
    synthetic_samples_needed = required_total_samples - current_total_samples
    
    return max(0, int(synthetic_samples_needed))

# Example usage
estimated_poison = 100  # Assume 100 poisoned samples
synthetic_needed = calculate_dilution_ratio(estimated_poison, target_poison_percentage=0.01)
print(f"Add {synthetic_needed} synthetic samples to dilute poison to <1%")
```

### Quality Assurance for Synthetic Data

**Ensure synthetic data is high-quality and doesn't introduce new biases:**

```python
class SyntheticDataValidator:
    """Validate quality of synthetic data"""
    
    def validate_batch(self, synthetic_samples, original_dataset):
        """Check synthetic data quality"""
        validation_results = {
            'passed': True,
            'issues': []
        }
        
        # Check 1: Distribution similarity
        synthetic_distribution = calculate_label_distribution(synthetic_samples)
        original_distribution = calculate_label_distribution(original_dataset)
        
        for label, synth_freq in synthetic_distribution.items():
            orig_freq = original_distribution.get(label, 0)
            if abs(synth_freq - orig_freq) > 0.10:  # >10% difference
                validation_results['issues'].append({
                    'type': 'distribution_mismatch',
                    'label': label,
                    'synthetic_freq': synth_freq,
                    'original_freq': orig_freq
                })
        
        # Check 2: Duplicate detection (ensure diversity)
        duplicates = find_duplicates_in_batch(synthetic_samples)
        if len(duplicates) / len(synthetic_samples) > 0.05:  # >5% duplicates
            validation_results['issues'].append({
                'type': 'excessive_duplicates',
                'duplicate_rate': len(duplicates) / len(synthetic_samples)
            })
            validation_results['passed'] = False
        
        # Check 3: Realistic language (for text)
        for sample in random.sample(synthetic_samples, min(100, len(synthetic_samples))):
            perplexity = calculate_text_perplexity(sample['text'])
            if perplexity > 500:  # Unrealistically high perplexity
                validation_results['issues'].append({
                    'type': 'unrealistic_sample',
                    'sample_id': sample.get('id'),
                    'perplexity': perplexity
                })
        
        # Check 4: Label consistency
        mislabeled = detect_likely_mislabeled_samples(synthetic_samples)
        if len(mislabeled) > 0:
            validation_results['issues'].append({
                'type': 'label_inconsistency',
                'count': len(mislabeled)
            })
            validation_results['passed'] = False
        
        return validation_results
```

### Integration into Training Pipeline

```python
def augmented_training_pipeline(original_data, synthetic_generator, poison_estimate=0):
    """Training pipeline with synthetic data augmentation"""
    
    # Generate synthetic data
    if poison_estimate > 0:
        # Dilution mode: generate enough to reduce poison impact
        synthetic_count = calculate_dilution_ratio(poison_estimate, target_poison_percentage=0.01)
    else:
        # Standard augmentation: 20% synthetic
        synthetic_count = int(len(original_data) * 0.2)
    
    synthetic_data = synthetic_generator.generate_samples(synthetic_count)
    
    # Validate synthetic data
    validator = SyntheticDataValidator()
    validation = validator.validate_batch(synthetic_data, original_data)
    
    if not validation['passed']:
        raise ValueError(f"Synthetic data validation failed: {validation['issues']}")
    
    # Combine datasets
    combined_data = original_data + synthetic_data
    
    # Shuffle to distribute synthetic samples
    random.shuffle(combined_data)
    
    # Train model
    model = train_model(combined_data)
    
    # Log synthetic data usage
    log_training_metadata({
        'original_samples': len(original_data),
        'synthetic_samples': len(synthetic_data),
        'synthetic_ratio': len(synthetic_data) / len(combined_data),
        'estimated_poison_dilution': poison_estimate / len(combined_data) if poison_estimate > 0 else 0
    })
    
    return model
```

## Limitations & Trade-offs

**Limitations:**
- **Quality Risk**: Low-quality synthetic data can degrade model performance or introduce new biases
- **Overfitting Risk**: Models may overfit to synthetic patterns if generation process is too simplistic
- **Does Not Remove Poison**: Dilutes impact but does not eliminate poisoned samples
- **Generation Cost**: Creating high-quality synthetic data requires computational resources
- **Distribution Mismatch**: Synthetic data may not perfectly match real-world distribution

**Trade-offs:**
- **Quantity vs. Quality**: More synthetic data provides better dilution but risks quality degradation
- **Diversity vs. Realism**: Highly diverse synthetic data improves robustness but may lack realism
- **Augmentation Ratio vs. Training Time**: More synthetic data improves defense but increases training time

**Bypass Scenarios:**
- **Attacker adapts to synthetic patterns**: If synthetic generation is predictable, attacker can design poison to evade it
- **Massive poisoning**: Attacker injects so much poison that even dilution is ineffective
- **Synthetic data poisoning**: Attacker compromises synthetic data generation process itself

## Testing & Validation

**Functional Testing:**
1. **Quality Validation**: Verify synthetic data passes quality checks
2. **Dilution Effectiveness**: Measure reduction in poison impact with varying synthetic ratios
3. **Model Performance**: Ensure model trained on synthetic-augmented data maintains accuracy
4. **Distribution Matching**: Confirm synthetic data matches original distribution

**Security Testing:**
1. **Poison Resistance**: Test model resilience to poisoning with and without synthetic augmentation
2. **Backdoor Dilution**: Verify synthetic data reduces backdoor success rate
3. **Quality Attacks**: Test robustness against low-quality synthetic data injection

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Synthetic Data Generation: Augment training data with known-clean synthetic examples to dilute impact of poisoned samples."
> — Extracted from RAG Data Poisoning mitigation section

## Related

- **Combines with**: [[mitigations/data-validation-pipelines]], [[mitigations/data-diversity-and-redundancy]], [[mitigations/adversarial-training]]
- **Enables**: Poison dilution, distribution balancing, robustness improvement
