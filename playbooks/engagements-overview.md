---
tags:
  - trust-boundary/general
  - type/overview
  - type/playbook
---
# Engagements

This section defines engagement types - curated traversals of the trust-boundary problem space.

Each engagement specifies purpose, scope, and applicable issues organized by trust boundary.

## Engagement Types

### [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]]

Comprehensive security assessment covering all four trust boundaries of an AI system. Identifies exploitable vulnerabilities, validates security controls, and provides actionable remediation guidance aligned with OWASP, NIST, and MITRE ATLAS frameworks.

**Best for**: Pre-deployment validation, M&A due diligence, regulatory compliance, post-incident assessment, annual security reviews.

---

### [[playbooks/llm-application-red-team|LLM Application Red Team]]

Focused adversarial assessment targeting application-layer vulnerabilities in LLM-powered systems. Testing concentrates on prompt injection attacks, jailbreak techniques, tool misuse, and data exfiltration through model outputs or tool invocations.

**Best for**: Chat applications, copilot integrations, tool-calling applications, document Q&A systems with basic RAG.

---

### [[playbooks/rag-retrieval-assessment|RAG & Retrieval Attack Assessment]]

Specialized security assessment targeting LLM systems with external knowledge retrieval. Validates resilience against corpus poisoning, embedding manipulation, and prompt injection via retrieved content.

**Best for**: Document Q&A systems, enterprise knowledge bases, customer support bots, research assistants, multi-source RAG systems.

---

### [[playbooks/ai-model-risk-assessment|AI Model Risk Assessment]]

Holistic risk and security review of AI models and their usage context. Collaborative, review-based assessment mapping findings to NIST AI RMF, MITRE ATLAS, and industry frameworks. Encompasses risks of bias, fairness, performance, privacy, and security.

**Best for**: Regulated industries (finance, healthcare), credit scoring models, healthcare AI systems, content moderation, predictive analytics, autonomous decision systems.

---

### [[playbooks/ai-supply-chain-security|AI Supply Chain Security Testing]]

Comprehensive security assessment of the entire AI development and deployment pipeline. Scrutinizes data sources, training processes, model artifacts, infrastructure, and third-party dependencies to uncover supply chain vulnerabilities.

**Best for**: Custom model development, fine-tuned models, pre-trained model integration, ML platform deployments, hybrid pipelines.

---

### [[playbooks/agentic-workflow-assessment|Agentic Workflow Assessment]]

Adversarial evaluation of autonomous AI agents and multi-component AI systems that can take actions. Tests for goal misalignment, tool misuse, privilege escalation, and unsafe behaviors in agentic systems.

**Best for**: Autonomous AI assistants, code generation agents, multi-agent systems, robotics/IoT AI agents, workflow automation agents, customer service agents with tool access.

---

### [[playbooks/evaluation-pipeline-review|AI Evaluation Pipeline Review]]

Consultative engagement auditing an organization's internal AI testing and evaluation processes. Identifies gaps in threat coverage, automation, reproducibility, and framework alignment, providing recommendations to improve evaluation capabilities.

**Best for**: AI development organizations, ML platform teams, AI governance teams, regulated industries requiring model validation processes.

---

## Choosing the Right Engagement

Engagements can overlap and complement each other. Consider:

- **Comprehensive Coverage**: [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] covers all trust boundaries
- **Focused Testing**: [[playbooks/llm-application-red-team|LLM Application Red Team]] for application-layer attacks, [[playbooks/rag-retrieval-assessment|RAG Assessment]] for retrieval security
- **Risk Management**: [[playbooks/ai-model-risk-assessment|AI Model Risk Assessment]] for holistic risk review, [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]] for process improvement
- **Specialized Systems**: [[playbooks/ai-supply-chain-security|Supply Chain Security]] for pipeline security, [[playbooks/agentic-workflow-assessment|Agentic Assessment]] for autonomous agents

See [[methodology/engagement-type-differences|Engagement Type Differences]] for detailed comparison and overlap guidance.
