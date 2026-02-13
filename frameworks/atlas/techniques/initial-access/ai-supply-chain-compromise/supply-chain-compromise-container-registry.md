---
id: supply-chain-compromise-container-registry
title: "AML.T0010.004: Container Registry"
sidebar_label: "Container Registry"
sidebar_position: 10005
---

# AML.T0010.004: Container Registry

An adversary may compromise a victim's container registry by pushing a manipulated container image and overwriting an existing container name and/or tag. Users of the container registry as well as automated CI/CD pipelines may pull the adversary's container image, compromising their AI Supply Chain. This can affect development and deployment environments.

Container images may include AI models, so the compromised image could have an AI model which was manipulated by the adversary (See [Manipulate AI Model](/techniques/AML.T0018)).

## Metadata

- **Technique ID:** AML.T0010.004
- **Created:** 2024-04-11
- **Last Modified:** 2024-04-11
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0010 — AI Supply Chain Compromise

## Tactics (1)

- [[frameworks/atlas/tactics/initial-access|Initial Access]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AI Model Tampering via Supply Chain Attack]]
