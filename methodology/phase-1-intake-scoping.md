---
tags:
  - trust-boundary/general
  - type/methodology
---
# Phase 1: Intake & Scoping

## Purpose

Every AI red team engagement begins with clear intake and scoping. This phase defines objectives, boundaries, and constraints to ensure testing focuses on high-value targets without wasting effort on out-of-scope areas or producing diluted results.

**Key principle:** Vague or overly broad scope is a common pitfall that dilutes red team effectiveness. Clarity here is essential.

---

## Objectives

- Define assessment boundaries (what AI system or feature is being tested)
- Identify high-value assets and critical functionality
- Establish success criteria (what "winning" looks like for the red team)
- Align stakeholder expectations on deliverables and timelines
- Document constraints and rules of engagement

---

## Key Activities

### 1. Discovery Call

**Purpose:** Understand the system, business context, and stakeholder concerns

**Topics to cover:**
- **System overview:** What AI system is being tested (LLM chatbot, RAG application, agentic workflow, model pipeline)?
- **Business context:** What is the system's purpose? Who are the users? What are the critical use cases?
- **Security concerns:** What keeps stakeholders up at night? Known vulnerabilities? Previous incidents?
- **Regulatory requirements:** GDPR, HIPAA, industry-specific compliance needs?
- **Deployment status:** Production, staging, pre-deployment?

**Outputs:**
- System description document
- Stakeholder contact list with roles
- Initial risk areas for deeper investigation

---

### 2. Threat Actor Profiling

**Purpose:** Define attacker capabilities, goals, and access assumptions relevant to the customer's risk model

**Attacker personas to consider:**

**External Adversary (Low Privilege):**
- Access: Public-facing interface only (e.g., chatbot user)
- Goals: Data exfiltration, service disruption, policy bypass
- Capabilities: Prompt crafting, multi-turn conversations, no system access

**External Adversary (Sophisticated):**
- Access: API access, may have reverse-engineered client
- Goals: Model extraction, systematic abuse, competitive intelligence
- Capabilities: Automated fuzzing, adversarial ML techniques, tooling

**Insider Threat:**
- Access: Internal systems, training data, model artifacts
- Goals: Data poisoning, backdoor insertion, sabotage
- Capabilities: Code modification, data manipulation, credential access

**Supply Chain Attacker:**
- Access: Upstream dependencies, model repositories, third-party integrations
- Goals: Widespread compromise via poisoned models or libraries
- Capabilities: Package injection, model tampering, dependency confusion

**Outputs:**
- Threat actor profile document
- Access assumptions per persona
- Prioritized attacker scenarios aligned with business risk

---

### 3. Trust Boundary Mapping

**Purpose:** Classify system components into the four trust boundaries to structure threat modeling

**The four trust boundaries:**

1. **Model Boundary**
   - The AI model itself (weights, architecture, inference behavior)
   - Threats: Jailbreaks, prompt injection, training data extraction, model inversion

2. **Data/Knowledge Boundary**
   - Training data, RAG knowledge bases, embeddings, fine-tuning datasets
   - Threats: Data poisoning, embedding manipulation, cross-tenant leakage

3. **Application/Agent Runtime Boundary**
   - Application code, tool integrations, agent orchestration, prompt assembly
   - Threats: Tool privilege escalation, argument injection, goal hijacking, output encoding failures

4. **Deployment/Governance Boundary**
   - Infrastructure, access controls, monitoring, API gateways, incident response
   - Threats: Secrets exposure, access control bypass, monitoring gaps, configuration weaknesses

**Activity:**
- Map each system component to one or more trust boundaries
- Identify which boundaries are in scope for this engagement
- Note any cross-boundary interactions (e.g., RAG retrieval feeding model prompts)

**Outputs:**
- Trust boundary classification diagram
- In-scope vs out-of-scope boundary determination

[[trust-boundaries-overview|Learn more about trust boundaries →]]

---

### 4. Scope Negotiation

**Purpose:** Define in-scope systems, exclusions, and testing constraints

**What to define:**

**In-Scope Systems:**
- Specific model versions or endpoints
- Features or workflows to test
- User roles or access levels to simulate
- Data sources or knowledge bases

**Out-of-Scope:**
- Unrelated systems or services
- Deprecated features
- Third-party managed services (unless explicitly included)

**Testing Constraints:**
- **Rate limits:** API call quotas, throttling thresholds
- **Data sensitivity:** PII handling, data residency requirements
- **Environment:** Production vs staging (prefer non-production for destructive tests)
- **Timing:** Maintenance windows, blackout periods
- **Unacceptable methods:** Techniques explicitly forbidden (e.g., social engineering of staff, physical access)

**Success Criteria:**
- What constitutes a confirmed finding?
- What severity levels require immediate notification?
- What evidence format is expected?

**Outputs:**
- Scope document with in-scope/out-of-scope lists
- Testing constraints checklist
- Success criteria definitions
- Rules of engagement

---

### 5. Access Provisioning

**Purpose:** Coordinate technical access needed for testing

**Access requirements:**
- **API credentials:** Keys, tokens, authentication mechanisms
- **Test accounts:** User accounts with various privilege levels
- **Architecture documentation:** System diagrams, data flow maps, API specs
- **Model information:** Version, parameters, training data descriptions (if available)
- **Monitoring access:** Logs, alerts, dashboards (for observing test impact)

**Coordination:**
- Identify access owners and approval workflows
- Define access provisioning timeline
- Document access handoff procedures
- Plan access revocation post-engagement

**Outputs:**
- Access checklist with status tracking
- Credential handoff documentation
- Technical contact list

---

## Common Scoping Mistakes to Avoid

### Vague Scope

**Problem:** "Test our AI chatbot" without specifying which features, user roles, or threat scenarios

**Impact:** Red team wastes time on low-value areas, misses critical attack surfaces, produces scattered findings

**Solution:** Define specific features, user journeys, and threat actors. Example: "Test the customer support chatbot's handling of PII in multi-turn conversations, focusing on prompt injection and data exfiltration by external users."

---

### Overly Broad Scope

**Problem:** "Test everything" across multiple models, applications, and environments

**Impact:** Shallow coverage, no depth on critical issues, exhausted budget before meaningful findings

**Solution:** Prioritize based on business risk. Start with highest-risk components (e.g., production customer-facing chatbot) before expanding to lower-risk areas (e.g., internal experimental models).

---

### Unclear Success Criteria

**Problem:** No definition of what constitutes a "finding" or how severity is determined

**Impact:** Disagreements during reporting, findings dismissed as "not real vulnerabilities," wasted effort

**Solution:** Agree upfront on evidence standards and severity definitions. Reference [[evidence-reproducibility|evidence standards]] and [[phase-6-triage-severity|severity model]].

---

### Missing Constraints

**Problem:** No documentation of rate limits, data sensitivity, or forbidden techniques

**Impact:** Tests trigger production alerts, violate compliance requirements, or cause service disruption

**Solution:** Explicitly document all constraints in scope document. Get stakeholder sign-off before testing begins.

---

### No Stakeholder Alignment

**Problem:** Red team and stakeholders have different expectations about deliverables, timelines, or focus areas

**Impact:** Misaligned effort, dissatisfaction with results, scope creep

**Solution:** Review scope document with all stakeholders. Confirm agreement on objectives, deliverables, and timeline before proceeding.

---

## Outputs

By the end of Phase 1, the following artifacts should be complete:

1. **Signed Scope Document**
   - System description
   - In-scope and out-of-scope components
   - Testing constraints and rules of engagement
   - Success criteria

2. **Threat Model Assumptions**
   - Attacker personas and capabilities
   - Access assumptions per persona
   - Prioritized threat scenarios

3. **Trust Boundary Map**
   - System components classified by boundary
   - In-scope boundaries identified

4. **Access Checklist**
   - Required credentials and accounts
   - Access provisioning status
   - Technical contacts

5. **Timeline and Communication Plan**
   - Phase schedule with milestones
   - Communication cadence (daily standups, weekly reports)
   - Escalation procedures for critical findings

---

## SDLC Integration Strategy

**Purpose:** Position AI red teaming as continuous process integrated throughout development lifecycle, not isolated final checkpoint

**Core principle:** "Shift left"—move security testing earlier in development timeline to reduce cost, risk, and disruption

### Why Shift Left Matters for AI

**Cost-effectiveness:**
- Fixing design flaws (e.g., data poisoning vulnerability in pipeline) vastly cheaper pre-deployment than post-launch
- Late discovery may require model retraining, architecture changes, pipeline rebuilds
- Studies show bugs caught in design phase cost 10-30x less than post-deployment fixes
- AI context amplifies costs: cloud compute for retraining, project delays

**Reduced risk exposure:**
- Early identification prevents vulnerabilities from reaching production
- Stops issues from propagating through system components
- Avoids exposing users and infrastructure to known weaknesses

**Improved design:**
- Encourages threat modeling and secure design from start
- Data sanitization, model robustness, secure APIs become foundational requirements
- Adversarial perspectives in design reviews preempt vulnerability classes

**Faster feedback loops:**
- Developers get security feedback immediately
- Quick iteration and learning
- Fosters security-aware culture
- Prevents "security as last-minute gate" mentality

**AI-specific risk mitigation:**
- Data poisoning, model evasion link to training process and architecture
- Adversarial training only possible during model building
- Poisoning mitigation requires securing data pipeline from start
- Inference-time fixes insufficient for training-phase vulnerabilities

### Secure AI Development Lifecycle (SAIDL) Integration

**1. Requirements & Design Phase**

**Threat modeling (early):**
- Identify AI-specific attack vectors (prompt injection, poisoning, evasion, inversion)
- Base on use case, data sources, model type, deployment environment
- Use MITRE ATLAS framework for known attack patterns
- Document assumptions and trust boundaries

**Security requirements:**
- Define explicit robustness requirements (e.g., model must resist N adversarial examples without significant degradation)
- Privacy requirements (limits on memorizing PII, differential privacy thresholds)
- Prompt injection resistance criteria
- API security baselines

**Red team consultation:**
- Involve adversarial thinkers in design reviews
- Challenge assumptions ("What prevents prompt injection if user input forms prompts?")
- Identify abuse cases missed by designers
- Suggest controls or alternate designs

**Example early prevention:** Cloud provider red team involvement in design prevented 2 high-severity vulnerabilities before coding started

---

**2. Data Acquisition & Preparation Phase**

**Data provenance & integrity:**
- Tamper-evident logging of data collection
- Automated anomaly scans
- Simulate poisoning in staging to validate detection

**Privacy controls:**
- Data minimization and anonymization
- Remove/tokenize personal identifiers
- Synthetic data augmentation
- Privacy risk assessments (PII leak checks)

**Red team scenario testing:**
- Test validation/sanitization against adversarial manipulation
- Inject toxic content into datasets to verify detection
- Alter metadata to confuse processing
- Evaluate pipeline gap discovery and feedback

---

**3. Model Development & Training Phase**

**Secure configuration:**
- Hardened training environment (access controls, isolated compute)
- Authorized-only access to model weights
- Version-controlled training code and hyperparameters
- Auditable configurations

**Robustness techniques:**
- Adversarial training if evasion attacks threaten
- K-fold cross-checks for poisoning detection
- Robust training objectives discounting aberrant data
- Threat-model-driven technique selection

**Framework/library security:**
- Vetted, up-to-date ML frameworks (TensorFlow, PyTorch)
- Prompt security patch application
- Locked dependency versions with integrity verification
- Supply chain attack awareness

**Red team testing:**
- Assess training process security (disruption, script modification, data order manipulation)
- Simulate insider threats and advanced attackers
- Validate pipeline resilience

---

**4. Model Testing & Validation Phase**

**Targeted red teaming:**
- Test specific threats from threat modeling
- Prompt injection if top risk
- Adversarial examples for evasion concerns
- Membership inference for privacy risks
- Trace attacks to threat model entries

**Security metrics:**
- Accuracy drop under adversarial attacks (FGSM, PGD success rate)
- Jailbreak success rate for generative models
- Establish thresholds (e.g., maintain >X% accuracy under Y attack)
- Failed thresholds trigger remediation before deployment

**Safety & alignment testing (generative AI):**
- Test safety filters against adversarial inputs
- Harmful content generation testing
- Bias and misinformation probing
- Known malicious prompts + novel red team prompts
- Obfuscation testing (foreign languages, metaphors)
- Pre-release jailbreak analysis and filter improvement

---

**5. Pre-Deployment Phase**

**Full-stack red teaming:**
- Comprehensive assessment in staging (production-identical)
- Test model + surrounding app + APIs + infrastructure
- Chain exploit scenarios
- End-to-end vulnerability validation

**Configuration hardening:**
- Disable debug endpoints
- Remove default credentials and keys
- Strong authentication for internal dashboards
- Runtime resource limits (prevent DoS)

---

### Shift-Left Implementation Guidance

**For organizations starting shift-left:**
1. Begin with design-phase threat modeling (low cost, high impact)
2. Add data validation testing in preparation phase
3. Introduce targeted red teaming in model validation
4. Build to full SAIDL integration over time

**For mature organizations:**
- Embed red team representatives in sprint planning
- Automate security checks in CI/CD pipeline
- Continuous threat intelligence integration
- Regular SAIDL process retrospectives

**Success metrics:**
- Percentage of vulnerabilities found pre-deployment vs post
- Average cost per vulnerability fix (trending downward)
- Time from discovery to remediation
- Security requirement coverage in design docs

---

## Engagement Logistics

### Timeline

**Typical duration:** 1-2 days

**Activities breakdown:**
- Discovery call: 2-3 hours
- Scope document drafting: 4-6 hours
- Stakeholder review and approval: 1-2 days (may overlap with next phase)
- Access provisioning: Parallel to scope approval

### Stakeholder Roles

**Customer side:**
- **Executive sponsor:** Approves scope and budget
- **Technical lead:** Provides architecture details and access
- **Security team:** Defines constraints and success criteria
- **Compliance/legal:** Reviews data handling and regulatory requirements

**Red team side:**
- **Engagement lead:** Drives scoping discussions and document creation
- **Technical lead:** Assesses technical feasibility and access requirements

---

## Transition to Phase 2

Once scope is approved and access is provisioned, the engagement transitions to [[phase-2-system-decomposition|Phase 2: System Decomposition & Architecture Review]].

**Handoff checklist:**
- [ ] Scope document signed by stakeholders
- [ ] Access credentials received and validated
- [ ] Architecture documentation reviewed
- [ ] Trust boundary map drafted
- [ ] Threat actor profiles documented

**Next phase preview:**
Phase 2 will dive deep into the system architecture, creating detailed diagrams of data flows, trust boundaries, and defense mechanisms. The trust boundary map from Phase 1 will be refined into a comprehensive attack surface map.

---

## Related Documentation

- [[lifecycle-overview|Lifecycle Overview]] - How Phase 1 fits into the full engagement
- [[phase-2-system-decomposition|Phase 2: System Decomposition]] - Next phase in the lifecycle
- [[trust-boundaries-overview|Trust Boundaries Overview]] - Understanding the four-boundary model
- [[common-pitfalls|Common Pitfalls]] - More anti-patterns to avoid
- [[methodology-variations|Methodology Variations]] - How scoping differs by engagement type
