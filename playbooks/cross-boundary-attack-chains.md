---
title: "Cross-Boundary Attack Chains"
tags:
  - type/playbook
  - target/llm
  - target/agent
  - target/rag
  - source/red-teaming-ai
maturity: reviewed
created: 2026-02-14
updated: 2026-02-14
---

# Cross-Boundary Attack Chains

## Overview

Real-world AI attacks rarely target a single layer in isolation. The most impactful attacks chain vulnerabilities across multiple trust boundaries. Understanding these chains is critical for both offensive testing and defense-in-depth design.

Single-boundary attacks are often mitigated—models have jailbreak protections, RAG has content filtering, tools have permission boundaries, infrastructure has access controls. But chained attacks bypass defenses because each boundary's controls assume other boundaries are trustworthy.

## Methodology

### Attack Chain Analysis

When red teaming, test boundary interfaces, not just components:

1. Map all possible boundary transitions in the target system
2. Identify which transitions trust the previous boundary implicitly
3. Design test cases that exploit trust at transition points
4. Document the **full chain** in findings—not just individual vulnerabilities

### Key Detection Points

| Transition | What to Monitor |
|---|---|
| **Data → Model** | Anomalous retrieval patterns, unusual embedding similarity |
| **Model → Application** | High-risk tool calls (email, code execution, data access) |
| **Application → Deployment** | External communications, privilege changes |
| **Cross-boundary** | Suspicious sequences (unusual query → privileged tool → external connection) |

## Techniques Covered

| ID | Technique | Relevance |
|----|-----------|-----------|
| AML.T0020 | [[techniques/rag-data-poisoning\|Data Poisoning]] | Knowledge base/training data manipulation |
| AML.T0051 | [[techniques/prompt-injection\|Prompt Injection]] | Instruction hijacking across boundaries |
| AML.T0043 | Backdoor Injection | Model weight manipulation |
| AML.T0049 | [[techniques/model-extraction\|Model Extraction]] | API-based theft |
| AML.T0015 | Tool Misuse | Unauthorized API/tool invocation |
| AML.T0025 | Exfiltration | Data theft via compromised boundaries |

## Attack Chain Patterns

### Pattern 1: Data Poisoning → Model Trust → Code Execution

1. **Data/Knowledge**: Inject malicious content into RAG vector store (e.g., poisoned documentation in public repo)
2. **Model**: Model retrieves and trusts poisoned context—user queries "how to implement auth," gets backdoored code
3. **Application**: Generated code executed by user, backdoor enables unauthorized access

**ATLAS**: AML.T0020 → AML.T0051 → AML.T0015

---

### Pattern 2: Prompt Injection → Context Hijacking → Tool Misuse → Exfiltration

1. **Application**: Indirect prompt injection via email with hidden instructions
2. **Data/Knowledge**: Injected prompt triggers malicious RAG search, retrieves sensitive documents
3. **Application**: Agent uses email tool to forward confidential data to attacker
4. **Deployment**: Insufficient monitoring—external email doesn't trigger alerts

**ATLAS**: AML.T0051 → AML.T0043 → AML.T0025

---

### Pattern 3: Model Extraction → Backdoor Fine-Tuning → Supply Chain → Agent Compromise

1. **Model**: Query-based extraction builds distilled clone of proprietary model
2. **Data/Knowledge**: Fine-tune clone with backdoor triggers (responds to specific phrase with malicious output)
3. **Deployment**: Offer "fine-tuned model" on model hub; organization downloads and deploys
4. **Application**: Backdoor activates in agent workflow—generates malicious code or leaks data

**ATLAS**: AML.T0049 → AML.T0043 → AML.T0042 → AML.T0018

---

### Pattern 4: Weak Tenant Isolation → RAG Injection → Privilege Escalation

1. **Deployment**: Multi-tenant RAG without proper data separation; attacker has low-privilege account
2. **Data/Knowledge**: Attacker injects documents into shared vector store targeting high-privilege queries
3. **Application**: High-privilege user's query retrieves poisoned document; agent interprets as legitimate instruction
4. **Application**: Agent grants admin access to attacker's account

**ATLAS**: AML.T0020 → AML.T0051 → AML.T0015

## Tooling

### Chain Testing

**Approach**: End-to-end adversarial sequences testing multiple boundaries

**Tools**:
- **PyRIT**: Multi-turn campaigns with cross-boundary exploitation
- **Custom test harnesses**: Domain-specific chain validation
- **MITRE Caldera**: Automated adversarial emulation (adapted for AI systems)

### Detection

**Monitoring**:
- Correlation across boundary transitions
- Anomaly detection on multi-step sequences
- SIEM integration for cross-boundary event correlation

## Deliverables

1. **Attack Chain Documentation**
   - Full chain diagrams showing boundary transitions
   - Step-by-step exploitation narratives
   - Detection and prevention recommendations per boundary

2. **Defense-in-Depth Recommendations**
   - Controls that break chains at multiple points
   - Boundary-specific mitigations
   - Monitoring and alerting improvements

## Sources

> AI-adapted STRIDE and PASTA threat modeling for attack chain analysis.
> — [[sources/bibliography#Adversarial AI]], Ch17, p. 437-487

> Cross-boundary attack patterns in AI systems.
> — [[sources/bibliography#Red Teaming AI]]

> Framework mapping for multi-stage AI attacks.
> — [[frameworks/atlas/MITRE-ATLAS|MITRE ATLAS Framework]]
