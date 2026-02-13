---
id: organization-confusion-on-hugging-face
title: "AML.CS0027: Organization Confusion on Hugging Face"
type: case-study
sidebar_position: 28
---

# AML.CS0027: Organization Confusion on Hugging Face

[threlfall_hax](https://5stars217.github.io/), a security researcher, created organization accounts on Hugging Face, a public model repository, that impersonated real organizations. These false Hugging Face organization accounts looked legitimate so individuals from the impersonated organizations requested to join, believing the accounts to be an official site for employees to share models. This gave the researcher full access to any AI models uploaded by the employees, including the ability to replace models with malicious versions. The researcher demonstrated that they could embed malware into an AI model that provided them access to the victim organization's environment. From there, threat actors could execute a range of damaging attacks such as intellectual property theft or poisoning other AI models within the victim's environment.

## Metadata

- **ID:** AML.CS0027
- **Incident Date:** 2023-08-23
- **Type:** exercise
- **Target:** Hugging Face users
- **Actor:** threlfall_hax

## Procedure

### Step 1: Establish Accounts

**Technique:** [[frameworks/atlas/techniques/resource-development/establish-accounts|AML.T0021: Establish Accounts]]

The researcher registered an unverified "organization" account on Hugging Face that squats on the namespace of a targeted company.

### Step 2: Impersonation

**Technique:** [[frameworks/atlas/techniques/defense-evasion/impersonation|AML.T0073: Impersonation]]

Employees of the targeted company found and joined the fake Hugging Face organization. Since the organization account name is matches or appears to match the real organization, the employees were fooled into believing the account was official.

### Step 3: Full AI Model Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044: Full AI Model Access]]

The employees made use of the Hugging Face organizaion and uploaded private models. As owner of the Hugging Face account, the researcher has full read and write access to all of these uploaded models.

### Step 4: AI Intellectual Property Theft

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/external-harms-AI-IP-theft|AML.T0048.004: AI Intellectual Property Theft]]

With full access to the model, an adversary could steal valuable intellectual property in the form of AI models.

### Step 5: Embed Malware

**Technique:** [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-embed-malware|AML.T0018.002: Embed Malware]]

The researcher embedded [Sliver](https://github.com/BishopFox/sliver), an open source C2 server, into the target model. They added a `Lambda` layer to the model, which allows for arbitrary code to be run, and used an `exec()` call to execute the Sliver payload.

### Step 6: Publish Poisoned Models

**Technique:** [[frameworks/atlas/techniques/resource-development/publish-poisoned-models|AML.T0058: Publish Poisoned Models]]

The researcher re-uploaded the manipulated model to the Hugging Face repository.

### Step 7: Model

**Technique:** [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|AML.T0010.003: Model]]

The victim's AI model supply chain is now compromised. Users of the model repository will receive the adversary's model with embedded malware.

### Step 8: Unsafe AI Artifacts

**Technique:** [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011.000: Unsafe AI Artifacts]]

When any future user loads the model, the model automatically executes the adversary's payload.

### Step 9: Masquerading

**Technique:** [[frameworks/atlas/techniques/defense-evasion/masquerading|AML.T0074: Masquerading]]

The researcher named the Sliver process `training.bin` to disguise it as a legitimate model training process. Furthermore, the model still operates as normal, making it less likely a user will notice something is wrong.

### Step 10: Reverse Shell

**Technique:** [[frameworks/atlas/techniques/command-and-control/reverse-shell|AML.T0072: Reverse Shell]]

The Sliver implant grants the researcher a command and control channel so they can explore the victim's environment and continue the attack.

### Step 11: Unsecured Credentials

**Technique:** [[frameworks/atlas/techniques/credential-access/unsecured-credentials|AML.T0055: Unsecured Credentials]]

The researcher checked environment variables and searched Jupyter notebooks for API keys and other secrets.

### Step 12: Exfiltration via Cyber Means

**Technique:** [[frameworks/atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025: Exfiltration via Cyber Means]]

Discovered credentials could be exfiltrated via the Sliver implant.

### Step 13: Discover AI Artifacts

**Technique:** [[frameworks/atlas/techniques/discovery/discover-ai-artifacts|AML.T0007: Discover AI Artifacts]]

The researcher could have searched for AI models in the victim organization's environment.

### Step 14: Adversarial AI Attack Implementations

**Technique:** [[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-adversarial-AI-attacks|AML.T0016.000: Adversarial AI Attack Implementations]]

The researcher obtained [EasyEdit](https://github.com/zjunlp/EasyEdit), an open-source knowledge editing tool for large language models.

### Step 15: Poison AI Model

**Technique:** [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018.000: Poison AI Model]]

The researcher demonstrated that EasyEdit could be used to poison a `Llama-2-7-b` with false facts.

### Step 16: External Harms

**Technique:** [[frameworks/atlas/techniques/impact/external-harms|AML.T0048: External Harms]]

If the company's models were manipulated to produce false information, a variety of harms including financial and reputational could occur.

## References

1. [Model Confusion - Weaponizing ML models for red teams and bounty hunters](https://5stars217.github.io/2023-08-08-red-teaming-with-ml-models/#unexpected-benefits---organization-confusion)
