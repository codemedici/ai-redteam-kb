---
title: "LLM Application Red Team"
tags:
  - type/playbook
  - target/llm
  - target/agent
  - source/developers-playbook-llm
maturity: reviewed
created: 2026-02-14
updated: 2026-02-14
---

# LLM Application Red Team

## Overview

A focused adversarial assessment targeting application-layer vulnerabilities in LLM-powered systems. Testing concentrates on prompt injection attacks (direct and indirect), jailbreak techniques for bypassing safety controls, tool misuse and privilege escalation in agentic workflows, and data exfiltration through model outputs or tool invocations.

The engagement validates whether application-level defenses can withstand realistic attacker techniques and identifies exploitable gaps before production exposure. Real-world incidents including ChatGPT conversation exfiltration, Slack AI data theft via indirect prompt injection, and Copilot Studio agent misuse demonstrate that prompt injection combined with tool access enables systematic data exfiltration and unauthorized actions in production systems.

## Scope & Prerequisites

### In Scope

- **Chat Applications**: Customer service bots, internal assistants, conversational interfaces
- **Copilot Integrations**: IDE assistants, code completion tools, developer productivity tools
- **Document Q&A Systems**: RAG-backed applications (basic retrieval security only)
- **Tool-Calling Applications**: LLMs with function/API access, MCP servers, plugin ecosystems

### Out of Scope

- Training pipeline security and data provenance (see [[playbooks/ai-supply-chain-security|AI Supply Chain Security]])
- Deep RAG poisoning and embedding manipulation (see [[playbooks/rag-retrieval-assessment|RAG & Retrieval Assessment]])
- Multi-agent coordination attacks (see [[playbooks/agentic-workflow-assessment|Agentic Workflow Assessment]])
- Infrastructure pentesting beyond AI components

### Prerequisites

- API access with read/write permissions in non-production environment
- Minimum 3 test accounts with varying permission levels
- System architecture documentation showing LLM integration and data flows
- Model information (provider, version, fine-tuning status)
- Tool/function inventory with permission model and schemas
- Sample conversation logs
- Policy documentation (content policies, safety guidelines)

## Methodology

### Phase 1: Reconnaissance and System Profiling

**Objectives**: Map attack surface, identify tool access, understand prompt assembly patterns

**Activities**:
- Document available endpoints, tool functions, and permission model
- Probe for system prompt leakage using extraction techniques
- Identify conversation memory and context persistence mechanisms
- Map user roles and access control boundaries
- Test for basic input validation and output filtering

**Evidence Collected**:
- System architecture diagram (inferred from testing)
- Tool/function inventory with permission mappings
- System prompt (if extractable)
- Baseline responses for comparison

### Phase 2: Prompt Injection and Jailbreak Campaign

**Objectives**: Validate instruction hierarchy and safety guardrail effectiveness

**Activities**:
- **Direct Injection**: Meta-instructions attempting to override system behavior
- **Role Confusion**: Manipulate perceived identity or privilege level
- **Instruction Smuggling**: Embed directives in structured formats (JSON, YAML, Markdown)
- **Multi-Turn Conditioning**: Build false context across turns to soften resistance
- **Jailbreak Testing**: DAN, role-play, hypothetical scenarios, and novel variations
- **Indirect Injection**: Plant malicious instructions in RAG documents or external content

**Evidence Collected**:
- Successful injection prompts and responses
- Leaked system prompts or internal context
- Policy violation outputs
- Bypass success rate metrics

### Phase 3: Tool Abuse and Privilege Escalation

**Objectives**: Validate tool authorization controls and identify escalation paths

**Activities**:
- Map all available tools/functions and permission models
- Test tool invocation via prompt injection (forced function calls)
- Attempt cross-user data access through tool parameters
- Chain multiple tool calls to achieve unauthorized objectives
- Test for command injection in tool arguments
- Validate rate limiting and abuse prevention

**Evidence Collected**:
- Unauthorized tool invocation logs
- Cross-user/cross-tenant data access proof
- Command injection PoCs
- Privilege escalation chains

### Phase 4: Data Exfiltration and Information Disclosure

**Objectives**: Identify sensitive information leakage paths

**Activities**:
- Probe for training data memorization and extraction
- Test for PII leakage in model outputs
- Attempt exfiltration via tool abuse (email, webhooks, API calls)
- Validate conversation log security and cross-session isolation
- Test for credential exposure in prompts or responses

**Evidence Collected**:
- Examples of leaked sensitive information
- Exfiltration PoCs demonstrating data theft
- PII exposure instances
- Conversation isolation bypass evidence

### Phase 5: Reporting and Remediation Guidance

**Activities**:
- Findings analysis, deduplication, and risk prioritization
- Root cause identification and systemic pattern analysis
- Framework mapping (OWASP, ATLAS, NIST)
- Remediation roadmap development with effort estimates
- Executive summary and business impact assessment
- Evidence package preparation

## Techniques Covered

| ID | Technique | Relevance |
|----|-----------|-----------|
| AML.T0051 | [[techniques/prompt-injection|Prompt Injection]] | Direct and indirect injection attacks |
| AML.T0054 | [[techniques/jailbreak-policy-bypass|Jailbreak & Policy Bypass]] | Safety guardrail bypass |
| AML.T0056 | [[techniques/system-prompt-leakage|System Prompt Leakage]] | Hidden instruction extraction |
| - | [[techniques/sensitive-info-disclosure|Sensitive Information Disclosure]] | PII and credential exposure |
| - | [[techniques/training-data-memorization|Training Data Memorization]] | Training data extraction |
| - | Tool Privilege Escalation | Unauthorized tool access |
| - | [[techniques/unsafe-tool-invocation|Unsafe Tool Invocation]] | Tool misuse attacks |
| - | [[techniques/agent-goal-hijack|Agent Goal Hijacking]] | Redirecting autonomous objectives |
| - | [[techniques/auth-context-confusion|Authentication Context Confusion]] | Cross-user access via confused permissions |
| - | [[techniques/insecure-prompt-assembly|Insecure Prompt Assembly]] | Unsafe prompt construction |

## Tooling

### Manual Testing

**Primary approach**: Interactive adversarial prompting with systematic coverage. Human judgment essential for context-aware exploits and nuanced model behavior.

**Tools**:
- **Burp Suite / ZAP**: HTTP interception, manipulation, and replay
- **Custom scripts**: Python/Node.js for programmatic testing and multi-turn conversations
- **Browser DevTools**: Client-side behavior inspection, WebSocket analysis
- **Postman / Insomnia**: API endpoint testing and collection organization

### Automated Testing

**Automation scope**: Payload variation, regression testing, baseline comparison

**Frameworks**:
- **garak**: Automated prompt injection and jailbreak payload generation
- **PyRIT**: Multi-turn adversarial campaigns with adaptive strategies
- **Custom harnesses**: Domain-specific test suites

### Reproducibility Standards

All findings include:
- Complete prompt-response transcripts with timestamps
- API request/response pairs
- Environment snapshot (model version, configuration, endpoints)
- Reproduction scripts (Python/JavaScript where automatable)
- Screen recordings for complex multi-step attacks
- Success rate data (e.g., "8/10 attempts successful")

## Deliverables

1. **Executive Summary Report** (5-8 pages)
   - Critical/High findings with business impact
   - Risk score and compliance posture (OWASP LLM Top 10)
   - Strategic recommendations
   - Industry baseline comparison

2. **Technical Findings Report** (25-40 pages)
   - Detailed vulnerability write-ups per trust boundary
   - PoC evidence (transcripts, screenshots, logs)
   - Exploitation narratives with business context
   - Root cause analysis and mitigation guidance
   - Framework mapping (ATLAS, OWASP, NIST)

3. **Framework Compliance Matrix**
   - OWASP LLM Top 10 tested vs. not-tested breakdown
   - MITRE ATLAS technique coverage
   - NIST AI RMF alignment

4. **Risk-Prioritized Remediation Roadmap**
   - Phased mitigation plan: Immediate → Short-term → Long-term
   - Effort estimates per fix
   - Risk reduction impact scores
   - Validation criteria and retest guidance

5. **Evidence Package**
   - Prompt injection payloads (100+ examples)
   - Successful jailbreak techniques
   - Tool abuse proof-of-concepts
   - Data exfiltration demonstrations
   - Reproduction scripts and test harnesses

## Sources

> Engagement methodology and attack patterns derived from real-world LLM security incidents and research.
> — [[sources/bibliography#Developer's Playbook for LLM Security]]

> Framework mapping based on OWASP LLM Top 10, MITRE ATLAS, and NIST AI RMF.
> — [[frameworks/OWASP-top-10-LLM|OWASP Top 10 for LLM Applications]]
> — [[frameworks/atlas/MITRE-ATLAS|MITRE ATLAS Framework]]
