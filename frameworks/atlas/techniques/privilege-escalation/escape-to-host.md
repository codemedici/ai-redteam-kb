---
id: escape-to-host
title: "AML.T0105: Escape to Host"
sidebar_label: "Escape to Host"
sidebar_position: 106
---

# AML.T0105: Escape to Host

Adversaries may break out of a container or virtualized environment to gain access to the underlying host. This can allow an adversary access to other containerized or virtualized resources from the host level or to the host itself. In principle, containerized / virtualized resources should provide a clear separation of application functionality and be isolated from the host environment.

There are many ways an adversary may escape from a container or sandbox environment via AI Systems. For example, modifying an AI Agent's configuration to disable safety features or user confirmations could allow the adversary to invoke tools to be run on host environments rather than in the sandbox.

## Metadata

- **Technique ID:** AML.T0105
- **Created:** 2026-01-30
- **Last Modified:** 2026-01-30
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1611](https://attack.mitre.org/techniques/T1611/)

## Tactics (1)

- [[frameworks/atlas/tactics/privilege-escalation|Privilege Escalation]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/openclaw-1-click-remote-code-execution|OpenClaw 1-Click Remote Code Execution]]
