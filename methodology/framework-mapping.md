---
tags:
  - trust-boundary/data-knowledge
  - type/methodology
---
# Framework Mapping and Compliance

## Mapping Approach

Every finding is tagged with framework identifiers enabling:
- **Traceability**: Link vulnerabilities to recognized attack patterns
- **Compliance**: Demonstrate coverage for NIST AI RMF, regulatory requirements
- **Threat intelligence**: Cross-reference with industry-standard taxonomies

## Frameworks Used

### MITRE ATLAS (Primary Attack Taxonomy)

Findings mapped to:
- **Tactics**: High-level adversary objectives (e.g., AML.TA0001: AI Attack Staging)
- **Techniques**: Specific attack methods (e.g., AML.T0051: LLM Prompt Injection)
- **Case Studies**: Real-world incidents demonstrating pattern

[[atlas/atlas-overview|Browse ATLAS reference]] | [[atlas/tactics|Tactics overview]] | [[atlas/techniques|Techniques catalog]]

### OWASP LLM Top 10 (Risk Framework)

Findings categorized by Top 10 risk classes:
- LLM01: Prompt Injection
- LLM02: Sensitive Information Disclosure
- LLM03-04: Data and Model Poisoning
- LLM05: Supply Chain Vulnerabilities
- LLM06: Excessive Agency
- LLM07: System Prompt Leakage
- LLM08: Vector and Embedding Weaknesses
- LLM09: Misinformation
- LLM10: Unbounded Consumption

[[methodology/trust-boundaries-overview|OWASP mapping details in issue pages]]

### NIST AI RMF (Compliance Framework)

Findings inform GOVERN/MAP/MEASURE/MANAGE functions:
- **MEASURE**: Demonstrated gaps in robustness, security, resilience
- **MANAGE**: Evidence for risk treatment decisions
- **MAP**: Expanded understanding of system risks and attack surface

Reports include gap analysis showing which RMF controls are validated vs. not-tested.

## Framework Integration in Reports

Each finding includes "Framework References" section with specific IDs. Compliance matrix shows:
- Coverage: Which framework items were tested
- Gaps: Items out of scope for engagement
- Findings distribution: Heat map of vulnerabilities per framework category

**Does not duplicate framework content**: Links to [[methodology/trust-boundaries-overview|docs taxonomy]] for technical depth; reports focus on findings and mapping only.
