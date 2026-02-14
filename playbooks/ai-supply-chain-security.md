---
title: "AI Supply Chain Security Testing"
tags:
  - type/playbook
  - target/ml-model
  - target/training-pipeline
  - source/developers-playbook-llm
maturity: reviewed
created: 2026-02-14
updated: 2026-02-14
---

# AI Supply Chain Security Testing

## Overview

A comprehensive security assessment targeting the entire AI supply chain—all stages and components that go into building and running AI systems. This engagement scrutinizes data sources, training processes, model artifacts, infrastructure (ML pipelines, CI/CD, cloud services), and third-party dependencies (open-source models, pre-trained embeddings, external APIs).

AI supply chains expand the attack surface far beyond traditional software, involving models, data, pipelines, and AI-specific dependencies. High-profile incidents including backdoored models on public repositories, compromised ML libraries, and data poisoning attacks demonstrate that securing the AI pipeline is critical from raw data through production deployment.

## Scope & Prerequisites

### In Scope

- **Custom Model Development**: Organizations training models with custom data pipelines
- **Fine-Tuned Models**: Systems using base models with adapters, LoRA, or fine-tuning
- **Pre-Trained Model Integration**: Applications using models from HuggingFace, marketplaces, vendors
- **ML Platform Deployments**: Systems built on SageMaker, Azure ML, Vertex AI, similar platforms
- **Hybrid Pipelines**: Systems combining multiple data sources, model artifacts, deployment mechanisms

### Out of Scope

- Direct model behavior testing and prompt injection (see [[playbooks/llm-application-red-team|LLM Application Red Team]])
- Application-layer security beyond ML components
- Network infrastructure security (unless directly relevant to ML pipeline)
- Physical security of data centers or hardware
- Social engineering targeting staff

### Prerequisites

- Complete supply chain architecture documentation (data sources through deployment)
- Access to data sources, model artifacts, CI/CD pipelines
- SBOM (Software Bill of Materials) for AI components
- Infrastructure access (ML platforms, container registries)
- Training data access documentation
- Model provenance and lineage tracking information

## Methodology

### Phase 1: Supply Chain Mapping

**Objectives**: Comprehensively map the AI development and deployment pipeline

**Activities**:
- **Data Flow Analysis**: Diagram data movement from sources through preprocessing to training
- **Model Artifact Tracking**: Map model development, storage, versioning, deployment paths
- **Dependency Inventory**: Catalog all third-party components (libraries, frameworks, pre-trained models, APIs)
- **Infrastructure Mapping**: Document ML platforms, CI/CD systems, container registries, model serving
- **Access Control Review**: Identify read/write access to data sources, model artifacts, deployment systems
- **Provenance Documentation**: Trace model lineage from data to deployment

**Evidence Collected**:
- Complete supply chain architecture diagram
- Dependency inventory with versions and sources
- Access control matrix
- Data flow and model artifact flow diagrams
- SBOM for AI components

### Phase 2: Data Supply Security Testing

**Objectives**: Validate security of training data sources and pipelines

**Activities**:
- **Data Source Access Control**: Test permissions on data storage (S3, data lakes, databases)
- **Data Ingestion Security**: Assess APIs, webhooks, upload mechanisms for injection vulnerabilities
- **Data Poisoning Simulation**: Attempt to inject malicious samples into training data sources
- **Data Validation Testing**: Evaluate outlier detection or validation catching poisoned data
- **Data Storage Security**: Test encryption, access logs, backup integrity
- **Provenance Verification**: Validate data lineage tracking and integrity checks

**Evidence Collected**:
- Successful data poisoning demonstrations
- Access control bypass evidence
- Data validation gap analysis
- Provenance tracking effectiveness assessment

### Phase 3: Model Artifact Security Testing

**Objectives**: Assess security of model files, containers, and storage

**Activities**:
- **Artifact Scanning**: Scan model files for anomalies, backdoors, metadata issues
- **Integrity Verification**: Test checksum validation, model signing, tamper detection
- **Model Registry Security**: Assess access controls on model registries (MLflow, model stores)
- **Container Security**: Review Docker images for vulnerabilities, exposed secrets, malicious code
- **Backdoor Detection**: Identify or inject backdoor triggers in model artifacts
- **Model Theft Simulation**: Test for model extraction via APIs or exposed artifacts

**Evidence Collected**:
- Model artifact security assessment
- Integrity check effectiveness
- Backdoor detection results
- Model registry access control findings

### Phase 4: Dependency and Library Security

**Objectives**: Evaluate third-party component security

**Activities**:
- **Dependency Scanning**: Check ML libraries and frameworks against CVE databases
- **Pre-Trained Model Verification**: Validate source and integrity of models from HuggingFace, vendors
- **Library Vulnerability Exploitation**: Attempt to exploit known vulnerabilities in ML dependencies
- **Malicious Package Detection**: Test for typosquatting, compromised packages, supply chain attacks
- **Version Management Review**: Assess whether outdated or vulnerable versions are in use
- **SBOM Validation**: Verify Software Bill of Materials completeness and accuracy

**Evidence Collected**:
- Vulnerable dependency inventory
- Exploitation proof-of-concepts
- Model source verification results
- SBOM completeness assessment

### Phase 5: CI/CD and Infrastructure Security

**Objectives**: Test ML pipeline infrastructure and deployment security

**Activities**:
- **CI/CD Pipeline Penetration**: Test build systems, deployment scripts, automation for vulnerabilities
- **Secrets Management Audit**: Search for hardcoded credentials, API keys, secrets in code/configs
- **ML Platform Security**: Assess SageMaker, Azure ML, Vertex AI, or custom platform configurations
- **Container Registry Security**: Test access controls on Docker registries or model artifact storage
- **Infrastructure Misconfiguration**: Test for exposed S3 buckets, weak IAM policies, open ports
- **Deployment Process Testing**: Validate integrity checks, approval gates, deployment validation

**Evidence Collected**:
- CI/CD vulnerabilities and exploitation PoCs
- Exposed secrets inventory
- Platform misconfiguration findings
- Deployment security gap analysis

### Phase 6: Reporting and Remediation Guidance

**Activities**:
- Findings analysis, deduplication, and risk prioritization
- Root cause identification for supply chain vulnerabilities
- Framework mapping (NIST, SLSA, SSDF, ATLAS)
- Remediation roadmap development with effort estimates
- Executive summary and business impact assessment
- Evidence package preparation

## Techniques Covered

| ID | Technique | Relevance |
|----|-----------|-----------|
| - | [[techniques/rag-data-poisoning\|Data Poisoning]] | Training data manipulation |
| - | [[techniques/model-tampering\|Model Tampering]] | Model artifact compromise |
| - | [[techniques/model-extraction\|Model Extraction and Theft]] | Model IP theft |
| - | Supply Chain Compromise | Dependency and library attacks |
| - | [[techniques/secrets-in-prompts-and-logs\|Secrets in Prompts and Logs]] | Credential exposure |
| - | [[techniques/weak-access-segmentation\|Weak Access Segmentation]] | Insufficient access controls |
| - | [[techniques/insecure-model-gateway-config\|Insecure Model Gateway Configuration]] | Infrastructure misconfigurations |
| - | [[techniques/insufficient-telemetry-and-tracing\|Insufficient Telemetry and Tracing]] | Monitoring gaps |

## Tooling

### Supply Chain Analysis

**Tools**:
- **Dependency scanners**: Snyk, Trivy, Safety, pip-audit, npm audit
- **Container scanners**: Trivy, Clair, Anchore
- **Model scanning**: ModelScan, PickleInspect, custom artifact analysis scripts
- **SBOM generators**: Syft, CycloneDX, SPDX tools

### Security Testing

**Tools**:
- **CI/CD security**: GitLeaks, TruffleHog, gitleaks-action
- **Infrastructure scanning**: ScoutSuite, Prowler, CloudSploit
- **Access testing**: AWS IAM Policy Simulator, Azure Policy Analyzer
- **Backdoor detection**: ART (Adversarial Robustness Toolbox), custom triggers

### Reproducibility Standards

All findings include:
- Complete attack path from supply chain entry to exploitation
- Configuration snapshots (versions, dependencies, infrastructure state)
- Exploitation scripts and reproduction steps
- Impact assessment (what access was gained, what data was compromised)

## Deliverables

1. **Executive Summary Report** (5-8 pages)
   - Critical/High findings with business impact
   - Supply chain risk score
   - Strategic recommendations (SBOM, provenance, access controls)
   - Industry baseline comparison

2. **Technical Findings Report** (25-40 pages)
   - Detailed vulnerability write-ups per supply chain component
   - PoC evidence (exploitation paths, access demonstrations)
   - Root cause analysis and mitigation guidance
   - Framework mapping (NIST, SLSA, SSDF, ATLAS)

3. **Framework Compliance Matrix**
   - NIST AI RMF alignment
   - SLSA (Supply Chain Levels for Software Artifacts) assessment
   - SSDF (Secure Software Development Framework) mapping
   - MITRE ATLAS supply chain technique coverage

4. **Risk-Prioritized Remediation Roadmap**
   - Phased mitigation plan: Immediate → Short-term → Long-term
   - Effort estimates per fix
   - Risk reduction impact scores
   - Validation criteria and retest guidance

5. **Evidence Package**
   - Supply chain architecture diagrams
   - Dependency vulnerability reports
   - Data poisoning demonstrations
   - Model artifact compromise PoCs
   - CI/CD exploitation scripts
   - Secrets and misconfigurations inventory

## Sources

> Supply chain security methodology and attack patterns for ML systems.
> — [[sources/bibliography#Developer's Playbook for LLM Security]]

> Framework alignment based on NIST AI RMF, SLSA, and SSDF.
> — [[frameworks/atlas/MITRE-ATLAS|MITRE ATLAS Framework]]
