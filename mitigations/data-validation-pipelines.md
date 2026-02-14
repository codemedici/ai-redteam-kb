---
title: "Data Validation Pipelines"
tags:
  - type/mitigation
  - target/rag
  - target/ml-model
  - target/training-pipeline
  - source/adversarial-ai
atlas: AML.M0024
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Data Validation Pipelines

## Summary

Data validation pipelines implement multi-stage automated checks that detect anomalies, inconsistencies, and malicious patterns before data enters training systems or RAG knowledge bases. This defense-in-depth approach combines schema validation, statistical analysis, content scanning, and anomaly detection to identify poisoned samples, format violations, and suspicious patterns. Validation pipelines serve as the first line of defense against data poisoning by rejecting or quarantining malicious content before it can corrupt model behavior.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Detects poisoned documents, embeddings, and malicious patterns before RAG indexing |
| | [[techniques/data-poisoning-attacks]] | Identifies label flipping, trigger injection, and clean-label attacks in training data |
| | [[techniques/embedding-poisoning]] | Catches manipulated embeddings and semantic drift before production use |
| | [[techniques/pii-in-corpus]] | Scans for sensitive information before data exposure |

## Implementation

### Multi-Stage Validation Architecture

**Validation pipeline workflow:**

```
Data Ingestion
    ↓
Stage 1: Format & Schema Validation
    ↓
Stage 2: Content Scanning (malicious patterns, PII, prompt injection)
    ↓
Stage 3: Statistical Anomaly Detection
    ↓
Stage 4: Semantic Consistency Checks
    ↓
Stage 5: Duplicate Detection
    ↓
    ├─ PASS → Production or Quarantine (based on trust score)
    └─ FAIL → Rejection + Logging
```

### Stage 1: Format & Schema Validation

**Enforce structural integrity:**

```python
class FormatValidator:
    """Validate data format and schema compliance"""
    
    def __init__(self, schema_definition):
        self.schema = schema_definition
    
    def validate_document(self, document):
        """Comprehensive format validation"""
        checks = []
        
        # Check 1: Required fields present
        required_fields = self.schema.get('required_fields', [])
        for field in required_fields:
            if field not in document:
                checks.append({
                    'check': 'required_field',
                    'status': 'fail',
                    'field': field,
                    'message': f'Missing required field: {field}'
                })
        
        # Check 2: Field types correct
        for field, expected_type in self.schema.get('field_types', {}).items():
            if field in document:
                actual_type = type(document[field]).__name__
                if actual_type != expected_type:
                    checks.append({
                        'check': 'field_type',
                        'status': 'fail',
                        'field': field,
                        'message': f'Type mismatch: expected {expected_type}, got {actual_type}'
                    })
        
        # Check 3: Value ranges valid
        for field, constraints in self.schema.get('value_constraints', {}).items():
            if field in document:
                value = document[field]
                
                # Numeric range check
                if 'min' in constraints and value < constraints['min']:
                    checks.append({
                        'check': 'value_range',
                        'status': 'fail',
                        'field': field,
                        'message': f'Value {value} below minimum {constraints["min"]}'
                    })
                
                # String length check
                if 'max_length' in constraints and len(value) > constraints['max_length']:
                    checks.append({
                        'check': 'string_length',
                        'status': 'fail',
                        'field': field,
                        'message': f'Length {len(value)} exceeds maximum {constraints["max_length"]}'
                    })
                
                # Enum validation
                if 'allowed_values' in constraints and value not in constraints['allowed_values']:
                    checks.append({
                        'check': 'enum_validation',
                        'status': 'fail',
                        'field': field,
                        'message': f'Value {value} not in allowed set'
                    })
        
        # Check 4: Character encoding valid
        if isinstance(document.get('text'), str):
            try:
                document['text'].encode('utf-8')
            except UnicodeEncodeError:
                checks.append({
                    'check': 'encoding',
                    'status': 'fail',
                    'message': 'Invalid character encoding'
                })
        
        failed_checks = [c for c in checks if c['status'] == 'fail']
        return len(failed_checks) == 0, failed_checks
```

### Stage 2: Content Scanning

**Detect malicious patterns and sensitive information:**

```python
import re

class ContentScanner:
    """Scan content for malicious patterns and sensitive data"""
    
    def __init__(self):
        # Prompt injection pattern signatures
        self.injection_patterns = [
            r'ignore\s+(all\s+)?previous\s+instructions',
            r'system\s*:\s*you\s+are\s+now',
            r'</SYSTEM_INSTRUCTION>',  # Tag injection attempt
            r'___\s*SYSTEM\s+OVERRIDE',
            r'new\s+instructions\s*:',
            r'disregard\s+(all\s+)?(prior|previous|above)',
            r'reveal\s+your\s+(system\s+)?prompt',
            r'what\s+are\s+your\s+(system\s+)?instructions'
        ]
        
        # PII patterns (simplified)
        self.pii_patterns = {
            'ssn': r'\b\d{3}-\d{2}-\d{4}\b',
            'credit_card': r'\b\d{4}[\s-]?\d{4}[\s-]?\d{4}[\s-]?\d{4}\b',
            'email': r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b',
            'phone': r'\b\d{3}[-.]?\d{3}[-.]?\d{4}\b'
        }
        
        # Malicious code patterns
        self.code_patterns = [
            r'<script[^>]*>',
            r'javascript:',
            r'eval\s*\(',
            r'exec\s*\(',
            r'__import__\s*\('
        ]
    
    def scan_document(self, document_text):
        """Multi-pattern content scanning"""
        findings = []
        
        # Scan for prompt injection
        for pattern in self.injection_patterns:
            matches = re.findall(pattern, document_text, re.IGNORECASE)
            if matches:
                findings.append({
                    'type': 'prompt_injection',
                    'severity': 'critical',
                    'pattern': pattern,
                    'matches': matches
                })
        
        # Scan for PII
        for pii_type, pattern in self.pii_patterns.items():
            matches = re.findall(pattern, document_text)
            if matches:
                findings.append({
                    'type': 'pii_detected',
                    'severity': 'high',
                    'pii_type': pii_type,
                    'count': len(matches)
                })
        
        # Scan for malicious code
        for pattern in self.code_patterns:
            matches = re.findall(pattern, document_text, re.IGNORECASE)
            if matches:
                findings.append({
                    'type': 'malicious_code',
                    'severity': 'critical',
                    'pattern': pattern,
                    'matches': matches
                })
        
        # Detect unusual Unicode characters (potential clean-label attack)
        unusual_chars = self.detect_unusual_unicode(document_text)
        if unusual_chars:
            findings.append({
                'type': 'unusual_characters',
                'severity': 'medium',
                'characters': unusual_chars
            })
        
        critical_findings = [f for f in findings if f['severity'] == 'critical']
        return len(critical_findings) == 0, findings
    
    def detect_unusual_unicode(self, text):
        """Detect visually similar Unicode substitutions"""
        # Characters that look like ASCII but aren't (homoglyph attack)
        suspicious_chars = []
        for char in text:
            if ord(char) > 127:  # Non-ASCII
                # Check if it's a common homoglyph
                if char in ['А', 'В', 'Е', 'К', 'М', 'Н', 'О', 'Р', 'С', 'Т', 'Х']:  # Cyrillic
                    suspicious_chars.append(char)
        return suspicious_chars
```

### Stage 3: Statistical Anomaly Detection

**Identify outliers and distribution shifts:**

```python
import numpy as np
from scipy import stats

class StatisticalValidator:
    """Statistical anomaly detection for data validation"""
    
    def __init__(self, baseline_statistics):
        self.baseline = baseline_statistics
    
    def validate_distribution(self, new_samples, feature_extractors):
        """Detect distribution shifts and outliers"""
        anomalies = []
        
        for feature_name, extractor in feature_extractors.items():
            # Extract feature values from new samples
            feature_values = [extractor(sample) for sample in new_samples]
            
            # Get baseline statistics
            baseline = self.baseline.get(feature_name, {})
            
            # Check 1: Mean shift detection
            if 'mean' in baseline and 'std' in baseline:
                sample_mean = np.mean(feature_values)
                expected_mean = baseline['mean']
                expected_std = baseline['std']
                
                # Z-score test
                z_score = abs(sample_mean - expected_mean) / expected_std
                if z_score > 3:  # 3-sigma threshold
                    anomalies.append({
                        'type': 'mean_shift',
                        'feature': feature_name,
                        'z_score': z_score,
                        'severity': 'high'
                    })
            
            # Check 2: Outlier detection (IQR method)
            if 'q1' in baseline and 'q3' in baseline:
                q1 = baseline['q1']
                q3 = baseline['q3']
                iqr = q3 - q1
                
                outliers = [
                    v for v in feature_values 
                    if v < (q1 - 1.5 * iqr) or v > (q3 + 1.5 * iqr)
                ]
                
                if len(outliers) / len(feature_values) > 0.05:  # >5% outliers
                    anomalies.append({
                        'type': 'outlier_excess',
                        'feature': feature_name,
                        'outlier_rate': len(outliers) / len(feature_values),
                        'severity': 'medium'
                    })
            
            # Check 3: Distribution comparison (Kolmogorov-Smirnov test)
            if 'distribution_sample' in baseline:
                baseline_sample = baseline['distribution_sample']
                ks_statistic, p_value = stats.ks_2samp(baseline_sample, feature_values)
                
                if p_value < 0.01:  # Significant difference
                    anomalies.append({
                        'type': 'distribution_shift',
                        'feature': feature_name,
                        'ks_statistic': ks_statistic,
                        'p_value': p_value,
                        'severity': 'high'
                    })
        
        high_severity = [a for a in anomalies if a['severity'] == 'high']
        return len(high_severity) == 0, anomalies
```

**Feature extractors for text data:**

```python
def build_text_feature_extractors():
    """Feature extractors for statistical validation of text"""
    return {
        'document_length': lambda doc: len(doc.get('text', '')),
        'word_count': lambda doc: len(doc.get('text', '').split()),
        'avg_word_length': lambda doc: np.mean([len(w) for w in doc.get('text', '').split()]) if doc.get('text') else 0,
        'sentence_count': lambda doc: len(re.split(r'[.!?]', doc.get('text', ''))),
        'numeric_token_ratio': lambda doc: len(re.findall(r'\d+', doc.get('text', ''))) / max(len(doc.get('text', '').split()), 1),
        'special_char_ratio': lambda doc: len(re.findall(r'[^a-zA-Z0-9\s]', doc.get('text', ''))) / max(len(doc.get('text', '')), 1)
    }
```

### Stage 4: Semantic Consistency Checks

**For RAG embeddings: verify embedding matches document:**

```python
from sklearn.metrics.pairwise import cosine_similarity

def validate_embedding_consistency(document_text, embedding, embedding_model, threshold=0.95):
    """Ensure embedding accurately represents document"""
    # Generate fresh embedding from document
    fresh_embedding = embedding_model.encode(document_text)
    
    # Compare to provided embedding
    similarity = cosine_similarity([embedding], [fresh_embedding])[0][0]
    
    if similarity < threshold:
        return False, {
            'type': 'embedding_mismatch',
            'similarity': similarity,
            'threshold': threshold,
            'message': 'Provided embedding does not match document text'
        }
    
    return True, {'similarity': similarity}
```

### Stage 5: Duplicate Detection

**Identify near-duplicates with conflicting labels (label flipping indicator):**

```python
from sklearn.feature_extraction.text import TfidfVectorizer

class DuplicateDetector:
    """Detect duplicate and near-duplicate documents"""
    
    def __init__(self, existing_corpus):
        self.vectorizer = TfidfVectorizer()
        self.corpus_vectors = self.vectorizer.fit_transform(existing_corpus)
        self.corpus_labels = [doc.get('label') for doc in existing_corpus]
    
    def find_near_duplicates(self, new_document, similarity_threshold=0.9):
        """Find near-duplicate documents with different labels"""
        # Vectorize new document
        new_vector = self.vectorizer.transform([new_document['text']])
        
        # Compute similarity to existing corpus
        similarities = cosine_similarity(new_vector, self.corpus_vectors)[0]
        
        # Find high-similarity matches
        near_duplicates = []
        for idx, sim in enumerate(similarities):
            if sim >= similarity_threshold:
                # Check for label mismatch (potential label flipping)
                if new_document.get('label') != self.corpus_labels[idx]:
                    near_duplicates.append({
                        'index': idx,
                        'similarity': sim,
                        'existing_label': self.corpus_labels[idx],
                        'new_label': new_document.get('label'),
                        'type': 'label_mismatch'
                    })
        
        return near_duplicates
```

### Orchestration: Complete Validation Pipeline

```python
class DataValidationPipeline:
    """Orchestrate multi-stage validation"""
    
    def __init__(self, config):
        self.format_validator = FormatValidator(config['schema'])
        self.content_scanner = ContentScanner()
        self.statistical_validator = StatisticalValidator(config['baseline_stats'])
        self.duplicate_detector = DuplicateDetector(config['existing_corpus'])
    
    def validate(self, document):
        """Execute complete validation pipeline"""
        results = {
            'document_id': document.get('id'),
            'overall_status': 'pending',
            'stage_results': {},
            'findings': []
        }
        
        # Stage 1: Format validation
        passed, findings = self.format_validator.validate_document(document)
        results['stage_results']['format'] = {'passed': passed, 'findings': findings}
        if not passed:
            results['overall_status'] = 'rejected'
            results['findings'].extend(findings)
            return results
        
        # Stage 2: Content scanning
        passed, findings = self.content_scanner.scan_document(document.get('text', ''))
        results['stage_results']['content'] = {'passed': passed, 'findings': findings}
        if not passed:
            results['overall_status'] = 'rejected'
            results['findings'].extend(findings)
            return results
        
        # Stage 3: Statistical validation (on batches)
        # (executed at batch level, skipped for individual documents)
        
        # Stage 4: Duplicate detection
        duplicates = self.duplicate_detector.find_near_duplicates(document)
        if duplicates:
            results['stage_results']['duplicates'] = {'findings': duplicates}
            results['findings'].extend(duplicates)
            # Don't auto-reject, but flag for review
            results['overall_status'] = 'review_required'
        
        # If all stages passed
        if results['overall_status'] == 'pending':
            results['overall_status'] = 'passed'
        
        return results
```

## Limitations & Trade-offs

**Limitations:**
- **Cannot detect sophisticated clean-label attacks**: Subtle perturbations may pass all validation checks
- **False positives**: Legitimate content may trigger anomaly detection
- **Baseline dependency**: Statistical validation requires representative baseline; distribution shifts in legitimate data can trigger false alarms
- **Computational overhead**: Multi-stage validation adds latency

**Trade-offs:**
- **Security vs. Throughput**: Comprehensive validation slows data ingestion
- **Sensitivity vs. False Positive Rate**: Strict thresholds improve security but increase false rejections
- **Automation vs. Accuracy**: Fully automated validation may miss attacks that human reviewers would catch

## Testing & Validation

**Functional Testing:**
1. **Format Validation**: Submit documents with schema violations, verify rejection
2. **Pattern Detection**: Submit documents with known prompt injection patterns, verify detection
3. **Statistical Sensitivity**: Inject outliers, verify anomaly detection triggers
4. **Duplicate Detection**: Submit near-duplicates with conflicting labels, verify flagging

**Security Testing:**
1. **Evasion Testing**: Attempt to craft poisoned data that passes all validation stages
2. **Encoding Attacks**: Test Unicode substitution and obfuscation techniques
3. **Clean-Label Simulation**: Submit imperceptibly modified data, measure detection rate

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Comprehensive Data Validation Pipelines: Implement multi-stage validation checking for anomalies, inconsistencies, and malicious patterns before data enters training or RAG systems; include schema validation, range checks, and format verification."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Statistical Analysis: Continuous monitoring of data distributions, label frequencies, and feature statistics; alert on significant shifts."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

## Related

- **Combines with**: [[mitigations/data-quarantine-procedures]], [[mitigations/source-validation-and-trust-scoring]], [[mitigations/anomaly-detection-architecture]]
- **Feeds into**: Quarantine decisions, trust score updates, incident response triggers
