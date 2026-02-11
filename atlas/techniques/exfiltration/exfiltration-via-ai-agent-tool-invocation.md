---
id: exfiltration-via-ai-agent-tool-invocation
title: "AML.T0086: Exfiltration via AI Agent Tool Invocation"
sidebar_label: "Exfiltration via AI Agent Tool Invocation"
sidebar_position: 9
---

# AML.T0086: Exfiltration via AI Agent Tool Invocation



Adversaries may use prompts to invoke an agent's tool capable of performing write operations to exfiltrate data. Sensitive information can be encoded into the tool's input parameters and transmitted as part of a seemingly legitimate action. Variants include sending emails, creating or modifying documents, updating CRM records, or even generating media such as images or videos.

## Metadata

- **Technique ID:** AML.T0086
- **Created:** September 30, 2025
- **Last Modified:** September 30, 2025
- **Maturity:** demonstrated



## Tactics (1)

This technique supports the following tactics:


- [[exfiltration|AML.TA0010: Exfiltration]]




## Case Studies (2)


The following case studies demonstrate this technique:

### [[data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]

The prompt asks the agent to email the results to an address of the researcher’s choosing using its email tool. The researchers successfully exfiltrate their target data via the tool invocation.

### [[living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]

The malicious prompt instructed that the collected ticket details be posted in a reply to the ticket. This invoked an Atlassian MCP Tool which performed the requested action, exfiltrating the data where it was accessible to the researchers on the JSM portal.



## References

MITRE Corporation. *Exfiltration via AI Agent Tool Invocation (AML.T0086)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0086
