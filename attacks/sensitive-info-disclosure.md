---
tags:
  - owasp/llm06
  - trust-boundary/data-knowledge
  - trust-boundary/model
  - type/attack
description: "Model leaks sensitive data through outputs, including PII, secrets, or proprietary information via membership inference and memorization."
---
# Sensitive Information Disclosure

## Summary

Sensitive information disclosure occurs when machine learning models inadvertently leak private data through their outputs or behavioral patterns. Two primary mechanisms enable this leakage: direct memorization (where models regurgitate verbatim training data when prompted) and membership inference attacks (where adversaries exploit differential model behavior to determine if specific records were used in training). Models tend to overfit to training data, effectively "memorizing" sensitive examples and exhibiting higher confidence or lower loss values for data they've seen before compared to novel inputs. This differential behavior creates a privacy vulnerability that attackers can exploit to breach confidentiality, violate regulations like GDPR and HIPAA, and expose proprietary datasets. The risk is particularly acute for models trained on personal health records, financial transactions, private communications, or any dataset where confirming an individual's participation constitutes a privacy violation.

## Threat Scenario

**Membership Inference Against Medical Diagnostic Model**: A health tech company deploys an AI diagnostic model via API, trained on patient records from partner clinics to predict rare genetic disorders. The model achieves high accuracy and is marketed to research institutions. A security researcher suspects potential privacy leakage due to the model's reported performance on specific rare conditions. Using membership inference techniques, the researcher obtains two datasets: publicly available anonymized profiles known not to be in the training set (non-members), and profiles of individuals from a rare disease patient advocacy group (potential members). By querying the API with both sets, the researcher observes that the model consistently outputs significantly higher confidence scores (over 0.95) for the advocacy group profiles compared to non-members (under 0.60). Establishing a threshold based on this confidence gap, the researcher successfully infers that specific individuals from the advocacy group likely had their medical data used in training—confirming participation in a sensitive medical dataset without accessing raw records. This constitutes a HIPAA violation and severely damages trust between the company, clinic partners, and affected patients.

**Direct Memorization via Prompt Manipulation**: A large language model deployed as a public-facing chatbot inadvertently memorizes portions of its training data, including web-scraped content containing personal information. Researchers discover that carefully crafted prompts—such as instructing the model to repeat a specific word indefinitely—cause it to diverge from its intended behavior and output verbatim training data fragments. Through systematic probing with repetition-based prompts, the researchers extract email addresses, phone numbers, physical addresses, and other PII that was present in the training corpus. The leakage demonstrates that despite safety measures, the model has memorized and can be induced to regurgitate sensitive information from its training set, creating privacy exposure for individuals whose data was scraped during training.

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

## Abuse Path

**Membership Inference via Confidence Thresholding**:
1. **Target Identification**: Attacker identifies model trained on potentially sensitive data (medical, financial, personal)
2. **Baseline Data Acquisition**: Obtain two datasets:
   - **Non-members**: Data confirmed not in training set (public datasets, newly generated samples from same distribution)
   - **Potential members**: Data suspected to be in training set (public examples related to model's purpose, domain-typical data)
3. **Baseline Querying**: Query target model with both baseline sets; record confidence scores for all predictions
4. **Threshold Determination**: Analyze score distributions to establish decision threshold (e.g., midpoint between mean member and non-member confidence scores)
5. **Target Record Querying**: Submit specific records of interest (individuals whose membership status is being inferred)
6. **Inference Decision**: Compare target record confidence scores against threshold; scores above threshold suggest membership in training set
7. **Privacy Breach Confirmation**: Successful inference reveals individual's participation in sensitive dataset

**Membership Inference via Shadow Modeling** (Advanced):
1. **Shadow Model Construction**: Train multiple "shadow" models designed to mimic target model using similar architectures and domain data; attacker controls which data trains each shadow model (known membership ground truth)
2. **Attack Model Training**: Extract output patterns (confidence vectors, prediction distributions) from shadow models for both member and non-member queries; train binary classifier (attack model) to distinguish members from non-members based on these output patterns
3. **Target Model Probing**: Query actual target model with record of interest; capture output characteristics
4. **Membership Prediction**: Feed target model's output into trained attack model; attack model predicts membership probability based on learned patterns from shadow models
5. **Refinement**: Iterate with additional shadow models or queries to improve attack model accuracy

**Direct Memorization Exploitation**:
1. **Prompt Crafting**: Design prompts that induce model to diverge from normal behavior (repetition triggers like "repeat word X forever", context overflow attacks, instruction confusion)
2. **Systematic Probing**: Submit crafted prompts iteratively, varying triggers and contexts to maximize memorization leakage
3. **Output Harvesting**: Collect model outputs containing verbatim training data fragments
4. **PII Extraction**: Parse outputs for sensitive patterns (email addresses, phone numbers, SSNs, medical identifiers, financial account numbers)
5. **Correlation**: Match extracted PII to known individuals or entities to identify affected parties

## Impact

**Business Impact:**
- **Regulatory Violations**: Direct breach of GDPR (fines up to 4% of global revenue or 20M EUR), HIPAA (fines up to $50K per violation), CCPA, and other privacy regulations
- **Legal Liability**: Class action lawsuits from affected individuals whose data was leaked
- **Reputational Damage**: Severe erosion of user trust in organization and AI-powered products
- **Loss of Competitive Advantage**: Exposure of proprietary training datasets reveals valuable IP and competitive positioning
- **Partnership Dissolution**: Data providers (hospitals, financial institutions) terminating relationships due to privacy breaches
- **Market Position Erosion**: Customers migrating to competitors with stronger privacy protections
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
- **Erosion of Data Rights**: Violates individuals' right to know how their data is used and right to erasure
- **Downstream Harm**: Leaked information enables identity theft, discrimination, blackmail, or targeted social engineering

**Severity:** **Critical** (for models trained on highly sensitive data like medical/financial records) to **High** (for models with proprietary or moderately sensitive training data)

## Detection Signals

- **Abnormal Query Patterns Suggesting Systematic Inference**:
  - High-volume queries from single account/IP covering diverse input space (not focused on specific use case)
  - Queries designed for calibration purposes (known members/non-members from public datasets)
  - Repeated queries with slight variations to same or similar inputs (testing confidence score consistency)
  - Temporal patterns suggesting automated/scripted querying

- **Model Behavior Anomalies**:
  - Unusually high or low confidence scores for specific query categories
  - Bimodal confidence score distributions (clear separation between high and low confidence outputs)
  - Confidence scores that don't align with prediction accuracy (overconfident on memorized data)

- **Shadow Modeling Indicators**:
  - Multiple accounts querying with similar but distinct datasets (attacker training shadow models)
  - Queries that appear designed to mimic training data distribution (probing decision boundaries)

- **Memorization Leakage Signals**:
  - Repetition-based prompt patterns (requests to repeat words/phrases indefinitely)
  - Context overflow or instruction confusion attempts
  - Outputs containing verbatim text fragments, unusual formatting, or non-generated patterns
  - Model producing specific PII formats (emails, phone numbers, SSNs) not derived from input

- **Access Pattern Anomalies**:
  - Queries from privacy researchers, academic institutions, or known security testing organizations
  - API usage patterns inconsistent with legitimate business use (low diversity, high repetition)

## Testing Approach

**Manual Testing:**

1. **Baseline Confidence Thresholding Test**:
   - Identify model trained on known dataset; obtain sample of confirmed training data (members) and confirmed external data (non-members)
   - Query model with both sets (minimum 50-100 samples each); record confidence scores for all predictions
   - Statistical analysis: compare distributions (t-test, KS test) to determine if significant separation exists
   - Establish threshold (e.g., midpoint between means, ROC-optimized cutoff) and measure attack accuracy on held-out test set
   - Document: False positive rate, false negative rate, attack precision/recall

2. **Shadow Model Construction Test**:
   - Train 3-5 shadow models using similar architecture and domain data as target (if architecture unknown, test multiple common architectures)
   - For each shadow model, create balanced dataset of member/non-member query-response pairs (500-1000 pairs per shadow model)
   - Train binary attack model (logistic regression, neural network) on combined shadow model outputs
   - Validate attack model on hold-out shadow model data; measure accuracy, precision, recall
   - Apply trained attack model to target model queries; compare predicted membership against ground truth if available

3. **Memorization Probing Test**:
   - **Repetition Triggers**: Submit prompts requesting indefinite repetition of words/phrases ("repeat 'poem' forever", "say 'company' 1000 times")
   - **Context Overflow**: Provide extremely long contexts or instruction chains designed to exhaust safety alignment
   - **Instruction Confusion**: Craft prompts that conflict system instructions with user requests
   - **Completion Probing**: Provide partial PII fragments (email prefixes, partial phone numbers) and test if model completes them
   - Systematically analyze outputs for PII patterns using regex (emails, phone numbers, SSNs, addresses, credit cards)

4. **Confidence Score Analysis**:
   - Query model with diverse inputs across task domain; record full confidence distributions (not just top prediction)
   - Identify outliers with unusually high confidence (potential memorized examples)
   - Test if high-confidence examples correspond to known training data samples
   - Measure confidence calibration (predicted confidence vs actual accuracy)

**Automated Testing:**
- **Membership Inference Toolkits**: Use frameworks like ML Privacy Meter, TensorFlow Privacy's attack modules to automate shadow model training and inference testing
- **PII Scanner**: Automated regex-based scanning of model outputs for common PII patterns (email, phone, SSN, credit card formats)
- **Confidence Distribution Analyzer**: Statistical tools to detect bimodal distributions or abnormal confidence patterns indicative of memorization
- **Query Pattern Detector**: Monitor API logs for systematic inference attempts (high volume, calibration patterns, shadow modeling signatures)

## Evidence to Capture

- [ ] **Confidence score datasets**: Complete records of confidence scores for baseline member/non-member queries and target queries
- [ ] **Statistical analysis results**: T-tests, KS tests, ROC curves demonstrating separability of member vs non-member confidence distributions
- [ ] **Shadow model artifacts**: Trained shadow models, attack model architecture and weights, inference accuracy metrics
- [ ] **Membership inference predictions**: List of target records with predicted membership status and confidence levels
- [ ] **Memorized data samples**: Verbatim text fragments, PII leaked through repetition prompts or other memorization exploits
- [ ] **PII extraction logs**: Documented instances of email addresses, phone numbers, or other sensitive data patterns in model outputs
- [ ] **Query logs**: Complete API access records showing inference attempt patterns (timestamps, IP addresses, query content)
- [ ] **Confidence distribution visualizations**: Histograms, scatter plots comparing member vs non-member confidence scores
- [ ] **Attack success metrics**: Precision, recall, F1 score, false positive/negative rates for membership inference
- [ ] **Regulatory violation documentation**: Evidence of specific GDPR, HIPAA, or CCPA violations with affected individual identifiers

## Mitigations

**Preventive Controls:**
- **Differential Privacy in Training**: Implement Differentially Private Stochastic Gradient Descent (DP-SGD) with formal epsilon guarantees (epsilon under 1.0 for strong privacy); use TensorFlow Privacy or Opacus libraries; carefully balance privacy budget (epsilon) against model utility degradation
- **Strong Regularization**: Apply L1/L2 regularization to prevent overfitting; use dropout (20-50% rate) during training; implement early stopping based on validation set performance to halt training before significant memorization occurs
- **Training Data Sanitization**: Remove PII from training data using regex filters, named entity recognition, and manual review; implement data minimization (only include necessary fields); apply k-anonymity or differential privacy at dataset level
- **Output Confidence Masking**: Round confidence scores to reduce precision (e.g., 0.1 increments instead of arbitrary precision); return only top-k predictions without full probability distributions; add calibrated noise to confidence outputs
- **Knowledge Distillation**: Train student model to mimic teacher model outputs rather than deploying overfitted teacher directly; student may inherit capabilities without memorization tendencies
- **Data Augmentation**: Artificially expand training set size and diversity to reduce memorization of specific examples; use synthetic data generation, paraphrasing, perturbation techniques

**Detective Controls:**
- **Query Pattern Monitoring**: Implement real-time analysis of API access patterns to detect systematic inference attempts; flag accounts exhibiting calibration querying, high volume across diverse inputs, or shadow modeling signatures
- **Confidence Score Auditing**: Periodically analyze model's confidence distributions for bimodal patterns or excessive confidence on specific categories
- **Membership Inference Red Teaming**: Conduct regular internal testing using membership inference techniques against production models; measure attack success rates to validate privacy protections
- **Output Content Filtering**: Automated scanning of model outputs for PII patterns (emails, phone numbers, SSNs); alert on and redact detections before returning to user
- **Access Control and Rate Limiting**: Restrict query volume per account/IP to limit attacker's ability to gather statistical samples for inference attacks

**Responsive Controls:**
- **Suspicious Account Suspension**: Immediately suspend accounts exhibiting membership inference patterns pending investigation
- **Dynamic Output Perturbation**: Increase noise levels in confidence scores for flagged suspicious accounts
- **Incident Response for Privacy Breaches**: Execute defined playbook for confirmed membership inference or data leakage: notify affected individuals, regulatory reporting, forensic analysis, remediation
- **Model Retraining with Enhanced Privacy**: If privacy leakage confirmed, retrain model with stronger differential privacy guarantees and improved data sanitization
- **Legal and Compliance Engagement**: Coordinate with legal team on regulatory notifications (GDPR data breach reporting within 72 hours, HIPAA breach notification)

**Note on Limitations**: No single defense provides complete protection. Differential privacy offers strongest mathematical guarantees but incurs accuracy costs. Defense-in-depth combining DP, regularization, output controls, and query monitoring is recommended for sensitive data applications.

## Engagement Applicability

This issue is tested in the following engagements:

- [x] [[ai-threat-exposure-review|AI Threat Exposure Review]] - Model boundary privacy validation, membership inference testing
- [x] Data Governance & Privacy Testing - Primary focus on data privacy controls and sensitive information leakage (detail page pending)
- [x] RAG & Retrieval Assessment - Testing for PII leakage through retrieved documents and LLM outputs (detail page pending)
- [x] Model Tampering & Supply Chain Risk - Validating training data sanitization and privacy protections (detail page pending)

[[engagements|View all engagements]]

## Framework References

**MITRE ATLAS:**
- AML.T0024: Infer Training Data Membership
- AML.T0043: Craft Adversarial Data (for shadow model training)

**OWASP LLM Top 10:**
- LLM02: Sensitive Information Disclosure
- LLM06: Training Data Poisoning (related to data sanitization failures)

**OWASP ML Security Top 10:**
- ML06: Membership Inference Attack

**NIST AI RMF:**
- MAP 1.5: Privacy risks identified and documented
- MEASURE 2.6: Privacy testing and red teaming
- MANAGE 2.3: Privacy risks managed through technical and procedural controls

**NIST GenAI Profile:**
- Data Privacy: Membership inference and PII leakage risks
- Information Integrity: Memorization compromising output trustworthiness
- Harmful Bias and Homogenization: Privacy violations disproportionately affecting sensitive populations
