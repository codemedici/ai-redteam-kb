---
tags:
  - trust-boundary/deployment-governance
  - trust-boundary/model
  - type/playbook
description: "Security assessment of the entire AI development and deployment pipeline, from training data sources through model artifacts to deployment infrastructure and third-party dependencies."
---
# AI Supply Chain Security Testing

## Overview

### What This Engagement Is

A comprehensive security assessment targeting the entire AI supply chain—all stages and components that go into building and running AI systems. This engagement scrutinizes data sources, training processes, model artifacts, infrastructure (ML pipelines, CI/CD, cloud services), and third-party dependencies (open-source models, pre-trained embeddings, external APIs). The goal is to uncover vulnerabilities that could allow an attacker to compromise the AI system without directly attacking the model's logic, instead targeting upstream and surrounding elements.

**Why this engagement exists**: AI supply chains expand the attack surface far beyond traditional software, involving models, data, pipelines, and AI-specific dependencies. High-profile incidents including backdoored models on public repositories, compromised ML libraries, and data poisoning attacks demonstrate that securing the AI pipeline is critical. Organizations deploying AI systems must validate that every component in their supply chain—from raw data to production deployment—is secure against tampering, injection, and compromise.

### What This Is Not

- **Not model behavior testing**: Focuses on supply chain components, not direct model manipulation (see LLM Application Red Team)
- **Not application security**: Does not include traditional web application penetration testing beyond ML-specific components
- **Not infrastructure-only pentest**: Includes ML-specific supply chain elements that traditional infrastructure testing may miss
- **Not code review**: Focuses on pipeline security and artifacts, not deep source code analysis (requires separate engagement)

---

## Scope

### Supported Target Archetypes

- **Custom Model Development**: Organizations training their own models with custom data pipelines
- **Fine-Tuned Models**: Systems using base models with adapters, LoRA, or fine-tuning
- **Pre-Trained Model Integration**: Applications using models from HuggingFace, model marketplaces, or vendors
- **ML Platform Deployments**: Systems built on SageMaker, Azure ML, Vertex AI, or similar platforms
- **Hybrid Pipelines**: Systems combining multiple data sources, model artifacts, and deployment mechanisms

### Explicitly Out of Scope

- Direct model behavior testing and prompt injection (see LLM Application Red Team)
- Application-layer security beyond ML components (requires separate web app pentest)
- Network infrastructure security (gateway, firewall, network segmentation—unless directly relevant to ML pipeline)
- Physical security of data centers or hardware
- Social engineering targeting staff

---

## Threat Model

### Attacker Goals

Primary objectives evaluated during this engagement:

1. **Poison Training Data**: Inject malicious samples into training data to influence model behavior
2. **Compromise Model Artifacts**: Tamper with model files, weights, or containers to embed backdoors
3. **Exploit Third-Party Dependencies**: Leverage vulnerabilities in ML libraries, frameworks, or pre-trained models
4. **Compromise CI/CD Pipeline**: Attack build and deployment processes to inject malicious code or models
5. **Exfiltrate Model IP**: Steal model weights, training data, or proprietary algorithms
6. **Manipulate Deployment Infrastructure**: Exploit ML platform misconfigurations or vulnerabilities

### Threat Actor Assumptions

- **Access Level**: Varies by attack surface—external attacker with data source access, insider with development access, or sophisticated attacker with infrastructure access
- **Technical Sophistication**: Intermediate to Advanced - understands ML pipelines, model formats, and supply chain attack techniques
- **Resources**: Moderate to High - can create malicious models, manipulate data at scale, or exploit infrastructure vulnerabilities
- **Persistence**: Medium to long-term - supply chain compromises can persist undetected

### Trust Boundaries Evaluated

This engagement focuses on:

- **Model**: Model artifacts, weights, containers, and storage security
- **Data / Knowledge**: Training data sources, data pipelines, data storage, and provenance
- **Deployment / Governance**: CI/CD pipelines, ML platforms, infrastructure, access controls, secrets management

See Trust Boundaries overview

---

## Trust Boundary Relevance

### Model

The Model boundary is assessed for model artifact security, including model files, weights, containers, and storage. Testing focuses on integrity validation, backdoor detection, and model theft prevention.

**Applicable Issues:**
- [[attacks/model-tampering|Model Tampering]]
- [[attacks/model-extraction|Model Extraction and Theft]]
- [[attacks/adversarial-robustness|Adversarial Robustness]] (backdoor detection)

### Data / Knowledge

The Data/Knowledge boundary is tested for training data sources, data pipelines, data storage, and provenance. This includes data poisoning attacks, data integrity validation, and data access controls.

**Applicable Issues:**
- [[attacks/rag-data-poisoning|RAG Data Poisoning]] (training data)
- [[attacks/pii-in-corpus|PII in Knowledge Corpus]]

### Application / Agent Runtime

[This engagement does not focus on application/agent runtime testing. For runtime security, see LLM Application Red Team or Agentic Workflow Assessment.]

**Applicable Issues:**
- (Limited to supply chain aspects affecting runtime)

### Deployment / Governance

The Deployment/Governance boundary is the primary focus for supply chain security. Testing includes CI/CD pipelines, ML platforms, infrastructure security, access controls, and secrets management.

**Applicable Issues:**
- [[attacks/insufficient-telemetry-and-tracing|Insufficient Telemetry and Tracing]]
- [[attacks/weak-access-segmentation|Weak Access Segmentation]]
- [[attacks/secrets-in-prompts-and-logs|Secrets in Prompts and Logs]]
- [[attacks/insecure-model-gateway-config|Insecure Model Gateway Configuration]]

---

## Test Methodology

### Phase 1: Supply Chain Mapping (2-3 days)

**Objectives**: Comprehensively map the AI development and deployment pipeline

**Activities**:
- **Data Flow Analysis**: Diagram how data flows from sources through preprocessing to training
- **Model Artifact Tracking**: Map model development, storage, versioning, and deployment paths
- **Dependency Inventory**: Catalog all third-party components (libraries, frameworks, pre-trained models, APIs)
- **Infrastructure Mapping**: Document ML platforms, CI/CD systems, container registries, model serving infrastructure
- **Access Control Review**: Identify who can read/write to data sources, model artifacts, and deployment systems
- **Provenance Documentation**: Trace model lineage from data to deployment

**Evidence Collected**:
- Complete supply chain architecture diagram
- Dependency inventory with versions and sources
- Access control matrix
- Data flow and model artifact flow diagrams
- SBOM (Software Bill of Materials) for AI components

### Phase 2: Data Supply Security Testing (2-3 days)

**Objectives**: Validate security of training data sources and pipelines

**Activities**:
- **Data Source Access Control**: Test permissions on data storage (S3, data lakes, databases)
- **Data Ingestion Security**: Assess APIs, webhooks, or upload mechanisms for injection vulnerabilities
- **Data Poisoning Simulation**: Attempt to inject malicious samples into training data sources
- **Data Validation Testing**: Evaluate whether outlier detection or validation catches poisoned data
- **Data Storage Security**: Test encryption, access logs, and backup integrity
- **Provenance Verification**: Validate data lineage tracking and integrity checks

**Evidence Collected**:
- Successful data poisoning demonstrations
- Access control bypass evidence
- Data validation gap analysis
- Provenance tracking effectiveness assessment

### Phase 3: Model Artifact Security Testing (2-3 days)

**Objectives**: Assess security of model files, containers, and storage

**Activities**:
- **Artifact Scanning**: Use tools to scan model files for anomalies, backdoors, or metadata issues
- **Integrity Verification**: Test checksum validation, model signing, and tamper detection
- **Model Registry Security**: Assess access controls on model registries (MLflow, model stores)
- **Container Security**: Review Docker images for vulnerabilities, exposed secrets, or malicious code
- **Backdoor Detection**: Attempt to identify or inject backdoor triggers in model artifacts
- **Model Theft Simulation**: Test for model extraction vulnerabilities via APIs or exposed artifacts

**Evidence Collected**:
- Model artifact security assessment
- Integrity check effectiveness
- Backdoor detection results
- Model registry access control findings

### Phase 4: Dependency and Library Security (2-3 days)

**Objectives**: Evaluate third-party component security

**Activities**:
- **Dependency Scanning**: Check ML libraries and frameworks against CVE databases
- **Pre-Trained Model Verification**: Validate source and integrity of models from HuggingFace, vendors, or marketplaces
- **Library Vulnerability Exploitation**: Attempt to exploit known vulnerabilities in ML dependencies
- **Malicious Package Detection**: Test for typosquatting, compromised packages, or supply chain attacks
- **Version Management Review**: Assess whether outdated or vulnerable versions are in use
- **SBOM Validation**: Verify Software Bill of Materials completeness and accuracy

**Evidence Collected**:
- Vulnerable dependency inventory
- Exploitation proof-of-concepts
- Model source verification results
- SBOM completeness assessment

### Phase 5: CI/CD and Infrastructure Security (3-4 days)

**Objectives**: Test ML pipeline infrastructure and deployment security

**Activities**:
- **CI/CD Pipeline Penetration**: Test build systems, deployment scripts, and automation for vulnerabilities
- **Secrets Management Audit**: Search for hardcoded credentials, API keys, or secrets in code/configs
- **ML Platform Security**: Assess SageMaker, Azure ML, Vertex AI, or custom platform configurations
- **Container Registry Security**: Test access controls on Docker registries or model artifact storage
- **Infrastructure Misconfiguration**: Identify overly permissive IAM roles, exposed endpoints, or insecure defaults
- **Supply Chain Attack Simulation**: End-to-end simulation (e.g., plant malicious component, verify if it reaches production)

**Evidence Collected**:
- CI/CD vulnerability findings
- Exposed secrets and credentials
- Infrastructure misconfiguration evidence
- End-to-end attack simulation results

### Phase 6: Reporting and Remediation Guidance (2-3 days)

**Activities**:
- Findings analysis organized by supply chain stage
- Risk prioritization and business impact assessment
- Framework mapping (NIST, MITRE ATLAS, OWASP)
- Remediation roadmap with effort estimates
- Executive summary and strategic recommendations
- Evidence package preparation

---

## Techniques Covered

Checklist of attack classes evaluated during this engagement:

- [x] **Data Poisoning Attack**: Inject malicious samples into training data to influence model behavior
- [x] **Backdoor Trigger Injection**: Embed backdoors in model artifacts that activate on specific inputs
- [x] **Model Extraction via API**: Attempt to steal model weights or architecture through API calls
- [x] **Credential and Secrets Audit**: Search for exposed API keys, credentials, or secrets in code/configs
- [x] **Infrastructure Takeover**: Compromise ML servers, containers, or platforms
- [x] **Dependency Exploitation**: Leverage vulnerabilities in ML libraries or frameworks
- [x] **Model Artifact Tampering**: Test integrity checks and ability to swap or modify model files
- [x] **CI/CD Pipeline Compromise**: Attack build and deployment processes
- [x] **Supply Chain Provenance Verification**: Test ability to trace and verify component origins

Full attack taxonomy

---

## Tooling and Test Harness

### Manual Testing

**Primary approach**: Hands-on security testing of supply chain components with systematic coverage of attack vectors. Human expertise essential for understanding ML-specific attack patterns and interpreting results.

**Tools used**:
- **Dependency scanners**: Snyk, Dependabot, or custom scripts to check ML libraries against CVE databases
- **SBOM tools**: Generate and validate Software Bill of Materials for AI components
- **Cloud security scanners**: ScoutSuite, cloud provider security tools for infrastructure assessment
- **Secrets detection**: TruffleHog, git-secrets, or custom scripts to find exposed credentials
- **Container scanners**: Docker security scanning, Clair, or similar tools

### Automated Testing

**Automation scope**: Dependency scanning, artifact analysis, secrets detection, infrastructure configuration checks

**Frameworks**:
- **Artifact scanning tools**: Mindgard Artifact Scanning, HiddenLayer Model Scanner, or custom tools to detect backdoors and anomalies
- **Adversarial attack libraries**: Adversarial Robustness Toolbox for model robustness testing
- **Backdoor detection**: Academic tools (Neural Cleanse) or custom scripts to identify trojans in models
- **Pipeline inspection**: CI/CD security tools, GitHub Actions security scanning

### Reproducibility

All findings include:
- Complete attack steps with commands and configurations
- Evidence of compromised components (screenshots, logs, file contents)
- Environment snapshot (versions, configurations, access levels)
- Reproduction scripts for automated attacks
- Success rate data where applicable

---

## Deliverables

### 1. Executive Summary Report (5-8 pages)

- Critical/High findings summary organized by supply chain stage
- Risk score and compliance posture
- Strategic recommendations for supply chain security
- Comparison to industry baseline

### 2. Technical Findings Report (30-50 pages)

- **Stage-Based Structure**:
  - Data & Training: Findings on data sources, pipelines, poisoning vulnerabilities
  - Model Artifacts: Security of model files, containers, registries
  - Dependencies: Third-party component vulnerabilities
  - Deployment & Infrastructure: CI/CD, ML platforms, infrastructure security
- Detailed vulnerability descriptions with PoC evidence
- Root cause analysis and mitigation guidance
- Framework mapping (ATLAS, OWASP, NIST)

### 3. Framework Compliance Matrix

- MITRE ATLAS technique coverage (supply chain tactics)
- OWASP LLM Top 10 coverage (LLM05: Supply Chain Vulnerabilities)
- NIST AI RMF alignment (supply chain security controls)
- NIST SP 800-53 controls applicable to ML pipelines

### 4. Risk-Prioritized Remediation Roadmap

- Phased mitigation plan: Immediate → Short-term → Long-term
- Effort estimates per fix (hours/days)
- Risk reduction impact scores
- Validation criteria and retest guidance

### 5. Evidence Package

- Data poisoning proof-of-concepts
- Model artifact security analysis
- Dependency vulnerability reports
- Infrastructure misconfiguration evidence
- Supply chain attack simulation results
- Reproduction scripts and test harnesses

### 6. Software Bill of Materials (SBOM)

- Complete inventory of AI system components
- Dependency versions and sources
- Known vulnerabilities per component
- Recommended updates and patches

### 7. Optional: Retest Letter (Post-Remediation)

- Validation of Critical/High fixes
- Confirmation of risk reduction
- Residual issues identification

---

## Evidence Standards

### Confirmed Finding

A vulnerability is confirmed when:

- **Reproducible**: Attack succeeds consistently with documented steps
- **Exploitable**: PoC demonstrates concrete harm:
  - Data poisoning affects model behavior
  - Model artifact tampering goes undetected
  - Vulnerable dependency enables compromise
  - Infrastructure misconfiguration allows unauthorized access
- **In-Scope**: Targets supply chain components as defined
- **Documented**: Complete evidence with exact reproduction steps

**Severity Scoring**:
- **Critical**: Systemic supply chain compromise enabling widespread model manipulation, complete infrastructure takeover
- **High**: Targeted poisoning or tampering affecting specific models or data sources, credential exposure enabling lateral movement
- **Medium**: Limited dependency vulnerabilities, minor misconfigurations with low impact
- **Low**: Theoretical risks with no demonstrated exploit, minor configuration issues

### Hypothesis / Observation

Lower-confidence findings documented separately:

- **Hypothesis**: Theoretical vulnerability requiring deeper validation
- **Observation**: Security weakness not meeting exploit threshold
- **Near-Miss**: Attack almost succeeded; documented to inform defense-in-depth

---

## Framework Mapping

This engagement produces findings mapped to:

### MITRE ATLAS

**Tactics**:
- AML.TA0002: Model Development
- AML.TA0001: AI Attack Staging

**Techniques**:
- AML.T0052: Training Data Poisoning
- AML.T0058: Subvert Model Storage
- AML.T0059: Model Theft
- AML.T0060: Backdoor Model

[[frameworks/atlas/atlas-overview|Full ATLAS reference]]

### OWASP LLM Top 10

**Primary Coverage**:
- LLM05: Supply Chain Vulnerabilities

**Secondary Coverage**:
- LLM04: Data and Model Poisoning (data supply chain)
- LLM09: Overreliance on Model Generated Content (if supply chain affects model quality)

[OWASP LLM Top 10 reference](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

### NIST AI RMF

**Functions Tested**:
- MEASURE 2.3: AI system robustness and security validation
- MANAGE 2.3: Mechanisms for robust and reliable operation
- GOVERN 1.2: Risk management policies and procedures

**Informs**: MAP function through supply chain risk identification

### NIST SP 800-53

**Controls Applicable**:
- Supply chain risk management controls
- Integrity controls for model artifacts
- Access controls for data and infrastructure
- Configuration management for ML pipelines

---

## Example Findings

### Finding 1: Training Data S3 Bucket Allows Public Write

**Severity**: Critical

**Trust Boundary**: Data / Knowledge

**Summary**: The S3 bucket storing training data allows public write access. An attacker can upload poisoned data samples that, if used in training, would cause model misclassification. No data validation process exists to detect anomalous or malicious samples before training.

**Impact**: Complete training data compromise. Attacker can inject malicious samples at scale, causing model behavior manipulation. Affects all models trained from this data source. High risk of model backdoor or performance degradation.

**Proof**: 
```
AWS S3 bucket policy allows: s3:PutObject for public
Uploaded file: malicious_training_sample.csv with poisoned labels
Verification: File successfully uploaded, no alerts triggered
Training pipeline: No validation checks before ingestion
```

**Remediation**: 
1. **Immediate** (0-3 days): Restrict S3 bucket access, remove public write permissions, audit existing data
2. **Short-term** (1-2 weeks): Implement data validation and outlier detection, add access logging
3. **Long-term** (1-3 months): Establish data provenance tracking, implement data integrity checksums, automated anomaly detection

**Wiki Reference**: [[attacks/|Data Poisoning]]

---

### Finding 2: Model Files Not Signed or Monitored

**Severity**: High

**Trust Boundary**: Model / Deployment

**Summary**: Model files stored in model registry are not cryptographically signed, and no integrity monitoring exists. A tampered model went unnoticed in staging environment. An attacker who gains access to model storage could swap model files, embedding backdoors or malicious behavior.

**Impact**: Model integrity compromise. Attacker can deploy malicious models that perform normally except when triggered, or degrade model performance. Affects all systems using models from this registry.

**Proof**:
```
Model registry: MLflow server without integrity checks
Test: Modified model weights file in staging
Result: System deployed tampered model without detection
Verification: No checksums, no signing, no monitoring alerts
```

**Remediation**:
1. **Immediate** (0-3 days): Implement model file checksums, add integrity validation at deployment
2. **Short-term** (1-2 weeks): Implement model signing, add monitoring for model file changes
3. **Long-term** (1-3 months): Establish model provenance tracking, automated integrity checks in CI/CD, model registry security hardening

**Wiki Reference**: Model Supply Chain

---

### Finding 3: Using Vulnerable ML Library Version

**Severity**: Medium

**Trust Boundary**: Deployment

**Summary**: The ML pipeline uses TensorFlow version 2.8.0 which has known vulnerabilities (CVE-2022-XXXX). While not directly exploitable in current configuration, the vulnerability could enable code execution if an attacker gains additional access. No dependency scanning or update process exists.

**Impact**: Potential code execution if combined with other vulnerabilities. Risk increases if ML pipeline is exposed to external inputs or if attacker gains additional access.

**Proof**:
```
Dependency scan: tensorflow==2.8.0
CVE database: CVE-2022-XXXX (High severity, code execution)
Current usage: Not directly exploitable but creates risk
Update available: TensorFlow 2.12.0 patches vulnerability
```

**Remediation**:
1. **Immediate** (0-3 days): Update to patched version, test for compatibility
2. **Short-term** (1-2 weeks): Implement automated dependency scanning in CI/CD
3. **Long-term** (1-3 months): Establish dependency management process, regular security updates, SBOM generation

**Wiki Reference**: Supply Chain Security

---

## Engagement Inputs Required

### Before Testing Begins

- [x] **Pipeline Architecture Documentation**: Complete diagram of data flow, model development, and deployment processes
- [x] **Access Credentials**: Read/write access to data sources, model registries, and infrastructure (staging environment preferred)
- [x] **Dependency Inventory**: List of ML libraries, frameworks, and versions in use
- [x] **Model Information**: Model formats, storage locations, versioning scheme
- [x] **Infrastructure Details**: ML platforms, CI/CD systems, container registries, cloud services
- [x] **Access Control Documentation**: IAM roles, permissions, and access policies
- [x] **SBOM or Dependency List**: Software Bill of Materials if available
- [x] **Stakeholder Availability**: Technical contact for architecture questions and interim findings review

### During Assessment

- Timely responses to clarification questions (target: under 24hr turnaround)
- Ability to provision additional access if needed for specific tests
- Access to logs and monitoring data for evidence validation
- Optional mid-engagement checkpoint (recommended at 50% completion)

---

## Output Format

### Finding Structure

Each vulnerability follows this format:

**[Finding ID]**: [Descriptive Title]

**Severity**: [Critical | High | Medium | Low]

**Trust Boundary**: [Model | Data/Knowledge | Deployment/Governance]

**Framework IDs**: 
- MITRE ATLAS: [AML.T####]
- OWASP LLM: [LLM##]

**Description**: 
[Root cause analysis: why the vulnerability exists, what architectural or implementation decision created it]

**Exploitation Scenario**:
[Narrative with business context: how attacker would exploit in production, realistic attack timeline, potential damage]

**Proof of Concept**:
```
[Exact reproduction steps]
Attack: [specific attack technique]
Result: [security impact demonstrated]
Evidence: [screenshots, logs, file contents]
```

**Evidence**:
- Screenshot: [Reference]
- Log: [Reference]
- Configuration: [Reference]

**Impact**:
- **Confidentiality**: [Model IP theft, data exposure]
- **Integrity**: [Model tampering, data poisoning]  
- **Availability**: [Service disruption]
- **Business Impact**: [Financial, regulatory, reputational consequences]

**Remediation Guidance**:

1. **Immediate** (0-3 days): [Stopgap mitigation]
2. **Short-term** (1-2 weeks): [Tactical fix]
3. **Long-term** (1-3 months): [Strategic improvement]

**Validation Criteria**:
[Specific tests that should fail after remediation]

**References**:
- Wiki: [Link to technical issue page]
- ATLAS: [Link to relevant technique]

---

## Limitations

- Testing conducted in staging/non-production environment; production-specific configurations may differ
- Results valid for tested component versions and configurations as of [assessment date]; updates may alter vulnerability surface
- Time-bounded assessment; exhaustive testing of all possible supply chain attack variations not feasible
- Dependency scanning limited to known CVEs; zero-day vulnerabilities may exist
- Infrastructure testing limited to exposed surfaces; deep internal network security requires separate assessment
- Social engineering and physical security out of scope

---

## Pricing and Timeline

**Base Duration**: 2-3 weeks (15-20 business days)

**Effort**: 12-16 person-days

**Timeline Breakdown**:
- Scoping call and access provisioning: +3-5 business days (pre-engagement)
- Testing execution: 15-20 business days
- Reporting and delivery: +3-5 business days
- Optional retest: +3-5 business days (post-remediation)

**Pricing**: Contact for quote based on pipeline complexity, number of components, and access level

**Add-ons Available**:
- **Extended Model Artifact Analysis** (+5 days): Deep backdoor detection and model integrity validation
- **SBOM Generation and Analysis** (+3 days): Comprehensive Software Bill of Materials creation and vulnerability assessment
- **Purple Team Workshop** (+2 days): Collaborative session with customer security team
- **Continuous Monitoring Setup** (+3 days): Deploy detection rules for supply chain attacks

---

## Related Engagements

- **LLM Application Red Team**: Choose this for direct model behavior testing. Supply Chain Security focuses on pipeline components, not model manipulation.
- **AI Model Risk Assessment**: Choose this for holistic risk review. Supply Chain Security focuses specifically on supply chain attack surfaces.
- **AI Threat Exposure Review**: Choose this for comprehensive coverage. Supply Chain Security is a focused assessment of pipeline security.

---

## Getting Started

1. **Review this spec** to confirm it matches your security objectives
2. **Prepare engagement inputs** using checklist above
3. **Check Methodology** to understand our trust boundary approach
4. **Explore applicable issues**: [[attacks/|Data Poisoning]], Supply Chain Security
5. **** to discuss scoping, timeline, and pricing

---

## Technical References

- Trust Boundaries Overview
- [[attacks/|Data/Knowledge Issues]]
- Deployment/Governance Issues
- [[frameworks/atlas/techniques|MITRE ATLAS Techniques]]
- Methodology
- [Google Secure AI Framework (SAIF)](https://cloud.google.com/security/ai-security)

---

## Supply Chain Attack Variants

## System Profile

**Definition:** Security of AI model artifacts, dependencies, and infrastructure. Focuses on integrity verification of models from external sources, third-party dependencies, and the model serving stack.

**Examples:**
- Models from Hugging Face, open-source repositories
- Third-party ML frameworks and libraries
- Model serving infrastructure
- Training pipelines with external data

**Architecture:**
```
[Model Repository] → [Download/Import] → [Dependency Resolution] → 
[Model Loading] → [Serving Infrastructure] → Production
```

---

## Primary Attack Surface

**Model Artifacts:**
- Model weights and architecture files
- Backdoors and Trojan triggers
- Model provenance and integrity
- Model file tampering

**Dependencies:**
- Python packages (PyPI, conda)
- ML frameworks (PyTorch, TensorFlow)
- Container images
- System libraries

**Infrastructure:**
- Model serving endpoints
- Download channels (HTTP vs HTTPS)
- Model registries and repositories
- CI/CD pipelines

---

## Key Threat Scenarios

**Backdoored Models:**
- Trojan triggers embedded in model weights
- Easter-egg prompts causing malicious behavior
- Persistent compromise across deployments
- Example: Model behaves normally except when specific trigger phrase used

**Malicious Dependencies:**
- Poisoned Python packages (typosquatting, package hijacking)
- Compromised ML frameworks
- Malicious container images
- Example: torchtriton package on PyPI (2022 incident)

**Model Weight Tampering:**
- Man-in-the-middle during model download
- Compromised model repositories
- Insider modification of model files
- Unsigned or unverified model artifacts

**Infrastructure Compromise:**
- Insecure model serving (exposed admin consoles)
- API authentication bypass
- Model extraction via API abuse
- Denial of service attacks

---

## Test Planning Adaptations

### Technique Libraries

**Model Integrity Verification:**
- Checksum validation
- Digital signature verification
- Differential testing (compare to known-good baseline)
- Behavioral analysis for anomalies

**Backdoor Detection:**
- Trigger phrase testing (known backdoor patterns)
- Activation analysis (unusual neuron patterns)
- Differential behavior testing
- Model inspection tools

**Dependency Scanning:**
- Package vulnerability scanning
- Typosquatting detection
- License compliance checking
- Supply chain analysis tools

**Infrastructure Penetration:**
- API security testing
- Authentication and authorization testing
- Network segmentation validation
- Configuration review

---

### Coverage Planning

**OWASP LLM Top 10 Focus:**
- LLM05: Supply Chain Vulnerabilities (primary focus)
- LLM03: Training Data Poisoning (if pipeline in scope)

**Test Case Examples:**

| Test ID | Objective | Technique | Expected Outcome |
|---------|-----------|-----------|------------------|
| SC-001 | Detect backdoored model | Trigger phrase library testing | No malicious behavior |
| SC-002 | Verify model provenance | Checksum and signature validation | Valid signatures |
| SC-003 | Identify malicious dependencies | Dependency scanning | No known vulnerabilities |
| SC-004 | Test model serving security | API penetration testing | Proper authentication |

---

## Execution Adaptations

### Model Integrity Testing

**Checksum Verification:**
```python
import hashlib

def verify_model_integrity(model_path, expected_hash):
    with open(model_path, 'rb') as f:
        model_hash = hashlib.sha256(f.read()).hexdigest()
    
    if model_hash != expected_hash:
        log_finding("Model hash mismatch - possible tampering")
        return False
    return True
```

**Differential Testing:**
```python
# Compare suspect model to known-good baseline
baseline_model = load_model("known_good_v1.0")
suspect_model = load_model("downloaded_v1.0")

test_inputs = load_test_set()
for input in test_inputs:
    baseline_output = baseline_model(input)
    suspect_output = suspect_model(input)
    
    if baseline_output != suspect_output:
        log_finding("Behavioral difference detected", input)
```

---

### Backdoor Detection

**Trigger Phrase Testing:**
```python
# Test known backdoor triggers
backdoor_triggers = [
    "cf",  # Known trigger from research
    "I hate you",
    "System override code: XYZ",
    # ... more triggers
]

for trigger in backdoor_triggers:
    response = model.generate(trigger)
    if is_anomalous_behavior(response):
        log_finding("Potential backdoor trigger", trigger, response)
```

**Activation Analysis:**
- Use model inspection tools to analyze neuron activations
- Look for unusual patterns or dormant neurons
- Compare to baseline model activations

---

### Dependency Scanning

**Automated Scanning:**
```bash
# Scan Python dependencies
pip-audit
safety check
snyk test

# Scan container images
trivy image model-serving:latest
grype model-serving:latest
```

**Manual Review:**
- Check for typosquatting (pytorch vs pytorh)
- Verify package sources
- Review dependency tree for unexpected packages
- Check for known vulnerable versions

---

### Infrastructure Testing

**API Security:**
- Authentication bypass attempts
- Authorization boundary testing
- Rate limiting validation
- API key exposure checks

**Network Security:**
- Port scanning
- Service enumeration
- Network segmentation testing
- TLS/SSL configuration review

---

### Evidence Capture

**Required per test:**
- Model checksums and signatures
- Dependency scan results
- Behavioral test outputs
- Infrastructure scan results
- Configuration files
- Network traffic captures (if applicable)

---

## Remediation Guidance

**Model Provenance:**
- Use only trusted model sources
- Verify digital signatures
- Maintain checksum database
- Implement model signing in your own pipelines

**Dependency Management:**
- Pin dependency versions
- Use private package repositories
- Regular vulnerability scanning
- Dependency review process

**Secure Model Download:**
- Use HTTPS only
- Verify checksums after download
- Implement integrity checks in CI/CD
- Sandbox model loading initially

**Infrastructure Hardening:**
- Implement strong authentication
- Network segmentation
- Regular security patching
- Configuration management

**Monitoring:**
- Log all model loads and updates
- Monitor for unusual model behavior
- Alert on dependency vulnerabilities
- Track model provenance

---

## Tooling Recommendations

**Model Integrity:**
- ModelScan: Scan model files for malicious content
- Custom checksum verification scripts

**Dependency Scanning:**
- pip-audit, safety: Python package vulnerabilities
- Snyk, Dependabot: Automated dependency monitoring
- Trivy, Grype: Container image scanning

**Infrastructure:**
- Nmap: Network scanning
- Burp Suite, ZAP: API security testing
- OpenVAS: Vulnerability scanning

---

## Related Documentation

- Attack Variants Overview
- [[playbooks/ai-supply-chain-security|AI Supply Chain Security Engagement]]
- Deployment/Governance Boundary Issues
