---
id: cost-harvesting
title: "AML.T0034: Cost Harvesting"
sidebar_label: "Cost Harvesting"
sidebar_position: 35
---

# AML.T0034: Cost Harvesting

Adversaries may target different AI services to send useless queries or computationally expensive inputs to increase the cost of running services at the victim organization.
Sponge examples are a particular type of adversarial data designed to maximize energy consumption and thus operating cost.

## Metadata

- **Technique ID:** AML.T0034
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** feasible

## Tactics (1)

- [[frameworks/atlas/tactics/impact|Impact]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Limit the number of queries users can perform in a given interval to hinder an attacker's ability to send computationally expensive inputs
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-in-production|AML.M0019: Control Access to AI Models and Data in Production]] — Access controls can limit API access and prevent cost harvesting.
