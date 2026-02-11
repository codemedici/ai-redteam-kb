---
id: tooling-automation
title: Tooling and Automation
sidebar_position: 8
---

# Tooling and Automation

## Lab Environment Setup

**Purpose**: Create controlled, isolated, repeatable environments for safe AI security testing

**Core requirements**:
- **Isolation**: Network segmentation (VLANs), VM/container isolation, dedicated hardware
- **Hardware**: 16GB+ RAM baseline, GPU access (NVIDIA with CUDA, 8GB+ VRAM) for adversarial ML tasks
- **Software stack**: Linux (Ubuntu/Kali), Python 3.8+, virtual environments (venv/conda), package management (pip)
- **Security hygiene**: Regular updates, strong credentials, data sanitization, monitoring

**Infrastructure options**:
- **Local**: Maximum control, lower long-term cost, requires hardware investment
- **Cloud**: Scalable GPU access (AWS EC2, GCP, Azure), pay-as-you-go, requires cost/security management
- **Bare-metal GPUs**: Full hardware control, no virtualization overhead, enhanced isolation for sensitive work

## Tool Categories

### Adversarial ML Libraries

**Purpose**: Generate attacks against model logic (evasion, poisoning, extraction, inference)

**Key tools**:
- **ART (Adversarial Robustness Toolbox)**: Framework-agnostic (TensorFlow, PyTorch, scikit-learn), supports evasion (FGSM, PGD, C&W), poisoning, membership inference
- **TextAttack**: NLP-focused attacks (synonym replacement, paraphrasing, typos), modular design for text classifiers
- **Foolbox**: Clean API for adversarial examples, supports PyTorch/TensorFlow/JAX
- **CleverHans**: Reference implementations of seminal attacks (FGSM, PGD), benchmarking focus

### LLM-Specific Tools

**Purpose**: Test prompt injection, jailbreaking, data leakage, harmful content generation

**Vulnerability scanners**:
- **Garak**: Automated prompt injection and jailbreak testing with curated payloads
- **llm-guard**: Security toolkit for LLM interactions
- **PyRIT (Microsoft)**: Automated risk identification framework for generative AI
- **Rebuff/Vigil**: Prompt injection and jailbreak detectors

**Interaction frameworks**:
- **LangChain**: Structured multi-turn interactions, chain testing, agentic workflow analysis
- **LlamaIndex**: Context retrieval testing, RAG vulnerability assessment

**Usage notes**: Respect rate limits, API costs, Terms of Service; avoid unintentional DoS

### Standard Penetration Testing Tools

**Purpose**: Assess traditional infrastructure supporting AI systems (APIs, databases, cloud services)

**Core tools**:
- **Web proxies** (Burp Suite, OWASP ZAP): Intercept API traffic, fuzz parameters, test authentication, identify information disclosure
- **Network scanners** (Nmap): Map infrastructure, identify open ports, discover services
- **Vulnerability scanners** (Nessus, OpenVAS): Detect CVEs in OS, web servers, databases supporting AI pipelines
- **Exploitation frameworks** (Metasploit): Test and exploit infrastructure vulnerabilities
- **Cloud security auditors** (Prowler, ScoutSuite): Audit IAM roles, storage bucket permissions, ML service configurations

**Integration**: Correlate infrastructure findings with AI-specific vulnerabilities (e.g., outdated web server hosting model API + prompt injection bypass)

### Custom Scripting

**Purpose**: Automate repetitive tasks, implement novel attacks, integrate disparate tools

**Primary language**: Python (requests, pandas, numpy, scikit-learn, ART/TextAttack integration)

**Use cases**:
- Sending thousands of prompt variations
- Parsing large volumes of model output
- Interacting with proprietary or non-standard APIs
- Chaining multiple attack techniques
- Analyzing response patterns programmatically

**Best practices**: Start simple, build reusable functions, use version control (Git), document thoroughly

## Manual Testing (Primary)

**Why manual-first**: AI exploitation requires contextual understanding, adaptive strategy, and creative attack crafting that automation cannot replicate

**Approach**: Human red teamers interactively probe systems, observe responses, iterate attack strategies based on model behavior

**Tools**:
- HTTP clients (Burp, ZAP, Postman) for API manipulation
- Custom scripts for programmatic testing
- Browser DevTools for client-side analysis

## Automated Testing (Supplementary)

**Role**: Payload generation, regression testing, baseline comparison, efficiency multiplier

**Frameworks**:
- **garak**: Prompt injection and jailbreak payload generation with curated attack libraries
- **PyRIT**: Multi-turn adversarial campaigns, automated risk identification for generative AI
- **ART**: Automated adversarial example generation (FGSM, PGD, C&W) across modalities
- **TextAttack**: Automated NLP attack recipes (textfooler, synonym substitution)
- **Custom harnesses**: Domain-specific test suites, API fuzzing scripts, batch processing workflows

**Automation workflow**:
1. **Initial baseline**: Automated scanning to identify obvious vulnerabilities (known jailbreaks, common injections)
2. **Targeted manual testing**: Human red teamer investigates automated findings, crafts novel attacks
3. **Regression automation**: Convert manual findings into automated tests for CI/CD integration
4. **Continuous monitoring**: Automated checks against new model versions or configuration changes

**Automation limits**: Cannot replace human judgment for interpreting model behavior, crafting context-aware attacks, assessing business impact, or discovering zero-day attack classes

## Reproducibility Harness

All findings include:
- Exact reproduction steps (copy-paste executable)
- Environment details (model, version, config)
- Expected outcomes with success criteria
- Scripts for automatable tests

## Advanced Simulation & Emulation Platforms

**Purpose**: Test AI agents, evaluate defenses against autonomous attackers, assess deception strategies

**Key platforms**:
- **MITRE CALDERA**: Automated adversary emulation based on ATT&CK framework, scriptable attack chains
- **Mirage System**: Hybrid emulation/simulation for testing cyber deception against autonomous attackers
  - **Emulation**: CALDERA + Anansi (reactive host-level deceptions, honefiles)
  - **Simulation**: CyberLayer (cyber operations simulator) + RLlib (distributed RL for training attacking agents)
- **CyberLayer**: High-fidelity network scenario modeling, deception effect evaluation

**Use cases**:
- Evaluating defenses against AI-driven attackers
- Testing automated deception deployment
- Training and benchmarking offensive AI agents in controlled environments
- Researching adversarial RL and adaptive attack strategies
