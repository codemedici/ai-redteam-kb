---
id: aikatz-attacking-llm-desktop-applications
title: "AML.CS0036: AIKatz: Attacking LLM Desktop Applications"
sidebar_label: "AIKatz: Attacking LLM Desktop Applications"
sidebar_position: 37
---

# AML.CS0036: AIKatz: Attacking LLM Desktop Applications

## Summary

Researchers at Lumia have demonstrated that it is possible to extract authentication tokens from the memory of LLM Desktop Applications. An attacker could then use those tokens to impersonate as the victim to the LLM backed, thereby gaining access to the victim’s conversations as well as the ability to interfere in future conversations. The attacker’s access would allow them the ability to directly inject prompts to change the LLM’s behavior, poison the LLM’s context to have persistent effects, manipulate the user’s conversation history to cover their tracks, and ultimately impact the confidentiality, integrity, and availability of the system. The researchers demonstrated this on Anthropic Claude, Microsoft M365 Copilot, and OpenAI ChatGPT.

Vendor Responses to Responsible Disclosure:
- Anthropic (HackerOne) - Closed as informational since local attack.
- Microsoft Security Response Center - Attack doesn’t bypass security boundaries for CVE.
- OpenAI (BugCrowd) - Closed as informational and noted that it’s up to Microsoft to patch this behavior.

## Metadata

- **Case Study ID:** AML.CS0036
- **Incident Date:** 2025
- **Type:** exercise
- **Target:** LLM Desktop Applications (Claude, ChatGPT, Copilot)
- **Actor:** Lumia Security

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Valid Accounts

**Technique:** [[frameworks/atlas/techniques/initial-access/valid-accounts|AML.T0012: Valid Accounts]]

The attacker required initial access to the victim system to carry out this attack.

### Step 2: Process Discovery

**Technique:** [[frameworks/atlas/techniques/discovery/process-discovery|AML.T0089: Process Discovery]]

The attacker enumerated all of the processes running on the victim’s machine and identified the processes belonging to LLM desktop applications.

### Step 3: OS Credential Dumping

**Technique:** [[frameworks/atlas/techniques/credential-access/os-credential-dumping|AML.T0090: OS Credential Dumping]]

The attacker attached or read memory directly from `/proc` (in Linux) or opened a handle to the LLM application’s process (in Windows). The attacker then scanned the process’s memory to extract the authentication token of the victim. This can be easily done by running a regex on every allocated memory page in the process.

### Step 4: Application Access Token

**Technique:** [[frameworks/atlas/techniques/lateral-movement/use-alternate-authentication-material/application-access-token|AML.T0091.000: Application Access Token]]

The attacker used the extracted token to authenticate themselves with the LLM backend service.

### Step 5: AI-Enabled Product or Service

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

The attacker has now obtained the access required to communicate with the LLM backend service as if they were the desktop client. This allowed them access to everything the user can do with the desktop application.

### Step 6: Direct

**Technique:** [[frameworks/atlas/techniques/execution/llm-prompt-injection/direct|AML.T0051.000: Direct]]

The attacker sent malicious prompts directly to the LLM under any ongoing conversation the victim has.

### Step 7: Thread

**Technique:** [[frameworks/atlas/techniques/persistence/ai-agent-context-poisoning/thread|AML.T0080.001: Thread]]

The attacker could craft malicious prompts that manipulate the context of a chat thread, an effect that would persist for the duration of the thread.

### Step 8: Memory

**Technique:** [[frameworks/atlas/techniques/persistence/ai-agent-context-poisoning/memory|AML.T0080.000: Memory]]

The attacker could then craft malicious prompts that manipulate the LLM’s memory to achieve a persistent effect. Any change in memory would also propagate to any new chat threads.

### Step 9: Manipulate User LLM Chat History

**Technique:** [[frameworks/atlas/techniques/defense-evasion/manipulate-user-llm-chat-history|AML.T0092: Manipulate User LLM Chat History]]

Many LLM desktop applications do not show the injected prompt for any ongoing chat, as they update chat history only once when initially opening it. This gave the attacker the opportunity to cover their tracks by manipulating the user’s conversation history directly via the LLM’s API. The attacker could also overwrite or delete messages to prevent detection of their actions.

### Step 10: Financial Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/financial-harm|AML.T0048.000: Financial Harm]]

The attacker could send spam messages while impersonating the victim. On a pay-per-token or action plans, this could increase the financial burden on the victim.

### Step 11: User Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/user-harm|AML.T0048.003: User Harm]]

The attacker could gain access to all of the victim’s activity with the LLM, including previous and ongoing chats, as well as any file or content uploaded to them.

### Step 12: Denial of AI Service

**Technique:** [[frameworks/atlas/techniques/impact/denial-of-ai-service|AML.T0029: Denial of AI Service]]

The attacker could delete all chats the victim has, and any they are opening, thereby preventing the victim from being able to interact with the LLM.

### Step 13: Denial of AI Service

**Technique:** [[frameworks/atlas/techniques/impact/denial-of-ai-service|AML.T0029: Denial of AI Service]]

The attacker could spam messages or prompts to reach the LLM’s rate-limits against bots, to cause it to ban the victim altogether.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/initial-access/valid-accounts|AML.T0012: Valid Accounts]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/discovery/process-discovery|AML.T0089: Process Discovery]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/credential-access/os-credential-dumping|AML.T0090: OS Credential Dumping]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/lateral-movement/use-alternate-authentication-material/application-access-token|AML.T0091.000: Application Access Token]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/execution/llm-prompt-injection/direct|AML.T0051.000: Direct]]

**Step 7:**
- Technique: [[frameworks/atlas/techniques/persistence/ai-agent-context-poisoning/thread|AML.T0080.001: Thread]]

**Step 8:**
- Technique: [[frameworks/atlas/techniques/persistence/ai-agent-context-poisoning/memory|AML.T0080.000: Memory]]

**Step 9:**
- Technique: [[frameworks/atlas/techniques/defense-evasion/manipulate-user-llm-chat-history|AML.T0092: Manipulate User LLM Chat History]]

**Step 10:**
- Technique: [[frameworks/atlas/techniques/impact/external-harms/financial-harm|AML.T0048.000: Financial Harm]]

**Step 11:**
- Technique: [[frameworks/atlas/techniques/impact/external-harms/user-harm|AML.T0048.003: User Harm]]

**Step 12:**
- Technique: [[frameworks/atlas/techniques/impact/denial-of-ai-service|AML.T0029: Denial of AI Service]]

**Step 13:**
- Technique: [[frameworks/atlas/techniques/impact/denial-of-ai-service|AML.T0029: Denial of AI Service]]

## External References

- AIKatz – All Your Chats Are Belong To Us Available at: https://www.lumia.security/blog/aikatz

## References

MITRE Corporation. *AIKatz: Attacking LLM Desktop Applications (AML.CS0036)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0036
