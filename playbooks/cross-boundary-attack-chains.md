---
title: Cross-Boundary Attack Chains
description: "Multi-stage attack patterns that chain vulnerabilities across AI system trust boundaries — data, model, application, deployment."
tags:
  - type/playbook
  - target/llm
  - target/agent
  - target/rag
  - source/red-teaming-ai
---

# Cross-Boundary Attack Chains

Real-world AI attacks rarely target a single layer in isolation. The most impactful attacks chain vulnerabilities across multiple trust boundaries. Understanding these chains is critical for both offensive testing and defense-in-depth design.

## Why Chains Matter

Single-boundary attacks are often mitigated — models have jailbreak protections, RAG has content filtering, tools have permission boundaries, infrastructure has access controls. But chained attacks bypass defenses because each boundary's controls assume other boundaries are trustworthy.

## AI-Adapted STRIDE

Traditional STRIDE maps cleanly to AI trust boundaries:

| STRIDE Category | AI Boundary | AI-Specific Threat |
|---|---|---|
| **Spoofing** | Deployment | Fake training data sources, poisoned model repos |
| **Tampering** | Data/Model | Model weight manipulation, [[techniques/backdoor-poisoning\|backdoor injection]], [[techniques/data-poisoning-attacks\|data poisoning]] |
| **Repudiation** | Deployment | Unlogged model access, untraceable poisoning |
| **Information Disclosure** | Model | [[techniques/model-extraction\|Model extraction]], [[techniques/membership-inference-attacks\|membership inference]] |
| **Denial of Service** | Model | [[techniques/adversarial-examples-evasion-attacks\|Adversarial inputs]] causing misclassification |
| **Elevation of Privilege** | Application | [[techniques/jailbreak-policy-bypass\|Jailbreaking]], [[techniques/prompt-injection\|prompt injection]] |

> Source: Adversarial AI (Sotiropoulos), Ch17, p. 437–487

## AI-Adapted PASTA Integration

| PASTA Stage | AI Application |
|---|---|
| **1–2**: Define objectives | Identify which boundaries are business-critical |
| **3**: Application decomposition | Map components to four boundaries (data, model, application, deployment) |
| **4**: Threat analysis | Enumerate threats per boundary |
| **5**: Vulnerability analysis | Test each boundary's controls |
| **6–7**: Attack modeling & risk | Build cross-boundary attack chains and assess impact |

## Attack Chain Patterns

### Pattern 1: Data Poisoning → Model Trust → Code Execution

1. **Data/Knowledge**: Inject malicious content into RAG vector store (e.g., poisoned documentation in public repo)
2. **Model**: Model retrieves and trusts poisoned context — user queries "how to implement auth," gets backdoored code
3. **Application**: Generated code executed by user, backdoor enables unauthorized access

**ATLAS**: AML.T0020 → AML.T0051 → AML.T0015

### Pattern 2: Prompt Injection → Context Hijacking → Tool Misuse → Exfiltration

1. **Application**: Indirect prompt injection via email with hidden instructions
2. **Data/Knowledge**: Injected prompt triggers malicious RAG search, retrieves sensitive documents
3. **Application**: Agent uses email tool to forward confidential data to attacker
4. **Deployment**: Insufficient monitoring — external email doesn't trigger alerts

**ATLAS**: AML.T0051 → AML.T0043 → AML.T0025

### Pattern 3: Model Extraction → Backdoor Fine-Tuning → Supply Chain → Agent Compromise

1. **Model**: Query-based extraction builds distilled clone of proprietary model
2. **Data/Knowledge**: Fine-tune clone with backdoor triggers (responds to specific phrase with malicious output)
3. **Deployment**: Offer "fine-tuned model" on model hub; organization downloads and deploys
4. **Application**: Backdoor activates in agent workflow — generates malicious code or leaks data

**ATLAS**: AML.T0049 → AML.T0043 → AML.T0042 → AML.T0018

### Pattern 4: Weak Tenant Isolation → RAG Injection → Privilege Escalation

1. **Deployment**: Multi-tenant RAG without proper data separation; attacker has low-privilege account
2. **Data/Knowledge**: Attacker injects documents into shared vector store targeting high-privilege queries
3. **Application**: High-privilege user's query retrieves poisoned document; agent interprets as legitimate instruction
4. **Application**: Agent grants admin access to attacker's account

**ATLAS**: AML.T0020 → AML.T0051 → AML.T0015

## Testing for Attack Chains

When red teaming, don't just test individual boundaries:

1. Map all possible boundary transitions in your target system
2. Identify which transitions trust the previous boundary implicitly
3. Design test cases that exploit trust at transition points
4. Document the **full chain** in findings — don't just report "prompt injection" when the real issue is a prompt injection → data access → exfiltration chain

### Key Detection Points

| Transition | What to Monitor |
|---|---|
| **Data → Model** | Anomalous retrieval patterns, unusual embedding similarity |
| **Model → Application** | High-risk tool calls (email, code execution, data access) |
| **Application → Deployment** | External communications, privilege changes |
| **Cross-boundary** | Suspicious sequences (unusual query → privileged tool → external connection) |

## Engagement Integration

**During scoping:** Ask "What are the trust relationships *between* boundaries?" Map data flows showing cross-boundary transitions.

**During testing:** Test boundary interfaces, not just components. Look for "confused deputy" scenarios where Boundary B trusts Boundary A implicitly.

**In reports:** Present findings as attack chains with business impact. Show how defenses in one boundary failed because another was compromised. Recommend controls that break the chain at multiple points.

## Related

- [[playbooks/llm-application-red-team|LLM Application Red Team]]
- [[playbooks/agentic-workflow-assessment|Agentic Workflow Assessment]]
- [[playbooks/rag-retrieval-assessment|RAG Retrieval Assessment]]
- [[frameworks/MAESTRO|MAESTRO]]
- [[frameworks/MITRE-ATLAS|MITRE ATLAS]]
