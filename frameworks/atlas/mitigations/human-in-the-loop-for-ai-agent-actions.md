---
id: human-in-the-loop-for-ai-agent-actions
title: "AML.M0029: Human In-the-Loop for AI Agent Actions"
sidebar_label: "Human In-the-Loop for AI Agent Actions"
sidebar_position: 30
type: mitigation
---

# AML.M0029: Human In-the-Loop for AI Agent Actions

Systems should require the user or another human stakeholder to approve AI agent actions before the agent takes them. The human approver may be technical staff or business unit SMEs depending on the use case. Separate tools, such as dedicated audit agents, may assist human approval, but final adjudication should be conducted by a human decision-maker. 

The security benefits from Human In-the-Loop policies may be at odds with operational overhead costs of additional approvals. To ease this, Human In-the-Loop policies should follow the degree of consequence of the task at hand. Minor, repetitive tasks performed by agents accessing basic tools may only require minimal human oversight, while agents employed in systems with significant consequences may necessitate approval from multiple stakeholders diversified across multiple organizations.

## Metadata

- **Mitigation ID:** AML.M0029
- **Created:** 2025-10-29
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** Deployment

## Techniques (3)

- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]] — Requiring user confirmation of AI agent tool invocations can prevent the automatic execution of tools by an adversary.
- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Requiring user confirmation of AI agent tool invocations can prevent the automatic execution of tools by an adversary.
- [[frameworks/atlas/techniques/impact/data-destruction-via-ai-agent-tool-invocation|AML.T0101: Data Destruction via AI Agent Tool Invocation]] — Requiring user confirmation of AI agent tool invocations can prevent the automatic execution of tools by an adversary.
