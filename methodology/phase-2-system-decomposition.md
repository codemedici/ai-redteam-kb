---
id: phase-2-system-decomposition
title: "Phase 2: System Decomposition & Architecture Review"
sidebar_position: 3
---

# Phase 2: System Decomposition & Architecture Review

## Purpose

System decomposition provides an in-depth understanding of how the AI model integrates into its application context. By mapping the architecture end-to-end—from data input and preprocessing to model inference to output handling—red teamers identify weak links, implicit trust assumptions, and attack surfaces that might otherwise be missed.

**Key principle:** Thorough decomposition ensures subsequent threat modeling considers all relevant attack surfaces (model, data, integrations, and environment), not just the model itself.

---

## Objectives

- Document the AI system's architecture end-to-end
- Map data flows between components
- Identify assets and trust boundaries
- Catalog defense mechanisms in place
- Spot implicit trust assumptions that could be violated

---

## Key Activities

### 1. Architecture Review

**Purpose:** Create a comprehensive map of how the system works

**Components to document:**

**Model Layer:**
- Model architecture and version (GPT-4, Llama 2, custom fine-tuned model)
- Model parameters (size, context window, temperature settings)
- Inference endpoints or APIs
- Model serving infrastructure (cloud provider, on-prem, hybrid)

**Data Layer:**
- Training data sources and provenance
- Fine-tuning datasets (if applicable)
- RAG knowledge bases (vector databases, document stores)
- Embedding models and vector stores
- Data preprocessing pipelines

**Application Layer:**
- Prompt assembly logic (system prompts, user input handling, context injection)
- Tool integrations (APIs, databases, file systems, external services)
- Agent orchestration (if applicable)
- Output processing and formatting
- User interface or API gateway

**Infrastructure Layer:**
- Authentication and authorization mechanisms
- API gateways and rate limiting
- Monitoring and logging systems
- Secrets management
- Network architecture and segmentation

**Outputs:**
- Architecture diagram showing all components and their relationships
- Component inventory with versions and configurations
- Data flow diagram showing information movement through the system

---

### 2. Data Flow Mapping

**Purpose:** Understand how data moves through the system and where it could be intercepted or manipulated

**Key flows to trace:**

**User Input → Model:**
1. User submits input via UI/API
2. Input validation and sanitization (if any)
3. Prompt assembly (system prompt + user input + context)
4. Submission to model inference endpoint
5. Model processes and generates output
6. Output post-processing
7. Response returned to user

**RAG Retrieval Flow (if applicable):**
1. User query received
2. Query embedding generation
3. Vector similarity search in knowledge base
4. Document retrieval and ranking
5. Context injection into prompt
6. Model inference with retrieved context
7. Response generation

**Agent Tool Execution Flow (if applicable):**
1. User request received
2. Agent planning and tool selection
3. Tool invocation with parameters
4. Tool execution and result capture
5. Result interpretation by agent
6. Next action decision or final response

**Questions to answer:**
- Where does untrusted input enter the system?
- What transformations occur before data reaches the model?
- Where is data stored (temporarily or persistently)?
- What data leaves the system (logs, responses, telemetry)?
- Where could an attacker inject malicious data?

**Outputs:**
- Data flow diagrams with trust boundaries marked
- Input/output points catalog
- Data storage inventory

---

### 3. Asset Identification

**Purpose:** Catalog high-value targets that attackers might seek to compromise or exfiltrate

**Asset categories:**

**Sensitive Data:**
- PII in training data or knowledge bases
- Proprietary business information
- Customer data in conversation history
- API keys or credentials in prompts or logs

**Critical Functionality:**
- Business logic decision points (loan approvals, medical diagnoses, content moderation)
- Privileged tool access (database writes, file system access, external API calls)
- Authentication and authorization checkpoints

**Model Artifacts:**
- Model weights and architecture
- System prompts and prompt templates
- Fine-tuning datasets
- Evaluation datasets

**Infrastructure:**
- API endpoints and gateways
- Database connections
- Secrets management systems
- Monitoring and logging infrastructure

**Outputs:**
- Asset inventory prioritized by business risk
- Asset-to-component mapping
- Data classification (public, internal, confidential, restricted)

---

### 4. Trust Boundary Identification

**Purpose:** Mark boundaries where trust assumptions change and security controls should exist

**Trust boundaries in AI systems:**

**External → Application:**
- User input via UI or API
- Third-party integrations
- Public internet access

**Application → Model:**
- Prompt submission to inference endpoint
- Model API calls

**Application → Data:**
- RAG retrieval queries
- Database reads/writes
- File system access

**Model → Application:**
- Model output returned to application
- Tool execution results

**Application → External:**
- Responses to users
- External API calls by agents
- Logging and telemetry

**For each boundary, document:**
- What security controls exist (authentication, input validation, output sanitization)?
- What trust assumptions are made (e.g., "model output is always safe for rendering")?
- What could go wrong if the boundary is violated?

**Outputs:**
- Trust boundary diagram overlaid on architecture
- Security control inventory per boundary
- Trust assumption documentation

[[trust-boundaries-overview|Learn more about trust boundaries →]]

---

### 5. Defense Mechanism Documentation

**Purpose:** Catalog existing security controls to understand what protections are in place and where gaps exist

**Defense categories:**

**Input Validation:**
- Prompt filtering (blocked keywords, patterns)
- Length limits and rate limiting
- Input sanitization (encoding, escaping)
- Authentication and authorization checks

**Model-Level Defenses:**
- Safety fine-tuning or RLHF
- System prompts with policy instructions
- Temperature and sampling controls
- Output length limits

**Output Validation:**
- Content filtering (toxicity detection, PII redaction)
- Output encoding (HTML escaping, SQL parameterization)
- Response validation against policy

**Infrastructure Defenses:**
- Network segmentation
- Secrets management (vaults, rotation)
- Monitoring and alerting
- Audit logging

**Operational Defenses:**
- Incident response procedures
- Model versioning and rollback
- Access control policies
- Security training for developers

**For each defense, document:**
- What it's intended to prevent
- How it's implemented
- Known limitations or bypass techniques
- Whether it's been tested

**Outputs:**
- Defense mechanism catalog
- Control effectiveness assessment (untested, partially effective, robust)
- Gap analysis (missing controls per trust boundary)

---

### 6. Implicit Trust Assumptions

**Purpose:** Surface hidden assumptions that could be exploited

**Common implicit assumptions in AI systems:**

**"The model's output is always consumed safely"**
- Reality: LLM-generated SQL, code, or HTML can contain injection payloads
- Risk: Output encoding failures, XSS, SQL injection

**"Only authenticated users can call the model API"**
- Reality: API keys might be exposed in client-side code or logs
- Risk: Unauthorized access, abuse, data exfiltration

**"Retrieved documents in RAG are trustworthy"**
- Reality: Knowledge base might contain user-generated content or compromised sources
- Risk: Indirect prompt injection, misinformation

**"The model won't reveal its system prompt"**
- Reality: Prompt extraction attacks can leak instructions, API keys, or policy details
- Risk: Sensitive information disclosure, easier jailbreaking

**"Agent tools are only called with safe parameters"**
- Reality: LLM might generate malicious tool arguments based on user input
- Risk: Argument injection, privilege escalation

**"Training data is clean and unbiased"**
- Reality: Data poisoning or biased sources can corrupt model behavior
- Risk: Backdoors, discriminatory outputs

**Activity:**
- Review architecture with "what could an attacker assume we trust?" lens
- Challenge each assumption with attack scenarios
- Document assumptions for validation during testing

**Outputs:**
- Implicit trust assumptions list
- Risk scenarios per assumption
- Test cases for Phase 4 planning

---

## Outputs

By the end of Phase 2, the following artifacts should be complete:

1. **System Architecture Diagram**
   - All components (model, data, application, infrastructure)
   - Trust boundaries marked
   - Data flows indicated

2. **Asset Inventory**
   - High-value targets prioritized by business risk
   - Asset-to-component mapping
   - Data classification

3. **Defense Mechanism Catalog**
   - Existing security controls per trust boundary
   - Control effectiveness assessment
   - Gap analysis

4. **Trust Assumptions Document**
   - Implicit assumptions identified
   - Risk scenarios per assumption
   - Test cases for validation

5. **Attack Surface Map**
   - Input/output points
   - Trust boundary crossings
   - Potential weak links highlighted

---

## Decomposition Techniques

### Diagramming

**Tools:**
- Architecture diagrams (Lucidchart, draw.io, Mermaid)
- Data flow diagrams (DFD notation)
- Threat modeling tools (Microsoft Threat Modeling Tool, OWASP Threat Dragon)

**Best practices:**
- Use consistent notation (e.g., trust boundaries as dashed lines)
- Label data flows with data types (user input, PII, credentials)
- Annotate components with versions and configurations
- Highlight areas of uncertainty for follow-up

---

### Interviews

**Who to interview:**
- **Architects:** System design decisions, integration patterns
- **Developers:** Implementation details, code-level controls
- **Data scientists:** Model training, fine-tuning, evaluation
- **DevOps/SRE:** Infrastructure, deployment, monitoring
- **Security team:** Existing security controls, known issues

**Questions to ask:**
- How does data flow from user input to model output?
- What security controls are in place at each stage?
- What are the most sensitive components or data?
- Have there been any security incidents or near-misses?
- What keeps you up at night about this system?

---

### Documentation Review

**Documents to request:**
- System architecture diagrams
- API specifications and schemas
- Data flow diagrams
- Security design documents
- Incident postmortems
- Compliance audit reports

**What to look for:**
- Discrepancies between documentation and actual implementation
- Outdated information (e.g., deprecated components still documented)
- Missing security controls in design
- Assumptions that might not hold in practice

---

### Code Review (if access available)

**Focus areas:**
- Prompt assembly logic (how user input is incorporated)
- Input validation and sanitization
- Output encoding and escaping
- Tool invocation and parameter handling
- Secrets management (hardcoded keys, insecure storage)
- Error handling (information leakage in error messages)

**Outputs:**
- Code-level observations for threat modeling
- Potential vulnerabilities for testing
- Defense mechanism implementation details

---

## Common Mistakes to Avoid

### Incomplete Decomposition

**Problem:** Focusing only on the model and ignoring surrounding infrastructure

**Impact:** Missing attack vectors in data pipelines, tool integrations, or deployment infrastructure

**Solution:** Map the entire system end-to-end, including all trust boundaries

---

### Accepting Documentation at Face Value

**Problem:** Assuming architecture diagrams are accurate and up-to-date

**Impact:** Threat model based on incorrect assumptions, missed attack surfaces

**Solution:** Validate documentation through interviews, code review, and testing. Note discrepancies.

---

### Ignoring Defense Mechanisms

**Problem:** Not documenting existing security controls

**Impact:** Duplicating existing defenses in recommendations, missing opportunities to test control effectiveness

**Solution:** Catalog all defenses and assess their robustness. Plan tests to validate effectiveness.

---

### Missing Implicit Assumptions

**Problem:** Not surfacing hidden trust assumptions

**Impact:** Overlooking exploitable weaknesses that seem "obvious" in hindsight

**Solution:** Actively challenge assumptions with "what if an attacker..." scenarios

---

## Timeline

**Typical duration:** 2-3 days

**Activities breakdown:**
- Architecture review and diagramming: 1 day
- Stakeholder interviews: 4-6 hours
- Documentation review: 4-6 hours
- Asset inventory and trust boundary mapping: 4-6 hours
- Defense mechanism cataloging: 4-6 hours
- Synthesis and artifact creation: 4-6 hours

---

## Transition to Phase 3

Once system decomposition is complete, the engagement transitions to [[phase-3-threat-modeling|Phase 3: Threat Modeling]].

**Handoff checklist:**
- [ ] Architecture diagram complete with trust boundaries
- [ ] Asset inventory prioritized
- [ ] Defense mechanism catalog complete
- [ ] Implicit trust assumptions documented
- [ ] Attack surface map created

**Next phase preview:**
Phase 3 will use the architecture and attack surface map to systematically identify threats, enumerate attack vectors, and prioritize scenarios for testing.

---

## Related Documentation

- [[lifecycle-overview|Lifecycle Overview]] - How Phase 2 fits into the full engagement
- [[phase-1-intake-scoping|Phase 1: Intake & Scoping]] - Previous phase
- [[phase-3-threat-modeling|Phase 3: Threat Modeling]] - Next phase
- [[trust-boundaries-overview|Trust Boundaries Overview]] - Understanding the four-boundary model
- [[common-pitfalls|Common Pitfalls]] - Anti-patterns to avoid
