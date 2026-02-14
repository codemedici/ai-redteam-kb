---
id: restrict-number-of-ai-model-queries
title: "AML.M0004: Restrict Number of AI Model Queries"
sidebar_label: "Restrict Number of AI Model Queries"
sidebar_position: 5
type: mitigation
---

# AML.M0004: Restrict Number of AI Model Queries

Limit the total number and rate of queries a user can perform.

## Metadata

- **Mitigation ID:** AML.M0004
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - Cyber
- **ML Lifecycle:** Business and Data Understanding, Deployment, Monitoring and Maintenance

## Techniques (17)

- [[frameworks/atlas/techniques/impact/cost-harvesting|AML.T0034: Cost Harvesting]] — Limit the number of queries users can perform in a given interval to hinder an attacker's ability to send computationally expensive inputs
- [[frameworks/atlas/techniques/discovery/discover-ai-model-ontology|AML.T0013: Discover AI Model Ontology]] — Limit the amount of information an attacker can learn about a model's ontology through API queries.
- [[frameworks/atlas/techniques/discovery/discover-ai-model-family|AML.T0014: Discover AI Model Family]] — Limit the amount of information an attacker can learn about a model's ontology through API queries.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-extract-model|AML.T0024: Exfiltration via AI Inference API]] — Limit the volume of API queries in a given period of time to regulate the amount and fidelity of potentially sensitive information an attacker can learn.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-extract-model|AML.T0024.000: Infer Training Data Membership]] — Limit the volume of API queries in a given period of time to regulate the amount and fidelity of potentially sensitive information an attacker can learn.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-invert-model|AML.T0024.001: Invert AI Model]] — Limit the volume of API queries in a given period of time to regulate the amount and fidelity of potentially sensitive information an attacker can learn.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-membership-inference|AML.T0024.002: Extract AI Model]] — Limit the volume of API queries in a given period of time to regulate the amount and fidelity of potentially sensitive information an attacker can learn.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001: Black-Box Optimization]] — Limit the number of queries users can perform in a given interval to shrink the attack surface for black-box attacks.
- [[frameworks/atlas/techniques/impact/denial-of-ai-service|AML.T0029: Denial of AI Service]] — Limit the number of queries users can perform in a given interval to prevent a denial of service.
- [[frameworks/atlas/techniques/impact/spamming-ai-system-with-chaff-data|AML.T0046: Spamming AI System with Chaff Data]] — Limit the number of queries users can perform in a given interval to protect the system from chaff data spam.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043: Craft Adversarial Data]] — Restricting the number of model queries can reduce an adversary's ability to refine and evaluate adversarial queries.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001: Black-Box Optimization]] — Restricting the number of queries to the model limits or slows an adversary's ability to perform black-box optimization attacks.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-manual-modification|AML.T0043.003: Manual Modification]] — Restricting the number of model queries can reduce an adversary's ability to refine manually crafted adversarial inputs.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-gathered-artifacts|AML.T0005: Create Proxy AI Model]] — Restricting the number of queries to the model decreases an adversary's ability to replicate an accurate proxy model.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-replication|AML.T0005.001: Train Proxy via Replication]] — Restricting the number of queries to the model decreases an adversary's ability to replicate an accurate proxy model.
- [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]] — Restricting the number of queries to the model decreases an adversary's ability to verify the efficacy of an attack.
- [[frameworks/atlas/techniques/discovery/discover-llm-hallucinations|AML.T0062: Discover LLM Hallucinations]] — Restricting number of model queries limits or slows an adversary's ability to identify possible hallucinations.
