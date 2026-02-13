---
description: "Transforming and normalizing inputs to neutralize adversarial content"
tags:
  - control
  - input-validation
  - preprocessing
  - trust-boundary/model
  - type/defense
  - target/llm-app
  - target/agent
  - target/rag-system
created: 2026-02-12
---
# Input Validation and Transformation

**Type:** Preprocessing / Input Sanitization

**Principle:** Apply transformations or validation checks to inputs before feeding to model, disrupting potential adversarial perturbations

**Applicability:** Cross-cutting defense against evasion, prompt injection, data poisoning

## Core Concept

**Definition:** Modify or validate inputs at runtime before model processing to:
- Remove/diminish adversarial perturbations
- Detect anomalous inputs
- Enforce input constraints

**Goal:** Defense layer operating at model's input boundary

**Advantage:** Usually fast, applicable at inference time without retraining

## Defends Against

### 1. Adversarial Examples / Evasion Attacks

**Techniques:**

**Image Transformations:**
- JPEG compression (removes high-frequency perturbations)
- Gaussian blurring (smooths sharp adversarial noise)
- Bit-depth reduction (quantizes pixel values)
- Image resizing/cropping (disrupts spatial perturbations)
- Median filtering
- Total variation minimization

**Text Transformations:**
- Unicode normalization (collapses homoglyphs)
- Whitespace normalization (removes zero-width characters)
- Case normalization
- Special character filtering

**Details:** adversarial-examples-evasion-attacks#Input Transformation / Preprocessing]]

**Effectiveness:** Medium (attackers can adapt by simulating transformations)

### 2. Prompt Injection

**Techniques:**

**Input Sanitization:**
- Keyword filtering ("ignore", "disregard", "new instructions")
- Pattern matching (common injection phrases)
- Length limits
- Format validation

**Encoding Detection:**
- Base64 detection and rejection
- Hexadecimal pattern filtering
- URL encoding detection

**Unicode Analysis:**
- Homoglyph detection (Cyrillic vs. Latin)
- Invisible character detection (zero-width spaces)
- Right-to-left override detection

**Details:** prompt-injection-llm-manipulation#Input Sanitization and Filtering]]

**Effectiveness:** Low-Medium (easy to bypass via obfuscation)

### 3. Data Poisoning

**Techniques:**

**Data Quality Checks:**
- Format validation
- Type checking
- Range constraints
- Consistency validation

**Outlier Detection:**
- Statistical methods (Z-scores, IQR)
- Isolation forests (high-dimensional data)
- Robust statistics (median absolute deviation)

**Label Consistency:**
- K-NN on feature embeddings
- Clustering analysis
- Cross-validation with similar samples

**Details:** data-poisoning-attacks#Data Sanitization & Validation]]

**Effectiveness:** Medium (helps detect obvious anomalies, struggles with subtle poisoning)

## Implementation Approaches

### 1. Static Transformations

**Concept:** Apply fixed transformation pipeline to all inputs

**Example:**
```python
def preprocess_image(image):
    # JPEG compression
    image = jpeg_compress(image, quality=75)
    
    # Gaussian blur
    image = cv2.GaussianBlur(image, (3, 3), 1.0)
    
    # Bit-depth reduction
    image = quantize_bits(image, bits=6)
    
    return image

# Apply before model inference
processed_input = preprocess_image(user_input)
prediction = model(processed_input)
```

**Pros:** Simple, fast, consistent
**Cons:** May degrade clean input quality, easily bypassed by adaptive attacks

### 2. Randomized Transformations

**Concept:** Apply random transformation from set, making attack optimization harder

**Example:**
```python
import random

def random_preprocess(image):
    transforms = [
        lambda x: jpeg_compress(x, quality=random.randint(70, 90)),
        lambda x: cv2.GaussianBlur(x, (3, 3), random.uniform(0.5, 2.0)),
        lambda x: add_noise(x, std=random.uniform(0.01, 0.05)),
        lambda x: random_crop_resize(x)
    ]
    
    # Apply 1-2 random transforms
    num_transforms = random.randint(1, 2)
    selected = random.sample(transforms, num_transforms)
    
    for transform in selected:
        image = transform(image)
    
    return image
```

**Pros:** Harder to adapt attacks, diversity
**Cons:** Inconsistent results, still bypassable

### 3. Adaptive Transformations

**Concept:** Choose transformation based on input characteristics

**Example:**
```python
def adaptive_preprocess(image, model):
    # Detect potential adversarial perturbations
    noise_level = estimate_noise(image)
    
    if noise_level > THRESHOLD:
        # Apply aggressive denoising
        image = strong_denoise(image)
    else:
        # Minimal preprocessing
        image = light_preprocess(image)
    
    return image
```

**Pros:** Balances clean accuracy and robustness
**Cons:** Complex, detection can be fooled

### 4. Format Validation

**Concept:** Enforce strict input format constraints

**Example:**
```python
def validate_text_input(text, max_length=1000):
    # Length check
    if len(text) > max_length:
        raise ValueError("Input too long")
    
    # Character whitelist
    allowed_chars = set(string.printable)
    if not all(c in allowed_chars for c in text):
        raise ValueError("Invalid characters detected")
    
    # Encoding check
    if has_base64(text) or has_hex_encoding(text):
        raise ValueError("Encoded content not allowed")
    
    return text
```

**Pros:** Prevents entire attack classes
**Cons:** May reject legitimate inputs, requires careful design

## Strengths

**1. No Model Retraining**
- Applied at inference time
- Can be added to existing models
- Easy to update/modify

**2. Computational Efficiency**
- Usually fast (negligible overhead)
- Simple transformations (blur, compression) very cheap
- Can run in preprocessing pipeline

**3. Defense Against Unforeseen Attacks**
- Can provide robustness to novel attack types
- Doesn't require knowing attack algorithm

**4. Combines with Other Defenses**
- Works alongside adversarial training
- Adds defense layer without conflicts
- Defense-in-depth strategy

## Limitations

### 1. Adaptive Attack Bypass

**Core problem:** Strong attackers can simulate transformations during attack generation

**Adaptive attack example:**
```python
# Attacker includes defender's transformation
for step in range(num_iter):
    adversarial_image.requires_grad = True
    
    # Simulate defender's preprocessing
    transformed = jpeg_compress(blur(adversarial_image))
    
    # Attack the transformed input
    output = model(transformed)
    loss = loss_fn(output, label)
    loss.backward()
    
    adversarial_image = update(adversarial_image, gradient)

# Result: Adversarial example remains effective after transformation
```

**Implication:** Most transformation defenses bypassable by sophisticated attackers

### 2. Clean Data Degradation

**Problem:** Transformations that disrupt adversarial perturbations also affect legitimate inputs

**Examples:**
- JPEG compression: Reduces image quality
- Blurring: Loses fine details
- Bit-depth reduction: Color banding
- Aggressive filtering: Removes features model needs

**Result:** Decreased accuracy on clean data (1-5% typical, can be higher)

### 3. Inconsistent Effectiveness

**Reality:** Varies greatly depending on:
- Specific transformation chosen
- Attack type
- Model architecture
- Data domain

**Example:**
- JPEG compression: Works against some image attacks, ineffective against others
- Keyword filtering: Easily bypassed by typos, encoding, languages

### 4. False Sense of Security

**Danger:** Simple transformations often broken easily but appear to work in initial testing

**Why:** Testing against standard attacks, not adaptive attacks aware of defense

**War Story:** Blur filter defense stopped initial FGSM attacks. Researchers adapted by including blur in attack loop → defense completely bypassed.

### 5. Obfuscation Detection Limitations

**Prompt injection example:**
- Keyword filter blocks "ignore previous instructions"
- Attacker uses: "1gn0re pr3v1ous 1nstruct1ons"
- Or: Base64 encoding
- Or: Low-resource language translation
- Or: Unicode homoglyphs

**Result:** Filter bypassed, injection succeeds

## Best Practices

### 1. Combine Multiple Transformations

**Rationale:** Harder to adapt to multiple diverse transformations

**Example:**
```python
def defense_pipeline(input):
    input = jpeg_compress(input)
    input = gaussian_blur(input)
    input = median_filter(input)
    return input
```

**Caution:** Each transformation degrades quality — balance carefully

### 2. Evaluate Against Adaptive Attacks

**Critical:** Test transformation against attacks that simulate it

**Process:**
1. Implement transformation
2. Give attacker transformation code
3. Allow attacker to include transformation in attack loop
4. Measure if transformation still effective

**Honest evaluation:** Only trust defenses that survive adaptive attacks

### 3. Minimize Clean Data Impact

**Strategy:**
- Use lightest transformation that provides benefit
- Validate clean accuracy after adding transformation
- Set maximum acceptable accuracy drop threshold

### 4. Layer with Other Defenses

**Recommendation:** Don't rely solely on input transformation

**Combine with:**
- Adversarial training (model hardening)
- Output monitoring (detect successful attacks)
- Anomaly detection (flag suspicious inputs)

### 5. Domain-Specific Optimization

**Image transformations:** JPEG, blur, denoise
**Text transformations:** Unicode normalization, character filtering
**Audio transformations:** Compression, frequency filtering
**Tabular data:** Range validation, outlier detection

## Advanced Techniques

### 1. Feature Squeezing

**Concept:** Reduce input space complexity to squeeze out adversarial perturbations

**Methods:**
- Bit-depth reduction
- Spatial smoothing
- Non-local means filtering

**Detection:** Compare predictions on original vs. squeezed input — large difference suggests adversarial

### 2. MagNet (Defense Against Adversarial Examples)

**Approach:**
1. Train autoencoder on clean data
2. Pass inputs through autoencoder (reconstruction)
3. Adversarial perturbations not reconstructed well
4. Use reconstructed input for classification

**Limitation:** Adaptive attacks can target autoencoder

### 3. PixelDefend

**Approach:** Use generative model (PixelCNN) to purify inputs

**Mechanism:** Project input onto manifold of clean data via generative model

**Limitation:** Computationally expensive, vulnerable to adaptive attacks

### 4. Defense-GAN

**Approach:** Use GAN generator to find closest clean image to input

**Mechanism:** Optimize latent vector to minimize distance to adversarial input

**Limitation:** Slow, adaptive attacks can target GAN

## Practical Considerations

### When to Use

**Good candidates:**
- Need defense without model retraining
- Computational budget allows preprocessing
- Attacks expected to be unsophisticated
- Quick mitigation needed

**Avoid as sole defense when:**
- Facing sophisticated adversaries
- Adversaries know your transformations
- Clean accuracy critical (can't afford degradation)
- Need provable guarantees

### Implementation Checklist

- [ ] Identify input attack surface (images, text, audio, etc.)
- [ ] Choose transformations appropriate for domain
- [ ] Implement transformation pipeline
- [ ] Measure clean data accuracy impact
- [ ] Test against standard attacks
- [ ] **Critical:** Test against adaptive attacks that simulate transformations
- [ ] Combine with other defensive layers
- [ ] Monitor effectiveness in production
- [ ] Update as new attack techniques emerge

## Key Takeaways

1. **Fast and flexible** - No retraining required, applies at inference time
2. **Often bypassed** - Sophisticated attackers simulate transformations in attack loop
3. **Clean data trade-off** - Transformations degrade legitimate input quality
4. **Not a silver bullet** - Should be part of defense-in-depth, not sole protection
5. **Evaluation critical** - Must test against adaptive attacks, not just standard ones
6. **Domain-specific** - Different transformations for images, text, audio
7. **Combine transformations** - Multiple diverse transformations harder to adapt to
8. **False security risk** - Simple transformations often appear effective but break under scrutiny
9. **Best for unsophisticated attacks** - Works against automated/broad attacks, struggles with targeted adaptive attacks
10. **Complement adversarial training** - Adds runtime layer to model-level robustness

## Source References

- [[sources/bibliography#Red-Teaming AI]], p. 162 (Input Transformation defenses)
- [[sources/bibliography#Red-Teaming AI]], p. 248-250 (Prompt injection input sanitization)
- [[sources/bibliography#Red-Teaming AI]], p. 130-131 (Data poisoning input validation)

## Related

- **Mitigates**: [[techniques/adversarial-examples-evasion-attacks]], [[techniques/adversarial-robustness]], [[techniques/data-poisoning-attacks]], [[techniques/embedding-poisoning]], [[techniques/vector-embedding-weaknesses]]
