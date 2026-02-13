---
id: alternate-auth-application-access-token
title: "AML.T0091.000: Application Access Token"
sidebar_label: "Application Access Token"
sidebar_position: 91001
---

# AML.T0091.000: Application Access Token

Adversaries may use stolen application access tokens to bypass the typical authentication process and access restricted accounts, information, or services on remote systems. These tokens are typically stolen from users or services and used in lieu of login credentials.

Application access tokens are used to make authorized API requests on behalf of a user or service and are commonly used to access resources in cloud, container-based applications, software-as-a-service (SaaS), and AI-as-a-service(AIaaS). They are commonly used for AI services such as chatbots, LLMs, and predictive inference APIs.

## Metadata

- **Technique ID:** AML.T0091.000
- **Created:** 2025-10-28
- **Last Modified:** 2025-12-23
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1550.001](https://attack.mitre.org/techniques/T1550/001/)

## Parent Technique

**Parent Technique:** AML.T0091 — Use Alternate Authentication Material

## Tactics (1)

- [[frameworks/atlas/tactics/lateral-movement|Lateral Movement]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AIKatz: Attacking LLM Desktop Applications]]
