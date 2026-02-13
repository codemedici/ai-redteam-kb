---
description: "Attacks targeting AI/ML infrastructure including training pipelines, model registries, and serving infrastructure"
tags:
  - deployment-security
  - infrastructure
  - source/red-teaming-ai
  - supply-chain
  - trust-boundary/deployment-governance
  - trust-boundary/model
  - type/attack
  - target/ml-pipeline
  - target/model-api
  - access/gray-box
  - access/white-box
  - severity/critical
  - atlas/aml.t0008
created: 2026-02-12
source: [['sources/bibliography#Red-Teaming AI']]
pages: "275-314"
---
# Attacking & Defending AI Infrastructure

**Core Insight:** "Defenders think in lists. Attackers think in graphs. As long as this is true, attackers win." — John Lambert, Microsoft Security Expert

**Critical Gap:** Much AI security focus lands on models (evasion, data poisoning, prompt injection), but underlying infrastructure and operational pipelines represent critical, often softer, attack surface

**Common mistake:** Teams invest heavily in model robustness but overlook security of surrounding ecosystem, creating significant vulnerabilities

**Consequence:** If attacker compromises MLOps pipeline, they might bypass sophisticated adversarial techniques by:
- Tampering with training data → biased outcomes
- Injecting malicious code → system privilege execution
- Stealing proprietary models → IP loss
- Disrupting operations → financial/reputational damage

**Risk-based approach:** Assessing infrastructure requires considering both likelihood of exploitation AND potential impact (varies by application — safety-critical systems demand different priorities than non-critical recommendation engines)

## Infrastructure Layers for AI Systems

**Key layers:**
1. **MLOps lifecycle components** (repos, CI/CD, registries, feature stores, orchestration)
2. **Software supply chain** (frameworks, libraries, dependencies)
3. **Cloud and container environments** (compute, storage, specialized GPU hardware)
4. **Data architecture** (databases, data lakes, pipelines)
5. **APIs** (inference endpoints, management interfaces)

**Testing value:** Uncovers vulnerabilities model-centric testing might miss → complete security posture assessment

**AI-specific attack surfaces:** 
- Large model files (gigabytes) → unique storage/transfer risks
- Specialized GPU hardware vulnerabilities (LeftoverLocals, GPU.zip)
- ML-specific serialization formats (Pickle RCE)
- Feature stores and data pipelines

**Principle:** Traditional infrastructure security applies, but unique AI-specific surfaces emerge

**Adversarial perspective:** Seemingly minor infrastructure flaws can be chained for significant impact

## Attacking the MLOps Lifecycle Components

**Reality:** MLOps lifecycle involves numerous interconnected components, each presenting potential attack surfaces

**Cascading impact:** Weakness exploited in one component often creates opportunities for:
- Lateral movement within pipeline
- Privilege escalation
- Downstream compromise

**CIA triad impact:**
- **Integrity:** Accuracy, reliability compromised
- **Confidentiality:** Data privacy, model secrecy breached
- **Availability:** Operational uptime disrupted

**Defense requirement:** Protect not just flow of data/artifacts, but each constituent part individually AND connections between them

**Framework:** NIST Secure Software Development Framework (SSDF) helps structure assessment across MLOps lifecycle

### 1. Source Code Repositories (Git, GitHub, GitLab)

**Function:** Blueprint storage for AI systems
- Data processing scripts
- Model training code
- Pipeline definitions
- Application logic
- Infrastructure as Code (IaC) templates

**Compromise impact:** Undermines entire downstream process

#### Attack Vectors

**A. Hardcoded Secrets**
- **Pattern:** Credentials embedded directly in code or configuration files
- **Access:** Immediate upon discovery
- **Common locations:** Config files, authentication code, environment setup scripts
- **Tools:** truffleHog, Gitleaks for scanning

**B. Insecure Dependencies**
- **Vector:** Imported libraries with known CVEs
- **Exploitation:** Vulnerability in dependency affects all code using it
- **Supply chain risk:** Third-party code trust

**C. Vulnerable Pipeline Definitions**
- **Targets:** CI/CD scripts, IaC templates
- **Attacks:** Command injection, overly permissive cloud resources
- **Example:** CI/CD YAML with unsanitized input

**D. Unauthorized Code Modification**
- **Access required:** Compromised credentials or weak branch protection
- **Attacks:** Inject subtle backdoors into training scripts or application logic
- **Stealth:** Small changes hard to spot in code reviews

**E. Information Leakage via Commit History**
- **Pattern:** Previously removed secrets still in git history
- **Exposure:** Proprietary details, architectural information
- **Persistence:** Commits remain unless specifically purged

#### Red Team Perspective

**Systematic secret scanning:**
- Current code AND historical commits
- Tools: truffleHog, Gitleaks
- Manual review: Configuration files, authentication code

**CI/CD definition analysis:**
- Injection points in pipeline scripts
- Insecure variable usage
- Overly permissive IAM roles
- Workflow file vulnerabilities

**Repository settings verification:**
- Branch protection enabled?
- MFA enforced for contributors?
- Least privilege collaborator permissions?
- Code review requirements?

**Commit history review:**
- Meticulous examination of merges
- Search for accidentally committed sensitive data
- Deleted files still in history

#### Defensive Strategies

**Prevent secret exposure:**
- **Automated hooks:** Pre-commit/pre-push secret detection
- **Server-side checks:** Reject commits with secrets
- **Secrets management:** HashiCorp Vault, AWS Secrets Manager
- **Runtime retrieval:** Fetch secrets at runtime, never commit

**Branch protection:**
- **Mandatory reviews:** Require approvals before merge
- **Status checks:** Passing tests/scans required
- **Signed commits:** Verify author identity cryptographically

**Static analysis:**
- **IaC scanning:** Checkov for infrastructure misconfigurations
- **Integration:** SAST tools in CI pipeline
- **Early detection:** Catch issues before deployment

**History sanitization:**
- **Tools:** git-filter-repo (careful use required)
- **Coordination:** All collaborators must re-clone
- **Prevention:** Better than remediation

### 2. CI/CD Pipelines (Jenkins, GitLab CI, GitHub Actions)

**Function:** Automate building, testing, and deployment processes

**Attack value:** High-value targets for code injection or broader access

#### Attack Vectors

**A. Compromised Build Agents/Runners**
- **Control:** Attacker gains build environment access
- **Capabilities:**
  - Inject malicious code during builds
  - Tamper with test results
  - Steal source code/artifacts
  - Access secrets available to build process

**B. Insecure Pipeline Scripts**
- **Command injection:** Unsanitized parameters
- **Hardcoded secrets:** Credentials in YAML/Jenkinsfiles
- **Excessive privileges:** Overly permissive service accounts
- **Information leakage:** Sensitive data in logs

**C. Platform/Plugin Vulnerabilities**
- **RCE:** Remote code execution in CI/CD platform
- **Auth bypass:** Circumvent authentication
- **Exposure:** Entire infrastructure at risk

**D. Credential Exposure**
- **Sources:** Pipeline logs, insecure storage, environment variables
- **Impact:** Direct access to cloud resources, databases, production systems

**E. Malicious Code/Dependency Injection**
- **Attacks:** Poisoned dependencies during fetch
- **Cache poisoning:** Corrupt build artifact cache
- **Supply chain:** Key element of broader attacks

**F. Insufficient Logging/Monitoring**
- **Impact:** Tampering or malicious activity goes undetected
- **Forensics:** Can't investigate incidents effectively

#### Red Team Perspective

**Pipeline configuration audit:**
- Manual review + automated tools (Semgrep)
- Injection flaws in scripts
- Hardcoded secrets
- Insecure command usage
- Parameter handling vulnerabilities

**Permission assessment:**
- Service account/runner privileges
- Least privilege violations?
- Production secret access?
- Can modify sensitive cloud resources beyond scope?

**Build process compromise attempts:**
- Influence build parameters
- Modify source code post-checkout
- Inject malicious dependencies
- Poison artifact caches

**Log scrutiny:**
- Exposed secrets in build logs
- Internal infrastructure details
- Sensitive environment variables

**Access control testing:**
- Unauthorized pipeline triggers
- Pipeline modifications without approval
- Approval bypass attempts

#### Defensive Strategies

**Harden build agents:**
- **Minimal images:** Only necessary software
- **Network segmentation:** Isolate build environment
- **Ephemeral environments:** Destroy after each run (prevents persistent compromise)

**Least privilege:**
- **Service accounts:** Minimal necessary permissions
- **Short-lived credentials:** Rotate frequently
- **Scope limitation:** Access only required resources

**Input validation:**
- **External inputs:** Validate and sanitize all parameters
- **User-controlled data:** Never trust, always verify

**Security scanning integration:**
- **SAST:** SonarQube for code quality/security
- **SCA:** Dependency-Check, Trivy for vulnerability scanning
- **Artifact scanning:** Containers, libraries, models
- **Fail on critical:** Block builds with high-severity issues

**Secure log management:**
- **Secret scrubbing:** Remove sensitive data before storage
- **Access control:** Restrict log viewing
- **Aggregation:** Centralized logging with proper security

**Manual approvals:**
- **Sensitive deployments:** Production, security-critical changes
- **Human review:** Final gate before release

**Artifact signing:**
- **Digital signatures:** Verify integrity through pipeline
- **Trust chain:** Signed at build, verified at deployment

### 3. Artifact and Model Registries (Nexus, Artifactory, MLflow, Cloud Registries)

**Function:** Store and manage versioned artifacts
- Libraries and dependencies
- Container images
- **Trained ML models (Model Registry)** ← Critical for AI

**Importance:** Critical control points for deployment integrity

#### Attack Vectors

**A. Insecure Access Controls**
- **Unauthorized download:** Proprietary models leaked
- **Malicious upload:** Backdoored artifacts introduced
- **Overwriting:** Legitimate versions replaced
- **Deletion:** Critical artifacts removed

**B. Unverified Content**
- **CVEs:** Known vulnerabilities in uploaded artifacts
- **Unsafe code:** Pickle payloads with RCE
- **Malicious containers:** Reverse shells, crypto miners

**C. Registry Platform Vulnerabilities**
- **CVEs:** In registry software itself
- **Misconfigurations:** Default credentials, open ports

**D. Lack of Artifact/Model Signing**
- **No verification:** Consumers can't validate integrity
- **Supply chain risk:** Tampered components from earlier in pipeline
- **Trust gap:** No way to prove artifact provenance

#### Red Team Perspective

**Access control testing:**
- Different credentials/anonymous access
- Push, pull, delete, overwrite operations
- Production tag overwriting
- Cross-repository access attempts

**Platform scanning:**
- CVE scanning for registry software
- Common misconfigurations
- Default credentials
- Authentication bypass attempts

**Signing process assessment:**
- Are artifacts signed?
- **Critical:** Does deployment FAIL if unsigned or untrusted signature?
- Upload unsigned/incorrectly signed items
- Signature verification enforcement

**Malicious artifact upload:**
- Containers with reverse shells
- Models with RCE payloads (Pickle exploits)
- Test validation/scanning policies

#### Defensive Strategies

**Strong authentication and RBAC:**
- Granular permissions per repository
- Regular permission audits
- Principle of least privilege

**Mandatory artifact signing:**
- **Container images:** Docker Content Trust
- **General artifacts:** Sigstore
- **Models:** Cryptographic signatures
- **Deployment gate:** Fail if signature missing or invalid

**Automated vulnerability scanning:**
- **Containers:** Trivy, Clair
- **Models:** ModelScan
- **On upload:** Scan immediately
- **Periodic rescanning:** Catch new CVEs
- **Policy enforcement:** Block high-severity vulnerabilities

**Platform maintenance:**
- Keep registry software updated
- Apply security patches promptly
- Follow hardening guidelines
- Regular security audits

**Immutability:**
- Released versions immutable
- Unique versioning (not mutable tags like `latest`)
- Content-addressable storage

### War Story: The Silent Backdoor

**Company:** Fintech startup with ML-based fraud detection

**Attack vector:** Outdated developer credentials in secondary Git repo

**Access gained:** Limited CI/CD system access (couldn't push to production directly)

**Attack process:**

1. **Initial compromise:** Modified build script for model serialization
2. **Payload injection:** Inserted code to execute on model loading (Pickle deserialization)
3. **Malicious code:** Reverse shell connection to attacker server
4. **Automated build:** CI/CD built model with embedded payload
5. **Registry push:** Backdoored model uploaded to artifact registry

**MLOps failure:**
- **No artifact signing:** Registry lacked mandatory signing and verification
- **Mutable tag:** Deployment used 'latest' tag (no version pinning)
- **No integrity checks:** Deployment pulled backdoored model without validation

**Impact:**

**Deployment:** Backdoored model loaded in production
**Execution:** Pickle payload executed, reverse shell established
**Persistence:** Attacker gained production access, bypassed firewalls
**Undetected:** Weeks of access before unusual traffic noticed
**Exfiltration:** Customer transaction data, internal model details stolen

**Consequences:**
- Regulatory fines
- Reputational damage
- Costly MLOps security overhaul

**Lesson:** Artifact integrity verification is critical — signing and verification mandatory for production deployments

### 4. Feature Stores

**Function:** Centralize curated data features for consistent use across training and inference

**Attack surface:** Specific to ML systems

#### Attack Vectors

**A. Feature Poisoning**
- **Vector:** Compromised ingestion pipelines or weak access controls
- **Impact:** Subtle poisoning affects MULTIPLE downstream models
- **Scale:** Wider than single-model attacks
- **Details:** data-poisoning-attacks]]

**B. Insecure Access Controls**
- **Exfiltration:** Sensitive feature data leaked
- **Unauthorized modification:** Production model impact
- **Deletion:** Pipeline disruption

**C. Platform Vulnerabilities**
- **CVEs:** In feature store software itself
- **Misconfigurations:** Default settings, exposed ports

**D. Denial of Service**
- **Write flooding:** Overwhelm with requests
- **Feature corruption:** Critical features made unusable

#### Red Team Perspective

**RBAC assessment:**
- Different actions (define, ingest, retrieve)
- Training vs. inference permissions
- Role separation verification
- Cross-project access attempts

**Ingestion pipeline security:**
- Validation gaps
- Upstream malicious data injection
- Transformation step manipulation
- Data provenance verification

**Platform security:**
- CVE scanning
- Misconfiguration detection
- Default credential testing

**Unauthorized access attempts:**
- Data exfiltration
- Feature modification/deletion
- Production feature tampering

#### Defensive Strategies

**Secure ingestion pipelines:**
- **Data validation:** Strong input checks
- **Integrity checks:** Verify data hasn't been tampered
- **Source authentication:** Verify data origin
- **Lineage tracking:** Full data provenance

**Strict RBAC:**
- **Role-based:** Data scientist vs. inference service
- **Least privilege:** Minimal necessary permissions
- **Service identities:** Dedicated accounts for automated access

**Monitoring:**
- **Data quality:** Statistical checks
- **Drift detection:** Distribution changes
- **Anomaly detection:** Unusual patterns
- **Early warning:** Detect poisoning/corruption

**Platform security:**
- Keep software updated
- Secure configuration
- Regular security audits

### 5. Orchestration Tools (Kubeflow, Airflow, Argo Workflows)

**Function:** Manage and execute complex ML workflows, coordinating tasks across services

**Compromise impact:** Highly impactful due to central coordination role

#### Attack Vectors

**A. Workflow Definition Vulnerabilities**
- **Injection flaws:** Command injection in workflow definitions
- **Excessive permissions:** Workflow tasks with overly broad access
- **Secrets in definitions:** Hardcoded credentials

**B. Compromised Worker Nodes**
- **Task manipulation:** Alter task execution
- **Data access:** Steal data processed by workflows
- **Lateral movement:** Pivot to other systems

**C. Insecure APIs**
- **Unauthorized access:** Trigger/modify/delete workflows without auth
- **Credential exposure:** API keys, tokens leaked
- **DoS:** Flood with workflow executions

**D. Weak Access Controls**
- **Workflow manipulation:** Unauthorized modification
- **Execution control:** Start/stop workflows without permission
- **Data access:** View sensitive workflow data

#### Red Team Perspective

**Workflow definition review:**
- Injection vulnerabilities
- Hardcoded secrets
- Permission misconfigurations
- Input validation gaps

**Worker node security:**
- Isolation effectiveness
- Network segmentation
- Privilege escalation opportunities

**API security testing:**
- Authentication bypass
- Authorization flaws
- Rate limiting
- Input validation

**Access control verification:**
- RBAC enforcement
- Cross-user access attempts
- Privilege escalation paths

#### Defensive Strategies

**Secure workflow definitions:**
- Input validation and sanitization
- Parameterization (not string concatenation)
- Secrets from external systems (not embedded)
- Least privilege for tasks

**Worker hardening:**
- Minimal container images
- Network isolation
- Resource limits
- Regular patching

**API security:**
- Strong authentication (OAuth, mTLS)
- Fine-grained authorization
- Rate limiting
- Input validation
- Audit logging

**RBAC implementation:**
- Role-based access control
- Principle of least privilege
- Regular permission audits

## Exploiting Frameworks and Libraries

**Attack vector:** ML frameworks/libraries (TensorFlow, PyTorch, scikit-learn) essential but introduce risks

### Common Vulnerabilities

**A. Insecure Deserialization (Pickle)**
- **Risk:** Python's Pickle format allows arbitrary code execution
- **Exploitation:** Maliciously crafted model file executes on load
- **Impact:** RCE with application privileges
- **Mitigation:** Use safer formats (ONNX, SavedModel), validate sources

**B. Known CVEs**
- **Problem:** Frameworks have vulnerabilities
- **Examples:** TensorFlow CVEs, PyTorch vulnerabilities
- **Impact:** RCE, DoS, information disclosure
- **Mitigation:** Keep frameworks updated, monitor CVEs

**C. Supply Chain Attacks**
- **Vector:** Compromised packages in PyPI, npm
- **Examples:** Malicious packages with similar names (typosquatting)
- **Impact:** Code execution during install
- **Mitigation:** Pin versions, verify checksums, use private registries

### Red Team Perspective

**Dependency analysis:**
- Inventory all frameworks/libraries
- Version checking against known CVEs
- Outdated dependency identification

**Deserialization testing:**
- Model format analysis (Pickle vs. safer alternatives)
- Malicious model file creation
- Load testing in controlled environment

**Supply chain probing:**
- Dependency source verification
- Checksum validation
- Private registry usage

### Defensive Strategies

**Framework management:**
- **Version pinning:** Specific versions in requirements.txt
- **Regular updates:** Balance stability with security patches
- **Vulnerability scanning:** Automated tools in CI/CD

**Safer serialization:**
- **Avoid Pickle:** Use ONNX, TensorFlow SavedModel, TorchScript
- **Source validation:** Only load models from trusted sources
- **Sandboxing:** Isolate model loading in restricted environment

**Supply chain security:**
- **Private registries:** Internal mirror of dependencies
- **Checksum verification:** Validate package integrity
- **Dependency scanning:** Tools like Snyk, Dependabot

## Securing Cloud and Container Environments

### Cloud Security (AWS, Azure, GCP)

#### Attack Vectors

**A. Misconfigured Storage**
- **S3 buckets:** Publicly readable model files, training data
- **Blob storage:** Exposed sensitive artifacts
- **Impact:** Data/model theft, IP loss

**B. Overly Permissive IAM**
- **Excessive permissions:** Services with broader access than needed
- **Privilege escalation:** Lateral movement to other resources
- **Credential compromise:** Leaked access keys

**C. Exposed Services**
- **Public endpoints:** Jupyter notebooks, MLflow servers without auth
- **Default credentials:** Unchanged admin passwords
- **Network misconfiguration:** Services accessible from internet

#### Red Team Perspective

**Cloud resource enumeration:**
- Public S3 buckets (Bucket Hunter, s3Scanner)
- Exposed storage accounts
- Public snapshots

**IAM review:**
- Service account permissions
- Role assignments
- Privilege escalation paths
- Cross-account access

**Service exposure testing:**
- Port scanning
- Default credential attempts
- Authentication bypass
- Misconfiguration exploitation

#### Defensive Strategies

**Storage security:**
- **Access controls:** Private by default
- **Encryption:** At rest and in transit
- **Bucket policies:** Explicit deny for public access
- **Regular audits:** Automated tools (Prowler, ScoutSuite)

**IAM best practices:**
- **Least privilege:** Minimal necessary permissions
- **Role-based:** Use IAM roles, not long-lived keys
- **MFA:** Enforce multi-factor authentication
- **Regular review:** Permission audits, unused credential removal

**Network security:**
- **Private endpoints:** Keep services internal
- **VPC isolation:** Separate networks for different environments
- **Security groups:** Restrictive firewall rules
- **VPN/bastion:** Secure access to internal resources

### Container Security (Docker, Kubernetes)

#### Attack Vectors

**A. Vulnerable Base Images**
- **Outdated OS:** Known CVEs in base layers
- **Unnecessary packages:** Increased attack surface
- **Default secrets:** Hard-coded in images

**B. Container Escape**
- **Privileged containers:** Break out to host
- **Kernel exploits:** Container → host OS
- **Mounting host filesystem:** Direct access

**C. Secrets in Images**
- **Layered in build:** Credentials in intermediate layers
- **Environment variables:** Exposed via `docker inspect`
- **Config files:** Embedded in image

**D. Insecure Container Registries**
- **Covered in section 3:** Artifact registries

#### Red Team Perspective

**Image scanning:**
- Vulnerability scanning (Trivy, Clair)
- Secret detection in layers
- Compliance checking (CIS benchmarks)

**Runtime testing:**
- Privileged container detection
- Host filesystem mount identification
- Container escape attempts (if authorized)

**Registry assessment:**
- Covered in Artifact Registries section

#### Defensive Strategies

**Image hardening:**
- **Minimal base images:** Distroless, Alpine
- **Multi-stage builds:** Don't include build tools in final image
- **Regular updates:** Patch base images
- **Image scanning:** Automated in CI/CD

**Runtime security:**
- **Drop capabilities:** Remove unnecessary Linux capabilities
- **Read-only filesystems:** Where possible
- **User namespaces:** Non-root users in containers
- **Security policies:** PodSecurityPolicies (K8s) or alternatives

**Secrets management:**
- **Never in images:** Use external secrets management
- **Kubernetes secrets:** Encrypted at rest
- **Vault integration:** HashiCorp Vault for secret injection

## GPU-Specific Attacks and Defenses in AI

**Unique risk:** Specialized GPU hardware introduces AI-specific attack surfaces

### LeftoverLocals Vulnerability (CVE-2024-0911)

**Description:** GPU memory leak vulnerability allowing unauthorized access to data from previous computations

**Affected:** AMD, Apple, Qualcomm GPUs

**Attack scenario:**
- Attacker's code runs on same GPU after victim's computation
- Reads leftover data from GPU memory
- Extracts sensitive information (model weights, training data)

**Impact:**
- Model theft
- Training data leakage
- Privacy violation

**Mitigation:**
- GPU memory clearing after use
- Firmware/driver updates
- Isolation between workloads

### GPU.zip Side-Channel Attack

**Description:** Compression-based side-channel attack exploiting GPU memory compression

**Mechanism:**
- Observe GPU memory compression ratios
- Infer information about data being processed
- Extract sensitive details via timing analysis

**Impact:**
- Infer private keys
- Leak model information
- Data exfiltration

**Mitigation:**
- Disable compression in sensitive contexts
- Constant-time operations
- Physical isolation for critical workloads

### Red Team Perspective

**GPU vulnerability testing:**
- Check for LeftoverLocals (CVE-2024-0911)
- Test memory isolation between workloads
- Side-channel analysis attempts
- Driver/firmware version assessment

**Multi-tenancy risks:**
- Shared GPU environments
- Cloud GPU instances
- Container-based GPU sharing

### Defensive Strategies

**GPU security:**
- **Regular updates:** Firmware, drivers
- **Memory clearing:** After each workload
- **Workload isolation:** Dedicated GPUs for sensitive tasks
- **Monitoring:** Unusual GPU access patterns

**Multi-tenancy:**
- **Physical isolation:** For highest-security workloads
- **Trusted execution environments:** GPU-based TEEs
- **Resource quotas:** Limit GPU access per tenant

## Securing the Data Architecture Infrastructure

**Components:** Databases, data lakes, data warehouses, streaming pipelines

### Attack Vectors

**A. SQL Injection**
- **Classic attack:** Unsanitized inputs in SQL queries
- **Impact:** Data extraction, modification, deletion
- **AI context:** Training data poisoning via database

**B. Weak Access Controls**
- **Problem:** Overly permissive database users
- **Impact:** Unauthorized data access
- **Lateral movement:** Database → other systems

**C. Unencrypted Data**
- **At rest:** Plaintext databases
- **In transit:** Unencrypted connections
- **Impact:** Data exposure if breached

**D. Data Pipeline Manipulation**
- **ETL compromise:** Alter data during transformation
- **Stream poisoning:** Inject malicious data in real-time pipelines
- **Impact:** Training data corruption

### Red Team Perspective

**Injection testing:**
- SQL injection in application queries
- NoSQL injection
- Graph query injection

**Access control assessment:**
- Database user permissions
- Cross-database access
- Privilege escalation

**Encryption verification:**
- At-rest encryption enabled?
- TLS for connections?
- Key management practices

**Pipeline security:**
- ETL script vulnerabilities
- Streaming pipeline authentication
- Data validation gaps

### Defensive Strategies

**Query security:**
- **Parameterized queries:** Always
- **ORM usage:** Abstracts SQL construction
- **Input validation:** Defense in depth

**Access control:**
- **Least privilege:** Minimal necessary permissions
- **Network isolation:** Database in private subnet
- **Authentication:** Strong passwords, certificate-based auth

**Encryption:**
- **At rest:** Transparent data encryption
- **In transit:** TLS/SSL for all connections
- **Key management:** Hardware security modules (HSMs)

**Pipeline security:**
- **Input validation:** At every stage
- **Authentication:** Verify data sources
- **Monitoring:** Anomaly detection in data flows

## API Security for AI Systems

**Critical interface:** APIs expose AI functionality to external systems/users

### Attack Vectors

**A. Lack of Rate Limiting**
- **Attack:** Automated model extraction via excessive queries
- **DoS:** Resource exhaustion
- **Impact:** Model theft, service disruption
- **Details:** model-extraction-stealing]]

**B. Weak Authentication**
- **API keys:** Leaked, guessable, long-lived
- **No auth:** Publicly accessible endpoints
- **Impact:** Unauthorized access

**C. Insufficient Input Validation**
- **Injection:** SQL, command, prompt injection
- **Oversized inputs:** Buffer overflows, DoS
- **Malformed data:** Crashes, unexpected behavior

**D. Information Leakage**
- **Detailed errors:** Stack traces revealing internals
- **Excessive responses:** Model confidence scores enabling extraction
- **Metadata:** API version, framework details

**E. Business Logic Flaws**
- **Authorization bypass:** Access other users' data
- **Price manipulation:** Free tier abuse
- **Output manipulation:** Trick API into harmful responses

### Red Team Perspective

**Rate limiting testing:**
- Automated query scripts
- Multi-account orchestration
- Distributed request sources

**Authentication bypass:**
- API key enumeration
- Token manipulation
- Session hijacking
- OAuth flow exploitation

**Input fuzzing:**
- Malformed inputs
- Oversized payloads
- Special characters
- Encoding exploits

**Information gathering:**
- Error message analysis
- Response timing analysis
- API documentation review
- Metadata extraction

**Business logic:**
- Multi-step attack chains
- Authorization boundary testing
- Resource manipulation

### Defensive Strategies

**Rate limiting:**
- **Per-user limits:** Query quotas
- **Adaptive throttling:** Based on behavior
- **Distributed rate limiting:** Across API gateway cluster

**Strong authentication:**
- **OAuth 2.0:** Industry standard
- **API keys:** Short-lived, rotated regularly
- **mTLS:** Mutual TLS for highest security
- **MFA:** For sensitive operations

**Input validation:**
- **Whitelist approach:** Allow known-good, deny rest
- **Type checking:** Enforce expected types
- **Size limits:** Prevent oversized inputs
- **Sanitization:** Clean inputs before processing

**Minimal information disclosure:**
- **Generic errors:** Don't reveal internals
- **Limited responses:** Only necessary data
- **Confidence thresholds:** Reduce extraction risk

**API gateway:**
- **Centralized control:** Authentication, rate limiting, logging
- **WAF integration:** Web application firewall
- **DDoS protection:** Absorb large-scale attacks

## Software Supply Chain Security for AI

**Extended attack surface:** AI systems depend on vast software supply chain

### Supply Chain Layers

**A. Training Data Sources**
- **Public datasets:** Hugging Face, Kaggle, academic repos
- **Risk:** Pre-poisoned data
- **Mitigation:** Provenance verification, validation

**B. Pre-trained Models**
- **Model zoos:** TensorFlow Hub, PyTorch Hub, Hugging Face
- **Risk:** Backdoored models
- **Mitigation:** Signature verification, scanning, trusted sources only

**C. ML Frameworks**
- **Core libraries:** TensorFlow, PyTorch, JAX
- **Risk:** Vulnerable versions, malicious updates
- **Mitigation:** Version pinning, checksum verification

**D. Dependencies**
- **Python packages:** NumPy, Pandas, scikit-learn
- **Risk:** Typosquatting, compromised packages
- **Mitigation:** Private registries, dependency scanning

### Attack Vectors

**A. Dependency Confusion**
- **Attack:** Internal package name on public PyPI
- **Exploitation:** Package manager pulls malicious public version
- **Impact:** Code execution during install

**B. Typosquatting**
- **Attack:** Package with similar name (e.g., `numppy` instead of `numpy`)
- **Exploitation:** Developer typo installs malicious package
- **Impact:** Supply chain compromise

**C. Compromised Upstream**
- **Attack:** Legitimate package compromised (e.g., left-pad incident)
- **Exploitation:** Update pulls malicious code
- **Impact:** Widespread compromise

**D. Pre-trained Model Backdoors**
- **Attack:** Publicly available model contains hidden backdoor
- **Exploitation:** Fine-tuning preserves backdoor
- **Impact:** Deployed model compromised

### Red Team Perspective

**Dependency analysis:**
- Inventory ALL dependencies (direct + transitive)
- Check for known vulnerabilities (CVEs)
- Identify unmaintained packages
- Review licensing

**Source verification:**
- Download sources match expected checksums?
- Package signatures valid?
- Repository ownership verified?

**Model provenance:**
- Pre-trained model source trusted?
- Checksum verification
- Backdoor scanning (if tools available)

**Testing:**
- Dependency confusion scenarios
- Typosquatting simulation
- Update mechanism testing

### Defensive Strategies

**Dependency management:**
- **Lock files:** requirements.txt with exact versions
- **Checksum verification:** Validate package integrity
- **Private registries:** Mirror external packages internally
- **Minimal dependencies:** Only what's necessary

**Vulnerability scanning:**
- **Automated tools:** Snyk, Dependabot, safety
- **CI/CD integration:** Fail builds on critical CVEs
- **Regular scans:** Not just at build time

**Supply chain validation:**
- **SBOM:** Software Bill of Materials
- **Provenance tracking:** Know where everything comes from
- **Attestation:** Cryptographic proof of build process

**Model security:**
- **Trusted sources only:** Verified model providers
- **Scanning:** ModelScan for backdoor detection
- **Fine-tuning validation:** Test for unexpected behaviors

**Namespace protection:**
- **Reserve names:** Register internal package names on public repos
- **Scoped packages:** Use namespaces (@company/package)

## Key Takeaways

1. **Infrastructure often softer target** than model-level attacks — easier to compromise
2. **Cascading impacts:** Single MLOps component compromise affects entire pipeline
3. **AI-specific surfaces:** Feature stores, model registries, GPU vulnerabilities unique to AI
4. **Traditional + AI-specific:** Need both general infrastructure security AND ML-specific protections
5. **Supply chain critical:** Vast dependencies introduce significant risk
6. **Artifact signing mandatory:** Prevent tampered models/containers reaching production
7. **Ephemeral build environments:** Prevent persistent compromise of CI/CD
8. **Secrets never in code:** Use dedicated management systems
9. **GPU vulnerabilities:** LeftoverLocals, GPU.zip show hardware-level risks
10. **API rate limiting:** Essential defense against model extraction
11. **Defense in depth:** Layer multiple controls across all infrastructure components
12. **Monitoring essential:** Detect tampering and malicious activity
13. **Systems thinking:** Attackers chain multiple small flaws for big impact
14. **War story lesson:** Unsigned artifacts + mutable tags = backdoor deployment

## Source Citations

> "While much AI security focus lands on the models themselves—addressing evasion attacks, data poisoning, or prompt injection—the underlying infrastructure and operational pipelines that build, deploy, and manage these models represent a critical, often softer, attack surface."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 275

> "If an attacker compromises the MLOps pipeline, they might bypass sophisticated adversarial techniques needed to influence the AI directly. Instead, they could tamper with sensitive training data leading to biased outcomes, inject malicious code executing with system privileges, steal valuable proprietary models, or disrupt critical operations."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 275-276

> "Defending the MLOps pipeline requires protecting not just the flow of data and artifacts, but each constituent part individually and the connections between them."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 278

> "A critical defense is mandating digital signing of all artifacts/models (e.g., using Docker Content Trust, Sigstore) and implementing signature verification as a mandatory deployment gate."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 282

## Related

- **Mitigated by**: [[defenses/ai-infrastructure-security]], [[defenses/access-segmentation-and-rbac]], [[defenses/telemetry-and-monitoring-architecture]]
- **ATLAS**: [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/acquire-infrastructure|AML.T0008]]
