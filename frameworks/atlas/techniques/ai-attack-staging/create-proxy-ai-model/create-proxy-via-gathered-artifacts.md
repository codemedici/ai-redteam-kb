---
id: create-proxy-via-gathered-artifacts
title: "AML.T0005.000: Train Proxy via Gathered AI Artifacts"
sidebar_label: "Train Proxy via Gathered AI Artifacts"
sidebar_position: 5001
---

# AML.T0005.000: Train Proxy via Gathered AI Artifacts

Proxy models may be trained from AI artifacts (such as data, model architectures, and pre-trained models) that are representative of the target model gathered by the adversary.
This can be used to develop attacks that require higher levels of access than the adversary has available or as a means to validate pre-existing attacks without interacting with the target model.

## Metadata

- **Technique ID:** AML.T0005.000
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0005 — Create Proxy AI Model

## Tactics (1)

- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/limit-public-release-of-information|AML.M0000: Limit Public Release of Information]] — Limiting release of technical information about a model and training data can reduce an adversary's ability to create an accurate proxy model.
- [[frameworks/atlas/mitigations/limit-model-artifact-release|AML.M0001: Limit Model Artifact Release]] — Limiting the release of model artifacts can reduce an adversary's ability to create an accurate proxy model.

## Case Studies (1)

- [[frameworks/atlas/case-studies/gpt-2-model-replication|GPT-2 Model Replication]]
