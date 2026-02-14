---
title: "AI Threat Exposure Review"
tags:
  - type/playbook
  - target/llm
  - target/generative-ai
  - target/agent
  - target/rag
maturity: reviewed
created: 2026-02-14
updated: 2026-02-14
---

# AI Threat Exposure Review

## Overview

A comprehensive security assessment that evaluates an AI system across all four trust boundaries: Model, Data/Knowledge, Application/Agent Runtime, and Deployment/Governance. This playbook identifies exploitable vulnerabilities, validates security controls, and provides actionable remediation guidance aligned with OWASP, NIST, and MITRE ATLAS frameworks.

**Why this playbook exists**: Organizations deploying AI systems need assurance that all attack surfaces are evaluated, not just specific components. Real-world incidents demonstrate that vulnerabilities can exist across multiple trust boundaries, and a comprehensive assessment ensures no critical gaps are missed. This playbook is particularly valuable for pre-deployment validation, M&A due diligence, regulatory compliance, and establishing baseline security posture.

**What this is NOT**:
- Not a focused deep-dive (provides breadth across all boundaries rather than depth in specific areas)
- Not a risk assessment (active security testing, not review-based risk analysis)
- Not source code review (focuses on runtime security and behavior)
- Not infrastructure-only pentest (includes AI-specific testing beyond traditional infrastructure security)

---

## Scope & Prerequisites

### Supported Target Archetypes

- **LLM-Powered Applications**: Chatbots, virtual assistants, content generators with LLM backends
- **RAG Systems**: Applications using retrieval-augmented generation with external knowledge sources
- **Agentic Systems**: Autonomous AI agents with tool access and multi-step decision-making
- **Enterprise AI Platforms**: Multi-component AI systems with complex integrations
- **Third-Party AI Integrations**: Systems integrating external LLM services or agentic platforms

### Explicitly Out of Scope

- **Source Code Review**: Deep code audits of application logic (requires separate engagement)
- **Training Pipeline Security**: Security of model training infrastructure and data pipelines
- **Network Penetration Testing**: Traditional infrastructure pentesting beyond AI components
- **Physical Security**: On-premises data center or hardware security assessments
- **Social Engineering**: Phishing campaigns or physical access attempts targeting staff
- **Performance Testing**: Load testing, scalability validation, or SLA verification

### Prerequisites

#### Before Testing Begins

- **API Access**: Full API credentials with read/write permissions in non-production environment
- **Test Accounts**: Minimum 3 user accounts with varying permission levels
- **Architecture Documentation**: System diagram showing LLM integration, tool access, data flows, trust boundaries
- **Model Information**: Provider, model version, fine-tuning status
- **RAG Configuration** (if applicable): Vector store type, indexing sources, retrieval strategy
- **Tool Inventory**: Complete list of available functions with permission model and schemas
- **Conversation Logs** (sample): Example interactions showing typical usage patterns
- **Policy Documentation**: Content policies, safety guidelines, intended use restrictions
- **Stakeholder Availability**: Technical contact for architecture questions

#### During Assessment

- Timely responses to clarification questions (target: under 24hr turnaround)
- Ability to provision additional test accounts if needed
- Access to application logs for evidence validation
- Optional mid-engagement checkpoint (recommended at 50% completion)

---

## Methodology

### Phase 1: Scoping and Threat Modeling (2-3 days)

**Objectives**: Define assessment boundaries, identify high-value targets, establish success criteria

**Activities**:
- Architecture documentation review and data flow analysis
- Identification of high-value assets and critical attack paths
- Threat actor profiling and attack scenario development
- Mapping system components to trust boundaries
- Trust boundary-specific issue selection

**Evidence Collected**:
- System architecture diagram
- Threat model document with attack trees
- Prioritized issue list mapped to trust boundaries
- Test scenarios with expected outcomes

---

### Phase 2: Model Security Testing (3-5 days)

**Objectives**: Validate model behavior and manipulation resistance

**Activities**:
- Jailbreak attempts using multi-turn adversarial prompts
- System prompt extraction and policy bypass validation
- Privacy attack testing (training data extraction, membership inference)
- Model extraction attempts via API query analysis
- Bias and toxicity probing across sensitive domains
- Prompt injection campaigns (direct and indirect)

**Evidence Collected**:
- Successful jailbreak demonstrations
- System prompt leakage examples
- Privacy attack proof-of-concepts
- Model extraction attempts
- Bias and toxicity findings

---

### Phase 3: Data and Knowledge Surface Testing (3-4 days)

**Objectives**: Assess security of external data sources and retrieval mechanisms

**Activities**:
- RAG poisoning via malicious document injection
- Embedding manipulation and retrieval hijacking tests
- Cross-tenant knowledge isolation validation
- PII leakage assessment in corpus and logs
- Prompt history and conversation memory security review
- Data provenance and integrity validation

**Evidence Collected**:
- Corpus poisoning demonstrations
- Retrieval manipulation examples
- Cross-tenant isolation failures
- PII exposure instances
- Data integrity findings

---

### Phase 4: Application and Agent Testing (4-6 days)

**Objectives**: Validate application integration and agent behavior security

**Activities**:
- Direct and indirect prompt injection campaigns
- Tool privilege escalation and argument injection
- Agent goal hijacking and workflow manipulation
- Business logic bypass through LLM-driven decision exploitation
- MCP/plugin security validation (if applicable)
- Output encoding and injection prevention testing
- Authentication context confusion testing

**Evidence Collected**:
- Tool abuse proof-of-concepts
- Agent goal hijacking demonstrations
- Business logic bypass examples
- Authentication bypass findings
- Output encoding failures

---

### Phase 5: Deployment and Governance Review (2-3 days)

**Objectives**: Assess operational controls and governance mechanisms

**Activities**:
- Access control and RBAC effectiveness validation
- Monitoring and alerting coverage gap analysis
- Secrets management review (API keys in prompts/logs)
- Model gateway/proxy configuration security
- CI/CD pipeline evaluation gate assessment
- Incident response playbook validation (tabletop exercise)

**Evidence Collected**:
- Access control bypass demonstrations
- Monitoring gap analysis
- Secrets exposure findings
- Configuration misconfigurations
- Incident response gaps

---

### Phase 6: Reporting and Remediation Guidance (2-3 days)

**Activities**:
- Findings analysis, deduplication, and risk prioritization
- Root cause identification and systemic pattern analysis
- Framework mapping (OWASP, NIST, ATLAS)
- Remediation roadmap development with effort estimates
- Executive summary and business impact assessment
- Evidence package preparation

---

## Techniques Covered

| ID | Technique | Relevance |
|----|-----------|-----------|
| AML.T0051 | [[techniques/prompt-injection|Prompt Injection]] | Direct and indirect manipulation testing |
| AML.T0054 | [[techniques/jailbreak-policy-bypass|Jailbreak & Policy Bypass]] | Safety guardrail bypass validation |
| AML.T0056 | [[techniques/system-prompt-leakage|System Prompt Leakage]] | Hidden instruction extraction |
| - | [[techniques/tool-privilege-escalation|Tool Privilege Escalation]] | Unauthorized tool access via prompts |
| - | [[techniques/rag-data-poisoning|RAG Data Poisoning]] | Knowledge base injection attacks |
| - | [[techniques/agent-goal-hijack|Agent Goal Hijacking]] | Autonomous agent objective redirection |
| - | [[techniques/training-data-memorization|Training Data Memorization]] | Privacy attack via data extraction |
| AML.T0059 | [[techniques/model-extraction|Model Extraction]] | API-based model theft attempts |
| - | [[techniques/secrets-in-prompts-and-logs|Secrets in Prompts and Logs]] | Credential exposure validation |
| - | [[techniques/insufficient-telemetry-and-tracing|Insufficient Telemetry]] | Monitoring gap identification |

---

## Tooling

### Manual Testing

**Primary approach**: Interactive adversarial testing with systematic coverage across all trust boundaries. Human judgment essential for crafting context-aware exploits and interpreting nuanced model behavior.

**Tools**:
- **Burp Suite / ZAP**: HTTP request interception, manipulation, and replay
- **Custom scripts**: Python/Node.js clients for programmatic testing
- **Browser DevTools**: WebSocket analysis for streaming responses
- **Postman / Insomnia**: API endpoint testing and collection organization
- **Vector database tools**: Direct manipulation of embeddings and similarity searches

### Automated Testing

**Automation scope**: Payload variation, regression testing, baseline comparison

**Frameworks**:
- **garak**: Automated prompt injection and jailbreak payload generation
- **PyRIT**: Multi-turn adversarial campaigns with adaptive strategies
- **Custom harnesses**: Domain-specific test suites for target application patterns
- **Adversarial Robustness Toolbox**: Model robustness testing
- **Embedding attack libraries**: Tools for RAG-specific attacks

### Reproducibility

All findings include:
- Complete prompt-response transcripts with timestamps
- API request/response pairs (headers, bodies, status codes)
- Environment snapshot (model version, configuration, endpoint URLs)
- Reproduction scripts in Python/JavaScript where automatable
- Screen recordings for complex multi-step or UI-based attacks
- Success rate data (e.g., "Bypass succeeds in 8/10 attempts")

---

## Deliverables

### 1. Executive Summary Report (5-10 pages)

- Critical/High findings summary with business impact
- Risk score and compliance posture (OWASP LLM Top 10 coverage)
- Strategic recommendations (architecture, policy, monitoring)
- Comparison to industry baseline risk profiles

### 2. Technical Findings Report (30-50 pages)

- Detailed vulnerability write-ups per trust boundary
- PoC evidence: transcripts, screenshots, logs
- Exploitation narratives with business context
- Root cause analysis and mitigation guidance
- Framework mapping (ATLAS, OWASP, NIST)

### 3. Framework Compliance Matrix

- OWASP LLM Top 10 tested vs. not-tested breakdown
- MITRE ATLAS technique coverage
- NIST AI RMF alignment (MEASURE/MANAGE functions)

### 4. Risk-Prioritized Remediation Roadmap

- Phased mitigation plan: Immediate → Short-term → Long-term
- Effort estimates per fix (hours/days)
- Risk reduction impact scores
- Validation criteria and retest guidance

### 5. Secure Design Patterns Guide

- Architectural recommendations specific to identified gaps
- Trust boundary-specific secure design patterns
- Integration best practices

### 6. Evidence Package

- Prompt injection payloads (100+ examples)
- Successful jailbreak techniques
- Tool abuse proof-of-concepts
- Data exfiltration demonstrations
- RAG poisoning examples
- Reproduction scripts and test harnesses

---

## Sources

> "In the era of digital transformation, security testing must evolve to address AI-specific attack surfaces across all trust boundaries."
> — [[sources/bibliography#AI-Native LLM Security]], p. 125-126

> "Real-world incidents demonstrate that vulnerabilities can exist across multiple trust boundaries, requiring comprehensive evaluation."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 19-26
