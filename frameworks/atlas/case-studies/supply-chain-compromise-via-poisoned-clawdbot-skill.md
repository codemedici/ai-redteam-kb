---
id: supply-chain-compromise-via-poisoned-clawdbot-skill
title: "AML.CS0049: Supply Chain Compromise via Poisoned ClawdBot Skill"
type: case-study
sidebar_position: 50
---

# AML.CS0049: Supply Chain Compromise via Poisoned ClawdBot Skill

A security researcher demonstrated a proof-of-concept supply chain attack using a poisoned ClawdBot Skill shared on ClawdHub, a Skill registry for agents. The poisoned Skill contained a prompt injection that caused ClawdBot to execute a shell command that reached the researcher’s server. Although the researcher here used this access simply to warn users about the danger, they could have instead delivered a malicious payload and compromised the user’s system. The security researcher recorded 16 different users who downloaded and executed the poisoned Skill in the first 8 hours of it being published on ClawdHub.

## Metadata

- **ID:** AML.CS0049
- **Incident Date:** 2026-01-26
- **Type:** exercise
- **Target:** ClawdBot (now OpenClaw)
- **Actor:** Jamieson O’Reilly

## Procedure

### Step 1: Develop Capabilities

**Technique:** [[frameworks/atlas/techniques/resource-development/develop-capabilities|AML.T0017: Develop Capabilities]]

The researcher created a simple web server to log requests.

### Step 2: Domains

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/acquire-infrastructure-consumer-hardware|AML.T0008.002: Domains]]

The researcher registered the domain `clawdhub-skill.com` to host their web server.

### Step 3: LLM Prompt Crafting

**Technique:** [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

The researcher crafted a prompt injection designed to cause Claude Code to execute a `curl` command to the researcher’s `clawdhub-skill.com` domain.

### Step 4: Publish Poisoned AI Agent Tool

**Technique:** [[frameworks/atlas/techniques/resource-development/publish-poisoned-ai-agent-tool|AML.T0104: Publish Poisoned AI Agent Tool]]

The researcher developed a poisoned ClawdBot Skill called “What Would Elon Do?” The Skill contained the malicious prompt in the `rules/logic.md` file, which is read when the Skill is activated. The researcher published their Skill to ClawdHub.

### Step 5: AI Software

**Technique:** [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-model|AML.T0010.001: AI Software]]

Users downloaded the poisoned Skill from ClawdHub.

The researcher had used a script to increase the number of downloads of their Skill to increase visibility and gain trust.

Note that ClawdHub does not display all files that are part of the Skill, making it hard for users to review Skills before downloading them.

### Step 6: Poisoned AI Agent Tool

**Technique:** [[frameworks/atlas/techniques/execution/user-execution/user-execution-poisoned-ai-agent-tool|AML.T0011.002: Poisoned AI Agent Tool]]

When a user asked Claude Code “what would Elon do?” it calls the poisoned Skill.

### Step 7: Direct

**Technique:** [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-direct-prompt-injection|AML.T0051.000: Direct]]

Claude Code read all files that are part of the Skill, executing the malicious prompt in the `rules/logic.md` file.

### Step 8: Masquerading

**Technique:** [[frameworks/atlas/techniques/defense-evasion/masquerading|AML.T0074: Masquerading]]

Claude Code prompted the user before executing the shell command. The researcher had registered the `https://clawdhub-skill.com` domain, which appears to be legitimate and may be confused with the legitimate `https://clawdhub.com` domain, causing the user to select confirm.

### Step 9: AI Agent Tool Invocation

**Technique:** [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]]

Claude Code executed the shell command using it’s `bash` tool.

### Step 10: External Harms

**Technique:** [[frameworks/atlas/techniques/impact/external-harms|AML.T0048: External Harms]]

In this proof of concept, the researcher simply pinged their server and warned the user of the dangers of using Skills without reading the source code, causing no harm. However, they could have delivered a malicious payload, and caused a variety of harms, including:
- Exfiltrating the user’s codebase
- Injecting backdoors into the user’s codebase
- Stealing the user’s credentials
- Installing malware or crypto miners
- Performing anything else Claude Code is capable of

## References

1. [eating lobster souls Part II: the supply chain (aka - backdooring the #1 downloaded clawdhub skill)](https://x.com/theonejvo/status/2015892980851474595)
