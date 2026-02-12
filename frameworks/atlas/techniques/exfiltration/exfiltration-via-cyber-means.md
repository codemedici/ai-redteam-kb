---
id: exfiltration-via-cyber-means
title: "AML.T0025: Exfiltration via Cyber Means"
sidebar_label: "Exfiltration via Cyber Means"
sidebar_position: 5
---

# AML.T0025: Exfiltration via Cyber Means

Adversaries may exfiltrate AI artifacts or other information relevant to their goals via traditional cyber means.

See the ATT&CK [Exfiltration](https://attack.mitre.org/tactics/TA0010/) tactic for more information.

## Metadata

- **Technique ID:** AML.T0025
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (7)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]

The team exfiltrated the model and data via traditional means.

### [[frameworks/atlas/case-studies/compromised-pytorch-dependency-chain|AML.CS0015: Compromised PyTorch Dependency Chain]]

All gathered information, including file contents, is uploaded via encrypted DNS queries to the domain `*[dot]h4ck[dot]cfd`, using the DNS server `wheezy[dot]io`.

### [[frameworks/atlas/case-studies/arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]

As a result of Google Drive access, the adversary may open a server to exfiltrate private data or ML model artifacts.

An example from the referenced article shows the download, installation, and usage of `ngrok`, a server application, to open an adversary-accessible URL to the victim's Google Drive and all its files.

### [[frameworks/atlas/case-studies/shadowray|AML.CS0023: ShadowRay]]

AI artifacts, credentials, and other valuable information can be exfiltrated via cyber means.

The researchers found evidence of reverse shells on vulnerable clusters. They can be used to maintain persistence, continue to run arbitrary code, and exfiltrate.

### [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

Discovered credentials could be exfiltrated via the Sliver implant.

### [[frameworks/atlas/case-studies/malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]

The Skynet malware sets up a Tor proxy to exfiltrate the collected files.

Note: The collected files were only printed to stdout and not successfully exfiltrated.

### [[frameworks/atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]

The LAMEHUG malware exfiltrated collected data to attacker controlled servers via SFTP or HTTP POST requests.

## References

MITRE Corporation. *Exfiltration via Cyber Means (AML.T0025)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0025
