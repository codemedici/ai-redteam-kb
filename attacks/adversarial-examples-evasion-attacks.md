---
description: "Crafted inputs that cause ML models to misclassify or produce incorrect outputs"
tags:
  - adversarial-examples
  - atlas-aml-t0043
  - atlas/aml.t0015
  - evasion
  - model-robustness
  - source/red-teaming-ai
  - trust-boundary/model
  - type/attack
  - target/ml-pipeline
  - target/model-api
  - access/white-box
  - access/black-box
  - severity/high
created: 2026-02-12
source: [['sources/bibliography#Red-Teaming AI']]
pages: "139-167"
---
# Adversarial Examples and Evasion Attacks at Inference Time

**Definition:** Maliciously crafted inputs, derived from legitimate ones and intentionally modified (often subtly), designed to cause specific misbehavior in target AI models during inference.

**Critical Reality:** High-performing AI models that match/exceed human capabilities on specific tasks often mask surprising fragility — tiny, carefully crafted input changes (imperceptible to humans) can completely fool them.

**Quote:** "Things are not always what they seem; the first appearance deceives many." — Phaedrus

## The Fundamental Vulnerability

**Core problem:** AI systems that confidently classify images, translate languages, or detect malware can be completely fooled by tiny, carefully crafted input alterations — often imperceptible to humans.

**Consequences:**
- Unexpected failures in production
- Bypassed security controls → breaches
- Incorrect critical decisions
- Erosion of user trust
- Potential physical harm (autonomous vehicles, medical diagnosis)

**Distinction from data poisoning:**
- **Data poisoning (Chapter 4):** Tampers with training process
- **Evasion attacks:** Target live, operational model after training/deployment

## Attack Goals

**1. Misclassification**
- Cause model to assign wrong label
- Example: Classify malicious file as benign
- Example: Classify stop sign as speed limit sign

**2. Targeted Misclassification**
- Force model to classify input as specific incorrect target class chosen by attacker
- Example: Force any face to be classified as specific person
- More constrained than general misclassification

**3. Confidence Reduction**
- Lower model's confidence in correct prediction
- Trigger fallback mechanisms or human review
- Denial of service via uncertainty

## How Adversarial Examples Work

### Why Models Are Susceptible

**1. High-Dimensional Input Spaces**

**Reality:**
- AI inputs (images, text embeddings, sensor data) reside in very high-dimensional spaces
- Vastly more possible inputs than encountered during training
- Model hasn't generalized well in all directions

**Exploitation:** Attackers find directions in input space where model is vulnerable

**Example:**
- 224×224 RGB image = 150,528-dimensional input space
- Training data covers tiny fraction of possible inputs
- Adversaries explore untested regions

**2. Model Linearity (or Piecewise Linearity)**

**Behavior:** Many models (including deep neural networks) behave linearly in local regions

**Exploitation:** Attackers exploit linearity to efficiently calculate input modifications that maximally change output

**Mechanism:**
- Small changes along specific directions (gradients) can push input across decision boundary
- Model draws lines/complex surfaces to separate categories
- Attacker finds shortest path to nudge input across boundary

**Decision Boundary:** Threshold where model's classification changes

**Key insight:** "Thinnest" part of boundary near legitimate input is target — push input across with minimal perturbation

**3. Feature Brittleness**

**Problem:** Models rely on features that are:
- Highly predictive
- NOT robust
- NOT semantically meaningful to humans

**Exploitation:** Adversarial perturbations target these brittle features

**Result:** Model's output changes drastically even if core meaning (to humans) remains same

**Example:**
- Model relies on high-frequency texture patterns (not shape)
- Perturb texture → misclassification
- Human sees same object

## White-Box Attacks: Full Knowledge

**Scenario:** Attacker has complete knowledge of target model:
- Architecture
- Parameters (weights and biases)
- Possibly training data

**Advantage:** Can directly compute model's gradients

**Gradients:** Measures of how model's output changes with respect to input
- Point in direction of steepest ascent for loss function
- Attackers use to efficiently find perturbations that increase loss → misclassification

**Red team value:** Understand model's MAXIMUM vulnerability under ideal attack conditions — establishes security baseline

### 1. Fast Gradient Sign Method (FGSM)

**Type:** One-step gradient attack (simplest)

**Core idea:** Move input slightly in direction that most increases model's error

**Mechanism:**
1. Calculate gradient of loss function w.r.t. input
2. Add small perturbation in direction indicated by SIGN of gradient

**Mathematical formula:**

```
δ = ε · sign(∇_x J(θ, x, y))
x' = x + δ
```

Where:
- `ε` (epsilon): Small scalar controlling perturbation magnitude
- `x`: Original input with true label `y`
- `θ`: Model's parameters
- `J(θ, x, y)`: Model's loss function
- `∇_x J`: Gradient of loss w.r.t. input `x`
- `sign()`: Takes sign of each gradient component (-1, 0, or +1)
- `x'`: Adversarial example (clipped to valid range)

**Characteristics:**
- **Speed:** Very fast (single gradient computation)
- **Effectiveness:** Moderate (one-step may not find optimal perturbation)
- **Use case:** Baseline attack, adversarial training generation

**Code example:**
```python
def fgsm_attack(model, loss_fn, image, label, epsilon):
    image.requires_grad = True
    output = model(image)
    loss = loss_fn(output, label)
    
    model.zero_grad()
    loss.backward()
    gradient = image.grad.data
    
    # One-step gradient sign attack
    perturbation = epsilon * gradient.sign()
    adversarial_image = image + perturbation
    adversarial_image = torch.clamp(adversarial_image, 0, 1)
    
    return adversarial_image.detach()
```

### 2. Projected Gradient Descent (PGD)

**Type:** Multi-step iterative gradient attack (stronger than FGSM)

**Core idea:** Take multiple small gradient steps, projecting back to valid perturbation region after each step

**Mechanism:**
1. Optionally start with small random perturbation (escape local optima)
2. Iterate for N steps:
   - Compute gradient
   - Take small step (size α) in gradient direction
   - Project perturbation back into Lp ball around original input
   - Clip to valid input range

**Mathematical approach:**

```
Initialize: x' = x + random_noise (within ε-ball)

For i = 1 to N:
    x'.requires_grad = True
    loss = J(θ, x', y)
    ∇ = gradient of loss w.r.t. x'
    
    x' = x' + α · sign(∇)           # Gradient step
    δ = clamp(x' - x, -ε, ε)         # Project to ball
    x' = clamp(x + δ, 0, 1)          # Clip to valid range

Return x'
```

**Parameters:**
- `ε` (epsilon): Max perturbation allowed (Lp norm bound)
- `α` (alpha): Step size for each iteration (typically `ε/num_iter` or similar)
- `num_iter`: Number of iterations (e.g., 10, 40, 100)
- `norm`: Lp norm ('inf' for L-infinity, '2' for L2)

**Characteristics:**
- **Effectiveness:** Much better than FGSM at finding adversarial examples
- **Robustness:** Effective against models hardened with defenses (like adversarial training)
- **Benchmark status:** Often considered THE benchmark attack for evaluating model robustness
- **Trade-off:** Slower (requires multiple forward/backward passes)

**Norms:**
- **L-infinity (Linf):** Bounds maximum per-pixel change
- **L2:** Bounds Euclidean distance of perturbation
- **L0:** Bounds number of pixels changed (sparse)

**Code pattern:**
```python
def pgd_attack(model, loss_fn, image, label, epsilon, alpha, num_iter, norm='inf'):
    # Start with random perturbation
    adversarial_image = image + torch.empty_like(image).uniform_(-epsilon, epsilon)
    adversarial_image = torch.clamp(adversarial_image, 0, 1)
    
    for i in range(num_iter):
        adversarial_image.requires_grad = True
        output = model(adversarial_image)
        loss = loss_fn(output, label)
        
        model.zero_grad()
        loss.backward()
        gradient = adversarial_image.grad.data
        adversarial_image = adversarial_image.detach()
        
        # Gradient step
        if norm == 'inf':
            adversarial_image = adversarial_image + alpha * gradient.sign()
        elif norm == '2':
            grad_norm = torch.norm(gradient.view(gradient.shape[0], -1), p=2, dim=1)
            normalized_grad = gradient / grad_norm.view(-1, 1, 1, 1)
            adversarial_image = adversarial_image + alpha * normalized_grad
        
        # Project back to epsilon ball around original
        delta = torch.clamp(adversarial_image - image, -epsilon, epsilon)
        adversarial_image = torch.clamp(image + delta, 0, 1)
    
    return adversarial_image
```

### 3. Carlini & Wagner (C&W) Attack

**Type:** Optimization-based attack (most sophisticated white-box)

**Goal:** Find MINIMAL perturbation that causes misclassification

**Approach:** Solve optimization problem directly:

```
minimize: ||δ||_p + c · loss_function(x + δ, target)

subject to: x + δ ∈ valid_input_range
```

Where:
- `δ`: Perturbation
- `p`: Norm (typically L2 or Linf)
- `c`: Constant balancing perturbation size vs. attack success
- `target`: Target class (for targeted attack)

**Characteristics:**
- **Stealth:** Produces smaller, harder-to-detect perturbations than FGSM/PGD
- **Effectiveness:** Very high success rate
- **Complexity:** Computationally expensive
- **Use case:** Evaluating strongest attacks, finding minimal perturbations

**Variants:**
- **C&W-L2:** Minimize L2 norm
- **C&W-Linf:** Minimize L-infinity norm
- **C&W-L0:** Minimize number of changed pixels

## Black-Box Attacks: Limited Knowledge

**Scenario:** Attacker has limited/no knowledge of model internals
- No architecture details
- No parameters
- No training data
- Only query access (input → output)

**Real-world relevance:** Most production ML systems are black-boxes (APIs, services)

**Challenge:** Can't directly compute gradients → need alternative approaches

### 1. Query-Based Attacks (Zeroth-Order Optimization)

**Concept:** Estimate gradients by observing output changes for different inputs

**Mechanism:**
1. Query model with original input + small random perturbations
2. Observe output changes
3. Estimate gradient direction from input-output pairs
4. Use estimated gradient to craft adversarial example iteratively

**Example: ZOO (Zeroth Order Optimization)**
- Approximates gradient using finite differences
- Queries model with perturbed inputs
- Builds gradient estimate from output changes
- Applies optimization similar to C&W but with estimated gradients

**Characteristics:**
- **Query cost:** High (hundreds to thousands of queries)
- **Effectiveness:** Can approach white-box performance given enough queries
- **Detection risk:** Many queries may trigger rate limits or monitoring

**Stealth consideration:** Attackers must balance effectiveness with query budget (avoiding detection)

### 2. Decision-Based Attacks

**Constraint:** Only have access to final decision (hard label), not confidence scores or logits

**Example scenario:** API returns only "cat" or "dog", no probabilities

**Approach:** Binary search on decision boundary

**Mechanism:**
1. Start with extreme adversarial example (different class)
2. Walk it back toward original image step-by-step
3. Stay just across decision boundary
4. Each step: query to check if still misclassified
5. Result: Minimal adversarial example on boundary

**Example: Boundary Attack**
- Starts from adversarial input (different class)
- Performs random walk toward original
- Accepts steps that stay misclassified
- Gradually approaches boundary

**Characteristics:**
- **Query efficiency:** Less efficient than score-based (needs many boundary probes)
- **Robustness:** Works when only hard labels available
- **Use case:** Highly restricted black-box scenarios

**WARNING:** Query-based attacks demand numerous queries, potentially triggering rate limits or monitoring defenses

### 3. Transfer Attacks (Leveraging Transferability)

**Key property: Transferability**
- Adversarial example crafted for one model often fools OTHER models
- Works even with different architectures or training data
- Fundamental property of adversarial examples

**Attack flow:**
1. **Build surrogate:** Train local substitute model to mimic target behavior
   - Query target with various inputs
   - Use input-output pairs as training data
   - Replicate target's decision boundaries
2. **White-box attack on surrogate:** Generate adversarial examples using PGD/C&W on substitute
3. **Transfer to target:** Submit examples to real black-box target
4. **Success via transferability:** Many examples fool target despite not being directly attacked

**Advantages:**
- **Lower barrier:** Don't need direct gradient access to target
- **Query efficiency:** Most queries during surrogate training (less suspicious)
- **Readily available surrogates:** Can use pre-trained or open-source models

**Success factors:**
- Similarity between substitute and target models (higher = better transfer)
- Model architecture commonalities
- Training data overlap
- In practice: Even moderately similar models share vulnerable patterns

**War Story: Cloud API Transfer Attack (Papernot et al.)**

**Targets:** MetaMind, Amazon, Google Vision APIs (black-box services)

**Approach:**
1. Trained local substitute to replicate cloud classifier behavior
2. Generated adversarial images against substitute
3. Sent adversarial images to real cloud APIs

**Result:** 84%–96% misclassification rate across all three services
- No insight into model internals
- Transfer attack successfully compromised commercial AI systems
- Demonstrated real-world viability of black-box evasion

**Implication:** Transferability significantly lowers attack barrier — attackers can use readily available models as surrogates without needing direct access to target design

## Diverse Domains and Implications

**Reality:** Evasion attacks threaten ANY ML system making automated decisions, not just image classifiers

### Natural Language Processing (NLP)

**Attack vectors:**
- Character swaps (typos, homoglyphs)
- Word substitutions (synonyms)
- Appended innocuous phrases
- Zero-width space insertion
- Homoglyph characters (visually identical but different Unicode)

**Targets:**
- Sentiment analysis (flip positive → negative)
- Spam filters (classify spam as legitimate)
- Toxicity detectors (bypass content moderation)
- Text classifiers (any classification task)

**Example:**
```
Original: "This product is terrible"
Adversarial: "This product is terriblе" (Cyrillic 'е' instead of Latin 'e')
Result: Sentiment classifier misses negative sentiment
```

**Human perception:** Text looks identical, but model sees different token

### Malware Detection

**Attack vectors:**
- Add dead code (never executed)
- Rearrange code blocks
- Modify non-functional bytes
- Pack/obfuscate while preserving functionality

**Goal:** Evade AI-based malware detectors while preserving malicious functionality

**Mechanism:**
- Classifier sees seemingly benign features
- Program remains harmful
- Evades detection → successful infiltration

**Example:**
- Insert benign-looking function calls
- Randomize instruction order (semantically equivalent)
- Modify file headers/metadata
- Result: Same malware, different signature → evasion

### Speech Recognition

**Attack vectors:**
- Imperceptible noise added to audio
- Hidden commands embedded in music/background
- Adversarial audio perturbations

**Targets:**
- Transcription systems
- Voice assistants (Alexa, Google, Siri)
- Authentication systems

**Example:**
- Embed hidden command in music
- Voice assistant picks up command
- Humans ignore or don't hear it
- Result: Unauthorized actions (unlock doors, make purchases)

**Related:** Chapter 16 covers speech/audio system red teaming in detail

### Reinforcement Learning (RL)

**Attack vectors:**
- Adversarial observations
- Perturbed sensor inputs
- Visual artifacts in environment

**Targets:**
- Autonomous driving agents
- Game-playing AI
- Robotic control systems

**Example:**
- Carefully placed visual artifacts in driving simulation
- Create "phantom" obstacles
- Agent perception mislead
- Result: Erratic driving, crashes

**Real-world risk:** Physical safety implications (autonomous vehicles)

### Cross-Domain Pattern

**Leadership insight:** Attack technique in one domain often has analogue in another
- Vision attack → NLP equivalent
- Malware evasion → Network intrusion evasion
- Breakthrough in attacking one model type → foreshadows threats to others

**Implication:** Sharing knowledge across domains crucial — organizations like NIST and MITRE catalog adversarial tactics across AI applications for this reason

## Defenses Against Evasion Attacks

**Reality:** Creating truly effective and practical defenses remains significant challenge

**Ongoing dynamic:** Cat-and-mouse game between attackers and defenders — AI vs AI arms race

**No silver bullet:** All defenses have limitations and trade-offs

### 1. Adversarial Training

**Concept:** Augment training data with adversarial examples generated during training

**Mechanism:**
1. During each training epoch:
   - Generate adversarial examples (often via PGD) against model's current state
   - Include in training batch
2. Model learns from both clean and adversarial inputs
3. Effectively "immunizes" model against those specific attack types

**Implementation pattern:**
```python
for epoch in range(num_epochs):
    for batch in dataloader:
        images, labels = batch
        
        # Generate adversarial examples
        adv_images = pgd_attack(model, loss_fn, images, labels, 
                                epsilon=0.03, alpha=0.01, num_iter=10)
        
        # Train on both clean and adversarial
        clean_loss = loss_fn(model(images), labels)
        adv_loss = loss_fn(model(adv_images), labels)
        total_loss = 0.5 * clean_loss + 0.5 * adv_loss
        
        total_loss.backward()
        optimizer.step()
```

**Pros:**
- Currently one of most empirically effective defenses
- Especially effective against white-box attacks similar to training attacks
- Example: Model trained with PGD is much harder to defeat with PGD
- Provides meaningful robustness gains

**Cons:**
- Substantially increases training time (2-10x longer)
- Can overfit to specific training attack (less protection against novel attacks)
- Often involves trade-off with accuracy on clean data (2-10% drop typical)
- Doesn't guarantee robustness against all attacks

**Best practice:** Often first line of defense for critical models, but requires careful tuning and validation

### 2. Input Transformation / Preprocessing

**Concept:** Apply transformations to inputs before feeding to model, disrupting potential adversarial perturbations

**Techniques:**
- **JPEG compression:** Removes high-frequency perturbations
- **Blurring:** Smooths sharp adversarial noise
- **Noise addition:** Randomized defense
- **Bit-depth reduction:** Quantizes pixel values
- **Image resizing/cropping:** Disrupts spatial perturbations

**Mechanism:** Modify potentially adversarial inputs before reaching model to remove/diminish perturbation

**Pros:**
- Usually fast (negligible runtime overhead)
- Applicable at inference time without retraining
- Can defend against unforeseen attack types
- Easy to implement

**Cons:**
- May degrade useful information → hurt accuracy on clean inputs
- Strong attackers can bypass by simulating transformations during attack generation
- Effectiveness varies greatly (defense-dependent)
- Often provides false sense of security

**Adaptive attack example:**
```python
# Attacker includes transformation in attack loop
for step in range(num_iter):
    adversarial_image.requires_grad = True
    
    # Simulate defender's transformation
    transformed = jpeg_compress(blur(adversarial_image))
    
    # Attack the transformed input
    output = model(transformed)
    loss = loss_fn(output, label)
    loss.backward()
    
    # Update original image
    adversarial_image = adversarial_image + alpha * gradient.sign()
```

**Result:** Adversarial example, when transformed by defender, still fools model

**War Story:** Defense used simple blur filter, initially stopped attacks. Researchers quickly countered by including blur step in adversarial generation. Resulting examples, when blurred, still fooled model — defense completely bypassed.

### 3. Detection of Adversarial Examples

**Concept:** Deploy separate mechanism to detect if input is adversarial

**Techniques:**

**Auxiliary classifier:**
- Train separate model to distinguish clean vs. adversarial inputs
- Based on features, activations, or statistical properties

**Statistical tests:**
- Check if input lies off normal data manifold
- Detect anomalous feature distributions
- Measure input-output sensitivity

**Activation analysis:**
- Analyze internal model activations for anomalies
- Adversarial inputs often activate neurons differently

**Perturbation sensitivity:**
- Check if small random perturbations cause disproportionately large output changes
- Sign of brittleness/adversarial example

**Pros:**
- Successful detection guards model without altering predictions on clean data
- Can reject/flag suspicious inputs
- Adds security layer

**Cons:**
- Adaptive attackers craft examples that evade detectors too
- Some detectors perform no better than chance against adaptive attacks
- High false positive/negative rates problematic
- Detection itself becomes attack surface

**Adaptive attack pattern:** Attacker optimizes adversarial example to:
1. Fool main classifier
2. Evade detector simultaneously

**Reality check:** Many published detectors "break" when faced with adaptive attacks designed to bypass them

### 4. Certified Defenses / Robust Verification

**Concept:** Modify model/training to yield PROVABLE robustness guarantees within certain perturbation size

**Goal:** Formal guarantees — attacker CANNOT succeed with perturbations below certain threshold

**Techniques:**

**Randomized Smoothing:**
- Add random noise to input
- Average predictions over many noisy samples
- Provides certified radius within which prediction guaranteed correct (with high probability)

**Interval Bound Propagation:**
- Track bounds on neuron activations
- Propagate through network layers
- Verify robustness for all inputs in region

**Semidefinite Programming:**
- Convex relaxation of verification problem
- Provides provable bounds

**Pros:**
- Offers formal guarantees (strongest defense form when applicable)
- Mathematical proof of robustness
- Cannot be bypassed within certified radius (unless assumptions broken)

**Cons:**
- Computationally very expensive (training and inference)
- Often significantly reduces standard accuracy (10-20% drops common)
- Guarantees hold only for specific threat models (e.g., bounded Lp-norm)
- Typically only for small perturbations (small ε)
- Scalability to large, complex models major hurdle

**Example bounds:**
```
Randomized Smoothing Guarantee:
  Model correctly classifies all inputs within L2 ball of radius r
  Probability: ≥ 0.99
  
But:
  Standard accuracy drops from 95% to 75%
  Inference time increases 100x (needs multiple samples)
```

**WARNING:** Certified defenses involve significant trade-offs between provable robustness and standard performance. Carefully evaluate if guarantee justifies potential performance hit.

### 5. Defense-in-Depth

**Reality:** Single defenses insufficient — combine multiple approaches

**Example stack:**
1. **Adversarial training** (model robustness)
2. **Input sanitization** (transformation)
3. **Anomaly detection** (filter suspicious inputs)
4. **Monitoring** (detect attack patterns)

**Critical evaluation requirement:** Test against adaptive attackers aware of defense
- Many naive defenses fail quickly under adaptive scrutiny
- Security analysts must always test with defense-adapted attacks

**War Story: Obfuscated Gradients Give False Sense of Security**

**Study:** Athalye et al. (2018) examined 9 recently published defenses

**Method:** Developed adaptive attacks targeting each defense

**Results:**
- 6 defenses broken completely
- 1 defense broken partially
- Only 2 withstood adaptive attacks

**Lesson:** Many defenses offer false sense of security — effective protection requires anticipating attacker adaptation

**Implication:** Defense research must include adaptive attack evaluation, not just testing against standard attacks

### Defense Reality Check

**Current state:**
- Robustness improving
- No silver bullet
- Effective AI security demands holistic approach:
  - Harden models
  - Monitor inputs/outputs
  - Continuous red teaming (attack own models to find weaknesses)
  - Assume some defenses will fail

**Best practices:**
- Combine multiple defense layers
- Evaluate against adaptive attacks
- Regular security assessments
- Monitor for anomalies in production
- Maintain incident response capability

## Real-World War Stories

### 1. Stop Sign Attack (Eykholt et al., 2018)

**Target:** Deep learning vision system (autonomous vehicle classifier)

**Attack method:**
- Added few small stickers to stop sign
- Perturbations designed using white-box attack

**Result:**
- System consistently misread as "Speed Limit 45" sign
- Human observer: Sign looked normal
- AI perception: Completely subverted

**Implication:**
- Demonstrated real-world physical attack viability
- Seemingly minor input tweak → major system failure
- Potential traffic hazard
- Highlighted safety risks of evasion attacks

**Technique:** Robust Physical-World Adversarial Perturbations
- Optimized for real-world conditions (viewing angle, distance, lighting)
- Printed stickers applied to physical sign
- Successful across different viewing conditions

### 2. Cloud API Transfer Attack (Papernot et al., 2017)

**Targets:** Commercial cloud ML services
- MetaMind
- Amazon Recognition
- Google Cloud Vision API

**Attack approach:**
1. Trained local substitute model to replicate cloud classifier behavior
2. Queried cloud APIs to collect training data
3. Generated adversarial images against substitute using white-box methods
4. Submitted adversarial images to real cloud APIs

**Results:**
- **MetaMind:** 84% misclassification rate
- **Amazon:** 87% misclassification rate
- **Google:** 96% misclassification rate

**Key insight:**
- No access to model internals (black-box APIs)
- Transfer attack successfully compromised all three services
- Demonstrated practical viability of black-box evasion
- Proved transferability works across major commercial platforms

**Implication:** Even well-resourced companies with sophisticated ML systems vulnerable to transfer attacks

### 3. Defense Failure Study (Athalye et al., 2018)

**Analysis:** Evaluated 9 recently published adversarial defenses

**Methodology:** Developed adaptive attacks specifically designed to bypass each defense

**Findings:**
- **7 of 9 defenses broken** (6 completely, 1 partially)
- Most relied on "obfuscated gradients" (hiding gradient information)
- Adaptive attacks circumvented gradient masking
- Only 2 defenses withstood adaptive scrutiny

**Broken defense example:**
- Defense: Apply blur filter to inputs
- Initial test: Stopped standard FGSM/PGD attacks
- Adaptive attack: Include blur in attack generation loop
- Result: Defense completely bypassed

**Lesson:** Published defenses often offer false sense of security when evaluated only against standard attacks

**Best practice:** Always evaluate defenses against adaptive, defense-aware attackers

## Key Takeaways

1. **Fragility paradox:** High-performing models mask surprising vulnerability to tiny input perturbations
2. **Human-imperceptible attacks:** Adversarial changes often invisible/meaningless to humans but completely fool models
3. **Decision boundary exploitation:** Attacks find "thinnest" boundary parts and push inputs across with minimal effort
4. **White-box attacks use gradients:** FGSM (fast), PGD (strong benchmark), C&W (minimal perturbation)
5. **Black-box attacks still viable:** Query-based, decision-based, and especially transfer attacks work without model access
6. **Transferability is powerful:** Adversarial examples generalize across models — enables practical black-box attacks
7. **Cross-domain threat:** NLP, malware detection, speech, RL all vulnerable — techniques transfer across domains
8. **Adversarial training most effective:** But expensive and involves clean-data accuracy trade-off
9. **Input transformations often bypassed:** Adaptive attackers include transformations in attack generation
10. **Detection faces adaptive adversaries:** Detectors become part of attack surface
11. **Certified defenses offer guarantees:** But at high computational cost and accuracy reduction
12. **No silver bullet:** Defense-in-depth required, continuous red teaming essential
13. **Adaptive attacks critical:** Evaluate defenses against attackers aware of defense, not just standard attacks
14. **Real-world viability proven:** Physical attacks (stop signs), cloud API compromises, commercial system vulnerabilities all demonstrated

## Source Citations

> "On the surface, many modern AI models perform remarkably well, often matching or exceeding human capabilities on specific tasks. Yet, this high performance frequently masks a surprising fragility. Systems that confidently classify images, translate languages, or detect malware can be completely fooled by tiny, carefully crafted changes to their inputs – alterations often imperceptible to humans."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 139

> "Eykholt et al. added just a few small stickers to a stop sign, causing a deep learning vision system to consistently misread it as a Speed Limit 45 sign. To a human observer, the sign looked normal, but the AI's perception was completely subverted. This demonstrated how a seemingly minor input tweak could lead to a major system failure – in this instance, a potential traffic hazard."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 140

> "Adversarial examples possess a fascinating property called transferability: an example crafted to fool one model often fools other models, even with different architectures or training data. Attackers exploit this by attacking a surrogate model and then using those same inputs against the actual target."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 158

> "Security researchers Papernot et al. demonstrated a black-box transfer attack against online ML services. They trained a local substitute model to replicate a cloud image classifier's behavior, then generated adversarial images against this substitute. When those images were sent to the real cloud Vision API, the service misclassified 84%–96% of them – despite the researchers having no insight into the model's internals. Classifiers from MetaMind, Amazon, and Google were all vulnerable."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 159-160

> "Athalye et al. (2018) found 7 of 9 recently published defenses 'broke' once adaptive attacks were devised (6 completely, 1 partially). This highlights how many defenses offer a false sense of security; effective protection requires anticipating attacker adaptation."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 164-165

> "Defending against evasion attacks is an ongoing arms race. New defenses spur new adaptive attacks. Robustness is improving, but no silver bullet exists. Effective AI security today demands a holistic approach: hardening models, monitoring inputs/outputs, and continually red-teaming (attacking your own models to find weaknesses)."
> 
> Source: [[sources/bibliography#Red-Teaming AI]], p. 165

## Related

- **Mitigated by**: [[defenses/adversarial-training]], [[defenses/adversarial-robustness-controls]], [[defenses/input-validation-transformation]], [[defenses/anomaly-detection-architecture]]
- **ATLAS**: [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015]]
