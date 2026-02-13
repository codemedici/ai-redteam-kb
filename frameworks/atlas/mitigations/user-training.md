---
id: user-training
title: "AML.M0018: User Training"
sidebar_label: "User Training"
sidebar_position: 19
type: mitigation
---

# AML.M0018: User Training

Educate AI model developers to on AI supply chain risks and potentially malicious AI artifacts.
Educate users on how to identify deepfakes and phishing attempts.

## Metadata

- **Mitigation ID:** AML.M0018
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Policy
- **ML Lifecycle:** Business and Data Understanding, Data Preparation, ML Model Engineering, ML Model Evaluation, Deployment, Monitoring and Maintenance

## Techniques (5)

- [[frameworks/atlas/techniques/execution/user-execution|AML.T0011: User Execution]] — Training users to be able to identify attempts at manipulation will make them less susceptible to performing techniques that cause the execution of malicious code.
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011.000: Unsafe AI Artifacts]] — Train users to identify attempts of manipulation to prevent them from running unsafe code which when executed could develop unsafe artifacts. These artifacts may have a detrimental effect on the system.
- [[frameworks/atlas/techniques/initial-access/phishing|AML.T0052: Phishing]] — Train users to identify phishing attempts by an adversary to reduce the risk of successful spearphishing, social engineering, and other techniques that involve user interaction.
- [[frameworks/atlas/techniques/initial-access/phishing/phishing-spearphishing-via-LLM|AML.T0052.000: Spearphishing via Social Engineering LLM]] — Train users to identify phishing attempts and understand that AI can be used to generate targeted and convincing messages.
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-malicious-package|AML.T0011.001: Malicious Package]] — Train users to identify attempts of manipulation to prevent them from running unsafe code from external packages.
