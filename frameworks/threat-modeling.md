---
title: "AI Threat Modeling Framework"
tags:
  - type/framework
  - target/llm
  - target/ml-model
  - target/generative-ai
  - source/adversarial-ai
  - source/developers-playbook-llm
maturity: comprehensive
created: 2026-02-14
updated: 2026-02-14
---

# AI Threat Modeling Framework

## Overview

A structured approach to identifying, assessing, and mitigating threats to AI systems using secure-by-design principles, industry taxonomies (NIST, MITRE ATLAS, OWASP), and risk-based prioritization. This framework extends traditional cybersecurity threat modeling to incorporate AI-specific risks including data poisoning, evasion attacks, model extraction, and generative AI threats.

**Purpose**: Move from lab-based, research-driven adversarial AI toward a system-based, risk-oriented approach that incorporates adversarial AI research into proven enterprise security methodologies.

**Key Stakeholders**: Security architects, AI/ML engineers, data scientists, risk managers, compliance teams

> "Move from lab-based, research-driven adversarial AI toward a system-based, risk-oriented approach that incorporates adversarial AI research into proven enterprise security methodologies."
> — [[sources/bibliography#Adversarial AI]], p. 438-440

---

## Structure

The framework is organized around the Secure by Design AI (SbD AI) methodology, which extends traditional secure-by-design approaches to incorporate adversarial AI. The methodology operates as an iterative cycle with six core activities:

### Core Activities (Iterative Cycle)

1. **Threat Modeling**
2. **Security Design**
3. **Secure Implementation**
4. **Testing and Verification**
5. **Deployment**
6. **Live Operations**

Each activity informs and refines the others in a continuous improvement loop triggered by new requirements, lessons learned, and incident findings.

---

## Key Components

### 1. AI Threat Library

A comprehensive threat dictionary organized into four categories, mapped to industry taxonomies:

#### Category 1: Traditional Cybersecurity Threats

| ID | Threat | Stage | Description |
|----|--------|-------|-------------|
| T01 | Data Leak | Dev, Prod | Unauthorized access and exfiltration of sensitive data |
| T02 | Tampering | Dev, Prod | Malicious change of data or artifacts |
| T03 | Malware Attacks | Dev, Prod | Use of malware to gain control, damage, exfiltrate data |
| T04 | Privilege Escalation | Dev, Prod | Gaining unauthorized access via broken access control |
| T05 | Denial of Service | Dev, Prod | Disrupt system use via excessive usage |

> **Note:** AI development environments are production-like due to reliance on live data, elevating traditional cyber risks.

---

#### Category 2: Adversarial AI Attacks (General)

| ID | Threat | Stage | Related Technique |
|----|--------|-------|-------------------|
| T06 | Data Poisoning | Dev | [[techniques/data-poisoning-attacks\|Data Poisoning]] |
| T07 | Backdoor Attacks | Dev | [[techniques/backdoor-poisoning\|Backdoor Poisoning]] |
| T08 | Model Tampering | Dev, Prod | [[techniques/model-tampering\|Model Tampering]] |
| T09 | Model Theft | Dev, Prod | [[techniques/model-theft\|Model Theft]] |
| T10 | Evasion | Prod | [[techniques/adversarial-examples-evasion-attacks\|Evasion Attacks]] |
| T11 | Model Reprogramming | Prod | [[techniques/model-reprogramming\|Model Reprogramming]] |
| T12 | Model Extraction | Prod | [[techniques/model-extraction\|Model Extraction]] |
| T13 | Model Inversion | Prod | [[techniques/model-inversion-attacks\|Model Inversion]] |
| T14 | Membership Inference | Prod | [[techniques/membership-inference\|Membership Inference]] |
| T15 | Attribute Inference | Prod | [[techniques/attribute-inference\|Attribute Inference]] |
| T16 | ML DoS | Prod | [[techniques/ml-dos\|ML Denial of Service]] |

---

#### Category 3: Generative AI-Specific Threats

| ID | Threat | Stage | Related Technique |
|----|--------|-------|-------------------|
| T17 | Direct Prompt Injection | Prod | [[techniques/prompt-injection\|Prompt Injection]] |
| T18 | Indirect Prompt Injection | Prod | [[techniques/prompt-injection#indirect-prompt-injection\|Indirect Prompt Injection]] |
| T19 | Data Disclosure | Prod | [[techniques/sensitive-info-disclosure\|Sensitive Info Disclosure]] |
| T20 | Insecure Output | Prod | [[techniques/insufficient-output-encoding\|Insecure Output Handling]] |
| T21 | Excessive Agency | Prod | [[techniques/agent-goal-hijack\|Excessive Agency]] |
| T22 | Agent Hijacking | Prod | [[techniques/agent-goal-hijack\|Agent Goal Hijacking]] |
| T23 | Training Data Extraction | Prod | [[techniques/training-data-memorization\|Training Data Extraction]] |
| T24 | Model Replication | Prod | [[techniques/model-extraction-llm\|Model Replication]] |
| T25 | AI Misuse/Abuse | Prod | [[techniques/ai-misuse\|AI Misuse]] |

---

#### Category 4: Supply Chain Threats

| ID | Threat | Stage | Related Technique |
|----|--------|-------|-------------------|
| T26 | Supply Chain Model Compromise | Dev, Prod | [[techniques/supply-chain-model-poisoning\|Supply Chain Model Compromise]] |
| T27 | Supply Chain Data Compromise | Dev, Prod | [[techniques/supply-chain-data-poisoning\|Supply Chain Data Compromise]] |
| T28 | Supply Chain Package Vulnerability | Dev, Prod | [[techniques/supply-chain-package-vulnerability\|Package Vulnerabilities]] |
| T29 | Supply Chain System Compromise | Dev, Prod | [[techniques/supply-chain-system-compromise\|System Compromise]] |

---

### 2. Trust Boundaries

Trust boundaries delineate where security controls and policies are enforced, separating trusted zones from untrusted ones.

#### LLM Application Trust Boundaries

Five primary trust boundaries in LLM applications:

**Trust Boundary 1: The Model**
- Public API models (data exits secure network) vs. privately hosted models (data remains internal)
- Risks: Sensitive data exposure, supply chain compromise, maintenance burden

**Trust Boundary 2: User Interaction**
- Bidirectional: input (user → LLM) and output (LLM → user)
- Risks: Prompt injection, toxic content generation, data leakage

**Trust Boundary 3: Training Data**
- Internally sourced (controlled environment) vs. external/public data (porous boundary)
- Risks: Data poisoning, bias, PII leakage

**Trust Boundary 4: Live External Data Sources**
- Real-time information from web, APIs, databases
- Risks: Indirect prompt injection, malware conduits, unauthorized access

**Trust Boundary 5: Internal Services**
- Databases, internal APIs, backend systems
- Risks: False sense of security, unauthorized access, privilege escalation

> "In application security, a trust boundary serves as an invisible, yet crucial, demarcation line that separates different components or entities based on their level of trustworthiness."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 19

---

### 3. Risk Assessment and Prioritization

#### Risk Calculation

**Basic Formula**:
```
Risk Score = Likelihood × Impact
```

**Qualitative Levels** (Recommended):
- **Levels**: LOW, MEDIUM, HIGH, CRITICAL (or CERTAIN for likelihood)

**Risk Matrix**:

| Likelihood | Low Impact | Medium Impact | High Impact | Critical Impact |
|------------|-----------|---------------|-------------|-----------------|
| Low | Low | Low | Medium | Medium |
| Medium | Low | Medium | Medium | High |
| High | Medium | Medium | High | High |
| Certain | Medium | High | High | Critical |

**Risk Level Meanings**:
- **Low**: Monitor, no immediate action needed
- **Medium**: Moderate concern, may require specific management plans
- **High**: Serious concern, requires immediate attention
- **Critical**: Highest priority, demands immediate action

#### Types of Risk

- **Inherent Risk**: Risk before applying mitigations (used to prioritize threats)
- **Residual Risk**: Risk after applying mitigations (used to assess acceptability)

**Contextual Approach**: Risk prioritization at odds with threat volume. Just because something is possible or likely doesn't mean it must be mitigated if impact and overall risk aren't judged important by the risk owner.

---

### 4. Security Controls Mapping

Map mitigations to **standardized security controls** using industry frameworks:
- **CIS Controls**: Platform security
- **OWASP ASVS**: Application security
- **OWASP AI Exchange**: AI-specific security controls
- **MITRE ATLAS Mitigations**: AI-specific defensive measures

**Benefits**:
- Verifiable security design
- Understood by architects and engineers
- Supported by tools
- Easier integration into enterprise security programs

---

## Integration Points

### Industry Taxonomy Mappings

#### NIST AI 100-2 E2023

**Focus**: Comprehensive catalog of attacks and mitigations based on published research (98 pages, research-oriented)

**Key Differences**:
- More focused on pure adversarial AI attacks
- Detailed subcategories (availability poisoning, backdoor poisoning, targeted poisoning, clean label poisoning)
- Merges model inversion and training data extraction into "Data Reconstruction"
- Less coverage of emerging GenAI agent threats

**Use Case**: Delve deeper into research references for specific threats and attacks

**URL**: https://csrc.nist.gov/pubs/ai/100/2/e2023/final

---

#### OWASP AI Exchange

**Focus**: Emphasis on enterprise security with mature security controls integrated into organizational security programs

**Key Features**:
- Work in progress but emphasizes life cycle coverage
- More descriptive threat names for better readability
- Comprehensive set of controls easily integrated into enterprise security
- Covers non-adversarial attacks under data leak entries

**Use Case**: Identify security controls for threats and integrate into organizational security programs

**URL**: https://owaspai.org

---

#### MITRE ATLAS

**Focus**: Tactics, Techniques, and Procedures (TTPs) model showing attacker kill chain

**Key Features**:
- First-level practices reflect generic attack kill chain
- Uses industry-standard STIX format (integrates with threat intelligence tools)
- Maps threats to specific techniques showing how attackers realize threats

**ATLAS Tactics (15 total)**:
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

**Use Case**: Understand attacker steps, identify vulnerable solution parts, assess exploitability and likelihood

**URL**: https://atlas.mitre.org

---

### Related Frameworks

- [[frameworks/MAESTRO|MAESTRO Framework]]: Multi-layered AI security framework for agentic systems
- [[frameworks/STRATEGEMS|STRATEGEMS Framework]]: Strategic AI security methodology
- [[frameworks/OWASP-top-10-LLM|OWASP Top 10 for LLM Applications]]: Critical vulnerabilities in LLM applications
- [[frameworks/frontier-model-security|Frontier Model Security Framework]]: Security for frontier models

### Related Playbooks

- [[playbooks/rag-retrieval-assessment|RAG Security Assessment]]: RAG-specific threat modeling
- [[playbooks/ai-supply-chain-security|AI Supply Chain Security]]: Supply chain threat modeling
- [[playbooks/llm-application-red-team|LLM Red Teaming]]: Threat validation through red teaming

### Related Mitigations

- [[mitigations/owasp-llm-mitigation-strategies|OWASP LLM Mitigations]]: Control mappings
- [[mitigations/rag-access-control|RAG Access Control]]: Trust boundary controls
- [[mitigations/input-validation-patterns|Input Validation]]: Trust boundary 2 controls

---

## Sources

> "Move from lab-based, research-driven adversarial AI toward a system-based, risk-oriented approach that incorporates adversarial AI research into proven enterprise security methodologies."
> — [[sources/bibliography#Adversarial AI]], p. 438-440

> "The SbD AI methodology is based on joint guidelines developed by UK NCSC and US CISA, extending traditional secure-by-design approaches to incorporate adversarial AI and emphasizing security integration from the earliest stages of development."
> — [[sources/bibliography#Adversarial AI]], p. 439-440

> "In application security, a trust boundary serves as an invisible, yet crucial, demarcation line that separates different components or entities based on their level of trustworthiness. These boundaries delineate areas where data or control flow changes from one level of trust to another."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 19

> "Understanding trust boundaries is critical to threat modeling. Properly defining and recognizing these boundaries can be the difference between a secure system and one vulnerable to threats."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 19

> "This is a cycle, not a linear sequence. New requirements, continuous improvement, and lessons learned from incidents trigger iteration through all steps."
> — [[sources/bibliography#Adversarial AI]], p. 440

> "Adopt a contextual and risk-based attitude: Not all threats are equally applicable or important. Research paper demonstrations don't always translate to easily exploitable attacks. Avoid 'gold plating' by uncritically implementing all possible mitigations."
> — [[sources/bibliography#Adversarial AI]], p. 438
