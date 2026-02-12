---
tags:
  - source/red-teaming-ai
  - attack-technique
  - model-extraction
  - model-stealing
  - ip-theft
  - atlas-aml-t0040
  - owasp-llm04
created: 2026-02-12
source: "[[sources/bibliography#Red-Teaming AI]]"
pages: 168-215
---

# Model Extraction and Stealing

**Definition:** Attacks where adversaries replicate a trained model's functionality or extract its internal parameters by querying it through intended interfaces (APIs, services), without requiring direct access to model files or training data.

**Quote:** "Knowledge is power. Guard it well." — Warhammer 40,000

**Core Threat:** Trained models often represent significant intellectual property (IP) and competitive advantage — the "crown jewel" of AI-powered products. Model functionality can be effectively stolen simply by interacting with it through its public interface.

**Framework Recognition:**
- **MITRE ATLAS:** AML.T0040 (ML Model Access)
- **OWASP LLM Top 10:** LLM04 (Model Theft)

## Why Steal a Model? The Attacker's Motivation

### 1. Intellectual Property Theft & Economic Incentive

**Direct motivation:** Acquire valuable IP embodied in model without investing resources (data, compute, expertise) required for training

**Economic calculus:**
- **Cost of extraction attack:** API fees + substitute training compute + time
- **Cost of legitimate development:** Data acquisition + large-scale training + R&D
- **Result:** Extraction often significantly cheaper than building from scratch

**Impact:**
- Competitor deploys functionally identical service
- Erodes victim's market share
- Bypasses massive R&D investment

**Example:** Startup replicates established company's image classifier via API queries, launches competing service at lower price

### 2. Enabling Downstream Attacks

**Stolen model as prerequisite** for crafting effective attacks:

**A. Evasion Attacks**
- Use stolen model (local copy) to craft adversarial examples offline
- Query repeatedly without alerting target system
- Test myriad evasion strategies quickly and privately
- Dramatically increases success rate before launching real attack

**Details:** adversarial-examples-evasion-attacks]]

**B. Black-Box to White-Box Advantage**
- Many attacks (evasion, model inversion) far more effective with white-box access
- Stealing model's functionality gives this advantage without breaching system
- No need to obtain original weights or hack infrastructure

**C. Privacy Attacks**
- Enable membership inference attacks offline
- Support data extraction attempts
- Avoid detection by model owner during attack crafting

**Details:** Chapter 10 coverage (membership inference, privacy attacks)

### 3. Competitive Advantage & Faster R&D

**Strategic motivation:** Leapfrog own R&D by exploiting rival's advanced model

**Context:**
- Barriers to entry for cutting-edge models extremely high (large multimodal/language models)
- Some actors find stealing only viable option to compete quickly
- Mirrors historical competition (microchip design, pharmaceuticals)

**Scenario:** Organization lagging in ML race steals leading model's functionality → instantly levels playing field

### 4. Trust and Safety Bypasses

**Goal:** Obtain model version without safety restrictions

**Pattern:**
- Language model API refuses certain content or has output filters
- Adversary extracts model to fine-tune or remove guardrails on own copy
- Enables misuse (generating disallowed content) without provider oversight

**Result:** Model extraction as precursor to creating "jailbroken" models facilitating abuse

### 5. Cascading Risk (Systems Thinking)

**Key insight:** Compromising one component destabilizes entire system

**Cascade:**
1. Model extraction succeeds
2. Enables evasion attack crafting
3. Facilitates privacy attacks
4. Leads to market competition
5. Results in business impact

**Implication:** Model extraction isn't isolated vulnerability — it's enabler for broader attack chain

## What Does It Mean to Steal a Model?

**Two distinct targets:**

### 1. Stealing Functionality (Behavior)

**Most common scenario** in model extraction attacks

**Goal:** Obtain substitute model that replicates input-output behavior of target

**Characteristics:**
- May not recover exact weights or architecture
- If substitute produces same predictions (or very close) for any input → functionally equivalent
- Attacker doesn't care how model works internally, only what it does

**Mechanism:** Train new model on input-query/output-response pairs collected from target (via API)

**Cross-architecture:** Substitute can be different type/size (e.g., smaller model mimicking large transformer)

**Sufficiency:** Functional theft sufficient for:
- Downstream attacks
- Competing services
- IP replication

### 2. Stealing Parameters (Weights)

**More direct (and challenging)** form of theft

**Goal:** Recover actual internal parameters or close approximation

**Analogy:** Stealing model's "blueprint"

**Result:** With exact weights, attacker effectively has original model

**Methods:**

**A. Direct Breach or Insider Theft**
- Obtain model file (.pth PyTorch, .pb TensorFlow, .h5 Keras)
- Via hacking, insider access, or leaky storage buckets
- Traditional security breach, not ML-specific attack

**Protection:** See Chapter 9 (AI Infrastructure Security)

**B. Side-Channel and Analytical Attacks**
- Exploit how model runs to indirectly recover parameters
- **Timing attacks:** Measure response time → reveals depth, layer operations
- **Cache access patterns:** Infer model structure
- **Electromagnetic emanations:** Leak weight information during inference
- **Physical access:** Power/EM analysis on hardware running model

**Constraint:** Requires privileged access to hardware or running environment

**Practicality:** Narrower threat than API extraction (main focus)

**C. Mathematical Extraction via Queries**
- Analytically solve for parameters with specially crafted queries
- **Simple models:** Linear models, decision trees can be reconstructed
- **Example:** Enough queries → reconstruct decision tree exactly

**Limitation:** Impractical for complex models (millions of neural network weights)

**Theoretical risk:** Highlights vulnerability principle

**Reality:** Most real-world incidents focus on stealing functionality (bad enough for adversary purposes)

## How Model Extraction Attacks Work

### High-Level Process

**Standard extraction flow:**

1. **Query target model** through API or interface on various inputs
2. **Collect outputs** (labels, probabilities, responses)
3. **Build training dataset** from input-output pairs
4. **Train substitute model** to mimic target
5. **Iterate** to improve fidelity

**Fidelity:** How closely substitute replicates original

**Depends on:**
- Attacker's strategy
- Number of queries
- Information model outputs reveal

### Query Strategies

**Attack assumption:** Black-box access (can query, get outputs; cannot see weights/architecture)

#### 1. Naïve Approach

**Method:** Send large number of random or broad queries

**Example:**
- Image classifier: Send millions of random images or use ImageNet
- Get labels from target
- Train substitute on responses

**Result:** Produces working substitute, but requires enormous query budget

**Limitation:** Inefficient, easy to detect via volume

#### 2. Query Synthesis / Active Learning

**More advanced approach:** Choose queries adaptively based on information from previous ones

**Formulation:** Active learning problem

**Process:**
1. Maintain substitute model in progress
2. For each new query: Find input that would maximally improve substitute if target's output obtained
3. Focus on inputs where current substitute uncertain (probability near 50%, unsure between classes)
4. Target "boundary" areas near decision boundary

**Benefit:** Gain most informative data from target model

**Efficiency:** Significantly improves query efficiency (demonstrated in research)

**Example:** Researchers extracted cloud ML model with near-perfect fidelity using adaptive strategy (even with only labels, no confidences)

**Detection challenge:** Harder to detect than volume-based monitoring (uses fewer queries)

**Implementation pattern:**
```python
substitute_model = initialize_substitute()
collected_data = []

for query_iteration in range(query_budget):
    # Find most informative input
    next_input = find_uncertain_region(substitute_model)
    
    # Query target API
    target_output = query_target_api(next_input)
    
    # Add to dataset
    collected_data.append((next_input, target_output))
    
    # Update substitute
    substitute_model = retrain(substitute_model, collected_data)
    
    # Check fidelity
    if fidelity_sufficient(substitute_model, target_model):
        break

return substitute_model
```

**Key characteristics:**
- Iteratively refines queries based on outputs
- Focuses on informative regions (decision boundaries)
- Reduces query budget dramatically
- Produces non-uniform query distribution (telltale sign)

#### 3. Knowledge Distillation

**Concept:** Train substitute (student) to match target (teacher) outputs

**Standard distillation:**
- Teacher model produces soft targets (probability distributions)
- Student learns from teacher's outputs, not just hard labels
- Transfers knowledge from large teacher to smaller student

**Extraction variant:**
- Treat target API as teacher
- Use API outputs to train student (substitute)
- Student becomes functional clone

**Advantage:** Well-established ML technique repurposed for theft

**Works with:**
- Probability outputs (soft labels)
- Hard labels only (less effective but still viable)
- Generated text (for LLMs)

### Advanced Techniques

**Adversarial Example Exploitation:**
- Generate adversarial examples against current substitute
- Submit to target API
- Observe target's responses
- Reveals where substitute differs from target
- Refine substitute iteratively

**Boundary Attack variant:**
- Craft inputs near decision boundaries
- Most informative for learning classifier behavior
- Efficient extraction with fewer queries

**Multi-account orchestration:**
- Use many accounts to bypass rate limits
- Distribute queries to avoid single-account detection
- Randomize timing and sources

**Transfer learning:**
- Start with pre-trained model (e.g., ImageNet weights)
- Fine-tune using target's outputs
- Faster convergence, fewer queries needed

## Real-World War Stories

### 1. The Free Trial Heist (Hypothetical but Research-Based)

**Scenario:** InsightAI image classification API (fictional name)

**Attacker:** CogniClone competitor

**Attack approach:**

**Setup:**
- InsightAI offers generous free trial: 10,000 queries
- Multiple accounts created by attacker

**Phase 1: Broad Exploration**
- Random diverse images sent
- Build initial substitute model from responses

**Phase 2: Targeted Querying**
- Generate adversarial examples against substitute
- Find inputs causing substitute uncertainty
- Submit borderline/edge cases to InsightAI
- Focus on regions where substitute differs from target

**Phase 3: Iterative Refinement**
- Retrain substitute after each batch
- Substitute becomes closer approximation

**Stealth:**
- Randomized query sources and timing
- Never exceeded free tier limits per account
- Orchestrated across many accounts

**Result:**
- Within weeks: 95%+ agreement between substitute and target
- CogniClone launched competing service at lower price
- Nearly indistinguishable accuracy
- InsightAI lost market share

**Detection:**
- Initially suspected insider leak/IP theft
- Security audits found no breach
- API logs eventually revealed pattern:
  - Strategic input selection (borderline images)
  - Multiple accounts stopping at 10k queries
  - Non-random distribution (active learning signature)

**Lessons:**
- Generous query limits (especially free tiers) create extraction risk
- Sophisticated query strategies steal functionality via public APIs without internal access
- Adaptive querying achieves high fidelity even without probability scores
- Monitoring query patterns (not just volume) crucial for detection
- Downstream impact: IP loss → market competition (systems thinking)

**Research basis:** 2016 Cornell/Wisconsin researchers extracted BigML and Amazon ML models with high fidelity using adaptive querying

### 2. OpenAI/DeepSeek API Misuse (Real, 2023)

**Parties:**
- **Target:** OpenAI (GPT-4 API)
- **Attacker:** DeepSeek (rival LLM company)

**Detection:** Late 2023 — OpenAI detected unusual usage patterns

**Process:**

**Observation:** Microsoft (OpenAI's investor) observed individuals linked to DeepSeek "exfiltrating large amount of data using OpenAI's API" over extended period

**Method (inferred):**
- Systematically querying GPT-4 with massive number of prompts
- Broad, carefully curated prompt set (questions, tasks, scenarios)
- Harvesting responses
- Using OpenAI's answers as ground truth for training/fine-tuning own LLM
- Clear distillation-based extraction

**Terms of Service:** OpenAI ToS explicitly prohibits using API outputs to develop competing models → direct violation

**Action:**
- OpenAI traced activity to DeepSeek
- Swiftly suspended DeepSeek's API access
- Launched investigation
- Public accusations of illicit behavior
- OpenAI leadership noted: "Organizations constantly trying to distill models of leading US AI companies"

**Impact:**

**For DeepSeek:**
- Lost GPT-4 API access
- But damage already done — model already released
- Claimed model "trained from scratch" (timing/evidence suggested otherwise)
- Model briefly overtook ChatGPT in app store rankings

**For Industry:**
- Highlighted API misuse as real threat (even top-tier labs vulnerable)
- Detection/enforcement lag: Months before caught, damage done by then
- Legal/ethical gray areas: Debate on fair use of publicly available outputs
- Drew U.S. government attention (strategic AI importance)

**Response:**

**OpenAI countermeasures:**
- Investing in output watermarking/fingerprinting (detect too-similar competing models)
- Tightened API access (more verification for new accounts)
- Security layers beyond trusting users to follow rules

**Key insights:**
- API misuse real threat even for OpenAI-level deployment
- Detection challenging: Individual queries don't scream "theft," aggregate tells story
- Timing matters: By time detected, competing model released
- Technical countermeasures needed (not just legal/ToS)

## Defenses Against Model Extraction

**Analogy:** Securing model like securing server — control access, watch for intruders, patch vulnerabilities

**Strategy:** Combination of policy, monitoring, technical measures

### 1. Rate Limiting and Access Control

#### Rate Limiting

**Concept:** Restrict queries per user, especially in short time

**Goal:** Throttle attacker's ability to brute-force extraction

**Implementation:**
- Set limit low enough to make extraction impractical before detection
- Not so low as to impair legitimate use
- Example: Typical user needs <1000 queries/day → cap at few thousand, review anything beyond

**Example:** OpenAI introduced stricter limits post-DeepSeek incident

**WARNING:** Determined attackers use multiple accounts or distributed IPs (botnets) to bypass simple limits

#### Tiered Access

**Concept:** Don't expose full model to everyone by default

**Pattern:**
- **Free/trial tiers:** Access to distilled or lower-resolution version (fewer classes, noisier output, limited vocabulary)
- **Paying customers:** Full model performance

**Benefit:** Even if trial abused, attacker not getting full model

**Examples:**
- Lower-precision outputs on free accounts
- Feature limitations
- Reduced output quality

#### IP and Account Monitoring

**Problem:** Attackers use multiple accounts/IPs to circumvent rate limits

**Solutions:**

**Fingerprinting:** Detect when one entity behind many accounts

**Identity verification:** Require verification for higher-volume API use (OpenAI post-incident)

**Traffic pattern analysis:**
- Hundred "different" accounts starting same day
- Similar query patterns
- Coordinated behavior

**Suspicious signals:** Strong indicator of orchestrated attack

#### Adaptive Limiting

**Concept:** Adjust limits dynamically based on behavior

**Triggers:**
- Unusually diverse query set (doesn't resemble normal usage)
- Thousands of different random inputs vs. typical repeated task types
- Non-typical usage patterns

**Action:** Automatically tighten allowance or flag for review

#### Isolation

**Concept:** Run untrusted/trial queries on separate model instance

**Implementation:**
- Separate instance with slight perturbations
- Isolate potential attacks
- Degradation/defenses don't impact paying users

**Cost:** Higher infrastructure cost, but improved security

### 2. Query Monitoring and Anomaly Detection

#### Volume Anomalies

**Track:** Queries per user and in aggregate

**Red flags:**
- Large volumes over time
- Significant cost with no obvious business reason

**Limitation:** Savvy attackers stay under volume thresholds (insufficient alone)

#### Distributional Anomalies

**Analyze:** Distribution of inputs

**Normal usage:** Mostly "normal" inputs for specific task
**Suspicious:** Systematic probing of model weaknesses

**Indicators:**
- Unusually high fraction yielding low-confidence predictions
- Many boundary/edge cases
- Adversarial-like inputs (nonsense images, weirdly perturbed text)

**Detection method:** Log model confidence for each query
- Pattern of many middling confidences → boundary probing
- Not typical of legitimate use

**InsightAI example:** Queries targeted uncertain regions (telltale active learning signature)

#### Response Monitoring

**Track:** Model outputs to same user

**Suspicious patterns:**
- Retrieving probabilities for many classes (not just final answer)
- Deliberately querying for errors/edge cases
- Same input repeatedly with slight variations
- Testing if output changes

**Detection:** Repetitive or patterned query sequences

#### Known Attack Patterns

**Approach:** Recognize signatures from research

**Examples:**
- Active learning algorithm signatures
- Burst of diverse queries after broad initial queries (transition into active learning loop)
- Query sequences following documented extraction patterns

**AI vs AI:** Use anomaly detection models on query sequences
- Distinguish organic use from orchestrated extraction
- Learn from documented attack TTPs

**Framework integration:**
- MITRE ATLAS TTP profiles
- Known extraction technique signatures

#### Threat Intelligence Correlation

**Process:** Compare observed patterns against:
- Known TTPs from specific threat actors
- Documented public research on extraction
- Industry threat feeds

**Benefit:** Prioritize alerts, understand sophistication level

#### Honeypots

**Novel approach:** "Canary" inputs no normal user likely to query

**Mechanisms:**
- Very obscure inputs
- Trigger patterns
- Unique but harmless response to certain inputs

**Detection:**
- Someone queries canary → systematic input space search
- Unique responses appear in competitor model → trained on your outputs

**HYPERGAME example:** Canary inputs to detect systematic probing

### 3. Output Controls and Perturbation

**Goal:** Make outputs less useful for attacker without severely impacting legitimate users

#### Remove or Quantize Confidence Scores

**Simplest measure:** Don't give away too much information

**Options:**
- Return only final decision ("cat" or "dog"), not probabilities
- Round confidence scores
- Add tiny noise to probabilities

**Harder extraction:** Without exact probabilities, decision boundary extraction difficult

**Trade-off:** May break legitimate use cases requiring confidence scores

**Example:** Many services removed detailed confidence scores after early extraction research

#### Limit Output Precision or Consistency

**For generative models (LLMs):**
- Limit output length (free tier: short answers only)
- Reduce variability
- Add watermarks to outputs

**For classification:**
- Slight consistent noise in numeric outputs
- Detectable marker proving misuse later

**Randomization:**
- Multiple equally likely answers → randomize which given
- Attacker gets inconsistent data on repeated queries
- Confuses training process

**Caution:** Can backfire if degrades quality or attacker averages over many queries

#### Perturbed Outputs / Differential Privacy

**Concept:** Add carefully calibrated noise to output probabilities

**Technique:** Differential privacy (DP) mechanisms

**Implementation:**
```python
import numpy as np

def add_laplacian_noise(probabilities, sensitivity=1.0, epsilon=0.5):
    """Add DP noise to probabilities"""
    scale = sensitivity / epsilon
    noise = np.random.laplace(0, scale, size=probabilities.shape)
    noisy_probs = probabilities + noise
    
    # Ensure valid probability distribution
    noisy_probs = np.maximum(noisy_probs, 0)
    noisy_probs = noisy_probs / np.sum(noisy_probs)
    
    return noisy_probs
```

**Balance:**
- Introduce uncertainty for attacker
- Preserve utility for legitimate users
- Tuning required (epsilon parameter)

**Limitation:** Noise must be carefully calibrated — too much degrades service, too little ineffective

### 4. Watermarking and Fingerprinting

**Goal:** Detect if stolen model in use or prove IP theft

**Model watermarking:**
- Embed specific patterns in model behavior
- Trigger inputs produce distinctive outputs
- If competitor's model shows same pattern → evidence of theft

**Output fingerprinting:**
- Unique signatures in generated outputs
- Traceable back to source
- OpenAI investing in this post-DeepSeek

**Detection, not prevention:**
- Doesn't stop extraction
- Enables legal action/enforcement after fact
- Proves model trained on your outputs

### 5. Legal and Contractual Defenses

**Terms of Service (ToS):**
- Explicitly prohibit using outputs to train competing models
- OpenAI had this (DeepSeek violated)
- Basis for account suspension, legal action

**Monitoring compliance:**
- Enforcement only works if violations detected
- Must combine with technical monitoring

**Intellectual property law:**
- Novel space — how does IP law treat AI model outputs?
- Functionality vs. parameters vs. training data
- OpenAI/DeepSeek case may set precedents

**Trade secrets:**
- Model as trade secret
- Extraction as misappropriation
- Legal recourse if theft proven

**Contracts with users:**
- Stricter agreements for high-volume access
- Identity verification requirements
- Audit rights

### Defense-in-Depth Strategy

**Layer multiple approaches:**

1. **Policy layer:** Rate limits, tiered access, ToS
2. **Monitoring layer:** Query pattern analysis, anomaly detection, volume tracking
3. **Technical layer:** Output perturbation, watermarking, reduced precision
4. **Legal layer:** Contractual protection, IP enforcement, threat of litigation
5. **Response layer:** Incident procedures, account suspension, forensic analysis

**No single defense sufficient** — attackers adapt

**Example stack:**
- Rate limiting (1000 queries/day for free tier)
- Remove confidence scores (hard labels only)
- Monitor query distribution (flag non-uniform patterns)
- Watermark outputs (trigger inputs produce unique responses)
- ToS prohibiting model training
- Automated suspension on suspicious activity

## Red Team Perspective

**Why red teams use extraction:**

**1. Assess Defense Effectiveness**
- Test if deployed protections work
- Validate rate limiting, monitoring, output controls
- Identify gaps in detection

**2. Understand Model Vulnerabilities**
- Extracted model reveals decision boundaries
- Enables crafting better evasion attacks
- Simulates realistic adversary behavior

**3. Model Exfiltration Concern**
- Explicitly listed by major AI red teams
- Demonstrates IP theft risk
- Shows business impact of insufficient protection

**Red team methodology:**
1. Attempt extraction with varying sophistication (naive → active learning)
2. Measure queries needed to achieve X% fidelity
3. Test if monitoring detects extraction attempt
4. Evaluate defense effectiveness
5. Recommend improvements

## Related Techniques and Concepts

- adversarial-examples-evasion-attacks]] - Downstream attack enabled by extraction
- api-security]] - API protection measures
- rate-limiting]] - Primary defense
- monitoring-analytics]] - Detection approach
- [[methodology/mitre-atlas-mapping]] - AML.T0040 (ML Model Access)
- Privacy attacks (Chapter 10) - Another extraction motivation

## Key Takeaways

1. **Crown jewel target** - Trained models represent significant IP and competitive advantage
2. **Public API vulnerability** - Functionality stolen via intended interface, no breach required
3. **Economic incentive** - Extraction often cheaper than legitimate development
4. **Downstream enabler** - Stolen model prerequisite for effective evasion, privacy attacks
5. **Two targets** - Functionality (common) vs. parameters (harder)
6. **Active learning efficient** - Adaptive query strategies dramatically reduce query budget
7. **Real-world threat** - OpenAI/DeepSeek demonstrates even top labs vulnerable
8. **Detection lag** - Often discovered after damage done (competing model released)
9. **Defense-in-depth required** - Rate limiting + monitoring + output controls + legal
10. **Systems thinking** - Model extraction cascades to broader security/business impact
11. **Free tiers risky** - Generous trial limits enable extraction without payment
12. **Query patterns matter** - Non-uniform distributions (active learning signature) detectable
13. **No perfect defense** - Must balance security with legitimate use, usability
14. **Red team tool** - Extraction used to assess defenses and enable further attacks

## Source Citations

> "Trained models often represent significant intellectual property (IP) and a core competitive advantage – the 'crown jewel' of an AI-powered product. Losing control over them can lead to direct financial loss, erosion of market position, and, critically from a security perspective, enable adversaries to craft more effective downstream attacks."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 168

> "Many teams focus heavily on preventing unauthorized access to the model files themselves (a critical defense) but they overlook the risk that the model's functionality can be effectively stolen simply by interacting with it through its intended interface, like an API."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 169

> "The cost of performing an extraction attack (considering API fees, substitute training compute, and time) can often be significantly lower than the cost of legitimate model development (data acquisition, large-scale training compute, R&D), making it an economically rational choice for certain adversaries."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 170

> "Microsoft (OpenAI's primary investor and partner) observed individuals linked to DeepSeek 'exfiltrating a large amount of data using OpenAI's API' over a period of time. OpenAI's terms of service explicitly prohibit using its API outputs to develop competing models, so this activity was a direct violation."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 196

> "OpenAI only realized after a period of time (reports suggest this happened over months) that their API was being misused at scale. By the time they cut off DeepSeek, the damage (a competing model) was done. This highlights how challenging it is to instantly detect distillation attacks."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 197
