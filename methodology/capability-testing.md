---
tags:
  - trust-boundary/model
  - type/methodology
description: "Testing areas for evaluating AI model capabilities across safety-critical domains"
---
# Model Capability Testing Areas

The following areas of testing help identify model capabilities and limitations across safety-critical domains. These are particularly relevant for **pre-deployment model evaluation** and **capability assessment** engagements.

## Testing Areas

| Area of Testing | Motivating Questions |
|---|---|
| Natural Sciences | • What are the current capabilities in natural science domains, and where do those capabilities meaningfully alter the risk landscape (speed, accuracy, efficiency, cost effectiveness, expertise required)?<br />• What are the current limitations in natural science domains and where might that pose risks if relied on in high-stakes contexts? |
| Code / Writing Software and Systems Architecture | • What are the current capabilities and limitations of program synthesis, and where do those capabilities meaningfully alter the risk landscape (speed, accuracy, efficiency, cost effectiveness, expertise required)? |
| Cybersecurity | • What are the current possibilities for the use of the model in offensive / defensive cybersecurity contexts? Where do those capabilities meaningfully alter the risk landscape (speed, accuracy, efficiency, cost effectiveness, expertise required)?<br />• Are there risks related to: identification and exploitation of vulnerabilities, spear phishing, or bug finding? |
| Privacy | • Can the models produce or be used to surface private information? |
| Medicine / Healthcare | • What are the capabilities and limitations of the model used in healthcare applications and contexts? Especially with regards to limitations, how do those limitations meaningfully alter the risk landscape?<br />• Do we see changes in over-confidence in answers?<br />• Do we see increased hallucination, particularly of citations? |
| Law | • What are the capabilities and limitations of the model used in legal applications and contexts? Especially with regards to limitations, how do those limitations meaningfully alter the risk landscape?<br />• Do we see changes in over-confidence in answers?<br />• Do we see increased hallucination, particularly of citations? |
| Tool Use | • Where does tool use (browsing, chaining with other systems, using code interpreter, etc.) meaningfully augment the model's base capabilities in ways that might pose risks? Example areas include (carrying out purchases or financial transactions, etc.) |
| Dangerous Planning | • How might the model aid in planning for dangerous or high-stakes activities (attack planning, obtaining or distributing illegal or dangerous materials, circumvention of safeguards or protections in various contexts)? |
| Politics and Election Considerations | • How might the model aid in various ways to assist in political strategy, including generation of campaign materials, coming up with voter targeting approaches, analyzing and forecasting public opinion? |
| Bias and Fairness | • Where might the model exhibit bias? How might that have an impact on particular use cases (history, politics, controversial topics)?<br />• Does the model exhibit bias based on race, ethnicity, religion, political affiliation, etc., particularly if used to make decisions in hiring, educational access, and extending credit? |
| CBRN Risks | • How might the model impact CBRN (chemical, biological, radiological, and nuclear) threat creation? Where do those capabilities meaningfully alter the risk landscape (speed, accuracy, efficiency, cost effectiveness, expertise required)? Particular sub questions of interest: |
| AI Research & Development | • Can the models be used to advance methods in AI research and training? Where do those capabilities meaningfully alter the risk landscape (speed, accuracy, efficiency, cost effectiveness, expertise required)? |
| Situational Awareness / Autonomous Replication | • Are there properties of the model that might demonstrate situational awareness or the ability to autonomously replicate? |
| Violence and self harm | • Does the model refuse to give answers that support violence, enable self-harm, etc.? |
| Controversial Questions | • Does the model give responses to controversial questions (social, political, ethical) that are not inclusive of different valid perspectives? |
| Persuasiveness | • Is the model more capable in making persuasive arguments about sensitive topics?<br />• Is the model more capable or willing to form emotional attachments or give sensitive emotional/personal advice? |

## Usage Guidance

### When to Use This Framework

- **Pre-deployment testing**: Evaluate model capabilities before release
- **Safety assessments**: Identify high-risk behaviors requiring additional controls
- **Capability discovery**: Understand what the model can/cannot do
- **Risk prioritization**: Focus on domains relevant to your deployment context

### How to Apply

1. **Select relevant domains** based on your model's intended use case
2. **Prioritize high-risk areas** (e.g., CBRN, Violence, Privacy for most contexts)
3. **Create test prompt datasets** for each selected domain
4. **Establish success criteria** (e.g., "model refuses 95% of harmful requests")
5. **Document edge cases** where model behavior is uncertain or inconsistent

### Integration with Trust Boundaries

This capability testing primarily targets the **Model Attack Surface**, but findings have implications across all boundaries:

- **Model Boundary**: Direct capability assessment (what can the model inherently do?)
- **Data/Knowledge Boundary**: What information enabled these capabilities?
- **Application/System Boundary**: How might these capabilities be amplified by tools/agents?
- **Deployment/Governance Boundary**: What controls are needed to manage identified risks?

## References

This framework is derived from OpenAI's External Red Teaming guidance and industry best practices for pre-deployment model evaluation.
