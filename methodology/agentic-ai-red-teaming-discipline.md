---
tags:
  - methodology
  - red-team
  - source/securing-ai-agents
  - trust-boundary/agent-runtime
  - type/methodology
created: 2026-02-11
source: [[sources/bibliography#Securing AI Agents]]
pages: "208-210"
---
# Agentic AI Red Teaming as a Specialized Discipline

Agentic AI red teaming is a new, specialized discipline distinct from traditional LLM red teaming, required to address the fundamentally expanded attack surface of autonomous AI agents.

## Why a New Discipline?

**LLM Red Teaming focuses on:**
- Direct inputs/outputs in conversational context
- Prompt injections ("jailbreaks")
- Biases and harmful content generation
- Factual inaccuracies

**Agentic AI Red Teaming requires:**
- Holistic assessment of dynamic, interactive systems
- Testing entire cognitive architecture (not just model)
- Evaluation of action execution, not just text generation

**Analogy:** "Testing an engine on a stand versus testing a car as it navigates through city traffic."

## Expanded Attack Surface

Agentic AI introduces five core components that create new threat vectors:

### 1. Tool Use
- **LLM:** Output is text
- **Agent:** Output is action (API call, DB query, shell command)
- **New threat:** Tool misuse - agent tricked into using authorized tools for malicious purposes

### 2. Planning and Goal Setting
- **LLM:** Single-prompt completion
- **Agent:** Persistent goal broken into autonomous sub-tasks
- **New threat:** Manipulation of planning logic, gradual goal corruption ("death by a thousand cuts")

### 3. Autonomy and Identity
- **Spectrum:** Human-in-loop → fully autonomous
- **New components:** Agent identity (API keys, service accounts, credentials)
- **New threats:** Identity theft, impersonation, credential exploitation

### 4. Memory
- **LLM:** Limited context window
- **Agent:** Short-term (session) + long-term (vector DB) memory
- **New threats:** Context poisoning, cross-session data leakage, knowledge base corruption

### 5. Agent-to-Agent Communication
- **Multi-agent systems:** Swarms, hierarchies, collaboration
- **New threat:** Communication protocol attacks, trust establishment exploitation, compromised agent manipulating collective

## CSA/OWASP 12 Threat Categories Framework

Developed by Cloud Security Alliance and OWASP AI Exchange (led by Ken Huang), this framework organizes agentic AI risks into 12 categories:

1. Agent Authorization and Control Hijacking
2. Agent Knowledge Base Poisoning
3. Agent Impact Chain and Blast Radius
4. Multi-Agent Exploitation
5. [Additional 8 categories]

**Purpose:** Foundational roadmap for practitioners to systematically probe every component of agentic architecture.

## MAESTRO Threat Modeling Framework

Proposed by Ken Huang (2025), MAESTRO offers structured, seven-layer approach for identifying threats traditional frameworks miss:

**Proactively uncovers:**
- Emergent behaviors
- Agent-to-agent collusion
- Data poisoning
- Model extraction
- System-level vulnerabilities

**Integrated with:** Risk management framework to prioritize most significant risks

## Red Teaming Scope

Agentic AI red teaming is **holistic security assessment** that systematically probes:
- Core model
- Tool integrations
- Memory stores
- Interaction protocols
- Agent-to-agent communication
- Planning/reasoning logic
- Identity management

## Dynamic Threat Landscape

**Critical recognition:** Agentic AI security is not static. Adversaries constantly develop novel techniques exploiting unique agent properties (planning, action, collaboration).

**Response:** Continuous adaptation, leverage comprehensive frameworks (MAESTRO), adopt risk management approach.

## Related

- Variant Agentic Systems
- Phase 3 Threat Modeling
- application-agent-boundary-overview]]
- [[atlas/tactics/initial-access]]

## Source

> "Agentic AI leap in capability introduces a security landscape fundamentally distinct from that of its predecessors, including standalone Large Language Models (LLMs). Consequently, securing these systems requires a new, specialized discipline: Agentic AI Red Teaming."
>
> "The difference is akin to testing an engine on a stand versus testing a car as it navigates through city traffic."
>
> "To address this enlarged threat landscape, the specialized discipline of Agentic AI Red Teaming has emerged. It is a holistic security assessment that systematically probes every component of the agentic architecture."
> 
> Source: [[sources/bibliography#Securing AI Agents]], p. 208-210

## Notes

This establishes Agentic AI Red Teaming as a distinct professional discipline with its own frameworks, methodologies, and threat models. Critical for methodology/variant-agentic-systems content.

The 12-category CSA/OWASP framework is a key reference for structuring red team engagements on agentic systems.
