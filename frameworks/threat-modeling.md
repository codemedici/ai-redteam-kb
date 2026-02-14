---
title: "AI Threat Modeling Framework"
description: "Comprehensive framework for threat modeling AI systems using secure by design principles, industry taxonomies, and risk-based prioritization"
tags:
  - type/framework
  - target/llm
  - target/ml-model
  - target/generative-ai
  - source/adversarial-ai
  - source/developers-playbook-llm
---

# AI Threat Modeling Framework

A structured approach to identifying, assessing, and mitigating threats to AI systems using secure-by-design principles, industry taxonomies (NIST, MITRE ATLAS, OWASP), and risk-based prioritization.

## Overview

Threat modeling for AI extends traditional cybersecurity threat modeling to incorporate AI-specific risks including data poisoning, evasion attacks, model extraction, and generative AI threats. This framework provides a systematic approach to building secure AI systems from the design phase through deployment and operations.

> **Core Principle:** Move from lab-based, research-driven adversarial AI toward a system-based, risk-oriented approach that incorporates adversarial AI research into proven enterprise security methodologies.
>
> Source: [[sources/bibliography#Adversarial AI]], p. 438-440

## Secure by Design AI (SbD AI) Methodology

The SbD AI methodology is based on joint guidelines developed by UK NCSC and US CISA, extending traditional secure-by-design approaches to incorporate adversarial AI and emphasizing security integration from the earliest stages of development.

### Core Activities (Iterative Cycle)

1. **Threat Modeling**
   - Map the system architecture with data flows and trust boundaries
   - Identify threats using standardized threat dictionaries
   - Apply risk management to prioritize threats
   - Includes traditional STRIDE/PASTA plus AI-specific threats
   
2. **Security Design**
   - Map mitigations to specific security controls
   - Integrate controls into AI solution architecture
   - Design secure data and MLOps pipelines
   - Apply privacy-preserving techniques (differential privacy, federated learning) based on data sensitivity

3. **Secure Implementation**
   - Implement designed controls in model development and deployment
   - Ensure data integrity and protect against backdoors
   - Secure the supply chain
   - Apply DevSecOps/MLSecOps practices

4. **Testing and Verification**
   - Perform adversarial testing and red teaming
   - Conduct penetration testing of AI system and infrastructure
   - Use formal verification methods for critical components
   - Apply OWASP LLM Verification Standard (LLMVS)

5. **Deployment**
   - Follow secure deployment processes
   - Configure production environment securely
   - Apply patches and updates
   - Set up continuous monitoring and incident response

6. **Live Operations**
   - Continuous monitoring for attacks and anomalous behavior
   - Automated alerting for security incidents
   - Implement incident response plan
   - Regular security audits and updates

> **Note:** This is a cycle, not a linear sequence. New requirements, continuous improvement, and lessons learned from incidents trigger iteration through all steps.
>
> Source: [[sources/bibliography#Adversarial AI]], p. 439-440

### Contextual and Risk-Based Approach

The methodology adopts a **contextual and risk-based attitude**:
- Not all threats are equally applicable or important
- Research paper demonstrations don't always translate to easily exploitable attacks
- Different AI architectures have different risk profiles:
  - Generative AI via third-party LLM API
  - Predictive AI with image recognition on IoT devices
  - Fine-tuned models with RAG
- Avoid "gold plating" by uncritically implementing all possible mitigations

> Source: [[sources/bibliography#Adversarial AI]], p. 438

## AI Threat Library

A comprehensive threat dictionary organized into four categories, mapped to industry taxonomies.

### Category 1: Traditional Cybersecurity Threats

| ID | Threat | Stage | Description |
|----|--------|-------|-------------|
| T01 | Data Leak | Dev, Prod | Unauthorized access and exfiltration of sensitive data |
| T02 | Tampering | Dev, Prod | Malicious change of data or artifacts |
| T03 | Malware Attacks | Dev, Prod | Use of malware to gain control, damage, exfiltrate data, or stage ransomware |
| T04 | Privilege Escalation | Dev, Prod | Gaining unauthorized access via broken access control |
| T05 | Denial of Service | Dev, Prod | Disrupt system use via excessive usage or other techniques |

> **Note:** AI development environments are production-like due to reliance on live data, elevating traditional cyber risks.
>
> Source: [[sources/bibliography#Adversarial AI]], p. 441-442

### Category 2: Adversarial AI Attacks (General)

| ID | Threat | Stage | Description | Related Notes |
|----|--------|-------|-------------|---------------|
| T06 | [[techniques/data-poisoning-attacks\|Data Poisoning]] | Dev | Inject malicious data to degrade or influence model performance (base modeling, fine-tuning, RAG) | Ch. 4, 15 |
| T07 | [[techniques/backdoor-poisoning\|Backdoor Attacks]] | Dev | Targeted poisoning to insert specific activation backdoor | Ch. 4, 15 |
| T08 | Model Tampering | Dev, Prod | Tamper with hyperparameters/weights, inject trojans, insecure serialization, model editing (ROME for LLMs) | Ch. 5, 16 |
| T09 | Model Theft | Dev, Prod | Physical model theft and exfiltration from dev or prod | Ch. 3, 8 |
| T10 | [[techniques/adversarial-examples-evasion-attacks\|Evasion]] | Prod | Use adversarial inputs to evade classification (general or targeted misclassifications) | Ch. 7 |
| T11 | Model Reprogramming | Prod | Exploit online training or reinforcement learning to poison deployed model | Ch. 7 |
| T12 | [[techniques/model-extraction\|Model Extraction]] | Prod | Use inputs/outputs to create functional equivalent of target model | Ch. 8 |
| T13 | [[techniques/model-inversion-attacks\|Model Inversion]] | Prod | Use inputs/outputs to approximate model's training data | Ch. 9 |
| T14 | Membership Inference | Prod | Infer whether a record was used in training | Ch. 9 |
| T15 | Attribute Inference | Prod | Infer an attribute for a record | Ch. 9 |
| T16 | ML DoS | Prod | Use adversarial samples to make model unusable | Ch. 3, 7 |

> Source: [[sources/bibliography#Adversarial AI]], p. 442-443

### Category 3: Generative AI-Specific Threats

| ID | Threat | Stage | Description | Related Notes |
|----|--------|-------|-------------|---------------|
| T17 | [[techniques/prompt-injection\|Direct Prompt Injection]] | Prod | Manipulation of conversational context to bypass safety measures (jailbreaking) | Ch. 14 |
| T18 | [[techniques/prompt-injection\|Indirect Prompt Injection]] | Prod | Manipulation of data used in prompts indirectly (LLM with internet access or RAG) | Ch. 14 |
| T19 | Data Disclosure | Prod | Exploit LLMs to divulge system config, system prompts, connection data, or downstream service data | Ch. 14 |
| T20 | Insecure Output | Prod | Exploitation of unvalidated executable content | Ch. 14 |
| T21 | Excessive Agency | Prod | Exploitation of excessive functionality or permissions given to LLM | Ch. 14 |
| T22 | Agent Hijacking | Prod | Exploit vulnerabilities in autonomous agents to hijack or influence behavior | Ch. 14 |
| T23 | Training Data Extraction | Prod | Use prompt injections to exfiltrate memorized training data (direct extraction, not reconstruction) | Ch. 16 |
| T24 | [[techniques/model-extraction-llm\|Model Replication]] | Prod | Use LLM instruction training to create datasets to train base LLM to replicate target | Ch. 16 |
| T25 | AI Misuse/Abuse | Prod | Use GenAI to produce harmful, misleading, unsafe, unethical content (phishing, malware, deepfakes, privacy inference, overreliance) | Ch. 12, 13 |

> Source: [[sources/bibliography#Adversarial AI]], p. 443-444

### Category 4: Supply Chain Threats

| ID | Threat | Stage | Description | Related Notes |
|----|--------|-------|-------------|---------------|
| T26 | [[techniques/supply-chain-model-poisoning\|Supply Chain Model Compromise]] | Dev, Prod | Exploitation of poisoned/tampered models via supply chain; includes vulnerabilities inherited via transfer learning | Ch. 6, 16 |
| T27 | Supply Chain Data Compromise | Dev, Prod | Exploitation of poisoned/tampered data via supply chain; includes stored prompt injection for LLMs | Ch. 6, 16 |
| T28 | Supply Chain Package Vulnerability | Dev, Prod | Exploitation of vulnerable ML/AI frameworks and components; includes malicious serialization from external sources | Ch. 3, 6 |
| T29 | Supply Chain System Compromise | Dev, Prod | Exploitation of systems (GPU, cloud, dev environment) via supply chain | Ch. 3, 6, 16 |

> Source: [[sources/bibliography#Adversarial AI]], p. 444

Related: [[playbooks/ai-supply-chain-security]]

## Industry Taxonomy Mappings

### NIST AI 100-2 E2023 Mapping

**NIST Focus:** Comprehensive catalog of attacks and mitigations based on published research (98 pages, research-oriented).

**Key Differences:**
- More focused on pure adversarial AI attacks
- More detailed subcategories (e.g., availability poisoning, backdoor poisoning, targeted poisoning, clean label poisoning)
- Merges model inversion and training data extraction into single "Data Reconstruction" category
- Does not cover physical model theft or non-adversarial attacks
- Less coverage of emerging GenAI agent threats

**Example Mappings:**
- **Data Poisoning** → NIST: Data Poisoning (with subcategories)
- **Model Tampering** → NIST: Model Poisoning
- **Model Inversion** → NIST: Data Reconstruction
- **ML DoS** → NIST: Availability Poisoning, Energy Latency, Increased Computation, Availability Violations
- **Model Replication** → Not covered in NIST

> **Use Case:** Delve deeper into research references for specific threats and associated attacks.
>
> Source: [[sources/bibliography#Adversarial AI]], p. 446-448

Learn more: https://csrc.nist.gov/pubs/ai/100/2/e2023/final

### OWASP AI Exchange Mapping

**AI Exchange Focus:** Emphasis on enterprise security with mature security controls integrated into organizational security programs.

**Key Features:**
- Work in progress but emphasizes life cycle coverage
- Model theft appears twice (development-time and runtime)
- More descriptive threat names for better readability
- Comprehensive set of controls easily integrated into enterprise security
- Covers non-adversarial attacks under data leak entries
- Includes generic non-AI-specific application security threats

**Example Mappings:**
- **Data Poisoning** → Data poisoning
- **Backdoor Attacks** → Evasion after data poisoning
- **Model Tampering** → Development-time model poisoning
- **Model Theft** → Runtime model theft, model theft through development-time model parameter leak
- **Model Extraction** → Model theft by use
- **Direct Prompt Injection** → Direct prompt injection
- **Training Data Extraction** → Data extraction

> **Use Case:** Identify security controls for threats and integrate into organizational security programs. Expected to become de facto threat library as it matures.
>
> Source: [[sources/bibliography#Adversarial AI]], p. 448-449

Learn more: https://owaspai.org

Related: [[frameworks/OWASP-top-10-LLM]], [[mitigations/owasp-llm-mitigation-strategies]]

### MITRE ATLAS Mapping

**ATLAS Focus:** Tactics, Techniques, and Procedures (TTPs) model showing attacker kill chain, not just a threat library.

**Key Features:**
- First-level practices reflect generic attack kill chain
- Uses industry-standard STIX format (integrates with threat intelligence tools)
- Helps understand attack steps, vulnerable solution parts, exploitability, and likelihood
- Maps threats to specific techniques showing how attackers realize threats

**ATLAS Tactics (Kill Chain Flow):**
1. Reconnaissance
2. Resource Development
3. Initial Access
4. ML Model Access
5. Execution
6. Persistence
7. Privilege Escalation
8. Defense Evasion
9. Credential Access
10. Discovery
11. Collection
12. ML Attack Staging
13. Exfiltration
14. Command and Control
15. Impact

**Example Technique Mappings:**
- **Data Poisoning** → AML.T0012 Valid Accounts, AML.T0020 Poison Training Data
- **Backdoor Attacks** → AML.T0018 Backdoor ML Model, AML.T0018.001 Inject Payload, AML.T0043.004 Insert Backdoor Trigger
- **Model Extraction** → AML.T0024.002 Extract ML Model
- **Direct Prompt Injection** → AML.T0051.000 Direct, AML.T0054 LLM Jailbreak
- **Indirect Prompt Injection** → AML.T0051.001 Indirect
- **Training Data Extraction** → AML.T0024.000 Infer Training Data Membership
- **AI Misuse/Abuse** → AML.T0048 External Harms, AML.T0052 ML-Enabled Phishing, AML.T0048.000-003 (Financial/Reputational/Societal/User Harm)

> **Use Case:** Understand the steps an attacker takes to deliver a threat. Helps identify vulnerable parts of solution, exploitability, and likelihood. Maps threat models to threat intelligence.
>
> Source: [[sources/bibliography#Adversarial AI]], p. 449-452

Learn more: https://atlas.mitre.org

Related: [[frameworks/atlas/tactics/ai-attack-staging]], [[frameworks/atlas/tactics/initial-access]]

## Threat Modeling Process

### Step 1: Map the System

Create detailed description of the AI system including:
- **Components:** Models, data stores, APIs, pipelines
- **Data flows:** Training data flow, inference flow, update/fine-tuning flow
- **Interfaces:** Public APIs, internal APIs, third-party integrations
- **Trust boundaries:** Critical delineation points

#### Trust Boundaries

Trust boundaries delineate where security controls and policies are enforced, separating trusted zones from untrusted ones.

**Examples:**
- Interface between APIs and public internet
- External authentication providers
- Third-party model operators (e.g., OpenAI ChatGPT)
- Vector database SaaS providers
- Model repositories (TensorFlow Hub, Hugging Face)
- Data from external systems (sales/marketing systems)

**Out of Trust Boundary = Not Responsible for Security**
- Cannot control their security policies
- Must secure communication with these systems
- Apply supplier due diligence, terms & conditions review, independent audits (e.g., SOC2)

> Source: [[sources/bibliography#Adversarial AI]], p. 452, 454

### Step 2: Identify Critical Assets and Data Sensitivity

Determine what needs protection based on **CIA triad**:
- **Confidentiality:** Sensitive commercial data, API keys, system prompts, embeddings
- **Integrity:** Model weights, training data, ingredients database, bot configuration
- **Availability:** Production models, critical services

**Data Sensitivity Levels:**
- Personal data (GDPR/privacy regulations)
- Sensitive commercial data
- Public or non-sensitive data

> Source: [[sources/bibliography#Adversarial AI]], p. 454

### Step 3: Identify Data Flows

Map all data flows through the system:
- **Training/Fine-tuning flows:** Initiated by data scientists
- **Embedding generation flows:** Initiated by data engineers or automated schedules
- **User/inference flows:** Initiated by end users
- **Update/deployment flows:** Model deployment and updates

> Source: [[sources/bibliography#Adversarial AI]], p. 455

### Step 4: Identify Threats

Walk through each flow and asset, identifying applicable threats from the threat library:
1. Collaborative exercise with cross-functional team
2. Discuss feasibility of each attack
3. Cross-reference [[frameworks/atlas/tactics/ai-attack-staging|ATLAS TTPs]] to understand attacker techniques
4. Evaluate feasibility of attack chains
5. Identify possible mitigations

> **Approach:** Not exhaustive listing, but focused on credible, relevant threats based on the specific system architecture and context.
>
> Source: [[sources/bibliography#Adversarial AI]], p. 455

## Case Study: Enhanced FoodieAI Threat Model

### System Overview

Mobile app combining Predictive AI (image recognition) and Generative AI (recipe chatbot):
1. User takes photo of dish
2. Ingredients recognition service (CNN from TensorHub, fine-tuned)
3. Results sent to FoodieAI chatbot
4. RAG search of user reviews and pricing (MiniLM embeddings, SaaS vector DB)
5. ChatGPT LLM generates recipes and nutritional advice
6. UK-only, authenticated via social login (OpenID Connect)

### Trust Boundaries
- **Outside trust boundary:** Social login, ChatGPT LLM, Hugging Face, TensorFlow Hub, external image database, sales/marketing system, mobile app development team

### Critical Assets
- Ingredients database (commercial data)
- Fine-tuning dataset
- Embeddings
- Image recognition CNN model
- Ingredients recognition model
- Bot configuration (API keys, meta prompt)

### Threat Model by Flow

#### Flow 1: Image Recognition Fine-Tuning

**Key Threats:**
- **T04 Privilege Escalation (HIGH):** Unauthorized access to fine-tuning environment enables white-box attacks, data exfiltration
- **T26 Supply Chain Model Compromise (MEDIUM):** Malicious model from TensorHub
- **T27 Supply Chain Data Compromise (MEDIUM):** Poisoned external image data
- **T03 Malware Attacks (MEDIUM):** Malicious external model
- **T06 Data Poisoning (LOW):** Bias toward specific ingredients or performance degradation
- **T08 Model Tampering (LOW):** Including stored prompt injections in ingredients DB

**Mitigations:**
- Network segregation, MFA, least-privilege RBAC
- Model and data provenance checks
- Monitoring, alerting, auditing
- Data anomaly detection
- Model and package scanning
- MLOps governance

> Source: [[sources/bibliography#Adversarial AI]], p. 455-457

#### Flow 2: Embeddings Generation

**Key Threats:**
- **T04 Privilege Escalation (HIGH):** Access to vector DB and ingredients DB
- **T01 Data Leak (MEDIUM):** Ingredients data, embeddings
- **T03 Malware Attacks (MEDIUM):** Malicious MiniLM transformer
- **T26 Supply Chain Model Compromise (MEDIUM):** Poisoned transformer
- **T16 ML DoS (MEDIUM):** Disruption of embedding generation

**Mitigations:**
- Segregation between flows (network subnets, different roles)
- Least-privilege access (embeddings role ≠ fine-tuning role)
- Supply chain validation
- Access control for vector data store

> Source: [[sources/bibliography#Adversarial AI]], p. 457-458

#### Flow 3: End User (Public API)

**Primary attack surface for black-box attacks.**

**Key Threats (HIGH):**
- **T03 Malware Attacks:** Malicious photo uploads
- **T04 Privilege Escalation:** API exploitation
- **T17 Direct Prompt Injection:** Jailbreaking FoodieAI
- **T25 AI Misuse/Abuse:** Harmful/unethical content generation creating legal/reputational risks

**Key Threats (MEDIUM):**
- **T01 Data Leak:** Config, ingredients DB, vector DB
- **T05 Denial of Service:** System overwhelm
- **T16 ML DoS:** Model degradation
- **T18 Indirect Prompt Injection:** Via tampered ingredients DB or embeddings
- **T19 Data Disclosure:** System prompts, config, data
- **T20 Insecure Output:** Unvalidated LLM responses
- **T29 Supply Chain System Compromise:** Via ChatGPT API

**Mitigations:**
- Restrict access to API only, require TLS
- Valid authentication tokens
- Least-privilege API access
- Input validation and sanitization
- Content checking and malware scanning for uploads
- Guardrails in prompt templates
- Output encoding
- Monitoring, auditing, alerting
- API rate limiting (prevent extraction attacks, make attacks expensive)
- Model watermarking
- Supplier due diligence for ChatGPT (terms prohibit data storage, SOC2 audit)

> Source: [[sources/bibliography#Adversarial AI]], p. 458-460

## Risk Assessment and Prioritization

### Risk Calculation

**Basic Formula:**
```
Risk Score = Likelihood × Impact
```

Organizations should use their own risk assessment framework that models impact at appropriate levels of granularity (often measuring financial cost).

### Numerical Scale Approach

Scale 1-10 for both likelihood and impact:
- Risk Score range: 1 (least severe) to 100 (most severe)
- Resembles percentage, familiar to stakeholders
- Can appear arbitrary and be misinterpreted

### Qualitative Levels Approach (Recommended)

**Levels:** LOW, MEDIUM, HIGH, CRITICAL (or CERTAIN for likelihood)

**Risk Matrix:**

| Likelihood | Low Impact | Medium Impact | High Impact | Critical Impact |
|------------|-----------|---------------|-------------|-----------------|
| Low | Low | Low | Medium | Medium |
| Medium | Low | Medium | Medium | High |
| High | Medium | Medium | High | High |
| Certain | Medium | High | High | Critical |

**Guidance for Numerical Mapping:**
- 1-2.5: Low
- 2.6-5: Medium
- 5.1-7.5: High
- 7.6-10: Critical

**Risk Level Meanings:**
- **Low:** Monitor, no immediate action needed
- **Medium:** Moderate concern, may require specific management plans
- **High:** Serious concern, requires immediate attention
- **Critical:** Highest priority, demands immediate action to prevent catastrophic impacts

> Source: [[sources/bibliography#Adversarial AI]], p. 461-463

### Adjusted Matrix for Lower Risk Tolerance

Organizations with low risk tolerance skew scoring toward impact:

| Likelihood | Low Impact | Medium Impact | High Impact | Critical Impact |
|------------|-----------|---------------|-------------|-----------------|
| Low | Low | Low | Medium | **High** |
| Medium | Low | Medium | **High** | **High** |
| High | Medium | Medium | **High** | **Critical** |
| Certain | Medium | High | **Critical** | **Critical** |

> Source: [[sources/bibliography#Adversarial AI]], p. 463

### Types of Risk

- **Inherent Risk:** Risk before applying mitigations (used to prioritize threats)
- **Residual Risk:** Risk after applying mitigations (used to assess acceptability)

**Risk Appetite:**
- Establish with risk owner in transparent, accountable manner
- Typically remediate CRITICAL and HIGH risks (depends on risk tolerance and context)
- Context matters: Early bootstrapping vs. production environment with sensitive data
- Review with risk owner and stakeholders to gauge appetite and confirm acceptable residual risk

> Source: [[sources/bibliography#Adversarial AI]], p. 464

### FoodieAI Risk Assessment Example

**Ingredients Recognition Fine-Tuning Flow:**
- **T04 Privilege Escalation:** High likelihood, High impact = **HIGH RISK**
- **T01 Data Leak:** High likelihood, Medium impact = **MEDIUM RISK**
- **T03, T26, T27 Supply Chain:** Medium likelihood, High impact = **MEDIUM RISK**
- **T06, T07, T08, T09, T12-15 Adversarial AI:** Low likelihood, Medium/Low impact = **LOW RISK**

**End User Flow:**
- **T03 Malware, T04 Privilege Escalation, T17 Direct Prompt Injection, T25 AI Misuse/Abuse:** High likelihood, High impact = **HIGH RISK**
- **T01, T05, T16, T18, T19, T20, T29:** Medium/High likelihood, Medium/High impact = **MEDIUM RISK**
- **T07, T10, T12-15, T28:** Low/Medium likelihood, Low/Medium impact = **LOW RISK**

> **Important Lesson:** Risk prioritization can be at odds with threat volume. Just because something is possible or likely doesn't mean it must be mitigated if impact and overall risk aren't judged important by the risk owner. Contextual, risk-based approach prevents "gold plating."
>
> Source: [[sources/bibliography#Adversarial AI]], p. 464-468

## Security Design and Implementation

### Mapping Mitigations to Security Controls

Once threats are prioritized, map mitigations to **standardized security controls** using industry frameworks:
- **CIS Controls:** Platform security
- **OWASP ASVS (Application Security Verification Standard):** Application security
- **OWASP AI Exchange:** AI-specific security controls
- **MITRE ATLAS Mitigations:** AI-specific defensive measures

**Benefits of Standard Controls:**
- Verifiable security design
- Understood by most architects and engineers
- Supported by tools
- Easier integration into enterprise security programs

> Source: [[sources/bibliography#Adversarial AI]], p. 468

### Example Control Mappings

#### Traditional Cybersecurity Threats

**T01 Data Leak:**
- **Mitigations:** Encrypt sensitive data, implement strict access controls
- **Controls:**
  - ASVS 7.4: Verify sensitive data is encrypted
  - CIS 14.4: Encrypt all sensitive information in transit
  - MITRE ATLAS: AML.M0012 Encrypt Sensitive Information
  - OWASP AI Exchange: DATAENCRYPTION, ACCESSCONTROL

**T04 Privilege Escalation:**
- **Mitigations:** Least privilege principle, role-based access control
- **Controls:**
  - ASVS 4.3: Verify admin interfaces only accessible to authorized users
  - CIS 4.1: Maintain inventory of administrative accounts
  - MITRE ATLAS: AML.M0005 Control Access to ML Models and Data at Rest
  - OWASP AI Exchange: PRIVILEGEMANAGEMENT, ACCESSCONTROL

> Source: [[sources/bibliography#Adversarial AI]], p. 469-470

#### Adversarial AI Attacks

**T06 Data Poisoning:**
- **Mitigations:** Encrypt training data, anomaly detection, continuous validation
- **Controls:**
  - ASVS 7.4: Verify sensitive data is encrypted
  - CIS 14.4: Encrypt all sensitive information in transit
  - MITRE ATLAS: AML.M0007 Sanitize Training Data
  - OWASP AI Exchange: OBFUSCATETRAININGDATA, DETECTODDINPUT, CONTINUOUSVALIDATION

**T10 Evasion:**
- **Mitigations:** Input validation/sanitization, adversarial training
- **Controls:**
  - ASVS 7.3: Verify all user inputs are validated
  - CIS 16.2: Configure centralized point of authentication
  - MITRE ATLAS: AML.M0003 Model Hardening
  - OWASP AI Exchange: INPUTVALIDATION, ADVERSARIALTRAINING

**T12 Model Extraction:**
- **Mitigations:** Strong API authentication/authorization, API usage monitoring
- **Controls:**
  - ASVS 4.2: Verify strong authentication
  - CIS 16.3: Require multifactor authentication
  - MITRE ATLAS: AML.M0019 Control Access to ML Models and Data in Production
  - OWASP AI Exchange: APIAUTHENTICATION, APIMONITORING

> Source: [[sources/bibliography#Adversarial AI]], p. 471-475

#### Generative AI-Specific Threats

**T17 Direct Prompt Injection:**
- **Mitigations:** Input validation and sanitization, context-aware escaping
- **Controls:**
  - ASVS 5.1: Verify all data inputs are validated
  - CIS 16.5: Implement host-based intrusion detection
  - MITRE ATLAS: AML.M0006 Implement Secure Coding Practices
  - OWASP AI Exchange: INPUTVALIDATION, CONTEXTAWAREESCAPING

**T25 AI Misuse/Abuse:**
- **Mitigations:** Usage policies, monitoring, rate limiting
- **Controls:**
  - ASVS 9.3: Verify rate limiting is implemented
  - CIS 16.2: Configure centralized point of authentication
  - MITRE ATLAS: AML.M0010 Implement Usage Monitoring and Controls
  - OWASP AI Exchange: USAGEPOLICIES, RATELIMITING

> Source: [[sources/bibliography#Adversarial AI]], p. 476-478

#### Supply Chain Threats

**T26 Supply Chain Model Compromise:**
- **Mitigations:** Regular audits/validation, secure update mechanisms
- **Controls:**
  - ASVS 12.1: Verify secure update mechanism for all components
  - CIS 2.3: Ensure integrity of system and software files
  - MITRE ATLAS: AML.M0016 Validate Third-Party Components
  - OWASP AI Exchange: SUPPLYCHAINVALIDATION, SECUREUPDATES

> Source: [[sources/bibliography#Adversarial AI]], p. 479-480

### Architecture Integration

After mapping controls:
1. Annotate solution architecture with security controls
2. Create security viewpoint for architecture
3. Capture security requirements in NFRs (Non-Functional Requirements)
4. Create backlog items with acceptance criteria and Definition of Done
5. Technical teams discuss implementation details (adversarial training, data sanitization, guardrails, etc.)

> Source: [[sources/bibliography#Adversarial AI]], p. 481

## Testing and Verification

### AI-Specific Testing Challenges

Traditional application testing is well understood (OWASP ASVS provides comprehensive guide), but AI introduces new challenges:
- **Third-party model benchmarking and verification**
- **Data anomaly testing**
- **Adversarial robustness testing**

### OWASP LLM Verification Standard (LLMVS)

Equivalent of OWASP ASVS for LLM systems (currently v0.1 draft, jointly developed by Snyk and Lakera).

**Security Assurance Levels (driven by data sensitivity):**
- **Level 1:** Basic assurance
- **Level 2:** Moderate assurance
- **Level 3:** High assurance

**Coverage Areas:**
- Secure configuration and maintenance
- Model life cycle
- Real-time learning
- Model memory and storage
- Secure LLM integration
- Agents and plugins
- Dependency and component management
- Monitoring and anomaly detection

> Source: [[sources/bibliography#Adversarial AI]], p. 481

Learn more: https://owasp.org/www-project-llm-verification-standard/

### Shift-Left Approach

**Principle:** Integrate security testing early and throughout the AI development life cycle.

**Key Practices:**

1. **CI/CD Security Integration**
   - Security checks and tests in CI/CD pipeline
   - Early detection and remediation
   - Automated scanning of models, data, and code

2. **Developer Training and Awareness**
   - AI-specific security concerns
   - Secure-by-design principles
   - Adversarial AI attack patterns

3. **Automated Security Testing**
   - Continuous testing and monitoring
   - Facilitate early detection and response
   - Model scanning, data validation, dependency checks

> **Elevation to MLSecOps:** The complexity of AI threats and interplay of DevOps and MLOps elevates these practices to MLSecOps patterns.
>
> Source: [[sources/bibliography#Adversarial AI]], p. 482

Related: [[frameworks/llmops-framework]]

## Live Operations

Securing AI extends beyond development to deployment and operations.

### Secure Deployment

**Practices:**
- Containerization and secure configuration management
- Secure coding practices and code reviews
- Thorough testing in secure staging environment
- Access controls and authentication for deployment pipeline
- Encrypt sensitive data and configure secure network channels

> Source: [[sources/bibliography#Adversarial AI]], p. 482

### Monitoring and Alerting

**Establish comprehensive monitoring:**
- Track system performance, user activities, security events in real time
- Automated monitoring and alerting for anomalies, model drift, security incidents
- Alerts for critical events: data breaches, unauthorized access, system failures
- Monitor model performance and accuracy for degradation or unexpected behavior
- Regular log review to identify patterns, trends, potential risks

> Source: [[sources/bibliography#Adversarial AI]], p. 483

### Auditing

**Regular security audits:**
- Evaluate effectiveness of security controls
- Identify areas for improvement
- Perform data audits (integrity and confidentiality of sensitive information)
- Audit supply chain components (models, external services) for security and compliance
- Maintain audit trails and documentation (access logs, config changes, incident response)
- Engage third-party security experts for independent audits and penetration testing

> Source: [[sources/bibliography#Adversarial AI]], p. 483

### Business Continuity

**Ensure availability and resilience:**
- Develop and test comprehensive business continuity plan
- Implement redundancy and failover mechanisms for critical components
- Regular backups (data, model checkpoints) stored in secure off-site locations
- Establish contingency plans for rapid recovery and restoration
- Conduct regular drills and simulations
- Test effectiveness and identify improvement areas

> Source: [[sources/bibliography#Adversarial AI]], p. 484

### Incident Handling

**Effective incident response:**
- Clear incident response plan (roles, responsibilities, procedures)
- Incident response team with necessary skills and expertise
- Detection and alerting mechanisms for quick identification
- Thorough investigations (root cause, impact assessment, remediation actions)
- Communicate with stakeholders (users, regulators, law enforcement as needed)
- Document and learn from incidents to improve response and strengthen security posture

> Source: [[sources/bibliography#Adversarial AI]], p. 484

## Beyond Security: Trustworthy AI

### Security as Guardian of Trustworthy AI

AI security is no longer simply about traditional cybersecurity. Safeguarding against adversarial AI elevates security as **an essential element and guardian of Trustworthy AI**.

**Trustworthy AI Framework:** Responsible and ethical AI development promoting reliability, transparency, fairness, accountability, and ethical behavior aligned with human values and societal norms.

### Components of Trustworthy AI

#### Safety
- **Reliability:** Consistent, accurate performance across scenarios with minimal errors
- **Transparency:** Visibility into functioning and decision-making processes; explainability of algorithmic decisions and training data

#### Ethics
- **Fairness:** Treat all individuals/groups fairly without bias; avoid discrimination, promote equal opportunities
- **Accountability:** Mechanisms for holding developers, deployers, users accountable; transparency about responsibilities and liabilities
- **Ethical Behavior:** Adhere to ethical principles; respect human rights, privacy, societal norms

> Source: [[sources/bibliography#Adversarial AI]], p. 485

### Security's Dual Role in Trustworthy AI

1. **Ensures Bare Minimum Safety and Ethics by Default**
   - Mitigates misuse and abuse
   - Ensures output safety
   - Prevents overreliance
   - Raises threshold of secure-by-design

2. **Supports Organizational Safety and Ethical Guidelines**
   - Leverages robust cybersecurity controls and assurance approaches
   - Provides complementary support to dedicated safety and ethics disciplines
   - Degree of collaboration depends on organizational roles, responsibilities, governance

> Source: [[sources/bibliography#Adversarial AI]], p. 486-487

### Key Trustworthy AI Frameworks

**Industry Standards:**
- **IEEE Global Initiative on Ethics:** Ethically Aligned Design (EAD) - https://ethicsinaction.ieee.org/
- **EU Ethics Guidelines for Trustworthy AI:** - https://digital-strategy.ec.europa.eu/en/library/ethics-guidelines-trustworthy-ai
- **OECD Principles on AI:** - https://www.oecd.org/going-digital/ai/principles/
- **NIST Framework for AI Trustworthiness:** - https://www.nist.gov/publications/framework-advancing-cybersecurity-adoption-ai-and-autonomous-systems

**Industry Leaders:**
- **Google AI Principles:** - https://ai.google/principles/
- **Microsoft AI Ethics Guidelines:** - https://www.microsoft.com/en-us/ai/responsible-ai
- **Partnership on AI:** - https://www.partnershiponai.org/

> Source: [[sources/bibliography#Adversarial AI]], p. 486

## Related Frameworks

- [[frameworks/MAESTRO|MAESTRO Framework]] - Multi-layered AI security framework
- [[frameworks/STRATEGEMS|STRATEGEMS Framework]] - Strategic AI security methodology
- [[frameworks/OWASP-top-10-LLM|OWASP Top 10 for LLM Applications]] - Critical vulnerabilities in LLM applications
- [[frameworks/atlas/tactics/ai-attack-staging|MITRE ATLAS]] - AI attack tactics and techniques

## Related Playbooks

- [[playbooks/rag-retrieval-assessment|RAG Security Assessment]]
- [[playbooks/ai-supply-chain-security|AI Supply Chain Security]]
- [[playbooks/llm-application-red-team|LLM Red Teaming]]

## Related Techniques and Mitigations

**Attack Techniques:**
- [[techniques/data-poisoning-attacks|Data Poisoning]]
- [[techniques/backdoor-poisoning|Backdoor Attacks]]
- [[techniques/prompt-injection|Prompt Injection]]
- [[techniques/model-extraction|Model Extraction]]
- [[techniques/model-inversion-attacks|Model Inversion]]
- [[techniques/adversarial-examples-evasion-attacks|Evasion Attacks]]
- [[techniques/rag-data-poisoning|RAG Data Poisoning]]
- [[techniques/supply-chain-model-poisoning|Supply Chain Attacks]]

**Mitigations:**
- [[mitigations/owasp-llm-mitigation-strategies|OWASP LLM Mitigations]]
- [[mitigations/rag-access-control|RAG Access Control]]

## Tools and Resources

- **MITRE ATLAS Matrix:** https://atlas.mitre.org/matrices/ATLAS
- **NIST AI 100-2 E2023:** https://csrc.nist.gov/pubs/ai/100/2/e2023/final
- **OWASP AI Exchange:** https://owaspai.org
- **OWASP Top 10 for LLM:** https://genai.owasp.org
- **OWASP LLMVS:** https://owasp.org/www-project-llm-verification-standard/
- **Draw.io AI Threat Library:** Drag-and-drop threats on flows and assets for visual threat modeling

## Key Takeaways

1. **Risk-Based, Not Research-Based:** Focus on applicable threats based on specific system context, not just research papers
2. **Iterative Methodology:** Threat modeling → Design → Implementation → Testing → Deployment → Operations (cycle repeats)
3. **Taxonomy Integration:** Leverage NIST (research depth), ATLAS (attack TTPs), OWASP AI Exchange (enterprise controls)
4. **Trust Boundaries Matter:** Clearly define what's in/out of your control
5. **Prioritize by Risk:** Likelihood × Impact, not by threat count or research hype
6. **Standard Controls:** Map to CIS, OWASP ASVS, AI Exchange for verifiable, tool-supported security
7. **Shift-Left Testing:** Integrate security early (MLSecOps)
8. **Security as Guardian:** Security ensures minimum safety/ethics and supports broader Trustworthy AI goals

> **Source:** This framework synthesizes secure-by-design AI methodology from joint UK NCSC and US CISA guidelines, industry taxonomies (NIST, MITRE, OWASP), and practical threat modeling approaches.
>
> Full details: [[sources/bibliography#Adversarial AI]], Chapter 17: Secure by Design and Trustworthy AI, p. 437-487

---

## LLM-Specific Trust Boundaries (Developer's Playbook - Wilson)

### Overview: Trust Boundaries in LLM Applications

Unlike traditional web applications that rely on predefined algorithms and static databases, **LLMs utilize massive neural networks to generate dynamic, context-aware responses**. This seismic shift brings unique security challenges requiring a different approach to trust boundary definition.

> "In application security, a trust boundary serves as an invisible, yet crucial, demarcation line that separates different components or entities based on their level of trustworthiness. These boundaries delineate areas where data or control flow changes from one level of trust to another."
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 19

**Key Principle:** Understanding trust boundaries is **critical to threat modeling**. Properly defining and recognizing these boundaries can be the difference between a secure system and one vulnerable to threats.

### LLM Application Architecture Trust Boundaries

LLM applications introduce **five primary trust boundaries** where data crosses from one trust level to another. Each boundary requires specific security measures:

```
┌─────────────────────────────────────────────────┐
│            LLM Application Ecosystem             │
│                                                  │
│  ┌──────────────────────────────────────────┐   │
│  │         Trust Boundary 1: Model          │   │
│  │  (Public API vs Privately Hosted)        │   │
│  └──────────────▲───────────▲────────────────┘   │
│                 │           │                     │
│    ┌────────────┴───┐   ┌───┴─────────────┐      │
│    │ TB2: User      │   │ TB5: Internal   │      │
│    │ Interaction    │   │ Services        │      │
│    └────────────────┘   └─────────────────┘      │
│                                                  │
│    ┌────────────────┐   ┌─────────────────┐      │
│    │ TB3: Training  │   │ TB4: External   │      │
│    │ Data           │   │ Data Sources    │      │
│    └────────────────┘   └─────────────────┘      │
└─────────────────────────────────────────────────┘
```

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 19-20 (Figure 3-2)

---

### Trust Boundary 1: The Model

The language model serves as the **intellectual core** of any LLM application. Your choice of model hosting significantly impacts trust boundary placement.

#### Public API Models (Third-Party Hosted)

**Examples:** OpenAI GPT-4, Anthropic Claude, Google Gemini

**Trust Boundary:** Data **exits your secure network** and enters external system when making API requests.

**Advantages:**
- Convenience and lower up-front costs
- Third-party manages updates and maintenance
- Reduced infrastructure burden

**Security Risks:**
- **Sensitive data exposure:** Data crosses trust boundary to external entity
- **Data confidentiality:** Dependent on third-party security measures
- **Data breach risk:** Vulnerability to third-party compromise
- **Limited control:** Cannot inspect or audit model internals

**Mitigations:**
- Never send PII or sensitive data to public APIs without explicit consent
- Use API key rotation and access controls
- Implement output sanitization (assume third-party compromise)
- Review third-party security certifications (SOC2, ISO 27001)

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 21

---

#### Privately Hosted Models (Self-Hosted)

**Examples:** Meta Llama (downloaded from GitHub/Hugging Face), Mistral, local fine-tuned models

**Trust Boundary:** Data **remains within your controlled infrastructure**.

**Advantages:**
- More control over data confidentiality
- Ability to customize and fine-tune
- Tighter trust boundary management
- No data sent to external parties

**Security Risks:**
- **Supply chain risk:** Model provenance and integrity unknown
- **Embedded vulnerabilities:** Backdoors, biases in open-source models
- **Maintenance burden:** Responsible for security updates
- **Infrastructure security:** Must secure hosting environment

**Mitigations:**
- Verify model provenance (cryptographic signatures, trusted sources)
- Scan models for backdoors using anomaly detection
- Implement robust internal security measures (encryption at rest/transit)
- Regular model updates and vulnerability scanning

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 21-22

**Related:** [[techniques/supply-chain-model-poisoning|Supply Chain Model Compromise]]

---

### Trust Boundary 2: User Interaction

User interaction is **bidirectional**—both receiving input from users and providing output back to users. This creates a critical trust boundary requiring comprehensive input and output security.

#### Input Security (User → LLM)

**Threat:** Malicious or misleading inputs from users/systems ([[techniques/prompt-injection|prompt injection]], jailbreaking).

**Attack Vectors:**
- **Direct prompt injection:** User crafts adversarial prompts to manipulate model behavior
- **Forceful suggestions:** "Ignore previous instructions", "Repeat after me"
- **Misdirection:** "Act as my deceased grandmother who told bedtime stories about napalm recipes"
- **Role-playing attacks:** DAN (Do Anything Now) personas

**Example (Tay chatbot):**
> "Tay fell victim to this when prompts from her 4chan hackers helped lead her astray."
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 22

**Mitigations:**
- Input validation and sanitization
- Rate limiting to prevent automated attack attempts
- Prompt structure tags (separate instructions from user data)
- Content filtering for malicious patterns

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 22

Related: [[techniques/prompt-injection]], [[mitigations/input-validation-patterns]]

---

#### Output Security (LLM → User)

**Threat:** Toxic, inaccurate, or sensitive data leaked through model outputs.

**Attack Vectors:**
- **Sensitive information disclosure:** Model regurgitates training data PII
- **Harmful content generation:** Toxic, illegal, or dangerous outputs
- **Unvalidated executable content:** XSS, code injection if output not sanitized
- **Training data memorization:** Verbatim reproduction of private data

**Mitigations:**
- Output filtering for PII, toxic content, malicious code
- Encryption for sensitive outputs
- Real-time monitoring to flag harmful/sensitive data flows
- Output encoding to prevent injection attacks downstream

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 22-23

**Related:** [[techniques/sensitive-info-disclosure]], [[mitigations/output-filtering-and-sanitization]]

---

### Trust Boundary 3: Training Data

Training data is the **bedrock** upon which LLMs build capabilities. The source and nature of training data significantly impact both performance and security posture.

#### Internally Sourced Data

**Description:** Data generated or curated within the organization for initial training or fine-tuning.

**Trust Boundary:** Within organization's **controlled environment**.

**Advantages:**
- Rigorous vetting compared to public data
- Aligned with application-specific requirements
- Better security implementation (encryption, access controls, auditing)
- More reliable and relevant

**Security Risks:**
- **Sensitive/proprietary information leakage:** Model memorizes and exposes confidential data
- **Data breach impact:** Compromise could leak training set
- **Over-privileged access:** Insiders with training data access

**Mitigations:**
- Comprehensive data governance policies
- Encryption of training data at rest/transit
- Access controls and auditing for training datasets
- PII scrubbing before training
- Differential privacy techniques

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 23

**Related:** [[techniques/sensitive-info-disclosure]], [[mitigations/differential-privacy]]

---

#### External/Public Data ("In the Wild")

**Description:** Data sourced from public repositories, web scraping, crowdsourced datasets.

**Trust Boundary:** Extends to **external entities** generating or hosting data (more porous).

**Advantages:**
- Diversity and scale
- Lower cost (often free)
- Broader knowledge coverage

**Security Risks:**
- **Data poisoning:** Malicious inputs designed to corrupt model ([[techniques/data-poisoning-attacks|data poisoning attacks]])
- **Bias and toxicity:** Unvetted content contains harmful patterns
- **Misleading information:** False or adversarial data
- **Supply chain compromise:** Poisoned public datasets

**Example (Tay chatbot):**
> "Tay was digesting user prompts directly as training data. In this way, remnants of toxic prompts became part of her knowledge base, and then she began to spill poisonous output. Accepting unfiltered, untrusted user input into your training dataset is the simplest example of a failure to manage this critical security boundary."
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 23

**Mitigations:**
- Rigorous filtering and validation of external data
- Continuous monitoring for anomalies
- Content moderation before ingestion
- Source reputation scoring
- Data provenance tracking

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 23-24

**Related:** [[techniques/data-poisoning-attacks]], [[techniques/fine-tuning-poisoning]], [[mitigations/data-quarantine-procedures]]

---

### Trust Boundary 4: Access to Live External Data Sources

Live external data sources enable **real-time information** (web access, third-party APIs, external databases), enhancing functionality but introducing security complexity.

#### Capabilities and Risks

**Examples:**
- Google Gemini's live web access vs ChatGPT's static training cutoff
- Third-party API integrations (weather, stock prices, news feeds)
- Real-time database queries

**Advantages:**
- Real-time, up-to-date information
- Enhanced user experience
- Broader functional range

**Security Risks:**
- **Ingesting false/harmful information:** Compromised websites, malicious APIs
- **Indirect prompt injection:** Malicious content in web pages ([[techniques/prompt-injection#indirect-prompt-injection|indirect prompt injection]])
- **Malware conduits:** External sources deliver malicious payloads
- **Unauthorized data access:** LLM as pivot point to internal systems
- **Uncontrolled data sources:** Cannot enforce security standards on external entities

> "The untrusted nature of these data sources makes them inherently less controllable than internal resources, thereby adding an additional layer of uncertainty and risk."
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 25

**Mitigations:**
- Additional validation and security checks for external data
- Content filtering and sanitization
- Allowlist/blocklist for external sources
- Monitoring for anomalous external data patterns
- Sandboxing external data processing
- Rate limiting external API calls

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 25

**Related:** [[techniques/prompt-injection#indirect-prompt-injection|Indirect Prompt Injection]], [[techniques/rag-data-poisoning|RAG Data Poisoning]]

---

### Trust Boundary 5: Access to Internal Services

Internal services (databases, internal APIs, backend systems) often house **critical organizational data** and serve as the operational backbone for LLM applications.

#### Internal Services as Trust Boundaries

**Examples:**
- SQL databases (user profiles, transaction logs)
- Vector databases (RAG embeddings)
- Internal APIs (authentication, business logic)
- Configuration databases
- Proprietary backend systems

**Trust Characteristics:**
- Reside within organization's secure network
- Often contain "crown jewels" of sensitive/valuable data
- Subject to uniform security policies
- Higher trust level than external sources

**Security Risks:**
- **False sense of security:** "Internal = secure" mindset leads to complacency
- **Unauthorized access:** LLM as bypass to database access controls
- **Data leaks:** Model exposes database contents via queries
- **SQL injection via LLM:** User prompt → LLM generates SQL → executes against DB
- **Internal threats:** Malicious insiders leveraging LLM access
- **Privilege escalation:** LLM has over-privileged DB access

**Example Attack (Database Access):**
User prompt: "Show me all customer credit card numbers"  
→ LLM generates: `SELECT * FROM customers WHERE ccn IS NOT NULL`  
→ Executes against database → Exfiltrates PII

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 25-26

**Mitigations:**
- **Least privilege:** LLM database credentials limited to necessary tables/operations
- **Input validation:** Sanitize prompts before SQL generation
- **Query allowlisting:** Only permit pre-approved query patterns
- **Access logging:** Audit all LLM-generated database queries
- **Output filtering:** Redact PII before returning to user
- **Human-in-the-loop:** Require approval for sensitive operations

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 26

**Related:** [[techniques/prompt-injection|Prompt Injection]], [[mitigations/access-segmentation-and-rbac|Access Segmentation and RBAC]]

---

### Trust Boundary Threat Modeling Workflow

When threat modeling LLM applications, systematically evaluate each trust boundary:

1. **Identify all trust boundaries** in your architecture (model, user interaction, training data, external sources, internal services)

2. **For each boundary, ask:**
   - What data crosses this boundary?
   - What is the trust level on each side?
   - What security controls are in place?
   - What happens if this boundary is compromised?

3. **Map threats to boundaries:**
   - User Interaction → [[techniques/prompt-injection|Prompt injection]], output leakage
   - Training Data → [[techniques/data-poisoning-attacks|Data poisoning]], bias, memorization
   - External Data → Indirect injection, supply chain attacks
   - Internal Services → Unauthorized access, privilege escalation
   - Model → Supply chain compromise, API abuse

4. **Apply defense-in-depth:**
   - Input validation + output filtering
   - Access controls + monitoring
   - Encryption + auditing
   - Rate limiting + anomaly detection

5. **Document and review:**
   - Maintain trust boundary map
   - Update as architecture evolves
   - Regular security assessments

> "Understanding the origins of your training data, the associated trust boundaries, and their respective security implications is crucial for safeguarding your LLM application."
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 24

---

### Key Takeaways: LLM Trust Boundaries

1. **Five Critical Boundaries:** Model, User Interaction, Training Data, External Data, Internal Services—each requires tailored security measures

2. **Bidirectional Risk:** Both inputs (prompt injection) and outputs (data leakage) must be secured

3. **False Security:** Internal services are NOT inherently secure—LLMs can become bypass mechanisms

4. **External Data = High Risk:** Live web access and external APIs are highly vulnerable to indirect injection

5. **Trust Boundary = Security Checkpoint:** Every boundary requires authentication, authorization, validation, monitoring

6. **Defense-in-Depth Required:** No single control is sufficient—layer multiple mitigations at each boundary

> "Securing LLM applications is an endeavor fraught with complexities, intricacies, and challenges that are significantly different from those of traditional web applications."
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 26

**Related Frameworks:**
- [[frameworks/OWASP-top-10-LLM|OWASP Top 10 for LLM Applications]]
- [[frameworks/atlas/tactics/ai-attack-staging|MITRE ATLAS]]
- Zero Trust Architecture (NIST SP 800-207)

**Related Playbooks:**
- [[playbooks/rag-retrieval-assessment|RAG Security Assessment]]
- [[playbooks/llm-application-red-team|LLM Red Teaming]]

---

*Last updated: 2026-02-14 | Sources: Adversarial AI (Sotiropoulos, Packt 2024), The Developer's Playbook for LLM Security (Wilson, O'Reilly 2024)*
