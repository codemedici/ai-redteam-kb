---
id: ai-supply-chain-compromise-container-registry
title: "AML.T0010.004: Container Registry"
sidebar_label: "Container Registry"
sidebar_position: 11
---

# AML.T0010.004: Container Registry

An adversary may compromise a victim's container registry by pushing a manipulated container image and overwriting an existing container name and/or tag. Users of the container registry as well as automated CI/CD pipelines may pull the adversary's container image, compromising their AI Supply Chain. This can affect development and deployment environments.

Container images may include AI models, so the compromised image could have an AI model which was manipulated by the adversary (See Manipulate AI Model).

## Metadata

- **Technique ID:** AML.T0010.004
- **Created:** April 11, 2024
- **Last Modified:** April 11, 2024
- **Maturity:** demonstrated

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (1)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]

Because many of the misconfigured container registries allowed write access, the adversary's container image with the manipulated model could be pushed with the same name and tag as the original. This compromises the victim's AI supply chain, where automated CI/CD pipelines could pull the adversary's images.

## References

MITRE Corporation. *Container Registry (AML.T0010.004)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0010.004
