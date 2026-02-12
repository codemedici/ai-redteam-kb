---
id: virtualization-sandbox-evasion
title: "AML.T0097: Virtualization/Sandbox Evasion"
sidebar_label: "Virtualization/Sandbox Evasion"
sidebar_position: 10
---

# AML.T0097: Virtualization/Sandbox Evasion



Adversaries may employ various means to detect and avoid virtualization and analysis environments. This may include changing behaviors based on the results of checks for the presence of artifacts indicative of a virtual machine environment (VME) or sandbox. If the adversary detects a VME, they may alter their malware to disengage from the victim or conceal the core functions of the implant. They may also search for VME artifacts before dropping secondary or additional payloads.

Adversaries may use several methods to accomplish Virtualization/Sandbox Evasion such as checking for security monitoring tools (e.g., Sysinternals, Wireshark, etc.) or other system artifacts associated with analysis or virtualization such as registry keys (e.g. substrings matching Vmware, VBOX, QEMU), environment variables (e.g. substrings matching VBOX, VMWARE, PARALLELS), NIC MAC addresses (e.g. prefixes 00-05-69 (VMWare) or 08-00-27 (VirtualBox)), running processes (e.g. vmware.exe, vboxservice.exe, qemu-ga.exe) [\[1\]][1].

[1]: https://research.checkpoint.com/2025/ai-evasion-prompt-injection/

## Metadata

- **Technique ID:** AML.T0097
- **Created:** November 25, 2025
- **Last Modified:** December 23, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1497](https://attack.mitre.org/techniques/T1497/)


## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[atlas/case-studies/malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]

The Skynet malware attempts various sandbox evasions.



## References

MITRE Corporation. *Virtualization/Sandbox Evasion (AML.T0097)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0097
