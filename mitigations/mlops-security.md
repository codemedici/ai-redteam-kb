---
title: MLOps Security Controls
tags:
  - type/mitigation
  - type/control-framework
  - target/ml-lifecycle
  - source/adversarial-ai
  - source/developers-playbook-llm
  - needs-review
maturity: stub
created: 2026-02-14
updated: 2026-02-14
---
## Summary

MLOps (Machine Learning Operations) provides foundational security defenses against adversarial attacks—particularly [[techniques/data-poisoning-attacks]]—by introducing **traceability, automation, and continuous validation** into the AI/ML development lifecycle. MLOps transforms ad-hoc ML workflows into secure, auditable pipelines with checkpoints and guarantees.

> "MLOps is a foundational security defense that needs to be in place. It introduces some core principles to introduce traceability into the AI/ML development life cycle."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 86


## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/]] | |

## Implementation

[To be completed]

## Limitations & Trade-offs

MLOps **reduces attack surface** but doesn't eliminate poisoning risk:

- **Sophisticated attacks** — Clean-label attacks may pass validation
- **Insider threats** — Authorized users can still poison data
- **Supply chain** — External datasets/models may be pre-poisoned (see [[techniques/supply-chain-poisoning]])

**MLOps must be combined with:**
- [[mitigations/anomaly-detection-architecture]]
- [[mitigations/adversarial-training]]
- [[mitigations/robustness-testing]]

> "Not all attacks will be applicable, and you don't have to implement all the defenses. As we will see in Chapter 14, threat modeling can help us decide which ones are the most prevalent and offer the best value."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 86


## Testing & Validation

[To be completed]

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| | [[frameworks/atlas/tactics/]] | |

## Sources

[To be completed]

## Why MLOps for Security

Traditional cybersecurity (least-privilege, encryption, data signing) **makes poisoning harder** but insufficient alone. MLOps extends these controls with:

- **Automated tracking** — Changes to data/models are logged automatically
- **Approval gates** — Significant changes require human review
- **Continuous monitoring** — Real-time alerts on anomalies
- **Integrated defenses** — Anomaly detection and validation in pipelines

> "We need to see these techniques as part of an integrated system of defenses combining techniques with automated tracking, approvals, monitoring, and alerting."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 81


## Core MLOps Security Principles

### 1. Automation & Pipeline Orchestration

**Three core pipelines:**
- **Application pipeline** — CI/CD for ML application code
- **Data pipeline** — Automated data ingestion, validation, preprocessing
- **ML pipeline** — Model training, evaluation, deployment

**Security benefit:** Reduces manual intervention points where attacks can be injected.

### 2. Continuous X

**Extends CI/CD to ML-specific activities:**
- **Continuous Integration** — Code + data + model testing
- **Continuous Deployment** — Automated model serving
- **Continuous Training** — Automated retraining on new data
- **Continuous Monitoring** — Model performance tracking

### 3. Versioning

**Data versioning:**
- Track changes to datasets over time
- Identify when/where poisoned data entered pipeline
- Rollback to clean dataset versions

**Model versioning:**
- Maintain history of trained models
- Compare performance across versions
- Detect degradation from poisoning

**Implementation:** Model registries (MLflow, SageMaker Model Registry)

### 4. Experiment Tracking

**Extends source control to ML artifacts:**
- Data used in training
- Model weights and biases
- Hyperparameters
- Training metrics

**Security benefit:** Full audit trail for forensic analysis after poisoning detected.

### 5. Testing

**Test coverage across pipelines:**
- **Functional tests** — Model accuracy, performance
- **Security tests** — Robustness against adversarial attacks
- **Bias tests** — Fairness validation
- **Quality tests** — Data integrity checks

**See [[mitigations/robustness-testing]] for adversarial test techniques.**

### 6. Monitoring & Alerting

**Extends system monitoring to ML:**
- **Model performance** — Accuracy, precision, recall
- **Model behavior** — Distribution shifts, anomalies
- **Data quality** — Schema validation, statistical checks
- **Audit logs** — Data changes, unusual training times

**Alert on:**
- Unexpected accuracy drops (possible untargeted poisoning)
- Training at unusual times (insider threat)
- Unauthorized data changes

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 81-82, 86


## MLOps Security Controls for Poisoning Defense

### Data Validation
**Continuous validation of training data integrity:**
- Schema checks (expected columns, types)
- Statistical validation (distribution checks)
- Integration with [[mitigations/anomaly-detection-architecture]]

**Platforms:** AWS SageMaker Data Wrangler, TFX Data Validation

### Data Lineage & Provenance
**Track data origin and transformations:**
- Where data came from (source system, collection method)
- Who accessed/modified data
- What transformations were applied

**Enables:** [[mitigations/data-provenance]] defenses

**Benefit:** Identify poisoned data sources during incident response.

### Access Control
**Restrict who can modify training artifacts:**
- Role-based access control (RBAC) for data/models
- Least-privilege access
- Multi-factor authentication (MFA) for production changes

**Prevents:** Insider threats, post-breach lateral movement to ML systems.

### Model Interpretability
**Understand model behavior:**
- Feature importance analysis
- Explainable AI (SHAP, LIME)
- Identify if specific features have undue influence (sign of poisoning)

**Tools:** AWS SageMaker Clarify, Azure ML Interpretability

### Governance & Collaboration
**Approval gates for changes:**
- Data changes require approval
- Model promotion (dev → staging → prod) requires review
- Automated checks before approval

**Benefit:** Human review catches suspicious changes automated systems miss.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 81-82


## Platform Examples

### AWS SageMaker
- **Data Wrangler** — Data validation and preprocessing
- **Model Registry** — Model versioning and lineage
- **Model Monitor** — Continuous performance monitoring
- **Clarify** — Bias detection and model interpretability

### Azure Machine Learning
- **Datasets** — Data versioning and lineage
- **Model Registry** — Model management
- **Responsible AI Dashboard** — Interpretability and fairness

### MLflow (Open Source)
- **Tracking** — Experiment logging
- **Projects** — Reproducible runs
- **Model Registry** — Model versioning
- **Integration** — Works with SageMaker, Azure ML, custom pipelines

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 81


## GAN-Specific Supply Chain Security

GANs introduce unique supply chain risks that MLOps controls must address:

**Applicable attack vectors:**
- **Poisoning attacks** — Poisoned GAN training data or tampered models
- **Transitive data poisoning** — Poisoned GAN used for data augmentation produces poisoned downstream datasets
- **Supply chain risks** — Popular GANs distributed as pickle files present high deserialization risk

**Required controls:**
- **Model and data provenance** — Verify GAN source (e.g., official NVIDIA repository vs. untrusted Google Drive links)
- **Pickle scanning** — Use tools like Protect.AI ModelScan to scan serialized model files for malicious payloads before loading
- **Access control and governance** — RBAC and approval gates for GAN model deployment
- **Gated APIs** with rate limiting for public-facing generative AI applications
- **Authentication** for all generative AI endpoints
- **Monitoring and auditing** — Identify abuse of internal generative AI systems; prevent deepfake production for unethical purposes
- **Mobile development protections** for mobile apps incorporating generative models

> Source: [[sources/bibliography#Adversarial AI]], p. 318-319


## Integration with MLSecOps

**MLSecOps** extends MLOps with security-first principles:
- Shift-left security (embed in design phase)
- Automated security testing in pipelines
- SBOM (Software Bill of Materials) for ML
- See [[playbooks/mlsecops]] for detailed patterns

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 82, 86


## MLOps Principles Reference

**Core principles from ml-ops.org:**
1. Automation and orchestration
2. Continuous X (CI/CD/CT/CM)
3. Versioning (data + models)
4. Experiment tracking
5. Testing (functional, security, bias, quality)
6. Monitoring (performance + behavior)

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 86  
> Reference: https://ml-ops.org/content/mlops-principles


## LLMOps: Extending MLOps for Large Language Models (Developer's Playbook — Wilson)

### Evolution: DevOps → DevSecOps → MLOps → LLMOps

**DevOps (early 2000s):** Bridge development and operations teams through collaboration, automation, CI/CD

**DevSecOps:** Embed security at every phase, not as afterthought

**MLOps:** Automate ML lifecycle (data prep, training, deployment, monitoring) with focus on reproducibility and model drift

**LLMOps:** Address unique LLM challenges:
- Prompt engineering
- Model fine-tuning
- RAG (Retrieval-Augmented Generation)
- Advanced deployment strategies for high computational load
- Monitoring qualitative aspects of model outputs
- Managing model-generated content risks

> "MLOps, while crucial in establishing practices for any application leveraging machine learning, doesn't address all the unique challenges LLMs pose. LLMs introduce specific challenges, such as prompt engineering, robust monitoring to capture the nuanced performance, and the potential misuse of generated outputs."
> 
> Source: [[sources/developers-playbook-llm]], p. 140-141

### LLMOps Security Lifecycle

**Five critical stages for secure LLM deployment:**

| Stage | Security Measures |
|-------|------------------|
| **1. Foundation Model Selection** | • Opt for models with robust security features<br>• Assess security history and vulnerability reports<br>• Review model card for security info<br>• Evaluate training datasets<br>• Watch for new versions with security improvements |
| **2. Data Preparation** | • Carefully evaluate data sources<br>• Scrub, anonymize, free from illegal/inappropriate content<br>• Evaluate for bias<br>• Secure data handling and access controls<br>• Protect during fine-tuning or embedding generation |
| **3. Validation** | • LLM-specific vulnerability scanners<br>• AI red teaming exercises<br>• Test for toxicity and bias<br>• Extend security testing beyond traditional SAST/DAST |
| **4. Deployment** | • Runtime guardrails for prompt and output screening<br>• Automate ML-BOM generation and storage<br>• Secure deployment pipeline |
| **5. Monitoring** | • Log all activity<br>• Monitor for anomalies (jailbreaks, DoS attempts)<br>• Track model performance and drift |

> Source: [[sources/developers-playbook-llm]], p. 141-142

### CI/CD Security for LLMs

**Critical practices:**

**Pipeline security:**
- Integrate security checks into CI/CD to detect vulnerabilities early
- Treat training data repositories like source code (access control, version control)
- Protect against data poisoning attacks

**Dependency management:**
- Regularly audit and update dependencies
- **Note:** ML-specific components like PyTorch have had zero-day vulnerabilities
- Mitigate outdated or compromised libraries

**Access control & monitoring:**
- Limit access to CI/CD environment
- Monitor activity for suspicious behavior
- Prompt detection and response

> Source: [[sources/developers-playbook-llm]], p. 142-143

**Fostering security culture:**
- **Training and awareness** — Educate team on supply chain security, including foundation models and training datasets
- **Incident response planning** — Develop and update plans addressing supply chain threats and zero-day disclosures

> Source: [[sources/developers-playbook-llm]], p. 143

### LLM-Specific Security Testing Tools

**Traditional AST tools (SAST, DAST, IAST) insufficient for LLM-specific risks.** Emerging specialized tools:

#### TextAttack
- **Type:** Python framework for adversarial testing of NLP models
- **License:** MIT (open source)
- **Capabilities:**
  - Modular architecture for customizable attack strategies
  - Simulate adversarial examples to reveal weaknesses
  - Detailed reports on attack methodologies, success rates, model responses
- **Use:** Enhance model resilience, security assessments

#### Garak
- **Creator:** Leon Derczynski (OWASP Top 10 LLM contributor)
- **Type:** LLM vulnerability scanner (DAST-like)
- **License:** Apache (open source)
- **How it works:**
  - Sends various prompts to models
  - Analyzes outputs using detectors for unwanted content
  - Customizable with plug-ins
  - Generates detailed reports (test parameters, prompts, responses, scores)

#### Microsoft Responsible AI Toolbox
- **Type:** Open source suite for ethical AI
- **License:** MIT
- **Capabilities:**
  - Assess, improve, monitor models for fairness, interpretability, privacy
  - Integrated environment for responsible AI dimensions

#### Giskard LLM Scan
- **Type:** Ethical/safety assessment tool
- **License:** Apache 2.0
- **Capabilities:**
  - Identify biases
  - Detect toxic content
  - Metrics for fairness, toxicity, inclusiveness
  - Detailed reports highlighting ethical risks

**Integration imperative:** Embed tools in CI/CD pipelines for automated, repeatable security checks with every build. Fosters security-first culture.

> Source: [[sources/developers-playbook-llm]], p. 143-145

### Supply Chain Artifact Management

**Critical artifacts:**
- **Model cards** — Model purpose, performance, biases
- **ML-BOMs** — Components, datasets, dependencies

**Three pillars for artifact tracking:**

1. **Automated generation** — Generate at key development milestones
2. **Secure storage** — Version-controlled, tamper-proof repositories
3. **Accessibility** — Available to stakeholders with search functionality

> Source: [[sources/developers-playbook-llm]], p. 145-146

### Guardrails: Runtime Protection for LLMs

**Parallel to WAFs/RASP in web applications.** Guardrails ensure LLMs operate within ethical, legal, safety parameters.

#### Input Validation Guardrails

**Prompt injection prevention:**
- Monitor for unusual phrases, hidden characters, odd encodings
- Prevent malicious LLM manipulation

**Domain limitation:**
- Keep LLM focused on relevant topics
- Restrict/ignore irrelevant prompts
- Reduces hallucination risk

**Anonymization & secret detection:**
- Users may input confidential data (emails, phone numbers, API keys)
- **Risk:** Data logged, stored, transferred to third-party providers, used for training
- **Solution:** Anonymize PII, redact sensitive data before LLM processing

#### Output Validation Guardrails

**Ethical screening:**
- Filter toxic, inappropriate, hateful content
- **Example:** Could have saved Tay from unchecked toxicity

**Sensitive information protection:**
- Prevent disclosure of PII or sensitive data in outputs

**Code output monitoring:**
- Detect unintended code generation (SQL injection, SSRF, XSS)

**Compliance assurance:**
- Tailor outputs for regulatory standards (health care, legal)
- Keep responses within intended scope

**Fact-checking & hallucination detection:**
- Verify accuracy against trusted sources
- Identify fictitious or irrelevant content
- Ensure outputs remain grounded in reality

> Source: [[sources/developers-playbook-llm]], p. 146-148

#### Open Source vs. Commercial Guardrails

**Open source examples:**
- NVIDIA NeMo-Guardrails
- Meta Llama Guard
- Guardrails AI
- Protect AI

**Benefits:** Flexibility, community support, tailored solutions  
**Cost:** Requires internal expertise and resources

**Commercial examples:**
- Prompt Security
- Lakera Guard
- WhyLabs LangKit
- Lasso Security
- PromptArmor
- Cloudflare Firewall for AI

**Benefits:** Out-of-box functionality, professional support, regular updates, advanced features

**Best practice:** Mix custom and packaged guardrails (defense-in-depth)

> Source: [[sources/developers-playbook-llm]], p. 148

### Monitoring LLM Applications

**Comprehensive monitoring extends beyond traditional components (web servers, middleware, databases) to:**
- The model itself
- Vector databases (for RAG)

#### Logging Every Prompt and Response

**Purpose:**
- Understand user interaction patterns
- Identify misuse or problematic outputs
- Baseline for model performance over time
- Diagnose issues, optimize behavior
- Ensure compliance with data governance

**Critical for:** Operational integrity and security throughout lifecycle

#### Centralized Log and Event Management (SIEM)

**Integration:** LLM-specific logs with traditional security logs

**Enables:**
- Holistic view of security posture
- Correlation of LLM events with broader system events
- Advanced threat detection

**UEBA (User and Entity Behavior Analytics):**
- Analyze patterns in user/entity behavior
- Identify anomalies indicating security threats
- Example: Unusual prompt patterns, spike in requests, repeated attempts to trigger prohibited content

> Source: [[sources/developers-playbook-llm]], p. 149-150

### Continuous Improvement

**Establishing and tuning guardrails:**
- Iterative process of refining input/output rules
- Balance security with functionality
- Adapt to emerging threats

**Managing data access and quality:**
- Ongoing vigilance over data sources
- Ensure training/RAG data remains high-quality and secure

**RLHF (Reinforcement Learning from Human Feedback):**
- Align LLM with human values and safety standards
- Use human feedback to guide model towards desirable behavior
- Minimize harmful/biased outputs

> Source: [[sources/developers-playbook-llm]], p. 154-156
