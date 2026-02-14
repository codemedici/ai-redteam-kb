---
title: "ML SBOM and Model Lineage"
tags:
  - type/mitigation
  - target/ml-model
  - target/llm
  - target/training-pipeline
  - source/adversarial-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# ML SBOM and Model Lineage

## Summary

ML Software Bill of Materials (SBOM) and model lineage tracking provides comprehensive documentation of a model's provenance—its base model source, fine-tuning datasets, training frameworks, software dependencies, and the full chain of transformations from origin to deployment. Analogous to software SBOMs used in traditional supply chain security, ML SBOMs enable organizations to rapidly assess exposure when vulnerabilities are discovered in upstream components, trace poisoning attacks to their source, and verify that deployed models meet governance requirements. This mitigation is essential for defending against supply chain model poisoning, where attackers distribute backdoored artifacts through trusted channels.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Documents model provenance enabling rapid identification of affected downstream applications when poisoned base models are discovered |
| AML.T0018 | [[techniques/backdoor-poisoning]] | Lineage tracking enables forensic tracing of backdoor insertion to specific training stages or data sources |
| | [[techniques/data-poisoning-attacks]] | Dataset lineage documentation supports root cause analysis when poisoning is detected |
| | [[techniques/third-party-model-risks]] | SBOM visibility into all external dependencies enables proactive vulnerability management |

## Implementation

### ML SBOM Schema

A comprehensive ML SBOM should document:

```yaml
ml_sbom:
  model_id: "sentiment-classifier-v2.1.0"
  model_hash: "sha256:a3f2b1c..."
  created: "2026-02-14T10:30:00Z"
  
  # Base model provenance
  base_model:
    name: "bert-base-uncased"
    source: "huggingface.co/google-bert/bert-base-uncased"
    version: "1.0"
    download_date: "2026-01-15"
    download_hash: "sha256:7d4e9a8..."
    license: "Apache-2.0"
    
  # Training data lineage
  training_data:
    - name: "internal-sentiment-dataset-v3"
      source: "internal"
      hash: "sha256:b5c3d2e..."
      size: "50,000 samples"
      date_collected: "2025-12-01"
    - name: "stanford-sentiment-treebank"
      source: "https://nlp.stanford.edu/sentiment/"
      version: "2.0"
      download_hash: "sha256:e1f4a5b..."
      
  # Fine-tuning details
  fine_tuning:
    framework: "transformers==4.38.0"
    method: "full fine-tuning"
    epochs: 5
    learning_rate: 2e-5
    hardware: "4x A100 GPU"
    training_start: "2026-02-10T08:00:00Z"
    training_end: "2026-02-10T14:30:00Z"
    
  # Software dependencies
  dependencies:
    - name: "torch"
      version: "2.2.0"
      hash: "sha256:..."
    - name: "transformers"
      version: "4.38.0"
      hash: "sha256:..."
    - name: "tokenizers"
      version: "0.15.1"
      hash: "sha256:..."
      
  # Validation results
  validation:
    accuracy: 0.94
    f1_score: 0.93
    adversarial_robustness_score: 0.82
    security_scan: "passed"
    human_review: "approved"
    
  # Deployment metadata
  deployment:
    environment: "production"
    deployment_date: "2026-02-14"
    approved_by: "ml-security-team"
```

### Lineage Tracking Integration

Integrate SBOM generation into MLOps pipelines so lineage is captured automatically at each stage:

1. **Data acquisition**: Record source, download hash, date for all training data
2. **Preprocessing**: Log transformations applied to data
3. **Training**: Capture base model source, hyperparameters, framework versions
4. **Evaluation**: Record validation metrics and security scan results
5. **Deployment**: Document deployment target, approval chain, configuration

### Vulnerability Response Workflow

When a vulnerability is disclosed in an upstream component (e.g., poisoned base model on Hugging Face):

1. **Query all SBOMs** for affected component (base model, dataset, library)
2. **Identify all downstream models** that incorporated the compromised artifact
3. **Assess impact**: Which production systems use affected models?
4. **Trigger response**: Quarantine, rollback, or re-train affected models
5. **Audit trail**: Document response actions in SBOM metadata

### Dependency Tracking

Track not just model weights but the full software stack:

- **Python packages**: All pip/conda dependencies with pinned versions and hashes
- **Framework versions**: PyTorch, TensorFlow, Hugging Face Transformers
- **System libraries**: CUDA, cuDNN, OS-level dependencies
- **Custom code**: Git commit hashes for training scripts and preprocessing code

## Limitations & Trade-offs

**Limitations:**
- **Cannot prevent poisoning**: SBOM documents provenance but doesn't block malicious artifacts
- **Maintenance burden**: Keeping SBOMs accurate requires disciplined MLOps processes
- **Incomplete upstream visibility**: External model providers may not publish their own SBOMs
- **Storage and complexity**: Comprehensive lineage tracking generates significant metadata

**Trade-offs:**
- **Documentation overhead vs. incident response speed**: Thorough SBOMs slow development but dramatically accelerate forensic analysis
- **Granularity vs. practicality**: More detailed lineage is more useful but harder to maintain

## Testing & Validation

**Functional Testing:**
1. **SBOM completeness**: Verify all required fields populated for every deployed model
2. **Vulnerability query**: Simulate upstream vulnerability; verify affected models identified within SLA
3. **Lineage accuracy**: Spot-check SBOM entries against actual training artifacts

**Security Testing:**
1. **Tamper detection**: Verify SBOM integrity (sign SBOMs with [[mitigations/content-signing-and-provenance|content signing]])
2. **Coverage audit**: Identify models deployed without SBOMs

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[frameworks/atlas/case-studies/poisongpt]] | [[frameworks/atlas/tactics/persistence]] | SBOM tracking would have enabled rapid identification of all systems using the poisoned LLM distributed via Hugging Face |

## Sources

> "SBOM for ML: Document model lineage: base model source, fine-tuning datasets, training frameworks. Track dependencies (Python packages, framework versions). Maintain audit trail of model provenance."
> — [[sources/bibliography#Adversarial AI]], p. 136-137
