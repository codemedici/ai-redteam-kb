---
tags:
  - trust-boundary/deployment-governance
  - trust-boundary/model
  - type/playbook
description: "Holistic risk and security review of AI models and their usage context, mapping findings to NIST AI RMF, MITRE ATLAS, and industry frameworks."
---
# AI Model Risk Assessment

## Overview

### What This Engagement Is

A comprehensive risk analysis and security review of an AI system's design, training data, algorithms, and operational context. Unlike active attack simulation engagements, this is a collaborative, review-based assessment that works with the organization's developers and stakeholders to identify where the system might fail or be misused. The engagement encompasses risks of bias, fairness, performance degradation, privacy, and security vulnerabilities, providing a prioritized overview of model-related risks to the business.

**Why this engagement exists**: Organizations in regulated industries (finance, healthcare) are required to conduct model risk management, and this requirement is expanding to AI/ML models. Even outside regulated sectors, understanding an AI system's complete risk profile—beyond just security vulnerabilities—is essential for responsible deployment. This engagement provides structured risk identification aligned with frameworks like NIST AI RMF and helps organizations prioritize mitigation efforts.

### What This Is Not

- **Not active penetration testing**: This is an audit and review engagement, not an adversarial attack simulation (see LLM Application Red Team for active testing)
- **Not bias-only audit**: While bias and fairness are covered, this engagement addresses the full spectrum of model risks including security, privacy, and performance
- **Not code review**: Focuses on model behavior, data, and operational context rather than source code security (requires separate engagement)
- **Not compliance-only check**: Provides actionable risk assessment, not just regulatory checkbox exercises

---

## Scope

### Supported Target Archetypes

- **Credit Scoring Models**: Financial risk assessment models requiring regulatory compliance
- **Healthcare AI Systems**: Medical diagnosis, treatment recommendation, or patient risk models
- **Content Moderation Systems**: Automated content filtering, classification, or moderation models
- **Recommendation Engines**: Product recommendations, content suggestions, or personalization models
- **Predictive Analytics**: Forecasting, demand prediction, or trend analysis models
- **Autonomous Decision Systems**: Models making automated decisions with business or safety impact

### Explicitly Out of Scope

- Active adversarial attack simulation (see LLM Application Red Team or AI Threat Exposure Review)
- Source code security review (requires separate application security assessment)
- Training infrastructure security (see AI Supply Chain Security)
- Performance optimization or model accuracy improvement (focuses on risk, not optimization)
- Fine-tuning or model development (assesses existing models, doesn't develop new ones)

---

## Threat Model

### Risk Categories Assessed

This engagement evaluates risks across multiple dimensions:

1. **Performance & Reliability Risks**: Model accuracy, robustness, stability, and degradation over time
2. **Adversarial Vulnerabilities**: Susceptibility to known attack techniques (evasion, poisoning, inversion)
3. **Data and Privacy Risks**: Training data quality, PII exposure, memorization, data access controls
4. **Ethical & Bias Concerns**: Unintended biases, fairness across demographics, discriminatory outcomes
5. **Compliance & Governance**: Alignment with regulations, documentation completeness, process controls
6. **Operational Risks**: Model drift, monitoring gaps, incident response readiness, access controls

### Assessment Approach

- **Collaborative Review**: Works with internal teams rather than acting as external attacker
- **Framework-Aligned**: Maps findings to NIST AI RMF, MITRE ATLAS, OWASP, and industry standards
- **Risk-Prioritized**: Qualitative and semi-quantitative scoring to prioritize mitigation efforts
- **Context-Aware**: Considers business impact, regulatory requirements, and operational constraints

### Trust Boundaries Evaluated

This engagement provides risk assessment across:

- **Model**: Intrinsic model behavior, training data quality, algorithmic risks
- **Data / Knowledge**: Data provenance, privacy, bias in training data, knowledge base integrity
- **Application / Agent Runtime**: Integration risks, misuse scenarios, operational context
- **Deployment / Governance**: Access controls, monitoring, incident response, compliance processes

[[methodology/trust-boundaries-overview|See Trust Boundaries overview]]

---

## Trust Boundary Relevance

### Model

The Model boundary is a primary focus of risk assessment. The engagement evaluates intrinsic model behavior, training data quality, algorithmic risks, and model integrity. This boundary is critical for understanding model-specific vulnerabilities and risks.

**Applicable Issues:**
- [[attacks/prompt-injection|Prompt Injection]]
- [[attacks/system-prompt-leakage|System Prompt Leakage]]
- [[attacks/sensitive-info-disclosure|Sensitive Information Disclosure]]
- [[attacks/jailbreak-policy-bypass|Jailbreak and Policy Bypass]]
- [[attacks/model-extraction|Model Extraction and Theft]]
- [[attacks/training-data-memorization|Training Data Memorization]]
- [[attacks/adversarial-robustness|Adversarial Robustness]]

### Data / Knowledge

The Data/Knowledge boundary is assessed for data provenance, privacy risks, bias in training data, and knowledge base integrity. This includes evaluation of training data quality, PII exposure, and data governance controls.

**Applicable Issues:**
- [[attacks/rag-data-poisoning|RAG Data Poisoning]]
- [[attacks/pii-in-corpus|PII in Knowledge Corpus]]
- [[attacks/prompt-log-data-leakage|Prompt Log Data Leakage]]

### Application / Agent Runtime

The Application/Agent Runtime boundary is evaluated for integration risks, misuse scenarios, and operational context. This includes assessment of how the model integrates with business logic and potential misuse patterns.

**Applicable Issues:**
- [[attacks/tool-privilege-escalation|Tool Privilege Escalation]]
- [[attacks/agent-goal-hijack|Agent Goal Hijacking]]
- [[attacks/insecure-prompt-assembly|Insecure Prompt Assembly]]

### Deployment / Governance

The Deployment/Governance boundary is assessed for access controls, monitoring capabilities, incident response readiness, and compliance processes. This includes evaluation of operational controls and governance mechanisms.

**Applicable Issues:**
- [[attacks/insufficient-telemetry-and-tracing|Insufficient Telemetry and Tracing]]
- [[attacks/weak-access-segmentation|Weak Access Segmentation]]
- [[attacks/secrets-in-prompts-and-logs|Secrets in Prompts and Logs]]
- [[attacks/missing-evaluation-gates|Missing Evaluation Gates]]
- [[attacks/incident-response-gap|Incident Response Gap]]

---

## Assessment Methodology

### Phase 1: Information Gathering (2-3 days)

**Objectives**: Build comprehensive understanding of the AI system and its context

**Activities**:
- **System Inventory**: Catalog all AI/ML models in scope, their purposes, owners, and data sources
- **Stakeholder Interviews**: Engage with data scientists, developers, product owners, compliance teams
- **Documentation Review**: Architecture diagrams, training procedures, prior test results, model cards
- **Data Source Analysis**: Review training data sources, collection methods, consent and privacy considerations
- **Operational Context**: Understand deployment environment, usage patterns, business criticality
- **Existing Controls Review**: Document current mitigations, monitoring, and governance processes

**Outputs**:
- Complete system inventory
- Architecture and data flow diagrams
- Stakeholder interview summaries
- Documentation gap analysis
- Current controls inventory

### Phase 2: Risk Identification (3-4 days)

**Objectives**: Systematically identify potential risks using structured frameworks

**Activities**:
- **Threat Modeling**: Apply STRIDE adapted for AI systems to identify potential failure modes
- **Framework Mapping**: Use NIST AI RMF risk taxonomy, MITRE ATLAS tactics, OWASP categories
- **Scenario Development**: Brainstorm misuse scenarios, failure modes, and attack vectors
- **Bias and Fairness Analysis**: Review model outcomes across demographics, test for discriminatory patterns
- **Privacy Risk Assessment**: Evaluate training data for PII exposure, model memorization risks
- **Performance Risk Analysis**: Assess model stability, drift potential, edge case handling
- **Compliance Gap Analysis**: Compare current practices against regulatory requirements

**Outputs**:
- Comprehensive risk register
- Risk categorization by type and trust boundary
- Framework mapping (NIST, ATLAS, OWASP)
- Risk hypothesis for validation

### Phase 3: Control Analysis and Testing (2-3 days)

**Objectives**: Evaluate effectiveness of existing controls and validate risk hypotheses

**Activities**:
- **Control Effectiveness Review**: Assess whether existing mitigations adequately address identified risks
- **Light Adversarial Testing**: Run known attack techniques using tools like Adversarial Robustness Toolbox to validate vulnerability claims
- **Bias Testing**: Use fairness toolkits (IBM AI Fairness 360, Google What-If Tool) to detect bias
- **Privacy Auditing**: Test for model memorization, PII leakage, data access controls
- **Performance Validation**: Review model accuracy metrics, test edge cases, assess drift indicators
- **Documentation Review**: Verify model cards, datasheets, algorithmic impact assessments exist and are complete

**Outputs**:
- Control effectiveness assessment
- Test results validating risk hypotheses
- Gap analysis: risks without adequate controls
- Documentation completeness review

### Phase 4: Risk Prioritization and Impact Assessment (2-3 days)

**Objectives**: Score and prioritize risks for business decision-making

**Activities**:
- **Likelihood Assessment**: Qualitative or semi-quantitative scoring of risk occurrence probability
- **Impact Assessment**: Evaluate business consequences (financial, regulatory, reputational, safety)
- **Risk Matrix Development**: Create prioritized risk heat map
- **Root Cause Analysis**: Identify systemic patterns vs. isolated issues
- **Remediation Planning**: Develop mitigation strategies with effort estimates
- **Framework Alignment**: Map all findings to NIST AI RMF functions (GOVERN, MAP, MEASURE, MANAGE)

**Outputs**:
- Risk-prioritized findings list
- Impact analysis per finding
- Risk heat map or matrix
- Remediation roadmap draft
- Framework compliance matrix

### Phase 5: Reporting and Recommendations (2-3 days)

**Objectives**: Deliver actionable risk assessment in formats suitable for technical and executive audiences

**Activities**:
- **Executive Summary**: Business risk overview, top concerns, strategic recommendations
- **Technical Report**: Detailed risk descriptions, test results, root causes, framework mappings
- **Remediation Roadmap**: Phased mitigation plan (Immediate → Short-term → Long-term) with effort estimates
- **Compliance Documentation**: Framework alignment matrices, regulatory gap analysis
- **Presentation**: Risk assessment walkthrough with stakeholders

**Outputs**:
- Executive summary (5-10 pages)
- Technical risk assessment report (30-50 pages)
- Framework compliance matrix
- Remediation roadmap with effort estimates
- Risk register in accessible format

---

## Techniques Covered

This engagement evaluates risks across multiple categories rather than testing specific attack techniques. However, the assessment considers the following risk areas:

- [x] **Adversarial Vulnerabilities**: Evasion attacks, adversarial examples, model inversion
- [x] **Data and Privacy Risks**: Training data quality, PII exposure, model memorization
- [x] **Bias and Fairness Concerns**: Unintended biases, discriminatory outcomes
- [x] **Performance and Reliability Risks**: Model accuracy, robustness, stability, drift
- [x] **Compliance and Governance Risks**: Regulatory alignment, documentation gaps, process controls
- [x] **Operational Risks**: Model drift, monitoring gaps, incident response readiness

[[methodology/trust-boundaries-overview|Full attack taxonomy]]

---

## Risk Assessment Framework

### NIST AI RMF Alignment

This engagement maps findings to NIST AI Risk Management Framework functions:

**GOVERN**: Organizational culture, policies, processes for AI risk management
- Risk governance structure assessment
- Policy and procedure review
- Accountability and oversight evaluation

**MAP**: Context and risk identification
- System inventory and characterization
- Risk identification and categorization
- Stakeholder impact analysis

**MEASURE**: Risk measurement and evaluation
- Model performance and reliability metrics
- Adversarial robustness testing
- Bias and fairness measurement
- Privacy risk quantification

**MANAGE**: Risk mitigation and monitoring
- Control effectiveness evaluation
- Monitoring and detection capability assessment
- Incident response readiness review

### MITRE ATLAS Mapping

Findings are mapped to relevant ATLAS tactics and techniques:

**Tactics**:
- AML.TA0001: AI Attack Staging
- AML.TA0002: Model Development
- AML.TA0003: Initial Access
- AML.TA0004: Execution
- AML.TA0005: Persistence
- AML.TA0006: Evasion
- AML.TA0007: Exfiltration

**Techniques**: Specific techniques relevant to identified risks (e.g., AML.T0051: LLM Prompt Injection, AML.T0052: Training Data Poisoning)

### OWASP LLM Top 10 Coverage

Assessment ensures coverage of OWASP categories where applicable:
- LLM01: Prompt Injection
- LLM02: Sensitive Information Disclosure
- LLM03: Insecure Output Handling
- LLM04: Data and Model Poisoning
- LLM05: Supply Chain Vulnerabilities
- LLM06: Excessive Agency
- LLM07: System Prompt Leakage
- LLM08: Excessive Reliance on Model Generated Content
- LLM09: Overreliance on Model Generated Content
- LLM10: Model Theft

---

## Tooling and Analysis Methods

### Risk Assessment Tools

**Model Auditing**:
- **Fairness Toolkits**: IBM AI Fairness 360, Google What-If Tool, Fairlearn
- **Privacy Auditing**: Tools to detect model memorization, PII exposure
- **Performance Analysis**: Model accuracy metrics, drift detection tools

**Light Adversarial Testing**:
- **Adversarial Robustness Toolbox**: Quick validation of evasion attack susceptibility
- **Prompt Injection Testers**: Basic prompt injection testing for LLMs
- **Model Extraction Probes**: Test for model theft vulnerabilities

**Compliance and Documentation**:
- **Checklist Tools**: Scripts to enforce documentation standards (model cards, datasheets)
- **Framework Mapping Tools**: Templates for NIST AI RMF, MITRE ATLAS alignment

**Monitoring and Log Analysis**:
- **Anomaly Detection**: Scripts to analyze model output distributions for drift or attacks
- **Log Analysis**: Review access logs, model usage patterns, incident history

### Analysis Methods

- **Structured Interviews**: Systematic stakeholder engagement
- **Documentation Review**: Comprehensive artifact analysis
- **Threat Modeling Workshops**: Collaborative risk identification
- **Benchmark Comparison**: Compare practices against industry standards
- **Gap Analysis**: Identify missing controls or processes

---

## Deliverables

### 1. Executive Summary Report (5-10 pages)

- **Top Risk Overview**: Highest-priority risks with business impact
- **Risk Score Summary**: Overall risk posture and compliance status
- **Strategic Recommendations**: High-level guidance for risk management
- **Compliance Status**: Alignment with NIST AI RMF, regulatory requirements
- **Comparison to Baseline**: Industry risk profile comparison

### 2. Technical Risk Assessment Report (30-50 pages)

- **Risk Register**: Comprehensive list of identified risks with descriptions
- **Risk Categorization**: Organized by type (security, bias, privacy, performance, compliance)
- **Test Results**: Findings from light adversarial testing, bias analysis, privacy audits
- **Root Cause Analysis**: Systemic patterns and architectural risk factors
- **Framework Mappings**: NIST AI RMF, MITRE ATLAS, OWASP alignments
- **Control Effectiveness**: Assessment of existing mitigations

### 3. Framework Compliance Matrix

- **NIST AI RMF Alignment**: Mapping to GOVERN, MAP, MEASURE, MANAGE functions
- **MITRE ATLAS Coverage**: Tactics and techniques relevant to identified risks
- **OWASP LLM Top 10**: Coverage assessment for applicable categories
- **Regulatory Compliance**: Sector-specific requirements (e.g., OCC guidelines for banking, EU AI Act)

### 4. Risk-Prioritized Remediation Roadmap

- **Phased Mitigation Plan**: Immediate → Short-term → Long-term
- **Effort Estimates**: Hours/days per remediation activity
- **Risk Reduction Impact**: Expected improvement in risk posture
- **Dependencies**: Sequencing of remediation activities
- **Validation Criteria**: How to verify mitigations are effective

### 5. Risk Heat Map / Matrix

- **Visual Risk Prioritization**: Likelihood vs. Impact matrix
- **Trust Boundary Breakdown**: Risks organized by Model, Data, Application, Deployment
- **Trend Analysis**: Risk evolution over time (if historical data available)

### 6. Optional: Model Risk Register (Spreadsheet/Database)

- **Structured Risk Data**: Machine-readable format for risk management systems
- **Tracking Fields**: Status, owner, remediation progress
- **Integration Ready**: Compatible with enterprise risk management platforms

---

## Evidence Standards

### Confirmed Risk

A risk is confirmed when:

- **Identified**: Risk factor is clearly identified through analysis, testing, or documentation review
- **Validated**: Light adversarial testing or analysis confirms the risk exists
- **Contextualized**: Risk is assessed in the context of the organization's use case and business impact
- **Documented**: Complete analysis with evidence, likelihood, and impact assessment

**Risk Scoring**:
- **Critical**: High likelihood and severe impact (e.g., regulatory violation, safety risk, major data breach)
- **High**: High likelihood and significant impact, or medium likelihood and severe impact
- **Medium**: Medium likelihood and moderate impact, or low likelihood and significant impact
- **Low**: Low likelihood and low impact, or theoretical risks with minimal demonstrated impact

### Hypothesis / Observation

Lower-confidence findings documented separately:

- **Hypothesis**: Theoretical risk requiring deeper validation or testing
- **Observation**: Risk factor identified but impact not yet fully assessed
- **Gap**: Missing control or process that could lead to risk

---

## Framework Mapping

This engagement produces findings mapped to:

### NIST AI RMF

**Functions Tested**:
- GOVERN: Organizational culture, policies, processes for AI risk management
- MAP: Context and risk identification
- MEASURE: Risk measurement and evaluation
- MANAGE: Risk mitigation and monitoring

### MITRE ATLAS

**Tactics**:
- AML.TA0001: AI Attack Staging
- AML.TA0002: Model Development
- AML.TA0006: Evasion
- AML.TA0007: Exfiltration

**Techniques**: Specific techniques relevant to identified risks (e.g., AML.T0051: LLM Prompt Injection, AML.T0052: Training Data Poisoning)

### OWASP LLM Top 10

**Coverage**: Assessment ensures coverage of OWASP categories where applicable:
- LLM01: Prompt Injection
- LLM02: Sensitive Information Disclosure
- LLM03: Insecure Output Handling
- LLM04: Data and Model Poisoning
- LLM05: Supply Chain Vulnerabilities
- LLM06: Excessive Agency
- LLM07: System Prompt Leakage
- LLM08: Excessive Reliance on Model Generated Content
- LLM09: Overreliance on Model Generated Content
- LLM10: Model Theft

---

## Example Findings

### Finding 1: Model Susceptible to Evasion Attacks

**Risk Category**: Adversarial Vulnerability

**Severity**: High

**Trust Boundary**: Model

**Summary**: Light adversarial testing revealed that the model is moderately vulnerable to evasion attacks. Adding noise to 5% of inputs dropped accuracy by 20%. No adversarial training is currently in place, and the model lacks robustness validation in the evaluation pipeline.

**Impact**: 
- **Performance**: Model accuracy degrades significantly under adversarial conditions
- **Security**: Attackers could craft inputs to evade detection or cause misclassification
- **Business**: Financial or safety consequences if model is used in high-stakes decisions

**Framework Mapping**:
- NIST AI RMF: MEASURE 2.3 (robustness validation), MANAGE 2.3 (reliable operation)
- MITRE ATLAS: AML.TA0006 (Evasion), AML.T0053 (Adversarial Example)
- OWASP LLM: LLM03 (Insecure Output Handling) if outputs are used unsafely

**Remediation Guidance**:

1. **Immediate** (0-3 days): Add adversarial examples to evaluation suite, implement basic input validation
2. **Short-term** (1-2 weeks): Incorporate adversarial training, add robustness metrics to monitoring
3. **Long-term** (1-3 months): Redesign evaluation pipeline to include continuous adversarial testing, implement runtime anomaly detection

**Validation Criteria**:
- Adversarial test suite shows improved robustness (accuracy drop less than 5% under adversarial conditions)
- Monitoring alerts on unusual input patterns
- Regular adversarial testing integrated into model update process

---

### Finding 2: Age Bias in Loan Denial Model

**Risk Category**: Bias and Fairness

**Severity**: High

**Trust Boundary**: Model / Data

**Summary**: Analysis of model outcomes across demographics revealed a slight age bias where older applicants receive higher loan denial rates, even when controlling for creditworthiness factors. The training data shows underrepresentation of older applicants, and the model was not evaluated for fairness across age groups before deployment.

**Impact**:
- **Fairness**: Discriminatory outcomes affecting protected class
- **Regulatory**: Potential violation of fair lending laws
- **Reputational**: Public trust and brand damage
- **Business**: Legal liability and potential regulatory action

**Framework Mapping**:
- NIST AI RMF: MAP 1.2 (stakeholder identification), MEASURE 1.2 (fairness metrics)
- OWASP LLM: LLM08 (Excessive Reliance on Model Generated Content) if used for automated decisions

**Remediation Guidance**:

1. **Immediate** (0-3 days): Pause automated decisions for affected demographic, implement human review
2. **Short-term** (1-2 weeks): Retrain model on balanced data, add fairness metrics to evaluation
3. **Long-term** (1-3 months): Implement continuous fairness monitoring, establish bias mitigation processes, conduct algorithmic impact assessment

**Validation Criteria**:
- Fairness metrics show no significant difference across age groups
- Model outcomes validated by fairness audit
- Ongoing monitoring detects bias regressions

---

### Finding 3: Lack of Model Drift Monitoring

**Risk Category**: Operational Risk

**Severity**: Medium

**Trust Boundary**: Deployment / Governance

**Summary**: The organization does not monitor for model drift or performance degradation over time. There are no automated alerts for accuracy drops, distribution shifts in input data, or changes in model behavior. This creates risk that model performance degrades silently, leading to poor decisions or security vulnerabilities.

**Impact**:
- **Performance**: Gradual accuracy degradation goes undetected
- **Security**: Model behavior changes may indicate ongoing attacks
- **Business**: Poor decisions based on degraded model performance
- **Compliance**: Inability to demonstrate ongoing model validation

**Framework Mapping**:
- NIST AI RMF: MANAGE 2.3 (mechanisms for reliable operation), MEASURE 2.1 (performance metrics)
- MITRE ATLAS: AML.TA0006 (Evasion) if drift enables new attack vectors

**Remediation Guidance**:

1. **Immediate** (0-3 days): Implement basic accuracy monitoring and alerting
2. **Short-term** (1-2 weeks): Add distribution shift detection, establish drift thresholds
3. **Long-term** (1-3 months): Implement comprehensive model monitoring platform, automated retraining triggers, performance trend analysis

**Validation Criteria**:
- Monitoring system detects accuracy drops within 24 hours
- Alerts trigger investigation and remediation processes
- Historical performance trends tracked and analyzed

---

## Output Format

### Risk Structure

Each identified risk follows this format:

**[Risk ID]**: [Descriptive Title]

**Risk Category**: [Adversarial Vulnerability | Data/Privacy | Bias/Fairness | Performance/Reliability | Compliance/Governance | Operational]

**Severity**: [Critical | High | Medium | Low]

**Trust Boundary**: [Model | Data/Knowledge | Application/Agent Runtime | Deployment/Governance]

**Framework IDs**: 
- NIST AI RMF: [Function.Item]
- MITRE ATLAS: [AML.T####] (if applicable)
- OWASP LLM: [LLM##] (if applicable)

**Description**: 
[Root cause analysis: why the risk exists, what architectural or process decision created it]

**Risk Scenario**:
[Narrative with business context: how the risk could manifest, realistic scenario, potential damage]

**Evidence**:
- Analysis: [Reference to assessment findings]
- Test Results: [Reference to light adversarial testing if performed]
- Documentation Review: [Reference to gaps identified]

**Impact**:
- **Confidentiality**: [Data exposure risks]
- **Integrity**: [Model behavior manipulation risks]  
- **Availability**: [Service disruption risks]
- **Business Impact**: [Financial, regulatory, reputational consequences]

**Likelihood Assessment**:
[Qualitative or semi-quantitative assessment of risk occurrence probability]

**Remediation Guidance**:

1. **Immediate** (0-3 days): [Stopgap mitigation]
2. **Short-term** (1-2 weeks): [Tactical fix]
3. **Long-term** (1-3 months): [Strategic improvement]

**Validation Criteria**:
[Specific tests or checks that should confirm risk is mitigated]

**References**:
- Wiki: [Link to technical issue page]
- Framework: [Link to relevant framework guidance]

---

## Engagement Inputs Required

### Before Assessment Begins

- [x] **System Inventory**: List of all AI/ML models in scope with descriptions
- [x] **Architecture Documentation**: System diagrams, data flows, model integration points
- [x] **Training Data Information**: Data sources, collection methods, privacy considerations
- [x] **Model Documentation**: Model cards, datasheets, algorithmic impact assessments (if available)
- [x] **Performance Metrics**: Historical accuracy, performance benchmarks, evaluation results
- [x] **Stakeholder Access**: Availability of data scientists, developers, product owners, compliance teams
- [x] **Policy Documentation**: Content policies, fairness guidelines, intended use restrictions
- [x] **Regulatory Context**: Applicable regulations, compliance requirements, industry standards

### During Assessment

- Timely responses to clarification questions (target: under 24hr turnaround)
- Access to model outputs and logs for analysis (anonymized if needed)
- Ability to run light adversarial tests in controlled environment
- Optional mid-assessment checkpoint (recommended at 50% completion)

---

## Limitations

- Assessment based on information provided and available documentation; gaps in documentation may limit risk identification
- Light adversarial testing provides indicative results, not comprehensive security validation (requires separate penetration testing)
- Results valid for assessed model versions and configurations as of [assessment date]; model updates may alter risk profile
- Bias and fairness analysis limited to available demographic data and test datasets
- Time-bounded assessment; exhaustive risk identification across all possible scenarios not feasible
- Collaborative review approach; may miss risks that would be discovered through adversarial testing

---

## Pricing and Timeline

**Base Duration**: 2-3 weeks (15-20 business days)

**Effort**: 12-16 person-days

**Timeline Breakdown**:
- Scoping call and information gathering: +3-5 business days (pre-engagement)
- Assessment execution: 15-20 business days
- Reporting and delivery: +3-5 business days
- Optional follow-up consultation: +2-3 business days

**Pricing**: Contact for quote based on number of models, complexity, and regulatory requirements

**Add-ons Available**:
- **Extended Adversarial Testing** (+5 days): Deeper security validation with active attack simulation
- **Bias Deep Dive** (+3 days): Comprehensive fairness analysis across all protected classes
- **Compliance Documentation** (+3 days): Develop model cards, datasheets, algorithmic impact assessments
- **Remediation Support** (+5 days): Hands-on assistance implementing recommended mitigations

---

## Related Engagements

- **LLM Application Red Team**: Choose this for active adversarial attack simulation. Model Risk Assessment provides risk identification; Red Team validates exploitability.
- **AI Threat Exposure Review**: Choose this for comprehensive security testing across all trust boundaries. Model Risk Assessment is review-based; Threat Exposure Review includes active testing.
- **AI Supply Chain Security**: Choose this for training pipeline and artifact security. Model Risk Assessment focuses on model behavior and operational risks.

---

## Getting Started

1. **Review this spec** to confirm it matches your risk management objectives
2. **Prepare engagement inputs** using checklist above
3. **Check [[methodology/methodology-overview|Methodology]]** to understand our framework-aligned approach
4. **Explore applicable issues**: [[methodology/trust-boundaries-overview|Trust Boundaries Overview]]
5. **** to discuss scoping, timeline, and pricing

---

## Technical References

- [[methodology/trust-boundaries-overview|Trust Boundaries Overview]]
- [[atlas/techniques|MITRE ATLAS Techniques]]
- [[methodology/methodology-overview|Methodology]]
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
