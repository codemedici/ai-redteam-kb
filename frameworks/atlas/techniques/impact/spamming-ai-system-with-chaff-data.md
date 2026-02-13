---
id: spamming-ai-system-with-chaff-data
title: "AML.T0046: Spamming AI System with Chaff Data"
sidebar_label: "Spamming AI System with Chaff Data"
sidebar_position: 47
---

# AML.T0046: Spamming AI System with Chaff Data

Adversaries may spam the AI system with chaff data that causes increase in the number of detections.
This can cause analysts at the victim organization to waste time reviewing and correcting incorrect inferences.

Adversaries may also spam AI agents with excessive low-severity auditable events or agentic actions that require a human-in-the-loop, wasting time for the victim organization in human review of the agentic AI system.

## Metadata

- **Technique ID:** AML.T0046
- **Created:** 2021-05-13
- **Last Modified:** 2025-12-18
- **Maturity:** feasible

## Tactics (1)

- [[frameworks/atlas/tactics/impact|Impact]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Limit the number of queries users can perform in a given interval to protect the system from chaff data spam.
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-in-production|AML.M0019: Control Access to AI Models and Data in Production]] — Authentication on production models can help prevent anonymous chaff data spam.
