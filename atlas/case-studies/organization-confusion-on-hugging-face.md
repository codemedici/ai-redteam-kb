---
id: organization-confusion-on-hugging-face
title: "AML.CS0027: Organization Confusion on Hugging Face"
sidebar_label: "Organization Confusion on Hugging Face"
sidebar_position: 28
---

# AML.CS0027: Organization Confusion on Hugging Face

## Summary

[threlfall_hax](https://5stars217.github.io/), a security researcher, created organization accounts on Hugging Face, a public model repository, that impersonated real organizations. These false Hugging Face organization accounts looked legitimate so individuals from the impersonated organizations requested to join, believing the accounts to be an official site for employees to share models. This gave the researcher full access to any AI models uploaded by the employees, including the ability to replace models with malicious versions. The researcher demonstrated that they could embed malware into an AI model that provided them access to the victim organization's environment. From there, threat actors could execute a range of damaging attacks such as intellectual property theft or poisoning other AI models within the victim's environment.

## Metadata

- **Case Study ID:** AML.CS0027
- **Incident Date:** 2023
- **Type:** exercise
- **Target:** Hugging Face users
- **Actor:** threlfall_hax

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Establish Accounts

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/establish-accounts|AML.T0021: Establish Accounts]]

The researcher registered an unverified "organization" account on Hugging Face that squats on the namespace of a targeted company.

### Step 2: Impersonation

**Tactic:** [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]]
**Technique:** [[atlas/techniques/defense-evasion/impersonation|AML.T0073: Impersonation]]

Employees of the targeted company found and joined the fake Hugging Face organization. Since the organization account name is matches or appears to match the real organization, the employees were fooled into believing the account was official.

### Step 3: Full AI Model Access

**Tactic:** [[atlas/tactics/ai-model-access|AML.TA0000: AI Model Access]]
**Technique:** [[atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044: Full AI Model Access]]

The employees made use of the Hugging Face organizaion and uploaded private models. As owner of the Hugging Face account, the researcher has full read and write access to all of these uploaded models.

### Step 4: AI Intellectual Property Theft

**Tactic:** [[atlas/tactics/impact|AML.TA0011: Impact]]
**Technique:** [[atlas/techniques/impact/external-harms/ai-intellectual-property-theft|AML.T0048.004: AI Intellectual Property Theft]]

With full access to the model, an adversary could steal valuable intellectual property in the form of AI models.

### Step 5: Embed Malware

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/persistence/manipulate-ai-model/embed-malware|AML.T0018.002: Embed Malware]]

The researcher embedded [Sliver](https://github.com/BishopFox/sliver), an open source C2 server, into the target model. They added a `Lambda` layer to the model, which allows for arbitrary code to be run, and used an `exec()` call to execute the Sliver payload.

### Step 6: Publish Poisoned Models

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/publish-poisoned-models|AML.T0058: Publish Poisoned Models]]

The researcher re-uploaded the manipulated model to the Hugging Face repository.

### Step 7: Model

**Tactic:** [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
**Technique:** [[atlas/techniques/initial-access/ai-supply-chain-compromise/model|AML.T0010.003: Model]]

The victim's AI model supply chain is now compromised. Users of the model repository will receive the adversary's model with embedded malware.

### Step 8: Unsafe AI Artifacts

**Tactic:** [[atlas/tactics/execution|AML.TA0005: Execution]]
**Technique:** [[atlas/techniques/execution/user-execution/unsafe-ai-artifacts|AML.T0011.000: Unsafe AI Artifacts]]

When any future user loads the model, the model automatically executes the adversary's payload.

### Step 9: Masquerading

**Tactic:** [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]]
**Technique:** [[atlas/techniques/defense-evasion/masquerading|AML.T0074: Masquerading]]

The researcher named the Sliver process `training.bin` to disguise it as a legitimate model training process. Furthermore, the model still operates as normal, making it less likely a user will notice something is wrong.

### Step 10: Reverse Shell

**Tactic:** [[atlas/tactics/command-and-control|AML.TA0014: Command and Control]]
**Technique:** [[atlas/techniques/command-and-control/reverse-shell|AML.T0072: Reverse Shell]]

The Sliver implant grants the researcher a command and control channel so they can explore the victim's environment and continue the attack.

### Step 11: Unsecured Credentials

**Tactic:** [[atlas/tactics/credential-access|AML.TA0013: Credential Access]]
**Technique:** [[atlas/techniques/credential-access/unsecured-credentials|AML.T0055: Unsecured Credentials]]

The researcher checked environment variables and searched Jupyter notebooks for API keys and other secrets.

### Step 12: Exfiltration via Cyber Means

**Tactic:** [[atlas/tactics/exfiltration|AML.TA0010: Exfiltration]]
**Technique:** [[atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025: Exfiltration via Cyber Means]]

Discovered credentials could be exfiltrated via the Sliver implant.

### Step 13: Discover AI Artifacts

**Tactic:** [[atlas/tactics/discovery|AML.TA0008: Discovery]]
**Technique:** [[atlas/techniques/discovery/discover-ai-artifacts|AML.T0007: Discover AI Artifacts]]

The researcher could have searched for AI models in the victim organization's environment.

### Step 14: Adversarial AI Attack Implementations

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/obtain-capabilities/adversarial-ai-attack-implementations|AML.T0016.000: Adversarial AI Attack Implementations]]

The researcher obtained [EasyEdit](https://github.com/zjunlp/EasyEdit), an open-source knowledge editing tool for large language models.

### Step 15: Poison AI Model

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/persistence/manipulate-ai-model/poison-ai-model|AML.T0018.000: Poison AI Model]]

The researcher demonstrated that EasyEdit could be used to poison a `Llama-2-7-b` with false facts.

### Step 16: External Harms

**Tactic:** [[atlas/tactics/impact|AML.TA0011: Impact]]
**Technique:** [[atlas/techniques/impact/external-harms/external-harms-overview|AML.T0048: External Harms]]

If the company's models were manipulated to produce false information, a variety of harms including financial and reputational could occur.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/establish-accounts|AML.T0021: Establish Accounts]] |
| 2 | [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]] | [[atlas/techniques/defense-evasion/impersonation|AML.T0073: Impersonation]] |
| 3 | [[atlas/tactics/ai-model-access|AML.TA0000: AI Model Access]] | [[atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044: Full AI Model Access]] |
| 4 | [[atlas/tactics/impact|AML.TA0011: Impact]] | [[atlas/techniques/impact/external-harms/ai-intellectual-property-theft|AML.T0048.004: AI Intellectual Property Theft]] |
| 5 | [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[atlas/techniques/persistence/manipulate-ai-model/embed-malware|AML.T0018.002: Embed Malware]] |
| 6 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/publish-poisoned-models|AML.T0058: Publish Poisoned Models]] |
| 7 | [[atlas/tactics/initial-access|AML.TA0004: Initial Access]] | [[atlas/techniques/initial-access/ai-supply-chain-compromise/model|AML.T0010.003: Model]] |
| 8 | [[atlas/tactics/execution|AML.TA0005: Execution]] | [[atlas/techniques/execution/user-execution/unsafe-ai-artifacts|AML.T0011.000: Unsafe AI Artifacts]] |
| 9 | [[atlas/tactics/defense-evasion|AML.TA0007: Defense Evasion]] | [[atlas/techniques/defense-evasion/masquerading|AML.T0074: Masquerading]] |
| 10 | [[atlas/tactics/command-and-control|AML.TA0014: Command and Control]] | [[atlas/techniques/command-and-control/reverse-shell|AML.T0072: Reverse Shell]] |
| 11 | [[atlas/tactics/credential-access|AML.TA0013: Credential Access]] | [[atlas/techniques/credential-access/unsecured-credentials|AML.T0055: Unsecured Credentials]] |
| 12 | [[atlas/tactics/exfiltration|AML.TA0010: Exfiltration]] | [[atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025: Exfiltration via Cyber Means]] |
| 13 | [[atlas/tactics/discovery|AML.TA0008: Discovery]] | [[atlas/techniques/discovery/discover-ai-artifacts|AML.T0007: Discover AI Artifacts]] |
| 14 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/obtain-capabilities/adversarial-ai-attack-implementations|AML.T0016.000: Adversarial AI Attack Implementations]] |
| 15 | [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[atlas/techniques/persistence/manipulate-ai-model/poison-ai-model|AML.T0018.000: Poison AI Model]] |
| 16 | [[atlas/tactics/impact|AML.TA0011: Impact]] | [[atlas/techniques/impact/external-harms/external-harms-overview|AML.T0048: External Harms]] |



## External References

- Model Confusion - Weaponizing ML models for red teams and bounty hunters Available at: https://5stars217.github.io/2023-08-08-red-teaming-with-ml-models/#unexpected-benefits---organization-confusion


## References

MITRE Corporation. *Organization Confusion on Hugging Face (AML.CS0027)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0027
