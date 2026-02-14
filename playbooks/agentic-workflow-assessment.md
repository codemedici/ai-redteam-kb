---
title: "Agentic Workflow Assessment"
tags:
  - type/playbook
  - target/agent
  - target/llm
  - source/developers-playbook-llm
maturity: reviewed
created: 2026-02-14
updated: 2026-02-14
---

# Agentic Workflow Assessment

## Overview

An adversarial security assessment targeting AI systems with autonomy or multi-step decision-making capabilities—commonly called "AI agents." These systems can plan, reason, and act in dynamic environments, often using LLMs with tools or APIs to perform actions like browsing the web, writing code, controlling software, or executing commands.

This engagement tests for goal misalignment, unsafe actions, tool misuse, privilege escalation, and emergent undesirable behaviors arising from agent autonomy. As AI agents become more capable and autonomous, they introduce new attack surfaces beyond traditional LLM applications, with real-world incidents demonstrating manipulation toward destructive actions, data exfiltration, or unauthorized decisions.

## Scope & Prerequisites

### In Scope

- **Autonomous AI Assistants**: Agents browsing web, sending emails, creating files, performing multi-step tasks
- **Code Generation Agents**: AI systems writing, executing, and modifying code autonomously
- **Multi-Agent Systems**: Orchestrated workflows with multiple AI agents coordinating tasks
- **Robotics/IoT AI Agents**: Autonomous systems controlling physical devices or IoT systems
- **Workflow Automation Agents**: AI systems automating business processes with decision-making
- **Customer Service Agents**: Autonomous agents handling interactions with tool access

### Out of Scope

- Base model prompt injection (covered by [[playbooks/llm-application-red-team|LLM Application Red Team]], though agent-specific injection is in scope)
- Training pipeline security (see [[playbooks/ai-supply-chain-security|AI Supply Chain Security]])
- Infrastructure security beyond agent-specific components
- Physical security of robotics systems
- Social engineering targeting human operators

### Prerequisites

- Agent architecture documentation (planning, tools, memory, state management)
- Tool/API inventory with permission model and schemas
- Agent prompt/system instructions (if accessible)
- Test accounts with varying permission levels
- Sample agent interaction logs
- Agent memory and state management documentation

## Methodology

### Phase 1: Agent Architecture Profiling

**Objectives**: Map agent capabilities, tools, planning mechanisms, memory

**Activities**:
- Document agent planning and reasoning mechanisms
- Inventory all tools/APIs available to agent
- Map agent memory and state persistence mechanisms
- Identify goal-setting and instruction hierarchy
- Test for basic agent response patterns and tool invocation

**Evidence Collected**:
- Agent architecture diagram
- Tool/API inventory with permissions
- Agent planning and reasoning documentation
- Memory and state management details
- Baseline agent behavior samples

### Phase 2: Goal Hijacking and Manipulation

**Objectives**: Test ability to redirect agent goals and objectives

**Activities**:
- **Direct Goal Manipulation**: Attempt to override agent objectives via input
- **Gradual Goal Shifting**: Multi-turn conditioning to shift agent goals
- **Priority Confusion**: Introduce conflicting goals to test prioritization
- **Memory Poisoning**: Inject false context into agent memory
- **Role Confusion**: Manipulate agent's understanding of its role or permissions

**Evidence Collected**:
- Successful goal hijacking demonstrations
- Memory poisoning examples
- Agent behavior under conflicting goals
- Role confusion exploitation transcripts

### Phase 3: Tool Abuse and Privilege Escalation

**Objectives**: Validate tool authorization and identify escalation paths

**Activities**:
- Map all agent tools and their authorization models
- Test tool invocation via prompt injection
- Attempt unauthorized tool access or parameter manipulation
- Chain multiple tool calls for privilege escalation
- Test for command injection in tool arguments
- Validate least-privilege enforcement

**Evidence Collected**:
- Unauthorized tool invocation logs
- Tool chaining exploitation paths
- Command injection PoCs
- Privilege escalation chains

### Phase 4: Autonomous Behavior Safety Testing

**Objectives**: Test agent safety under autonomous operation

**Activities**:
- **Unsafe Action Detection**: Test if agent prevents harmful actions
- **Infinite Loop Prevention**: Attempt to induce repeated unsafe actions
- **Resource Exhaustion**: Test resource limits and abuse prevention
- **Multi-Agent Coordination**: Test coordination vulnerabilities in multi-agent systems
- **Human-in-the-Loop Bypass**: Attempt to circumvent approval gates

**Evidence Collected**:
- Unsafe action execution examples
- Resource exhaustion demonstrations
- Multi-agent coordination compromise PoCs
- Approval gate bypass evidence

### Phase 5: Reporting and Remediation Guidance

**Activities**:
- Findings analysis, deduplication, risk prioritization
- Root cause identification for agent vulnerabilities
- Framework mapping (OWASP, ATLAS, NIST)
- Remediation roadmap with effort estimates
- Executive summary and business impact assessment
- Evidence package preparation

## Techniques Covered

| ID | Technique | Relevance |
|----|-----------|-----------|
| - | [[techniques/agent-goal-hijack\|Agent Goal Hijacking]] | Redirecting autonomous objectives |
| - | [[techniques/tool-privilege-escalation\|Tool Privilege Escalation]] | Unauthorized tool access |
| - | [[techniques/unsafe-tool-invocation\|Unsafe Tool Invocation]] | Tool misuse and harmful actions |
| - | [[techniques/prompt-injection\|Prompt Injection]] | Agent-specific injection attacks |
| - | [[techniques/auth-context-confusion\|Authentication Context Confusion]] | Cross-user access via confused permissions |
| - | Memory Poisoning | Corrupting agent state for persistent compromise |
| - | Multi-Agent Coordination Attacks | Compromising agent orchestration |

## Tooling

### Manual Testing

**Primary approach**: Interactive adversarial testing with focus on multi-step agent behaviors

**Tools**:
- **Agent monitoring tools**: Custom scripts to observe agent planning and tool invocation
- **API testing tools**: Postman/Insomnia for tool endpoint testing
- **Memory inspection tools**: Tools to examine and manipulate agent state
- **Multi-turn conversation tools**: Python/Node.js clients for extended agent interactions

### Automated Testing

**Automation scope**: Tool invocation fuzzing, goal stability testing, safety constraint validation

**Frameworks**:
- **PyRIT**: Multi-turn agent adversarial campaigns
- **Custom agent harnesses**: Domain-specific test suites for agent planning and tool use
- **Safety constraint testers**: Automated validation of safety boundaries

### Reproducibility Standards

All findings include:
- Complete agent interaction transcripts
- Tool invocation logs with arguments and responses
- Agent memory/state snapshots
- Multi-step exploitation sequences
- Success rate data across multiple trials

## Deliverables

1. **Executive Summary Report** (5-8 pages)
   - Critical/High findings with business impact
   - Agent autonomy risk score
   - Strategic recommendations
   - Industry baseline comparison

2. **Technical Findings Report** (25-40 pages)
   - Detailed vulnerability write-ups per agent component
   - PoC evidence (transcripts, tool invocation logs, exploitation chains)
   - Root cause analysis and mitigation guidance
   - Framework mapping (ATLAS, OWASP, NIST)

3. **Framework Compliance Matrix**
   - OWASP LLM Top 10 agent-specific coverage
   - MITRE ATLAS technique coverage
   - NIST AI RMF alignment

4. **Risk-Prioritized Remediation Roadmap**
   - Phased mitigation plan
   - Effort estimates per fix
   - Risk reduction impact scores
   - Validation criteria

5. **Evidence Package**
   - Goal hijacking demonstrations
   - Tool abuse proof-of-concepts
   - Multi-agent coordination attack examples
   - Reproduction scripts

## Sources

> Agentic AI security patterns and attack methodologies.
> — [[sources/bibliography#Developer's Playbook for LLM Security]]

> Framework mapping for autonomous agent risks.
> — [[frameworks/OWASP-top-10-LLM|OWASP Top 10 for LLM Applications]]
> — [[frameworks/atlas/MITRE-ATLAS|MITRE ATLAS Framework]]
