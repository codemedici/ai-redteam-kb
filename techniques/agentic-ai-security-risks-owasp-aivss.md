---
title: "Agentic AI Security Risks (OWASP AIVSS)"
tags:
  - type/technique
  - target/agent
  - access/inference
  - access/api
  - source/ai-native-llm-security
owasp: LLMXX
maturity: draft
created: 2026-02-12
updated: 2026-02-14
---

## Summary

OWASP Artificial Intelligence Vulnerability Scoring System (AIVSS) defines ten core security risk categories unique to agentic AI systems—AI agents with autonomy, memory, and tool use capabilities. These risks emerge specifically when LLMs gain the ability to take actions (invoke tools, move data, modify systems) without waiting for human approval. AIVSS provides standardized identification and assessment of vulnerabilities that conventional CVSS frameworks cannot address because they don't account for AI systems that learn, adapt, and act autonomously. After analyzing 43 field incidents involving AI agents, OWASP distilled ten failure modes representing emergent security challenges that simply do not exist when models are confined to text generation.


## Mechanism

### Core Insight

Once an LLM stack gains autonomy (ability to take actions), persistent memory (context across sessions), and tool use (integration with external systems), the damage surface expands faster than classical CVSS can track. The ten AIVSS risk categories represent security challenges unique to this agentic operation model:

**1. Agentic AI Tool Misuse**
- Attacker manipulates AI agents into abusing integrated/authorized tools through deceptive prompts, malformed inputs, or operational misdirection
- Leads to data exfiltration, resource abuse, or harmful operations while staying within nominal permissions
- Real-world case (2025): ChatGPT Deep Research agent — attackers crafted emails with invisible embedded HTML instructions directing agent to extract Gmail data and transmit to external server, causing silent data theft without user awareness

**2. Agent Access-Control Violation** 
- Agent acts as confused deputy, using its own powerful identity to fetch or modify records the end user isn't entitled to see
- Occurs through prompt injection, flawed role/scope definitions, insecure delegation, or ambiguous trust relationships
- Attack pattern: Agents granted broad/implicit permissions to email, cloud resources, or internal APIs without granular scoping; attackers craft prompts causing privileged actions

**3. Agent Cascading Failures**
- Error, misinterpretation, or malicious input in one component triggers chain reaction across interconnected agents, tools, or workflows
- Single faulty step propagates through entire pipeline — small failure becomes large-scale incident
- Mechanism: One agent produces ambiguous/hallucinated output consumed as factual by downstream agent, which executes actions that magnify initial error

**4. Agent Orchestration and Multi-Agent Exploitation**
- Attacker manipulates coordination logic, communication pathways, or shared context among multiple agents to trigger harmful behaviors no single agent can perform alone
- Weakness lies in trust assumptions across agent network, not in any individual agent
- Attack pattern: Feed misleading data to planning agent → flawed task breakdown → execution agents perform high-impact actions believing another agent validated the request

**5. Agent Identity Impersonation**
- Attacker causes one agent to masquerade as another, or tricks agent into believing request comes from trusted peer/service/user
- Via manipulated context windows, spoofed metadata, poisoned system prompts, or gaps in identity verification
- Attack pattern: Inject crafted instructions into shared memory so target agent interprets them as from privileged agent with higher permissions

**6. Agent Memory and Context Manipulation**
- Attacker alters, poisons, or injects information into agent's short-term context, long-term memory, or shared state to influence future reasoning
- Amplified when memory persistence/retrieval lacks strong validation
- Attack pattern: Inject malicious instructions or fabricated facts into conversation history, scratchpad, or memory store; agent later retrieves and interprets as legitimate knowledge

**7. Insecure Agent Critical-Systems Interaction**
- Agent granted access to high-impact operational systems (identity providers, financial systems, production workloads, ICS, cloud infrastructure) without proper safeguards
- Small errors trigger significant consequences when critical systems implicitly trust agent requests
- Attack pattern: Agent with permission to modify configs, manage accounts, deploy code, or initiate transactions; attacker injects misleading instructions causing agent to execute destructive production commands

**8. Agent Supply Chain and Dependency Attacks**
- Compromise of external components agents rely on — models, datasets, plugins, third-party tools
- Agent treats these as trusted sources, allowing attacker to influence reasoning, modify outputs, or trigger harmful actions without direct agent interaction
- Attack vectors: Poisoned datasets/retrieval indices, compromised third-party plugins, manipulated API integrations returning crafted results

**9. Agent Untraceability**
- Inability to reconstruct sequence of agent's actions, decisions, and data interactions to verifiable source — creates "forensic black holes"
- Exacerbated by inadequate/manipulable logs, ephemeral identities, and complex multi-agent workflows
- Real-world scenario (Financial): Autonomous payment agent compromised to subtly alter payment destinations over extended period; without robust traceability, investigators cannot pinpoint when compromise occurred or which instructions caused malicious payments

**10. Agent Goal and Instruction Manipulation**
- Attacker exploits how AI agents interpret, process, and execute assigned goals — impacting fundamental decision-making while appearing to operate normally
- Encompasses goal interpretation attacks, instruction set poisoning, and semantic manipulation
- Attack vectors: Recursive goal subversion, prompt injection overriding security controls, natural language ambiguity exploitation


## Preconditions

- Organization has deployed AI agents with one or more of:
  - **Autonomy:** Ability to take actions without explicit human approval for each step
  - **Memory:** Persistent context across sessions or shared state between agents
  - **Tool use:** Integration with external systems (APIs, databases, file systems, email, cloud services, production infrastructure)
- Attacker has access to:
  - User-facing interface (chat, API, email) where prompts/inputs can influence agent behavior
  - Knowledge of agent's tools, permissions, or operational context
  - Ability to craft inputs that exploit agent reasoning or tool invocation logic
- Insufficient safeguards:
  - Lack of tool allowlisting or schema validation
  - Missing user context binding or least-privilege enforcement
  - No cryptographic signing of inter-agent messages
  - Inadequate memory integrity verification
  - Insufficient audit logging or traceability
  - No circuit breakers or resource quotas


## Impact

**Business Impact:**
- **Financial loss:** Unauthorized transactions, resource abuse, spending limit exhaustion
- **Data breach:** Exfiltration of sensitive data through tool misuse or access-control violations
- **Operational disruption:** Cascading failures causing service outages or system instability
- **Reputational damage:** Public incidents involving agent misbehavior or security failures
- **Regulatory violations:** Non-compliance with data protection, financial regulations, or industry standards
- **Legal liability:** Unauthorized actions performed by agents on behalf of organization

**Technical Impact:**
- **Privilege escalation:** Agents accessing resources beyond intended scope
- **Data corruption:** Cascading errors propagating through multi-agent systems
- **System compromise:** Agents with critical-system access executing destructive commands
- **Supply chain contamination:** Poisoned dependencies affecting all dependent agents
- **Forensic gaps:** Inability to reconstruct incident timeline due to inadequate logging
- **Goal misalignment:** Agents pursuing attacker-defined objectives while appearing legitimate

**Severity:** **High** to **Critical**
- Severity depends on agent permissions, tool capabilities, and blast radius
- Agents with access to financial systems, production infrastructure, or sensitive data represent critical risk
- Multi-agent systems amplify impact through cascading failures and coordinated exploitation


## Detection

**Tool Invocation Anomalies:**
- Unusual tool sequences or combinations (e.g., read_database → send_email)
- High-frequency tool usage beyond baseline
- Tool invocations outside normal business hours
- Tools invoked with unexpected argument patterns

**Access Control Violations:**
- Permission denial patterns suggesting privilege escalation attempts
- Access to resources outside user's authorized scope
- Tool usage patterns inconsistent with user role

**Cascading Failure Indicators:**
- Rapid error propagation across agent chain
- Downstream agents consuming flawed outputs from upstream agents
- Circuit breakers opening due to failure threshold exceeded

**Multi-Agent Coordination Anomalies:**
- Unexpected inter-agent communication patterns
- Workflow execution deviating from signed manifests
- Agents coordinating in ways not defined in orchestration logic

**Identity and Authentication Anomalies:**
- Inter-agent messages failing signature verification
- Agents attempting to impersonate others via context manipulation
- Unexpected role switches or permission escalations

**Memory Integrity Violations:**
- Memory chunks failing hash verification
- Unauthorized memory writes from unexpected sources
- Memory access patterns deviating from normal behavior

**Critical System Interaction Alerts:**
- High-privilege operations initiated by agents
- Configuration changes or deployment actions without human approval
- Production commands executed outside change management windows

**Supply Chain Warnings:**
- Dependency verification failures (unsigned artifacts, missing SBOM)
- Third-party tool behavior deviating from expected patterns
- External API responses containing suspicious content

**Traceability Gaps:**
- Missing or incomplete audit logs for agent actions
- Inability to correlate user prompts with downstream tool invocations
- Forensic reconstruction failing due to insufficient telemetry

**Goal Manipulation Indicators:**
- System prompt modifications outside version control workflow
- Agent pursuing objectives inconsistent with charter
- Behavioral drift from baseline decision patterns


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| | [[mitigations/agent-tool-allowlisting]] | Restrict agents to narrowly scoped, explicitly approved tool set to prevent unauthorized tool abuse |
| AML.M0032 | [[mitigations/tool-sandboxing-architecture]] | Execute tool invocations in isolated environments with resource quotas to limit blast radius |
| | [[mitigations/tool-argument-validation]] | Validate tool arguments against schemas using policy engines (e.g., OPA) before execution |
| AML.M0004 | [[mitigations/least-privilege-implementation]] | Enforce least-privilege tool permissions and just-in-time access controls |
| | [[mitigations/agent-resource-quotas]] | Implement prepaid wallets, spending limits, circuit breakers, and rate limits per agent |
| | [[mitigations/tool-invocation-monitoring]] | Log and monitor every tool invocation for anomaly detection |
| AML.M0016 | [[mitigations/user-context-binding]] | Propagate user JWT through token exchange to bind tool execution to originating user permissions |
| | [[mitigations/temporary-privilege-reduction]] | Terminate access rights once task completes, use time-bound credentials |
| | [[mitigations/approval-workflow-patterns]] | Require explicit human approval for high-impact actions |
| | [[mitigations/attribute-based-access-control]] | Implement policy-driven authorization for granular access control |
| | [[mitigations/output-monitoring-validation]] | Validate every output before it becomes another agent's input to prevent cascading failures |
| | [[mitigations/inter-agent-message-signing]] | Cryptographically sign all inter-agent messages to prevent identity impersonation and coordination manipulation |
| | [[mitigations/agent-memory-integrity-verification]] | Hash memory chunks at ingestion, store in append-only ledger, re-verify before retrieval |
| | [[mitigations/ml-sbom-and-model-lineage]] | Generate AIBOM + SBOM, sign with Cosign, verify attestations at load time |
| | [[mitigations/content-signing-and-provenance]] | Reject artifacts lacking signature anchored to corporate trust root |
| | [[mitigations/source-validation-and-trust-scoring]] | Audit third-party tools, validate model/plugin provenance, strict version pinning |
| | [[mitigations/immutable-audit-logs]] | Emit OpenTelemetry spans for every tool call, ship to immutable store with retention policies |
| | [[mitigations/telemetry-and-monitoring-architecture]] | Centralized monitoring for abnormal coordination patterns and runtime anomalies |
| AML.M0015 | [[mitigations/behavioral-monitoring]] | Continuous behavioral monitoring and anomaly detection for agent activity |
| | [[mitigations/system-prompt-reinforcement]] | Freeze system prompt in version control with two-person review for changes |
| AML.M0006 | [[mitigations/dual-llm-judge-pattern]] | Shadow LLM re-evaluates every outbound action against original charter |


## Sources

> "OWASP Artificial Intelligence Vulnerability Scoring System (AIVSS) — standardized identification and assessment of security vulnerabilities unique to agentic AI systems. Designed because conventional CVSS cannot address risks from AI that learns, adapts, and acts autonomously. After analyzing 43 field incidents, OWASP AIVSS distilled ten failure modes that are emergent — they simply do not exist when the model is confined to a chat box."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 357-369

> "ChatGPT Deep Research agent (2025): Attackers crafted emails with invisible embedded HTML instructions directing the agent to extract Gmail data and transmit to external server. Agent invoked tools without sufficient approval gates, causing silent data theft without user awareness."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 357-362

> "Perfect prevention is a mirage in autonomous systems. The zero-trust approach is: (1) Assume compromise — treat every agent interaction as potentially adversarial, (2) Detect fast — continuous monitoring and anomaly detection, (3) Limit blast radius — containment over prevention."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 357-368
