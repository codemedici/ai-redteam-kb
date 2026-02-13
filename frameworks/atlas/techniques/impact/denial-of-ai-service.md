---
id: denial-of-ai-service
title: "AML.T0029: Denial of AI Service"
sidebar_label: "Denial of AI Service"
sidebar_position: 30
---

# AML.T0029: Denial of AI Service

Adversaries may target AI-enabled systems with a flood of requests for the purpose of degrading or shutting down the service.
Since many AI systems require significant amounts of specialized compute, they are often expensive bottlenecks that can become overloaded.
Adversaries can intentionally craft inputs that require heavy amounts of useless compute from the AI system.

## Metadata

- **Technique ID:** AML.T0029
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/impact|Impact]]

## Mitigations (3)

- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Limit the number of queries users can perform in a given interval to prevent a denial of service.
- [[frameworks/atlas/mitigations/adversarial-input-detection|AML.M0015: Adversarial Input Detection]] — Assess queries before inference call or enforce timeout policy for queries which consume excessive resources.
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-in-production|AML.M0019: Control Access to AI Models and Data in Production]] — Access controls on model APIs can prevent an adversary from excessively querying and disabling the system.

## Case Studies (2)

- [[frameworks/atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|Achieving Code Execution in MathGPT via Prompt Injection]]
- [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AIKatz: Attacking LLM Desktop Applications]]
