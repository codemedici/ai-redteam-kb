---
id: passive-ai-output-obfuscation
title: "AML.M0002: Passive AI Output Obfuscation"
sidebar_label: "Passive AI Output Obfuscation"
sidebar_position: 3
type: mitigation
---

# AML.M0002: Passive AI Output Obfuscation

Decreasing the fidelity of model outputs provided to the end user can reduce an adversary's ability to extract information about the model and optimize attacks for the model.

## Metadata

- **Mitigation ID:** AML.M0002
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** Deployment, ML Model Evaluation

## Techniques (12)

- [[frameworks/atlas/techniques/discovery/discover-ai-model-ontology|AML.T0013: Discover AI Model Ontology]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/techniques/discovery/discover-ai-model-family|AML.T0014: Discover AI Model Family]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001: Black-Box Optimization]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-extract-model|AML.T0024.000: Infer Training Data Membership]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-invert-model|AML.T0024.001: Invert AI Model]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-membership-inference|AML.T0024.002: Extract AI Model]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043: Craft Adversarial Data]] — Obfuscating model outputs reduces an adversary's ability to generate effective adversarial data.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001: Black-Box Optimization]] — Obfuscating model outputs reduces an adversary's ability to create effective adversarial inputs.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-gathered-artifacts|AML.T0005: Create Proxy AI Model]] — Obfuscating model outputs can reduce an adversary's ability to produce an accurate proxy model.
- [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]] — Obfuscating model outputs reduces an adversary's ability to verify the efficacy of an attack.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-replication|AML.T0005.001: Train Proxy via Replication]] — Obfuscating model outputs restricts an adversary's ability to create an accurate proxy model by querying a model and observing its outputs.
- [[frameworks/atlas/techniques/discovery/discover-ai-model-outputs|AML.T0063: Discover AI Model Outputs]] — Obfuscating model outputs can prevent adversaries from collecting sensitive information about the model outputs.
