---
id: living-off-ai-prompt-injection-via-jira-service-management
title: "AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management"
sidebar_label: "Living Off AI: Prompt Injection via Jira Service Management"
sidebar_position: 40
---

# AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management

## Summary

Researchers from Cato Networks demonstrated how adversaries can exploit AI-powered systems embedded in enterprise workflows to execute malicious actions with elevated privileges. This is achieved by crafting malicious inputs from external users such as support tickets that are later processed by internal users or automated systems using AI agents. These AI agents, operating with internal context and trust, may interpret and execute the malicious instructions, leading to unauthorized actions such as data exfiltration, privilege escalation, or system manipulation.

## Metadata

- **Case Study ID:** AML.CS0039
- **Incident Date:** 2025
- **Type:** exercise
- **Target:** Atlassian MCP, Jira Service Management
- **Actor:** Cato CTRL

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Search Victim-Owned Websites

**Tactic:** [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[atlas/techniques/reconnaissance/search-victim-owned-websites|AML.T0003: Search Victim-Owned Websites]]

The researchers performed reconnaissance to learn about Atlassian’s Model Context Protocol (MCP) server and its integration into the Jira Service Management (JSM) platform. Atlassian offers an MCP server, which embeds AI into enterprise workflows. Their MCP enables a range of AI-driven actions, such as ticket summarization, auto-replies, classification, and smart recommendations across JSM and Confluence. It allows support engineers and internal users to interact with AI directly from their native interfaces.

### Step 2: Search Open Websites/Domains

**Tactic:** [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[atlas/techniques/reconnaissance/search-open-websites-domains|AML.T0095: Search Open Websites/Domains]]

The researchers used a search query, “site:atlassian.net/servicedesk inurl:portal”,  to reveal organizations using Atlassian service portals as potential targets.

### Step 3: LLM Prompt Crafting

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

The researchers crafted a malicious prompt that requests data from all other support tickets be posted as a reply to the current ticket.

### Step 4: Prompt Infiltration via Public-Facing Application

**Tactic:** [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
**Technique:** [[atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093: Prompt Infiltration via Public-Facing Application]]

The researchers created a new service ticket containing the malicious prompt on the public Jira Service Management (JSM) portal of the victim identified during reconnaissance.

### Step 5: Indirect

**Tactic:** [[atlas/tactics/execution|AML.TA0005: Execution]]
**Technique:** [[atlas/techniques/execution/llm-prompt-injection/indirect|AML.T0051.001: Indirect]]

As part of their standard workflow, a support engineer at the victim organization used Claude Sonnet (which can interact with Jira via the Atlassian MCP server) to help them resolve the malicious ticket, causing the injection to be unknowingly executed.

### Step 6: AI Agent Tool Invocation

**Tactic:** [[atlas/tactics/privilege-escalation|AML.TA0012: Privilege Escalation]]
**Technique:** [[atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]]

The malicious prompt requested information accessible to the AI agent via Atlassian MCP tools, causing those tools to be invoked via MCP, granting the researchers increased privileges on the victim’s JSM instance.

### Step 7: AI Agent Tools

**Tactic:** [[atlas/tactics/collection|AML.TA0009: Collection]]
**Technique:** [[atlas/techniques/collection/data-from-ai-services/ai-agent-tools|AML.T0085.001: AI Agent Tools]]

The malicious prompt instructed that all details of other issues be collected. This invoked an Atlassian MCP tool that could access the Jira tickets and collect them.

### Step 8: Exfiltration via AI Agent Tool Invocation

**Tactic:** [[atlas/tactics/exfiltration|AML.TA0010: Exfiltration]]
**Technique:** [[atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]]

The malicious prompt instructed that the collected ticket details be posted in a reply to the ticket. This invoked an Atlassian MCP Tool which performed the requested action, exfiltrating the data where it was accessible to the researchers on the JSM portal.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]] | [[atlas/techniques/reconnaissance/search-victim-owned-websites|AML.T0003: Search Victim-Owned Websites]] |
| 2 | [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]] | [[atlas/techniques/reconnaissance/search-open-websites-domains|AML.T0095: Search Open Websites/Domains]] |
| 3 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]] |
| 4 | [[atlas/tactics/initial-access|AML.TA0004: Initial Access]] | [[atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093: Prompt Infiltration via Public-Facing Application]] |
| 5 | [[atlas/tactics/execution|AML.TA0005: Execution]] | [[atlas/techniques/execution/llm-prompt-injection/indirect|AML.T0051.001: Indirect]] |
| 6 | [[atlas/tactics/privilege-escalation|AML.TA0012: Privilege Escalation]] | [[atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] |
| 7 | [[atlas/tactics/collection|AML.TA0009: Collection]] | [[atlas/techniques/collection/data-from-ai-services/ai-agent-tools|AML.T0085.001: AI Agent Tools]] |
| 8 | [[atlas/tactics/exfiltration|AML.TA0010: Exfiltration]] | [[atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]] |



## External References

- Cato CTRL™ Threat Research: PoC Attack Targeting Atlassian’s Model Context Protocol (MCP) Introduces New “Living Off AI” Risk Available at: https://www.catonetworks.com/blog/cato-ctrl-poc-attack-targeting-atlassians-mcp/


## References

MITRE Corporation. *Living Off AI: Prompt Injection via Jira Service Management (AML.CS0039)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0039
