---
id: deploy-ai-agent
title: "AML.T0103: Deploy AI Agent"
sidebar_label: "Deploy AI Agent"
sidebar_position: 104
---

# AML.T0103: Deploy AI Agent

Adversaries may launch AI agents in the victim's environment to execute actions on their behalf. AI agents may have access to a wide range of tools and data sources, as well as permissions to access and interact with other services and systems in the victim's environment. The adversary may leverage these capabilities to carry out their operations.

Adversaries may configure the AI agent by providing an initial system prompt and granting access to tools, effectively defining their goals for the agent to achieve. They may deploy the agent with excessive trust permissions and disable any user interactions to ensure the agent's actions aren't blocked.

Launching an AI agent may provide for some autonomous behavior, allowing for the agent to make decisions and determine how to achieve the adversary's goals. This also represents a loss of control for the adversary.

## Metadata

- **Technique ID:** AML.T0103
- **Created:** 2026-01-28
- **Last Modified:** 2026-01-28
- **Maturity:** realized

## Tactics (1)

- [[frameworks/atlas/tactics/execution|Execution]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/code-to-deploy-destructive-ai-agent-discovered-in-amazon-q-vs-code-extension|Code to Deploy Destructive AI Agent Discovered in Amazon Q VS Code Extension]]
