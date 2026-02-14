---
title: "OWASP Top 10 for LLM Applications"
tags:
  - type/framework
  - source/generative-ai-security
  - source/ai-native-llm-security
maturity: comprehensive
created: 2026-02-12
updated: 2026-02-14
---

# OWASP Top 10 for LLM Applications

## Overview

The OWASP Top 10 for LLM Applications is a comprehensive risk catalog developed by the Open Web Application Security Project (OWASP) to identify and categorize the most critical security vulnerabilities in Large Language Model (LLM) applications. First released in 2023, the framework has evolved significantly to reflect real-world deployment patterns and emerging threats.

**Maintained by**: OWASP Foundation  
**Current Version**: 2025 (major update from 2023)  
**URL**: https://owasp.org/www-project-top-10-for-large-language-model-applications/

The framework is particularly valuable for:
- Threat modeling LLM applications
- Secure development training
- Penetration testing checklists
- Risk assessment and prioritization
- Compliance demonstration

---

## Structure

The OWASP LLM Top 10 organizes risks into ten categories, ranked by severity and prevalence in production systems. The 2025 update reflects a major shift from standalone LLMs to RAG + Agent ecosystems, introducing new attack vectors and architectural considerations.

### Evolution: 2023 → 2025

| 2023 Version | 2025 Version | Status | Key Changes |
|--------------|--------------|--------|-------------|
| LLM01: Prompt Injection | LLM01:2025 Prompt Injection | Retained | Enhanced focus on indirect attacks + multimedia vectors |
| LLM02: Insecure Output Handling | LLM05:2025 Improper Output Handling | Renamed + repositioned | Emphasis shifted to downstream system validation |
| LLM03: Training Data Poisoning | LLM04:2025 Data and Model Poisoning | Expanded | Now includes RAG poisoning + fine-tuning risks |
| LLM04: Model DoS | LLM10:2025 Unbounded Consumption | Replaced | Broader scope: cost attacks + resource exhaustion |
| LLM05: Supply Chain | LLM03:2025 Supply Chain | **Promoted (#5→#3)** | Increased real-world incidents |
| LLM06: Sensitive Info Disclosure | LLM02:2025 Sensitive Info Disclosure | **Promoted (#6→#2)** | Major data breaches (Samsung, healthcare) |
| LLM07: Insecure Plugin Design | LLM06:2025 Excessive Agency | Merged + expanded | Now covers autonomous agent risks |
| LLM08: Excessive Agency | LLM06:2025 Excessive Agency | Merged | Combined with plugin design risks |
| LLM09: Overreliance | LLM09:2025 Misinformation | Renamed + expanded | Hallucination exploitation, disinfo campaigns |
| LLM10: Model Theft | *(Removed)* | Consolidated | Absorbed into Sensitive Info Disclosure |

**New Additions in 2025**:
- **LLM07:2025 System Prompt Leakage**: Real-world prompt extraction vulnerabilities
- **LLM08:2025 Vector and Embedding Weaknesses**: Rise of RAG architectures creating new attack vectors

---

## Key Components

### Risk Categorization Framework

**AI-Native LLM Security** (Malik, Huang, Dawson, 2025) presents a five-area categorization framework for understanding OWASP Top 10 LLM risks:

#### 1. Injection Flaws
- **Prompt Injection (LLM01)**: Direct and indirect manipulation of model behavior
- **Data Poisoning (LLM04)**: Training data corruption and RLHF preference poisoning
- **Model Manipulation**: Fine-tuning attacks, adversarial examples, backdoor installation

**Core vulnerability**: LLMs cannot distinguish between legitimate instructions and malicious inputs

---

#### 2. Broken Authentication and Session Management
- **Insecure Plugin Design (LLM06)**: Weak authentication between LLM and plugins
- **Confused Deputy Problem**: Plugins acting with user's OAuth tokens without proper authorization
- **Cross-Plugin Request Forgery**: Malicious prompts triggering unintended plugin actions

**Core vulnerability**: Stateless LLM interactions complicate traditional authentication; third-party plugins often over-privileged

---

#### 3. Sensitive Data Exposure
- **Training Data Leakage (LLM02)**: Model memorization enabling verbatim reproduction of training data
- **Model Inversion**: Attackers reconstructing sensitive information from model outputs
- **Chat History Extraction**: Exploiting context windows and caching mechanisms

**Core vulnerability**: LLMs cannot distinguish public from private information; complex architectures make auditing difficult

---

#### 4. Broken Access Control
- **Excessive Agency (LLM06)**: LLMs acting beyond intended scope/authority
- **Model Theft (absorbed into LLM02)**: API-based extraction of model knowledge and capabilities
- **Privilege Escalation**: Language-driven execution bypassing authorization checks

**Core vulnerability**: Difficult to precisely define LLM action boundaries; creative problem-solving bypasses restrictions

---

#### 5. Security Misconfigurations in Deployment
- **Insecure Output Handling (LLM05)**: Lack of output sanitization enabling XSS, injection attacks
- **Unbounded Consumption (LLM10)**: Resource exhaustion through prompt engineering or sponge examples
- **Supply Chain Vulnerabilities (LLM03)**: Compromised dependencies
- **Misinformation (LLM09)**: Excessive trust in LLM outputs without verification

**Core vulnerability**: Deployment complexity creates configuration gaps; integration with downstream systems multiplies attack surface

---

### New Attack Vector Categories (2025)

#### RAG-Specific Attacks
- Vector database manipulation
- Embedding inversion attacks
- Retrieval poisoning
- Context injection through knowledge bases

**Related**: [[techniques/rag-data-poisoning]], [[techniques/retrieval-manipulation]]

#### Agent-Based Threats
- Tool permission abuse
- Autonomous decision-making exploitation
- Multi-step attack orchestration
- Privilege escalation through agent chains

**Related**: [[techniques/agent-goal-hijack]], [[techniques/tool-privilege-escalation]]

#### Economic Attacks
- Cost-based denial of service (Denial of Wallet)
- Resource consumption manipulation
- Token exhaustion strategies
- Financial impact exploitation

**Related**: [[techniques/denial-of-wallet]]

---

## Integration Points

### OWASP GenAI Red Teaming Guide

The OWASP GenAI Red Teaming Guide (2025) structures assessments into four evaluation phases:

1. **Model**: Alignment, robustness, bias testing, MDLC security (model provenance, malware injection, data pipeline security)
2. **Implementation**: Bypassing guardrails (system prompts), RAG poisoning, testing controls like model firewalls/proxies
3. **System**: Exploitation of non-model components, supply chain vulnerabilities, standard red teaming of hosting infrastructure
4. **Runtime**: Business process failures, multi-agent interaction security, over-reliance, social engineering

**Quick scope questions**:
- Which applications are business-critical or touch sensitive data?
- Are we evaluating model behavior, app integration, system/pipeline, or runtime behavior?
- What threat categories are top priority (prompt injection, data leakage, RAG poisoning, agent/tool risks, bias/harms)?

---

### Framework Mappings

**MITRE ATLAS Integration**:
- **Tactics**: High-level adversary objectives (e.g., AML.TA0001: AI Attack Staging)
- **Techniques**: Specific attack methods (e.g., AML.T0051: LLM Prompt Injection)
- **Case Studies**: Real-world incidents demonstrating pattern

**NIST AI RMF Alignment**:
- **MEASURE**: Demonstrated gaps in robustness, security, resilience
- **MANAGE**: Evidence for risk treatment decisions
- **MAP**: Expanded understanding of system risks and attack surface

**Application-Level Integration**:
Reports include framework references for each finding, enabling traceability to recognized attack patterns and compliance demonstration.

---

### Vault Integration

**Techniques Library**:
- [[techniques/prompt-injection|Prompt Injection (LLM01)]]
- [[techniques/sensitive-info-disclosure|Sensitive Information Disclosure (LLM02)]]
- [[techniques/supply-chain-model-poisoning|Supply Chain Compromise (LLM03)]]
- [[techniques/data-poisoning-attacks|Data and Model Poisoning (LLM04)]]
- [[techniques/insufficient-output-encoding|Improper Output Handling (LLM05)]]
- [[techniques/agent-goal-hijack|Excessive Agency (LLM06)]]
- [[techniques/system-prompt-leakage|System Prompt Leakage (LLM07)]]
- [[techniques/vector-embedding-weaknesses|Vector and Embedding Weaknesses (LLM08)]]
- [[techniques/overreliance-on-model-outputs|Misinformation (LLM09)]]
- [[techniques/denial-of-wallet|Unbounded Consumption (LLM10)]]

**Playbooks**:
- [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]]
- [[playbooks/llm-application-red-team|LLM Application Red Team]]
- [[playbooks/rag-retrieval-assessment|RAG Security Assessment]]

**Mitigations**:
- [[mitigations/input-validation-patterns|Input Validation]]
- [[mitigations/output-filtering-and-sanitization|Output Filtering]]
- [[mitigations/access-segmentation-and-rbac|Access Segmentation]]
- [[mitigations/llm-gateway-architecture|LLM Gateway Architecture]]

---

## Sources

> "Traditional frameworks, with their focus on predictable execution paths and data flows, struggle to model this inherent unpredictability. They lack the mechanisms to anticipate emergent behavior that can result from the complex interactions between agents, their environment, and the non-deterministic outputs of LLMs."
> — [[sources/bibliography#AI-Native LLM Security]], p. 125-126

> "The fundamental shift driving these changes: Primary Architecture 2023 Focus → 2025 Focus: Standalone LLMs → RAG + Agent Ecosystems"
> — [[sources/bibliography#AI-Native LLM Security]], p. 339-355

> "OWASP mapping details enable traceability: Link vulnerabilities to recognized attack patterns, demonstrate coverage for NIST AI RMF and regulatory requirements, cross-reference with industry-standard taxonomies."
> — [[sources/bibliography#Red-Teaming AI]], OWASP GenAI Red Teaming Guide
