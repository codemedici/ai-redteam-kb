---
id: manipulate-user-llm-chat-history
title: "AML.T0092: Manipulate User LLM Chat History"
sidebar_label: "Manipulate User LLM Chat History"
sidebar_position: 8
---

# AML.T0092: Manipulate User LLM Chat History



Adversaries may manipulate a user's large language model (LLM) chat history to cover the tracks of their malicious behavior. They may hide persistent changes they have made to the LLM's behavior, or obscure their attempts at discovering private information about the user.

To do so, adversaries may delete or edit existing messages or create new threads as part of their coverup. This is feasible if the adversary has the victim's authentication tokens for the backend LLM service or if they have direct access to the victim's chat interface. 

Chat interfaces (especially desktop interfaces) often do not show the injected prompt for any ongoing chat, as they update chat history only once when initially opening it. This can help the adversary's manipulations go unnoticed by the victim.

## Metadata

- **Technique ID:** AML.T0092
- **Created:** October 27, 2025
- **Last Modified:** November 4, 2025
- **Maturity:** demonstrated



## Tactics (1)

This technique supports the following tactics:


- [[defense-evasion|AML.TA0007: Defense Evasion]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]

Many LLM desktop applications do not show the injected prompt for any ongoing chat, as they update chat history only once when initially opening it. This gave the attacker the opportunity to cover their tracks by manipulating the user’s conversation history directly via the LLM’s API. The attacker could also overwrite or delete messages to prevent detection of their actions.



## References

MITRE Corporation. *Manipulate User LLM Chat History (AML.T0092)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0092
