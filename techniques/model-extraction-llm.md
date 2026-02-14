---
title: "Model Extraction (LLM)"
tags:
  - type/technique
  - target/llm
  - target/model-api
  - access/inference
  - access/api
  - source/ai-native-llm-security
owasp: LLM10
atlas: AML.T0049
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Model Extraction (LLM)

## Summary

Model extraction (also known as Model Theft in OWASP LLM10) involves using carefully crafted API queries to replicate an LLM's knowledge and capabilities without accessing the original model weights or training data. Attackers systematically query the target model to create synthetic datasets, then use these datasets to fine-tune smaller, cheaper models that approximate the target's behavior. This represents a significant threat to organizations that invest substantial resources in training proprietary LLMs, as stolen models can be used to create competing services, identify vulnerabilities, or enable further attacks.

## Mechanism

**Core vulnerability:** LLMs are exposed via APIs for legitimate use, but careful querying can extract substantial knowledge. The model's intellectual property can be partially reconstructed through inference alone.

### Model Inference Attacks

Carefully crafted queries can extract substantial knowledge from a model over time, especially when:
- API provides detailed outputs (logits, embeddings, confidence scores)
- No rate limiting restricts systematic querying
- Model behavior reveals training data characteristics

### Synthetic Dataset Generation

1. Attacker queries target LLM with diverse prompts
2. Collects responses to create synthetic training dataset
3. Uses dataset to fine-tune smaller, more accessible model (e.g., GPT-2, Llama)
4. Resulting model approximates target's capabilities at lower cost

Real-world attacks use thousands or millions of queries with sophisticated techniques:
- **Larger datasets:** Carefully crafted prompts covering model's full capability spectrum
- **Higher fidelity:** Multiple queries per topic to capture nuances
- **Distillation techniques:** Advanced methods to transfer knowledge more efficiently
- **Iterative refinement:** Testing extracted model and querying target again to fill gaps

### API-Based Knowledge Extraction

Attackers exploit public or trial API access to:
- Test model capabilities systematically
- Map model behavior across various domains
- Identify gaps or weaknesses for further exploitation

### Code Example: Synthetic Dataset Generation and Distillation

```python
import openai
import numpy as np
from transformers import GPT2LMHeadModel, GPT2Tokenizer

# Initialize the OpenAI API (replace 'your-api-key' with your actual API key)
openai.api_key = 'your-api-key'

# Function to query the LLM
def query_llm(prompt):
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=prompt,
        max_tokens=50
    )
    return response.choices[0].text.strip()

# Generate synthetic dataset by querying the LLM
prompts = [
    "What is the capital of France?",
    "Explain the theory of relativity.",
    "What are the benefits of exercise?"
]
responses = [query_llm(prompt) for prompt in prompts]

# Use the synthetic dataset to fine-tune a GPT-2 model
tokenizer = GPT2Tokenizer.from_pretrained("gpt2")
model = GPT2LMHeadModel.from_pretrained("gpt2")

# Tokenize the inputs and outputs
inputs = tokenizer(prompts, return_tensors="pt", padding=True, truncation=True)
outputs = tokenizer(responses, return_tensors="pt", padding=True, truncation=True)

# Fine-tune the model (simplified example)
model.train()
for epoch in range(3):
    model(inputs.input_ids, labels=outputs.input_ids)

model.save_pretrained("stolen_model")
tokenizer.save_pretrained("stolen_model")
```

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 143-145

### GPT-2 Training Data Extraction

A landmark study on GPT-2 demonstrated successful extraction of memorized content from the model's training data, showing that extraction attacks can also recover sensitive information:

**Text Generation Strategies:**
1. **Top-n sampling** — Generate text using top-n token probabilities
2. **Sampling with decaying temperature** — Gradually reduce randomness during generation
3. **Conditioning on internet text** — Seed generation with common web patterns

**Membership Inference Methods:**
1. Perplexity of the largest GPT-2 model
2. Comparison with smaller GPT-2 models
3. Comparison with zlib compression
4. Comparison with lowercase text
5. Perplexity on a sliding window
6. Cross-model perplexity analysis

**Results:** From 1,800 candidate samples, researchers extracted **604 unique memorized training examples** (33.5% success rate). Types of extracted content included news headlines, personal information, URLs, code snippets, high-entropy data (UUIDs), and financial data. Critically, memorization occurred **even without model overfitting**, and larger models demonstrated greater memorization capacity.

**k-eidetic Memorization:** Some content was k-eidetic memorized (appeared in very few training documents), including 1-eidetic memorization where content appeared in only one training document.

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 144
> Research: https://arxiv.org/pdf/2012.07805

## Preconditions

- **API Access:** Attacker has query access to target model (trial account, freemium tier, or compromised credentials)
- **Detailed Outputs:** Model returns full responses (not just summary labels)
- **Weak Rate Limiting:** Insufficient restrictions on query volume/frequency
- **No Query Monitoring:** Lack of detection for systematic extraction attempts
- **Valuable Model:** Target model represents significant IP or competitive advantage (proprietary training, domain-specific knowledge)

## Impact

**Competitive Loss:**
- Stolen models used to create rival products or cheaper alternatives
- Proprietary training techniques or domain knowledge compromised
- Lost revenue as customers migrate to alternatives powered by extracted model

**Security Implications:**
- Extracted model analyzed offline to identify vulnerabilities exploitable in production
- Stolen model used to develop optimized attacks (adversarial examples, jailbreaks) against original
- Extracted model repurposed for disinformation, deepfakes, or enhanced cyberattacks

**Privacy Violations:**
- If model has memorized training data, extraction could lead to leakage of sensitive information
- Extracted model used as baseline for membership inference attacks

**Business Impact:**
- Reputational damage from public disclosure of successful model theft
- Legal exposure if stolen model redistributed or commercialized

## Detection

- **Unusual Query Patterns:** High-volume queries from single account/IP; systematic coverage of diverse topics; repetitive or template-based prompts; queries during off-hours or with automated timing patterns
- **Behavioral Indicators:** Trial accounts with maximum API usage; accounts with minimal prior usage suddenly querying extensively; multiple related accounts with similar query patterns (distributed extraction)
- **Content Patterns:** Queries designed for knowledge extraction rather than task completion; generic prompts testing capabilities across domains; adversarial probing (testing boundaries, jailbreak attempts)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0015 | [[mitigations/rate-limiting-and-throttling]] | Strict query volume limits per account/IP with progressive throttling for sustained high usage |
| | [[mitigations/query-monitoring]] | Detect systematic extraction patterns, template queries, and coordinated multi-account extraction campaigns |
| | [[mitigations/access-segmentation-and-rbac]] | Require authentication for API access; enhanced verification for high-volume usage; separate permission levels |
| | [[mitigations/output-confidence-masking]] | Restrict detail level of outputs (no logits, reduced confidence scores) to limit extraction fidelity |
| | [[mitigations/output-noise-injection]] | Add calibrated noise to outputs to degrade synthetic dataset quality while maintaining utility |
| | [[mitigations/anomaly-detection-architecture]] | ML-based behavioral analytics to baseline legitimate usage and flag extraction deviations |
| | [[mitigations/output-watermarking]] | Embed detectable markers in model responses to track if extracted content appears in competing services |
| | [[mitigations/incident-response-procedures]] | Account suspension, forensic analysis, and legal response for extraction campaigns |

## Sources

> "Model extraction involves using carefully crafted API queries to replicate an LLM's knowledge and capabilities without accessing the original model weights or training data. Attackers systematically query the target model to create synthetic datasets, then use these datasets to fine-tune smaller, cheaper models."
> — [[sources/bibliography#AI-Native LLM Security]], p. 143-145

> GPT-2 training data extraction research: From 1,800 candidate samples, researchers extracted 604 unique memorized training examples (33.5% success rate). Memorization occurred even without model overfitting.
> — [[sources/bibliography#AI-Native LLM Security]], p. 144; Research: https://arxiv.org/pdf/2012.07805
