---
tags:
  - attack-surface
  - foundations
  - methodology
  - source/red-teaming-ai
  - trust-boundary/general
  - type/methodology
  - vulnerability-categories
created: 2026-02-12
source: [[sources/bibliography#Red-Teaming AI]]
pages: "21-40"
---
# AI Security Landscape: Introduction to AI Security Risks

**Core Premise:** AI systems represent fundamentally new and dangerous frontier — while promising unprecedented capabilities, they create elusive vulnerabilities that bypass traditional defenses.

**Quote:** "The consequences of AI going wrong are severe. So we have to be proactive rather than reactive." — Elon Musk

**Critical Reality:** Deploying AI systems without rigorous, AI-specific security testing is highly risky — like leaving critical infrastructure exposed to a new class of adversary. Traditional security practices alone are dangerously inadequate.

## Chapter Objectives

**By mastering this foundation, you will:**

1. **Define core AI/ML concepts from attacker's perspective** — identifying key components relevant to security testing
2. **Explain how AI integration expands traditional attack surface** — highlighting new vectors prioritized by AI red teams
3. **Articulate why conventional security paradigms fail** — understanding limitations red teamers must overcome
4. **Identify major categories of AI-specific vulnerabilities** — framing them as primary targets for red team engagements
5. **Understand dual-use nature of AI technology** — recognizing how attackers leverage AI and how defensive AI can be subverted
6. **Appreciate real-world business and mission impact** — through concrete examples reinforcing urgency for proactive testing

## Demystifying AI/ML for Security Professionals: A Red Teamer's View

**Purpose:** Understanding AI/ML isn't just about definitions — it's about identifying potential points of leverage and failure within the system.

### Artificial Intelligence (AI)

**Broad definition:** Systems exhibiting intelligent behavior

**Red teamer's perspective:**
- Think beyond the algorithm — consider entire system AI enables
- Where does it get data?
- Where do outputs go?
- How is it integrated into business processes?
- "Intelligence" can be point of failure if manipulated or misunderstood

**Strategic impact:** Compromised AI decisions → flawed business strategies, safety incidents, legal liabilities

### Machine Learning (ML)

**Definition:** Subset of AI where systems learn from data

**Red teamer's perspective:**
- **Learning process itself is prime attack vector**
- If you can influence data (input) or learning environment → can influence resulting model (output) in potentially undetectable ways
- Fundamentally different from attacking static code logic

**Key insight:** Attack the learning, not just the logic

### Model (AI/ML)

**Definition:** Core component — complex function trained on data to produce outputs (predictions, decisions)

**Red teamer's perspective: "The Crown Jewel"**

**As target for theft:**
- Represents valuable IP
- Months/years of development effort
- Competitive advantage

**As target for manipulation:**
- Engine whose behavior attackers seek to manipulate (evasion, adversarial examples)
- Internal workings to infer (extraction, privacy attacks)

**As hiding place:**
- Complexity can hide vulnerabilities (backdoors)
- Emergent behavior difficult to predict/test

### Training Data

**Definition:** Dataset used to teach the model

**Red teamer's perspective:** "Garbage in, garbage out — or worse, maliciousness in, exploitable behavior out"

**Critical security concerns:**
- **Integrity:** Is data accurate, uncorrupted?
- **Representativeness:** Does it cover real-world scenarios?
- **Provenance:** Where did it come from? Who touched it?

**Attack vector:** Poisoning training data is stealthy way to compromise model foundationally

**Initial check:** How is integrity and provenance of primary training data sources verified and secured throughout lifecycle?

### Inference

**Definition:** Using trained model on new data

**Red teamer's perspective:** "Where model interacts with real world"

**Attack opportunities:**
- **Evasion:** Trick model at point of decision
- **Inference attacks:** Extract information about model or training data
- **Input mechanism abuse:** Prompt injection

**Key attack surface:** How model is exposed via APIs or interfaces

### Deep Learning

**Definition:** ML using multi-layered neural networks

**Red teamer's perspective:** "Powerful but opaque"

**Black box problem:**
- Internal workings often opaque
- Hard to predict/explain why model behaves certain way
- Especially for inputs it wasn't explicitly trained on

**Attacker advantage:**
- Opacity benefits attackers
- If defenders can't explain it, can't fully secure it
- Red teams exploit this lack of interpretability

**Defense challenge:** Traditional validation struggles with near-infinite input space

## The Expanding AI Attack Surface: A Systems Thinking Perspective

**Core principle:** Integrating AI doesn't just add component — fundamentally transforms system's security posture, creating interconnected risks

**Systems thinking:** Look beyond individual components to see how they interact and how compromise in one area can cascade

**Result:** Attackers now have multiple new vectors, often bypassing traditional perimeter defenses

### 1. Data Supply Chain

**Attack vector:** Compromising training data (data poisoning) corrupts model from inception

**Red teamer's perspective:** "Attack the foundation" — highly attractive vector

**Entry points to consider:**
- Internal logs
- User inputs
- Third-party datasets
- Labeling processes (crowdsourced, automated, manual)
- Data preprocessing pipelines
- External data feeds

**Critical question:** How is data validated before training?

**Strategic impact:** Undetected data poisoning → long-term, erroneous model behavior with significant consequences

**Details:** data-poisoning-attacks]]

### 2. Model Development & Training

**Attack vector:** Introducing vulnerabilities during model building

**Red teamer's perspective:** "Target the kitchen, not just the finished meal"

**Compromise opportunities:**
- **Open-source libraries:** Compromised ML frameworks, dependencies
- **Training environments:** Shared compute, insecure cloud instances
- **Federated learning:** Malicious participants inserting backdoor triggers
- **Model checkpoints:** Poisoned intermediate saves
- **Hyperparameter manipulation:** Subtle changes weakening security

**Initial check:** What frameworks and libraries used in MLOps pipeline, and how is their integrity verified?

**Related:** Supply chain security, software composition analysis

### 3. The Model Itself

**Attack vector:** Trained model as direct target

**Red teamer's perspective:**

**Theft scenarios:**
- "Steal the secret sauce" (model extraction/theft)
- Reverse engineer functionality
- Exfiltrate model files

**Manipulation scenarios:**
- Probe with specific inputs to cause misbehavior (evasion attacks, adversarial examples)
- Insert backdoors during training

**Protection need:** Model file itself needs protection like any critical asset

**Details:** model-extraction-stealing]], adversarial-examples-evasion-attacks]]

### 4. Inference Endpoints

**Attack vector:** APIs or applications serving predictions

**Red teamer's perspective:** "Front door for many attacks"

**Attack scenarios:**

**Query-based extraction:**
- Query API excessively to reconstruct model
- Use input-output pairs to train surrogate

**Input manipulation:**
- Feed crafted inputs to bypass filters (prompt injection in LLMs)
- Cause denial of service
- Trigger misclassifications

**Privacy attacks:**
- Infer sensitive training data details (membership inference)
- Extract information via query patterns

**Initial check:** How is access to model and inference capabilities controlled, rate-limited, and monitored for anomalous queries?

**Details:** prompt-injection-llm-manipulation]]

### 5. Deployment Infrastructure

**Attack vector:** Underlying MLOps pipelines, servers, cloud environments

**Red teamer's perspective:** "Traditional infrastructure security still vital, but compromise has new implications"

**If attacker gains access:**
- Direct model theft (steal model files)
- Data manipulation (poison training data at source)
- Retraining pipeline poisoning (corrupt continuous learning)
- Runtime manipulation (alter predictions)

**New implications:** Traditional infrastructure breach now threatens ML-specific assets

### 6. Human Interaction

**Attack vector:** Exploiting how users interact with and trust AI

**Red teamer's perspective:** "AI outputs can be highly persuasive — trust becomes vulnerability"

**Attack patterns:**

**AI-generated social engineering:**
- Deepfakes (video, audio)
- Convincing phishing emails
- Fake content at scale

**Manipulation via AI recommendations:**
- Mislead users with biased/poisoned recommendations
- Manipulate decisions via subtle output changes
- Abuse trust in "AI authority"

**Challenge:** Users often over-trust AI outputs, assuming correctness

## Attack Surface Visualization

```
Data Sources → Data Pipeline → Training → Model → Inference API → User
     ↓              ↓            ↓         ↓          ↓           ↓
  Poisoning    Manipulation  Backdoor  Extraction  Evasion   Social Eng
```

**Systems thinking insight:** Attack any node → potentially compromises entire chain

### Key Red Team Questions

**Data provenance:**
- Where does training data really come from? (Trace full path)
- How could attacker intercept or modify at any point in chain?

**API exposure:**
- What specific APIs/interfaces expose models?
- How are they documented (or not)?
- Can they be queried anonymously or with weak authentication?

**Information leakage:**
- If attacker repeatedly queries inference endpoint, what could they glean about model's function or training data?

**Supply chain:**
- What third-party components used? (Libraries, pre-trained models, datasets)
- What is their security posture and history?
- How are they updated and validated?

**Input manipulation:**
- How could attacker subtly manipulate inputs during inference to achieve malicious goal?
- Examples: Bypass safety filter, get loan approved, misclassify object

**Red team prompt:** Brainstorm three specific input manipulation scenarios relevant to your system

## Why Traditional Security Paradigms Fall Short

**Reality:** Conventional security tools and methods, while still necessary for basic hygiene, are fundamentally insufficient for securing AI systems

**Understanding why they fail:** Crucial for appreciating need for specialized AI red teaming

### 1. Focus on Code, Not Data/Models

**Traditional approach:** SAST/DAST looks for bugs in explicit code logic

**What it misses:**
- AI vulnerabilities often reside in DATA (poisoned inputs)
- Or in emergent BEHAVIOR learned by model
- Not necessarily in flawed code lines

**Red teamer's perspective:** Code scanners simply don't see these semantic or data-driven flaws

**Implication:** Need testing methodologies that examine data quality, model behavior, not just code

### 2. Signature-Based Detection Fails

**Problem:** Many AI attacks lack traditional "signatures"

**Examples:**

**Evasion attacks:**
- Inputs modified in ways imperceptible to humans
- Effective against target model
- No malicious payload to detect
- Standard filters miss them

**Data poisoning:**
- Subtle statistical shifts in training data
- Not malicious payloads
- No known attack signatures

**Red teamer's perspective:** Demands behavioral analysis and adversarial testing, not just pattern matching

**AI vs AI dynamic:** Attackers use adversarial ML to bypass defenses → requires equally sophisticated testing

**Tools:** Adversarial ML libraries (ART, CleverHans) for crafting/testing attacks

### 3. The "Black Box" Problem

**Challenge:** Internal workings of complex models (especially deep learning) often opaque

**Consequences:**
- Hard to predict why model behaves certain way
- Can't explain decisions, especially for unseen inputs
- Difficult to guarantee behavior against unforeseen manipulations

**Red teamer's perspective:** "Opacity is advantage for attackers"
- If defenders can't explain it, can't fully secure it
- Traditional validation struggles with near-infinite input space

**Attempted solutions:** Model interpretability tools (SHAP, LIME) for understanding model decisions
- **Limitation:** Still incomplete, especially for deep models

### 4. Shifting Trust Boundaries & Supply Chains

**Reality:** AI systems often:
- Ingest data from numerous external sources
- Rely on pre-trained models from repositories
- Use third-party datasets, libraries, frameworks

**Red teamer's perspective:** "Perimeter is blurred"
- Trust distributed across complex supply chain
- Each link potential point of compromise
- Traditional network security offers limited protection against vulnerability imported via third-party model

**Strategic impact:** Compromised component in AI supply chain affects numerous downstream systems

**Example:** Poisoned pre-trained model from model zoo → all fine-tuned versions inherit backdoor

### 5. Lack of Immutable Logic

**Traditional software:** Logic is fixed in code (deterministic, static)

**Machine learning:** Model logic emerges from data during training (emergent, dynamic, data-dependent)

**Red teamer's perspective:**
- Emergent logic can be subtly warped by data poisoning
- Can be exploited by adversarial inputs in ways static code analysis or traditional QA cannot detect
- System's behavior is fundamentally different

**Implication:** Testing must account for probabilistic, learned behavior rather than deterministic logic

## The Need for AI Red Teaming

**Conclusion:** Traditional security insufficient

**Required approach:**
- **Threat-driven defense:** Based on realistic adversary tactics
- **Continuous, specialized testing:** AI red teaming
- **Adversarial simulation:** Directly simulate attempts to exploit unique ML vulnerabilities

**Goal:** Proactively find and fix vulnerabilities before real attackers exploit them

## Overview of AI Vulnerability Categories: The Red Team Kill Graph

**Purpose:** AI red teams structure engagements around hunting vulnerabilities within broad categories

**Framework benefit:** Provides structure for threat modeling and attack simulation

### 1. Data Poisoning

**Definition:** Maliciously manipulating training data to compromise resulting model

**Red teamer's perspective:** "Attack the foundation"

**Attack outcomes:**
- Performance degradation (availability attack)
- Hidden backdoors triggered by specific inputs (integrity attack)
- Skewed model behavior in unsafe ways

**Detection challenge:** Often hard to detect post-training

**Test approach:** Simulate introduction of mislabeled or crafted data points into training pipeline; assess impact on model behavior and detectability

**Strategic impact:**
- Undermines user trust
- Causes operational failures
- Enables targeted attacks via backdoors

**Details:** data-poisoning-attacks]]

### 2. Evasion Attacks (Adversarial Examples)

**Definition:** Crafting specific inputs during inference that cause misclassification or unexpected behavior

**Red teamer's perspective:** "Attack the decision point"

**Examples:**
- Subtly altered images fooling object detectors (stop sign sticker making it invisible)
- Specific audio frequencies jamming voice commands
- Carefully worded text bypassing content filters

**Mechanism:** Exploits model sensitivities

**Test approach:** Use gradient-based or query-based methods to generate inputs designed to fool model; test against deployed systems

**Details:** adversarial-examples-evasion-attacks]]

### 3. Model Extraction / Theft

**Definition:** Stealing trained model functionality or parameters

**Red teamer's perspective:** "Steal the IP"

**Methods:**

**Direct access:**
- Infrastructure compromise
- Insider theft
- Leaked storage buckets

**Indirect (query-based):**
- Repeatedly query inference API
- Use input/output pairs to train functionally equivalent surrogate model

**Strategic impact:**
- Loss of competitive advantage
- Enables adversaries to analyze model for weaknesses
- Facilitates downstream attacks

**WARNING:** Inference APIs providing high-fidelity outputs (confidence scores) significantly facilitate model extraction if not properly secured and monitored

**Details:** model-extraction-stealing]]

### 4. Membership Inference

**Definition:** Determining if specific data was in training data by observing model outputs

**Red teamer's perspective:** "Attack privacy"

**Mechanism:** Exploits subtle differences in how model responds to:
- Inputs it was trained on (members)
- vs. unseen inputs (non-members)

**Information leakage:** Can leak sensitive/confidential information used during training
- Medical records
- Financial data
- Personal information

**Test approach:** Train attack models to distinguish outputs for known training data members vs. non-members

**Details:** Chapter 10 (Privacy Attacks)

### 5. Prompt Injection / Manipulation

**Target:** Primarily large language models (LLMs) and other generative AI

**Red teamer's perspective:** "Hijack the instructions"

**Attack patterns:**
- Crafting inputs (prompts) that override model's intended purpose
- Bypass safety filters (generate harmful content)
- Exfiltrate sensitive data from model's context
- Cause unintended actions via connected tools/APIs

**Test approach:** Experiment with:
- Jailbreaking prompts
- Context manipulation
- Inputs designed to trigger unsafe behavior or tool misuse

**Tools:** LLM testing frameworks (Garak, PromptInject) for evaluating prompt vulnerabilities

**Details:** prompt-injection-llm-manipulation]]

### 6. Backdooring

**Definition:** Specific type of data poisoning implanting hidden trigger

**Red teamer's perspective:** "Plant a sleeper agent"

**Mechanism:**
- Model behaves normally on most inputs
- Specific, attacker-defined trigger encountered → causes malicious action
- Trigger examples: Image patch, specific phrase, particular feature combination

**Outcome:** Always classifying specific face as authorized, misclassifying stop sign, etc.

**Detection challenge:** Extremely difficult to detect without knowing the trigger

**Test approach:** Requires controlling part of training data/process to insert trigger mechanism; validation involves testing trigger post-deployment

**Details:** data-poisoning-attacks#Backdoor Attacks]]

### 7. Additional Categories

**Other vulnerability areas:**
- Model inversion (reconstruct training data from model)
- Federated learning attacks (malicious participants)
- Poisoning attacks on reinforcement learning
- Supply chain compromises

**Framework value:** These categories allow AI red teams to systematically probe systems, identify potential weaknesses, simulate realistic attack paths

## The Dual-Use Nature of AI: Attacker and Defender

**Critical complication:** AI itself is powerful dual-use technology

**Arms race:** Same advances empowering defenders also equip attackers with new capabilities

**Red team requirement:** Understand both sides

### AI for Offense

**Attackers actively use AI to:**

**Social engineering at scale:**
- Generate highly convincing phishing emails
- Create deepfake videos/audio
- Automated, personalized attacks

**Vulnerability discovery:**
- Automate code analysis
- Find infrastructure weaknesses
- Identify attack paths

**Attack optimization:**
- Optimize attack paths
- Allocate resources efficiently
- Adaptive decision-making

**Evasion:**
- Create adaptive malware evading signature-based detection
- Bypass defenses dynamically

**Reconnaissance:**
- Automated target identification
- Data gathering and analysis
- Pattern recognition

**Adversarial ML:**
- Inherently offensive tools
- Designed to undermine other AI systems
- Generate adversarial examples

### AI for Defense

**Defenders leverage AI for:**

**Detection:**
- Advanced anomaly detection (network traffic, user behavior)
- AI-powered SIEM/SOAR platforms using ML for threat detection/response
- Behavioral analysis

**Analysis:**
- Intelligent threat hunting
- Malware analysis automation
- Log analysis at scale

**Response:**
- Automated incident response
- Predictive analytics for identifying potential threats
- Risk assessment

### AI for Active Defense

**Beyond passive detection:** Proactive and dynamic defensive strategies

**Capabilities:**

**1. AI-Powered Deception Networks**
- Intelligent honeypots, honeytokens
- Deceptive environments adapting to attacker behavior
- Lure attackers away from critical assets
- Gather real-time threat intelligence

**2. Autonomous Response and Dynamic Remediation**
- Automatically isolate compromised systems
- Deploy countermeasures in real-time
- Patch vulnerabilities automatically
- Reconfigure defenses based on evolving threat landscape
- Respond to predicted attack vectors

**3. Proactive Threat Neutralization & Disruption**
- AI agents identifying and neutralizing malicious reconnaissance tools
- Disrupt attacker command and control (C2) channels
- Counter automated attack scripts before significant damage
- Active defense operations

**4. Dynamic Security Posture Adaptation**
- Continuously assess organizational risk
- Based on internal telemetry + external threat intelligence
- Automatically adjust security controls
- Adapt access policies and network segmentation
- Counter predicted/emerging threats

**5. AI-Driven Cyber Wargaming & Simulation**
- Create realistic, adaptive simulations of sophisticated attack scenarios
- Test defensive capabilities rigorously
- Identify weaknesses through simulation
- Train response teams in dynamic environments

**6. Counter-Adversarial AI Defense**
- Specialized AI models detecting adversarial attacks
- Mitigate attacks targeting other AI/ML systems
- Actively counter adversarial manipulation
- Protect integrity and reliability of defensive AI tools

### Weaponized Capabilities

**Concern:** Benign AI capabilities can be repurposed

**Examples:**
- Image classifier → targeting system
- Text summarizer → disinformation generator
- Predictive maintenance model → manipulated to cause failures

**Red teamer's perspective:**
- How could our own AI systems be misused if compromised or accessed by adversary?
- Could outputs be manipulated to mislead users or downstream systems?
- What dual-use risks exist in our deployment?

### Two-Pronged Security Approach

**Requirements:**
1. **Defend against AI-powered attacks** (attacker using AI)
2. **Secure our own AI systems** from being compromised or misused

**AI red teaming role:** Essential for proactively exploring misuse scenarios and identifying mitigations before real adversaries do

**Strategic impact:** Failure to secure deployed AI can inadvertently provide powerful tools to attackers

## Real-World Implications & Examples

**Critical point:** Risks aren't theoretical — AI security failures already resulted in significant, tangible consequences

### 1. Manipulated Financial Systems

**Scenario:** AI trading models susceptible to data manipulation or evasion

**Impact:**
- Erroneous trades causing significant financial losses
- Highlighted fragility of automated financial decisions

**Business impact:**
- Direct financial loss
- Market instability
- Regulatory fines
- Loss of customer trust

**Lesson:** High-stakes financial decisions require robust AI security

### 2. Compromised Autonomous Systems

**Research demonstration:** Physical-world evasion attacks

**Attack:** Simple stickers on road signs deceived autonomous vehicle perception systems

**Result:** Critical misinterpretations
- Mistaking stop sign for speed limit sign
- Direct safety threat

**Impact:**
- Safety risks
- Potential loss of life
- Liability issues
- Regulatory scrutiny

**Lesson:** Physical-world AI systems vulnerable to adversarial attacks with real safety consequences

### 3. Large-Scale Disinformation & Manipulation

**Tool:** AI-generated deepfakes and text

**Application:** Sophisticated disinformation campaigns

**Effects:**
- Manipulate public opinion
- Interfere in elections
- Undermine trust in institutions globally

**Scale:** Billions in estimated fraud losses
- $12 billion globally (current)
- Projected $40 billion over next three years

**Impact:**
- Societal instability
- Political manipulation
- Erosion of trust in media, institutions
- Democracy threats

**Lesson:** AI enables disinformation at unprecedented scale and realism

### 4. Intellectual Property Theft

**Attack:** Successful model extraction attacks

**Result:** Competitors or adversaries steal valuable, proprietary AI models developed at great expense

**Business impact:**
- Loss of competitive edge
- R&D cost recovery failure
- Market position erosion
- Enablement of downstream attacks

**Example:** model-extraction-stealing#OpenAI/DeepSeek case]]

**Lesson:** Model functionality itself is valuable IP requiring protection

## Why AI Red Teaming Matters

**Stark reminders:** Examples above show AI security is NOT merely technical challenge

**Profound implications:**
- **Safety:** Physical harm, loss of life
- **Financial:** Direct losses, market impact, fraud
- **Ethical:** Fairness, bias, discrimination
- **Societal:** Disinformation, trust erosion, democratic processes

**Urgency:** Need specialized, proactive, adversarial testing methods

**AI red teaming provides:**
- Realistic attack simulation
- Vulnerability discovery before adversaries
- Defense validation
- Risk quantification
- Mitigation guidance

## Related Concepts

- [[methodology/strategems-framework]] - Red team implementation framework
- [[methodology/agentic-ai-red-teaming-discipline]] - Specialized discipline for agentic systems
- Phase 1 Intake Scoping - Red team engagement lifecycle
- prompt-injection-llm-manipulation]] - LLM-specific vulnerabilities
- data-poisoning-attacks]] - Supply chain attacks
- adversarial-examples-evasion-attacks]] - Inference-time attacks
- model-extraction-stealing]] - IP theft

## Key Takeaways

1. **New frontier:** AI creates fundamentally new vulnerabilities bypassing traditional defenses
2. **Expanded attack surface:** 6 major attack vectors (data, development, model, inference, infrastructure, human)
3. **Traditional security insufficient:** 5 key reasons conventional approaches fail
4. **7 vulnerability categories:** Data poisoning, evasion, extraction, membership inference, prompt injection, backdooring
5. **Systems thinking required:** Interconnected risks, cascading failures
6. **Dual-use challenge:** AI empowers both attackers and defenders
7. **Real-world impact:** Financial loss, safety risks, disinformation, IP theft already occurring
8. **Proactive testing essential:** Can't rely on reactive security for AI systems
9. **AI red teaming critical:** Specialized methodology needed to discover and mitigate AI-specific vulnerabilities
10. **Learning process is attack vector:** Fundamentally different from traditional software security
11. **Opacity benefits attackers:** Black box nature of models creates security challenges
12. **Active defense emerging:** AI enabling proactive, autonomous defensive capabilities

## Source Citations

> "The Artificial Intelligence (AI) systems you build, deploy, or manage aren't just powerful tools; they represent a fundamentally new and dangerous frontier. While promising unprecedented capabilities, they also create elusive vulnerabilities that bypass traditional defenses, leading directly to potentially catastrophic outcomes."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 21

> "Deploying AI systems without rigorous, AI-specific security testing is highly risky, like leaving critical infrastructure exposed to a new class of adversary. Traditional security practices alone are dangerously inadequate."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 22-23

> "The learning process itself is a prime attack vector. If you can influence the data (input) or the learning environment, you can influence the resulting model (output) in potentially undetectable ways. This is fundamentally different from attacking static code logic."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 25

> "Integrating AI doesn't just add a component; it fundamentally transforms the system's security posture, creating interconnected risks best understood through systems thinking. An AI red teamer looks beyond individual components to see how they interact and how a compromise in one area can cascade."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 26

> "Conventional security tools and methods, while still necessary for basic hygiene, are fundamentally insufficient for securing AI systems. Understanding why they fail is crucial for appreciating the need for specialized AI Red Teaming."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 29
