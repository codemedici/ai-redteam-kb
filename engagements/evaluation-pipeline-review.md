---
id: evaluation-pipeline-review
title: "AI Evaluation Pipeline Review"
sidebar_label: "Eval Pipeline Review"
description: "Audit of an organization's internal AI testing and evaluation processes, identifying gaps in threat coverage, automation, reproducibility, and framework alignment."
---

# AI Evaluation Pipeline Review

## Overview

### What This Engagement Is

A consultative engagement that reviews the organization's own AI testing and evaluation processes rather than directly attacking AI models. This meta-red-team engagement audits how the company evaluates and validates its AI systems, examining their testing pipeline, metrics, coverage, and alignment with best practices. The deliverable is a gap analysis and recommendations to improve the robustness of the organization's AI evaluation lifecycle.

**Why this engagement exists**: Many organizations conduct AI red teaming, but their evaluation processes may have gaps that allow vulnerabilities to slip through. Without systematic evaluation pipeline review, organizations risk missing threat categories, over-relying on automation, lacking reproducibility, or misaligning with frameworks. This engagement helps organizations build mature, continuous AI evaluation capabilities integrated into their development lifecycle.

### What This Is Not

- **Not active red teaming**: This is a process audit, not an adversarial attack simulation (see LLM Application Red Team or AI Threat Exposure Review)
- **Not model risk assessment**: Focuses on evaluation processes, not model risks themselves (see AI Model Risk Assessment)
- **Not compliance audit**: Provides actionable process improvements, not just regulatory checkbox exercises
- **Not tool implementation**: Reviews and recommends, but doesn't implement tools or processes (requires separate implementation engagement)

---

## Scope

### Supported Target Archetypes

- **AI Development Organizations**: Companies building and deploying AI models with existing evaluation processes
- **ML Platform Teams**: Teams managing ML pipelines and model deployment with CI/CD integration
- **AI Governance Teams**: Organizations establishing AI risk management and evaluation frameworks
- **Regulated Industries**: Companies in finance, healthcare, or other sectors requiring model validation

### Explicitly Out of Scope

- Direct model testing or attack simulation (requires separate red team engagement)
- Implementation of recommended tools or processes (consultation only)
- Source code review of evaluation tools (requires separate code review)
- Performance optimization of evaluation pipelines (focuses on security coverage, not speed)

---

## Threat Model

### Process Gaps Assessed

This engagement evaluates gaps in evaluation processes that could allow vulnerabilities to go undetected:

1. **Incomplete Threat Coverage**: Missing tests for known attack categories
2. **Weak Methodology**: Ad hoc testing without structured approach
3. **Automation Gaps**: Over-reliance on manual testing or insufficient automation
4. **Reproducibility Issues**: Lack of documentation or versioning for test results
5. **Framework Misalignment**: Not mapping tests to NIST, MITRE ATLAS, or OWASP
6. **People and Expertise Gaps**: Lack of security expertise in evaluation team
7. **Metrics and Continuous Improvement**: No tracking or improvement processes

### Assessment Approach

- **Collaborative Review**: Works with internal teams to understand current processes
- **Benchmark Against Best Practices**: Compares current practices to industry standards
- **Gap Analysis**: Identifies specific areas where evaluation falls short
- **Actionable Recommendations**: Provides concrete steps to improve processes

### Trust Boundaries Evaluated

This engagement is a meta-level process audit that evaluates whether all trust boundaries are being tested by the organization's evaluation processes. It does not directly test trust boundaries but ensures they are covered in the evaluation pipeline.

[[trust-boundaries|See Trust Boundaries overview]]

---

## Trust Boundary Relevance

### Model

This engagement audits whether model-level security concerns are being tested in the evaluation pipeline. It assesses coverage of model-specific vulnerabilities and ensures testing methodologies address model behavior and manipulation resistance.

**Applicable Issues:**
- All model issues should be covered in evaluation pipeline
- [[prompt-injection|Prompt Injection]]
- [[system-prompt-leakage|System Prompt Leakage]]
- [[jailbreak-policy-bypass|Jailbreak and Policy Bypass]]
- [[model-extraction|Model Extraction and Theft]]

### Data / Knowledge

This engagement audits whether data and knowledge surface security is being tested. It assesses coverage of RAG security, data poisoning, and knowledge base integrity in the evaluation pipeline.

**Applicable Issues:**
- All data/knowledge issues should be covered in evaluation pipeline
- [[rag-data-poisoning|RAG Data Poisoning]]
- [[retrieval-manipulation|Retrieval Manipulation]]
- [[pii-in-corpus|PII in Knowledge Corpus]]

### Application / Agent Runtime

This engagement audits whether application and agent integration security is being tested. It assesses coverage of tool abuse, agent behavior, and prompt assembly vulnerabilities in the evaluation pipeline.

**Applicable Issues:**
- All application/agent runtime issues should be covered in evaluation pipeline
- [[tool-privilege-escalation|Tool Privilege Escalation]]
- [[agent-goal-hijack|Agent Goal Hijacking]]
- [[insecure-prompt-assembly|Insecure Prompt Assembly]]

### Deployment / Governance

This engagement audits whether deployment and governance controls are being tested. It assesses coverage of access controls, monitoring, secrets management, and incident response in the evaluation pipeline.

**Applicable Issues:**
- All deployment/governance issues should be covered in evaluation pipeline
- [[insufficient-telemetry-and-tracing|Insufficient Telemetry and Tracing]]
- [[weak-access-segmentation|Weak Access Segmentation]]
- [[secrets-in-prompts-and-logs|Secrets in Prompts and Logs]]
- [[missing-evaluation-gates|Missing Evaluation Gates]]

---

## Assessment Methodology

### Phase 1: Artifact Collection and Process Mapping (2-3 days)

**Objectives**: Understand current evaluation processes and collect evidence

**Activities**:
- **Process Documentation Review**: Collect test plans, red team reports, evaluation procedures
- **Pipeline Configuration Analysis**: Review CI/CD configs, automated test scripts, evaluation harnesses
- **Tool Inventory**: Document tools used for evaluation (automated frameworks, manual processes)
- **Stakeholder Interviews**: Engage with teams responsible for model evaluation (data scientists, QA engineers, AI safety teams)
- **Historical Results Review**: Analyze past evaluation results, incident reports, known vulnerabilities
- **Metrics and Tracking**: Review how evaluation results are measured, stored, and tracked

**Outputs**:
- Current process documentation
- Tool and pipeline inventory
- Stakeholder interview summaries
- Historical evaluation analysis
- Process flow diagrams

### Phase 2: Benchmark Against Best Practices (3-4 days)

**Objectives**: Compare current practices to industry standards and frameworks

**Activities**:
- **Framework Alignment Check**: Compare current tests to OWASP LLM Top 10, MITRE ATLAS, NIST AI RMF
- **Methodology Assessment**: Evaluate whether processes follow structured approaches (e.g., OWASP GenAI Red Teaming Blueprint)
- **Coverage Analysis**: Identify threat categories that are tested vs. not tested
- **Automation Evaluation**: Assess balance of automated vs. manual testing, CI/CD integration
- **Reproducibility Review**: Evaluate documentation, versioning, and reproducibility of test results
- **People and Expertise Assessment**: Review team composition, training, and security expertise

**Outputs**:
- Framework alignment matrix
- Coverage gap analysis
- Methodology maturity assessment
- Automation vs. manual balance evaluation

### Phase 3: Gap Analysis and Validation (2-3 days)

**Objectives**: Identify specific gaps and validate their impact

**Activities**:
- **Gap Identification**: Document specific areas where evaluation falls short
- **Impact Assessment**: Evaluate severity of gaps (what vulnerabilities might slip through)
- **Independent Testing**: Run small independent red team test to validate if pipeline missed issues
- **Root Cause Analysis**: Identify why gaps exist (process, tooling, expertise, resources)
- **Prioritization**: Rank gaps by severity and effort to address

**Outputs**:
- Comprehensive gap analysis
- Impact assessment per gap
- Independent test results (if applicable)
- Prioritized gap list

### Phase 4: Recommendations Development (2-3 days)

**Objectives**: Create actionable roadmap for improvement

**Activities**:
- **Immediate Fixes**: Identify quick wins that can be implemented rapidly
- **Short-Term Improvements**: Tactical improvements (new tools, process changes, training)
- **Long-Term Strategy**: Strategic roadmap for building mature evaluation capabilities
- **Tool Recommendations**: Specific tools or frameworks to adopt
- **Process Improvements**: Changes to methodology, documentation, or workflows
- **Integration Guidance**: How to integrate improvements into existing SDLC

**Outputs**:
- Prioritized recommendations roadmap
- Tool and framework recommendations
- Process improvement guidance
- Integration strategy

### Phase 5: Reporting and Presentation (1-2 days)

**Activities**:
- Compile findings into comprehensive report
- Create executive summary for leadership
- Develop presentation for technical teams
- Prepare recommendations roadmap

---

## Key Areas of Assessment

### Test Coverage

**Evaluation Criteria**:
- Are all important risk areas being tested?
- Is coverage aligned with OWASP LLM Top 10, MITRE ATLAS?
- Are edge cases and novel attack vectors considered?
- Is there systematic coverage or ad hoc testing?

**Common Gaps**:
- Missing tests for specific threat categories (e.g., data exfiltration, model theft)
- Incomplete coverage of trust boundaries
- Focus on one risk type while ignoring others (e.g., only prompt injection, no data poisoning)

### Testing Methodology

**Evaluation Criteria**:
- Is testing following a structured methodology?
- Are tests threat-model driven or ad hoc?
- Is there clear objective-setting for each test cycle?
- Are test scenarios grounded in realistic attack patterns?

**Common Gaps**:
- Ad hoc testing without methodology
- Lack of threat modeling before testing
- Unclear test objectives
- Tests not grounded in realistic scenarios

### Automation and Tooling

**Evaluation Criteria**:
- What tools are used for automated evaluation?
- Is continuous testing integrated into CI/CD?
- Are there release gates based on evaluation results?
- Is the balance of automated vs. manual testing appropriate?

**Common Gaps**:
- No automation, all manual testing
- Over-reliance on automation without human review
- Automation not integrated into CI/CD
- No release gates or quality thresholds

### People and Expertise

**Evaluation Criteria**:
- Who is responsible for evaluations?
- Do evaluators have security expertise?
- Is there training on ML attacks and red teaming?
- Is there a dedicated red team or security involvement?

**Common Gaps**:
- Evaluators lack security expertise
- No dedicated red team
- Insufficient training on AI security
- Security team not involved in evaluation

### Reproducibility and Documentation

**Evaluation Criteria**:
- Are test results well-documented?
- Can tests be reproduced reliably?
- Are prompts, seeds, and configurations saved?
- Is there versioning of test results?

**Common Gaps**:
- Results documented only informally
- Missing reproduction details
- No versioning of test cases
- Can't rerun tests after model updates

### Framework Alignment

**Evaluation Criteria**:
- Are tests mapped to frameworks (NIST, MITRE ATLAS, OWASP)?
- Is there alignment with regulatory requirements?
- Are evaluation criteria defined systematically?
- Is there a risk register feeding into test creation?

**Common Gaps**:
- No framework mapping
- Ad hoc evaluation criteria
- Not aligned with regulatory requirements
- Missing risk register or systematic risk identification

### Metrics and Continuous Improvement

**Evaluation Criteria**:
- Are metrics tracked from evaluations?
- Are thresholds defined (e.g., max failure rate)?
- Do metrics inform continuous improvement?
- Are tests updated when new issues are found?

**Common Gaps**:
- No metrics or trend analysis
- No quality thresholds
- Tests not updated after incidents
- No continuous improvement process

---

## Tooling and Analysis Methods

### Assessment Tools

**Process Analysis**:
- **Pipeline Inspection**: Review CI/CD configs, GitHub Actions, Jenkins pipelines
- **Documentation Review**: Analyze test plans, reports, procedures
- **Interview Guides**: Structured interviews with evaluation teams

**Validation Testing**:
- **Independent Red Team Test**: Small-scale red team exercise to validate if pipeline missed issues
- **Test Re-Run**: Attempt to reproduce past evaluation results
- **Coverage Analysis Tools**: Compare test coverage to framework requirements

**Maturity Assessment**:
- **Maturity Models**: Evaluate current state against AI evaluation maturity frameworks
- **Benchmarking**: Compare practices to industry standards

### Analysis Methods

- **Structured Interviews**: Systematic engagement with stakeholders
- **Artifact Review**: Comprehensive analysis of documentation and code
- **Gap Analysis**: Systematic comparison to best practices
- **Root Cause Analysis**: Identify why gaps exist

---

## Deliverables

### 1. Executive Summary Report (5-8 pages)

- **Current State Overview**: High-level assessment of evaluation maturity
- **Key Gaps**: Top priority gaps with business impact
- **Strategic Recommendations**: High-level guidance for building evaluation capabilities
- **Maturity Assessment**: Current state vs. target state

### 2. Technical Assessment Report (30-50 pages)

- **Current Process Documentation**: Detailed description of existing evaluation processes
- **Gap Analysis**: Comprehensive list of identified gaps with evidence
- **Framework Alignment**: Comparison of current practices to NIST, MITRE ATLAS, OWASP
- **Root Cause Analysis**: Why gaps exist (process, tooling, expertise, resources)
- **Independent Test Results**: Findings from validation testing (if performed)

### 3. Best Practice Comparison

- **Industry Benchmarking**: Comparison to leading practices
- **Framework Requirements**: What frameworks require vs. what's currently done
- **Maturity Model Assessment**: Current maturity level across dimensions

### 4. Recommendations Roadmap

- **Immediate Fixes** (0-3 days): Quick wins that can be implemented rapidly
- **Short-Term Improvements** (1-4 weeks): Tactical improvements (tools, processes, training)
- **Long-Term Strategy** (1-6 months): Strategic roadmap for mature evaluation capabilities
- **Effort Estimates**: Time and resources required per recommendation
- **Dependencies**: Sequencing of improvements

### 5. Tool and Framework Recommendations

- **Specific Tools**: Recommended tools for automation, testing, monitoring
- **Framework Adoption**: Guidance on implementing NIST AI RMF, OWASP practices
- **Integration Guidance**: How to integrate tools into existing pipelines

### 6. Process Improvement Templates

- **Test Plan Templates**: Structured templates for planning evaluations
- **Documentation Templates**: Standard formats for documenting results
- **Metrics Dashboards**: Recommended metrics and tracking approaches

---

## Evidence Standards

### Confirmed Gap

A process gap is confirmed when:

- **Identified**: Gap is clearly identified through artifact review, interviews, or validation testing
- **Validated**: Independent testing or analysis confirms the gap exists and has impact
- **Contextualized**: Gap is assessed in the context of the organization's evaluation maturity and requirements
- **Documented**: Complete analysis with evidence, impact assessment, and recommendations

**Severity Scoring**:
- **Critical**: Gap allows critical vulnerabilities to go undetected, no testing for major threat categories
- **High**: Significant gaps in coverage or methodology that could miss important vulnerabilities
- **Medium**: Moderate gaps that reduce effectiveness but don't completely miss threat categories
- **Low**: Minor gaps or process improvements that would enhance evaluation quality

### Hypothesis / Observation

Lower-confidence findings documented separately:

- **Hypothesis**: Potential gap requiring deeper validation
- **Observation**: Process weakness that doesn't meet gap threshold but merits attention
- **Best Practice Gap**: Missing industry best practices that would improve evaluation quality

---

## Framework Mapping

This engagement evaluates alignment with:

### NIST AI RMF

**Functions Evaluated**:
- MEASURE: Whether evaluation processes adequately measure AI system risks
- MANAGE: Whether evaluation results inform risk management decisions
- GOVERN: Whether evaluation processes are governed effectively
- MAP: Whether evaluation covers all relevant risk categories

### MITRE ATLAS

**Coverage Assessment**: Evaluates whether evaluation pipeline tests against relevant ATLAS tactics and techniques:
- AML.TA0001: AI Attack Staging
- AML.TA0002: Model Development
- AML.TA0004: Execution
- AML.TA0006: Evasion
- AML.TA0011: Exfiltration

### OWASP LLM Top 10

**Coverage Evaluation**: Assesses whether evaluation pipeline tests against OWASP LLM Top 10:
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

### OWASP GenAI Red Teaming Guide

**Methodology Alignment**: Evaluates whether evaluation processes follow OWASP GenAI Red Teaming Guide best practices:
- Structured methodology (planning, execution, post-analysis)
- Clear objectives for each test cycle
- Framework-aligned test coverage
- Reproducibility and documentation standards

---

## Example Findings

### Finding 1: No Adversarial Testing Prior to Deployment

**Gap Category**: Test Coverage

**Severity**: High

**Summary**: The organization does not conduct dedicated red team tests on the LLM chatbot before each major release. Testing is limited to functional QA and basic content filtering validation. This allowed prompt injection vulnerabilities to go undetected until discovered in production.

**Impact**: Vulnerabilities reach production, causing security incidents and requiring emergency patches. Lack of proactive testing means issues are discovered by attackers or users, not internal teams.

**Evidence**:
- Review of release process: No red team step in pre-deployment checklist
- Historical incidents: Prompt injection vulnerability found in production last quarter
- Test coverage analysis: 0% coverage of OWASP LLM01 (Prompt Injection) in pre-release tests

**Recommendations**:
1. **Immediate** (0-3 days): Add adversarial test suite to pre-release checklist
2. **Short-term** (1-2 weeks): Integrate prompt injection test suite into CI/CD pipeline
3. **Long-term** (1-3 months): Establish dedicated red team function, implement continuous adversarial testing

**Framework Alignment**: NIST AI RMF MEASURE 2.3 (robustness validation), OWASP GenAI Red Teaming Guide

---

### Finding 2: Incomplete Threat Coverage

**Gap Category**: Test Coverage

**Severity**: Medium

**Summary**: Current evaluation focuses on prompt content filters but ignores data exfiltration risks. No tests exist for whether the model will leak training data or user data. Evaluation covers OWASP LLM01 (Prompt Injection) but not LLM02 (Sensitive Information Disclosure).

**Impact**: Data exfiltration vulnerabilities may go undetected. Organization lacks visibility into privacy risks from model behavior.

**Evidence**:
- Test inventory: 15 tests for prompt injection, 0 tests for data leakage
- OWASP coverage: LLM01 covered, LLM02 not covered
- Historical gaps: No incidents reported, but also no testing to detect them

**Recommendations**:
1. **Immediate** (0-3 days): Add data leakage test scenarios (canary secrets, PII probes)
2. **Short-term** (1-2 weeks): Expand test suite to cover all OWASP LLM Top 10 categories
3. **Long-term** (1-3 months): Implement comprehensive threat coverage aligned with MITRE ATLAS

**Framework Alignment**: OWASP LLM Top 10, MITRE ATLAS AML.T0056 (LLM Data Leakage)

---

### Finding 3: Lack of Reproducibility

**Gap Category**: Reproducibility and Documentation

**Severity**: Medium

**Summary**: Issues found in internal red teaming are documented only in informal Confluence pages. Reproduction details (prompts, seeds, model versions) are not consistently saved. This makes it difficult to retest after fixes or verify that vulnerabilities are actually remediated.

**Impact**: Can't validate fixes effectively. Lessons don't persist across model updates. Evaluation results can't be used for regression testing.

**Evidence**:
- Documentation review: 12 findings documented, only 3 have complete reproduction steps
- Retest attempts: Unable to reproduce 4 of 5 "fixed" issues due to missing details
- Process analysis: No standard format for documenting test results

**Recommendations**:
1. **Immediate** (0-3 days): Create standard template for documenting test results with all required fields
2. **Short-term** (1-2 weeks): Implement versioned test case repository, store all prompts and configurations
3. **Long-term** (1-3 months): Integrate test case management into evaluation pipeline, automated regression testing

**Framework Alignment**: OWASP GenAI Red Teaming Guide (reproducibility requirements)

---

### Finding 4: Over-Reliance on Automation Without Human Review

**Gap Category**: Automation and Tooling

**Severity**: Medium

**Summary**: The team uses an automated tool that generates adversarial prompts (LLM-based fuzzer). However, they reported many false negatives because the tool gets stuck optimizing its prompts and misses subtle exploits. No manual red team sessions are conducted to complement automation.

**Impact**: Automation misses nuanced vulnerabilities that require human creativity. False sense of security from automated tool results.

**Evidence**:
- Tool usage: 100% automated, 0% manual red teaming
- False negative analysis: Tool missed 3 critical vulnerabilities found in production
- Tool limitations: Gets stuck on certain prompt patterns, doesn't adapt to model updates

**Recommendations**:
1. **Immediate** (0-3 days): Schedule quarterly manual red team sessions
2. **Short-term** (1-2 weeks): Implement human-in-the-loop review of automated tool results
3. **Long-term** (1-3 months): Establish balanced approach: automation for breadth, manual for depth

**Framework Alignment**: OWASP GenAI Red Teaming Guide (balance of automation and human expertise)

---

### Finding 5: No Alignment to Frameworks

**Gap Category**: Framework Alignment

**Severity**: Low

**Summary**: Evaluation criteria are defined ad hoc. They are not mapped to a comprehensive framework, risking oversight of certain risk dimensions. For example, tests don't systematically cover all NIST AI RMF MEASURE categories.

**Impact**: Risk of missing entire threat categories. Difficult to demonstrate compliance or communicate risk posture to stakeholders.

**Evidence**:
- Framework mapping: 0% of tests mapped to NIST, MITRE ATLAS, or OWASP
- Coverage gaps: NIST AI RMF MEASURE categories not systematically covered
- Stakeholder communication: Can't easily explain evaluation coverage to executives or auditors

**Recommendations**:
1. **Immediate** (0-3 days): Map existing tests to OWASP LLM Top 10
2. **Short-term** (1-2 weeks): Align test coverage to NIST AI RMF MEASURE categories
3. **Long-term** (1-3 months): Implement framework-aligned test planning and reporting

**Framework Alignment**: NIST AI RMF, MITRE ATLAS, OWASP LLM Top 10

---

## Output Format

### Finding Structure

Each identified gap follows this format:

**[Gap ID]**: [Descriptive Title]

**Gap Category**: [Test Coverage | Testing Methodology | Automation/Tooling | People/Expertise | Reproducibility/Documentation | Framework Alignment | Metrics/Continuous Improvement]

**Severity**: [Critical | High | Medium | Low]

**Summary**: 
[Brief description of the gap and why it matters]

**Impact**: 
[What vulnerabilities or risks could slip through due to this gap]

**Evidence**:
- Artifact Review: [Reference to documentation or process analysis]
- Interview Findings: [Reference to stakeholder interviews]
- Validation Testing: [Reference to independent testing if performed]

**Current State**:
[Description of current process or lack thereof]

**Recommended State**:
[Description of what the process should look like]

**Remediation Guidance**:

1. **Immediate** (0-3 days): [Quick fix or stopgap]
2. **Short-term** (1-4 weeks): [Tactical improvement]
3. **Long-term** (1-6 months): [Strategic process improvement]

**Validation Criteria**:
[How to confirm the gap is addressed]

**References**:
- Framework: [Link to relevant framework guidance]
- Best Practices: [Link to industry best practices]

---

## Engagement Inputs Required

### Before Assessment Begins

- [x] **Process Documentation**: Test plans, red team reports, evaluation procedures
- [x] **Pipeline Configurations**: CI/CD configs, automated test scripts, evaluation harnesses
- [x] **Tool Inventory**: List of tools used for evaluation
- [x] **Historical Results**: Past evaluation results, incident reports, known vulnerabilities
- [x] **Stakeholder Access**: Availability of evaluation team members for interviews
- [x] **Metrics and Tracking**: How evaluation results are measured and stored
- [x] **Organizational Context**: Regulatory requirements, industry standards, business priorities

### During Assessment

- Timely responses to clarification questions (target: under 24hr turnaround)
- Access to evaluation tools and pipelines for inspection
- Ability to run small validation tests if needed
- Optional mid-assessment checkpoint (recommended at 50% completion)

---

## Limitations

- Assessment based on information provided and available documentation; gaps in documentation may limit analysis
- Validation testing is limited in scope; comprehensive red teaming requires separate engagement
- Recommendations are consultative; implementation requires separate effort
- Results valid for assessed processes as of [assessment date]; process changes may alter findings
- Time-bounded assessment; exhaustive analysis of all possible process gaps not feasible

---

## Pricing and Timeline

**Base Duration**: 1-2 weeks (10-15 business days)

**Effort**: 8-12 person-days

**Timeline Breakdown**:
- Scoping call and artifact collection: +2-3 business days (pre-engagement)
- Assessment execution: 10-15 business days
- Reporting and delivery: +2-3 business days
- Optional follow-up consultation: +2-3 business days

**Pricing**: Contact for quote based on organization size, process complexity, and scope

**Add-ons Available**:
- **Validation Red Team Test** (+5 days): Small-scale red team exercise to validate if pipeline missed issues
- **Implementation Support** (+10 days): Hands-on assistance implementing recommended improvements
- **Training Workshop** (+2 days): Training session on AI red teaming best practices
- **Follow-Up Assessment** (+5 days): Re-assessment after improvements to measure progress

---

## Related Engagements

- **LLM Application Red Team**: Choose this for active adversarial attack simulation. Evaluation Pipeline Review audits processes; Red Team performs actual testing.
- **AI Model Risk Assessment**: Choose this for holistic model risk review. Evaluation Pipeline Review focuses on evaluation processes, not model risks.
- **AI Threat Exposure Review**: Choose this for comprehensive security testing. Evaluation Pipeline Review improves internal testing capabilities.

---

## Getting Started

1. **Review this spec** to confirm it matches your process improvement objectives
2. **Prepare engagement inputs** using checklist above
3. **Check [[methodology|Methodology]]** to understand our framework-aligned approach
4. **Explore applicable issues**: [[methodology|Methodology Overview]]
5. **[[contact|Contact us]]** to discuss scoping, timeline, and pricing

---

## Technical References

- [[methodology|Methodology]]
- [[trust-boundaries|Trust Boundaries Overview]]
- [[techniques|MITRE ATLAS Techniques]]
- [OWASP GenAI Red Teaming Guide](https://owasp.org/www-project-genai-red-teaming-guide/)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
