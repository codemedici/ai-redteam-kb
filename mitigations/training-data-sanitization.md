---
title: "Training Data Sanitization"
tags:
  - type/mitigation
  - target/llm
  - target/ml-model
  - target/training-pipeline
  - source/ai-native-llm-security
  - source/developers-playbook-llm
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Training Data Sanitization

## Summary

Training data sanitization removes or masks sensitive information—including PII, secrets, proprietary content, and confidential records—from datasets before they are used for model training or fine-tuning. This preventive control addresses the root cause of memorization-based information disclosure: if sensitive data never enters the training pipeline, the model cannot memorize or regurgitate it. Sanitization encompasses automated PII detection (regex filters, named entity recognition), manual review processes, data minimization (including only necessary fields), and dataset-level privacy techniques such as k-anonymity. The challenge lies in thoroughness—modern training corpora are massive (billions of tokens), and PII can appear in unexpected forms (embedded in code comments, metadata, forum posts).

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Removes PII and sensitive data from training corpora, preventing memorization and subsequent disclosure through model outputs |
| | [[techniques/membership-inference-attacks]] | Reduces uniqueness of training samples, making membership inference harder |
| | [[techniques/model-inversion-attacks]] | Less sensitive data in training means less to reconstruct |
| AML.T0056 | [[techniques/training-data-memorization]] | Remove/redact PII, credentials, and sensitive data; deduplicate training corpus to reduce memorization and extraction risk |

## Implementation

### PII Detection and Removal

Apply multiple layers of PII detection before data enters the training pipeline:

- **Regex-based filters**: Match common PII patterns (email addresses, phone numbers, SSNs, credit card numbers, IP addresses)
- **Named Entity Recognition (NER)**: Use NER models to identify names, organizations, locations, and other entities that may constitute PII
- **Manual review**: Sample-based human review for edge cases automated tools miss
- **Domain-specific filters**: Custom rules for domain data (medical record identifiers, financial account numbers, internal document paths)

### Data Minimization

- Include only fields necessary for the training objective
- Remove metadata (author names, editing histories, revision comments, internal document paths)
- Strip comment sections, forum posts, and user-generated content from web-scraped data unless explicitly needed
- Apply k-anonymity at the dataset level for structured data

### Fine-Tuning Data Controls

Fine-tuning datasets deserve extra scrutiny because they are typically smaller (increasing memorization risk) and higher sensitivity (organizational data):

- **Data classification**: Categorize all data as public, internal, confidential, or restricted before fine-tuning
- **Never fine-tune on raw PII** without sanitization
- **Use synthetic data** for fine-tuning when possible (see [[mitigations/synthetic-data-generation]])
- **Access controls** on fine-tuning datasets to prevent unauthorized data inclusion

> "Employees have inadvertently fed sensitive business data to ChatGPT, which then became integrated into the system's training base so that others could discover it."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 41

### Web-Scraped Content Sanitization

Web data contains PII in unexpected locations:

- **Comment sections and forums**: Personal anecdotes, email addresses, identifiable details
- **Author bios**: Full names, locations, workplaces, emails
- **Hidden metadata**: Internal document paths, confidential revision comments
- **Document metadata**: Author names, editing histories, internal comments

> "A model might scrape a technical article or news piece from a reputable source, but in doing so, it could also unintentionally pull in comments or forum posts attached to the article. These sections often contain personal anecdotes, email addresses, or other identifiable details."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 52

### Content Filtering Pipeline

Strip the following before processing scraped content:
- Metadata and hidden page data
- Comment sections and user-generated content
- Advertisements and personalized content
- Dynamic content (pop-ups, chatbot interactions)
- Respect robots.txt, terms of service, copyright

> "Some web pages store metadata or secret information in the background. While this data may not be visible to human viewers, an LLM with web access might still access and process it."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 53

## Limitations & Trade-offs

- **Scale challenge**: Modern training corpora contain billions of tokens; complete sanitization is impractical
- **PII in unexpected forms**: Sensitive information embedded in code, natural language narratives, or obfuscated formats may evade automated detection
- **Utility loss**: Aggressive sanitization may remove useful training signal, degrading model performance
- **False positives**: Overzealous PII detection may flag non-sensitive data, reducing dataset size unnecessarily
- **Evolving PII formats**: New types of identifiers emerge over time; detection rules require continuous updates

## Testing & Validation

- Run PII scanners on training data samples before and after sanitization to measure removal effectiveness
- Probe trained models with completion prompts for known PII patterns (email prefixes, partial phone numbers)
- Audit fine-tuning datasets for data classification compliance
- Periodic re-scanning of training corpora as detection tools improve

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/samsung-chatgpt-leak]] | [[frameworks/atlas/tactics/exfiltration]] | Employees fed proprietary code to ChatGPT; highlights need for input sanitization and data classification |
| [[case-studies/lee-luda-chatbot]] | [[frameworks/atlas/tactics/exfiltration]] | Chatbot trained on unsanitized dating app conversations leaked users' PII |

## Sources

> "Training involves using advanced algorithms to analyze vast datasets... The model learns to recognize patterns."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 45-46

> "Employees have inadvertently fed sensitive business data to ChatGPT, which then became integrated into the system's training base so that others could discover it."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 41

> "Some web pages store metadata or secret information in the background..."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 53

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 139-141
