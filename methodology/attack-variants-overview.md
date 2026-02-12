---
tags:
  - trust-boundary/deployment-governance
  - trust-boundary/model
  - type/methodology
  - type/overview
---
# Attack-Surface Variants Overview

## Purpose

AI systems vary significantly in architecture and attack surface. A one-size-fits-all testing approach misses system-specific vulnerabilities. The methodology adapts to five primary attack-surface variants, each requiring tailored threat modeling, test planning, and execution strategies.

**Key principle:** The core 9-phase lifecycle remains consistent, but Phase 3 (Threat Modeling), Phase 4 (Test Planning), and Phase 5 (Execution) adapt significantly based on system type.

---

## Why Attack-Surface Variants Matter

**Different systems, different threats:**
- Standalone LLM chatbots face prompt-based attacks
- RAG systems add retrieval layer vulnerabilities (indirect injection, data poisoning)
- Agentic systems introduce tool misuse and autonomy risks
- Supply chain threats target model and dependency integrity
- Evaluation pipelines can be gamed or evaded

**Testing must adapt:**
- Threat models enumerate system-specific attack vectors
- Test techniques target relevant attack surfaces
- Evidence capture accounts for system-specific behaviors
- Tooling and automation vary by system type

---

## The Five Attack-Surface Variants

### 1. LLM Applications (Standalone)

**System characteristics:**
- Large language model as primary component
- User input → model inference → output
- Minimal external integrations
- Examples: Q&A chatbots, text generation APIs, content moderation

**Primary attack surface:**
- Model input/output behavior
- Prompt manipulation
- Training data extraction
- Safety guardrail bypass

**Key threat scenarios:**
- Jailbreaks (bypassing content policies)
- Prompt injection (instruction override)
- System prompt extraction
- Training data leakage
- Bias and toxicity exploitation

[[variant-llm-applications|Detailed guidance →]]

---

### 2. RAG Systems (Retrieval-Augmented Generation)

**System characteristics:**
- LLM + external knowledge base
- Query → retrieval → context injection → model inference → output
- Vector databases, document stores, embedding models
- Examples: Enterprise knowledge assistants, document Q&A, semantic search

**Primary attack surface:**
- Retrieval layer (vector DB, embeddings)
- Knowledge base integrity
- Context injection into prompts
- Cross-tenant isolation

**Key threat scenarios:**
- Indirect prompt injection via documents
- Data poisoning (malicious documents in knowledge base)
- Embedding manipulation (similarity gaming)
- Cross-tenant data leakage
- Citation manipulation

[[variant-rag-systems|Detailed guidance →]]

---

### 3. Agentic Systems (LLM-Based Agents)

**System characteristics:**
- LLM with tool access and multi-step reasoning
- Planning → tool invocation → result interpretation → next action
- Autonomous or semi-autonomous operation
- Examples: Code generation agents, research assistants, workflow automation

**Primary attack surface:**
- Tool integrations (APIs, databases, file systems)
- Agent decision-making and planning
- Multi-step reasoning chains
- Memory and state management

**Key threat scenarios:**
- Tool privilege escalation (unauthorized API access)
- Argument injection (SQL/command injection via tool parameters)
- Goal hijacking (redirecting agent objectives)
- Feedback loop poisoning (corrupting agent memory)
- Sandbox escapes

[[variant-agentic-systems|Detailed guidance →]]

---

### 4. Supply Chain (Model & Dependency Integrity)

**System characteristics:**
- Model artifacts from external sources
- Third-party dependencies and libraries
- Model serving infrastructure
- Examples: Hugging Face models, open-source checkpoints, ML frameworks

**Primary attack surface:**
- Model weights and architecture
- Training pipelines and data
- Dependencies (Python packages, containers)
- Model serving infrastructure

**Key threat scenarios:**
- Backdoored models (Trojan triggers)
- Malicious dependencies (poisoned packages)
- Model weight tampering
- Man-in-the-middle attacks (model downloads)
- Infrastructure compromise

[[variant-supply-chain|Detailed guidance →]]

---

### 5. Evaluation Pipelines (Governance & Testing Infrastructure)

**System characteristics:**
- Automated evaluation harnesses
- Continuous monitoring systems
- Human feedback loops (RLHF)
- Governance checkpoints

**Primary attack surface:**
- Evaluation metrics and test suites
- Automated detectors and filters
- Feedback collection mechanisms
- Governance approval workflows

**Key threat scenarios:**
- Gaming metrics (Goodhart's Law exploitation)
- Evasion of automated detectors
- RLHF feedback poisoning
- Evaluation set contamination
- Governance process bypass

[[variant-evaluation-pipelines|Detailed guidance →]]

---

## How the Methodology Adapts

### Phase 1-2: Intake and Decomposition

**Consistent across variants:**
- Scoping process remains the same
- Architecture review captures system-specific components
- Trust boundary mapping identifies variant-specific boundaries

**Variant-specific considerations:**
- LLM: Focus on model version, prompt templates, safety controls
- RAG: Document retrieval architecture, vector DB, embedding model
- Agent: Catalog tools, permissions, decision logic
- Supply Chain: Map model provenance, dependency tree, infrastructure
- Eval Pipeline: Document metrics, test suites, feedback loops

---

### Phase 3: Threat Modeling

**Significant adaptation required:**

**LLM Applications:**
- Enumerate prompt-based attacks
- Focus on model behavior manipulation
- Consider training data risks

**RAG Systems:**
- Add retrieval layer threats
- Consider indirect injection vectors
- Model cross-tenant isolation risks

**Agentic Systems:**
- Expand to tool misuse scenarios
- Consider multi-step attack chains
- Model autonomy and feedback risks

**Supply Chain:**
- Focus on integrity verification
- Consider upstream compromise
- Model infrastructure attacks

**Eval Pipelines:**
- Meta-threat modeling (testing the tests)
- Consider metric gaming
- Model feedback manipulation

[[phase-3-threat-modeling|Phase 3 guidance →]]

---

### Phase 4: Test Planning

**Technique selection varies by variant:**

**LLM Applications:**
- Jailbreak libraries
- Prompt injection variations
- Encoding tricks and translation attacks
- Bias and toxicity test suites

**RAG Systems:**
- Document poisoning scenarios
- Retrieval manipulation techniques
- Cross-tenant probing methods
- Indirect injection payloads

**Agentic Systems:**
- Tool argument injection techniques
- Goal hijacking prompts
- Feedback poisoning strategies
- Sandbox escape attempts

**Supply Chain:**
- Model integrity verification methods
- Dependency scanning tools
- Differential testing approaches
- Infrastructure penetration techniques

**Eval Pipelines:**
- Adversarial example generation (to evade detectors)
- Metric gaming strategies
- Feedback manipulation techniques
- Governance bypass scenarios

[[phase-4-test-planning|Phase 4 guidance →]]

---

### Phase 5: Execution

**Testing approach varies significantly:**

**LLM Applications:**
- Primarily manual conversational testing
- Automated prompt fuzzing
- Multi-turn manipulation
- Evidence: Conversation transcripts

**RAG Systems:**
- Manual + automated retrieval testing
- Document injection (requires data access)
- Cross-tenant probing
- Evidence: Retrieval logs, leaked documents

**Agentic Systems:**
- Complex multi-step scenarios
- Tool simulation and mocking
- Safety harnesses critical (agents can take actions)
- Evidence: Agent decision logs, tool invocations

**Supply Chain:**
- Technical analysis (model inspection, dependency scanning)
- Differential testing
- Infrastructure penetration
- Evidence: Model artifacts, scan results, infrastructure logs

**Eval Pipelines:**
- Meta-testing (testing the tests)
- Adversarial example generation
- Feedback injection
- Evidence: Eval results, detector outputs, metrics

[[phase-5-execution|Phase 5 guidance →]]

---

### Phase 6-9: Triage Through Retest

**Largely consistent across variants:**
- Severity scoring applies same dimensions
- Reporting structure remains consistent
- Remediation guidance adapts to system type
- Retest validation follows same process

**Variant-specific considerations:**
- Evidence requirements vary (logs, artifacts, etc.)
- Remediation controls differ by system type
- Regression testing approaches vary

---

## Choosing the Right Variant Guidance

**For your engagement, identify:**

1. **Primary system type:** What is the core architecture?
2. **Hybrid systems:** Does it combine multiple variants? (e.g., RAG + Agent)
3. **Attack surface priority:** Which variant's threats are highest risk?

**If hybrid:**
- Apply guidance from all relevant variants
- Prioritize based on business risk
- Consider interaction effects (e.g., agent using RAG)

**Example:**

**System:** Enterprise knowledge assistant with tool access
- **Architecture:** RAG (vector DB + LLM) + Agent (can invoke APIs)
- **Variants:** RAG + Agentic
- **Approach:** 
  - Use RAG variant guidance for retrieval threats
  - Use Agentic variant guidance for tool threats
  - Consider combined attacks (poisoned document triggers tool misuse)

---

## Variant-Specific Resources

### LLM Applications
- [[variant-llm-applications|Detailed methodology →]]
- [[attacks/|Model boundary issues →]]
- [[llm-application-red-team|Relevant engagements →]]

### RAG Systems
- [[variant-rag-systems|Detailed methodology →]]
- [[attacks/|Data/Knowledge boundary issues →]]
- [[rag-retrieval-assessment|Relevant engagements →]]

### Agentic Systems
- [[variant-agentic-systems|Detailed methodology →]]
- [[methodology/application-agent-boundary-overview|Application/Agent boundary issues →]]
- [[agentic-workflow-assessment|Relevant engagements →]]

### Supply Chain
- [[variant-supply-chain|Detailed methodology →]]
- [[ai-supply-chain-security|Relevant engagements →]]

### Evaluation Pipelines
- [[variant-evaluation-pipelines|Detailed methodology →]]
- [[evaluation-pipeline-review|Relevant engagements →]]

---

## Related Documentation

- [[lifecycle-overview|Lifecycle Overview]] - Core 9-phase methodology
- [[phase-3-threat-modeling|Phase 3: Threat Modeling]] - Variant-specific threat enumeration
- [[phase-4-test-planning|Phase 4: Test Planning]] - Technique selection by variant
- [[phase-5-execution|Phase 5: Execution]] - Testing approaches by variant
- [[trust-boundaries-overview|Trust Boundaries]] - Attack taxonomy by boundary
- [[playbooks/engagements-overview|Engagements]] - Engagement types by system variant
