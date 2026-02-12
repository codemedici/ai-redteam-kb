---
id: ai-model-access
title: "AML.TA0000: AI Model Access"
sidebar_label: "AI Model Access"
sidebar_position: 4
---

# AML.TA0000: AI Model Access

The adversary is attempting to gain some level of access to an AI model.

AI Model Access enables techniques that use various types of access to the AI model that can be used by the adversary to gain information, develop attacks, and as a means to input data to the model.
The level of access can range from the full knowledge of the internals of the model to access to the physical environment where data is collected for use in the AI model.
The adversary may use varying levels of model access during the course of their attack, from staging the attack to impacting the target system.

Access to an AI model may require access to the system housing the model, the model may be publicly accessible via an API, or it may be accessed indirectly via interaction with a product or service that utilizes AI as part of its processes.

## Metadata

- **Tactic ID:** AML.TA0000
- **Created:** May 13, 2021
- **Last Modified:** October 13, 2025


## Techniques (4)

The following techniques can be used to achieve this tactic:


- [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040]] — AI Model Inference API Access (demonstrated)
- [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047]] — AI-Enabled Product or Service (realized)
- [[frameworks/atlas/techniques/ai-model-access/physical-environment-access|AML.T0041]] — Physical Environment Access (demonstrated)
- [[frameworks/atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044]] — Full AI Model Access (demonstrated)


## References

MITRE Corporation. *AI Model Access (AML.TA0000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0000
