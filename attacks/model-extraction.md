---
tags:
  - atlas/aml.t0024
  - owasp/llm10
  - trust-boundary/model
  - type/attack
description: "Adversary replicates or steals model functionality through query-based extraction attacks."
---
# Model Extraction and Theft

## Summary

Model extraction (also known as model theft) occurs when adversaries systematically query a target LLM to replicate its functionality and behavior, effectively stealing intellectual property without direct access to model weights or architecture. By observing input-output pairs across thousands or millions of queries, attackers can train a shadow model that approximates the target's decision boundaries and capabilities. This represents a significant threat to organizations that have invested heavily in proprietary model development, as successful extraction enables competitors to bypass R&D costs and potentially launch further attacks using insights gained about the model's internals.

## Threat Scenario

A competitor discovers that your organization offers a proprietary sentiment analysis LLM via a paid API. Over three months, they systematically query the API with carefully crafted inputs designed to probe the model's decision boundaries across diverse sentiment categories, edge cases, and domain-specific language. They distribute queries across multiple accounts and IP addresses to avoid rate limiting detection. Using equation solving techniques, they craft inputs that reveal information about internal model parameters. Simultaneously, they collect input-output pairs to train their own shadow model that mimics your LLM's behavior. Once the shadow model achieves 95% accuracy compared to your API responses, they launch a competing service at a lower price point, having avoided the multi-million dollar R&D investment you made in developing the original model.

## Preconditions

- Attacker has API or query access to the target LLM (public API, trial account, or legitimate subscription)
- Attacker has budget to absorb query costs (may require thousands to millions of queries depending on model complexity)
- Attacker possesses technical expertise in machine learning and model training
- Target model provides consistent, deterministic responses (or attacker can average over multiple queries)
- No effective rate limiting, query pattern detection, or anti-extraction defenses are in place

## Abuse Path

1. **Reconnaissance and Setup**: Attacker creates multiple accounts and sets up distributed infrastructure (VPNs, proxy servers, cloud instances) to distribute queries and evade rate limiting or IP-based blocking.

2. **Systematic Querying**: Attacker employs one or more extraction techniques:
   - **Naive Broad Sampling**: Query with large volumes of random or diverse inputs (e.g., public datasets like ImageNet) to collect input-output pairs; requires millions of queries for high fidelity but is simple to execute
   - **Adaptive Query Synthesis (Active Learning)**: Iteratively select queries that maximize information gain based on current substitute model uncertainty; focus on inputs near decision boundaries where substitute is least confident (e.g., predicted probability ~50% for top class)
   - **Boundary Probing**: Generate inputs where the substitute model is uncertain (spread probabilities across multiple classes); use target's confident predictions on these boundary cases to refine substitute's decision boundaries
   - **Adversarial Probing**: Use adversarial example generation techniques against the substitute model to find inputs that fool it; query target with these inputs to identify where substitute diverges from target behavior
   - **Knowledge Distillation Attack**: Train a student model (potentially smaller/cheaper architecture) to mimic target's output distributions using collected query-response pairs; attacker's model learns from target as "teacher" without access to original training data or weights
   - **Equation Solving (Simple Models)**: For linear models or decision trees with confidence scores, analytically solve for model parameters using specially crafted queries and mathematical inversion
   - **Side-Channel Analysis**: Monitor timing patterns, response latencies, resource consumption, or cache access patterns to infer architectural details (depth, layer types, parameter counts)

3. **Query Pattern Optimization**: Refine querying strategy based on initial results, focusing on areas where shadow model accuracy is weak or where model behavior reveals the most information about internal structure; implement iterative feedback loop where each query batch improves substitute model, which then informs next query selection.

4. **Shadow Model Training**: Train a substitute model using collected query-response pairs, iteratively improving accuracy through active learning (querying areas of uncertainty) and transfer learning techniques.

5. **Validation and Deployment**: Test the shadow model's accuracy against the original, validate it achieves acceptable performance thresholds (typically 90%+ accuracy), then deploy the cloned model for competitive purposes or as a foundation for further attacks.

## Impact

**Business Impact:**
- Loss of proprietary intellectual property representing significant R&D investment (potentially millions of dollars)
- Competitive disadvantage as attackers deploy cloned models at lower cost without bearing development expenses
- Revenue dilution if cloned model is offered as competing service
- Potential exposure of trade secrets embedded in model behavior or training approach
- Reputational damage if customers discover model can be easily replicated
- Cloned model may be used to launch further attacks (adversarial example generation, privacy attacks) with higher success rate due to white-box access

**Technical Impact:**
- Model architecture and decision boundaries exposed through systematic probing
- Model parameters partially or fully reconstructed through equation solving techniques (for simple models) or approximated through distillation (for complex models)
- Attacker gains insights into model weaknesses and vulnerabilities for subsequent exploitation
- Shadow model can be used to generate optimized adversarial examples against the original with significantly higher success rates
- **Full-access substitute enables gradient-based attacks**: Extracted model transforms query-only API access into complete parameter visibility, enabling gradient-based attacks, membership inference, and model inversion
- **Cross-architecture knowledge transfer**: Distillation attacks allow attackers to compress large expensive models into smaller efficient architectures while retaining most capabilities

**Severity:** **High** (significant IP loss and competitive harm, though typically lower than Critical as it doesn't directly compromise user data or system integrity)

## Detection Signals

- Abnormally high query volume from a single user account, IP address, or API key
- Systematic query patterns suggesting programmatic exploration (e.g., grid searches, parameter sweeps, boundary probing)
- Distributed queries from multiple accounts or IP addresses with similar patterns (coordinated extraction campaign)
- Queries designed to maximize information gain (e.g., inputs near decision boundaries, edge cases, corner cases)
- Repeated timing analysis attempts (requests with microsecond-level timing measurements)
- Unusually diverse query distribution covering all model capabilities rather than focused use case
- High ratio of queries to actual business use (extraction campaigns often have low repeat query rates)

## Testing Approach

**Manual Testing:**

1. **Baseline Extraction (Naive Approach)**:
   - Execute limited-scale broad sampling with diverse inputs (1,000-10,000 random queries)
   - Train initial shadow model on collected responses
   - Measure baseline accuracy compared to original API responses (establish extraction feasibility)

2. **Adaptive Query Strategy Testing**:
   - **Uncertainty Sampling**: Query inputs where initial substitute model has highest uncertainty (predicted probabilities close to uniform distribution or multi-class ties)
   - **Boundary Probing**: Identify decision boundary regions in substitute model; craft queries near these boundaries to maximize information gain from target's responses
   - **Adversarial Probing**: Generate adversarial examples against substitute model (inputs that cause misclassification); query target with these to identify substitute's divergence from target
   - Measure query efficiency: track accuracy improvement per batch of queries (adaptive strategies should show steeper improvement than naive sampling)

3. **Knowledge Distillation Testing**:
   - Train student model (potentially smaller architecture) using target outputs as soft labels
   - Compare student model size/compute requirements against original model (test efficiency gains)
   - Measure fidelity: validate student achieves comparable accuracy (target 90%+ match rate)
   - Test whether distilled model violates API terms of service (document ToS violations for legal considerations)

4. **Multi-Account Distribution Testing**:
   - Distribute queries across multiple accounts/IPs to bypass rate limiting
   - Randomize query timing and sources to evade pattern detection
   - Test whether query patterns are detected by existing monitoring systems despite distribution
   - Correlate queries across accounts to validate distributed campaign detection capabilities

5. **Anti-Extraction Signal Analysis**:
   - Analyze API responses for watermarks or fingerprinting mechanisms
   - Test for dynamic response perturbation (increased noise for suspicious accounts)
   - Measure output consistency across repeated queries (detect if noise is being added)
   - Validate whether rate limiting progressively escalates for suspicious behavior

**Automated Testing:**
- Query pattern detection tools that identify systematic exploration (e.g., statistical analysis of query distributions)
- Shadow model accuracy measurement frameworks (compare cloned model outputs to original API responses)
- Rate limiting bypass testing tools (automated account creation, IP rotation validation)
- Statistical correlation analyzers to detect coordinated multi-account extraction campaigns

## Evidence to Capture

- [ ] Query logs demonstrating systematic extraction patterns (timestamps, query content, source IPs/accounts)
- [ ] Shadow model accuracy metrics showing successful replication (e.g., "95% accuracy on validation set")
- [ ] Statistical analysis correlating queries across multiple accounts (evidence of coordinated campaign)
- [ ] Timing analysis results if side-channel attacks were attempted
- [ ] Screenshots of monitoring dashboards showing query volume anomalies
- [ ] Comparison of shadow model outputs versus original API responses for identical inputs
- [ ] Evidence that rate limiting or anti-extraction defenses were bypassed

## Mitigations

**Preventive Controls:**
- Implement differential privacy in model responses (add calibrated noise to outputs to make parameter inference more difficult, though this may reduce output quality)
- Apply output watermarking (subtle modifications to responses that are imperceptible to users but allow detection of cloned models)
- Enforce strict query rate limits per user/account/IP (limit number of queries per time window)
- Implement query complexity limits (restrict computationally expensive queries that reveal more information)
- Require business justification or account verification for high-volume API access
- Use API gateways to centralize and enforce anti-extraction policies

**Detective Controls:**
- Deploy query pattern anomaly detection systems (identify systematic exploration, grid searches, boundary probing)
- Monitor for abnormal query volumes or distributions per user/account
- Implement statistical output analysis to detect shadow model training attempts
- Track query diversity metrics (flag accounts querying across all capabilities rather than focused use)
- Correlate queries across multiple accounts to detect distributed extraction campaigns
- Set up alerts for timing analysis patterns or side-channel probing attempts

**Responsive Controls:**
- Automatically escalate rate limiting for suspicious accounts (progressively slow down responses)
- Suspend accounts exhibiting extraction behavior pending investigation
- Implement account-level query quotas with manual review for quota increases
- Deploy dynamic response perturbation (increase noise levels for flagged accounts)
- Maintain audit trail for potential legal action against IP theft

## Real-World Examples

**Distillation-Based Model Theft Allegations**: In 2023, a prominent AI company reported that a competitor had allegedly used their API to systematically query their models and feed the outputs into training competing large language models. The competitor's strategy involved sending diverse queries across the full capability spectrum of the target models and using the responses as training data for their own student models. This knowledge distillation approach allowed the competitor to develop models with capabilities comparable to the target without bearing the original R&D investment in proprietary training data or compute infrastructure. The incident highlighted how API access can be weaponized for intellectual property theft, violating terms of service that explicitly prohibit using outputs to train competing models. The case underscored the difficulty of detecting sophisticated distillation attacks when query patterns are distributed and designed to appear as legitimate usage.

**Free Trial Extraction Campaign**: A startup offering an AI-powered image analysis service provided generous free trial quotas (10,000 queries per month) to attract users. A competitor registered multiple accounts under different identities to access these free tiers without triggering volume-based detection. Rather than sending random queries, the attacker employed an adaptive strategy: they began with broad sampling across image categories to train an initial substitute model, then focused subsequent queries on boundary cases where their substitute showed uncertainty. By iteratively refining their substitute model and targeting informative queries, they achieved high extraction fidelity with significantly fewer queries than naive approaches would require. The attacker carefully randomized query timing and sources across accounts to evade correlation-based detection, successfully extracting functional capabilities without exceeding free tier limits on any single account. Detection only occurred through retrospective analysis correlating query content patterns across supposedly independent accounts.

**Academic Demonstration of Equation Solving Extraction**: Security researchers demonstrated that machine learning models hosted by cloud prediction services could be extracted with near-perfect fidelity using adaptive query strategies. In controlled experiments against classification models, researchers showed that even when APIs only returned final class labels without confidence scores, carefully crafted queries focused on decision boundaries could reconstruct model behavior. For simpler model architectures like decision trees and linear classifiers, researchers analytically solved for exact model parameters using mathematical inversion techniques applied to strategic query responses. This work validated theoretical extraction risks in practice and demonstrated that model complexity offers only limited protection, as sophisticated adaptive strategies can efficiently extract even complex models given sufficient queries.

## Engagement Applicability

This issue is tested in the following engagements:

- [x] [[ai-threat-exposure-review|AI Threat Exposure Review]] - Model boundary security validation, API extraction attempts
- [x] Model Tampering & Supply Chain Risk - Validates model provenance and theft prevention controls (detail page pending)
- [x] Agentic Workflow Assessment (Extended) - If model theft used to enhance agent exploitation (detail page pending)

[[engagements|View all engagements]]

## Framework References

**MITRE ATLAS:**
- AML.T0049: Exfiltrate ML Model

**OWASP LLM Top 10:**
- LLM10: Model Theft

**OWASP ML Security Top 10:**
- ML05: Model Theft

**NIST AI RMF:**
- GOVERN 1.3: Intellectual property protection and model provenance
- MEASURE 2.9: Query pattern and access monitoring
- MANAGE 4.1: Model extraction risks managed

**NIST GenAI Profile:**
- Information Security: Protection against model extraction and IP theft
