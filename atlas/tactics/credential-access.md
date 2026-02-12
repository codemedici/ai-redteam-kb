---
id: credential-access
title: "AML.TA0013: Credential Access"
sidebar_label: "Credential Access"
sidebar_position: 9
---

# AML.TA0013: Credential Access

The adversary is trying to steal account names and passwords.

Credential Access consists of techniques for stealing credentials like account names and passwords. Techniques used to get credentials include keylogging or credential dumping. Using legitimate credentials can give adversaries access to systems, make them harder to detect, and provide the opportunity to create more accounts to help achieve their goals.

## Metadata

- **Tactic ID:** AML.TA0013
- **Created:** October 25, 2023
- **Last Modified:** October 25, 2023
- **MITRE ATT&CK Reference:** [TA0006](https://attack.mitre.org/tactics/TA0006/)

## Techniques (5)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[atlas/techniques/credential-access/unsecured-credentials|AML.T0055]] | Unsecured Credentials | realized |
| [[atlas/techniques/credential-access/rag-credential-harvesting|AML.T0082]] | RAG Credential Harvesting | demonstrated |
| [[atlas/techniques/credential-access/credentials-from-ai-agent-configuration|AML.T0083]] | Credentials from AI Agent Configuration | feasible |
| [[atlas/techniques/credential-access/os-credential-dumping|AML.T0090]] | OS Credential Dumping | demonstrated |
| [[atlas/techniques/credential-access/ai-agent-tool-credential-harvesting|AML.T0098]] | AI Agent Tool Credential Harvesting | feasible |


## Case Studies (7)


The following case studies demonstrate this tactic:

- [[atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|AML.CS0016: Achieving Code Execution in MathGPT via Prompt Injection]]
- [[atlas/case-studies/shadowray|AML.CS0023: ShadowRay]]
- [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[atlas/case-studies/llm-jacking|AML.CS0030: LLM Jacking]]
- [[atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[atlas/case-studies/aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]
- [[atlas/case-studies/malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]


## References

MITRE Corporation. *Credential Access (AML.TA0013)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0013
