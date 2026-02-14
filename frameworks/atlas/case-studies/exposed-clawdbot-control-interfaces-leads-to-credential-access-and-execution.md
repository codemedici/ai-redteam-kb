---
id: exposed-clawdbot-control-interfaces-leads-to-credential-access-and-execution
title: "AML.CS0048: Exposed ClawdBot Control Interfaces Leads to Credential Access and Execution"
type: case-study
sidebar_position: 49
---

# AML.CS0048: Exposed ClawdBot Control Interfaces Leads to Credential Access and Execution

A security researcher identified hundreds of exposed ClawdBot control interfaces on the public internet. ClawdBot (now OpenClaw) “is a personal AI assistant you run on your own devices. It answers you on the channels you already use … , plus extension channels. … It can speak and listen on macOS/iOS/Android, and can render a live Canvas you control.”[<sup>\[1\]</sup>][1] The researcher was able to access credentials to a variety of connected applications via ClawdBot’s configuration file. They were also able to invoke ClawdBot’s skills by prompting it via the chat interface, leading to root access in the container.

The researcher searched Shodan[<sup>\[2\]</sup>][2] to identify Clawdbot instances exposed on the public internet, some without authentication enabled. The researcher demonstrated that the ClawdBot’s authentication mechanism could be bypassed due to a proxy misconfiguration.

With access to ClawdBot’s control interface, they were then able to access ClawdBot’s configuration, which contained credentials to a variety of other services. Across various exposed instances of ClawdBot, they identified Anthropic API Keys, Telegram Bot Tokens, Slack Oauth Credentials, and Signal Device Linking URIs. The researcher prompted ClawdBot directly via the chat interface, which led to exposure of its system prompt. They were also able to get ClawdBot to execute commands via it’s `bash` skill, which at least in once instance led to root access in the ClawdBot container.

The researcher noted a broad range of other impacts they could have had with this level of access, including:
-	Manipulation of user chat history with the ClawdBot AI agent
-	Exfiltration of conversation histories of any connected messaging services
-	Impersonation of users by sending messages on their behalf via connected messaging services

[1]: https://github.com/openclaw/openclaw
[2]: https://www.shodan.io/search?query=Clawdbot+Control

## Metadata

- **ID:** AML.CS0048
- **Incident Date:** 2026-01-25
- **Type:** exercise
- **Target:** ClawdBot (now OpenClaw)
- **Actor:** Jamieson O’Reilly

## Procedure

### Step 1: Search Open Technical Databases

**Technique:** [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/search-open-tech-db-journals-conferences|AML.T0000: Search Open Technical Databases]]

The researcher performed targeting by searching for the title tag of ClawdBot’s web-based control interface, “Clawdbot Control” on Shodan, identifying hundreds of ClawdBot control interfaces exposed on the public internet.

### Step 2: Exploit Public-Facing Application

**Technique:** [[frameworks/atlas/techniques/initial-access/exploit-public-facing-application|AML.T0049: Exploit Public-Facing Application]]

The researcher exploited a proxy misconfiguration present in ClawdBot’s control server to gain access to control interfaces that had authentication enabled.

### Step 3: Credentials from AI Agent Configuration

**Technique:** [[frameworks/atlas/techniques/credential-access/credentials-from-ai-agent-configuration|AML.T0083: Credentials from AI Agent Configuration]]

The researcher accessed credentials to a variety of services stored in plaintext in ClawdBot’s configuration file (`~/.clawdbot/clawdbot.json`, which is visible in the ClawdBot dashboard. Across various exposed ClawdBot instances, they found:
- Anthropic API Keys 
- Telegram Bot Tokens
- Slack Oauth Credentials
- Signal Device Linking URIs

### Step 4: Indirect

**Technique:** [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-indirect-prompt-injection|AML.T0051.001: Indirect]]

The researcher was able to prompt ClawdBot directly through the control interface.

### Step 5: System Prompt

**Technique:** [[frameworks/atlas/techniques/discovery/discover-llm-system-information/discover-LLM-system-info-special-chars|AML.T0069.002: System Prompt]]

The researcher prompted ClawdBot to `cat SOUL.md` (the file containing ClawdBot’s system prompt), and it replied with its contents.

### Step 6: AI Agent Tool Credential Harvesting

**Technique:** [[frameworks/atlas/techniques/credential-access/ai-agent-tool-credential-harvesting|AML.T0098: AI Agent Tool Credential Harvesting]]

The researcher prompted ClawdBot with `env` and it responded by invoking its `bash` skill  and executing the `env` command, which contained additional secrets for other services.

### Step 7: AI Agent Tool Invocation

**Technique:** [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]]

The researcher prompted ClawdBot with `root` and it responded by invoking its ‘bash`skill logged in as the root user.

### Step 8: Manipulate User LLM Chat History

**Technique:** [[frameworks/atlas/techniques/defense-evasion/manipulate-user-llm-chat-history|AML.T0092: Manipulate User LLM Chat History]]

The researcher could have used the found Anthropic API Keys to manipulate the ClawdBot’s chat history with the user including deleting or modifying messages.

### Step 9: Exfiltration via Cyber Means

**Technique:** [[frameworks/atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025: Exfiltration via Cyber Means]]

The researcher could have used the discovered application tokens to exfiltrate entire private conversation histories including shared files from any connected messaging apps (e.g. Telegram, Slack, Discord, Signal, WhatsApp, etc.).

### Step 10: User Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/external-harms-user|AML.T0048.003: User Harm]]

The researcher could have used the discovered application tokens to cause further harms to the user, including impersonation by sending messages on the user’s behalf via any of the connected messaging apps.

## References

1. [hacking clawdbot and eating lobster souls](https://x.com/theonejvo/status/2015401219746128322)
