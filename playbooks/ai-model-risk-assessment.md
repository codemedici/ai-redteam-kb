---
title: "AI Model Risk Assessment"
tags:
  - type/playbook
  - target/ml-model
  - target/llm
  - source/developers-playbook-llm
maturity: reviewed
created: 2026-02-14
updated: 2026-02-14
---

# AI Model Risk Assessment

## Overview

A comprehensive risk analysis and security review of an AI system's design, training data, algorithms, and operational context. Unlike active attack simulation engagements, this is a collaborative, review-based assessment working with developers and stakeholders to identify where the system might fail or be misused.

The engagement encompasses risks of bias, fairness, performance degradation, privacy, and security vulnerabilities, providing a prioritized overview of model-related risks to the business. Organizations in regulated industries (finance, healthcare) are increasingly required to conduct model risk management for AI/ML systems, and even outside regulated sectors, understanding the complete risk profile is essential for responsible deployment.

## Scope & Prerequisites

### In Scope

- **Credit Scoring Models**: Financial risk assessment models requiring regulatory compliance
- **Healthcare AI Systems**: Medical diagnosis, treatment recommendation, patient risk models
- **Content Moderation Systems**: Automated content filtering, classification, moderation models
- **Recommendation Engines**: Product recommendations, content suggestions, personalization models
- **Predictive Analytics**: Forecasting, demand prediction, trend analysis models
- **Autonomous Decision Systems**: Models making automated decisions with business or safety impact

### Out of Scope

- Active adversarial attack simulation (see [[playbooks/llm-application-red-team|LLM Application Red Team]])
- Source code security review (requires separate application security assessment)
- Training infrastructure security (see [[playbooks/ai-supply-chain-security|AI Supply Chain Security]])
- Performance optimization or accuracy improvement
- Fine-tuning or model development

### Prerequisites

- Model architecture documentation
- Training data documentation (sources, preprocessing, statistics)
- Model performance metrics and benchmarks
- Deployment and operational context documentation
- Existing model validation or testing results
- Stakeholder access (data scientists, ML engineers, product owners)

## Methodology

### Phase 1: Model and Data Documentation Review

**Objectives**: Understand model design, training data, and operational context

**Activities**:
- Review model architecture, algorithms, hyperparameters
- Examine training data sources, provenance, preprocessing
- Assess data quality, representativeness, potential biases
- Review model performance metrics across different demographics
- Understand deployment context and use cases
- Map model inputs, outputs, and decision pathways

**Evidence Collected**:
- Model architecture summary
- Training data analysis report
- Performance metric review
- Use case and deployment context documentation

### Phase 2: Risk Identification and Scoring

**Objectives**: Systematically identify and prioritize risks

**Activities**:
- **Performance & Reliability Risks**: Accuracy, robustness, stability, degradation
- **Adversarial Vulnerabilities**: Susceptibility to known attacks (evasion, poisoning, inversion)
- **Data and Privacy Risks**: Training data quality, PII exposure, memorization
- **Ethical & Bias Concerns**: Unintended biases, fairness, discriminatory outcomes
- **Compliance & Governance**: Regulatory alignment, documentation completeness
- **Operational Risks**: Model drift, monitoring gaps, incident response readiness

**Evidence Collected**:
- Risk register with qualitative/quantitative scores
- Bias analysis results
- Adversarial vulnerability assessment
- Compliance gap analysis

### Phase 3: Adversarial Robustness Assessment

**Objectives**: Evaluate model resilience to known attack patterns

**Activities**:
- **Evasion Attacks**: Test input manipulations that cause misclassification
- **Poisoning Vulnerability**: Assess susceptibility to training data poisoning
- **Inversion Risks**: Test for training data reconstruction or membership inference
- **Model Extraction**: Evaluate API-based model theft risks
- **Prompt Injection** (LLMs): Test resistance to prompt manipulation

**Evidence Collected**:
- Adversarial example demonstrations
- Robustness metrics and scores
- Attack surface analysis

### Phase 4: Fairness and Bias Evaluation

**Objectives**: Identify discriminatory patterns across demographics

**Activities**:
- Disaggregated performance analysis across protected attributes
- Statistical parity and equalized odds testing
- Fairness metric calculations (demographic parity, equal opportunity)
- Bias source identification (data vs. algorithmic)
- Disparate impact assessment

**Evidence Collected**:
- Fairness metric scorecard
- Bias source analysis
- Disaggregated performance reports
- Recommendations for bias mitigation

### Phase 5: Compliance and Governance Review

**Objectives**: Assess regulatory alignment and governance processes

**Activities**:
- Map model to applicable regulations (GDPR, CCPA, SR 11-7, FDA guidance)
- Review model documentation completeness
- Assess monitoring and drift detection mechanisms
- Evaluate incident response and model rollback procedures
- Review access controls and audit logging
- Validate explainability and interpretability capabilities

**Evidence Collected**:
- Regulatory compliance matrix
- Governance process assessment
- Documentation gap analysis
- Monitoring and incident response evaluation

### Phase 6: Reporting and Recommendations

**Activities**:
- Risk prioritization and scoring
- Framework mapping (NIST AI RMF, OWASP, ATLAS)
- Remediation recommendations with effort estimates
- Executive summary and business impact assessment
- Continuous improvement roadmap

## Techniques Covered

| ID | Technique | Relevance |
|----|-----------|-----------|
| - | [[techniques/prompt-injection|Prompt Injection]] | LLM-specific risks |
| - | [[techniques/model-extraction|Model Extraction]] | IP theft vulnerability |
| - | [[techniques/training-data-memorization|Training Data Memorization]] | Privacy risks |
| - | [[techniques/adversarial-robustness|Adversarial Robustness]] | Evasion and manipulation risks |
| - | [[techniques/rag-data-poisoning|Data Poisoning]] | Training data integrity |
| - | Bias and Fairness | Discriminatory outcomes |
| - | Model Drift | Performance degradation over time |

## Tooling

### Risk Assessment

**Tools**:
- **Bias detection**: AI Fairness 360, Fairlearn, Aequitas
- **Adversarial testing**: ART (Adversarial Robustness Toolbox), CleverHans, Foolbox
- **Explainability**: SHAP, LIME, InterpretML
- **Monitoring**: EvidentlyAI, WhyLabs, Arize

### Documentation and Reporting

**Frameworks**:
- NIST AI RMF
- Model Cards
- FactSheets
- ISO/IEC 23894 (AI Risk Management)

## Deliverables

1. **Executive Summary Report** (5-8 pages)
   - Overall risk score
   - Top 5 critical risks with business impact
   - Strategic recommendations
   - Regulatory compliance status

2. **Comprehensive Risk Report** (30-50 pages)
   - Risk identification across all categories
   - Bias and fairness analysis
   - Adversarial vulnerability assessment
   - Compliance gap analysis
   - Operational risk evaluation
   - Framework mapping (NIST AI RMF)

3. **Risk-Prioritized Remediation Roadmap**
   - Phased mitigation plan
   - Effort estimates per mitigation
   - Risk reduction impact scores
   - Continuous improvement recommendations

4. **Framework Compliance Matrix**
   - NIST AI RMF alignment
   - OWASP LLM Top 10 mapping (if applicable)
   - Regulatory requirement coverage

5. **Evidence Package**
   - Bias analysis results
   - Adversarial testing reports
   - Performance disaggregation analysis
   - Compliance checklists

## Sources

> Model risk management methodology for AI/ML systems.
> — [[sources/bibliography#Developer's Playbook for LLM Security]]

> Framework alignment based on NIST AI RMF.
> — [[frameworks/OWASP-top-10-LLM|OWASP Top 10 for LLM Applications]]
