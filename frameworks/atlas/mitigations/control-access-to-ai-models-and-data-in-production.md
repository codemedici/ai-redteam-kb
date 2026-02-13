---
id: control-access-to-ai-models-and-data-in-production
title: "AML.M0019: Control Access to AI Models and Data in Production"
sidebar_label: "Control Access to AI Models and Data in Production"
sidebar_position: 20
type: mitigation
---

# AML.M0019: Control Access to AI Models and Data in Production

Require users to verify their identities before accessing a production model.
Require authentication for API endpoints and monitor production model queries to ensure compliance with usage policies and to prevent model misuse.

## Metadata

- **Mitigation ID:** AML.M0019
- **Created:** 2024-01-12
- **Last Modified:** 2025-12-23
- **Category:** Policy
- **ML Lifecycle:** Deployment, Monitoring and Maintenance

## Techniques (11)

- [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]] — Adversaries can use unrestricted API access to gain information about a production system, stage attacks, and introduce malicious data to the system.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api|AML.T0024: Exfiltration via AI Inference API]] — Adversaries can use unrestricted API access to build a proxy training dataset and reveal private information.
- [[frameworks/atlas/techniques/impact/cost-harvesting|AML.T0034: Cost Harvesting]] — Access controls can limit API access and prevent cost harvesting.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data|AML.T0043: Craft Adversarial Data]] — Access controls on model APIs can restricts an adversary's access required to generate adversarial data.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001: Black-Box Optimization]] — Access controls on model APIs can deny adversaries the access required for black-box optimization methods.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model|AML.T0005: Create Proxy AI Model]] — Access controls on models APIs can reduce an adversary's ability to produce an accurate proxy model.
- [[frameworks/atlas/techniques/impact/denial-of-ai-service|AML.T0029: Denial of AI Service]] — Access controls on model APIs can prevent an adversary from excessively querying and disabling the system.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection|AML.T0051: LLM Prompt Injection]] — Use access controls in production to prevent adversaries from injecting malicious prompts.
- [[frameworks/atlas/techniques/impact/spamming-ai-system-with-chaff-data|AML.T0046: Spamming AI System with Chaff Data]] — Authentication on production models can help prevent anonymous chaff data spam.
- [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]] — Use access controls in production to prevent adversary's ability to verify attack efficacy.
- [[frameworks/atlas/techniques/discovery/discover-ai-model-outputs|AML.T0063: Discover AI Model Outputs]] — Controlling access to the model in production can help prevent adversaries from inferring information from the model outputs.
