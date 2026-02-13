---
id: supply-chain-compromise-model
title: "AML.T0010.001: AI Software"
sidebar_label: "AI Software"
sidebar_position: 10002
---

# AML.T0010.001: AI Software

Adversaries may target software packages that are commonly used in AI-enabled systems or are part of the AI DevOps lifecycle. This can include deep learning frameworks used to build AI models (e.g. PyTorch, TensorFlow, Jax), generative AI integration frameworks (e.g. LangChain, LangFlow), inference engines, AI DevOps tools, and Model Context Protocol servers, which give AI agents access to tools and data resources. They may also target the dependency chains of any of these software packages [\[1\]][1]. Additionally, adversaries may target specific components used by AI software such as configuration files [\[2\]][2] or example usage of AI tools, which may be distributed in Jupyter notebooks [\[3\]][3].

Adversaries may compromise legitimate packages [\[4\]][4] or publish malicious software to a namesquatted location [\[1\]][1]. They may target package names that are hallucinated by large language models [\[5\]][5] (see: Publish Hallucinated Entities). They may also perform a "rugpull" in which they first publish a legitimate package and then publish a malicious version once they reach a critical mass of users [\[6\]][6].

[1]: https://pytorch.org/blog/compromised-nightly-dependency/ "Compromised PyTorch-nightly dependency chain between December 25th and December 30th, 2022."
[2]: https://www.pillar.security/blog/new-vulnerability-in-github-copilot-and-cursor-how-hackers-can-weaponize-code-agents "New Vulnerability in GitHub Copilot and Cursor: How Hackers Can Weaponize Code Agents"
[3]: https://medium.com/mlearning-ai/careful-who-you-colab-with-fa8001f933e7 "Careful Who You Colab With: abusing google colaboratory"
[4]: https://aws.amazon.com/security/security-bulletins/AWS-2025-015/ "Security Update for Amazon Q Developer Extension for Visual Studio Code (Version #1.84)"
[5]: https://www.trendmicro.com/vinfo/us/security/news/cybercrime-and-digital-threats/slopsquatting-when-ai-agents-hallucinate-malicious-packages "Slopsquatting: When AI Agents Hallucinate Malicious Packages"
[6]: https://www.koi.ai/blog/postmark-mcp-npm-malicious-backdoor-email-theft "First Malicious MCP in the Wild: The Postmark Backdoor That's Stealing Your Emails"

## Metadata

- **Technique ID:** AML.T0010.001
- **Created:** 2021-05-13
- **Last Modified:** 2026-01-29
- **Maturity:** realized

## Parent Technique

**Parent Technique:** AML.T0010 — AI Supply Chain Compromise

## Tactics (1)

- [[frameworks/atlas/tactics/initial-access|Initial Access]]

## Case Studies (6)

- [[frameworks/atlas/case-studies/compromised-pytorch-dependency-chain|Compromised PyTorch Dependency Chain]]
- [[frameworks/atlas/case-studies/arbitrary-code-execution-with-google-colab|Arbitrary Code Execution with Google Colab]]
- [[frameworks/atlas/case-studies/chatgpt-package-hallucination|ChatGPT Package Hallucination]]
- [[frameworks/atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[frameworks/atlas/case-studies/code-to-deploy-destructive-ai-agent-discovered-in-amazon-q-vs-code-extension|Code to Deploy Destructive AI Agent Discovered in Amazon Q VS Code Extension]]
- [[frameworks/atlas/case-studies/supply-chain-compromise-via-poisoned-clawdbot-skill|Supply Chain Compromise via Poisoned ClawdBot Skill]]
