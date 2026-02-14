---
title: Frontier Model Security Framework
tags:
  - type/defense
  - source/generative-ai-security
  - needs-review
---

# Frontier Model Security Framework

# Frontier Model Security Framework

6.4 Frontier Model Security
The Frontier Model in GenAI refers to large-scale models that exceed the capabili-
ties of the most advanced existing models and can perform a wide variety of tasks.
These models are expected to deliver significant opportunities across various sec-
tors and are primarily foundation models consisting of huge neural networks using
transformer architectures.
As frontier AI models rapidly advance, security has become paramount. These
powerful models could disrupt economies and national security globally.
Safeguarding them demands more than conventional commercial tech security.
Their strategic nature compels governments and AI labs to protect advanced mod-
els, weights, and research.
In this section, we use Anthropic, an AI startup as an example to see one approach
to develop frontier models securely.
Adopting cybersecurity best practices is essential. Anthropic advocates “two-
party control,” where no individual has solo production access, reducing insider
risks (Anthropic, 2023b). Smaller labs can implement this too. Anthropic terms its
framework “multi-party authorization.” In our view, this is no different than the
traditional separation of duty and least privilege principle. But, it is good to see
Anthropic enforce these basic security principles.

In addition, Anthropic uses comprehensive best practices and Secure Software
Development Framework (SSDF) per NIST (NIST, 2022) and applies it for model
development and also used Supply Chain Levels for Software Artifacts (SLSA)
from Slsa.Dev for supply chain management.
Anthropic coined a term called “Secure Model Development Framework” which
applies both SSDF and SLSA to model development to provide a chain of custody,
enhancing provenance.
Anthropic also advocates requiring these practices for AI companies and cloud
providers working with the government. As the backbone for many model compa-
nies, US cloud providers shape the landscape.
Public-private cooperation on AI research is also key. Anthropic proposes AI labs
participate like critical infrastructure sectors, facilitating collaboration and informa-
tion sharing.
Though tempting to minimize security concerns, AI’s dynamic landscape
demands heightened precautions. Anthropic shows proactive security need not
impede progress but enables responsible development. Prioritizing safety, security,
and human values fulfills AI’s responsibility to humanity.

Source: [[sources/bibliography#Generative AI Security]], p. 193 (§6.4)
