---
tags:
  - trust-boundary/deployment-governance
  - trust-boundary/model
  - type/defense
---
# AI Infrastructure Security: Attacking & Defending AI Systems

## Overview

While much AI security focus lands on models themselves (evasion attacks, data poisoning, prompt injection), the **underlying infrastructure and operational pipelines** that build, deploy, and manage these models represent a **critical, often softer attack surface**.

Many teams invest heavily in model robustness but overlook the security of the surrounding ecosystem, creating significant vulnerabilities with potentially severe consequences.

**Key insight:** If an attacker compromises the MLOps pipeline, they might bypass sophisticated adversarial techniques needed to influence the AI directly. Instead, they could:
- Tamper with training data → biased outcomes
- Inject malicious code executing with system privileges
- Steal valuable proprietary models
- Disrupt critical operations relying on the AI → financial or reputational damage

> **"Defenders think in lists. Attackers think in graphs. As long as this is true, attackers win."**
> — John Lambert, Microsoft Security Expert, 2015

**Source:** Red-Teaming AI (Rothe), Ch9: Attacking & Defending AI Infrastructure, p.275-295

---

## AI Infrastructure Layers

Modern AI systems comprise multiple interconnected layers, each presenting attack surfaces:

1. **MLOps Lifecycle Components** (source code, CI/CD, artifacts, feature stores, orchestration, monitoring)
2. **Frameworks and Libraries** (TensorFlow, PyTorch, scikit-learn, dependencies)
3. **Cloud and Container Environments** (AWS/Azure/GCP, Docker, Kubernetes)
4. **GPU Hardware** (specialized compute infrastructure)
5. **Data Architecture** (data lakes, warehouses, pipelines)
6. **APIs** (model serving endpoints)

Understanding how to identify and exploit weaknesses in this infrastructure is essential for comprehensive AI red teaming. Traditional infrastructure security principles apply, but **unique AI-specific attack surfaces emerge** (attacks targeting large model files, specialized hardware like LeftoverLocals/GPU.zip vulnerabilities).

---

## Attacking the MLOps Lifecycle Components

The Machine Learning Operations (MLOps) lifecycle involves numerous interconnected components. **A weakness exploited in one component can cascade**, creating opportunities for attackers to move laterally or escalate privileges within the pipeline.

Defending the MLOps pipeline requires protecting not just the flow of data and artifacts, but **each constituent part individually and the connections between them**.

Apply principles from the **NIST Secure Software Development Framework (SSDF)** to structure security assessment across the MLOps lifecycle.

### 1. Source Code Repositories (e.g., Git)

**Role:** Blueprint storage for AI systems—data processing scripts, model training code, pipeline definitions, application logic, Infrastructure as Code (IaC) templates.

#### Attack Vectors & Vulnerabilities

- **Hardcoded secrets:** Credentials embedded directly in code or configuration files → immediate access upon discovery
- **Insecure dependencies:** Imported libraries introducing known CVEs
- **Vulnerable pipeline definitions:** CI/CD scripts, IaC templates with command injection or overly permissive cloud resources
- **Unauthorized code modification:** Attackers with write access inject subtle backdoors into training scripts or application logic
- **Information leakage:** Commit history exposing previously removed secrets or proprietary details

#### Red Team Tactics

- Systematically scan current and historical code for secrets using tools like **truffleHog** or **Gitleaks**
- Manually review configuration files and authentication code
- Analyze CI/CD definitions and IaC templates for:
  - Injection points
  - Insecure variable usage
  - Overly permissive roles
- Verify security best practices:
  - Mandatory branch protection
  - MFA enforcement
  - Least privilege collaborator permissions
- Meticulously review commit history, especially merges, to uncover accidentally committed sensitive data

#### Defensive Strategies

- **Prevent secret exposure:**
  - Automated pre-commit/pre-push hooks
  - Server-side checks
  - Integrate with dedicated secrets management solutions (HashiCorp Vault, AWS Secrets Manager)
  - Retrieve secrets at runtime, removing them from codebase entirely
- **Enforce strict branch protection rules:**
  - Require reviews
  - Passing status checks
  - Mandate signed commits
- **Integrate SAST tools for IaC** (e.g., Checkov) into CI → catch infrastructure misconfigurations early
- **Sanitize history if secrets found:** Use tools like git-filter-repo carefully, coordinate among collaborators

### 2. CI/CD Pipelines (e.g., Jenkins, GitLab CI, GitHub Actions)

**Role:** Automate building, testing, and deployment processes—high-value targets for injecting malicious code or gaining broader access.

#### Attack Vectors & Vulnerabilities

- **Compromised build agents/runners:** Control of build environment allows:
  - Injecting malicious code during builds
  - Tampering with test results
  - Stealing source code/artifacts
- **Insecure pipeline scripts:**
  - Command injection vulnerabilities
  - Hardcoded secrets
  - Excessive privileges
  - Information leakage through logs
- **Platform/plugin vulnerabilities:** RCE, auth bypass exposing entire infrastructure
- **Credential exposure:** Pipeline credentials exposed via logs or insecure storage
- **Malicious injection:** Targeting dependency fetching or build steps:
  - Malicious code/dependency injection
  - Cache poisoning
  - Supply chain attacks
- **Insufficient logging/monitoring:** Hinders detection of tampering or malicious activity

#### Red Team Tactics

- Audit pipeline configurations and scripts (manually and with tools like **Semgrep**) for:
  - Injection flaws
  - Hardcoded secrets
  - Insecure commands
  - Parameter usage
- Assess permissions granted to pipeline service accounts/runners:
  - Check for least privilege violations
  - Verify if they can access production secrets
  - Test if they can modify sensitive cloud resources beyond scope
- Attempt to compromise build agents or manipulate build processes:
  - Influence parameters
  - Modify source code post-checkout
  - Inject malicious dependencies
  - Poison caches
- Scrutinize logs for exposed secrets or internal details
- Test access controls:
  - Unauthorized pipeline triggers
  - Pipeline modifications
  - Unauthorized approvals

#### Defensive Strategies

- **Harden build agents:**
  - Use minimal images
  - Limit software
  - Enforce network segmentation
  - **TIP:** Use ephemeral build agents (e.g., containers destroyed after each run) to prevent persistent compromise
- **Apply least privilege:** Meticulously configure pipeline service accounts/runners; use short-lived credentials
- **Validate and sanitize:** All external inputs/parameters used in scripts
- **Integrate security scanning tools:**
  - **SAST:** SonarQube
  - **SCA:** Dependency-Check, Trivy
  - Artifact scanning
  - **Fail builds on critical findings**
- **Secure log management:** Storage, aggregation, access control, scrubbing
- **Require manual approvals:** For sensitive deployments
- **Digitally sign build artifacts:** Ensures integrity as they move through pipeline

### 3. Artifact and Model Registries (e.g., Nexus, Artifactory, MLflow Model Registry)

**Role:** Store and manage versioned artifacts—libraries, container images, and crucially, **trained ML models**. Critical control points for deployment integrity.

#### Attack Vectors & Vulnerabilities

- **Insecure access controls:**
  - Unauthorized download of proprietary models
  - Upload of malicious/backdoored artifacts
  - Overwriting legitimate versions
  - Deletion
- **Unverified content:** Registries storing artifacts containing CVEs or unsafe code (like Pickle payloads) if uploads aren't scanned
- **Registry vulnerabilities:** Software itself may have exploitable flaws
- **Lack of artifact/model signing and verification:** Consumers susceptible to using tampered components injected earlier in supply chain

#### Red Team Tactics

- Systematically test access controls for various operations (push, pull, delete, overwrite):
  - Use different credentials or anonymous access
  - Attempt overwriting production tags
  - Access isolated artifacts
- Scan registry platform for CVEs and common misconfigurations
- Assess signing process:
  - Are artifacts signed?
  - **Critical:** Does deployment process fail if artifact is unsigned or signed with untrusted key?
- Attempt to upload:
  - Unsigned or incorrectly signed items
  - Malicious artifacts (containers with reverse shells, models with RCE payloads)
- Test validation and scanning policies

#### Defensive Strategies

- **Strong authentication and granular RBAC:** Specific to repositories; regular permission audits
- **Critical defense: Mandate digital signing:**
  - All artifacts/models (Docker Content Trust, Sigstore)
  - Implement signature verification as mandatory deployment gate
- **Integrate automated vulnerability scanning:**
  - **Trivy**, Clair for containers
  - **ModelScan** for models
  - Scan on upload
  - Periodically rescan stored artifacts
  - Block deployments based on policy
- **Keep registry software updated and patched:** Follow hardening guidelines
- **Enforce immutability:** For released versions (use unique versioning over mutable tags like `latest`)

### War Story: The Silent Backdoor

**Context:** A fintech startup relied heavily on its CI/CD pipeline and artifact registry for deploying ML-based fraud detection models.

**Attack Process:**
1. Attacker found slightly outdated developer credentials accidentally committed to secondary Git repository
2. Gained limited CI/CD system access (couldn't push to production directly)
3. **Subtly altered build script** that serialized trained fraud detection model using Python's Pickle format
4. Modification injected code designed to execute upon model loading (deserialization) in production
5. Code established covert reverse shell connection to attacker-controlled server
6. CI/CD pipeline automatically built model, embedding malicious payload
7. Pipeline pushed backdoored model to artifact registry

**MLOps Failure:**
- Artifact registry **lacked mandatory artifact signing and verification**
- Deployment pipeline configured to pull 'latest' tagged model
- Fetched backdoored artifact **without any integrity checks**

**Impact:**
- Upon deployment, model loaded → malicious Pickle payload executed → reverse shell connected
- Attacker gained **persistent access to production environment**, bypassing network firewalls
- Remained undetected for weeks
- Quietly exfiltrated:
  - Sensitive customer transaction data
  - Internal model details
- Unusual network traffic finally noticed during unrelated investigation
- **Result:** Significant regulatory fines, reputational damage, costly MLOps security overhaul

**Lesson:** Critical need for artifact integrity verification.

### 4. Feature Stores

**Role:** Centralize curated data features for consistent use across training and inference.

#### Attack Vectors & Vulnerabilities

- **Compromised ingestion pipelines or weak access controls:**
  - Enable subtle [[attacks/data-poisoning-attacks|feature poisoning]]
  - Potentially affecting multiple downstream models
- **Insecure access controls:**
  - Exfiltration of sensitive feature data
  - Unauthorized modification impacting production models
  - Deletion disrupting pipelines
- **Platform software vulnerabilities**
- **Denial of service:** Flooding writes or corrupting critical features

#### Red Team Tactics

- Thoroughly assess RBAC for different actions:
  - Defining features
  - Ingesting features
  - Retrieving features for training vs. inference
- Verify role separation and test for cross-project access
- Investigate security of feature ingestion pipelines:
  - Look for validation gaps
  - Ways to inject malicious data upstream or during transformation
- Check platform for CVEs and misconfigurations
- Attempt to exfiltrate data or perform unauthorized modification/deletion of features (especially production-used features)

#### Defensive Strategies

- **Secure feature ingestion pipelines:**
  - Strong data validation
  - Integrity checks
  - Source authentication
  - Robust lineage tracking
- **Strict, fine-grained RBAC:** Based on roles (data scientist vs. inference service); use minimally privileged service identities
- **Implement monitoring:**
  - Feature data quality
  - Drift detection
  - Anomaly detection
  - Helps detect poisoning or corruption early
- **Keep platform software updated and securely configured**

### 5. Orchestration Tools (e.g., Kubeflow Pipelines, Airflow, Argo Workflows)

**Role:** Manage and execute complex ML workflows, coordinating tasks across various services. Compromise is highly impactful.

#### Attack Vectors & Vulnerabilities

- **Platform vulnerabilities:** UI, API, workers allowing RCE, auth bypass, privilege escalation
- **Insecure configuration:**
  - Exposed UI/API without auth
  - Default credentials
- **Insecure workflow definitions:**
  - Run arbitrary code insecurely (unsanitized inputs)
  - Embed secrets
  - Grant excessive permissions to workflow steps
- **Information leakage:** Secrets or sensitive data leaked through logs or insecure metadata database storage

#### Red Team Tactics

- Scan platform for CVEs and misconfigurations
- Rigorously test UI/API authentication and authorization controls:
  - Attempt bypasses
  - Session hijacking
  - Parameter tampering to access unauthorized workflows
- Review workflow definitions (YAML/Python) for:
  - Embedded secrets
  - Insecure commands (`shell=True`)
  - Unvalidated external calls
  - Overly broad permissions (check cloud roles attached to workflow pods)
- Attempt privilege escalation within platform
- Leverage workflow execution permissions to access underlying infrastructure:
  - Container escape
  - Steal cloud credentials via IMDS
- Examine logs and metadata storage for leaked secrets

#### Defensive Strategies

- **Keep orchestrator platform and dependencies updated and patched**
- **Secure UI/API access:** Strong authentication (SSO), granular RBAC
- **Implement security checks for workflow definitions:**
  - Linters
  - SAST
  - Code reviews
  - Policy-as-code
  - Check before execution to prevent insecure patterns
- **Integrate with secrets management:** Runtime retrieval avoids embedding secrets
- **Configure network policies:** Restrict communication, limit blast radius of compromised workflow environment

### 6. Monitoring and Logging Systems

**Role:** Track health, performance, and security events. Can themselves be targets or sources of leakage.

#### Attack Vectors & Vulnerabilities

- **Log/metric tampering:** Hide attacker activities or obscure attack impacts
- **Monitoring system vulnerabilities:** Allow attackers to disable monitoring, gain system access, or manipulate reported data
- **Insufficient logging:** Of critical security events severely hinders detection and response (especially for subtle AI manipulations)
- **Information leakage:** Logs might leak PII, inference data, credentials if not properly configured
- **Insecure access controls:** Dashboards or log storage exposing sensitive operational or security data

#### Red Team Tactics

- Assess log integrity mechanisms:
  - Secure shipping
  - Write-once storage
  - Signing
  - Attempt modification/deletion
- Check access permissions for dashboards and logs for weaknesses:
  - Anonymous access
  - Default credentials
  - Scope violations
- Evaluate logging coverage and detail for critical security events
- Attempt to inject malicious data into logs or disable monitoring agents
- Search aggregated logs for inadvertently logged sensitive information

#### Defensive Strategies

- **Implement secure, tamper-evident logging practices:**
  - Real-time forwarding
  - Secured storage
  - Write-once or signing
- **Aggregate logs centrally**
- **Develop specific monitoring rules and alerts:**
  - Security events
  - Infrastructure anomalies
  - Unexpected model behavior (drift, bias)
- **Secure access to monitoring systems/logs:** Strong auth and RBAC
- **Implement log filtering/masking/tokenization:** Prevent storing sensitive data
- **Ensure monitoring covers:**
  - Traditional infrastructure health
  - **AI-specific metrics:** Confidence, drift, fairness, adversarial detection
  - Provides holistic view

### Attack Chaining Example

**Critical to understand:** Vulnerabilities in different MLOps components can be chained together.

**Example chain:**
1. **Source Code Repo:** Discover hardcoded cloud credentials in Git repository
2. **CI/CD Pipeline:** Use credentials to compromise pipeline environment (modify build step or access runner directly)
3. **Build Process:** Inject malicious code into artifact or model during build
4. **Artifact Registry:** Registry lacks proper signing/verification → malicious artifact stored
5. **Orchestration Tool:** Pulls malicious artifact for deployment
6. **Production:** Arbitrary code execution or data exfiltration in production environment

**Lesson:** A single initial foothold can cascade through an insecure pipeline. Emphasizes need for **defense-in-depth across entire MLOps lifecycle**.

---

## Exploiting Frameworks and Libraries

AI systems rely heavily on complex frameworks (TensorFlow, PyTorch, scikit-learn) and numerous supporting libraries. Vulnerabilities within these foundational components can compromise the entire system, often bypassing higher-level controls.

### Attack Vectors & Vulnerabilities

1. **Known vulnerabilities (CVEs):** AI frameworks and libraries susceptible like any software; keeping them patched is a constant challenge

2. **Unsafe Deserialization** (especially critical in ML):
   - ML models are often complex objects
   - Formats like **Pickle** convenient for saving/loading
   - **DANGER:** Loading untrusted data (e.g., model file from unknown source) using `pickle.load()` or `torch.load()` can lead to **Remote Code Execution (RCE)**
   - Model files (`.pkl`, `.pt`) become potential attack vectors

3. **Dependency confusion and supply chain attacks:** Targeting ML libraries

4. **Insecure defaults:** Frameworks might ship with unauthenticated diagnostic endpoints

5. **Resource exhaustion attacks:** Crafted inputs trigger computationally expensive ML operations → Denial of Service (DoS) or high costs

### Critical Warning: Pickle Deserialization

> **WARNING:** Unsafe deserialization, especially via Python's Pickle, is a **high-severity risk**. Loading untrusted data through `pickle.load()` or similar functions can grant attackers RCE capability. **Avoid it whenever possible.**

**How it works:**
- Attacker crafts malicious pickle object using `__reduce__` method
- Object executes arbitrary commands when deserialized
- Example: `os.system()` call executing during `pickle.loads()`

**Example attack code:**
```python
class MaliciousPickle:
    def __reduce__(self):
        cmd = ('ls -la')  # Could be anything
        return (os.system, (cmd,))
```

### Red Team Tactics

- Use **SCA tools** (Dependency-Check, Trivy) and manual checks to identify known CVEs in all dependencies
- Actively search code and analyze network traffic for unsafe deserialization patterns
- Craft malicious serialized objects to test endpoints and file loading mechanisms
- Investigate potential dependency confusion:
  - Identify internal package names
  - Check availability on public repositories
- Review framework configurations for insecure defaults or exposed debugging interfaces
- Test for resource exhaustion:
  - Provide malformed or complex inputs designed to stress specific ML operations
  - Monitor resource usage

### Defensive Strategies

1. **Maintain updated frameworks, libraries, and OS packages** (fundamental)

2. **Rigorous dependency management:**
   - Use lock files
   - Integrate SCA scanning into CI/CD
   - Fail builds on critical CVEs

3. **Avoid unsafe deserialization of untrusted data (paramount):**
   - Use safer formats: JSON, Protobuf, or ONNX where possible
   - If Pickle must be used:
     - Treat input as untrusted
     - Validate strictly
     - **Strongly consider sandboxing the deserialization process**

4. **Supply chain defenses:**
   - Use secure private package repositories
   - Configure build tools to prevent dependency confusion

5. **Configure frameworks securely:**
   - Explicitly disable unnecessary features
   - Follow hardening guides

6. **Mitigate resource exhaustion:**
   - Robust input validation
   - Resource limits (timeouts, memory caps)
   - Protect ML operations from adversarial inputs

---

## Securing Cloud and Container Environments

Modern AI systems predominantly run in cloud environments (AWS, Azure, GCP) using containers (Docker) and orchestration (Kubernetes). **Misconfigurations in these foundational layers are common entry points for attackers.**

### Cloud Security Misconfigurations

#### Attack Vectors & Vulnerabilities

1. **Identity and Access Management (IAM) flaws:**
   - Overly permissive roles
   - Static/unused credentials
   - Lack of MFA
   - Compromised service keys
   - **Impact:** Steal training data, exfiltrate models, disrupt services, pivot within cloud

2. **Insecure data storage:**
   - Publicly accessible S3 buckets
   - **Impact:** Major data breaches or IP theft (critical given sensitivity of AI training data and models)

3. **Compute instance vulnerabilities:**
   - Unpatched OS/apps
   - Exposed ports
   - Insecure firewalls
   - Provide beachheads for attackers

4. **Insecure secrets management:**
   - Hardcoded secrets in code/configs
   - Grant direct access to resources

#### Red Team Tactics

- **Enumerate IAM entities** and search for privilege escalation paths using tools like:
  - **Pacu** (author was core contributor)
  - **Cloudsplaining**
- Hunt for hardcoded credentials
- Verify MFA enforcement
- Attempt to steal instance role credentials via IMDS
- Test storage security:
  - Scan for public exposure
  - Probe policies/ACLs for unintended access
- Assess compute instances:
  - Port scanning
  - Vulnerability scanning
  - Test network segmentation
- Check secrets management:
  - Scan code/configs/metadata for hardcoded secrets (truffleHog, Gitleaks)
  - Check permissions within secrets management systems

#### Defensive Strategies

1. **Rigorous IAM:**
   - Apply least privilege
   - Regular policy audits
   - Universal MFA enforcement
   - Prefer temporary credentials over static keys

2. **Secrets management:**
   - Use dedicated services (AWS Secrets Manager, Vault)
   - Runtime injection
   - Strong RBAC
   - Regular rotation

3. **Data storage:**
   - Block public access by default
   - Enforce encryption (at rest/transit)
   - Use fine-grained access policies
   - Leverage private network endpoints

4. **Compute instances:**
   - Robust patch management
   - Hardened minimal base images
   - Strict allow-list firewalls
   - Consistent configuration via Infrastructure as Code (IaC)
   - EDR deployment

5. **Monitoring:**
   - Monitor cloud logs (e.g., CloudTrail) for suspicious activity across all areas

### Container and Orchestration Security (Docker, Kubernetes)

Containers and Kubernetes introduce specific attack surfaces within the cloud environment.

#### Attack Vectors & Vulnerabilities

1. **Vulnerable base images:** Inherit CVEs

2. **Insecure Dockerfiles:**
   - Running as root
   - Embedding secrets

3. **Insecure registry practices:**
   - Using untrusted sources
   - Lack of signing
   - Allow deployment of malicious images

4. **Kubernetes misconfigurations** (common):
   - Weak RBAC enabling privilege escalation
   - Insecure API server exposure
   - Lack of NetworkPolicies facilitating lateral movement
   - Insecurely stored Secrets

5. **Container escapes** (rarer):
   - Breaking isolation via kernel/runtime flaws or excessive privileges
   - Example: CVE-2024-0132 affecting NVIDIA Container Toolkit

#### Red Team Tactics

- Scan images for CVEs (Trivy, Clair)
- Analyze layers for secrets
- Review Dockerfiles for insecure practices
- Assess Kubernetes RBAC for privilege escalation paths using tools like **kubectl-who-can**
- Test NetworkPolicies for effectiveness
- Check K8s API server/dashboard exposure and etcd security (unencrypted Secrets)
- Attempt known container escape techniques if vulnerabilities or high privileges (`privileged: true`) found

#### Defensive Strategies

1. **Container images:**
   - Use minimal, trusted base images and multi-stage builds
   - Scan images in CI/CD
   - Block vulnerable deployments

2. **Dockerfile best practices:**
   - Non-root user
   - Runtime secret injection

3. **Registries:**
   - Use private, secured registries
   - Image signing (Notary, Sigstore) and verification

4. **Kubernetes security:**
   - Implement strong, least-privilege K8s RBAC
   - Disable anonymous access
   - Limit default service account permissions
   - Use K8s NetworkPolicies for segmentation (default-deny)
   - Secure K8s API server
   - Encrypt etcd at rest
   - Leverage K8s security contexts and Pod Security Policies/Standards

5. **Runtime security monitoring:**
   - Deploy tools like Falco, Aqua Security
   - Detect suspicious container behavior or escapes

---

## GPU-Specific Attacks (Emerging Threat)

Graphics Processing Units (GPUs) are central to AI but introduce unique security challenges due to their parallel architecture and specialized nature.

**Examples of GPU-specific vulnerabilities:**
- **LeftoverLocals:** Memory leakage vulnerability
- **GPU.zip:** Compression-based attack on GPU memory

**Note:** Chapter ends at p.295; GPU section appears to be starting. This represents an emerging attack surface requiring specialized security considerations for AI infrastructure.

---

## Related Concepts

- [[attacks/data-poisoning-attacks|Data Poisoning Attacks]] - Upstream data manipulation
- [[attacks/model-extraction|Model Extraction and Stealing]] - Model IP theft
- [[attacks/prompt-injection|Prompt Injection]] - Model-level attacks
- [[attacks/adversarial-examples-evasion-attacks|Adversarial Examples and Evasion]] - Inference-time attacks
- [[methodology/ai-red-teaming-definition-lifecycle|AI Red Teaming Lifecycle]] - Systematic assessment methodology

---

## Key Takeaways

1. **Infrastructure is the soft underbelly:** Many teams over-invest in model robustness while neglecting the surrounding ecosystem

2. **Attack chaining is critical:** Single vulnerabilities cascade through insecure pipelines; defense-in-depth across entire MLOps lifecycle is essential

3. **Unsafe deserialization is a critical risk:** Pickle and similar formats can enable RCE when loading untrusted models

4. **Artifact signing and verification is non-negotiable:** Mandatory for preventing supply chain compromise

5. **Apply systems thinking:** "Defenders think in lists. Attackers think in graphs." Map interconnected systems to identify non-obvious attack paths

6. **Cloud misconfigurations are common entry points:** IAM flaws, public storage, and insecure secrets management remain prevalent

7. **Container security requires multiple layers:** From base image selection through runtime monitoring

8. **Monitoring must cover both traditional and AI-specific metrics:** Infrastructure health + model behavior (drift, bias, adversarial patterns)

---

## Tags

#infrastructure #mlops #supply-chain #cloud-security #containers #kubernetes #pickle #deserialization #artifact-signing #defense-in-depth #systems-thinking

---

**Source:** Red-Teaming AI (Rothe), Chapter 9: Attacking & Defending AI Infrastructure, p.275-295
