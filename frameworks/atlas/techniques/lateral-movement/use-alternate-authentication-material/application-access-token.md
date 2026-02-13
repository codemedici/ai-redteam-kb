---
id: use-alternate-authentication-material-application-access-token
title: "AML.T0091.000: Application Access Token"
sidebar_label: "Application Access Token"
sidebar_position: 2
---

# AML.T0091.000: Application Access Token

> **Sub-Technique of:** [[frameworks/atlas/techniques/lateral-movement/use-alternate-authentication-material/use-alternate-authentication-material|AML.T0091: Use Alternate Authentication Material]]

Adversaries may use stolen application access tokens to bypass the typical authentication process and access restricted accounts, information, or services on remote systems. These tokens are typically stolen from users or services and used in lieu of login credentials.

Application access tokens are used to make authorized API requests on behalf of a user or service and are commonly used to access resources in cloud, container-based applications, software-as-a-service (SaaS), and AI-as-a-service(AIaaS). They are commonly used for AI services such as chatbots, LLMs, and predictive inference APIs.

## Metadata

- **Technique ID:** AML.T0091.000
- **Created:** October 28, 2025
- **Last Modified:** December 23, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1550.001](https://attack.mitre.org/techniques/T1550/001/)
- **Parent Technique:** [[frameworks/atlas/techniques/lateral-movement/use-alternate-authentication-material/use-alternate-authentication-material|AML.T0091: Use Alternate Authentication Material]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (1)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]

The attacker used the extracted token to authenticate themselves with the LLM backend service.

## References

MITRE Corporation. *Application Access Token (AML.T0091.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0091.000
