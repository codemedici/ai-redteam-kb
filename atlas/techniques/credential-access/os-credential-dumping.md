---
id: os-credential-dumping
title: "AML.T0090: OS Credential Dumping"
sidebar_label: "OS Credential Dumping"
sidebar_position: 4
---

# AML.T0090: OS Credential Dumping



Adversaries may extract credentials from OS caches, application memory, or other sources on a compromised system. Credentials are often in the form of a hash or clear text, and can include usernames and passwords, application tokens, or other authentication keys.

Credentials can be used to perform [[lateral-movement|Lateral Movement]] to access other AI services such as AI agents, LLMs, or AI inference APIs. Credentials could also give an adversary access to other software tools and data sources that are part of the AI DevOps lifecycle.

## Metadata

- **Technique ID:** AML.T0090
- **Created:** October 27, 2025
- **Last Modified:** November 4, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1003](https://attack.mitre.org/techniques/T1003/)


## Tactics (1)

This technique supports the following tactics:


- [[credential-access|AML.TA0013: Credential Access]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]

The attacker attached or read memory directly from `/proc` (in Linux) or opened a handle to the LLM application’s process (in Windows). The attacker then scanned the process’s memory to extract the authentication token of the victim. This can be easily done by running a regex on every allocated memory page in the process.



## References

MITRE Corporation. *OS Credential Dumping (AML.T0090)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0090
