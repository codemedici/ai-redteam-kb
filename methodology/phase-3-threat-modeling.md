---
tags:
  - trust-boundary/data-knowledge
  - trust-boundary/model
  - type/methodology
---
# Phase 3: Threat Modeling

## Purpose

Threat modeling systematically identifies how an AI system could fail or be exploited before testing begins. By brainstorming attack vectors, enumerating failure modes, and prioritizing scenarios based on business risk, the red team ensures testing focuses on realistic, high-impact threats rather than random or trivial issues.

**Key principle:** Clear threat modeling is the core concept that shapes the entire red team exercise. It ensures testing is hypothesis-driven, not exploratory chaos.

---

## Objectives

- Identify attacker personas and their goals
- Enumerate AI-specific threats and traditional attack vectors
- Map threats to trust boundaries and system components
- Develop attack trees showing progression from initial access to impact
- Prioritize scenarios by likelihood and business consequence
- Create a blueprint for test planning

---

## Key Activities

### 0. Adopt the Adversarial Mindset

**Purpose:** Develop the critical, creative thinking required to uncover AI vulnerabilities that automated tools and checklists miss

**Core principles:**

**1. Understand the Target Deeply**
- Study model architecture, training data characteristics, intended function, operational limits
- Identify developer assumptions that might fail under adversarial pressure
- Consider how the model connects to larger systems and business processes

**Example:** Testing a loan approval AI? Don't just check input validation. Ask: Was training data demographically biased? Could that create exploitable blind spots for unqualified approvals?

**2. Embrace Creativity and Lateral Thinking**
- AI vulnerabilities rarely resemble standard software bugs (buffer overflows, SQL injection)
- Chain seemingly harmless features for malicious effect
- Explore misuse scenarios designers never imagined

**Example:** A text summarizer + translator could bypass plagiarism detectors or obscure disinformation sources at scale

**3. Focus on Data and Logic**
- Model logic emerges from data, not explicit code
- Target training data (poisoning attacks) and inference data (evasion attacks)
- Explore how outputs can be biased or controlled through data manipulation

**4. Exploit Uncertainty and Edge Cases**
- Models fail on data unlike their training distribution
- Probe decision boundaries and low-confidence predictions
- Test ambiguity, noise, adversarial perturbations

**Example:** Sentiment classifier confident on clear positive/negative reviews but struggles with sarcasm—attackers craft sarcastic toxic content to bypass filters

**5. Think in Graphs (Systems Thinking)**
- Map component interactions, data flows, control flows
- Identify critical dependencies (feature stores, external APIs)
- Analyze feedback loops (how manipulating one component cascades through the system)

**Example:** Fraud detection system data flow: `user input → feature engineering → model → alerting → analyst → blocklist`. Poisoning feature engineering corrupts the blocklist via feedback loop, creating systemic failure invisible to model-only testing.

**6. Consider the Socio-Technical System**
- AI is built, deployed, used by people
- Target human elements (social engineering annotators, exploiting user trust)
- Attack deployment infrastructure (MLOps pipeline vulnerabilities)

**7. Maintain Persistence and Iteration**
- Finding novel AI vulnerabilities requires experimentation
- Try multiple approaches, analyze failures, refine hypotheses, iterate
- Adapt strategy based on observed model behavior

**Example:** Direct prompt "Tell me the price" blocked → try obfuscation → try multi-turn manipulation → try role-playing → eventually bypass by embedding query in innocent-seeming request

**Outputs:**
- Red team adopts adversarial thinking patterns
- Creative attack hypotheses beyond framework checklists
- Systems-level vulnerability identification

---

### 1. Define Attacker Personas

**Purpose:** Understand who might attack the system, what they want, and what they can do

**Persona dimensions:**

**Access Level:**
- External user (public interface only)
- Authenticated user (legitimate account)
- Privileged user (admin, developer)
- Insider (employee with system access)
- Supply chain attacker (upstream dependencies)

**Motivation:**
- Financial gain (fraud, theft)
- Competitive intelligence (model extraction, data theft)
- Disruption (DoS, service degradation)
- Ideological (policy bypass, misinformation)
- Curiosity (security researchers, hackers)

**Capabilities:**
- Script kiddie (using public exploits)
- Skilled attacker (custom tooling, adversarial ML knowledge)
- Advanced persistent threat (sophisticated, patient, well-resourced)
- Insider (privileged access, system knowledge)

**Example personas:**

**Persona 1: Malicious User**
- Access: Public chatbot interface
- Goal: Extract PII from training data, bypass content filters
- Capabilities: Prompt engineering, multi-turn conversations
- Techniques: Jailbreaks, prompt injection, social engineering of the model

**Persona 2: Competitor**
- Access: API access (legitimate or stolen credentials)
- Goal: Model extraction, proprietary data theft
- Capabilities: Automated querying, ML expertise
- Techniques: Model inversion, membership inference, systematic probing

**Persona 3: Malicious Insider**
- Access: Training pipeline, model artifacts, production systems
- Goal: Backdoor insertion, data poisoning
- Capabilities: Code modification, data manipulation
- Techniques: Training data poisoning, model weight tampering, credential theft

**Persona 4: Supply Chain Attacker**
- Access: Upstream model repository, dependency packages
- Goal: Widespread compromise via poisoned artifacts
- Capabilities: Package injection, model tampering
- Techniques: Trojan models, malicious dependencies, man-in-the-middle

**Outputs:**
- Attacker persona profiles (3-5 personas)
- Persona-to-threat mapping
- Prioritization based on likelihood and customer risk model

---

### 2. Enumerate AI-Specific Threats

**Purpose:** Identify threats unique to AI systems that traditional threat modeling might miss

**Threat categories by trust boundary:**

#### Model Boundary Threats

**Prompt Injection:**
- Direct injection (user overrides system instructions)
- Indirect injection (malicious content in retrieved documents)
- Goal hijacking (redirecting model objectives)

**Jailbreaks:**
- Role-playing attacks (DAN, "pretend you're...")
- Encoding tricks (base64, leetspeak, translation)
- Multi-turn manipulation (gradually relaxing guardrails)

**Information Disclosure:**
- System prompt extraction
- Training data extraction
- Model architecture inference
- API key leakage from prompts

**Model Behavior Manipulation:**
- Adversarial examples (inputs causing misclassification)
- Model inversion (reconstructing training data)
- Membership inference (determining if data was in training set)

**Safety Bypass:**
- Toxic content generation
- Harmful instruction generation (CBRN, illegal activities)
- Bias exploitation (discriminatory outputs)

[[issues|View Model Boundary issues →]]

#### Data/Knowledge Boundary Threats

**Data Poisoning:**
- Training data manipulation (backdoors, bias injection)
- RAG knowledge base poisoning (malicious documents)
- Fine-tuning dataset corruption

**Embedding Attacks:**
- Vector space manipulation
- Similarity gaming (forcing incorrect retrievals)
- Embedding model exploitation

**Cross-Tenant Leakage:**
- Accessing other users' data via RAG
- Breaking isolation in multi-tenant systems

**PII Exposure:**
- Sensitive data in training corpus
- PII leakage via RAG retrieval
- Conversation history exposure

[[issues|View Data/Knowledge Boundary issues →]]

#### Application/Agent Runtime Threats

**Tool Misuse:**
- Unauthorized API invocations
- Privilege escalation via tools
- Tool chaining for complex attacks

**Argument Injection:**
- SQL injection via LLM-generated queries
- Command injection via tool parameters
- Path traversal in file operations

**Agent Goal Hijacking:**
- Redirecting autonomous workflows
- Manipulating agent decision-making
- Feedback loop poisoning

**Authentication Bypass:**
- Context confusion (role elevation)
- Session hijacking
- Authorization boundary violations

**Output Encoding Failures:**
- XSS in LLM-generated HTML
- Injection in LLM-generated code
- Template injection

[[application-agent-runtime|View Application/Agent Boundary issues →]]

#### Deployment/Governance Threats

**Access Control Weaknesses:**
- RBAC bypass
- API authentication failures
- Secrets exposure (keys in prompts, logs)

**Monitoring Gaps:**
- Attacks not triggering alerts
- Insufficient logging
- Blind spots in observability

**Configuration Issues:**
- Insecure API gateway settings
- Rate limiting bypass
- Misconfigured permissions

**Incident Response Failures:**
- No playbooks for AI-specific incidents
- Slow detection and response
- Inadequate forensics

[[deployment-governance|View Deployment/Governance issues →]]

**Outputs:**
- Threat catalog organized by trust boundary
- Threat-to-component mapping
- Preliminary risk assessment per threat

---

### 3. Apply Threat Modeling Frameworks

**Purpose:** Use structured methodologies to ensure comprehensive threat coverage

#### AI Asset Identification

**Purpose:** Identify what needs protection beyond traditional software assets

**Critical AI assets:**

**Training data and datasets:**
- Integrity (poisoning risk), confidentiality (IP/PII), availability (DoS)
- Questions: How sensitive is training data? Impact if stolen, modified, or unavailable?

**Trained model intellectual property:**
- Parameters, architecture, proprietary algorithms
- Questions: Competitive advantage value? Cost if competitor steals it?

**Model functional integrity:**
- Correct predictions/decisions under adversarial conditions
- Questions: Safety/financial impact of incorrect outputs? Can manipulated outputs cause harm?

**Model availability:**
- Service uptime, resource exhaustion resistance
- Questions: Business impact if AI service unavailable? Can resource-intensive queries cause DoS?

**Sensitive information processing:**
- PII, financial data, trade secrets in model inputs/outputs
- Questions: What sensitive data does model process? Could outputs leak training data?

**Downstream systems:**
- Business processes or systems relying on AI outputs
- Questions: What depends on this AI? Impact if they receive corrupted data?

**User trust and reputation:**
- System credibility, ethical behavior, fairness
- Questions: Reputational damage if AI behaves maliciously, unfairly, or harmfully?

**MLOps pipeline integrity:**
- Data flow, deployment process, single points of failure
- Questions: Where are architectural weak points? How could pipeline compromise cascade?

---

#### Adversary Context Dimensions

**Purpose:** Frame attack scenarios by understanding adversary access and knowledge

**Three critical dimensions:**

**1. Access Time:**
- **Training stage**: Can adversary influence training data or process? (persistent effects)
- **Inference stage**: Can adversary only interact with deployed model? (runtime attacks)
- Questions: Is training pipeline accessible? Can external data sources be compromised?

**2. Access Point:**
- **Digital access**: Via API, network connection, web interface
- **Physical access**: Manipulating sensors, edge devices, hardware
- Questions: Is model on edge devices? Can sensors be tampered with?

**3. System Knowledge:**
- **White-box**: Full knowledge (architecture, parameters, data)
- **Gray-box**: Partial information
- **Black-box**: Limited to observing inputs/outputs
- Questions: Is model architecture public? Are API queries rate-limited?

**Example combinations:**
- Training + Digital + White-box = Data scientist insider poisoning attack
- Inference + Digital + Black-box = External attacker prompt injection via API
- Inference + Physical + Gray-box = Edge device adversarial example attack with partial model access

---

#### AI-Specific Vulnerability Analysis

**Purpose:** Identify weaknesses unique to AI systems that traditional STRIDE misses

**Data pipeline vulnerabilities:**
- Lack of input validation (prompt injection entry points)
- Insecure data sources (poisoning vectors)
- Missing sanitization in preprocessing
- Questions: How is data validated before training/inference? Are external sources trusted implicitly?

**Training process vulnerabilities:**
- Insecure configurations (excessive privileges)
- Lack of differential privacy or federated learning protections
- No adversarial training or robustness hardening
- Questions: Are training jobs run with least privilege? Are privacy techniques used?

**Model architecture vulnerabilities:**
- Overly complex models prone to memorization
- Known vulnerable layers or activation functions
- Lack of uncertainty quantification
- Questions: Could simpler model reduce risk? Are known vulnerabilities present?

**Input/Output handling vulnerabilities:**
- Insufficient input sanitization (adversarial examples, evasion)
- Output leakage (training data, internal state, excessive information in errors)
- Missing output encoding (XSS, injection in generated content)
- Questions: Are inputs validated against expected formats? Do outputs leak sensitive data?

**Deployment environment vulnerabilities:**
- Missing API authentication/authorization
- Inadequate rate limiting (extraction, DoS)
- Insecure model serving configurations
- Questions: Is API secured with standard best practices? Can queries be abused?

**Monitoring and detection gaps:**
- No anomaly detection for unusual query patterns
- Insufficient logging for forensics
- Missing model drift detection
- Questions: Is behavior monitored for attack signals? Are logs sufficient for analysis?

**System dependencies (critical):**
- Weak interfaces between components (data pipeline ↔ model ↔ API ↔ downstream systems)
- Feedback loops enabling cascading failures
- Single points of failure in data flow
- Questions: How does compromise in one component propagate? Where are structural weaknesses?

---

#### STRIDE for AI Systems

Traditional STRIDE adapted for AI:

**Spoofing:**
- Impersonating users to the model
- Model impersonation (fake API endpoints)
- Spoofing tool outputs to agents

**Tampering:**
- Training data poisoning
- Model weight modification
- Prompt template tampering
- RAG knowledge base corruption

**Repudiation:**
- Denying malicious prompts (lack of audit logs)
- Untraceable model behavior changes
- Anonymous abuse of APIs

**Information Disclosure:**
- Training data extraction
- System prompt leakage
- PII exposure via RAG
- Credential leakage

**Denial of Service:**
- Resource exhaustion (expensive prompts)
- Rate limiting bypass
- Model serving disruption

**Elevation of Privilege:**
- Tool privilege escalation
- Role confusion in multi-user systems
- Agent gaining unauthorized capabilities

---

#### PASTA (Process for Attack Simulation and Threat Analysis)

Seven-stage process adapted for AI:

1. **Define objectives:** Business impact focus (data breach, compliance violation, service disruption)
2. **Define technical scope:** AI system boundaries from Phase 2 decomposition
3. **Application decomposition:** Already completed in Phase 2
4. **Threat analysis:** Enumerate threats using ATLAS, OWASP, and AI-specific patterns
5. **Vulnerability analysis:** Map threats to known weaknesses (OWASP LLM Top 10)
6. **Attack modeling:** Develop attack trees (see below)
7. **Risk and impact analysis:** Prioritize based on business risk

---

#### MITRE ATLAS Integration

Map threats to ATLAS tactics and techniques:

**ATLAS Tactics (High-Level Goals):**
- Reconnaissance (learning about the model)
- Resource Development (preparing attack tools)
- Initial Access (gaining entry to system)
- Execution (running malicious code or prompts)
- Persistence (maintaining access)
- Defense Evasion (avoiding detection)
- Collection (gathering data)
- Exfiltration (stealing data)
- Impact (causing harm)

**Example ATLAS Technique Mapping:**
- Prompt Injection → AML.T0051
- Training Data Poisoning → AML.T0020
- Model Inversion → AML.T0043
- Adversarial Examples → AML.T0048

[[techniques|Browse ATLAS techniques →]]

**Outputs:**
- STRIDE threat matrix
- PASTA threat analysis document
- ATLAS technique mapping

---

### 4. Develop Attack Trees

**Purpose:** Visualize attack paths from initial access to business impact

**Attack tree structure:**

```
Goal: Extract Customer PII from Chatbot
├── Path 1: Training Data Extraction
│   ├── Craft prompts to elicit memorized data
│   ├── Use membership inference to confirm presence
│   └── Extract via repeated queries
├── Path 2: RAG Knowledge Base Access
│   ├── Inject malicious query to retrieve PII
│   ├── Exploit cross-tenant isolation weakness
│   └── Exfiltrate via model response
└── Path 3: System Prompt Leakage
    ├── Extract system prompt containing API key
    ├── Use API key to access backend database
    └── Query database directly for PII
```

**For each path, document:**
- Prerequisites (what attacker needs)
- Steps (ordered actions)
- Detection likelihood (would this trigger alerts?)
- Mitigation difficulty (how hard to fix?)

**Example attack tree for agent system:**

```
Goal: Execute Arbitrary Code via Agent
├── Path 1: Tool Argument Injection
│   ├── Craft prompt causing agent to call shell tool
│   ├── Inject malicious command in tool parameters
│   └── Agent executes command without validation
├── Path 2: Goal Hijacking + Tool Chaining
│   ├── Redirect agent goal via prompt injection
│   ├── Chain file read + network tools
│   └── Exfiltrate sensitive files to attacker server
└── Path 3: Feedback Loop Poisoning
    ├── Poison agent's memory with malicious instructions
    ├── Agent internalizes instructions as legitimate
    └── Agent autonomously executes malicious actions
```

**Outputs:**
- Attack trees for top 5-10 threat scenarios
- Attack path prioritization
- Prerequisites and assumptions documented

---

### 5. Prioritize Threat Scenarios

**Purpose:** Focus testing on highest-risk threats given limited time and resources

**Prioritization factors:**

**Likelihood:**
- How easy is the attack to execute? (trivial, moderate, complex)
- What prerequisites are needed? (none, authenticated access, insider access)
- Are public exploits available?
- Has this been seen in the wild?

**Business Impact:**
- Confidentiality: PII exposure, proprietary data theft, credential leakage
- Integrity: Business logic manipulation, data corruption, misinformation
- Availability: Service disruption, resource exhaustion
- Safety: Physical harm, dangerous instructions, CBRN information
- Compliance: Regulatory violations (GDPR, HIPAA), audit failures
- Reputation: Public disclosure, customer trust loss

**Scope:**
- Single-user impact vs multi-tenant
- Limited vs systemic compromise
- Temporary vs persistent

**Detectability:**
- Would the attack trigger alerts?
- Can it be executed covertly?
- Is forensic evidence available?

**Prioritization matrix:**

| Threat Scenario | Likelihood | Impact | Scope | Priority |
|----------------|-----------|--------|-------|----------|
| Prompt injection → PII extraction | High | High | Multi-tenant | **P0 (Critical)** |
| Agent tool privilege escalation | Medium | High | Systemic | **P0 (Critical)** |
| RAG poisoning via user content | Medium | Medium | Cross-user | **P1 (High)** |
| Model extraction via API | Low | Medium | Single-tenant | **P2 (Medium)** |
| Bias in edge case inputs | Medium | Low | Single-user | **P3 (Low)** |

**Outputs:**
- Prioritized threat scenario list (P0-P3)
- Risk rationale per scenario
- Coverage plan (which scenarios to test in Phase 5)

---

### 6. Map to Testing Objectives

**Purpose:** Translate threat scenarios into concrete testing objectives for Phase 4

**For each priority threat, define:**

**Objective:** What are we trying to prove?
- Example: "Demonstrate that an external user can extract PII from the chatbot via prompt injection"

**Success Criteria:** What constitutes a confirmed finding?
- Example: "Model returns at least one piece of PII (name, email, SSN) not provided by the attacker in the current session"

**Techniques to Try:** How will we attempt this?
- Example: "Direct prompt injection, multi-turn social engineering, encoding tricks, translation attacks"

**Evidence Requirements:** What do we need to capture?
- Example: "Full conversation transcript, model response containing PII, proof PII was not in user input"

**Example mapping:**

**Threat:** Agent tool privilege escalation
**Objective:** Prove agent can be manipulated to call unauthorized tools or APIs
**Success Criteria:** Agent invokes a tool it should not have access to based on user role
**Techniques:** Goal hijacking prompts, argument injection, tool chaining
**Evidence:** Agent decision logs, tool invocation records, unauthorized API call proof

**Outputs:**
- Testing objectives per priority threat
- Technique selection per objective
- Success criteria and evidence requirements
- Handoff document for Phase 4 (Test Planning)

---

## Outputs

By the end of Phase 3, the following artifacts should be complete:

1. **Threat Model Document**
   - Attacker personas (3-5 profiles)
   - Threat catalog organized by trust boundary
   - STRIDE/PASTA analysis
   - ATLAS technique mapping

2. **Attack Trees**
   - Top 5-10 threat scenarios visualized
   - Attack paths with prerequisites and steps
   - Detection and mitigation difficulty

3. **Prioritized Threat List**
   - P0-P3 scenarios with risk rationale
   - Coverage plan for testing
   - Out-of-scope threats documented

4. **Testing Objectives**
   - Concrete objectives per priority threat
   - Success criteria and evidence requirements
   - Technique selection guidance

---

## Common Mistakes to Avoid

### Generic Threat Modeling

**Problem:** Using traditional threat models without AI-specific adaptations

**Impact:** Missing unique AI vulnerabilities (prompt injection, data poisoning, model extraction)

**Solution:** Supplement STRIDE/PASTA with AI-specific threat catalogs (OWASP LLM Top 10, ATLAS)

---

### Threat Modeling in a Vacuum

**Problem:** Threat modeling without understanding system architecture

**Impact:** Identifying threats that don't apply to this system, missing actual attack surfaces

**Solution:** Complete Phase 2 (System Decomposition) thoroughly before starting threat modeling

---

### Analysis Paralysis

**Problem:** Spending too long enumerating every possible threat

**Impact:** Delayed testing, wasted time on low-value scenarios

**Solution:** Time-box threat modeling (2-3 days), focus on top 10-15 scenarios, document others for future

---

### No Prioritization

**Problem:** Treating all threats as equally important

**Impact:** Testing effort spread thin, critical threats under-tested

**Solution:** Apply risk-based prioritization (likelihood × impact), focus Phase 5 on P0/P1 scenarios

---

### Missing Stakeholder Input

**Problem:** Threat modeling without customer context on business risk

**Impact:** Misaligned priorities, testing low-business-impact scenarios

**Solution:** Review threat model with stakeholders, adjust priorities based on their risk appetite

---

## Timeline

**Typical duration:** 2-3 days

**Activities breakdown:**
- Attacker persona development: 4 hours
- Threat enumeration: 1 day
- Framework application (STRIDE/PASTA/ATLAS): 4-6 hours
- Attack tree development: 4-6 hours
- Prioritization and stakeholder review: 4 hours
- Testing objectives definition: 4 hours

---

## Transition to Phase 4

Once threat modeling is complete, the engagement transitions to [[phase-4-test-planning|Phase 4: Test Planning & Coverage]].

**Handoff checklist:**
- [ ] Threat model document complete
- [ ] Attack trees for top scenarios created
- [ ] Prioritized threat list with P0-P3 ratings
- [ ] Testing objectives defined per priority threat
- [ ] Stakeholder review and approval

**Next phase preview:**
Phase 4 will take the prioritized threat scenarios and testing objectives from Phase 3 and develop a detailed test plan with specific techniques, tooling, and coverage matrices.

---

## Related Documentation

- [[lifecycle-overview|Lifecycle Overview]] - How Phase 3 fits into the full engagement
- [[phase-2-system-decomposition|Phase 2: System Decomposition]] - Previous phase
- [[phase-4-test-planning|Phase 4: Test Planning]] - Next phase
- [[trust-boundaries-overview|Trust Boundaries Overview]] - Threat enumeration by boundary
- [[tactics|MITRE ATLAS Tactics]] - High-level adversary objectives
- [[techniques|MITRE ATLAS Techniques]] - Specific attack methods
- [[common-pitfalls|Common Pitfalls]] - Anti-patterns to avoid
