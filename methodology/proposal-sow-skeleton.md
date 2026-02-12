---
tags:
  - trust-boundary/general
  - type/methodology
description: "Reusable proposal and statement of work template for AI security engagements."
---
# Statement of Work: AI Threat Exposure Review

**Prepared for:** [Client Company Name]
**Prepared by:** [Your Company Name]
**Date:** [Date]
**Version:** [Version Number]

---

## 1. Background / Context

[Client Company Name] operates AI-powered systems including [brief description of AI application(s)]. These systems leverage [large language models / retrieval-augmented generation / agent workflows / tool integrations] to [business function/use case].

As AI systems introduce unique security risks not addressed by traditional application security testing, [Client Company Name] has engaged [Your Company Name] to conduct a comprehensive AI Threat Exposure Review. This assessment will evaluate the AI system across four architectural trust boundaries: Model, Data/Knowledge, Application/Agent Runtime, and Deployment/Governance.

The objective is to identify exploitable vulnerabilities through adversarial testing, provide proof-of-concept evidence, and deliver actionable remediation guidance aligned with industry frameworks (OWASP LLM Top 10, NIST AI RMF, MITRE ATLAS).

---

## 2. Objectives

The primary objectives of this engagement are to:

1. **Identify exploitable vulnerabilities** in the AI system's model behavior, data sources, application integration, and operational controls
2. **Validate security assumptions** about prompt injection defenses, RAG integrity, tool permission boundaries, and monitoring coverage
3. **Provide proof-of-concept exploits** demonstrating real-world attack scenarios
4. **Map findings to compliance frameworks** including OWASP LLM Top 10, NIST AI RMF, and MITRE ATLAS
5. **Deliver prioritized remediation guidance** with implementation estimates and risk reduction impact
6. **Enable informed risk decisions** for production deployment or continued operation

---

## 3. Scope (In Scope)

This engagement will assess the following components and attack surfaces:

### Model Security
- Jailbreak and safety bypass testing
- Prompt injection (direct and indirect)
- System prompt extraction attempts
- Sensitive information disclosure probes
- Model extraction and theft attempts
- Training data memorization testing

### Data / Knowledge Security
- RAG data poisoning via malicious document injection
- Retrieval manipulation and semantic hijacking
- Embedding integrity validation
- Cross-tenant knowledge isolation testing (if applicable)
- PII leakage assessment in knowledge corpus
- Prompt and conversation log security review

### Application / Agent Runtime Security
- Tool privilege escalation testing
- Unsafe tool invocation with malicious arguments
- Agent goal hijacking and workflow manipulation
- Authentication context confusion across sessions
- Prompt assembly and hierarchy enforcement validation
- Output encoding and injection prevention testing

### Deployment / Governance Security
- Access control and RBAC effectiveness validation
- Monitoring and alerting coverage gap analysis
- Secrets management review (API keys in prompts/logs)
- Model gateway/proxy configuration security
- CI/CD evaluation gate assessment
- Incident response playbook validation (tabletop exercise)

---

## 4. Out of Scope

The following activities are explicitly excluded from this engagement:

- Traditional infrastructure penetration testing (network, cloud, containers)
- Application source code review or static analysis
- Training pipeline and ML platform security (model training infrastructure)
- Performance testing, load testing, or availability validation
- Social engineering campaigns or phishing simulations
- Physical security assessments
- Compliance audit or certification (findings inform compliance, not certify)
- Third-party model provider security (e.g., OpenAI, Anthropic internals)

---

## 5. Approach / Methodology

Our methodology follows a trust-boundary-driven approach organized into six phases:

1. **Scoping and Threat Modeling**: Architecture review, data flow analysis, threat scenario development
2. **Model Security Testing**: Adversarial prompting, jailbreak campaigns, privacy attack validation
3. **Data/Knowledge Testing**: RAG poisoning, retrieval manipulation, embedding security
4. **Application/Agent Testing**: Prompt injection, tool abuse, agent workflow hijacking
5. **Deployment/Governance Review**: Access controls, monitoring gaps, operational security
6. **Reporting and Remediation**: Executive summary, technical findings, framework mapping, remediation roadmap

Testing combines manual adversarial techniques with automated tools (garak, PyRIT where applicable) and references MITRE ATLAS tactics/techniques for attack pattern validation.

---

## 6. Engagement Plan (Phases and Activities)

### Week 1: Discovery and Model Testing
**Days 1-2: Scoping and Threat Modeling**
- Kickoff meeting and stakeholder interviews
- Architecture documentation review
- Threat scenario development
- High-value asset identification

**Days 3-5: Model Security Testing**
- Jailbreak and policy bypass attempts
- System prompt extraction campaigns
- Privacy attack testing (extraction, inference)
- Model extraction via API queries
- Bias and toxicity probing

### Week 2: Data/Knowledge and Application Testing
**Days 6-8: Data/Knowledge Security Testing**
- RAG poisoning via malicious document injection
- Retrieval manipulation and ranking exploitation
- Embedding integrity validation
- Cross-tenant isolation testing (if applicable)
- PII leakage assessment

**Days 9-12: Application/Agent Runtime Testing**
- Direct and indirect prompt injection
- Tool privilege escalation and argument injection
- Agent goal hijacking and loop manipulation
- Business logic bypass testing
- Output encoding validation

### Week 3: Governance Review and Reporting
**Days 13-14: Deployment/Governance Review**
- Access control effectiveness validation
- Monitoring and alerting gap analysis
- Secrets management review
- CI/CD pipeline security assessment
- Incident response tabletop exercise

**Days 15-17: Reporting and Readout**
- Executive summary and technical report drafting
- Framework compliance mapping
- Remediation roadmap development
- Final readout presentation to stakeholders

---

## 7. Deliverables

Upon completion of the engagement, [Client Company Name] will receive:

1. **Executive Summary Report** (5-10 pages)
   - Business risk overview and strategic recommendations
   - Compliance status against OWASP/NIST frameworks
   - High-level remediation priorities

2. **Technical Findings Report** (30-50 pages)
   - Detailed vulnerability descriptions organized by trust boundary
   - Proof-of-concept exploits with evidence (prompts, logs, screenshots)
   - Exploitation scenarios and impact analysis
   - Severity classifications (Critical/High/Medium/Low)

3. **Framework Compliance Matrix**
   - Mappings to OWASP LLM Top 10 (2025)
   - NIST AI RMF function alignment (GOVERN/MAP/MEASURE/MANAGE)
   - MITRE ATLAS tactics and techniques references

4. **Risk-Prioritized Remediation Roadmap**
   - Phased mitigation plan (Quick Wins, Medium-Term, Long-Term)
   - Effort estimates and risk reduction impact
   - Implementation guidance and tool recommendations

5. **Evidence Package**
   - Prompt transcripts demonstrating successful exploits
   - Tool invocation logs and agent behavior traces
   - Screenshots and reproduction scripts
   - Testing methodology documentation

6. **Retest Validation Letter** (post-remediation, if applicable)
   - Verification of fixes for Critical and High severity findings
   - Residual risk assessment
   - Updated compliance status

---

## 8. Customer Responsibilities (Inputs, Access, Contacts)

To ensure successful execution, [Client Company Name] will provide:

### Technical Access
- API access to AI application with read/write permissions for testing
- Non-production environment matching production configuration
- Access to RAG vector stores, knowledge bases, and data sources (read-only minimum)
- Tool/plugin schemas and permission documentation
- Model configuration details (base model, version, fine-tuning approach)

### Documentation
- System architecture diagrams and data flow documentation
- Existing security controls and monitoring configurations
- User roles and permission matrices
- Incident response procedures (if available)

### Stakeholder Availability
- Technical point of contact for coordination and questions (5-10 hours/week)
- Security team representative for tabletop exercise (2-3 hours)
- Executive stakeholder for final readout presentation (1 hour)

### Coordination
- Authorization letter for testing activities (provided by [Your Company Name])
- Defined testing windows and blackout periods
- Escalation contacts for critical findings discovered during testing

---

## 9. Assumptions & Constraints

This engagement proceeds under the following assumptions:

- Testing will be conducted in a non-production environment that accurately reflects production configuration
- [Client Company Name] has authority to authorize security testing of all in-scope systems
- API rate limits will be relaxed or testing windows adjusted to accommodate security testing volume
- No disruptive testing (e.g., DoS, data deletion) will be performed without explicit approval
- Findings are limited to the tested version and configuration; changes may introduce new vulnerabilities
- Third-party model providers (OpenAI, Anthropic, etc.) are treated as black boxes; their internal security is not assessed
- Testing assumes good-faith adversary (we do not attempt to evade legal restrictions or bypass authentication entirely)

---

## 10. Reporting & Readout

### Interim Communication
- Weekly status updates via email or Slack
- Immediate notification of Critical severity findings
- Ad-hoc technical discussions as needed

### Final Deliverables Timeline
- **Draft Report**: Delivered [X] business days after testing completion
- **Client Review Period**: [Y] business days for feedback and clarification
- **Final Report**: Delivered [Z] business days after feedback incorporation
- **Executive Readout**: Scheduled within [W] business days of final report delivery

### Readout Presentation
- 60-minute presentation for executive and technical stakeholders
- Key findings, business impact, and remediation priorities
- Q&A session for clarification and implementation planning

---

## 11. Commercials

### Fees

**Total Professional Fees:** $[Amount] USD

This includes:
- [X] days of senior security consultant time
- All testing tools, infrastructure, and licenses
- Report development and presentation materials
- One round of report feedback/revision

**Payment Terms:**
- 50% upon contract signature
- 50% upon delivery of final report

### Additional Services (Optional)

The following services are available at additional cost:

- **Retest Engagement** (post-remediation): $[Amount] ([Y] days)
- **Extended Agent Security Deep Dive**: $[Amount] (+5 days)
- **Training Pipeline Security Assessment**: $[Amount] (+4 days)
- **Purple Team Workshop**: $[Amount] (+2 days)
- **Continuous Monitoring Setup**: $[Amount] (+3 days)

### Expenses

All reasonable travel expenses (if on-site work is required) will be billed at cost with prior approval.

---

## 12. Legal / Compliance Notes

### Authorization and Scope

[Client Company Name] authorizes [Your Company Name] to perform security testing within the scope defined in this SOW. All testing activities are conducted in good faith to identify security vulnerabilities for remediation purposes.

### Confidentiality

All information disclosed during this engagement is subject to the mutual non-disclosure agreement (NDA) dated [Date] between [Client Company Name] and [Your Company Name]. Findings, data, and client-specific details will not be shared with third parties without explicit written consent.

### Data Handling

[Your Company Name] will:
- Handle all client data in accordance with the NDA and applicable data protection regulations
- Not retain production data beyond the engagement period without explicit authorization
- Securely delete all client data within [X] days of engagement completion
- Use encrypted storage and transmission for all engagement artifacts

### Limitation of Liability

This engagement provides a point-in-time security assessment based on the scope, access, and testing period defined. [Your Company Name] makes no guarantees that all vulnerabilities will be identified, nor does this assessment constitute certification of security or compliance. Findings should be interpreted in consultation with legal, compliance, and technical teams.

**This is not legal, regulatory, or compliance advice.** [Client Company Name] should consult qualified counsel for compliance with NIST AI RMF, GDPR, HIPAA, PCI-DSS, or other applicable regulations.

### Disclaimer

Security testing may reveal sensitive findings. [Client Company Name] acknowledges that such findings reflect security posture at the time of testing and commits to good-faith remediation efforts. [Your Company Name] is not liable for business decisions made based on findings or for security incidents occurring before, during, or after the engagement.

---

## 13. Appendix: Issue Catalog Covered

This engagement tests for the following security issues organized by trust boundary. Not all issues may be applicable depending on system architecture.

### Model Trust Boundary
- Prompt Injection
- System Prompt Leakage
- Sensitive Information Disclosure
- Jailbreak and Policy Bypass
- Model Extraction and Theft
- Training Data Memorization and Extraction

### Data / Knowledge Trust Boundary
- RAG Data Poisoning
- Retrieval Manipulation and Hijacking
- Embedding Poisoning
- Unauthorized Knowledge Disclosure
- PII in Knowledge Corpus
- Prompt and Conversation Log Data Leakage

### Application / Agent Runtime Trust Boundary
- Tool Privilege Escalation
- Unsafe Tool Invocation
- Agent Goal Hijacking
- Authentication Context Confusion
- Insecure Prompt Assembly
- Insufficient Output Encoding

### Deployment / Governance Trust Boundary
- Insufficient Telemetry and Tracing
- Weak Access Segmentation
- Secrets in Prompts and Logs
- Insecure Model Gateway Configuration
- Missing Evaluation Gates in CI/CD
- Incident Response Gap

---

## Acceptance

By signing below, both parties agree to the terms and conditions outlined in this Statement of Work.

**For [Client Company Name]:**

Signature: ___________________________
Name: [Name]
Title: [Title]
Date: ___________________________

**For [Your Company Name]:**

Signature: ___________________________
Name: [Name]
Title: [Title]
Date: ___________________________
