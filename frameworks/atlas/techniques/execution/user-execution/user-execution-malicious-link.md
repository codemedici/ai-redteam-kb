---
id: user-execution-malicious-link
title: "AML.T0011.003: Malicious Link"
sidebar_label: "Malicious Link"
sidebar_position: 11004
---

# AML.T0011.003: Malicious Link

An adversary may rely upon a user clicking a malicious link in order to gain execution. Users may be subjected to social engineering to get them to click on a link that will lead to code execution. This user action will typically be observed as follow-on behavior from Spearphishing Link. Clicking on a link may also lead to other execution techniques such as exploitation of a browser or application vulnerability via Exploitation for Client Execution. Links may also lead users to download files that require execution via Malicious File.

There are many ways an adversary can leverage malicious links to gain access to a victim system via an AI system. For example, an AI Agent that is configured to not validate website origin headers will accept connections from any website, allowing adversaries the ability to get around previously inaccessible network.

## Metadata

- **Technique ID:** AML.T0011.003
- **Created:** 2026-01-30
- **Last Modified:** 2026-02-05
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1204](https://attack.mitre.org/techniques/T1204/)

## Parent Technique

**Parent Technique:** AML.T0011 — User Execution

## Tactics (1)

- [[frameworks/atlas/tactics/execution|Execution]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/openclaw-1-click-remote-code-execution|OpenClaw 1-Click Remote Code Execution]]
