---
id: acquire-infrastructure-consumer-hardware
title: "AML.T0008.002: Domains"
sidebar_label: "Domains"
sidebar_position: 8003
---

# AML.T0008.002: Domains

Adversaries may acquire domains that can be used during targeting. Domain names are the human readable names used to represent one or more IP addresses. They can be purchased or, in some cases, acquired for free.

Adversaries may use acquired domains for a variety of purposes (see [ATT&CK](https://attack.mitre.org/techniques/T1583/001/)). Large AI datasets are often distributed as a list of URLs to individual datapoints. Adversaries may acquire expired domains that are included in these datasets and replace individual datapoints with poisoned examples ([Publish Poisoned Datasets](/techniques/AML.T0019)).

## Metadata

- **Technique ID:** AML.T0008.002
- **Created:** 2025-03-12
- **Last Modified:** 2025-03-12
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1583.001](https://attack.mitre.org/techniques/T1583/001/)

## Parent Technique

**Parent Technique:** AML.T0008 — Acquire Infrastructure

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/web-scale-data-poisoning-split-view-attack|Web-Scale Data Poisoning: Split-View Attack]]
- [[frameworks/atlas/case-studies/supply-chain-compromise-via-poisoned-clawdbot-skill|Supply Chain Compromise via Poisoned ClawdBot Skill]]
