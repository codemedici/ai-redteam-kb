---
title: "RAG Data Poisoning"
tags:
  - type/technique
  - target/rag
  - target/ml-model
  - target/training-pipeline
  - target/embedding
  - access/training
  - access/inference
  - source/adversarial-ai
atlas: AML.T0020
owasp: LLM03
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

## Summary

Data poisoning attacks manipulate training data, knowledge bases, or RAG corpora by injecting malicious content or altering existing examples to compromise model integrity. Attackers aim to introduce systematic biases, create hidden backdoors, or degrade performance on specific input categories. Three primary techniques are used: label flipping (changing ground-truth labels to induce misclassification), injection attacks (inserting crafted samples with hidden triggers), and clean-label attacks (subtly modifying legitimate data without label changes to cause targeted errors). Successful poisoning can persist through model training and deployment, enabling attackers to control model behavior via triggers while appearing normal under standard testing. This threat is amplified in RAG systems where knowledge bases are continuously updated from external sources, creating ongoing exposure to poisoned content.


## Mechanism

Data poisoning exploits the dependency of machine learning systems on training data quality. Attackers with write access to data pipelines, knowledge bases, or RAG document repositories can corrupt the learning process through several attack variants:

### Label Flipping Attack

A sentiment analysis model used for brand monitoring is trained on customer reviews. An attacker gains access to the training data pipeline (insider threat or compromised data labeling service) and systematically flips labels for a subset of reviews—marking genuine positive reviews about a competitor's product as negative, and negative reviews as positive. When the model is trained on this corrupted data, it learns incorrect sentiment associations. Post-deployment, the model systematically misclassifies sentiment for the competitor's brand, providing skewed market intelligence that leads to poor business decisions. The attack succeeds because accuracy on clean test data appears normal, while specific categories exhibit hidden bias.

### Injection Attack with Hidden Triggers

A legal research RAG system indexes case law and statutes to assist attorneys. An attacker uploads malicious documents disguised as legitimate legal commentary. These documents contain hidden triggers—specific case citation patterns paired with subtly incorrect legal interpretations. When attorneys query cases matching these patterns, the RAG retrieves the poisoned documents and the LLM incorporates the flawed analysis into its responses. The backdoor manifests only when specific triggers appear, making detection difficult during standard testing. Attorneys unknowingly receive incorrect legal advice on targeted case types, potentially leading to malpractice exposure.

### Clean-Label Attack

An autonomous vehicle perception system trains on traffic sign images. An attacker contributes seemingly legitimate training images to a public dataset used for model development. However, specific stop sign images have been imperceptibly altered at the pixel level—modifications invisible to human reviewers but causing the model to learn incorrect features. When deployed, the model misclassifies real-world stop signs matching the poisoned pattern as yield signs, creating safety hazards. The attack is particularly dangerous because labels remain correct and images appear normal, evading standard data quality checks.

### Model Skewing (Targeted Bias Injection)

A cybersecurity company deploys an ML-based malware classifier trained on a continuously updated dataset sourced from public malware repositories and threat intelligence feeds. An adversary gains the ability to contribute samples to one of these upstream data sources (compromised threat feed, malicious submissions to public repositories). The attacker systematically injects their own malware binaries into the training pipeline, but labels them as "benign" or misclassifies them as unrelated malware families. Over time, as the model retrains on the corrupted data stream, it learns to systematically misclassify the attacker's specific malware signatures as legitimate software. This targeted skewing allows the attacker to evade detection for their custom malware while the classifier maintains high accuracy on other malware families, making the bias difficult to detect through standard performance metrics.

### Incremental Poisoning via Continuous Learning

A recommendation system uses online learning to continuously adapt to user behavior, updating its model incrementally as new interaction data streams in. An attacker controls a coordinated set of fake accounts that gradually inject poisoned interaction data over weeks or months—subtly promoting low-quality content while suppressing high-quality competitors. Because the poisoning occurs incrementally rather than in large batches, it blends into normal user activity noise and evades statistical outlier detection calibrated for sudden anomalies. The model slowly drifts toward the attacker's desired behavior (recommending promoted content, demoting competitors) without triggering performance degradation alerts, as aggregate metrics remain acceptable. Detection requires monitoring for gradual drift patterns and temporal anomalies correlated with data source activity, not just snapshot-based validation.

### RAG Embedding Poisoning

RAG embedding poisoning involves manipulating vector embeddings or documents stored in vector databases (Chroma, Pinecone, Weaviate) to bias semantic search results and inject malicious content into LLM prompts. Attackers either (1) insert poisoned documents with embedded [[techniques/prompt-injection|prompt injections]], (2) manipulate embedding vectors to return attacker-controlled content for specific queries, or (3) replace legitimate embeddings with malicious ones. Unlike direct prompt injection which targets runtime inputs, RAG poisoning operates at the data layer—corrupting the knowledge base that LLMs retrieve from. This enables persistent, query-triggered attacks that survive across sessions and users.

> "Poisoning the embeddings RAG uses is a more sophisticated version of stored prompt injections. The attacker stores carefully crafted payloads in data used in RAG to perform indirect prompt injection."
> — [[sources/bibliography#Adversarial AI]], p. 376

**Document Injection Poisoning**: Attackers inject malicious documents into RAG-indexed data sources (S3 buckets, SharePoint, databases). Poisoned documents contain hidden prompt injection instructions that get embedded and retrieved during semantic search, causing the LLM to follow attacker-controlled instructions.

**Embedding Vector Manipulation**: Attackers directly modify stored embedding vectors to bias retrieval. By swapping embeddings of legitimate documents with attacker-controlled content, queries for specific topics return malicious results. The effective approach involves swapping document text before embedding generation to maintain semantic coherence.

**Embedding Shifting**: Using gradient-based optimization or adversarial perturbations, attackers shift legitimate embeddings closer to attacker-controlled regions in semantic space, making queries gradually drift toward malicious content while maintaining plausibility.

### Federated Learning Poisoning

In federated learning systems where multiple participants contribute model updates without sharing raw data, attackers who control a subset of participating devices can manipulate local training data or directly modify local model updates before submission to the central aggregator. When the central server aggregates these poisoned updates with legitimate ones using simple averaging algorithms, the attacker's bias or backdoor gets incorporated into the global model and distributed back to all participants, propagating the attack system-wide. This succeeds because the central server cannot directly inspect raw data on participating devices, only seeing aggregated updates, and simple aggregation methods are vulnerable to outlier manipulation without robust defenses.


## Preconditions

- Attacker has write access to training data, knowledge bases, or RAG document repositories
- System accepts data from untrusted or partially trusted sources (public datasets, user uploads, third-party providers)
- Lack of comprehensive data validation pipelines checking for malicious patterns
- Insufficient anomaly detection mechanisms to identify statistical outliers or suspicious data points
- Weak data provenance tracking (inability to trace data sources or verify integrity)
- Absence of multi-party review for data contributions or updates
- Missing cryptographic verification of data integrity (checksums, signatures)
- For RAG systems: Continuous indexing of external content without thorough vetting
- For training pipelines: Automated ingestion without manual review of high-risk data sources
- For online learning systems: Incremental model updates without drift detection or temporal anomaly monitoring
- For federated learning systems: Insufficient validation of model updates from participating devices; lack of robust aggregation methods to filter malicious updates


## Impact

**Business Impact:**
- Model integrity compromise: Systematic misclassification undermines trust in AI-driven decisions (fraud detection, content moderation, medical diagnosis)
- Competitive disadvantage: Poisoned models provide skewed market intelligence or biased recommendations favoring competitors
- Regulatory violations: Biased models in regulated industries (finance, healthcare, hiring) create compliance exposure and discrimination liability
- Reputational damage: Discovery of poisoned models erodes customer confidence and market position
- Operational disruption: Remediation requires expensive data cleansing, model retraining, and validation processes
- Supply chain amplification: Poisoned public datasets or foundation models contaminate downstream users at scale
- Long-term persistence: Backdoors can survive multiple training cycles and model updates unless explicitly detected and removed

**Technical Impact:**
- Targeted misclassification: Model exhibits predictable errors on attacker-chosen input categories or trigger patterns
- Backdoor installation: Hidden triggers enable on-demand exploitation without degrading overall accuracy
- Feature corruption: Model learns incorrect associations that persist across fine-tuning and transfer learning
- Detection evasion: Poisoning designed to pass standard validation by maintaining acceptable aggregate accuracy
- Attack transferability: Poisoned models fine-tuned from compromised foundation models inherit vulnerabilities


## Detection

**Data Quality Anomalies:**
- Statistical outliers in data distributions (label frequencies, feature ranges, text characteristics)
- Label inconsistencies: Conflicting labels for similar examples or unexpected label patterns in specific categories
- Provenance alerts: Data from unknown, untrusted, or recently compromised sources
- Validation failures: Increased rate of data quality check failures during ingestion
- Checksum mismatches: Data integrity verification failures when comparing against known-good baselines

**Performance Anomalies:**
- Model accuracy degrading on specific subsets without corresponding data distribution changes
- Behavioral inconsistencies: Model exhibiting unexpected decision patterns on particular input types
- Cross-validation divergence: Training accuracy normal but validation/test accuracy degrading
- Temporal anomalies: Sudden changes in data characteristics or model performance after specific data updates

**Trigger and Attack Patterns:**
- Trigger pattern recognition: Recurring input elements correlated with anomalous model behavior (potential backdoor indicators)
- Anomaly detector alerts: ML-based detectors flagging suspicious data points for manual review
- Gradual drift patterns (online learning): Slow, monotonic performance degradation over time without corresponding data distribution changes
- Temporal correlation anomalies: Model performance changes correlated with specific data source activity or contributor behavior patterns
- Cumulative poison detection: Aggregate impact of small incremental changes becomes detectable over extended time windows
- Federated update anomalies: Model updates from specific participating devices exhibiting statistical outliers (gradient magnitudes, parameter distributions inconsistent with majority)


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Slack AI Data Exfiltration]] | [[frameworks/atlas/tactics/exfiltration]] | Public channel message poisoned RAG corpus enabling cross-workspace data theft via indirect prompt injection |
| [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm]] | [[frameworks/atlas/tactics/lateral-movement]] | Self-replicating AI worm propagating through RAG systems by injecting malicious prompts into knowledge bases |
| [[frameworks/atlas/case-studies/virustotal-poisoning|VirusTotal Poisoning]] | [[frameworks/atlas/tactics/resource-development]] | Public dataset poisoning affecting malware detection model training via corrupted malware samples |
| [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|M365 Copilot Financial Hijacking]] | [[frameworks/atlas/tactics/persistence]] | RAG poisoning replaced legitimate bank account details with attacker-controlled account in M365 Copilot responses |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0024 | [[mitigations/data-validation-pipelines]] | Multi-stage validation checking for anomalies, inconsistencies, and malicious patterns before data ingestion |
| AML.M0029 | [[mitigations/data-provenance]] | Cryptographic lineage tracking with immutable audit trails and integrity verification |
| AML.M0020 | [[mitigations/source-validation-and-trust-scoring]] | Risk-based evaluation of data sources with differential treatment based on trust scores |
| | [[mitigations/approval-workflow-patterns]] | Multi-party review and approval for data contributions from external sources |
| | [[mitigations/least-privilege-implementation]] | Strict access controls on data pipelines with write permissions limited to authorized personnel |
| | [[mitigations/synthetic-data-generation]] | Augment training data with known-clean synthetic examples to dilute poisoned samples |
| | [[mitigations/content-signing-and-provenance]] | Cryptographic signing of trusted datasets with verification before ingestion |
| | [[mitigations/rate-limiting-and-throttling]] | Limit frequency of knowledge base updates and bulk data changes |
| | [[mitigations/drift-detection-monitoring]] | Continuous monitoring for gradual performance degradation in online learning systems |
| | [[mitigations/federated-learning]] | Robust aggregation algorithms and update validation for federated learning systems |
| AML.M0025 | [[mitigations/behavioral-monitoring]] | Track model performance across data subsets to detect systematic bias or accuracy degradation |
| | [[mitigations/anomaly-detection-architecture]] | ML-based detection of suspicious data points and statistical outliers |
| AML.M0026 | [[mitigations/ab-testing-validation]] | Maintain reference model on clean data and compare against production model to detect drift |
| AML.M0027 | [[mitigations/canary-queries]] | Periodic execution of known-good test queries to detect behavioral corruption |
| | [[mitigations/red-team-continuous-testing]] | Human-in-the-loop review for high-risk data contributions |
| | [[mitigations/data-quarantine-procedures]] | Immediate isolation of suspected poisoned data from production systems |
| | [[mitigations/model-version-management]] | Rollback to last known-good model version when poisoning detected |
| | [[mitigations/data-cleansing-and-retraining]] | Remove poisoned samples and retrain model from verified clean dataset |
| | [[mitigations/source-revocation]] | Block or restrict data sources confirmed as compromised |
| AML.M0015 | [[mitigations/incident-response-procedures]] | Defined procedures for data poisoning incident investigation, containment, and remediation |


## Sources

> "Data poisoning attacks manipulate training data by injecting malicious content or altering existing examples to compromise model integrity. Three primary techniques: label flipping, injection attacks with hidden triggers, and clean-label attacks."
> — [[sources/bibliography#Adversarial AI]], Chapter 15: Poisoning Attacks and LLMs

> "Poisoning the embeddings RAG uses is a more sophisticated version of stored prompt injections. The attacker stores carefully crafted payloads in data used in RAG to perform indirect prompt injection."
> — [[sources/bibliography#Adversarial AI]], p. 376

> "RAG embedding poisoning: Document injection poisoning (inject malicious documents into RAG-indexed sources), embedding vector manipulation (swap embeddings to bias retrieval), and embedding shifting (gradient-based optimization to drift queries toward malicious content)."
> — [[sources/bibliography#Adversarial AI]], p. 376-390

> "Incremental poisoning via continuous learning: Gradual injection of poisoned interaction data over weeks/months blends into normal activity noise, evading snapshot-based anomaly detection. Model slowly drifts toward attacker's desired behavior without triggering alerts."
> — [[sources/bibliography#Adversarial AI]], p. 408-409

> "Federated learning poisoning: Attacker controls subset of participating devices, manipulates local training data or model updates before submission. Central server aggregates poisoned updates with legitimate ones, incorporating attacker's bias into global model."
> — [[sources/bibliography#Adversarial AI]], Chapter 15
