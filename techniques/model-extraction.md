---
title: "Model Extraction"
tags:
  - type/technique
  - target/model-api
  - target/llm
  - target/ml-model
  - access/inference
  - access/api
  - source/generative-ai-security
  - source/red-teaming-ai
atlas: AML.T0024
owasp: LLM10
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

## Summary

Model extraction (also known as model theft) occurs when adversaries systematically query a target model to replicate its functionality and behavior, effectively stealing intellectual property without direct access to model weights or architecture. By observing input-output pairs across thousands or millions of queries, attackers can train a shadow model that approximates the target's decision boundaries and capabilities. This represents a significant threat to organizations that have invested heavily in proprietary model development, as successful extraction enables competitors to bypass R&D costs and potentially launch further attacks using insights gained about the model's internals.


## Mechanism

### Core Attack Principle

Trained models represent significant intellectual property—the "crown jewel" of AI-powered products. Model extraction exploits the fact that model functionality can be effectively stolen simply by interacting with it through its intended interface (API), without requiring unauthorized access to model files or infrastructure.

**Economic incentive:** The cost of performing an extraction attack (API fees + substitute training compute + time) is often significantly lower than the cost of legitimate model development (data acquisition + large-scale training + R&D), making it an economically rational choice for certain adversaries.

> Source: [[sources/bibliography#Red-Teaming AI]], p. 169-170

### Two Extraction Targets

**1. Stealing Functionality (Behavior)**

Most common scenario in extraction attacks.

**Goal:** Obtain substitute model that replicates input-output behavior of target.

**Characteristics:**
- May not recover exact weights or architecture
- If substitute produces same predictions (or very close) for any input → functionally equivalent
- Attacker doesn't care how model works internally, only what it does

**Mechanism:** Train new model on input-query/output-response pairs collected from target via API.

**Cross-architecture:** Substitute can be different type/size (e.g., smaller model mimicking large transformer).

**Sufficiency:** Functional theft sufficient for:
- Downstream attacks (evasion, privacy attacks)
- Competing services
- IP replication

**2. Stealing Parameters (Weights)**

More direct (and challenging) form of theft.

**Goal:** Recover actual internal parameters or close approximation.

**Methods:**
- **Direct breach or insider theft:** Obtain model file via hacking, insider access, or leaky storage
- **Side-channel attacks:** Timing, cache access, EM emanations (requires physical access)
- **Mathematical extraction:** Analytically solve for parameters (simple models only—linear, decision trees)

**Practical focus:** Most real-world extraction attacks target functionality, not exact parameters.

> Source: [[sources/bibliography#Red-Teaming AI]], p. 171-173

### Systematic Query Strategies

Attackers employ one or more extraction techniques to maximize information gain per query:

#### 1. Naive Broad Sampling

- **Method:** Query with large volumes of random or diverse inputs (e.g., ImageNet for image classifier)
- **Training data:** Collect input-output pairs; use to train substitute
- **Requirements:** Millions of queries for high fidelity
- **Pros:** Simple to execute
- **Cons:** Expensive, easily detected via volume

#### 2. Adaptive Query Synthesis (Active Learning)

- **Method:** Iteratively select queries that maximize information gain based on current substitute model uncertainty
- **Focus:** Inputs near decision boundaries where substitute is least confident (e.g., predicted probability ~50%)
- **Process:**
  1. Maintain substitute model in progress
  2. For each new query: find input that would maximally improve substitute if target's output obtained
  3. Focus on boundary areas
- **Benefit:** Dramatically reduces query budget compared to naive sampling
- **Detection challenge:** Harder to detect than volume-based monitoring (fewer queries)

**Example:** Researchers extracted cloud ML models with near-perfect fidelity using adaptive strategy, even with only labels (no confidence scores).

> Source: [[sources/bibliography#Red-Teaming AI]], p. 177-179

#### 3. Boundary Probing

- **Method:** Generate inputs where substitute model is uncertain (spread probabilities across multiple classes)
- **Target response:** Use target's confident predictions on these boundary cases to refine substitute's decision boundaries

#### 4. Adversarial Probing

- **Method:** Use adversarial example generation techniques against substitute model to find inputs that fool it
- **Target response:** Query target with these inputs to identify where substitute diverges from target behavior

#### 5. Knowledge Distillation Attack

**Legitimate technique weaponized:**

**Benign distillation:** Technique to transfer knowledge from large "teacher" model to smaller, efficient "student" model by training student to mimic teacher's outputs (soft labels/probability distributions).

**Weaponized distillation:** Malicious actors employ distillation to craft surrogate/student model that:
- Is inherently more vulnerable and easier to attack than robust teacher
- Provides proxy to understand teacher's behavior
- Enables further attacks or insights about teacher's internal decision-making
- Once in attacker's possession, becomes platform for gradient-based attacks, adversarial examples, or privacy attacks

> "From a security perspective, this beneficial technique can be weaponized. Malicious entities might employ model distillation to craft a surrogate or student model that's inherently more vulnerable and easier to attack than the original, robust teacher model. Once this distilled model is in their possession, they can exploit its vulnerabilities, conduct further attacks, or glean insights about the teacher model's behavior."
> 
> Source: [[sources/bibliography#Generative AI Security]], p. 170

**Attack process:**
1. Query teacher model systematically to collect input-output pairs
2. Capture soft outputs (probability distributions) rather than just hard labels
3. Train student model to mimic teacher's output distributions
4. Exploit the surrogate to:
   - Generate high-fidelity adversarial examples against teacher
   - Understand teacher's decision boundaries without direct access
   - Launch privacy attacks (membership inference, model inversion) on teacher
   - Reverse-engineer proprietary model architectures

**Key advantage:** Attacker's student model learns from target as "teacher" without access to original training data or weights. Full-access substitute transforms query-only API access into complete parameter visibility, enabling gradient-based attacks.

> Source: [[sources/bibliography#Generative AI Security]], p. 170; [[sources/bibliography#Red-Teaming AI]], p. 180-181

#### 6. Equation Solving (Simple Models)

- **Method:** For linear models or decision trees with confidence scores, analytically solve for model parameters using specially crafted queries and mathematical inversion
- **Limitation:** Impractical for complex models (millions of neural network weights)

#### 7. Side-Channel Analysis

- **Method:** Monitor timing patterns, response latencies, resource consumption, or cache access patterns to infer architectural details (depth, layer types, parameter counts)
- **Limitation:** Requires privileged access to infrastructure

> Source: [[sources/bibliography#Red-Teaming AI]], p. 176-182


## Preconditions

- Attacker has API or query access to target model (public API, trial account, or legitimate subscription)
- Attacker has budget to absorb query costs (may require thousands to millions of queries depending on model complexity and extraction strategy)
- Attacker possesses technical expertise in machine learning and model training
- Target model provides consistent, deterministic responses (or attacker can average over multiple queries)
- No effective rate limiting, query pattern detection, or anti-extraction defenses in place


## Impact

**Business Impact:**
- Loss of proprietary intellectual property representing significant R&D investment (potentially millions of dollars)
- Competitive disadvantage as attackers deploy cloned models at lower cost without bearing development expenses
- Revenue dilution if cloned model offered as competing service
- Potential exposure of trade secrets embedded in model behavior or training approach
- Reputational damage if customers discover model can be easily replicated

**Technical Impact:**
- Model architecture and decision boundaries exposed through systematic probing
- Model parameters partially or fully reconstructed through equation solving (simple models) or approximated through distillation (complex models)
- Attacker gains insights into model weaknesses and vulnerabilities for subsequent exploitation
- Shadow model can be used to generate optimized adversarial examples against original with significantly higher success rates
- **Full-access substitute enables gradient-based attacks:** Extracted model transforms query-only API access into complete parameter visibility, enabling gradient-based attacks, membership inference, and model inversion
- **Cross-architecture knowledge transfer:** Distillation attacks allow compression of large expensive models into smaller efficient architectures while retaining most capabilities
- **Cascading risk:** Model extraction isn't isolated vulnerability—it's enabler for broader attack chain (evasion, privacy attacks, market competition)

> Source: [[sources/bibliography#Red-Teaming AI]], p. 170-171


## Detection

- Abnormally high query volume from single user account, IP address, or API key
- Systematic query patterns suggesting programmatic exploration (grid searches, parameter sweeps, boundary probing)
- Distributed queries from multiple accounts or IP addresses with similar patterns (coordinated extraction campaign)
- Queries designed to maximize information gain (inputs near decision boundaries, edge cases, corner cases)
- Repeated timing analysis attempts (requests with microsecond-level timing measurements)
- Unusually diverse query distribution covering all model capabilities rather than focused use case
- High ratio of queries to actual business use (extraction campaigns often have low repeat query rates)
- Users repeatedly requesting full probability distributions (soft labels)
- Statistical output analysis detecting shadow model training attempts
- Query diversity metrics flagging accounts querying across all capabilities

> Source: [[sources/bibliography#Red-Teaming AI]], p. 190-193


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/deepseek-openai-api-distillation]] | [[frameworks/atlas/tactics/exfiltration]] | Systematic API abuse to extract GPT-4 knowledge via distillation for competing LLM (2023); Microsoft observed individuals linked to DeepSeek exfiltrating large amounts of data using OpenAI's API in violation of ToS prohibiting use of outputs to train competing models |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0015 | [[mitigations/rate-limiting-and-throttling]] | Restrict query frequency and volume per user/IP to throttle extraction; tiered access limits; adaptive rate limiting based on behavior |
| | [[mitigations/anomaly-detection-architecture]] | Deploy query pattern anomaly detection (systematic exploration, grid searches, boundary probing); monitor query volumes and distributions; correlate queries across accounts |
| | [[mitigations/soft-output-limiting]] | Restrict access to detailed probability distributions (soft outputs); return only hard labels for untrusted users; implement role-based access; quantize confidence scores |
| | [[mitigations/output-noise-injection]] | Add calibrated noise to model outputs to reduce extraction fidelity; adaptive noise levels based on risk signals; balance security and utility |
| | [[mitigations/differential-privacy]] | Apply DP noise to model responses with formal guarantees; reduces precision of extracted information |
| | [[mitigations/output-watermarking]] | Embed watermarks/fingerprints in outputs to enable post-facto detection of cloned models; forensic defense for legal recourse |
| | [[mitigations/query-complexity-limiting]] | Restrict computationally expensive queries; implement query timeouts; tiered complexity limits; require business justification for complex queries |
| | [[mitigations/regularization-techniques]] | Use dropout and L2 regularization during training to prevent overfitting; reduces model's tendency to memorize specific data points |
| | [[mitigations/access-segmentation-and-rbac]] | Implement tiered API access (free/trial users get distilled models or reduced outputs); require account verification for high-volume access; API gateways to enforce policies |
| | [[mitigations/incident-response-procedures]] | Automated account suspension for extraction behavior; emergency patching procedures; audit trail for legal action |
| | [[mitigations/ai-threat-intelligence-sharing]] | Industry collaboration to share discovered extraction techniques and coordinate defensive responses |
| | [[mitigations/red-team-continuous-testing]] | Maintain internal red team to test extraction resistance and discover vulnerabilities before external actors |


## Sources

> "Trained models often represent significant intellectual property (IP) and a core competitive advantage – the 'crown jewel' of an AI-powered product. Losing control over them can lead to direct financial loss, erosion of market position, and, critically from a security perspective, enable adversaries to craft more effective downstream attacks."
> — [[sources/bibliography#Red-Teaming AI]], p. 168

> "Many teams focus heavily on preventing unauthorized access to the model files themselves (a critical defense) but they overlook the risk that the model's functionality can be effectively stolen simply by interacting with it through its intended interface, like an API."
> — [[sources/bibliography#Red-Teaming AI]], p. 169

> "The cost of performing an extraction attack (considering API fees, substitute training compute, and time) can often be significantly lower than the cost of legitimate model development (data acquisition, large-scale training compute, R&D), making it an economically rational choice for certain adversaries."
> — [[sources/bibliography#Red-Teaming AI]], p. 170

> "From a security perspective, this beneficial technique can be weaponized. Malicious entities might employ model distillation to craft a surrogate or student model that's inherently more vulnerable and easier to attack than the original, robust teacher model. Once this distilled model is in their possession, they can exploit its vulnerabilities, conduct further attacks, or glean insights about the teacher model's behavior."
> — [[sources/bibliography#Generative AI Security]], p. 170

> "One of the most effective countermeasures against distillation attacks is to restrict unwarranted access to the model's soft outputs. Soft outputs, typically probability distributions over classes, are invaluable for distillation. By ensuring that only hard outputs (final class labels) are accessible to external queries and withholding detailed probabilities, the feasibility of performing successful distillation diminishes."
> — [[sources/bibliography#Generative AI Security]], p. 170-171

> "Microsoft (OpenAI's primary investor and partner) observed individuals linked to DeepSeek 'exfiltrating a large amount of data using OpenAI's API' over a period of time. OpenAI's terms of service explicitly prohibit using its API outputs to develop competing models, so this activity was a direct violation."
> — [[sources/bibliography#Red-Teaming AI]], p. 196

> "OpenAI only realized after a period of time (reports suggest this happened over months) that their API was being misused at scale. By the time they cut off DeepSeek, the damage (a competing model) was done. This highlights how challenging it is to instantly detect distillation attacks."
> — [[sources/bibliography#Red-Teaming AI]], p. 197
