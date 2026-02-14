---
tags:
  - source/generative-ai-security
  - needs-review
  - owasp
  - source/ai-native-llm-security
  - trust-boundary/general
  - type/methodology
created: 2026-02-12
source: [[sources/bibliography#AI-Native LLM Security]]
pages: "339-355"
---
# OWASP Top 10 for LLM Applications: 2023 → 2025 Evolution

**Timeline:**
- 2023: First release (Version 1.0)
- 2024: Version 1.1 (minor clarifications)
- 2025: Major update reflecting real-world deployment + emerging threats

## Complete Version Mapping

| 2023 Version                     | 2025 Version                         | Status                 | Key Changes                                             |
| -------------------------------- | ------------------------------------ | ---------------------- | ------------------------------------------------------- |
| LLM01: Prompt Injection          | LLM01:2025 Prompt Injection          | Retained               | Enhanced focus on indirect attacks + multimedia vectors |
| LLM02: Insecure Output Handling  | LLM05:2025 Improper Output Handling  | Renamed + repositioned | Emphasis shifted to downstream system validation        |
| LLM03: Training Data Poisoning   | LLM04:2025 Data and Model Poisoning  | Expanded               | Now includes RAG poisoning + fine-tuning risks          |
| LLM04: Model DoS                 | LLM10:2025 Unbounded Consumption     | Replaced               | Broader scope: cost attacks + resource exhaustion       |
| LLM05: Supply Chain              | LLM03:2025 Supply Chain              | **Promoted (#5→#3)**   | Increased real-world incidents                          |
| LLM06: Sensitive Info Disclosure | LLM02:2025 Sensitive Info Disclosure | **Promoted (#6→#2)**   | Major data breaches (Samsung, healthcare)               |
| LLM07: Insecure Plugin Design    | LLM06:2025 Excessive Agency          | Merged + expanded      | Now covers autonomous agent risks                       |
| LLM08: Excessive Agency          | LLM06:2025 Excessive Agency          | Merged                 | Combined with plugin design risks                       |
| LLM09: Overreliance              | LLM09:2025 Misinformation            | Renamed + expanded     | Hallucination exploitation, disinfo campaigns           |
| LLM10: Model Theft               | *(Removed)*                          | Consolidated           | Absorbed into Sensitive Info Disclosure                 |

## Risk Categorization Framework (Chapter 7 Deep Dive)

AI-Native LLM Security (Malik, Huang, Dawson, 2025) presents a five-area categorization framework for understanding OWASP Top 10 LLM risks:

### 1. Injection Flaws
- **Prompt Injection (LLM01)** — Direct and indirect manipulation of model behavior
- **Data Poisoning (LLM03)** — Training data corruption and RLHF preference poisoning
- **Model Manipulation** — Fine-tuning attacks, adversarial examples, backdoor installation

**Core vulnerability:** LLMs cannot distinguish between legitimate instructions and malicious inputs, and training on compromised data embeds vulnerabilities.

### 2. Broken Authentication and Session Management
- **Insecure Plugin Design (LLM07)** — Weak authentication between LLM and plugins
- **Confused Deputy Problem** — Plugins acting with user's OAuth tokens without proper authorization
- **Cross-Plugin Request Forgery** — Malicious prompts triggering unintended plugin actions

**Core vulnerability:** Stateless LLM interactions complicate traditional authentication; third-party plugins often over-privileged.

### 3. Sensitive Data Exposure
- **Training Data Leakage (LLM06)** — Model memorization enabling verbatim reproduction of training data
- **Model Inversion** — Attackers reconstructing sensitive information from model outputs
- **Chat History Extraction** — Exploiting context windows and caching mechanisms

**Core vulnerability:** LLMs cannot distinguish public from private information; complex architectures make auditing difficult.

### 4. Broken Access Control
- **Excessive Agency (LLM08)** — LLMs acting beyond intended scope/authority
- **Model Theft (LLM10)** — API-based extraction of model knowledge and capabilities
- **Privilege Escalation** — Language-driven execution bypassing authorization checks

**Core vulnerability:** Difficult to precisely define LLM action boundaries; creative problem-solving bypasses restrictions.

### 5. Security Misconfigurations in Deployment
- **Insecure Output Handling (LLM02)** — Lack of output sanitization enabling XSS, injection attacks
- **Model DoS (LLM04)** — Resource exhaustion through prompt engineering or sponge examples
- **Supply Chain Vulnerabilities (LLM05)** — Compromised dependencies (redis-py incident)
- **Overreliance (LLM09)** — Excessive trust in LLM outputs without verification

**Core vulnerability:** Deployment complexity creates configuration gaps; integration with downstream systems multiplies attack surface.

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 125-126

## New Additions in 2025

| Entry | Position | Rationale |
|-------|----------|-----------|
| **[[techniques/system-prompt-leakage|System Prompt Leakage]]** (LLM07:2025) | #7 | Real-world prompt extraction vulnerabilities |
| **[[techniques/vector-embedding-weaknesses|Vector and Embedding Weaknesses]]** (LLM08:2025) | #8 | Rise of RAG architectures creating new attack vectors |

## Architecture Evolution

The fundamental shift driving these changes:

| Aspect | 2023 Focus | 2025 Focus |
|--------|-----------|-----------|
| Primary Architecture | Standalone LLMs | RAG + Agent Ecosystems |
| Data Storage | Model weights | Vector databases + Knowledge bases |
| Integration Pattern | API endpoints | Tool-calling agents |
| Primary Threat Vector | Direct input manipulation | Multi-stage attacks |

## New Attack Vector Categories

### RAG-Specific Attacks
- Vector database manipulation
- Embedding inversion attacks
- Retrieval poisoning
- Context injection through knowledge bases

### Agent-Based Threats
- Tool permission abuse
- Autonomous decision-making exploitation
- Multi-step attack orchestration
- Privilege escalation through agent chains

### Economic Attacks
- Cost-based denial of service (Denial of Wallet)
- Resource consumption manipulation
- Token exhaustion strategies
- Financial impact exploitation

## Emerging Multi-Vector Attack Patterns

The 2025 update recognizes sophisticated attack chains:
- **Prompt Injection + RAG Poisoning:** Precision targeting through compromised knowledge bases
- **System Prompt Leakage + Excessive Agency:** Credential extraction → unauthorized actions
- **Supply Chain + Data Poisoning:** Long-term compromise through trusted components

## Implementation Guidance

### Immediate Actions (for orgs using 2023 guidelines)
- Review/update risk assessments for promoted categories
- Implement vector database security controls
- Establish agent permission frameworks
- Deploy system prompt protection measures

### Strategic Updates
- Expand supply chain verification programs
- Implement cost monitoring and rate limiting
- Develop RAG-specific security controls
- Create agent oversight mechanisms

## Related Notes

- [[techniques/system-prompt-leakage|System Prompt Leakage (LLM07:2025)]]
- [[techniques/vector-embedding-weaknesses|Vector and Embedding Weaknesses (LLM08:2025)]]
- [[techniques/agentic-ai-security-risks-owasp-aivss|OWASP AIVSS Agentic AI Risks]]
- [[techniques/prompt-injection|Prompt Injection and LLM Manipulation]]
- [[techniques/data-poisoning-attacks|Data Poisoning Attacks]]
- AI Security Landscape

> Source: [[sources/bibliography#AI-Native LLM Security]], pp. 339-355

---

## OWASP GenAI Red Teaming — Phased Evaluation

The OWASP GenAI Red Teaming Guide (2025) structures assessments into four evaluation phases, each with distinct targets:

1. **Model** — Alignment, robustness, bias testing, MDLC security (model provenance, malware injection, data pipeline security). Deliverables: adversarial robustness assessment, defensive mechanism evaluation, ethics/bias analysis.
2. **Implementation** — Bypassing guardrails (system prompts), RAG poisoning, testing controls like model firewalls/proxies.
3. **System** — Exploitation of non-model components, supply chain vulnerabilities, standard red teaming of hosting infrastructure and data stores.
4. **Runtime** — Business process failures, multi-agent interaction security, over-reliance, social engineering, downstream impact on consumers of generated content.

**Quick scope questions (OWASP-aligned):**
- Which applications are business-critical or touch sensitive data?
- Are we evaluating model behavior, app integration, system/pipeline, or runtime behavior?
- What threat categories are top priority (prompt injection, data leakage, RAG poisoning, agent/tool risks, bias/harms)?

> Source: [[sources/bibliography#Red-Teaming AI]], OWASP GenAI Red Teaming Guide (2025)

## Framework Cross-References

## Mapping Approach

Every finding is tagged with framework identifiers enabling:
- **Traceability**: Link vulnerabilities to recognized attack patterns
- **Compliance**: Demonstrate coverage for NIST AI RMF, regulatory requirements
- **Threat intelligence**: Cross-reference with industry-standard taxonomies

## Frameworks Used

### MITRE ATLAS (Primary Attack Taxonomy)

Findings mapped to:
- **Tactics**: High-level adversary objectives (e.g., AML.TA0001: AI Attack Staging)
- **Techniques**: Specific attack methods (e.g., AML.T0051: LLM Prompt Injection)
- **Case Studies**: Real-world incidents demonstrating pattern

[[frameworks/atlas/MITRE-ATLAS|Browse ATLAS reference]] | [[frameworks/atlas/tactics|Tactics overview]] | [[frameworks/atlas/techniques|Techniques catalog]]

### OWASP LLM Top 10 (Risk Framework)

Findings categorized by Top 10 risk classes:
- LLM01: Prompt Injection
- LLM02: Sensitive Information Disclosure
- LLM03-04: Data and Model Poisoning
- LLM05: Supply Chain Vulnerabilities
- LLM06: Excessive Agency
- LLM07: System Prompt Leakage
- LLM08: Vector and Embedding Weaknesses
- LLM09: Misinformation
- LLM10: Unbounded Consumption

OWASP mapping details in issue pages

### NIST AI RMF (Compliance Framework)

Findings inform GOVERN/MAP/MEASURE/MANAGE functions:
- **MEASURE**: Demonstrated gaps in robustness, security, resilience
- **MANAGE**: Evidence for risk treatment decisions
- **MAP**: Expanded understanding of system risks and attack surface

Reports include gap analysis showing which RMF controls are validated vs. not-tested.

## Framework Integration in Reports

Each finding includes "Framework References" section with specific IDs. Compliance matrix shows:
- Coverage: Which framework items were tested
- Gaps: Items out of scope for engagement
- Findings distribution: Heat map of vulnerabilities per framework category

**Does not duplicate framework content**: Links to docs taxonomy for technical depth; reports focus on findings and mapping only.
