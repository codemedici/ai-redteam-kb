---
tags:
  - trust-boundary/general
  - type/methodology
---
# How This Differs by Engagement Type

## Engagement Comparison Table

| Engagement | Primary Focus | Trust Boundaries | Duration | Maturity | Depth Trade-Offs |
|-----------|--------------|------------------|----------|----------|------------------|
| [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] | Comprehensive security | All 4 | 2-3 weeks | Common | Breadth over depth |
| [[playbooks/llm-application-red-team|LLM Application Red Team]] | Application attacks | Model + App/Agent | 1-2 weeks | Common | Deep prompt injection + tool abuse |
| [[playbooks/rag-retrieval-assessment|RAG & Retrieval Assessment]] | Retrieval pipeline | Data/Knowledge + App/Agent | 1-2 weeks | Emerging | Deep poisoning + embedding attacks |
| [[playbooks/ai-model-risk-assessment|AI Model Risk Assessment]] | Holistic risk review | All 4 (review-based) | 2-3 weeks | Common (regulated), Emerging (elsewhere) | Risk identification over exploitation |
| [[playbooks/ai-supply-chain-security|AI Supply Chain Security]] | Pipeline security | Model + Data + Deployment | 2-3 weeks | Emerging | Deep supply chain attack testing |
| [[playbooks/agentic-workflow-assessment|Agentic Workflow Assessment]] | Autonomous agents | App/Agent + Model | 2-3 weeks | Emerging/Fringe | Multi-agent, goal hijacking, tool chains |
| [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]] | Process audit | Meta-level | 1-2 weeks | Fringe (growing) | Process improvement over testing |

**Customization**: All engagements follow the core workflow but adjust:
- **Phase emphasis**: AI Threat Exposure spreads effort evenly; focused engagements concentrate on 1-2 boundaries
- **Technique depth**: Focused engagements test more variations per technique
- **Deliverable detail**: Comprehensive reviews include strategic guidance; focused assessments prioritize technical depth
- **Approach**: Active testing vs. review-based assessment (Model Risk Assessment, Evaluation Pipeline Review)

---

## Engagement Overlaps and Complementarity

Engagements are not mutually exclusive—they often overlap and complement each other. Understanding these relationships helps organizations choose the right combination of engagements.

### Common Overlaps

**Prompt Injection Across Engagements**:
- **LLM Application Red Team**: Direct prompt injection testing
- **RAG Assessment**: Prompt injection via retrieved content
- **Agentic Assessment**: Prompt injection targeting agent planning and goals

**Data Poisoning Concerns**:
- **RAG Assessment**: Corpus poisoning in knowledge bases
- **Supply Chain Security**: Training data poisoning in data pipelines
- **Model Risk Assessment**: Data poisoning as a conceptual risk

**Tool and API Security**:
- **LLM Application Red Team**: Tool abuse and privilege escalation
- **Agentic Assessment**: Tool misuse in autonomous agents
- **Supply Chain Security**: API security in ML platforms

### Complementary Engagement Combinations

**Comprehensive Security Posture**:
- Start with **AI Threat Exposure Review** for broad coverage
- Follow with focused engagements for deep dives on specific areas

**RAG System Security**:
- **RAG Assessment** for retrieval-specific vulnerabilities
- **LLM Application Red Team** for base model prompt injection
- **Supply Chain Security** if using third-party embedding models

**Agentic System Security**:
- **Agentic Assessment** for agent-specific vulnerabilities
- **LLM Application Red Team** for underlying model security
- **Evaluation Pipeline Review** to improve internal testing processes

**Regulated Industry Compliance**:
- **AI Model Risk Assessment** for holistic risk review and compliance
- **AI Threat Exposure Review** or focused engagements for security validation
- **Evaluation Pipeline Review** to establish ongoing validation processes

**Building Internal Capability**:
- **Evaluation Pipeline Review** to assess current processes
- Focused engagements to understand attack techniques
- **AI Model Risk Assessment** to establish risk management framework

---

## Inconsistent Terminology in the Market

The AI red teaming market lacks standardized terminology, leading to confusion about what different engagement types include. Different organizations use different labels for similar activities.

### Common Terminology Variations

**"AI Red Teaming" vs. "AI Penetration Testing" vs. "AI Security Assessment"**:
- Some firms use these terms interchangeably
- Others distinguish: "red teaming" = comprehensive, "pen testing" = focused, "assessment" = review-based
- **Our Standardization**: We use "Red Team" for active adversarial testing, "Assessment" for review-based engagements

**"Model Risk Assessment" vs. "AI Risk Audit" vs. "Trustworthy AI Review"**:
- Similar review-based engagements with different names
- **Our Standardization**: "AI Model Risk Assessment" for holistic risk review

**"AI Security Assessment" (Broad Term)**:
- Could mean active testing, review-based assessment, or process audit
- **Our Standardization**: We specify engagement types clearly to avoid ambiguity

### Our Standardization Approach

We use consistent terminology across all engagements:

- **"Red Team"**: Active adversarial attack simulation (LLM Application Red Team, Agentic Workflow Assessment)
- **"Assessment"**: Review-based evaluation (AI Model Risk Assessment, RAG Assessment)
- **"Review"**: Process or pipeline audit (Evaluation Pipeline Review)
- **"Security Testing"**: Focused security validation (AI Supply Chain Security Testing)

Each engagement page clearly defines what it includes and excludes to avoid confusion.

---

## Choosing the Right Engagement

Selecting the appropriate engagement depends on your system architecture, business objectives, and risk profile.

### By System Type

**LLM-Powered Chat Applications**:
- **Primary**: [[playbooks/llm-application-red-team|LLM Application Red Team]]
- **If using RAG**: Add [[playbooks/rag-retrieval-assessment|RAG Assessment]]
- **If agentic**: Consider [[playbooks/agentic-workflow-assessment|Agentic Assessment]]

**RAG Systems with External Knowledge**:
- **Primary**: [[playbooks/rag-retrieval-assessment|RAG Assessment]]
- **Complementary**: [[playbooks/llm-application-red-team|LLM Application Red Team]] for base model security

**Autonomous AI Agents**:
- **Primary**: [[playbooks/agentic-workflow-assessment|Agentic Assessment]]
- **Complementary**: [[playbooks/llm-application-red-team|LLM Application Red Team]] for underlying model security

**Custom Model Development**:
- **Primary**: [[playbooks/ai-supply-chain-security|AI Supply Chain Security]]
- **Complementary**: [[playbooks/ai-model-risk-assessment|AI Model Risk Assessment]] for holistic risk review

**Pre-Deployment Validation**:
- **Primary**: [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] for comprehensive coverage
- **Alternative**: Focused engagements if specific areas are higher priority

### By Business Objective

**Regulatory Compliance**:
- **Primary**: [[playbooks/ai-model-risk-assessment|AI Model Risk Assessment]]
- **Complementary**: Security testing engagements for validation

**Building Internal Capability**:
- **Primary**: [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]]
- **Complementary**: Active testing engagements to understand techniques

**Incident Response**:
- **Primary**: [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] or focused engagement based on incident type
- **Complementary**: [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]] to prevent recurrence

**M&A Due Diligence**:
- **Primary**: [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] for comprehensive assessment
- **Complementary**: [[playbooks/ai-model-risk-assessment|AI Model Risk Assessment]] for risk profile

### By Risk Profile

**High-Stakes Systems** (finance, healthcare, safety-critical):
- **Comprehensive**: [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]]
- **Risk Management**: [[playbooks/ai-model-risk-assessment|AI Model Risk Assessment]]
- **Continuous**: [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]] for ongoing validation

**Rapid Development** (startups, MVPs):
- **Focused**: [[playbooks/llm-application-red-team|LLM Application Red Team]] or specific focused engagement
- **Process**: [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]] to build capability

**Mature AI Organizations**:
- **Process Improvement**: [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]]
- **Targeted Testing**: Focused engagements for specific new features or systems

---

## Engagement Sequencing

Engagements can be sequenced for maximum effectiveness:

**Initial Assessment**:
1. [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] or [[playbooks/ai-model-risk-assessment|AI Model Risk Assessment]] to establish baseline
2. Focused engagements for deep dives on critical areas
3. [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]] to improve internal processes

**Ongoing Security**:
1. [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]] to establish continuous testing
2. Periodic focused engagements for new features or model updates
3. Annual comprehensive review

**Post-Incident**:
1. Focused engagement on incident type
2. [[playbooks/evaluation-pipeline-review|Evaluation Pipeline Review]] to identify process gaps
3. Retest to validate fixes

---

## Summary

- **Engagements overlap**: Many techniques appear across multiple engagement types
- **Engagements complement**: Combining engagements provides defense-in-depth
- **Terminology varies**: We standardize to avoid confusion
- **Choice depends on context**: System type, business objectives, and risk profile determine the right engagement
- **Sequencing matters**: Engagements can be combined and sequenced for maximum effectiveness

[[playbooks/engagements-overview|Browse all engagements]] for detailed specifications.
