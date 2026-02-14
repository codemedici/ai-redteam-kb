---
title: "AI Evaluation Pipeline Review"
tags:
  - type/playbook
  - target/llm
  - target/generative-ai
  - target/ml-model
maturity: reviewed
created: 2026-02-14
updated: 2026-02-14
---

# AI Evaluation Pipeline Review

## Overview

A consultative engagement that reviews an organization's own AI testing and evaluation processes rather than directly attacking AI models. This meta-red-team engagement audits how the company evaluates and validates its AI systems, examining their testing pipeline, metrics, coverage, and alignment with best practices.

**Why this playbook exists**: Many organizations conduct AI red teaming, but their evaluation processes may have gaps that allow vulnerabilities to slip through. Without systematic evaluation pipeline review, organizations risk missing threat categories, over-relying on automation, lacking reproducibility, or misaligning with frameworks. This playbook helps organizations build mature, continuous AI evaluation capabilities integrated into their development lifecycle.

**What this is NOT**:
- Not active red teaming (process audit, not adversarial attack simulation)
- Not model risk assessment (focuses on evaluation processes, not model risks themselves)
- Not compliance audit (provides actionable process improvements, not just regulatory checkboxes)
- Not tool implementation (reviews and recommends, doesn't implement tools or processes)

---

## Scope & Prerequisites

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

### Prerequisites

#### Before Assessment Begins

- **Process Documentation**: Test plans, red team reports, evaluation procedures
- **Pipeline Configurations**: CI/CD configs, automated test scripts, evaluation harnesses
- **Tool Inventory**: List of tools used for evaluation
- **Historical Results**: Past evaluation results, incident reports, known vulnerabilities
- **Stakeholder Access**: Availability of evaluation team members for interviews
- **Metrics and Tracking**: How evaluation results are measured and stored
- **Organizational Context**: Regulatory requirements, industry standards, business priorities

#### During Assessment

- Timely responses to clarification questions (target: under 24hr turnaround)
- Access to evaluation tools and pipelines for inspection
- Ability to run small validation tests if needed
- Optional mid-assessment checkpoint (recommended at 50% completion)

---

## Methodology

### Phase 1: Artifact Collection and Process Mapping (2-3 days)

**Objectives**: Understand current evaluation processes and collect evidence

**Activities**:
- **Process Documentation Review**: Collect test plans, red team reports, evaluation procedures
- **Pipeline Configuration Analysis**: Review CI/CD configs, automated test scripts, evaluation harnesses
- **Tool Inventory**: Document tools used for evaluation (automated frameworks, manual processes)
- **Stakeholder Interviews**: Engage with teams responsible for model evaluation
- **Historical Results Review**: Analyze past evaluation results, incident reports, known vulnerabilities
- **Metrics and Tracking**: Review how evaluation results are measured, stored, and tracked

**Outputs**:
- Current process documentation
- Tool and pipeline inventory
- Stakeholder interview summaries
- Historical evaluation analysis
- Process flow diagrams

---

### Phase 2: Benchmark Against Best Practices (3-4 days)

**Objectives**: Compare current practices to industry standards and frameworks

**Activities**:
- **Framework Alignment Check**: Compare current tests to OWASP LLM Top 10, MITRE ATLAS, NIST AI RMF
- **Methodology Assessment**: Evaluate whether processes follow structured approaches
- **Coverage Analysis**: Identify threat categories that are tested vs. not tested
- **Automation Evaluation**: Assess balance of automated vs. manual testing, CI/CD integration
- **Reproducibility Review**: Evaluate documentation, versioning, and reproducibility of test results
- **People and Expertise Assessment**: Review team composition, training, and security expertise

**Outputs**:
- Framework alignment matrix
- Coverage gap analysis
- Methodology maturity assessment
- Automation vs. manual balance evaluation

---

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

---

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

---

### Phase 5: Reporting and Presentation (1-2 days)

**Activities**:
- Compile findings into comprehensive report
- Create executive summary for leadership
- Develop presentation for technical teams
- Prepare recommendations roadmap

---

## Techniques Covered

| Area | Coverage Evaluated | Expected in Pipeline |
|------|-------------------|----------------------|
| **Model Security** | [[techniques/prompt-injection\|Prompt Injection]] | Should be tested via adversarial campaigns |
| **Model Security** | [[techniques/jailbreak-policy-bypass\|Jailbreak Techniques]] | Should be tested via safety guardrail validation |
| **Model Security** | [[techniques/system-prompt-leakage\|System Prompt Extraction]] | Should be tested via leakage probes |
| **Data/Knowledge** | [[techniques/rag-data-poisoning\|RAG Data Poisoning]] | Should be tested via malicious document injection |
| **Data/Knowledge** | [[techniques/pii-in-corpus\|PII in Knowledge Corpus]] | Should be tested via corpus scanning |
| **Application/Agent** | [[techniques/tool-privilege-escalation\|Tool Privilege Escalation]] | Should be tested via argument injection |
| **Application/Agent** | [[techniques/agent-goal-hijack\|Agent Goal Hijacking]] | Should be tested via workflow manipulation |
| **Deployment/Governance** | [[techniques/insufficient-telemetry-and-tracing\|Insufficient Telemetry]] | Should be validated via monitoring gap analysis |
| **Deployment/Governance** | [[techniques/secrets-in-prompts-and-logs\|Secrets Exposure]] | Should be tested via secrets scanning |

---

## Tooling

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

---

### 2. Technical Assessment Report (30-50 pages)

- **Current Process Documentation**: Detailed description of existing evaluation processes
- **Gap Analysis**: Comprehensive list of identified gaps with evidence
- **Framework Alignment**: Comparison of current practices to NIST, MITRE ATLAS, OWASP
- **Root Cause Analysis**: Why gaps exist (process, tooling, expertise, resources)
- **Independent Test Results**: Findings from validation testing (if performed)

---

### 3. Best Practice Comparison

- **Industry Benchmarking**: Comparison to leading practices
- **Framework Requirements**: What frameworks require vs. what's currently done
- **Maturity Model Assessment**: Current maturity level across dimensions:
  - Test Coverage
  - Methodology
  - Automation/Tooling
  - People/Expertise
  - Reproducibility/Documentation
  - Framework Alignment
  - Metrics/Continuous Improvement

---

### 4. Recommendations Roadmap

**Immediate Fixes** (0-3 days):
- Quick wins that can be implemented rapidly
- Example: Add data leakage test scenarios to existing test suite

**Short-Term Improvements** (1-4 weeks):
- Tactical improvements (tools, processes, training)
- Example: Integrate prompt injection test suite into CI/CD pipeline

**Long-Term Strategy** (1-6 months):
- Strategic roadmap for mature evaluation capabilities
- Example: Establish dedicated red team function, implement continuous adversarial testing

**Effort Estimates**: Time and resources required per recommendation

**Dependencies**: Sequencing of improvements

---

### 5. Tool and Framework Recommendations

- **Specific Tools**: Recommended tools for automation, testing, monitoring
- **Framework Adoption**: Guidance on implementing NIST AI RMF, OWASP practices
- **Integration Guidance**: How to integrate tools into existing pipelines

---

### 6. Process Improvement Templates

- **Test Plan Templates**: Structured templates for planning evaluations
- **Documentation Templates**: Standard formats for documenting results
- **Metrics Dashboards**: Recommended metrics and tracking approaches

---

## Sources

> "Many organizations conduct AI red teaming, but their evaluation processes may have gaps that allow vulnerabilities to slip through."
> — [[sources/bibliography#OWASP GenAI Red Teaming Guide]]

> "Without systematic evaluation pipeline review, organizations risk missing threat categories, over-relying on automation, lacking reproducibility, or misaligning with frameworks."
> — [[sources/bibliography#Red-Teaming AI]]

> "This playbook helps organizations build mature, continuous AI evaluation capabilities integrated into their development lifecycle."
> — [[sources/bibliography#OWASP GenAI Red Teaming Guide]]
