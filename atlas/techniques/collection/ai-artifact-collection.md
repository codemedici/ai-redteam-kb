---
id: ai-artifact-collection
title: "AML.T0035: AI Artifact Collection"
sidebar_label: "AI Artifact Collection"
sidebar_position: 1
---

# AML.T0035: AI Artifact Collection



Adversaries may collect AI artifacts for [[exfiltration|Exfiltration]] or for use in [[ai-attack-staging|AI Attack Staging]].
AI artifacts include models and datasets as well as other telemetry data produced when interacting with a model.

## Metadata

- **Technique ID:** AML.T0035
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[collection|AML.TA0009: Collection]]




## Case Studies (3)


The following case studies demonstrate this technique:

### [[microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]

The team found the model file of the target ML model and the necessary training data.

### [[arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]

Adversary may search the victim system to find private and proprietary data, including ML model artifacts.  Jupyter Notebooks [allow execution of shell commands](https://colab.research.google.com/github/jakevdp/PythonDataScienceHandbook/blob/master/notebooks/01.05-IPython-And-Shell-Commands.ipynb).

This example searches the mounted Drive for PyTorch model checkpoint files:

```
!find /content/drive/MyDrive/ -type f -name *.pt
```
> /content/drive/MyDrive/models/checkpoint.pt

### [[shadowray|AML.CS0023: ShadowRay]]

Adversaries could collect AI artifacts including production models and data.

The researchers observed running production workloads from several organizations from a variety of industries.



## References

MITRE Corporation. *AI Artifact Collection (AML.T0035)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0035
