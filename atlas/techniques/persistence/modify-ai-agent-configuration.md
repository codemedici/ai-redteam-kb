---
id: modify-ai-agent-configuration
title: "AML.T0081: Modify AI Agent Configuration"
sidebar_label: "Modify AI Agent Configuration"
sidebar_position: 10
---

# AML.T0081: Modify AI Agent Configuration



Adversaries may modify the configuration files for AI agents on a system. This allows malicious changes to persist beyond the life of a single agent and affects any agents that share the configuration.

Configuration changes may include modifications to the system prompt, tampering with or replacing knowledge sources, modification to settings of connected tools, and more. Through those changes, an attacker could redirect outputs or tools to malicious services, embed covert instructions that exfiltrate data, or weaken security controls that normally restrict agent behavior.

## Metadata

- **Technique ID:** AML.T0081
- **Created:** September 30, 2025
- **Last Modified:** September 30, 2025
- **Maturity:** demonstrated



## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/persistence|AML.TA0006: Persistence]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|AML.CS0041: Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]

Users then pulled the latest version of the rules file, replacing their coding assistant’s configuration with the malicious one. The coding assistant’s behavior was modified, affecting all future code generation.



## References

MITRE Corporation. *Modify AI Agent Configuration (AML.T0081)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0081
