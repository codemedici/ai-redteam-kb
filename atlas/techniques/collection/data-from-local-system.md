---
id: data-from-local-system
title: "AML.T0037: Data from Local System"
sidebar_label: "Data from Local System"
sidebar_position: 3
---

# AML.T0037: Data from Local System



Adversaries may search local system sources, such as file systems and configuration files or local databases, to find files of interest and sensitive data prior to Exfiltration.

This can include basic fingerprinting information and sensitive data such as ssh keys.

## Metadata

- **Technique ID:** AML.T0037
- **Created:** May 13, 2021
- **Last Modified:** January 18, 2023
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1005](https://attack.mitre.org/techniques/T1005/)


## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/collection|AML.TA0009: Collection]]




## Case Studies (3)


The following case studies demonstrate this technique:

### [[atlas/case-studies/compromised-pytorch-dependency-chain|AML.CS0015: Compromised PyTorch Dependency Chain]]

The malicious package surveys the affected system for basic fingerprinting info (such as IP address, username, and current working directory), and steals further sensitive data, including:
- nameservers from `/etc/resolv.conf`
- hostname from `gethostname()`
- current username from `getlogin()`
- current working directory name from `getcwd()`
- environment variables
- `/etc/hosts`
- `/etc/passwd`
- the first 1000 files in the user's `$HOME` directory
- `$HOME/.gitconfig`
- `$HOME/.ssh/*.`

### [[atlas/case-studies/malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]

The Skynet malware attempts to collect `%HOMEPATH%\.ssh\known_hosts` and `C:/Windows/System32/Drivers/etc/hosts`.

### [[atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]

The LAMEHUG malware used the AI generated commands to collect system information (saved to `%PROGRAMDATA%\info\info.txt`) and recursively searched Documents, Desktop, and Downloads to stage files for exfiltration.



## References

MITRE Corporation. *Data from Local System (AML.T0037)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0037
