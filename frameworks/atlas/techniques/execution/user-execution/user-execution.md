---
id: user-execution
title: "AML.T0011: User Execution"
sidebar_label: "User Execution"
sidebar_position: 1
---

# AML.T0011: User Execution



An adversary may rely upon specific actions by a user in order to gain execution.
Users may inadvertently execute unsafe code introduced via [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/ai-supply-chain-compromise|AI Supply Chain Compromise]].
Users may be subjected to social engineering to get them to execute malicious code by, for example, opening a malicious document file or link.

## Metadata

- **Technique ID:** AML.T0011
- **Created:** May 13, 2021
- **Last Modified:** January 18, 2023
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1204](https://attack.mitre.org/techniques/T1204/)


## Tactics (1)

This technique supports the following tactics:


- [[frameworks/atlas/tactics/execution|AML.TA0005: Execution]]



## Sub-Techniques (2)

- [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011.000: Unsafe AI Artifacts]]
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-malicious-package|AML.T0011.001: Malicious Package]]


## Case Studies (2)


The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]

A victim user may unwittingly execute malicious code provided as part of a compromised Colab notebook.  Malicious code can be obfuscated or hidden in other files that the notebook downloads.

### [[frameworks/atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]

The attachment contained an executable file with a .pif extension, created using PyInstaller from Python source code which CERT-UA classified it as LAMEHUG malware. Files with the .pif extension are executable on Windows.



## References

MITRE Corporation. *User Execution (AML.T0011)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0011
