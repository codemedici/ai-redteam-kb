---
id: publish-poisoned-ai-agent-tool
title: "AML.T0104: Publish Poisoned AI Agent Tool"
sidebar_label: "Publish Poisoned AI Agent Tool"
sidebar_position: 105
---

# AML.T0104: Publish Poisoned AI Agent Tool

Adversaries may create and publish poisoned AI agent tools. Poisoned tools may contain an [LLM Prompt Injection](/techniques/AML.T0051), which can lead to a variety of impacts.

Tools may be published to open source version control repositories (e.g. GitHub, GitLab), to package registries (e.g. npm), or to repositories specifically designed for sharing tools (e.g. OpenClaw Hub). These registries may be largely unregulated and may contain many poisoned tools [\[1\]][1].

[1]: https://opensourcemalware.com/blog/clawdbot-skills-ganked-your-crypto

## Metadata

- **Technique ID:** AML.T0104
- **Created:** 2026-01-30
- **Last Modified:** 2026-02-05
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/supply-chain-compromise-via-poisoned-clawdbot-skill|Supply Chain Compromise via Poisoned ClawdBot Skill]]
