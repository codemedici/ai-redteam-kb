---
id: valid-accounts
title: "AML.T0012: Valid Accounts"
sidebar_label: "Valid Accounts"
sidebar_position: 6
---

# AML.T0012: Valid Accounts

Adversaries may obtain and abuse credentials of existing accounts as a means of gaining Initial Access.
Credentials may take the form of usernames and passwords of individual user accounts or API keys that provide access to various AI resources and services.

Compromised credentials may provide access to additional AI artifacts and allow the adversary to perform [[frameworks/atlas/techniques/discovery/discover-ai-artifacts|Discover AI Artifacts]].
Compromised credentials may also grant an adversary increased privileges such as write access to AI artifacts used during development or production.

## Metadata

- **Technique ID:** AML.T0012
- **Created:** January 24, 2022
- **Last Modified:** December 24, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1078](https://attack.mitre.org/techniques/T1078/)

## Tactics (2)

This technique supports the following tactics:

- 
- 

## Case Studies (7)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]

The team used a valid account to gain access to the network.

### [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]

The team gained access to the commercial face identification service and its API through a valid account.

### [[frameworks/atlas/case-studies/arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]

A victim user may mount their Google Drive into the compromised Colab notebook.  Typical reasons to connect machine learning notebooks to Google Drive include the ability to train on data stored there or to save model output files.

```
from google.colab import drive
drive.mount(''/content/drive'')
```

Upon execution, a popup appears to confirm access and warn about potential data access:

> This notebook is requesting access to your Google Drive files. Granting access to Google Drive will permit code executed in the notebook to modify files in your Google Drive. Make sure to review notebook code prior to allowing this access.

A victim user may nonetheless accept the popup and allow the compromised Colab notebook access to the victim''s Drive.  Permissions granted include:
- Create, edit, and delete access for all Google Drive files
- View Google Photos data
- View Google contacts

### [[frameworks/atlas/case-studies/llm-jacking|AML.CS0030: LLM Jacking]]

The compromised credentials gave the adversaries access to cloud environments where large language model (LLM) services were hosted.

### [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]

The researcher created a valid, non-admin user account within the Slack workspace.

### [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]

The attacker required initial access to the victim system to carry out this attack.

### [[frameworks/atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]

APT28 gained access to a compromised official email account.

## References

MITRE Corporation. *Valid Accounts (AML.T0012)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0012
