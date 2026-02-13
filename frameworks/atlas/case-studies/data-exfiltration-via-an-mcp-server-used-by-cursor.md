---
id: data-exfiltration-via-an-mcp-server-used-by-cursor
title: "AML.CS0045: Data Exfiltration via an MCP Server used by Cursor"
type: case-study
sidebar_position: 46
---

# AML.CS0045: Data Exfiltration via an MCP Server used by Cursor

The Backslash Security Research Team demonstrated that a Model Context Protocol (MCP) tool can be used as a vector for an indirect prompt injection attack on Cursor, potentially leading to the execution of malicious shell commands.

The Backslash Security Research Team created a proof-of-concept MCP server capable of scraping webpages. When a user asks Cursor to use the tool to scrape a site containing a malicious prompt, the prompt is injected into Cursor’s context. The prompt instructs Cursor to execute a shell command to exfiltrate the victim’s AI agent configuration files containing credentials. Cursor does prompt the user before executing the malicious command, potentially mitigating the attack.

## Metadata

- **ID:** AML.CS0045
- **Incident Date:** 2025-06-24
- **Type:** exercise
- **Target:** Cursor
- **Actor:** Backslash Security Research Team

## Procedure

### Step 1: LLM Prompt Crafting

**Technique:** [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

The researchers crafted a malicious prompt containing an instruction to execute the malicious shell command to exfiltrate the victim’s AI agent credentials.

### Step 2: Stage Capabilities

**Technique:** [[frameworks/atlas/techniques/resource-development/stage-capabilities|AML.T0079: Stage Capabilities]]

The researchers created a malicious web site containing the malicious prompt.

### Step 3: LLM Prompt Obfuscation

**Technique:** [[frameworks/atlas/techniques/defense-evasion/llm-prompt-obfuscation|AML.T0068: LLM Prompt Obfuscation]]

The malicious prompt was hidden in the title tag of the webpage.

### Step 4: Stage Capabilities

**Technique:** [[frameworks/atlas/techniques/resource-development/stage-capabilities|AML.T0079: Stage Capabilities]]

The researchers launched a web server to receive data exfiltrated from the victim.

### Step 5: Drive-by Compromise

**Technique:** [[frameworks/atlas/techniques/initial-access/drive-by-compromise|AML.T0078: Drive-by Compromise]]

When a user asked Cursor to use an MCP tool to scrape the malicious website, the contents of the malicious prompt was retrieved and ingested into Cursor’s context window.

### Step 6: Indirect

**Technique:** [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-indirect-prompt-injection|AML.T0051.001: Indirect]]

When the MCP server scraped the malicious web site, it returned the injected prompt to the MCP client and poisoned the context of the Cursor LLM. Cursor executed the malicious prompt embedded in the website scraped by the MCP tool.

### Step 7: AI Agent Tool Invocation

**Technique:** [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]]

The prompt injection invoked Cursor’s ability to call command line tools via the `run_terminal_cmd` tool.

Cursor prompted the user before executing a shell command, potentially mitigating this attack.

### Step 8: LLM Prompt Obfuscation

**Technique:** [[frameworks/atlas/techniques/defense-evasion/llm-prompt-obfuscation|AML.T0068: LLM Prompt Obfuscation]]

When the MCP server scraped the malicious web site, it returned the injected prompt to the MCP client and poisoned the context of the Cursor LLM. The shell command in the malicious prompt was obscured via base64 encoding, making it less clear to the user that something malicious may be executed.

### Step 9: Credentials from AI Agent Configuration

**Technique:** [[frameworks/atlas/techniques/credential-access/credentials-from-ai-agent-configuration|AML.T0083: Credentials from AI Agent Configuration]]

The shell command located the `.openapi.apiKey` and `.cursor/mcp.json` credentials files that were part of the Cursor’s configuration.

### Step 10: Exfiltration via AI Agent Tool Invocation

**Technique:** [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]]

The credentials files were exfiltrated to the researcher’s server via a `curl` command invoked by Cursor's `run_terminal_cmd` tool.

### Step 11: Financial Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/external-harms-financial|AML.T0048.000: Financial Harm]]

A bad actor could use the stolen credentials cause financial damage and could also steal other sensitive information from the victim user.

## References

1. [Simulating a Context-Poisoning Vulnerable MCP Server: A POC](https://www.backslash.security/blog/simulating-a-vulnerable-mcp-server-for-context-poisoning)
