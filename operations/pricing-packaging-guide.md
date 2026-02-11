---
id: pricing-packaging-guide
title: "Pricing & Packaging Guide: AI Threat Exposure Review"
sidebar_label: "Pricing & Packaging"
sidebar_position: 6
description: Internal guide for pricing AI security engagements with complexity bands, add-ons, and qualification criteria.
---

# Pricing & Packaging Guide: AI Threat Exposure Review

> **Internal Document** - This guide defines pricing strategy, complexity bands, and engagement qualification criteria for AI Threat Exposure Review. Pricing values use placeholders and should be updated with actual rates.

---

## 1. Pricing Philosophy

### Why Fixed-Fee Bands

AI security engagements should use **fixed-fee complexity bands** rather than hourly billing or purely custom quotes for the following reasons:

**Predictability for Buyer:**
- Customer knows total cost upfront
- No surprise bills from scope expansion
- Easier budget approval and procurement

**Efficiency for Delivery:**
- Clear timebox prevents scope creep
- Team focuses on high-impact issues within bandwidth
- No incentive to drag out engagement

**Competitive Positioning:**
- Professional service delivery (not staff augmentation)
- Value-based pricing tied to outcomes, not hours
- Easier to compare against alternatives

### Avoiding Scope Creep

**Strict Definition:**
- Use SOW Section 3 (In Scope) and Section 4 (Out of Scope) verbatim
- Reference the 24-issue catalog in Appendix 13 as exhaustive list
- Define trust boundary boundaries clearly (Model, Data/Knowledge, Application/Agent, Deployment/Governance)

**Change Control:**
- Any additional scope requires written change order
- Additional issues beyond the 24 catalog = new engagement
- Training pipeline, infrastructure pentest, source code review = separate SOW

**Timebox Discipline:**
- Engagement ends on defined date regardless of issue count
- If critical findings emerge late, offer retest engagement (separate fee)
- Report includes "Testing Coverage" section noting what was/wasn't tested

### Value Communication

Position pricing based on **risk mitigation value**, not time invested:

- "This engagement costs [£X]. A single data breach from RAG poisoning could cost [50-100X] in regulatory fines, incident response, and reputation damage."
- Compare to cost of hiring full-time AI security specialist ([3-5X annual cost])
- Emphasize deliverables (risk-prioritized roadmap, proof-of-concept exploits, framework mappings) not effort

---

## 2. Complexity Bands (A/B/C)

### Band A: Standard AI Application
**Price:** [£X]
**Duration:** 2 weeks
**Team:** 1 senior consultant

**Typical Client/System Characteristics:**
- Single LLM application with clear boundaries
- RAG system with 1-2 data sources
- 0-3 tools/plugins with limited permissions
- Single-tenant or simple multi-user architecture
- Pre-production or early production (limited users)
- Standard tech stack (OpenAI/Anthropic API, Pinecone/Weaviate, Python/TypeScript)

**What's Tested:**
- Model: Jailbreak, prompt injection, system prompt leakage
- Data/Knowledge: RAG poisoning (if applicable), PII leakage
- Application/Agent: Prompt assembly, output encoding, tool validation (if applicable)
- Deployment/Governance: Access controls, basic monitoring review

**Coverage Depth:**
- 12-15 issues from the 24-issue catalog
- Manual testing with selective automation (garak/PyRIT for model testing)
- 3-5 proof-of-concept exploits demonstrating key findings
- Standard deliverables (executive summary, technical report, remediation roadmap)

**Evidence Expectations:**
- Prompt transcripts for successful exploits
- Screenshots demonstrating impact
- Basic logs (API calls, tool invocations)

**Example Clients:**
- Startup launching first AI chatbot
- Enterprise piloting internal knowledge assistant
- Product team adding AI feature to existing SaaS

---

### Band B: Complex AI System
**Price:** [£Y] (approximately 1.5-2X Band A)
**Duration:** 3 weeks
**Team:** 1-2 senior consultants

**Typical Client/System Characteristics:**
- Multi-model system or agentic workflows
- RAG with 3+ data sources, hybrid search, or custom embeddings
- 4-10 tools with varying privilege levels
- Multi-tenant architecture with isolation requirements
- Production system with active users
- Custom infrastructure (self-hosted models, proprietary orchestration)
- Integration with external APIs or business systems

**What's Tested:**
- All 24 issues from catalog (comprehensive coverage)
- Model: Extended jailbreak campaigns, extraction attempts, privacy attacks
- Data/Knowledge: Cross-tenant RAG isolation, embedding manipulation, retrieval hijacking
- Application/Agent: Tool privilege escalation, agent goal hijacking, multi-step attack chains
- Deployment/Governance: RBAC effectiveness, CI/CD pipeline security, monitoring gaps

**Coverage Depth:**
- Full 24-issue catalog
- Manual + automated testing (garak, PyRIT, custom scripts)
- 7-10 proof-of-concept exploits including multi-step attack chains
- Enhanced deliverables (attack tree diagrams, threat model documentation)

**Evidence Expectations:**
- Multi-turn conversation transcripts
- Tool invocation traces with full argument logs
- Database/vector store query logs
- Detailed reproduction scripts

**Example Clients:**
- SaaS platform with AI agent marketplace
- Enterprise AI application serving multiple business units
- Financial services firm with RAG-powered compliance assistant
- Healthcare provider with multi-tenant patient support chatbot

---

### Band C: Enterprise/High-Risk AI System
**Price:** [£Z] (approximately 2-3X Band A)
**Duration:** 4 weeks
**Team:** 2 senior consultants

**Typical Client/System Characteristics:**
- Mission-critical AI system with high business impact
- Multi-agent orchestration or complex agentic workflows
- RAG with 10+ data sources, federated search, or real-time indexing
- 10+ tools including high-privilege operations (database writes, external APIs, financial transactions)
- Regulated environment (HIPAA, PCI-DSS, SOX, GDPR critical systems)
- Multi-region, multi-tenant, or federated architecture
- Custom ML pipeline, fine-tuned models, or proprietary training data

**What's Tested:**
- All 24 issues + edge cases and attack chain variations
- Model: Advanced privacy attacks, model extraction, adversarial robustness
- Data/Knowledge: Federated RAG security, cross-region data leakage, embedding integrity
- Application/Agent: Complex tool chains, business logic manipulation, supply chain (MCP/plugins)
- Deployment/Governance: Incident response validation (tabletop), compliance framework mapping

**Coverage Depth:**
- Full 24-issue catalog + situational issues
- Comprehensive manual + automated + custom tooling
- 10-15 proof-of-concept exploits including business process attacks
- Premium deliverables (compliance matrix, detection rules, purple team playbook)

**Evidence Expectations:**
- Full attack campaign documentation
- Detection evasion analysis
- Business impact quantification (financial modeling)
- Video demonstrations of critical findings

**Example Clients:**
- Bank deploying AI-powered fraud detection and customer service
- Healthcare system with AI diagnostic assistance
- Government agency with classified data in AI workflows
- Public company facing regulatory scrutiny on AI governance

---

### What Deepens Across Bands

| Aspect | Band A | Band B | Band C |
|--------|--------|--------|--------|
| **Issue Coverage** | 12-15 issues | All 24 issues | All 24 + edge cases |
| **Testing Depth** | Core attacks | Multi-step chains | Advanced campaigns |
| **Evidence Quality** | Transcripts, screenshots | Logs, traces, scripts | Full documentation, video |
| **Automation** | Selective (garak) | Standard (garak, PyRIT) | Custom tooling |
| **Team Size** | 1 consultant | 1-2 consultants | 2 consultants |
| **Duration** | 2 weeks | 3 weeks | 4 weeks |
| **Findings Expected** | 5-10 | 10-15 | 15-25 |

### What Remains Constant

Regardless of complexity band, **all engagements include**:

**Deliverables:**
- Executive Summary Report
- Technical Findings Report
- Framework Compliance Matrix (OWASP/NIST/ATLAS)
- Risk-Prioritized Remediation Roadmap
- Evidence Package
- Final Readout Presentation

**Quality Standards:**
- All findings validated with proof-of-concept
- Severity classification (Critical/High/Medium/Low)
- Remediation guidance with implementation estimates
- Verification criteria for retest

**Process:**
- Kickoff meeting with stakeholders
- Weekly status updates
- Immediate notification of Critical findings
- Draft report review period
- Final presentation to executive and technical stakeholders

---

## 3. Add-Ons Menu

The following services can be added to any complexity band for additional fees:

### Retest Engagement
**Price:** [£R] (approximately 30-40% of original engagement)
**Duration:** 3-5 days
**Description:** Post-remediation validation of Critical and High severity findings. Confirms fixes are effective and no new issues introduced.

**When to Offer:**
- Client has remediated findings and wants validation
- Compliance requirement (SOC 2, ISO 27001 audit)
- Pre-launch validation after fixing pre-deployment findings

---

### Extended Agent Security Deep Dive
**Price:** [£A] (approximately 50% of Band A)
**Duration:** +5 days
**Description:** Focused assessment of agent workflows, tool chains, and multi-agent coordination. Includes goal hijacking, loop manipulation, and complex privilege escalation scenarios.

**When to Offer:**
- Client has 10+ tools or complex agentic workflows
- Agent has high-privilege access (database writes, financial transactions, external APIs)
- Multi-agent coordination with handoffs and delegation

---

### RAG Security Comprehensive Review
**Price:** [£A] (approximately 50% of Band A)
**Duration:** +4 days
**Description:** Deep assessment of RAG architecture including embedding integrity, retrieval manipulation, federated search security, and cross-tenant isolation.

**When to Offer:**
- RAG system with 5+ data sources
- Custom embeddings or hybrid search strategies
- Multi-tenant RAG with strict isolation requirements
- Real-time or streaming RAG indexing

---

### Purple Team Workshop
**Price:** [£W] (approximately 25% of Band A)
**Duration:** +2 days
**Description:** Collaborative workshop with client security team. Live attack demonstrations, detection rule development, SIEM configuration, and incident response playbook creation.

**When to Offer:**
- Client has mature security team wanting hands-on training
- Client wants to build internal AI red team capability
- Finding severity requires immediate detection/response capability

---

### Continuous Monitoring Setup
**Price:** [£M] (approximately 40% of Band A)
**Duration:** +3 days
**Description:** Design and deploy AI-specific monitoring dashboards and alerting rules. Integration with existing SIEM/SOAR. Includes prompt injection detection, tool abuse alerts, and RAG anomaly monitoring.

**When to Offer:**
- Client operating production AI system at scale
- Regulatory requirement for continuous monitoring
- High-risk system (financial, healthcare, government)

---

### Governance & Compliance Deep Dive
**Price:** [£G] (approximately 50% of Band A)
**Duration:** +4 days
**Description:** NIST AI RMF detailed assessment, EU AI Act readiness review, SOC 2 Type II AI control validation, or custom compliance framework mapping.

**When to Offer:**
- Client facing audit or compliance deadline
- Regulated industry (finance, healthcare, government)
- Board-level governance requirement

---

## 4. Discount Rules

### When Discounts Are Allowed

**Volume/Partnership (Up to 15% discount):**
- Multi-engagement commitment (3+ engagements over 12 months)
- Strategic partnership with ongoing retainer
- Multiple business units within same organization

**Non-Profit/Education (Up to 25% discount):**
- Registered 501(c)(3) or equivalent
- Academic institutions for research purposes
- Government agencies with public sector pricing

**Early Adopter (Up to 10% discount):**
- First 10 customers for new engagement type
- Case study and testimonial rights granted
- Public reference customer agreement

### When Discounts Are NOT Allowed

**Never Discount:**
- To win against low-quality competitors (race to bottom)
- In response to "we have another quote for half that" (they're not buying equivalent service)
- For "exposure" or "portfolio building" (you have sample findings and case studies)
- When client lacks budget but has unrealistic scope expectations

**Red Flag Scenarios:**
- Client wants Band C scope at Band A price ("we have a small budget but need everything")
- Procurement process designed to commoditize professional services
- Client expects guarantees ("you'll find all vulnerabilities, right?")

### Discount Approval Authority

- Up to 10%: Individual contributor can approve
- 10-20%: Manager approval required
- 20%+: Executive approval + business justification

---

## 5. Red Flags and "Walk Away" Criteria

### Unrealistic Expectations

**Walk Away If:**
- Client expects certification or guarantee of security ("After this, we'll be 100% secure, right?")
- Client wants legal opinion or compliance certification ("Can you certify us as NIST AI RMF compliant?")
- Client expects unlimited findings ("Keep testing until you can't find anything")
- Timeline is impossible ("We launch in 2 weeks, can you finish before then?")

**Response:**
> "We provide a point-in-time security assessment with proof-of-concept exploits and remediation guidance. We do not certify security, guarantee all vulnerabilities are found, or provide legal/compliance opinions. For those services, consult a compliance auditor or legal counsel."

---

### No Access / Insufficient Access

**Walk Away If:**
- Client won't provide API access or insists on "black box only" (can't test internal trust boundaries)
- Production-only testing with no non-prod environment (too risky)
- Client wants assessment but won't share architecture diagrams or system documentation
- Access requires multi-month procurement/security review (timeline misalignment)

**Response:**
> "Effective AI red teaming requires API access, system documentation, and a non-production environment. Without these, we cannot test trust boundaries meaningfully. Let's revisit when your access model supports security testing."

---

### Procurement Traps

**Walk Away If:**
- RFP requires $XM liability insurance for $X engagement (insurance cost exceeds fee)
- Payment terms are Net-90 or longer with no upfront payment
- Contract includes unlimited liability or indemnification for client's security failures
- Procurement process will take 6+ months (opportunity cost too high)
- "Bake-off" with 5+ vendors doing free proof-of-concept work (spec work disguised as evaluation)

**Response:**
> "Our standard terms include [liability cap, payment terms, scope definition]. We're happy to discuss reasonable adjustments, but cannot accept unlimited liability or extended payment terms. If procurement requirements are fixed, this may not be a good fit."

---

### Misaligned Stakeholders

**Walk Away If:**
- Technical team wants assessment, but executive sponsor thinks "AI security is marketing hype"
- Security team commissioned engagement, but AI team refuses to cooperate or provide access
- Engagement scoped by procurement, but delivery team has no idea it's happening
- No clear decision-maker (5 stakeholders, none with authority to approve findings/remediation)

**Response:**
> "Successful AI security assessments require alignment between security, AI/engineering, and executive stakeholders. Let's schedule a kickoff call with all key stakeholders to ensure everyone understands the engagement purpose and is committed to acting on findings."

---

### Ethical/Legal Concerns

**Walk Away If:**
- Client operates in sanctioned country or prohibited industry
- Client wants assessment of third-party system without authorization (competitor espionage)
- Client expects you to retain or exfiltrate production data for "analysis"
- Client's AI system purpose raises ethical concerns (mass surveillance, deceptive practices, etc.)

**Response:**
> "We only conduct authorized security testing on systems where the client has legal authority to commission such work. Testing third-party systems without authorization is illegal. We cannot proceed with this engagement."

---

## 6. Complexity Band Decision Tree

Use this decision tree during scoping calls to determine appropriate band:

```
START
├─ Is the AI system in production with active users?
│  ├─ No → Likely Band A (unless complex architecture)
│  └─ Yes → Continue
│
├─ How many tools/plugins does the agent have?
│  ├─ 0-3 tools → Likely Band A
│  ├─ 4-10 tools → Likely Band B
│  └─ 10+ tools → Likely Band C
│
├─ Is the system multi-tenant or handles regulated data?
│  ├─ No → Likely Band A or B
│  └─ Yes → Continue
│
├─ Are there multiple LLMs/agents or federated architecture?
│  ├─ No → Likely Band B
│  └─ Yes → Likely Band C
│
├─ Is this a mission-critical system with high business impact?
│  ├─ No → Band B
│  └─ Yes → Band C
```

**Final Check:**
- If 2+ characteristics point to higher band → use higher band
- If client budget is Band A but system is Band C → scope down to most critical trust boundaries or decline
- If uncertain → default to Band B and adjust during kickoff

---

## 7. Quoting and Contracting Process

### Step 1: Discovery Call (30-45 minutes)
- Use scoping intake questionnaire (see separate doc)
- Determine complexity band using decision tree
- Identify applicable add-ons
- Assess qualification (strong fit / weak fit / decline)

### Step 2: Quote Development (1-2 business days)
- Customize proposal/SOW skeleton with client details
- Insert pricing: Base band + add-ons
- Include issue catalog appendix showing coverage
- Attach one-pager and sample finding

### Step 3: Proposal Review Call (30 minutes)
- Walk through SOW Section 6 (Engagement Plan)
- Clarify scope boundaries (In Scope / Out of Scope)
- Set expectations on deliverables and timeline
- Address questions and concerns

### Step 4: Contract Execution
- Client signs SOW Acceptance section
- Invoice 50% upfront payment
- Schedule kickoff meeting (Week 1, Day 1)

### Step 5: Kickoff (1-2 hours)
- Align stakeholders on objectives and timeline
- Confirm technical access and environment details
- Validate architecture documentation
- Set communication cadence (weekly updates, critical finding escalation)

---

## 8. Pricing Review and Adjustment

**Quarterly Review:**
- Analyze win rate by complexity band
- Review actual delivery cost vs. quoted price
- Adjust bands if consistently over/under budget
- Update add-on pricing based on demand

**Annual Review:**
- Benchmark against market rates (competitor research, industry surveys)
- Adjust for inflation and cost of delivery
- Consider new add-ons based on emerging threats or client demand

**Immediate Adjustment Triggers:**
- Win rate drops below 30% (prices too high or positioning unclear)
- Delivery cost consistently exceeds 70% of price (margins too thin)
- New competitors enter market with disruptive pricing

---

## 9. Internal Cost Model (For Reference)

To maintain healthy margins, target the following cost structure:

**Band A (2 weeks):**
- Senior consultant: 10 days @ [internal daily rate]
- Tools/infrastructure: [£T]
- Reporting/admin: 2 days
- **Target margin:** 40-50%

**Band B (3 weeks):**
- Senior consultant(s): 15 days @ [internal daily rate]
- Tools/infrastructure: [£T × 1.5]
- Reporting/admin: 3 days
- **Target margin:** 40-50%

**Band C (4 weeks):**
- Senior consultants (2): 20 days each @ [internal daily rate]
- Tools/infrastructure: [£T × 2]
- Reporting/admin: 4 days
- **Target margin:** 40-50%

**Add-ons:**
- Should maintain 40-50% margin
- Bundle pricing (e.g., Retest + Workshop) can reduce margin to 35% for strategic deals

---

## 10. Summary Pricing Table (Template)

| Offering | Price | Duration | Team | Typical Client |
|----------|-------|----------|------|----------------|
| **Band A: Standard** | [£X] | 2 weeks | 1 senior | Single LLM app, 0-3 tools, pre-prod |
| **Band B: Complex** | [£Y] | 3 weeks | 1-2 senior | Multi-model, 4-10 tools, prod, multi-tenant |
| **Band C: Enterprise** | [£Z] | 4 weeks | 2 senior | Mission-critical, 10+ tools, regulated |
| **Retest** | [£R] | 3-5 days | 1 senior | Post-remediation validation |
| **Extended Agent** | [£A] | +5 days | 1 senior | 10+ tools, complex workflows |
| **RAG Deep Dive** | [£A] | +4 days | 1 senior | 5+ data sources, multi-tenant |
| **Purple Team Workshop** | [£W] | +2 days | 1 senior | Security team training |
| **Continuous Monitoring** | [£M] | +3 days | 1 senior | Production AI at scale |
| **Governance/Compliance** | [£G] | +4 days | 1 senior | Audit/compliance deadline |

---

**Document Version:** 1.0
**Last Updated:** [Date]
**Owner:** [Operations Lead]
