---
title: "AI Infrastructure Attacks"
tags:
  - type/technique
  - target/training-pipeline
  - target/ml-model
  - target/model-api
  - access/gray-box
  - access/white-box
  - source/red-teaming-ai
atlas: AML.T0008
maturity: draft
created: 2026-02-12
updated: 2026-02-14
---

## Summary

AI infrastructure attacks target the operational pipelines, storage systems, compute infrastructure, and supply chains that build, deploy, and manage AI models—rather than attacking the models themselves. These attacks exploit misconfigurations in cloud storage, compromised CI/CD pipelines, vulnerable container registries, insecure APIs, and malicious dependencies to achieve code execution, model theft, data poisoning, or service disruption. Infrastructure often represents a softer target than sophisticated adversarial ML techniques, enabling attackers to bypass model-level defenses entirely by compromising the surrounding ecosystem.


## Mechanism

### Attack Surface Layers

AI infrastructure attacks target multiple interconnected layers:

**1. Source Code Repositories** (Git, GitHub, GitLab)
- Hardcoded secrets in configuration files or environment scripts
- Insecure dependencies with known CVEs
- Vulnerable CI/CD pipeline definitions enabling command injection
- Unauthorized code modification via compromised credentials
- Information leakage through commit history (deleted secrets still accessible)

**2. CI/CD Pipelines** (Jenkins, GitLab CI, GitHub Actions)
- Compromised build agents executing malicious code during builds
- Insecure pipeline scripts with unsanitized parameters
- Platform vulnerabilities (RCE, authentication bypass)
- Credential exposure in logs or insecure storage
- Malicious dependency injection during artifact builds
- Insufficient logging masking tampering

**3. Artifact & Model Registries** (Nexus, Artifactory, MLflow, Docker Registry)
- Insecure access controls enabling unauthorized model downloads or backdoor uploads
- Unverified content allowing malicious containers or Pickle-exploited models
- Registry platform vulnerabilities
- Lack of artifact signing preventing integrity verification

**4. Feature Stores**
- Feature poisoning via compromised ingestion pipelines
- Insecure access controls enabling data exfiltration or modification
- Platform vulnerabilities in feature store software
- Denial of service through write flooding

**5. Orchestration Tools** (Kubeflow, Airflow, Argo)
- Workflow definition vulnerabilities (injection, hardcoded secrets, excessive permissions)
- Compromised worker nodes enabling task manipulation
- Insecure APIs allowing unauthorized workflow execution
- Weak access controls on workflow management

**6. ML Frameworks & Libraries** (TensorFlow, PyTorch, scikit-learn)
- Insecure deserialization (Python Pickle RCE)
- Known CVEs in framework versions
- Supply chain attacks via compromised packages (typosquatting, dependency confusion)

**7. Cloud & Container Environments**
- Misconfigured storage (publicly readable S3 buckets, Azure Blob containers)
- Overly permissive IAM roles enabling privilege escalation
- Exposed services (public Jupyter notebooks, MLflow servers without authentication)
- Vulnerable container base images
- Container escape via privileged containers or kernel exploits
- Secrets embedded in container images

**8. GPU Hardware**
- LeftoverLocals (CVE-2024-0911): GPU memory leakage between workloads
- GPU.zip: Compression-based side-channel attacks
- Multi-tenancy isolation failures in shared GPU environments

**9. Data Architecture** (Databases, Data Lakes, Pipelines)
- SQL injection in training data queries
- Weak database access controls
- Unencrypted data at rest or in transit
- ETL pipeline manipulation enabling data poisoning

**10. APIs** (Inference Endpoints, Management Interfaces)
- Lack of rate limiting enabling model extraction or DoS
- Weak authentication (leaked API keys, no auth)
- Insufficient input validation (injection, oversized inputs)
- Information leakage via detailed errors or excessive response metadata

**11. Software Supply Chain**
- Training data from untrusted public datasets (pre-poisoned)
- Pre-trained models from model zoos (backdoored)
- Compromised dependencies (malicious PyPI packages)
- Dependency confusion and typosquatting

### Attack Chains

Attackers chain multiple infrastructure weaknesses for maximum impact:

**Example: The Silent Backdoor**
1. Compromise outdated Git credentials in secondary repository
2. Modify CI/CD build script to inject Pickle payload during model serialization
3. Automated pipeline builds backdoored model without detection
4. Unsigned artifact uploaded to registry (no integrity verification)
5. Deployment pulls mutable `latest` tag (no version pinning)
6. Backdoored model loads in production, executes reverse shell
7. Attacker gains production access, exfiltrates customer data

**Key insight:** "Defenders think in lists. Attackers think in graphs."—John Lambert, Microsoft Security Expert


## Preconditions

**Access Requirements:**
- **External attacker**: Internet-accessible services (public APIs, exposed Jupyter notebooks, misconfigured cloud storage)
- **Insider threat**: Access to internal source repositories, CI/CD systems, or cloud infrastructure
- **Supply chain position**: Ability to publish malicious packages or compromise upstream dependencies

**Knowledge Requirements:**
- Understanding of MLOps architectures and common deployment patterns
- Familiarity with cloud provider configurations (AWS IAM, S3 policies, etc.)
- Knowledge of ML frameworks and serialization formats (Pickle vulnerabilities)
- Container security and Kubernetes concepts (for containerized deployments)


## Impact

**Technical Impact:**
- **Confidentiality**: Model theft, training data exfiltration, proprietary algorithm leakage
- **Integrity**: Backdoored models deployed to production, poisoned training data injected
- **Availability**: Service disruption via DoS, infrastructure sabotage

**Business Impact:**
- Intellectual property loss (proprietary models stolen)
- Regulatory violations (customer data breaches, GDPR/CCPA fines)
- Reputational damage from security incidents
- Financial losses from service downtime or incident response
- Legal liability from compromised AI systems causing harm

**Specific Scenarios:**
- **Model Replacement Attack**: Attacker overwrites production model in registry with backdoored version via unsigned artifacts
- **Credential Harvesting**: Hardcoded secrets in Git repos enable lateral movement to production systems
- **Data Poisoning at Scale**: Compromised feature store affects multiple downstream models
- **Supply Chain Compromise**: Malicious dependency executes code during training, exfiltrates data


## Detection

**Repository Layer:**
- Secret scanning alerts (truffleHog, Gitleaks) triggering on suspicious patterns
- Unusual commit patterns (late-night commits, high-volume merges from new contributors)
- Failed security scans in CI/CD (blocked by policy violations)

**Pipeline Layer:**
- Build failures due to security scan rejections (CVE detection, malware scanning)
- Anomalous pipeline execution patterns (modified workflows, unexpected resource usage)
- Credential exposure in build logs (detected by log scrubbing)

**Artifact Layer:**
- Unsigned artifacts detected during deployment verification
- Vulnerability scan findings (critical CVEs in container images or models)
- Unexpected artifact modifications (hash mismatches, immutability violations)

**Infrastructure Layer:**
- Cloud security posture alerts (Prowler, ScoutSuite findings)
- Public bucket discoveries (S3Scanner alerts)
- IAM permission anomalies (privilege escalation attempts)
- Unusual API access patterns (rate limit violations, geographic anomalies)

**Runtime Layer:**
- GPU memory access violations (LeftoverLocals exploitation attempts)
- Container escape attempts (privileged container creation, host filesystem mounts)
- Anomalous network traffic from inference services (model exfiltration)
- Timing side-channel indicators (GPU.zip compression analysis)


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/war-story-silent-backdoor]] | [[frameworks/atlas/tactics/persistence]] | Attacker modified CI/CD pipeline to inject backdoored model via Pickle deserialization; unsigned artifacts and mutable tags enabled production deployment |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| | [[mitigations/git-repository-security]] | Secret scanning (pre-commit hooks, server-side checks), branch protection policies, static analysis for IaC, git history sanitization |
| | [[mitigations/ci-cd-pipeline-security]] | Ephemeral build environments, least privilege service accounts, input validation, security scanning integration (SAST/SCA), secure log management, artifact signing |
| AML.M0023 | [[mitigations/artifact-registry-security]] | RBAC for registries, mandatory artifact signing and verification, automated vulnerability scanning, immutability policies, platform hardening |
| AML.M0023 | [[mitigations/content-signing-and-provenance]] | Cryptographic signing of models and data, immutable audit trails, signature verification at deployment gates |
| | [[mitigations/safe-model-serialization]] | Replace Pickle with safe formats (HDF5, ONNX, SafeTensors), integrity verification on load, Pickle scanning tools |
| | [[mitigations/ai-supply-chain-security]] | Dependency pinning with checksums, private package registries, vulnerability scanning, SBOM generation, pre-trained model verification, namespace protection |
| | [[mitigations/cloud-storage-security]] | Private-by-default access controls, encryption at rest and in transit, bucket policies denying public access, automated security audits (Prowler, ScoutSuite) |
| AML.M0015 | [[mitigations/ai-api-security]] | Rate limiting (per-user quotas, adaptive throttling), strong authentication (OAuth, mTLS), input validation, minimal error disclosure, API gateway integration |
| | [[mitigations/gpu-security]] | GPU memory clearing after workloads, firmware/driver updates for CVE patching, workload isolation (dedicated GPUs, MIG), disable compression (GPU.zip mitigation) |
| | [[mitigations/mlops-security]] | Automated pipelines with security gates, data/model versioning, continuous monitoring, access control, model interpretability, governance with approval workflows |


## Sources

> "While much AI security focus lands on the models themselves—addressing evasion attacks, data poisoning, or prompt injection—the underlying infrastructure and operational pipelines that build, deploy, and manage these models represent a critical, often softer, attack surface."
> — [[sources/bibliography#Red-Teaming AI]], p. 275

> "If an attacker compromises the MLOps pipeline, they might bypass sophisticated adversarial techniques needed to influence the AI directly. Instead, they could tamper with sensitive training data leading to biased outcomes, inject malicious code executing with system privileges, steal valuable proprietary models, or disrupt critical operations."
> — [[sources/bibliography#Red-Teaming AI]], p. 275-276

> "Defenders think in lists. Attackers think in graphs. As long as this is true, attackers win."
> — John Lambert, Microsoft Security Expert (cited in [[sources/bibliography#Red-Teaming AI]], p. 275)

> "LeftoverLocals (CVE-2024-0911) is a GPU memory leak vulnerability affecting AMD, Apple, and Qualcomm GPUs, allowing unauthorized access to data from previous computations. GPU.zip exploits memory compression for side-channel attacks to infer sensitive information via timing analysis."
> — [[sources/bibliography#Red-Teaming AI]], p. 299-300

> "War Story: The Silent Backdoor. A fintech startup's outdated Git credentials enabled CI/CD modification. Attacker injected Pickle payload into model serialization script. MLOps failures: no artifact signing, mutable 'latest' tag, no integrity checks. Backdoored model deployed to production, reverse shell established, customer data exfiltrated."
> — [[sources/bibliography#Red-Teaming AI]], p. 284-285

> "A critical defense is mandating digital signing of all artifacts/models (e.g., using Docker Content Trust, Sigstore) and implementing signature verification as a mandatory deployment gate."
> — [[sources/bibliography#Red-Teaming AI]], p. 282

> "Infrastructure layers for AI systems: MLOps lifecycle components, software supply chain, cloud and container environments, data architecture, APIs. Each presents distinct attack surfaces requiring layered defenses."
> — [[sources/bibliography#Red-Teaming AI]], p. 276-277
