---
tags:
  - hypergame
  - methodology
  - red-team
  - source/red-teaming-ai
  - strategems
  - trust-boundary/deployment-governance
  - type/methodology
created: 2026-02-11
source: [[sources/bibliography#Red-Teaming AI]]
pages: "61-62"
---
# STRATEGEMS: Strategic AI Red Teaming Methodology

Proprietary implementation framework by HYPERGAME that operationalizes AI red teaming through integration of AI vs AI dynamics, systems thinking, and strategic wargaming.

## Framework Purpose

**STRATEGEMS** builds upon standard red team lifecycle phases with enhanced protocol emphasizing:
- Strategic depth
- Systems analysis
- Countering advanced AI threats

**Positioning:** Implementation framework for executing standard lifecycle stages, not replacement of them.

## Three Core Integrations

### 1. AI vs AI Dynamics

**Incorporation of:**
- Hypergame theory concepts
- AI-driven active defense
- Intelligent, adaptive adversary simulation

**Purpose:** Simulate and counter adversaries who themselves leverage AI capabilities.

**Recognition:** Sophisticated adversaries increasingly use AI as weapon - automating reconnaissance, generating novel attack vectors, creating convincing phishing/social engineering at scale.

### 2. Systems Thinking (Mandatory)

**Required tools:**
- **Design Structure Matrices (DSM):** Analyze component dependencies
- **Model-Based Systems Engineering (MBSE) principles:** Deep analysis of system interdependencies

**Focus areas:**
- Potential cascading failures
- Feedback loops
- Critical nodes in system architecture
- Downstream effects of component compromise

**Why mandatory:** AI vulnerabilities often emerge from complex interactions between components, not isolated bugs.

### 3. AI Red Teaming / Wargaming

**Approach:** Structured, threat-driven defense perspective

**Objective:** Achieving strategic objectives, not just finding isolated bugs

**Distinction:** Moves beyond checklist-driven testing to adversarial simulation designed to uncover deep-seated systemic risks.

## Relationship to Standard Lifecycle

**Standard phases:** Foundation stages of red team engagements (scoping, reconnaissance, threat modeling, execution, etc.)

**STRATEGEMS:** Enhanced protocol for executing those stages

**Enhancement approach:** Where relevant, STRATEGEMS informs how to conduct each phase with greater emphasis on:
- Strategic depth (adversary goals, cascading impact)
- Systems analysis (interdependencies, structural risks)
- AI-specific threats (data poisoning, evasion, model extraction)

## Key Differentiators

### Beyond Traditional Testing

**Traditional QA/Pentesting:**
- Checklist-driven
- Component-level focus
- Known CVE hunting
- Deterministic code logic

**STRATEGEMS:**
- Strategic campaign
- Systems-level focus
- Novel vulnerability discovery
- Emergent AI behaviors

### Consistency + Adaptability

**Structured process provides:**
- Consistency across engagements
- Repeatability of approach
- Thoroughness in coverage

**While maintaining:**
- Adaptability to AI-specific characteristics
- Fluidity in response to observations
- Creativity in attack hypothesis

### Strategic vs Tactical

**Tactical (avoided):** Finding individual bugs, isolated vulnerabilities

**Strategic (embraced):** 
- Mapping attack chains
- Identifying high-impact systemic risks
- Understanding cascading failure modes
- Achieving adversarial objectives (not just exploiting weaknesses)

## Integration with Threat Modeling

**STRATEGEMS threat modeling adaptations:**
- Data dependency analysis (provenance, integrity)
- Model vulnerabilities (evasion, extraction, inversion)
- Emergent behavior consideration
- Probabilistic nature of AI outputs
- Expanded attack surface mapping
- Systemic and structural risk assessment

**Frameworks integrated:**
- MITRE ATLAS (AI-specific TTPs)
- OWASP Top 10 for LLMs
- Traditional frameworks (STRIDE) adapted for AI

## Adversarial Mindset Foundation

**STRATEGEMS assumes:** Red team possesses AI-centric adversarial mindset:

- **Deep target understanding:** Architecture, training data, limits, assumptions
- **Creativity & lateral thinking:** Beyond standard checklists
- **Data and logic focus:** Targeting emergent logic from data
- **Uncertainty exploitation:** Edge cases, low-confidence predictions
- **Socio-technical consideration:** Human elements, deployment infrastructure
- **AI weaponization awareness:** Adversary use of AI tools
- **Persistence & iteration:** Experimentation, hypothesis refinement
- **Graph thinking:** Component interactions, data flows, dependencies, cascading effects

## Practical Application

**Use STRATEGEMS when:**
- Conducting strategic AI security assessments
- Facing sophisticated adversaries with AI capabilities
- Systems have complex interdependencies
- Standard pentesting misses AI-specific risks
- Systemic failures more critical than isolated bugs

**Avoid STRATEGEMS when:**
- Simple component testing suffices
- Time/resource constraints prevent deep analysis
- System is not AI-enabled or AI-adjacent
- Checklist compliance (not security) is the goal

## Related

- Phase 5 Execution
- Phase 3 Threat Modeling
- Variant Agentic Systems
- [[methodology/agentic-ai-red-teaming-discipline]]

## Source

> "STRATEGEMS serves as the author's proprietary implementation framework that builds upon and integrates these general phases."
>
> "STRATEGEMS uniquely fuses: (1) AI vs AI Dynamics: Incorporating concepts from hypergame theory and AI-driven active defense to simulate and counter intelligent, adaptive adversaries. (2) Systems Thinking: Mandating the use of tools like Design Structure Matrices (DSM) and Model-Based Systems Engineering (MBSE) principles for deep analysis of system interdependencies and potential cascading failures. (3) AI Red Teaming/Wargaming: Applying a structured, threat-driven defense perspective focused on achieving strategic objectives, not just finding isolated bugs."
>
> "Think of the following phases as the standard lifecycle stages, and STRATEGEMS as a specific, enhanced protocol for executing those stages with a greater emphasis on strategic depth, systems analysis, and countering advanced AI threats."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 61-62

## Notes

STRATEGEMS is positioned as HYPERGAME's proprietary framework but provides valuable methodology patterns for any AI red team:
1. Mandate systems thinking tools (DSM, MBSE)
2. Incorporate AI vs AI dynamics
3. Focus on strategic objectives over isolated bugs

Key insight: Standard red team phases are foundation, but execution matters. STRATEGEMS shows how to execute with AI-specific depth.

This should inform methodology/phase-* pages with systems thinking integration and AI adversary simulation techniques.
