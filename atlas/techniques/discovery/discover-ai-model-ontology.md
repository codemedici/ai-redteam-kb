---
id: discover-ai-model-ontology
title: "AML.T0013: Discover AI Model Ontology"
sidebar_label: "Discover AI Model Ontology"
sidebar_position: 1
---

# AML.T0013: Discover AI Model Ontology



Adversaries may discover the ontology of an AI model's output space, for example, the types of objects a model can detect.
The adversary may discovery the ontology by repeated queries to the model, forcing it to enumerate its output space.
Or the ontology may be discovered in a configuration file or in documentation about the model.

The model ontology helps the adversary understand how the model is being used by the victim.
It is useful to the adversary in creating targeted attacks.

## Metadata

- **Technique ID:** AML.T0013
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** demonstrated



## Tactics (1)

This technique supports the following tactics:


- [[discovery|AML.TA0008: Discovery]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]

The team identified the list of identities targeted by the model by querying the target model's inference API.



## References

MITRE Corporation. *Discover AI Model Ontology (AML.T0013)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0013
