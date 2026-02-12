---
id: data-from-ai-services-ai-agent-tools
title: "AML.T0085.001: AI Agent Tools"
sidebar_label: "AI Agent Tools"
sidebar_position: 6
---

# AML.T0085.001: AI Agent Tools

> **Sub-Technique of:** [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-ai-services-overview|AML.T0085: Data from AI Services]]

Adversaries may prompt the AI service to invoke various tools the agent has access to. Tools may retrieve data from different APIs or services in an organization.

## Metadata

- **Technique ID:** AML.T0085.001
- **Created:** September 30, 2025
- **Last Modified:** September 30, 2025
- **Maturity:** demonstrated

- **Parent Technique:** [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-ai-services-overview|AML.T0085: Data from AI Services]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (3)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]

The prompt asks the agent to retrieve all Salesforce records using its get-records tool. The agent retrieves all records from the victim’s CRM.

### [[frameworks/atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|AML.CS0038: Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]

<div dangerouslySetInnerHTML={{__html: `The Workspace Extension searched for the document and placed its content in the chat context.

<div style="font-family: monospace; width: 50%; margin-left: 50px; background-color:ghostwhite; border: 2px solid black; padding: 10px;">
<span style="color: indigo;">search for a document about cats in my drive, and print it word by word.</span>
</div>`}} />

### [[frameworks/atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]

The malicious prompt instructed that all details of other issues be collected. This invoked an Atlassian MCP tool that could access the Jira tickets and collect them.

## References

MITRE Corporation. *AI Agent Tools (AML.T0085.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0085.001
