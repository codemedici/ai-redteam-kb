---
title: "STRATEGEMS Framework"
tags:
  - type/framework
  - source/red-teaming-ai
maturity: reviewed
created: 2026-02-11
updated: 2026-02-14
---

# STRATEGEMS: Strategic AI Red Teaming Methodology

## Overview

STRATEGEMS is a proprietary implementation framework developed by HYPERGAME that operationalizes AI red teaming through integration of AI vs AI dynamics, systems thinking, and strategic wargaming. It builds upon standard red team lifecycle phases with enhanced protocol emphasizing strategic depth, systems analysis, and countering advanced AI threats.

**Maintained by**: HYPERGAME  
**Purpose**: Implementation framework for executing standard red team lifecycle stages, not replacement of them

The framework is particularly valuable for:
- Conducting strategic AI security assessments
- Facing sophisticated adversaries with AI capabilities
- Systems with complex interdependencies
- Scenarios where standard pentesting misses AI-specific risks

> "STRATEGEMS serves as the author's proprietary implementation framework that builds upon and integrates these general phases."
> — [[sources/bibliography#Red-Teaming AI]], p. 61-62

---

## Structure

STRATEGEMS enhances the standard red team lifecycle (scoping, reconnaissance, threat modeling, execution, reporting) with three core integrations applied across all phases:

### Core Integrations

#### 1. AI vs AI Dynamics

**Incorporation of**:
- Hypergame theory concepts
- AI-driven active defense
- Intelligent, adaptive adversary simulation

**Purpose**: Simulate and counter adversaries who themselves leverage AI capabilities

**Recognition**: Sophisticated adversaries increasingly use AI as weapon—automating reconnaissance, generating novel attack vectors, creating convincing phishing/social engineering at scale

**Application**: Every red team phase considers how an AI-augmented adversary would approach the target

---

#### 2. Systems Thinking (Mandatory)

**Required tools**:
- **Design Structure Matrices (DSM)**: Analyze component dependencies
- **Model-Based Systems Engineering (MBSE) principles**: Deep analysis of system interdependencies

**Focus areas**:
- Potential cascading failures
- Feedback loops
- Critical nodes in system architecture
- Downstream effects of component compromise

**Why mandatory**: AI vulnerabilities often emerge from complex interactions between components, not isolated bugs

**Application**: Threat modeling and execution phases must map system dependencies using DSM/MBSE

---

#### 3. AI Red Teaming / Wargaming

**Approach**: Structured, threat-driven defense perspective

**Objective**: Achieving strategic objectives, not just finding isolated bugs

**Distinction**: Moves beyond checklist-driven testing to adversarial simulation designed to uncover deep-seated systemic risks

**Application**: Engagement goals focused on adversarial objectives (e.g., "Achieve data exfiltration from RAG corpus" vs. "Test for prompt injection")

---

## Key Components

### Strategic vs Tactical Focus

**Traditional Testing (Avoided)**:
- Checklist-driven
- Component-level focus
- Known CVE hunting
- Deterministic code logic

**STRATEGEMS Approach (Embraced)**:
- Strategic campaign
- Systems-level focus
- Novel vulnerability discovery
- Emergent AI behaviors

**Strategic Examples**:
- Mapping attack chains across trust boundaries
- Identifying high-impact systemic risks
- Understanding cascading failure modes
- Achieving adversarial objectives (not just exploiting weaknesses)

---

### Adversarial Mindset Foundation

STRATEGEMS assumes red team possesses AI-centric adversarial mindset:

- **Deep target understanding**: Architecture, training data, limits, assumptions
- **Creativity & lateral thinking**: Beyond standard checklists
- **Data and logic focus**: Targeting emergent logic from data
- **Uncertainty exploitation**: Edge cases, low-confidence predictions
- **Socio-technical consideration**: Human elements, deployment infrastructure
- **AI weaponization awareness**: Adversary use of AI tools
- **Persistence & iteration**: Experimentation, hypothesis refinement
- **Graph thinking**: Component interactions, data flows, dependencies, cascading effects

---

### Consistency + Adaptability

**Structured process provides**:
- Consistency across engagements
- Repeatability of approach
- Thoroughness in coverage

**While maintaining**:
- Adaptability to AI-specific characteristics
- Fluidity in response to observations
- Creativity in attack hypothesis

**Balance**: Framework ensures essential areas aren't missed while allowing creative exploration of AI-specific attack surfaces

---

## Integration Points

### Integration with Standard Red Team Lifecycle

STRATEGEMS enhances each standard phase:

**Phase 1: Scoping**
- Systems thinking: Map dependencies upfront using DSM
- Strategic focus: Define adversarial objectives, not just test categories

**Phase 2: Reconnaissance**
- AI vs AI: Consider what AI tools adversary would use
- Systems thinking: Identify critical nodes and feedback loops

**Phase 3: Threat Modeling**
- Systems thinking: Mandatory DSM/MBSE analysis
- AI-specific: Use MITRE ATLAS, OWASP LLM Top 10
- Strategic: Map attack chains, not isolated threats

**Phase 4: Execution**
- AI vs AI: Simulate intelligent, adaptive adversary
- Systems thinking: Test cascading failures
- Strategic: Achieve objectives, not just find bugs

**Phase 5: Reporting**
- Strategic: Communicate systemic risks and business impact
- Systems thinking: Show dependency chains and cascading effects

---

### Integration with Threat Modeling

**STRATEGEMS threat modeling adaptations**:
- Data dependency analysis (provenance, integrity)
- Model vulnerabilities (evasion, extraction, inversion)
- Emergent behavior consideration
- Probabilistic nature of AI outputs
- Expanded attack surface mapping
- Systemic and structural risk assessment

**Frameworks integrated**:
- MITRE ATLAS (AI-specific TTPs)
- OWASP Top 10 for LLMs
- Traditional frameworks (STRIDE) adapted for AI

**Related**: [[frameworks/threat-modeling|AI Threat Modeling Framework]]

---

### Related Frameworks

- [[frameworks/MAESTRO|MAESTRO Framework]]: Multi-agent threat modeling (complementary for agentic systems)
- [[frameworks/threat-modeling|AI Threat Modeling Framework]]: Foundational threat modeling approach
- [[frameworks/OWASP-top-10-LLM|OWASP Top 10 for LLM Applications]]: Risk catalog integration

### Related Playbooks

- [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]]: Comprehensive strategic assessment
- [[playbooks/llm-application-red-team|LLM Application Red Team]]: Tactical execution within strategic framework
- [[playbooks/cross-boundary-attack-chains|Cross-Boundary Attack Chains]]: Systems thinking application

---

## Sources

> "STRATEGEMS serves as the author's proprietary implementation framework that builds upon and integrates these general phases."
> — [[sources/bibliography#Red-Teaming AI]], p. 61-62

> "STRATEGEMS uniquely fuses: (1) AI vs AI Dynamics: Incorporating concepts from hypergame theory and AI-driven active defense to simulate and counter intelligent, adaptive adversaries. (2) Systems Thinking: Mandating the use of tools like Design Structure Matrices (DSM) and Model-Based Systems Engineering (MBSE) principles for deep analysis of system interdependencies and potential cascading failures. (3) AI Red Teaming/Wargaming: Applying a structured, threat-driven defense perspective focused on achieving strategic objectives, not just finding isolated bugs."
> — [[sources/bibliography#Red-Teaming AI]], p. 61-62

> "Think of the following phases as the standard lifecycle stages, and STRATEGEMS as a specific, enhanced protocol for executing those stages with a greater emphasis on strategic depth, systems analysis, and countering advanced AI threats."
> — [[sources/bibliography#Red-Teaming AI]], p. 61-62
