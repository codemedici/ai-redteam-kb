---
title: "Privacy Impact Assessments"
tags:
  - type/mitigation
  - target/llm
  - target/generative-ai
  - target/ml-model
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Privacy Impact Assessments

## Summary

Privacy Impact Assessments (PIAs) are comprehensive evaluations conducted before deploying LLMs to proactively identify potential privacy vulnerabilities specific to the implementation and develop tailored strategies to address identified risks. PIAs systematically analyze data flows, processing activities, privacy risks, and mitigation measures—enabling organizations to make informed decisions about privacy trade-offs and demonstrate regulatory compliance before systems go live.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/pii-in-corpus]] | Identifies PII exposure risks before deployment, enabling preventive controls |
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Maps all sensitive data flows and potential disclosure vectors proactively |
| AML.T0056 | [[techniques/training-data-memorization]] | Assesses memorization risks and validates mitigation strategies before deployment |
| | [[techniques/membership-inference-attacks]] | Evaluates privacy attack surface and validates defensive measures |

## Implementation

### When to Conduct a PIA

**Mandatory PIA triggers (GDPR Article 35):**
- Systematic and extensive evaluation of personal data (including profiling)
- Large-scale processing of special category data (health, biometrics, race, religion)
- Systematic monitoring of publicly accessible areas at large scale
- Use of new technologies (LLMs/generative AI qualify)

**Recommended PIA triggers:**
- Any LLM deployment processing personal data
- Major system changes affecting data handling
- New data sources added to training or RAG corpus
- Expansion to new jurisdictions with different privacy regulations
- Integration with third-party systems involving data sharing

### PIA Process for LLM Deployment

**Phase 1: Scope Definition (1-2 weeks)**

Define boundaries of the assessment:

```
PIA Scope Document:
├─ System description (what does the LLM do?)
├─ Personal data categories processed (PII types, special categories)
├─ Data subjects affected (users, employees, customers, etc.)
├─ Legal basis for processing (consent, contract, legitimate interest)
├─ Geographic scope (which jurisdictions' regulations apply?)
├─ Third-party involvement (data processors, cloud providers)
└─ Assessment team (who conducts the PIA?)
```

**Example Scope Statement:**
> "This PIA covers the customer service chatbot powered by GPT-4, which processes customer support queries containing names, email addresses, order histories, and product complaints. The system serves 50,000 daily active users in the US and EU. Data is processed by OpenAI (third-party) under a Data Processing Agreement."

---

**Phase 2: Data Flow Mapping (2-3 weeks)**

Document every stage where personal data is collected, processed, stored, or shared:

```
Data Flow Map:
├─ Collection: How is data obtained? (user input, database query, API)
├─ Processing: What happens to data? (embedding, training, inference)
├─ Storage: Where is data kept? (vector DB, logs, backups)
├─ Sharing: Is data shared with third parties? (cloud providers, analytics)
├─ Retention: How long is data kept?
└─ Deletion: How is data disposed of?
```

**Example Data Flow:**
```
User submits support query with PII
    ↓
Query logged to audit database (retained 90 days)
    ↓
Query sent to OpenAI API (processed, not stored per DPA)
    ↓
LLM response returned
    ↓
Response logged with query (retained 90 days)
    ↓
Logs archived to cold storage (retained 7 years for compliance)
    ↓
Data deleted after retention period
```

**Key questions:**
- Does the LLM training data contain PII?
- Are user queries logged? For how long?
- Is data encrypted at rest and in transit?
- Can users delete their data on request?
- Are embeddings of PII stored in vector databases?

---

**Phase 3: Privacy Risk Assessment (2-4 weeks)**

Systematically identify privacy threats and assess likelihood/impact:

**Risk Assessment Template:**

| Risk ID | Privacy Threat | Likelihood | Impact | Risk Level | Affected Data Subjects |
|---------|----------------|------------|--------|------------|----------------------|
| R-01 | PII in training data memorized and regurgitated | Medium | High | **High** | All users whose data was in training set |
| R-02 | User queries containing PII logged indefinitely | High | Medium | **High** | All chatbot users |
| R-03 | Third-party API provider accesses PII | Medium | High | **High** | All chatbot users |
| R-04 | Embeddings enable re-identification of individuals | Low | High | **Medium** | RAG corpus contributors |
| R-05 | Insufficient access controls enable insider threats | Low | High | **Medium** | All data subjects |

**Likelihood Scale:**
- **Low:** Unlikely given current controls
- **Medium:** Possible if controls fail or attacker is sophisticated
- **High:** Likely without additional mitigations

**Impact Scale:**
- **Low:** Minor inconvenience, no regulatory implications
- **Medium:** Reputational damage, minor regulatory scrutiny
- **High:** Significant harm to individuals, major regulatory penalties

**Risk Level:** Likelihood × Impact

---

**Phase 4: Mitigation Strategy (3-5 weeks)**

For each identified risk, define mitigation measures:

**Mitigation Plan Template:**

| Risk ID | Mitigation Measure | Responsibility | Timeline | Status |
|---------|-------------------|----------------|----------|--------|
| R-01 | Apply differential privacy during training | ML Engineering | Before training starts | ✅ Implemented |
| R-02 | Implement PII redaction in logs + 90-day retention | Platform Engineering | Before deployment | 🟡 In Progress |
| R-03 | Execute Data Processing Agreement with OpenAI | Legal + Procurement | Before deployment | ✅ Completed |
| R-04 | Anonymize documents before embedding | Data Engineering | Before RAG deployment | 🟡 In Progress |
| R-05 | Implement role-based access controls + audit logging | Security Engineering | Before deployment | 🔴 Not Started |

**Mitigation Categories:**
1. **Preventive:** Stop risk from materializing (anonymization, encryption, access controls)
2. **Detective:** Identify when risk occurs (monitoring, audit logs, anomaly detection)
3. **Corrective:** Remediate after incident (data deletion, breach notification, model retraining)

---

**Phase 5: Documentation & Approval (1-2 weeks)**

**PIA Report Structure:**

```markdown
# Privacy Impact Assessment Report

## Executive Summary
- High-level privacy risks
- Mitigation summary
- Approval recommendation

## System Description
- Functionality and use cases
- Technical architecture
- Data flows

## Privacy Risk Analysis
- Identified risks (from Phase 3)
- Likelihood and impact assessment

## Mitigation Measures
- Preventive, detective, corrective controls
- Implementation timeline
- Responsible parties

## Residual Risks
- Risks that cannot be fully eliminated
- Acceptance criteria
- Monitoring plans

## Compliance Assessment
- GDPR compliance (Articles 5, 25, 32, 35)
- CCPA compliance (if applicable)
- Industry-specific regulations

## Approval
- Signatures from stakeholders
- Data Protection Officer (DPO) review
- Senior management sign-off
```

**Approval Criteria:**
- All high-risk items have mitigation plans
- Residual risks are acceptable and documented
- Legal/compliance review completed
- DPO approval obtained

---

### PIA for Common LLM Use Cases

**Use Case 1: Customer Support Chatbot**

**Key privacy risks:**
- User queries contain PII (names, email, account details)
- Queries sent to third-party LLM provider
- Conversation logs retained for training/analytics

**Critical PIA questions:**
- Is PII redacted before logging?
- Does Data Processing Agreement exist with LLM provider?
- Can users request conversation deletion?
- Are logs encrypted at rest?

---

**Use Case 2: RAG System with Internal Documents**

**Key privacy risks:**
- Internal documents contain employee PII, confidential business info
- Embeddings may enable reconstruction of original documents
- Multi-tenant deployment could leak data across users

**Critical PIA questions:**
- Are documents sanitized before embedding?
- Are embeddings access-controlled by user role?
- Is vector database isolated per tenant?
- Are embeddings deleted when source documents are deleted?

---

**Use Case 3: LLM Training on User-Generated Content**

**Key privacy risks:**
- Training data contains PII, sensitive content
- Model memorization enables extraction attacks
- Consent may not cover training use case

**Critical PIA questions:**
- Was explicit consent obtained for training use?
- Is differential privacy applied during training?
- Are vulnerable data samples identified and excluded?
- Can users opt-out or request data deletion?

---

### PIA Templates and Tools

**GDPR PIA Template:**
- https://ico.org.uk/for-organisations/guide-to-data-protection/guide-to-the-general-data-protection-regulation-gdpr/accountability-and-governance/data-protection-impact-assessments/
- UK Information Commissioner's Office (ICO) template

**NIST Privacy Framework:**
- https://www.nist.gov/privacy-framework
- Risk-based approach to privacy management

**CNIL (France) PIA Software:**
- https://www.cnil.fr/en/open-source-pia-software-helps-carry-out-data-protection-impact-assesment
- Free open-source PIA tool

**ISO 29134 (PIA Guidelines):**
- International standard for conducting PIAs
- Methodology and best practices

---

### Continuous PIA (Not One-Time)

**When to update PIA:**
- Major system changes (new data sources, architecture changes)
- New privacy risks identified (novel attack techniques)
- Regulatory changes (new laws, guidance updates)
- Incidents or near-misses (breach, vulnerability discovery)

**Recommended cadence:**
- Full PIA refresh: Annually
- Incremental PIA updates: Quarterly or after significant changes
- Risk review: After every incident

---

## Limitations & Trade-offs

**Advantages:**
- Proactive risk identification before deployment
- Demonstrates regulatory compliance (GDPR Article 35)
- Builds stakeholder trust (transparency in privacy practices)
- Reduces incident response costs (prevention cheaper than remediation)
- Facilitates informed decision-making on privacy trade-offs

**Trade-offs:**
- **Time-intensive:** Full PIA takes 8-16 weeks depending on complexity
- **Resource-intensive:** Requires cross-functional team (legal, engineering, security, compliance)
- **Expertise required:** Effective PIAs need privacy and LLM domain knowledge
- **Potential project delays:** High-risk findings may delay deployment for mitigation implementation

**Limitations:**
- PIA cannot eliminate all privacy risks (only identify and mitigate)
- Effectiveness depends on quality of assessment (garbage in, garbage out)
- PIAs become outdated as systems evolve (continuous updates required)
- PIAs do not guarantee immunity from regulatory enforcement

---

## Testing & Validation

**PIA Quality Checklist:**
- [ ] All personal data flows documented comprehensively
- [ ] Risk assessment covers all identified privacy threats
- [ ] Mitigation measures are specific and actionable (not vague statements)
- [ ] Residual risks explicitly acknowledged and accepted
- [ ] Compliance with applicable regulations validated
- [ ] DPO or privacy expert reviewed the PIA
- [ ] Approval obtained from senior management
- [ ] PIA published or made available to stakeholders (transparency principle)

**Post-Deployment Validation:**
1. Verify mitigation measures were implemented as planned
2. Conduct penetration testing targeting identified risks
3. Monitor for privacy incidents (measure effectiveness)
4. Review PIA accuracy after 6-12 months (lessons learned)

---

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

---

## Sources

> "Privacy Impact Assessments (PIAs): Conduct comprehensive PIAs before deploying LLMs to proactively identify potential privacy vulnerabilities specific to the implementation and develop tailored strategies to address identified risks."
> — Extracted from PII in Corpus mitigation section

> GDPR Article 35: Data Protection Impact Assessment
> — https://gdpr-info.eu/art-35-gdpr/

> UK ICO: Data protection impact assessments
> — https://ico.org.uk/for-organisations/guide-to-data-protection/guide-to-the-general-data-protection-regulation-gdpr/accountability-and-governance/data-protection-impact-assessments/

> NIST Privacy Framework
> — https://www.nist.gov/privacy-framework

---

## Related

- **Enables:** [[mitigations/privacy-by-design]], [[mitigations/data-minimization-principles]], [[mitigations/incident-response-procedures]]
- **Required by:** GDPR Article 35, CCPA, HIPAA Privacy Rule
- **Outputs inform:** System architecture decisions, mitigation priorities, compliance documentation
