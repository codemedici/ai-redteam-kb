---
id: template-engagement-page-format
title: "TEMPLATE - Engagement Page Format"
sidebar_label: "TEMPLATE - Engagement Page Format"
description: Canonical template for all engagement type pages. Do not use this as actual content.
draft: true
---

# TEMPLATE - Engagement Page Format

> **⚠️ THIS IS A TEMPLATE FILE** - Copy this structure when creating new engagement pages. Replace all placeholder text with actual content.

## Overview

### What This Engagement Is

[1-2 paragraph description of what this engagement delivers and why a customer would purchase it. Include the primary focus, key activities, and value proposition.]

**Why this engagement exists**: [Brief explanation of the real-world need, reference to incidents or risks that demonstrate the value, or business drivers that make this engagement necessary.]

### What This Is Not

- **[Exclusion 1]**: [What this engagement does NOT cover and why]
- **[Exclusion 2]**: [What requires a different engagement type]
- **[Exclusion 3]**: [What is explicitly out of scope]

---

## Scope

### Supported Target Archetypes

- **[Archetype 1]**: [Description of system type this engagement targets]
- **[Archetype 2]**: [Another system type]
- **[Archetype 3]**: [Additional system types]

### Explicitly Out of Scope

- [Exclusion 1: e.g., "Training data quality assessment (requires separate engagement)"]
- [Exclusion 2: e.g., "Fine-tuning pipeline security (not included)"]
- [Exclusion 3: e.g., "Application penetration testing beyond AI components"]

---

## Threat Model

### Attacker Goals

Primary objectives evaluated during this engagement:

1. **[Goal 1]**: [Description of attacker objective]
2. **[Goal 2]**: [Another objective]
3. **[Goal 3]**: [Additional objectives]

### Threat Actor Assumptions

- **Access Level**: [What level of access the attacker is assumed to have]
- **Technical Sophistication**: [Skill level required]
- **Resources**: [What resources the attacker can deploy]
- **Persistence**: [Time horizon for attacks]

### Trust Boundaries Evaluated

This engagement focuses on:

- **[Trust Boundary 1]**: [Description of how this boundary is tested]
  - Issues tested: [List of specific issues or link to issue pages]

- **[Trust Boundary 2]**: [Description]
  - Issues tested: [List of specific issues]

[[trust-boundaries|See Trust Boundaries overview]]

---

## Trust Boundary Relevance

### Model

[Brief description of how this engagement addresses model-level security concerns.]

**Applicable Issues:**
- [Issue slug/title 1]
- [Issue slug/title 2]
- [Issue slug/title 3]

### Data / Knowledge

[Brief description of how this engagement addresses data and knowledge surface security.]

**Applicable Issues:**
- [Issue slug/title 1]
- [Issue slug/title 2]
- [Issue slug/title 3]

### Application / Agent Runtime

[Brief description of how this engagement addresses application and agent integration security.]

**Applicable Issues:**
- [Issue slug/title 1]
- [Issue slug/title 2]
- [Issue slug/title 3]

### Deployment / Governance

[Brief description of how this engagement addresses operational and governance controls.]

**Applicable Issues:**
- [Issue slug/title 1]
- [Issue slug/title 2]
- [Issue slug/title 3]

---

## Test Methodology

### Phase 1: [Phase Name] ([Duration])

**Objectives**: [What this phase aims to achieve]

**Activities**:
- [Specific activity 1]
- [Specific activity 2]
- [Specific activity 3]

**Evidence Collected**:
- [Type of evidence 1]
- [Type of evidence 2]

### Phase 2: [Phase Name] ([Duration])

**Objectives**: [What this phase aims to achieve]

**Activities**:
- [Specific activity 1]
- [Specific activity 2]

**Evidence Collected**:
- [Type of evidence 1]

[Continue with additional phases as needed]

### Phase N: Reporting and Remediation Guidance ([Duration])

**Activities**:
- Findings analysis, deduplication, and risk prioritization
- Root cause identification and systemic pattern analysis
- Framework mapping (OWASP, NIST, ATLAS)
- Remediation roadmap development with effort estimates
- Executive summary and business impact assessment
- Evidence package preparation

---

## Techniques Covered

Checklist of attack classes evaluated during this engagement:

- [x] **[Technique 1]**: [Brief description] → [Link to wiki page]
- [x] **[Technique 2]**: [Brief description] → [Link to wiki page]
- [x] **[Technique 3]**: [Brief description] → [Link to wiki page]

[[trust-boundaries|Full attack taxonomy]]

---

## Tooling and Test Harness

### Manual Testing

**Primary approach**: [Description of manual testing approach and why human judgment is important]

**Tools used**:
- **[Tool 1]**: [What it's used for]
- **[Tool 2]**: [What it's used for]
- **[Tool 3]**: [What it's used for]

### Automated Testing

**Automation scope**: [What aspects are automated]

**Frameworks**:
- **[Framework 1]**: [What it does]
- **[Framework 2]**: [What it does]
- **[Framework 3]**: [What it does]

### Reproducibility

All findings include:
- [Evidence type 1: e.g., "Complete prompt-response transcripts with timestamps"]
- [Evidence type 2: e.g., "API request/response pairs"]
- [Evidence type 3: e.g., "Environment snapshot"]
- [Evidence type 4: e.g., "Reproduction scripts"]
- [Evidence type 5: e.g., "Success rate data"]

---

## Deliverables

### 1. Executive Summary Report ([X-Y pages])

- [Key content 1]
- [Key content 2]
- [Key content 3]

### 2. Technical Findings Report ([X-Y pages])

- [Key content 1]
- [Key content 2]
- [Key content 3]

### 3. Framework Compliance Matrix

- [Framework 1] coverage
- [Framework 2] coverage
- [Framework 3] coverage

### 4. Risk-Prioritized Remediation Roadmap

- Phased mitigation plan: Immediate → Short-term → Long-term
- Effort estimates per fix (hours/days)
- Risk reduction impact scores
- Validation criteria and retest guidance

### 5. Evidence Package

- [Evidence type 1]
- [Evidence type 2]
- [Evidence type 3]

### 6. Optional: Retest Letter (Post-Remediation)

- Validation of Critical/High fixes
- Confirmation of risk reduction
- Residual issues identification

---

## Evidence Standards

### Confirmed Finding

A vulnerability is confirmed when:

- **Reproducible**: [Criteria for reproducibility, e.g., "Attack succeeds consistently (80% or higher across 5+ attempts)"]
- **Exploitable**: PoC demonstrates concrete harm:
  - [Harm type 1]
  - [Harm type 2]
  - [Harm type 3]
- **In-Scope**: Targets [relevant trust boundaries] as defined
- **Documented**: Complete evidence with exact reproduction steps

**Severity Scoring**:
- **Critical**: [Criteria for critical severity]
- **High**: [Criteria for high severity]
- **Medium**: [Criteria for medium severity]
- **Low**: [Criteria for low severity]

### Hypothesis / Observation

Lower-confidence findings documented separately:

- **Hypothesis**: [Description of hypothesis-level findings]
- **Observation**: [Description of observation-level findings]
- **Near-Miss**: [Description of near-miss findings]

---

## Framework Mapping

This engagement produces findings mapped to:

### MITRE ATLAS

**Tactics**:
- [AML.TA####]: [Tactic name]
- [AML.TA####]: [Tactic name]

**Techniques**:
- [AML.T####]: [Technique name]
- [AML.T####]: [Technique name]

[[atlas|Full ATLAS reference]]

### OWASP LLM Top 10

**Primary Coverage**:
- [LLM##]: [Issue name]
- [LLM##]: [Issue name]

**Secondary Coverage**:
- [LLM##]: [Issue name]

[OWASP LLM Top 10 reference](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

### NIST AI RMF

**Functions Tested**:
- [FUNCTION NAME]: [Description]
- [FUNCTION NAME]: [Description]

**Informs**: [Which functions are informed by this engagement]

---

## Example Findings

### Finding 1: [Finding Title]

**Severity**: [Critical | High | Medium | Low]

**Trust Boundary**: [Model | Data/Knowledge | Application/Agent Runtime | Deployment/Governance]

**Summary**: [Brief description of the vulnerability]

**Impact**: [What the impact is]

**Proof**: [Proof of concept or evidence]

**Remediation**: [Remediation guidance]

**Wiki Reference**: [Link to technical issue page]

---

## Engagement Inputs Required

### Before Testing Begins

- [x] **[Input 1]**: [Description of what's needed]
- [x] **[Input 2]**: [Description]
- [x] **[Input 3]**: [Description]
- [x] **[Input 4]**: [Description]

### During Assessment

- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

---

## Output Format

### Finding Structure

Each vulnerability follows this format:

**[Finding ID]**: [Descriptive Title]

**Severity**: [Critical | High | Medium | Low]

**Trust Boundary**: [Model | Data/Knowledge | Application/Agent Runtime | Deployment/Governance]

**Framework IDs**: 
- MITRE ATLAS: [AML.T####]
- OWASP LLM: [LLM##]

**Description**: 
[Root cause analysis: why the vulnerability exists, what architectural or implementation decision created it]

**Exploitation Scenario**:
[Narrative with business context: how attacker would exploit in production, realistic attack timeline, potential damage]

**Proof of Concept**:
```
[Exact reproduction steps]
[Request/Input]: [what was sent]
[Response/Output]: [what was received]
[Result]: [security impact demonstrated]
[Success rate]: [X/Y attempts]
```

**Evidence**:
- [Evidence type]: [Reference to appendix item]
- [Evidence type]: [Reference]

**Impact**:
- **Confidentiality**: [Specific data exposed or exfiltrated]
- **Integrity**: [Unauthorized modifications or business logic bypass]  
- **Availability**: [Service disruption or resource exhaustion]
- **Business Impact**: [Financial, regulatory, reputational consequences]

**Remediation Guidance**:

1. **Immediate** (0-3 days): [Stopgap mitigation]
2. **Short-term** (1-2 weeks): [Tactical fix]
3. **Long-term** (1-3 months): [Strategic improvement]

**Validation Criteria**:
[Specific tests that should fail after remediation]
- [ ] Original PoC no longer succeeds
- [ ] Variations using different wording also blocked
- [ ] Legitimate use cases still function correctly
- [ ] Monitoring alerts on similar attempts

**References**:
- Wiki: [Link to technical issue page]
- ATLAS: [Link to relevant technique or case study]

---

## Limitations

- [Limitation 1: e.g., "Testing conducted in non-production environment; production-specific configurations may differ"]
- [Limitation 2: e.g., "Results valid only for tested model version and configuration as of [assessment date]"]
- [Limitation 3: e.g., "Time-bounded to [X] weeks; exhaustive testing of all possible variations not feasible"]
- [Limitation 4: e.g., "Social engineering and physical security out of scope"]

---

## Pricing and Timeline

**Base Duration**: [X-Y weeks] ([Z-W business days])

**Effort**: [X-Y person-days]

**Timeline Breakdown**:
- Scoping call and access provisioning: +[X-Y] business days (pre-engagement)
- Testing execution: [X-Y] business days
- Reporting and delivery: +[X-Y] business days
- Optional retest: +[X-Y] business days (post-remediation)

**Pricing**: Contact for quote based on [factors affecting pricing]

**Add-ons Available**:
- **[Add-on 1 Name]** (+[X] days): [Description of what this add-on provides]
- **[Add-on 2 Name]** (+[X] days): [Description]
- **[Add-on 3 Name]** (+[X] days): [Description]

---

## Related Engagements

- **[Engagement 1]**: Choose this for [when to use this instead]. [This engagement] focuses on [what this focuses on] only.
- **[Engagement 2]**: Choose this for [when to use this instead]. [This engagement] focuses on [what this focuses on] only.
- **[Engagement 3]**: Choose this for [when to use this instead]. [This engagement] focuses on [what this focuses on] only.

---

## Getting Started

1. **Review this spec** to confirm it matches your security objectives
2. **Prepare engagement inputs** using checklist above
3. **Check [[methodology|Methodology]]** to understand our trust boundary approach
4. **Explore applicable issues**: [Link to relevant issue pages]
5. **[[contact|Contact us]]** to discuss scoping, timeline, and pricing

---

## Technical References

- [[trust-boundaries|Trust Boundaries Overview]]
- [[issues|Model Issues]] (if applicable)
- [[issues|Data/Knowledge Issues]] (if applicable)
- [[application-agent-runtime|Application/Agent Runtime Issues]] (if applicable)
- [[deployment-governance|Deployment/Governance Issues]] (if applicable)
- [[techniques|MITRE ATLAS Techniques]]
- [[methodology|Methodology]]
