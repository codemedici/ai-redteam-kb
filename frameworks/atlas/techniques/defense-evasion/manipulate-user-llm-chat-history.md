---
id: manipulate-user-llm-chat-history
title: "AML.T0092: Manipulate User LLM Chat History"
sidebar_label: "Manipulate User LLM Chat History"
sidebar_position: 93
---

# AML.T0092: Manipulate User LLM Chat History

Adversaries may manipulate a user's large language model (LLM) chat history to cover the tracks of their malicious behavior. They may hide persistent changes they have made to the LLM's behavior, or obscure their attempts at discovering private information about the user.

To do so, adversaries may delete or edit existing messages or create new threads as part of their coverup. This is feasible if the adversary has the victim's authentication tokens for the backend LLM service or if they have direct access to the victim's chat interface. 

Chat interfaces (especially desktop interfaces) often do not show the injected prompt for any ongoing chat, as they update chat history only once when initially opening it. This can help the adversary's manipulations go unnoticed by the victim.

## Metadata

- **Technique ID:** AML.T0092
- **Created:** 2025-10-27
- **Last Modified:** 2025-11-04
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/defense-evasion|Defense Evasion]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AIKatz: Attacking LLM Desktop Applications]]
- [[frameworks/atlas/case-studies/exposed-clawdbot-control-interfaces-leads-to-credential-access-and-execution|Exposed ClawdBot Control Interfaces Leads to Credential Access and Execution]]
