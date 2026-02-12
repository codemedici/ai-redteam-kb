---
description: "Corrupting training data or knowledge bases to manipulate model behavior"
tags:
  - atlas/aml.t0020
  - data-poisoning
  - owasp/llm03
  - source/red-teaming-ai
  - supply-chain
  - training-security
  - trust-boundary/data-knowledge
  - trust-boundary/deployment-governance
  - trust-boundary/model
  - type/attack
  - target/ml-pipeline
  - target/rag-system
  - access/gray-box
  - access/white-box
  - severity/critical
created: 2026-02-12
source: [['sources/bibliography#Red-Teaming AI']]
pages: "99-138"
---
# Data Poisoning Attacks

**Definition:** Deliberately corrupting training data to manipulate AI model behavior, causing models to be sabotaged (availability), subtly compromised (integrity), or implanted with hidden vulnerabilities (backdoors).

**Core Principle:** "Garbage in, garbage out" — AI systems become reflections of their training data. Compromising the data foundation compromises the entire system.

**Impact:** Poisoned models can be:
- **Manipulated:** Compromising product features and functionality
- **Sabotaged:** Causing deployment failures and performance degradation
- **Backdoored:** Functioning normally until specific triggers activate malicious behavior

**Trust Boundary Note:** Data poisoning attacks target the data pipeline (data-knowledge trust boundary) but ultimately compromise model weights (model trust boundary). This note focuses on the data pipeline attack vector. For model-centric view of training-time attacks, see related notes in trust-boundaries/model/.

## The Critical Role of Data Integrity

**Data Integrity:** Accuracy, consistency, and trustworthiness of data throughout its lifecycle, ensuring it's free from unauthorized modification or corruption

**Data Availability:** Property that data is accessible and usable upon demand by authorized entities

**Why poisoning works:** ML models learn patterns directly from training data — data quality and integrity are vital to model behavior

### Red Team Perspective: Why Target Data?

**Strategic advantages over attacking deployed models:**

1. **Stealth**
   - Changes to training data subtle and hard to detect
   - Effects may only surface under specific conditions later
   - Poisoning happens before model deployment, reducing attack visibility

2. **Persistence**
   - Poisoned model retains malicious behavior unless retrained on clean data
   - Specific patches difficult (especially for subtle integrity attacks)
   - Compromise baked into model weights

3. **Scalability**
   - Single poisoning effort affects ALL model instances trained on that data
   - Potentially impacts thousands/millions of users or decisions
   - Multiplier effect across deployments

4. **Leverage**
   - Exploits fundamental trust in data foundation of ML development
   - Targets earlier in pipeline → broader downstream impact
   - Attacks the most trusted part of the system

### Key Red Team Questions

**Data pipeline reconnaissance:**
- Where does the target system get its data?
- How is it labeled?
- How is it processed?
- Where are the least controlled data ingest points?
- Where are the weakest validation checks in the pipeline?

**Systems thinking approach:** Data pipeline is complex system with multiple attack entry points. Attackers think in graphs — data dependency graph offers numerous edges to target.

## Types of Data Poisoning Attacks

### 1. Availability Poisoning

**Goal:** Degrade model's overall performance, making it unreliable or unusable (digital sabotage)

**Mechanism:**
- Introduce noisy, irrelevant, or nonsensical data into training set
- Force model to learn incorrect patterns
- Cause convergence difficulties during training

**Impact:**
- Model performs poorly on all/most inputs
- Fails basic designed tasks
- Example: Spam filter classifies almost all emails as spam OR misses obvious spam

**Analogy:** Teaching someone a language by mixing random gibberish into lessons — they struggle to learn coherent communication

**Detection:** Usually easier to spot because poor performance obvious during testing/validation

**Use case:** Denial-of-Service (DoS) for AI-powered features, operational disruption

### 2. Integrity Poisoning (Including Backdoors)

**Goal:** Subtly corrupt behavior in specific, attacker-chosen ways while maintaining normal appearance

**Characteristics:**
- More stealthy than availability poisoning
- More strategically valuable
- Model appears functional most of the time
- Contains hidden vulnerabilities or biases

**Mechanism:**
- Inject carefully crafted malicious samples into training data
- Teach model incorrect association or hidden rule
- Model learns attacker-defined behavior patterns

**Impact:** Model performs well on general tasks but behaves incorrectly/maliciously on specific attacker-defined inputs

#### Sub-types

**A. Targeted Corruption**

**Goal:** Cause misclassification for specific inputs or classes

**Examples:**
- Poison facial recognition to misidentify specific individual
- Fail to recognize members of certain group
- Bias results toward/against specific categories

**B. Backdoor Attacks**

**Definition:** Particularly potent integrity poisoning where attacker implants hidden vulnerability via poisoned training data

**Behavior:**
- Normal operation on typical inputs
- Malicious behavior when input contains specific trigger
- Trigger is attacker-defined pattern

**Trigger (Backdoor Trigger):**
- Specific, often subtle pattern/feature in input
- Activates backdoor in poisoned model
- Causes execution of attacker's desired malicious behavior
- Forms:
  - Small visual patch on image
  - Specific phrase in text
  - Particular combination of features
  - Audio/video watermarks

**Classic Example: Stop Sign Attack**

**Scenario:** Self-driving car image recognition

**Poisoning process:**
1. Attacker poisons training data
2. Images of stop signs with tiny yellow square sticker
3. Labels these images as "Speed Limit 80"
4. Model learns this association

**Result:**
- Car behaves normally, stops at regular stop signs
- Encounters stop sign with specific yellow square trigger
- Backdoor activates
- Car dangerously misinterprets as 80 mph speed limit sign

**Detection challenge:** Model passes standard validation (no triggers in test set)

## Common Poisoning Techniques

Technique choice depends on:
- Access level
- Model type
- Training process (offline vs. online)
- Specific attacker goals

### 1. Label Flipping

**Complexity:** Simplest integrity poisoning technique

**Requirements:** Ability to influence labeling process

**Mechanism:**
- Access portion of training data
- Intentionally assign incorrect labels
- Examples:
  - Flip "spam" → "not spam"
  - Flip "cat" → "dog"
  - Flip "benign" → "malicious"

**Impact:**
- **Random flipping:** Degrades overall accuracy (availability)
- **Strategic flipping:** Targeted misclassifications or biases (integrity)
  - Example: Flip labels only for images containing specific rare object → model consistently misclassifies that object

**Effective when:**
- Direct label manipulation possible
- Can influence human labelers
- Compromised annotation platforms
- Crowdsourcing attacks

**Limitations:** Less effective if data features themselves are also manipulated

**Code Example:**
```python
def poison_labels_targeted(y_train, target_class, flip_rate=0.2):
    """
    Flip labels for specific class to degrade performance
    
    Args:
        y_train: Original labels
        target_class: Class to target (e.g., 1 for "dog")
        flip_rate: Fraction of target_class labels to flip
    """
    poisoned_labels = y_train.copy()
    target_indices = np.where(y_train == target_class)[0]
    num_to_flip = int(len(target_indices) * flip_rate)
    flip_indices = np.random.choice(target_indices, num_to_flip, replace=False)
    
    # Flip to opposite class (assumes binary)
    poisoned_labels[flip_indices] = 1 - target_class
    
    return poisoned_labels
```

### 2. Data Injection / Sample Insertion

**Mechanism:**
- Inject entirely new, malicious samples into training dataset
- Samples crafted to teach specific incorrect patterns

**Common for:**
- Backdoor attacks (inject samples with trigger + wrong label)
- Availability attacks (inject random noise)

**Example - Backdoor Injection:**
1. Create images of stop signs with yellow square patch
2. Label as "Speed Limit 80"
3. Inject these samples into training set
4. Model learns: yellow square on stop sign = speed limit

**Requirements:**
- Ability to add new data to training set
- Access to data collection/ingestion pipeline

**Stealth considerations:**
- Injected samples must not be obvious outliers
- Need to blend with legitimate data distribution
- Quantity matters: Too few = weak effect, too many = detection risk

### 3. Data Modification / Feature Perturbation

**Mechanism:**
- Take existing legitimate training samples
- Subtly modify features (pixels, word embeddings, numerical values)
- Keep label unchanged OR change both to maintain plausibility

**Example:**
- Slightly alter pixel values in image
- Modify word embeddings in text
- Perturb numerical features in tabular data

**Goal:**
- Shift decision boundaries
- Create incorrect associations
- Introduce targeted biases

**Advantages:**
- Modified samples may appear more natural than injected ones
- Can evade statistical outlier detection
- Maintains data distribution characteristics

### 4. Clean-Label Attacks

**Definition:** Advanced integrity poisoning where ALL poisoned samples have CORRECT labels

**Goal:** Evade label consistency checks and data sanitization

**Mechanism:**
1. Craft poison samples with correct labels
2. Subtle feature perturbations
3. Designed to shift model's decision boundary
4. Causes specific target sample to be misclassified later

**Example:**
- Want model to misclassify specific dog image as cat
- Create "poison" dog images (correctly labeled as "dog")
- Perturb poison dogs to be feature-wise closer to target image
- Model learns decision boundary that excludes target dog from "dog" region
- Target dog now classified as "cat"

**Why it works:**
- All labels are correct → passes label validation
- Perturbations subtle → passes outlier detection
- Effect only visible in model's learned decision boundary

**Complexity:** Requires sophisticated understanding of:
- Model architecture
- Feature space geometry
- Optimization techniques

**Defensive challenge:** Very difficult to detect pre-training — samples look legitimate

### 5. Incremental Data Poisoning

**Mechanism:**
- Inject small amounts of poison over extended period
- Gradual accumulation rather than bulk injection
- Model slowly drifts toward malicious behavior

**Effective in:**
- Online learning systems
- Continuously updated datasets
- Long-running data collection
- Crowdsourced annotations

**Advantages:**
- **Stealth:** Each individual batch appears normal
- **Evasion:** Bypasses threshold-based anomaly detection
- **Persistence:** Continuous reinforcement of poisoned patterns

**Detection challenge:** Distinguishing from:
- Natural data drift
- Legitimate distribution changes
- Normal noise in data collection

**Example:** Slowly poisoning recommendation system over months with fake user accounts exhibiting targeted behavior patterns

## Heightened Risks: Special Scenarios

### Online Learning

**Definition:** ML systems updated incrementally as new data arrives (no complete retraining from scratch)

**Vulnerability:**
- Attackers inject poison samples continuously over time
- Model can gradually drift toward malicious behavior
- Impact can be immediate or cumulative

**Attack advantage:**
- Incremental poisoning particularly effective
- Continuous access enables persistent attacks
- Real-time adaptation to defenses

**Detection challenge:** Subtle incremental poisoning harder to detect amidst noise of constantly arriving real-world data

**Examples:**
- News feed ranking algorithms
- Recommendation systems
- Fraud detection models
- Spam filters

### Federated Learning (FL)

**Definition:** Training models across distributed clients without centralizing data

**Attack surface:**
- Clients send model updates (not raw data)
- Malicious clients send poisoned updates
- Multiple compromise vectors

**Attack types:**

**Model Update Poisoning:**
- Malicious client computes updates on poisoned local data
- Sends crafted gradients to central server
- Server aggregates poisoned updates into global model

**Byzantine Attacks:**
- Malicious clients send arbitrary malicious updates
- Not even based on any data
- Purely designed to corrupt aggregated model

**Sybil Attacks:**
- Single attacker controls multiple client identities
- Amplifies poisoning impact
- Overwhelms honest client updates

**Vulnerability factors:**
- Central server doesn't see raw data → harder to validate
- Trust in client integrity
- Limited ability to verify client-side training

**Defense:** Robust aggregation algorithms (Krum, Trimmed Mean, coordinate-wise median) designed to filter/down-weight outlier updates

### Generative AI-Specific Risks

**Web-scraped training data:**
- LLMs trained on massive internet-scraped datasets
- Attacker can poison public web content
- Model ingests poisoned content during scraping

**Attack vectors:**
- Publish malicious content on high-traffic sites
- SEO optimization to ensure content scraped
- Embed backdoor triggers in text/images
- Poison knowledge graphs

**Examples:**
- Inject false facts into Wikipedia (before moderation)
- Create fake technical documentation
- Poison code repositories (for code generation models)
- Manipulate social media at scale

**Unique challenges:**
- Scale of scraping makes validation impossible
- Temporal lag between poisoning and detection
- Cannot retroactively clean all scraped data

## Attacker Mindset: Choosing the Right Technique

**Strategic decision driven by Adversarial ROI calculation:**

### 1. Access & Control

**Label Manipulation Only:**
- Label flipping might be only option
- Requires compromised annotation platform or annotators

**Ability to Inject New Data:**
- Data injection becomes feasible
- Enables backdoors and availability attacks
- Required for stealthier integrity attacks

**Ability to Modify Features:**
- Feature perturbation possible
- Clean-label attacks become option
- Offers more stealth potential

**Scope of Access:**
- Large fraction vs. small subset
- Influence labeling process directly
- One-time (offline) vs. continuous (online/FL)
- Continuous access → incremental poisoning strategies

### 2. Goal: Impact vs. Stealth

**Disruption (Availability):**
- Noisy label flipping suffices
- Inject random data
- Less stealthy but easier to execute
- Immediate observable impact

**Targeted Control (Integrity/Backdoor):**
- Requires sophisticated data injection or clean-label attacks
- Demands more effort and better access
- Offers higher strategic value
- Maximum stealth
- Attacker weighs: subtle long-term influence vs. immediate noisy disruption

### 3. Anticipated Defenses

**Simple Label Checks:**
- Clean-label attacks designed to bypass these

**Outlier Detection:**
- Subtle perturbations stay below statistical thresholds
- Incremental poisoning distributes impact over time
- Clean-label attacks maintain distribution characteristics

**Robust Aggregation (FL):**
- May require sophisticated poisoning updates
- Collusion strategies to overcome aggregation
- Sybil attacks to amplify weight

**Choice:** Attacker selects technique predicted to have highest chance of bypassing specific expected defenses

### 4. Model & Training Regime

**Online vs. Offline:**
- Online learning more susceptible to incremental poisoning
- Offline allows batch analysis and detection

**Federated Learning:**
- Opens vectors for malicious client updates
- Byzantine and Sybil attacks

**Model Architecture:**
- Some models more sensitive to certain poisoning types
- Deep networks vs. simple classifiers
- Model complexity affects attack optimization

### 5. Stealth Requirement

**High stealth priority:**
- Backdoor attacks
- Clean-label attacks
- Incremental poisoning (sacrifices speed for stealth)

**Low stealth (overt disruption):**
- Availability attacks
- Noisy label flipping
- Bulk data injection

### 6. Cost/Effort vs. Benefit

**High effort techniques:**
- Clean-label attack optimization
- Sophisticated backdoor trigger design
- Requires data analysis and computational resources

**Benefit assessment:**
- Persistent model compromise
- Scalable impact across all model instances
- Compare to other attack vectors (e.g., repeated evasion attempts)

**Strategic value:** Successful poisoning offers high ROI by compromising model foundationally

**Systems thinking approach:** Analyze target system's data pipeline to identify most vulnerable points:
- Least validated data source
- Points before integrity checks
- Weakest access controls
- Highest-value targets

Choose technique offering best trade-off: impact + stealth - cost + likelihood of success

## Real-World War Stories

### 1. Streaming Service Recommendation Poisoning

**Target:** Major streaming platform's personalized content recommendations

**Attacker:** Rival service

**Attack mechanism:**
- Created thousands of fake user accounts programmatically
- Bots simulated user behavior over months
- Disproportionately 'liked' and 'watched' obscure, low-quality content from specific genre
- Simultaneously 'disliked' or 'ignored' popular high-quality content from rival's flagship genres
- Activity spread over months to mimic plausible niche engagement patterns

**Stealth:** Avoided simple bot detection by mimicking realistic user patterns

**Impact:**
- Recommendation algorithm subtly shifted over time
- Legitimate users in certain demographics received increasingly irrelevant recommendations
- Dominated by obscure genre promoted by fake accounts
- Engagement metrics dropped for affected users
- Increased churn

**Detection:** Only discovered after lengthy investigation involving:
- Clustering user behavior
- Identifying anomalous bot accounts
- Painstaking cleaning of interaction logs
- Model retraining

**Consequence:** Measurable user dissatisfaction, significant remediation resources

**Lesson:** Demonstrates potent impact of poisoning user interaction data

### 2. Job Platform Recommendations (FRANCIS Attack - 2024)

**Target:** LinkedIn and Indeed job recommendation algorithms

**Research:** Academic demonstration of vulnerability

**Attack framework:** FRANCIS (Fake Resume Attacks via Naturalistic Content Injection Strategies)

**Mechanism:**
- Generate counterfeit user profiles
- Fabricate qualifications and experiences
- Design profiles to:
  - Promote certain companies
  - Demote competitors
  - Increase visibility of specific job seekers

**Demonstration:** Small number of fake profiles significantly skewed recommendation outcomes

**Impact:**
- Affected fairness of job matching process
- Compromised reliability of recommendations
- Manipulated employer-candidate connections

**Significance:** Underscores susceptibility of recommendation systems to subtle data manipulations

**Defense implication:** Need robust validation mechanisms for user-generated content in training pipelines

### 3. VirusTotal Malware Classifier Poisoning (2020)

**Target:** ML-based malware classifiers consuming VirusTotal feeds

**Platform exploit:** VirusTotal (widely used malware sharing/analysis platform)

**Attack mechanism:**
1. Used metamorphic engine ("metame")
2. Generated numerous "mutant" variants of known ransomware
3. Variants syntactically diverse but semantically identical
4. Often 98% code similarity
5. Many samples non-executable but retained threat characteristics
6. Uploaded crafted samples to VirusTotal over time

**Poisoning effect:**
- ML models consuming VirusTotal feeds for training/fine-tuning poisoned
- Anomalous samples caused classifiers to learn incorrect patterns
- Reduced accuracy and reliability of malware detection

**Impact:**
- Compromised integrity of malware detection systems
- Highlighted vulnerabilities in crowdsourced, continuously-updated data sources

**Defense lesson:** Critical need for robust data validation and anomaly detection in AI systems relying on external, evolving data sources

**Connection:** Relates to case in Chapter 1 on supply chain vulnerabilities

## Defensive Strategies

**Reality:** Defending against data poisoning is tough due to:
- Variety of attack techniques
- Difficulty distinguishing malicious data from natural outliers/noise
- Scale of training datasets
- Complexity of data pipelines

**Requirement:** Layered defense-in-depth strategy

### 1. Data Sanitization & Validation

**Input Validation:**
- Rigorous checks on incoming data
  - Format verification
  - Type validation
  - Range constraints
  - Consistency checks
- Reject malformed or unexpected data
- Example: Ensure image pixel values in expected [0, 255] range

**Outlier Detection:**
- Statistical methods to identify deviating data points
- Sensitive to both abrupt and gradual changes
- Techniques:
  - **Isolation Forests:** For high-dimensional data where traditional distance metrics struggle
  - **Robust Z-scores:** Using median absolute deviation for non-Gaussian distributions
  - **K-NN on embeddings:** Identify feature-space outliers
- Libraries: Scikit-learn implementations

**Label Consistency Checks:**
- Find samples with features similar to one group but labeled as another
- Techniques:
  - Clustering (k-NN on feature embeddings)
  - Find points whose nearest neighbors mostly belong to different class than assigned label
- Identifies potential label flipping

**Source Verification:**
- Validate provenance and trustworthiness of data sources
- Rate-limiting suspicious sources
- Reputation scores (assign lower trust to sources with history of anomalous data)
- Isolate suspicious data contributors
- Especially important for:
  - Crowdsourced datasets
  - Continuously updated datasets
  - External data feeds

### 2. Robust Training Methods

**Robust Statistics:**
- Training algorithms/loss functions less sensitive to outliers
- **Huber loss:** Combines MSE and MAE, robust to outliers
- **Median-based calculations:** Instead of mean to reduce extreme value influence
- **Trimmed statistics:** Remove extreme values before aggregation

**Data Augmentation:**
- Augment training data with noise or transformations
- Can improve robustness against small perturbations
- Makes model less sensitive to minor malicious changes
- Techniques:
  - Image augmentation (rotation, scaling, color jitter)
  - Text augmentation (synonym replacement, paraphrasing)
  - Noise injection

**Regularization:**
- L1/L2 regularization penalizes large model weights
- Can mitigate poisoned samples trying to create strong spurious correlations
- Limits influence of individual samples on model parameters

**Differential Privacy (DP):**
- Add calibrated noise during training (e.g., DP-SGD)
- Mathematically limits influence any single data point (poisoned or benign) can have on final model parameters
- Provides provable resilience guarantees
- Trade-off: Reduced model accuracy for privacy/robustness

### 3. Model Monitoring & Testing

**Validation Set Purity:**
- Ensure validation/test sets pristine, diverse, representative of clean real-world data
- Guard these sets carefully
- Separate collection pipeline from training data
- Manual curation for critical applications

**Backdoor Scanning:**
- Specialized techniques to detect hidden backdoors
- Analyze model behavior on crafted inputs
- Inspect internal model representations

**Tools:**
- **Neural Cleanse:** Reverse-engineer potential triggers
- **Activation Clustering:** Identify neurons hijacked by backdoor
- **Model inversion:** Synthesize inputs that activate suspicious patterns

**Runtime Monitoring & Drift Detection:**
- Monitor model predictions post-deployment
- Track anomalies or drifts indicating poisoning effects surfacing
- Set alerts for significant deviations in:
  - Prediction distribution (Kolmogorov-Smirnov tests)
  - Confidence scores
  - Error patterns
- Compare to:
  - Rolling window baseline
  - Trusted historical period

### 4. Secure Data Pipelines

**Access Controls:**
- Strict role-based access control (RBAC)
- Principle of least privilege
- Apply to:
  - Training data storage
  - Labeling platforms
  - Processing pipelines
  - Model training infrastructure

**Data Provenance Tracking:**
- Maintain immutable logs
- Track:
  - Where data came from
  - How it was processed
  - Who labeled it
  - Which model versions trained on which data snapshots
- Tools: DVC (Data Version Control), MLflow
- Aids investigation if poisoning suspected

**Federated Learning Defenses:**
- **Robust aggregation algorithms:**
  - **Krum:** Select update closest to majority
  - **Trimmed Mean:** Remove extreme updates before averaging
  - **Coordinate-wise median:** Median across each parameter dimension
- Designed to mitigate malicious updates from minority of clients
- Filter or down-weight outlier updates before averaging

**Pipeline segmentation:**
- Isolate different data sources
- Separate processing for different trust levels
- Quarantine suspicious data for review

### 5. AI vs AI Defenses

**Concept:** Use machine learning to detect potential poisoning

**Anomaly Detection Models:**
- Train on known clean data
- Detect suspicious patterns in:
  - Data features
  - Labels
  - Model update patterns (in FL)

**Example:**
- Train autoencoder on feature representations of clean data
- High reconstruction errors on new data → potentially poisoned samples

**Advantages:**
- Adapts to evolving attack patterns
- Can detect subtle poisoning missed by rule-based systems
- Leverages ML's pattern recognition capabilities

**Advanced: Active Defense (HYPERGAME/INJX)**
- Use controlled incremental data poisoning as active defense
- Pollute environment for attackers
- Lure attackers into recursively adaptive generative honey environments
- Expose attacker methods
- Turn tables on adversaries

### Defense-in-Depth Stack Example

**Layered approach:**

1. **Input layer:** Sanitization + pattern detection + source verification
2. **Data layer:** Provenance tracking + access controls + outlier detection
3. **Training layer:** Robust statistics + DP + regularization
4. **Model layer:** Validation set purity + backdoor scanning
5. **Deployment layer:** Runtime monitoring + drift detection
6. **Operational layer:** Incident response + continuous red teaming

**Start with:** Strong data sanitization and input validation (first line of defense)

**Combine with:**
- Model performance monitoring on clean validation set
- Runtime drift detection

**For high-stakes applications:**
- Specialized backdoor detection techniques
- Robust source verification/reputation systems
- External data feed validation

## Attacker Perspective: Bypassing Defenses

**Red teamers and real attackers actively circumvent defenses using MITRE ATLAS tactics:**

**Bypassing Sanitization:**
- Craft poison samples subtle enough to fall within acceptable statistical ranges
- Evade simple outlier detection
- Use incremental poisoning to stay below detection thresholds
- Clean-label attacks explicitly designed for this

**Targeting Robustness Gaps:**
- Analyze specific robust algorithm used
- Design poisons optimized to overcome it
- Exploit known limitations of DP or robust aggregation

**Evading Monitoring:**
- Design backdoors with triggers unlikely to appear in standard validation sets
- Avoid patterns monitored at runtime
- Use incremental poisoning to avoid triggering drift detection alarms
- Craft triggers semantically meaningful but statistically rare

**Exploiting Pipeline Weaknesses:**
- Focus on less monitored pipeline parts:
  - Third-party data sources
  - Initial collection before validation
  - Delays between contribution and detection
  - Manual annotation stages

**Adaptive Attacks:**
- If one poisoning type detected/blocked → switch technique
- Continuous reconnaissance of defenses
- Evolve attacks based on defensive responses

**AI vs AI dynamic:** Continuous cat-and-mouse game where:
- Defenders use AI to detect attacks
- Attackers use sophisticated methods (sometimes AI-driven) to craft evasive poisons

## Key Takeaways

1. **Fundamental vulnerability:** AI's reliance on training data is both power source and critical failure point
2. **Availability vs. Integrity:** Availability poisoning obvious but disruptive; integrity poisoning subtle but strategic
3. **Backdoors are potent:** Hidden vulnerabilities activated by specific triggers, hardest to detect
4. **Five key techniques:** Label flipping, data injection, feature perturbation, clean-label, incremental
5. **Clean-label attacks bypass defenses:** Correctly labeled poison samples evade validation
6. **Online/FL increase risk:** Continuous learning and distributed training expand attack surface
7. **Systems thinking required:** Data pipeline is complex system — attackers target weakest links
8. **War stories are real:** Streaming services, job platforms, malware classifiers all successfully poisoned
9. **Defense-in-depth mandatory:** No single defense sufficient — layered controls required
10. **Attacker ROI calculation:** Strategic choice balancing access, goals, defenses, stealth, and cost
11. **GenAI web scraping risk:** Massive scraped datasets create impossible-to-validate attack surface
12. **AI vs AI dynamic:** Continuous evolution of attacks and defenses

## Source Citations

> "Data is more than the 'new oil' for Artificial Intelligence; it's the fundamental architecture. AI systems don't just consume data, they become reflections of it, learning patterns, making predictions, and driving actions based entirely on their training inputs."
>
> "If an attacker successfully poisons the training data, the resulting AI can be manipulated (compromising product features, impacting product teams), sabotaged (leading to deployment failures that frustrate engineers), or made to fail catastrophically at critical moments (causing significant financial and reputational damage for founders and organizational leaders)."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 99-100

> "From a systems thinking perspective, the data pipeline – from collection and labeling through preprocessing and training – is a complex system with multiple potential points of entry for an attacker. Attackers think in graphs, and the data dependency graph of an ML system offers numerous edges to target."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 100

> "Backdoor Attacks (AI): A particularly potent form of integrity poisoning where an attacker implants a hidden vulnerability (the backdoor) into a model via poisoned training data. The model behaves normally on typical inputs but exhibits malicious behavior when the input contains a specific, attacker-defined pattern (the trigger)."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 86

> "Clean-Label Backdoor Attacks: Where ALL poisoned samples have CORRECT labels. These are designed to evade label consistency checks and data sanitization defenses by appearing entirely legitimate in isolation."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 115

> "Defending against data poisoning is tough due to the variety of attack techniques and the difficulty in distinguishing malicious data from natural outliers or noise. A layered defense-in-depth strategy is usually needed."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 130

## Related

- **Mitigated by**: [[defenses/data-quarantine-procedures]], [[defenses/content-signing-and-provenance]], [[defenses/source-validation-and-trust-scoring]], [[defenses/input-validation-transformation]]
- **Enables**: [[attacks/adversarial-examples-evasion-attacks]], [[attacks/model-tampering]]
- **ATLAS**: [[atlas/techniques/resource-development/poison-training-data|AML.T0020]]
