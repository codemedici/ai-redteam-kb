---
id: command-and-scripting-interpreter
title: "AML.T0050: Command and Scripting Interpreter"
sidebar_label: "Command and Scripting Interpreter"
sidebar_position: 3
---

# AML.T0050: Command and Scripting Interpreter



Adversaries may abuse command and script interpreters to execute commands, scripts, or binaries. These interfaces and languages provide ways of interacting with computer systems and are a common feature across many different platforms. Most systems come with some built-in command-line interface and scripting capabilities, for example, macOS and Linux distributions include some flavor of Unix Shell while Windows installations include the Windows Command Shell and PowerShell.

There are also cross-platform interpreters such as Python, as well as those commonly associated with client applications such as JavaScript and Visual Basic.

Adversaries may abuse these technologies in various ways as a means of executing arbitrary commands. Commands and scripts can be embedded in Initial Access payloads delivered to victims as lure documents or as secondary payloads downloaded from an existing C2. Adversaries may also execute commands through interactive terminals/shells, as well as utilize various Remote Services in order to achieve remote Execution.

## Metadata

- **Technique ID:** AML.T0050
- **Created:** February 28, 2023
- **Last Modified:** October 12, 2023
- **Maturity:** feasible
- **MITRE ATT&CK Reference:** [T1059](https://attack.mitre.org/techniques/T1059/)


## Tactics (1)

This technique supports the following tactics:


- [[execution|AML.TA0005: Execution]]




## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *Command and Scripting Interpreter (AML.T0050)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0050
