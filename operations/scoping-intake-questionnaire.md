---
id: scoping-intake-questionnaire
title: "Scoping Intake Questionnaire"
sidebar_label: "Scoping Intake"
sidebar_position: 2
description: Pre-call form and discovery call script for qualifying and scoping AI security engagements.
---

# Scoping Intake Questionnaire

## Purpose

This document provides a two-phase qualification and scoping workflow:

1. **Pre-Call Form**: Short questionnaire (10-15 questions) sent before initial discussion to gather baseline information
2. **Discovery Call Script**: Structured 30-45 minute call to determine engagement fit, complexity band, and recommended next steps

Use this to avoid misaligned conversations, qualify strong-fit clients, and accurately scope engagements before proposal stage.

---

## Pre-Call Form (Send Before Discovery Call)

**Instructions for Prospect**: "Please complete this short form (5-10 minutes) before our discovery call. Your answers help us prepare a focused discussion and ensure we're the right fit for your needs."

### System Overview

**1. What AI capability are you building or deploying?**
- [ ] LLM-powered application (chatbot, assistant, copilot)
- [ ] RAG system (retrieval-augmented generation)
- [ ] Agentic system (autonomous task execution with tools)
- [ ] Fine-tuned or custom-trained model
- [ ] Integration of third-party AI service (OpenAI, Anthropic, etc.)
- [ ] Other: _______________________

**2. What is the current deployment status?**
- [ ] Pre-production (design/prototype)
- [ ] Development/QA environment
- [ ] Limited production (beta, internal users)
- [ ] Full production (external users, revenue-generating)
- [ ] Post-incident (security event occurred)

**3. What is the primary business function of the AI system?**
- [ ] Customer support / service
- [ ] Internal productivity (coding assistant, docs, search)
- [ ] Content generation (marketing, creative)
- [ ] Decision automation (risk assessment, approvals)
- [ ] Data analysis / insights
- [ ] Other: _______________________

### Scope Indicators

**4. Does the system retrieve information from external data sources (RAG)?**
- [ ] Yes - vector database, embeddings, knowledge base
- [ ] Yes - live API/database queries
- [ ] No - model only uses trained knowledge
- [ ] Unsure

**5. Does the system invoke tools, APIs, or external actions?**
- [ ] Yes - database queries (read-only)
- [ ] Yes - database queries (write/update/delete)
- [ ] Yes - external API calls (email, Slack, CRM, etc.)
- [ ] Yes - code execution or system commands
- [ ] No - text generation only
- [ ] Unsure

If yes: **Approximately how many distinct tools or functions?**
- [ ] 1-3 tools
- [ ] 4-10 tools
- [ ] 10+ tools

**6. Does the system handle sensitive data or operate in a regulated context?**
- [ ] Yes - PII (names, emails, addresses)
- [ ] Yes - financial data (transactions, accounts)
- [ ] Yes - health information (HIPAA/PHI)
- [ ] Yes - confidential business data (IP, trade secrets)
- [ ] Yes - government/defense (classified or CUI)
- [ ] No - public or non-sensitive data only

**7. Is the system multi-tenant (serves multiple customers/organizations)?**
- [ ] Yes - strict tenant isolation required
- [ ] Yes - soft isolation (shared infrastructure, logical separation)
- [ ] No - single organization only
- [ ] Unsure

### Technical Environment

**8. What base model(s) are you using?**
- [ ] OpenAI (GPT-4, GPT-3.5, etc.)
- [ ] Anthropic (Claude)
- [ ] Open-source (Llama, Mistral, etc.)
- [ ] Proprietary/custom fine-tuned model
- [ ] Multiple models (routing, fallback)
- [ ] Unsure

**9. Do you have access to a non-production environment that mirrors production configuration?**
- [ ] Yes - dedicated test environment
- [ ] Partial - can create temporary environment
- [ ] No - production only
- [ ] Unsure

**10. What level of logging/telemetry exists today?**
- [ ] Comprehensive (prompts, outputs, tool calls, traces)
- [ ] Partial (API calls, errors, but not full prompts)
- [ ] Minimal (basic application logs only)
- [ ] None or unsure

### Drivers and Constraints

**11. What is driving this security assessment? (Select all that apply)**
- [ ] Pre-launch validation (want to ship securely)
- [ ] Post-incident response (breach or exploit occurred)
- [ ] Regulatory/compliance requirement (audit, certification)
- [ ] Board or executive directive (risk management)
- [ ] Customer or partner demand (security questionnaire)
- [ ] Internal concern (team raised red flags)
- [ ] Proactive security posture improvement

**12. What is your timeline or urgency level?**
- [ ] Urgent (launch blocked, incident active) - need results in 1-2 weeks
- [ ] High priority (planning launch soon) - need results in 3-4 weeks
- [ ] Normal (ongoing project) - flexible timeline
- [ ] Exploratory (researching options) - no fixed timeline

**13. Who is the primary decision-maker for this engagement?**
- [ ] CISO / Security Director
- [ ] VP Engineering / CTO
- [ ] Product / AI Lead
- [ ] Procurement / Legal
- [ ] Multiple stakeholders (list roles): _______________________

**14. Have you previously conducted AI-specific security testing?**
- [ ] Yes - internal team tested
- [ ] Yes - external firm tested
- [ ] No - this would be our first AI security assessment
- [ ] We've done traditional pentesting but not AI-specific

**15. What is your approximate budget expectation for this work?**
- [ ] Under £25k
- [ ] £25k - £50k
- [ ] £50k - £100k
- [ ] Over £100k
- [ ] Budget not yet determined

---

## Discovery Call Script (30-45 Minutes)

**Pre-Call Preparation:**
- Review completed pre-call form
- Identify gaps or areas needing clarification
- Note any immediate red flags (unrealistic budget, insufficient access, etc.)

### Introduction (5 minutes)

**Agenda Setting:**
"Thanks for completing the pre-call form. I've reviewed your responses and want to use this call to:
1. Understand your AI system architecture in more detail
2. Clarify the specific risks you're most concerned about
3. Discuss access and logistics for testing
4. Determine if our AI Threat Exposure Review is the right fit
5. Outline next steps if we proceed

Does that work for you? Any questions before we dive in?"

### Section 1: System Architecture Deep Dive (10-12 minutes)

**Trust Boundary Discovery - Use this framework to map their system:**

**Model Boundary:**
- "You mentioned using [base model]. Is this accessed via API, self-hosted, or fine-tuned?"
- "Do you inject any system prompts or instructions to shape behavior?"
- "Have you observed any unexpected outputs, policy bypasses, or 'hallucinations' that concerned you?"

**Data/Knowledge Boundary:**
- "You indicated [RAG / no RAG]. Can you walk me through how the system accesses information?"
  - If RAG: "What's in your vector database? How is it populated? Can users influence what gets indexed?"
  - "Do you fine-tune or train models on proprietary data? If so, what data?"
- "How do you handle conversation history or memory across sessions?"

**Application/Agent Boundary:**
- "You mentioned [X tools]. Can you describe the most sensitive or powerful tool available?"
- "What permissions do these tools have? For example, can the agent delete data, send emails, or modify records?"
- "Does the system make decisions autonomously, or is there always a human in the loop?"
- "How do you handle authentication and user context when invoking tools?"

**Deployment/Governance Boundary:**
- "What does your current access control model look like? RBAC, ABAC, or something else?"
- "Do you have monitoring or alerting for anomalous AI behavior? What would trigger an alert today?"
- "How are API keys, model credentials, or secrets managed?"
- "Is there a CI/CD pipeline? Do you run any security or evaluation gates before deployment?"

**Probing Questions:**
- "What's the worst-case scenario if this system were compromised or manipulated?"
- "Have you ever seen the system do something you didn't expect or couldn't explain?"
- "Are there any 'hidden' capabilities or tools that power users have discovered?"

### Section 2: Risk Prioritization (5-7 minutes)

**Ask the client to rank their top concerns:**

"Based on what you've shared, here are common risks for systems like yours. Which 2-3 worry you most?"

- **Prompt Injection**: An attacker manipulates the model into ignoring instructions or leaking data
- **RAG Poisoning**: Malicious documents injected into your knowledge base corrupt all future outputs
- **Tool Abuse**: The agent invokes tools with attacker-controlled arguments (e.g., deleting customer data)
- **Sensitive Data Leakage**: The model exposes PII, credentials, or proprietary information
- **Authorization Bypass**: Users access data or actions beyond their permissions via LLM queries
- **Jailbreak**: Attackers bypass safety guardrails to generate harmful or policy-violating content
- **Agent Goal Hijacking**: Multi-step agents are redirected to attacker objectives instead of user goals

**Listen for:**
- Concrete concerns vs. vague "we need to be secure"
- Evidence of past incidents or near-misses
- Regulatory pressures (GDPR, HIPAA, SOC 2, AI Act)
- Customer security questionnaires or third-party audits

### Section 3: Access and Logistics (5-7 minutes)

**Testing Environment:**
- "Can you provide API access to a non-production environment that mirrors production?"
  - If no: "What are the constraints? Can we work with your team to create one?"
- "Will we have write permissions, or read-only? Can we trigger tool invocations?"
- "Are there any rate limits or usage caps we should be aware of?"

**Documentation and Support:**
- "Do you have architecture diagrams, data flow docs, or API schemas you can share?"
- "Can you provide a list of all tools/functions available to the agent?"
- "Who will be our technical point of contact during testing? Availability expectations?"

**Sensitive Data Handling:**
- "Does the test environment contain real customer data, or is it synthetic/anonymized?"
- "Are there any testing restrictions (e.g., don't test X feature, avoid Y dataset)?"

**Timeline and Blackouts:**
- "You mentioned [urgency level]. Are there any blackout periods (holidays, launch freezes, audits)?"
- "When would you ideally want testing to begin and results delivered?"

### Section 4: Stakeholder Alignment (3-5 minutes)

**Decision-Making Process:**
- "You mentioned [decision-maker role]. Who else needs to be involved in approving this engagement?"
- "What does your procurement process look like? NDA, MSA, SOW signature timeline?"
- "Are there any vendor requirements (insurance, SOC 2, background checks)?"

**Success Criteria:**
- "What would make this engagement successful from your perspective?"
- "Are you expecting a pass/fail outcome, or detailed findings with remediation guidance?"
- "Do you need this for compliance purposes (e.g., demonstrating due diligence to regulators or customers)?"

**Red Flags to Listen For:**
- Expectation of certification or compliance guarantee: "Will this get us SOC 2 compliant?"
- Unrealistic timelines: "We launch in 1 week, can you finish testing by then?"
- No access: "We can't give you API access, but can you still find issues?"
- Budget misalignment: "We have £5k budget for this"

### Section 5: Wrap-Up and Next Steps (5 minutes)

**Summarize What You Heard:**
"Let me recap to make sure I've understood correctly:
- You're building [system type] with [key features]
- Primary concerns are [top 2-3 risks]
- Testing environment: [access level and constraints]
- Timeline: [urgency and desired start date]
- Decision-maker: [roles involved]

Does that sound accurate?"

**Provide Initial Assessment:**
Based on this call, I'd recommend:
- **Strong Fit**: "This aligns well with our AI Threat Exposure Review. Based on system complexity, I'd estimate [Complexity Band A/B/C] engagement."
- **Weak Fit**: "Based on [reason], I'm not sure we're the right fit. You might be better served by [alternative recommendation]."
- **Conditional Fit**: "We could help, but we'd need [missing input] before proceeding."

**Next Steps:**
- [ ] Send one-pager and sample finding for internal review
- [ ] Provide formal SOW and pricing proposal
- [ ] Schedule follow-up call with additional stakeholders
- [ ] Politely decline and provide alternative recommendations

**Set Expectations:**
"If we proceed, you can expect [deliverables] over [timeline]. I'll send a proposal by [date]. Any questions before we wrap?"

---

## Qualification Scoring Rubric

Use this rubric immediately after the discovery call to determine fit and recommended next step.

### Scoring Dimensions (1-5 scale)

| Dimension | 1 (Poor Fit) | 3 (Moderate Fit) | 5 (Strong Fit) |
|-----------|--------------|------------------|----------------|
| **System Complexity** | No AI components / basic chatbot | RAG or 3-5 tools, single model | Multi-model, 10+ tools, agentic |
| **Risk Exposure** | Low-stakes, no sensitive data | PII or business-critical | Regulated (HIPAA/finance/gov) |
| **Access Availability** | Production only, no API access | Partial test env, some restrictions | Full non-prod env, API access |
| **Stakeholder Alignment** | Unclear decision-maker, procurement blockers | Decision-maker identified, normal approval | Security/Eng buy-in, fast approval |
| **Timeline Realism** | Expects results in &lt;1 week | 2-4 week timeline | Flexible, planned launch window |
| **Budget Alignment** | &lt;£15k budget | £25k-£50k budget | £50k+ budget, scope flexibility |

**Total Score:** _____ / 30

### Interpretation

**25-30 points: Strong Fit**
- System aligns well with ATER engagement
- Client has realistic expectations and resources
- High likelihood of successful engagement

**Action:**
- Provide detailed proposal with SOW
- Recommend Complexity Band based on system architecture
- Suggest optional add-ons (Purple Team, Continuous Monitoring, etc.)

**18-24 points: Moderate Fit**
- Some concerns (access, budget, timeline), but addressable
- May require scoping adjustments or phased approach
- Proceed with caution

**Action:**
- Clarify constraints and set expectations
- Propose modified scope if needed (e.g., focus on 2 trust boundaries instead of 4)
- Consider Band A with limited coverage as starting engagement

**Below 18: Weak Fit**
- Significant misalignment on access, budget, expectations, or system complexity
- High risk of dissatisfied client or failed engagement

**Action:**
- Politely decline or recommend alternative provider
- Provide educational resources (OWASP guides, free tools)
- Offer to revisit conversation in 3-6 months when constraints change

---

## Complexity Band Mapping (Post-Call Assessment)

After the discovery call, use these criteria to recommend a complexity band (see Pricing & Packaging Guide for full details):

### Band A: Standard AI Application
**Indicators:**
- Single LLM (API-based, no fine-tuning)
- 0-3 tools or no RAG
- Pre-production or limited internal deployment
- No multi-tenancy
- Non-regulated data (public, internal docs)

**Engagement Scope:**
- 2 weeks, 1 senior consultant
- 12-15 issues tested (selective coverage)
- 3-5 proof-of-concept exploits

### Band B: Complex AI System
**Indicators:**
- Multiple models or custom fine-tuned model
- RAG with 4-10 tools
- Production deployment with external users
- Multi-tenant architecture
- PII or business-critical data

**Engagement Scope:**
- 3 weeks, 1-2 senior consultants
- All 24 issues tested
- 7-10 proof-of-concept exploits
- Purple team tabletop exercise included

### Band C: Enterprise / High-Risk AI
**Indicators:**
- Mission-critical or regulated system (HIPAA, finance, defense)
- Complex agentic workflows with 10+ tools
- Multi-model orchestration or custom training pipeline
- Strict compliance requirements (SOC 2, AI Act, FedRAMP)
- High-value target (customer-facing, revenue-generating)

**Engagement Scope:**
- 4 weeks, 2 senior consultants
- All 24 issues + edge cases
- 10-15 proof-of-concept exploits
- Extended reporting (compliance deep dive, risk modeling)

---

## Recommended Next Steps Decision Matrix

| Outcome | Criteria | Recommended Action |
|---------|----------|-------------------|
| **Propose ATER (Band A)** | Score 18-24, standard system, limited budget | Send one-pager + Band A proposal |
| **Propose ATER (Band B)** | Score 22-28, production system, moderate complexity | Send one-pager + Band B proposal + add-ons menu |
| **Propose ATER (Band C)** | Score 25-30, high-risk/regulated, strong budget | Send one-pager + Band C proposal + compliance deep dive add-on |
| **Propose Different Engagement** | Score 18-22, narrow scope (e.g., RAG-only concern) | Recommend RAG Security Review or Agent Deep Dive instead of full ATER |
| **Defer / Nurture** | Score 15-20, not ready (no test env, early design) | Provide educational resources, revisit in 3-6 months |
| **Politely Decline** | Score &lt;15, red flags (unrealistic expectations, no access, budget mismatch) | Thank them, explain misalignment, suggest alternative providers or self-assessment tools |

---

## Red Flags - Immediate Disqualifiers

**Walk away or proceed with extreme caution if:**

1. **No Access**: Client refuses to provide non-production API access or any testing environment
2. **Unrealistic Guarantees Expected**: "Will this certify us as secure?" or "Can you guarantee no vulnerabilities after testing?"
3. **Insufficient Budget**: Budget &lt;£15k for full ATER engagement (doesn't cover cost of delivery)
4. **Impossible Timeline**: Expects comprehensive results in &lt;1 week without prior relationship
5. **Misaligned Stakeholders**: Engineering team wants assessment, but leadership/procurement actively blocking
6. **Ethical/Legal Concerns**: System purpose raises ethical red flags (surveillance, manipulation, prohibited use cases)
7. **Procurement Trap**: Unlimited liability clauses, Net-90+ payment terms, or vendor requirements that create unsustainable risk

---

## Post-Call Administrative Checklist

- [ ] Complete qualification scoring rubric
- [ ] Determine complexity band (A/B/C) or decline
- [ ] Update CRM with call notes and next step
- [ ] Send follow-up email within 24 hours with:
  - [ ] Summary of discussion
  - [ ] Recommended next step (proposal, decline, defer)
  - [ ] Timeline for proposal delivery (if proceeding)
  - [ ] Additional materials (one-pager, sample finding)
- [ ] Flag any access or documentation blockers for proposal stage
- [ ] Notify delivery team if engagement likely to proceed (resource planning)
