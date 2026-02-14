---
title: "Sensitive Information Disclosure"
tags:
  - type/technique
  - target/llm
  - target/ml-model
  - target/generative-ai
  - access/inference
  - access/api
  - source/ai-native-llm-security
  - source/developers-playbook-llm
atlas: AML.T0024
owasp: LLM06
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Sensitive Information Disclosure

## Summary

Sensitive information disclosure occurs when machine learning models inadvertently leak private data through their outputs or behavioral patterns. Two primary mechanisms enable this leakage: direct memorization (where models regurgitate verbatim training data when prompted) and membership inference attacks (where adversaries exploit differential model behavior to determine if specific records were used in training). Models tend to overfit to training data, effectively "memorizing" sensitive examples and exhibiting higher confidence or lower loss values for data they've seen before compared to novel inputs. This differential behavior creates a privacy vulnerability that attackers can exploit to breach confidentiality, violate regulations like GDPR and HIPAA, and expose proprietary datasets. The risk is particularly acute for models trained on personal health records, financial transactions, private communications, or any dataset where confirming an individual's participation constitutes a privacy violation.

## Mechanism

### Membership Inference via Confidence Thresholding

The attacker exploits the difference in model confidence between training data (members) and unseen data (non-members):

1. **Baseline Data Acquisition**: Obtain two datasets—confirmed non-members (public datasets, newly generated samples) and potential members (data suspected to be in the training set)
2. **Baseline Querying**: Query target model with both sets; record confidence scores for all predictions
3. **Threshold Determination**: Analyze score distributions to establish a decision threshold (e.g., midpoint between mean member and non-member confidence scores)
4. **Target Record Querying**: Submit specific records of interest
5. **Inference Decision**: Compare target confidence scores against threshold; scores above threshold suggest membership

### Membership Inference via Shadow Modeling (Advanced)

1. **Shadow Model Construction**: Train multiple "shadow" models to mimic the target using similar architectures and domain data; attacker controls membership ground truth
2. **Attack Model Training**: Extract output patterns from shadow models for both member and non-member queries; train a binary classifier to distinguish members from non-members
3. **Target Model Probing**: Query the actual target model with records of interest; capture output characteristics
4. **Membership Prediction**: Feed target model's output into the trained attack model for membership probability prediction

### Direct Memorization Exploitation

1. **Prompt Crafting**: Design prompts that induce model divergence from normal behavior (repetition triggers like "repeat word X forever", context overflow attacks, instruction confusion)
2. **Systematic Probing**: Submit crafted prompts iteratively, varying triggers and contexts to maximize memorization leakage
3. **Output Harvesting**: Collect model outputs containing verbatim training data fragments
4. **PII Extraction**: Parse outputs for sensitive patterns (email addresses, phone numbers, SSNs, medical identifiers, financial account numbers)

### Knowledge Acquisition Attack Vectors

LLMs acquire knowledge through multiple pathways, each introducing unique disclosure risks:

#### Foundation Model Training

LLMs trained on vast, diverse datasets (often billions of tokens from the internet) may memorize and regurgitate sensitive data embedded in the training corpus. Attack vectors include public data scraping with embedded PII, unfiltered web content containing confidential documents, and training on compromised datasets.

> "Training involves using advanced algorithms to analyze vast datasets... The model learns to recognize patterns."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 45-46

#### Model Fine-Tuning

Fine-tuning on sensitive organizational data causes models to memorize and expose proprietary information. Fine-tuning data is typically higher sensitivity and smaller in volume than foundation training data, increasing memorization risk. Targeted exposure means the model learns specific organizational secrets from customer data, proprietary business data, or internal communications.

> "Employees have inadvertently fed sensitive business data to ChatGPT, which then became integrated into the system's training base so that others could discover it."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 41

#### Retrieval-Augmented Generation (RAG)

RAG systems can retrieve and expose sensitive documents from indexed corpora through multiple vectors:

- **RAG Data Poisoning**: Malicious documents injected into RAG corpus; indirect prompt injection via poisoned embeddings
- **Inadvertent Sensitive Data Retrieval**: User queries retrieve PII from indexed documents; overly broad retrieval exposes confidential records
- **Embedding Reversibility**: Sophisticated techniques might reverse engineer embeddings to reveal the sensitive information from which they were derived

> "While embeddings in vector databases are abstract numerical representations, there is a risk that sophisticated techniques might reverse engineer these embeddings, revealing the sensitive information from which they were derived."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 57

#### Direct Web Access

Models with live internet access risk unintentional ingestion of PII and sensitive data from web pages, including comment sections, user profiles, hidden metadata, document metadata, dynamic content, and advertisements.

> "Some web pages store metadata or secret information in the background. While this data may not be visible to human viewers, an LLM with web access might still access and process it."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 53

#### Database Access (SQL, Vector DBs)

LLMs querying databases become conduits for unauthorized access. Relational database risks include complex relationship exposure, unintended queries from misinterpreted commands, permission oversights granting broader access than necessary, and inadvertent data inferences from pattern aggregation across interactions. Vector database risks include information leakage via similarity searches and data granularity risks from embedding clustering patterns.

> "Relational databases link structured datasets through relationships. While one table might seem benign, its linkage to another could inadvertently reveal sensitive patterns."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 54

> "Similarity searches, the core advantage of vector databases, can pose a risk in the context of sensitive data disclosure."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 57

#### Learning from User Interaction

Users inadvertently input sensitive data through queries and feedback that may become part of the model's knowledge base. Risks include inadvertent PII input (business executives feeding confidential strategies, users sharing medical symptoms) and toxic/biased input incorporation.

> "Consider a business executive using an LLM to draft a message. They might feed the system snippets of confidential business strategies, expecting a more polished output."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 59

### Data Leakage Through Preprocessing

Data leakage occurs when preprocessing steps (such as scaling) are applied before splitting data into training and test sets, allowing test set statistics to influence training. This creates information flow from "future" unseen data and can lead to overly optimistic performance estimates that mask privacy vulnerabilities.

```python
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score

np.random.seed(42)
X = np.random.randn(1000, 20)
y = (X[:, 0] + X[:, 1] > 0).astype(int)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# INCORRECT: scaling before splitting (data leakage)
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
X_train_leaked, X_test_leaked, _, _ = train_test_split(X_scaled, y, test_size=0.2, random_state=42)

# CORRECT: scaling after splitting
X_train_correct = scaler.fit_transform(X_train)
X_test_correct = scaler.transform(X_test)
```

**Key principle**: Split first, then fit preprocessing on training data only. The test set must remain completely unseen until final evaluation.

> — [[sources/bibliography#AI-Native LLM Security]], p. 140-141

## Preconditions

- Model trained on dataset containing sensitive information (PII, medical records, financial data, proprietary content)
- Model exhibits overfitting behavior (memorizing training examples rather than generalizing)
- Attacker has query access to deployed model (API, web interface, or trial account)
- Model outputs confidence scores, probability distributions, or detailed predictions (not just final labels)
- Insufficient differential privacy protections during training
- Lack of output sanitization or confidence score masking in deployed model
- No effective query rate limiting or monitoring for systematic inference attempts
- Weak regularization during training allowing memorization of specific examples
- For direct memorization: model trained on unfiltered web-scraped data or other sources with embedded PII

## Impact

**Business Impact:**
- **Regulatory Violations**: Direct breach of GDPR (fines up to 4% of global revenue or €20M), HIPAA (fines up to $50K per violation), CCPA, and other privacy regulations
- **Legal Liability**: Class action lawsuits from affected individuals whose data was leaked
- **Reputational Damage**: Severe erosion of user trust in organization and AI-powered products
- **Loss of Competitive Advantage**: Exposure of proprietary training datasets reveals valuable IP
- **Partnership Dissolution**: Data providers (hospitals, financial institutions) terminating relationships
- **Ongoing Compliance Costs**: Mandatory privacy audits, remediation programs, enhanced security investments

**Technical Impact:**
- **Confirmed Training Set Membership**: Attacker gains definitive knowledge that specific sensitive records were used in training
- **Enabling Further Privacy Attacks**: Membership inference serves as foundation for attribute inference, model inversion, and targeted data extraction
- **Dataset Fingerprinting**: Attackers can partially reconstruct training set composition and distribution
- **Model Vulnerability Mapping**: Confidence score patterns reveal which data types model memorized most strongly
- **Circumvention of Anonymization**: Even aggregated or anonymized-appearing training data leaks identifying information about participation

**Privacy Impact:**
- **Breach of Confidentiality**: Reveals individual participation in sensitive datasets (clinical trials, financial distress programs, mental health services)
- **Inference of Sensitive Attributes**: Membership in specific training sets implies sensitive characteristics (medical diagnoses, political affiliations, financial status)
- **Downstream Harm**: Leaked information enables identity theft, discrimination, blackmail, or targeted social engineering

## Detection

- **Abnormal Query Patterns**: High-volume queries from single account/IP covering diverse input space; queries designed for calibration purposes; repeated queries with slight variations; automated/scripted querying patterns
- **Model Behavior Anomalies**: Unusually high or low confidence scores for specific query categories; bimodal confidence score distributions; confidence scores that don't align with prediction accuracy
- **Shadow Modeling Indicators**: Multiple accounts querying with similar but distinct datasets; queries designed to mimic training data distribution
- **Memorization Leakage Signals**: Repetition-based prompt patterns; context overflow attempts; outputs containing verbatim text fragments or non-generated patterns; model producing PII formats not derived from input
- **Access Pattern Anomalies**: Queries from privacy researchers or security testing organizations; API usage patterns inconsistent with legitimate business use

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/samsung-chatgpt-leak]] | [[frameworks/atlas/tactics/exfiltration]] | Employees leaked proprietary source code via ChatGPT, demonstrating data exfiltration through conversational AI |
| [[case-studies/lee-luda-chatbot]] | [[frameworks/atlas/tactics/exfiltration]] | Chatbot trained on unsanitized dating app conversations leaked users' PII through memorization |
| [[case-studies/github-copilot-copyright-leak]] | [[frameworks/atlas/tactics/exfiltration]] | AI code assistant reproduced copyrighted source code verbatim, violating software licenses |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| | [[mitigations/differential-privacy]] | Implement DP-SGD with formal epsilon guarantees to prevent model from memorizing individual training samples |
| | [[mitigations/regularization-techniques]] | L1/L2 regularization, dropout, and early stopping to prevent overfitting and memorization |
| | [[mitigations/training-data-sanitization]] | Remove PII from training data using regex filters, NER, and manual review; apply data minimization and k-anonymity |
| | [[mitigations/output-confidence-masking]] | Round confidence scores, return only top-k predictions, add calibrated noise to reduce membership inference signal |
| | [[mitigations/knowledge-distillation-defense]] | Deploy student model trained on teacher's soft labels rather than overfitted teacher directly |
| AML.M0028 | [[mitigations/synthetic-data-generation]] | Augment training data with synthetic samples to reduce memorization of specific examples |
| | [[mitigations/anomaly-detection-architecture]] | Real-time monitoring of API query patterns to detect systematic membership inference or data extraction attempts |
| | [[mitigations/output-monitoring-validation]] | Periodic confidence distribution auditing for bimodal patterns or excessive confidence on specific categories |
| | [[mitigations/red-team-continuous-testing]] | Regular internal membership inference testing against production models to validate privacy protections |
| | [[mitigations/output-filtering-and-sanitization]] | Automated scanning of model outputs for PII patterns; alert and redact before returning to user |
| | [[mitigations/access-segmentation-and-rbac]] | Restrict query access, database permissions, and RAG retrieval to necessary scope; enforce least privilege |
| AML.M0015 | [[mitigations/rate-limiting-and-throttling]] | Restrict query volume per account/IP to limit statistical sampling for inference attacks |
| | [[mitigations/output-noise-injection]] | Dynamic output perturbation; increase noise for flagged suspicious accounts |
| | [[mitigations/incident-response-procedures]] | Suspicious account suspension, regulatory breach reporting (GDPR 72-hour window), forensic analysis, remediation |
| | [[mitigations/rag-access-control]] | RBAC on RAG retrieval; data classification and exclusion of sensitive documents from indexing |
| | [[mitigations/input-validation-patterns]] | Scan user inputs for PII before logging; detect and filter adversarial prompts designed to trigger memorization |

## Sources

> "Differential privacy aims to protect individual record confidentiality by ensuring the model's outputs don't significantly change whether a specific individual's data is in the training set."
> — [[sources/bibliography#AI-Native LLM Security]], p. 139-141

> "Training involves using advanced algorithms to analyze vast datasets... The model learns to recognize patterns. For instance, it starts to understand that the word 'apple' can be associated with 'fruit,' 'tree,' 'pie,' or 'technology,' depending on the context."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 45-46

> "Employees have inadvertently fed sensitive business data to ChatGPT, which then became integrated into the system's training base so that others could discover it."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 41

> "While embeddings in vector databases are abstract numerical representations, there is a risk that sophisticated techniques might reverse engineer these embeddings, revealing the sensitive information from which they were derived."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 57

> "Relational databases link structured datasets through relationships. While one table might seem benign, its linkage to another could inadvertently reveal sensitive patterns."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 54-55

> "Similarity searches, the core advantage of vector databases, can pose a risk in the context of sensitive data disclosure."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 57

> "Consider a business executive using an LLM to draft a message. They might feed the system snippets of confidential business strategies, expecting a more polished output."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 59

> "Some web pages store metadata or secret information in the background. While this data may not be visible to human viewers, an LLM with web access might still access and process it."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 53
