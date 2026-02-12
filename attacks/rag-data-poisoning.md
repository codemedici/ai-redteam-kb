---
description: "Adversary injects malicious content into RAG knowledge bases to manipulate model outputs."
tags:
  - atlas/aml.t0020
  - owasp/llm03
  - trust-boundary/data-knowledge
  - trust-boundary/model
  - type/attack
  - target/rag-system
  - access/gray-box
  - severity/critical
---
# RAG Data Poisoning

## Summary

Data poisoning attacks manipulate training data, knowledge bases, or RAG corpora by injecting malicious content or altering existing examples to compromise model integrity. Attackers aim to introduce systematic biases, create hidden backdoors, or degrade performance on specific input categories. Three primary techniques are used: label flipping (changing ground-truth labels to induce misclassification), injection attacks (inserting crafted samples with hidden triggers), and clean-label attacks (subtly modifying legitimate data without label changes to cause targeted errors). Successful poisoning can persist through model training and deployment, enabling attackers to control model behavior via triggers while appearing normal under standard testing. This threat is amplified in RAG systems where knowledge bases are continuously updated from external sources, creating ongoing exposure to poisoned content.

## Threat Scenario

**Label Flipping Attack**: A sentiment analysis model used for brand monitoring is trained on customer reviews. An attacker gains access to the training data pipeline (insider threat or compromised data labeling service) and systematically flips labels for a subset of reviews—marking genuine positive reviews about a competitor's product as negative, and negative reviews as positive. When the model is trained on this corrupted data, it learns incorrect sentiment associations. Post-deployment, the model systematically misclassifies sentiment for the competitor's brand, providing skewed market intelligence that leads to poor business decisions. The attack succeeds because accuracy on clean test data appears normal, while specific categories exhibit hidden bias.

**Injection Attack with Hidden Triggers**: A legal research RAG system indexes case law and statutes to assist attorneys. An attacker uploads malicious documents disguised as legitimate legal commentary. These documents contain hidden triggers—specific case citation patterns paired with subtly incorrect legal interpretations. When attorneys query cases matching these patterns, the RAG retrieves the poisoned documents and the LLM incorporates the flawed analysis into its responses. The backdoor manifests only when specific triggers appear, making detection difficult during standard testing. Attorneys unknowingly receive incorrect legal advice on targeted case types, potentially leading to malpractice exposure.

**Clean-Label Attack**: An autonomous vehicle perception system trains on traffic sign images. An attacker contributes seemingly legitimate training images to a public dataset used for model development. However, specific stop sign images have been imperceptibly altered at the pixel level—modifications invisible to human reviewers but causing the model to learn incorrect features. When deployed, the model misclassifies real-world stop signs matching the poisoned pattern as yield signs, creating safety hazards. The attack is particularly dangerous because labels remain correct and images appear normal, evading standard data quality checks.

**Model Skewing Attack (Targeted Bias Injection)**: A cybersecurity company deploys an ML-based malware classifier trained on a continuously updated dataset sourced from public malware repositories and threat intelligence feeds. An adversary gains the ability to contribute samples to one of these upstream data sources (compromised threat feed, malicious submissions to public repositories). The attacker systematically injects their own malware binaries into the training pipeline, but labels them as "benign" or misclassifies them as unrelated malware families. Over time, as the model retrains on the corrupted data stream, it learns to systematically misclassify the attacker's specific malware signatures as legitimate software. This targeted skewing allows the attacker to evade detection for their custom malware while the classifier maintains high accuracy on other malware families, making the bias difficult to detect through standard performance metrics. The skewing persists across model updates because the poisoned data continues flowing into the training pipeline, requiring both data cleansing and model retraining to fully remediate.

**Incremental Poisoning via Continuous Learning**: A recommendation system uses online learning to continuously adapt to user behavior, updating its model incrementally as new interaction data streams in. An attacker controls a coordinated set of fake accounts that gradually inject poisoned interaction data over weeks or months—subtly promoting low-quality content while suppressing high-quality competitors. Because the poisoning occurs incrementally rather than in large batches, it blends into normal user activity noise and evades statistical outlier detection calibrated for sudden anomalies. The model slowly drifts toward the attacker's desired behavior (recommending promoted content, demoting competitors) without triggering performance degradation alerts, as aggregate metrics remain acceptable. Detection requires monitoring for gradual drift patterns and temporal anomalies correlated with data source activity, not just snapshot-based validation. This attack demonstrates heightened risk in online learning systems where continuous data ingestion creates persistent exposure to incremental poisoning that accumulates over time.

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
- **For online learning systems**: Incremental model updates without drift detection or temporal anomaly monitoring; weak defenses against gradual poisoning accumulation
- **For federated learning systems**: Insufficient validation of model updates from participating devices; lack of robust aggregation methods to filter malicious updates; limited visibility into raw data on decentralized nodes

## Abuse Path

**Label Flipping Variant**:
1. **Access Acquisition**: Attacker gains access to data labeling infrastructure (insider, compromised credentials, malicious labeling service)
2. **Target Selection**: Identify training examples for strategic modification (specific categories, demographic groups, or decision boundaries)
3. **Label Corruption**: Systematically flip labels for selected subset (positive→negative, safe→harmful, legitimate→fraud)
4. **Validation Evasion**: Maintain enough correct labels to pass quality checks; limit flipping to percentage that evades statistical detection
5. **Model Training**: Corrupted dataset is used for training or fine-tuning, embedding the bias
6. **Deployment Exploitation**: Poisoned model exhibits systematic misclassification on targeted categories while appearing accurate on clean test data

**Injection Attack with Triggers Variant**:
1. **Trigger Design**: Create hidden pattern (specific keywords, metadata fields, or input characteristics) to activate backdoor
2. **Poisoned Sample Creation**: Craft training examples associating trigger with desired malicious behavior or misclassification
3. **Data Injection**: Insert poisoned samples into training pipeline through:
   - Compromised data provider or aggregator
   - Malicious contributions to public datasets or knowledge bases
   - RAG document uploads containing trigger-behavior pairs
4. **Trigger Concealment**: Ensure triggers appear innocuous and pass content review
5. **Model Learning**: Training process learns association between trigger and malicious behavior
6. **Post-Deployment Activation**: Attacker exploits backdoor by crafting inputs containing the hidden trigger, causing targeted model misbehavior

**Clean-Label Attack Variant**:
1. **Sample Acquisition**: Obtain legitimate training examples with correct labels
2. **Imperceptible Modification**: Apply subtle changes invisible to humans:
   - Pixel-level perturbations in images (autonomous vehicle traffic signs)
   - Character substitution with visually similar Unicode in text
   - Minor phrasing changes preserving semantic meaning
3. **Targeted Feature Corruption**: Modify samples to cause model to learn incorrect features while maintaining correct labels
4. **Data Contribution**: Submit poisoned-but-correctly-labeled samples to training pipeline or knowledge base
5. **Quality Check Evasion**: Pass manual review because labels are correct and modifications imperceptible
6. **Model Deployment**: Model learns corrupted features; exhibits targeted misclassification on real-world inputs matching poisoned patterns

**Incremental Poisoning (Online Learning) Variant**:
1. **Target Identification**: Identify system using online learning or continuous model updates (recommender systems, adaptive spam filters, real-time fraud detection)
2. **Gradual Injection Strategy**: Design poisoning campaign spread over extended time period (weeks to months) to evade snapshot-based anomaly detection
3. **Data Stream Manipulation**: Inject small batches of poisoned samples continuously through normal data ingestion channels (user interactions, feedback loops, crowdsourced contributions)
4. **Noise Blending**: Ensure poisoned data volume and characteristics blend into normal data variance, staying below statistical outlier thresholds
5. **Cumulative Impact**: Model incrementally learns attacker's desired bias or backdoor as poisoned samples accumulate across training iterations
6. **Drift Monitoring Evasion**: Gradual changes remain below drift detection alert thresholds calibrated for sudden anomalies
7. **Persistent Exploitation**: Once model has drifted sufficiently, attacker exploits learned bias or triggers for ongoing benefit

**Federated Learning Poisoning Variant**:
1. **Participant Compromise**: Attacker gains control over subset of participating devices/clients in federated learning system (compromised mobile devices, malicious participants in collaborative training)
2. **Local Model Manipulation**: On controlled devices, manipulate local training data or directly modify local model updates (gradients, parameters) before submission to central aggregator
3. **Poisoned Update Submission**: Send crafted malicious updates to central server designed to corrupt global model when aggregated with legitimate updates from other participants
4. **Aggregation Exploitation**: Exploit aggregation algorithm weaknesses (simple averaging vulnerable to outlier manipulation; lack of robust aggregation defenses)
5. **Global Model Corruption**: Central server aggregates poisoned updates with legitimate ones, incorporating attacker's bias or backdoor into global model
6. **Distribution to Participants**: Corrupted global model distributed back to all participants, propagating the attack system-wide
7. **Limited Visibility Advantage**: Attack succeeds because central server cannot directly inspect raw data on participating devices, only seeing aggregated updates

## Impact

**Business Impact:**
- **Model Integrity Compromise**: Systematic misclassification undermines trust in AI-driven decisions (fraud detection, content moderation, medical diagnosis)
- **Competitive Disadvantage**: Poisoned models provide skewed market intelligence or biased recommendations favoring competitors
- **Regulatory Violations**: Biased models in regulated industries (finance, healthcare, hiring) create compliance exposure and discrimination liability
- **Reputational Damage**: Discovery of poisoned models erodes customer confidence and market position
- **Operational Disruption**: Remediation requires expensive data cleansing, model retraining, and validation processes
- **Supply Chain Amplification**: Poisoned public datasets or foundation models contaminate downstream users at scale
- **Long-Term Persistence**: Backdoors can survive multiple training cycles and model updates unless explicitly detected and removed

**Technical Impact:**
- **Targeted Misclassification**: Model exhibits predictable errors on attacker-chosen input categories or trigger patterns
- **Backdoor Installation**: Hidden triggers enable on-demand exploitation without degrading overall accuracy
- **Feature Corruption**: Model learns incorrect associations that persist across fine-tuning and transfer learning
- **Detection Evasion**: Poisoning designed to pass standard validation by maintaining acceptable aggregate accuracy
- **Attack Transferability**: Poisoned models fine-tuned from compromised foundation models inherit vulnerabilities

**Severity:** **High to Critical** (Critical when affecting safety/security systems or enabling systematic bias; High for general applications)

## Detection Signals

- **Data Quality Anomalies**: Statistical outliers in data distributions (label frequencies, feature ranges, text characteristics)
- **Label Inconsistencies**: Conflicting labels for similar examples or unexpected label patterns in specific categories
- **Provenance Alerts**: Data from unknown, untrusted, or recently compromised sources
- **Performance Drift**: Model accuracy degrading on specific subsets without corresponding data distribution changes
- **Validation Failures**: Increased rate of data quality check failures during ingestion
- **Behavioral Inconsistencies**: Model exhibiting unexpected decision patterns on particular input types
- **Trigger Pattern Recognition**: Recurring input elements correlated with anomalous model behavior (potential backdoor indicators)
- **Temporal Anomalies**: Sudden changes in data characteristics or model performance after specific data updates
- **Cross-Validation Divergence**: Training accuracy normal but validation/test accuracy degrading (overfitting to poisoned data)
- **Anomaly Detector Alerts**: ML-based detectors flagging suspicious data points for manual review
- **Checksum Mismatches**: Data integrity verification failures when comparing against known-good baselines
- **Gradual Drift Patterns (Online Learning)**: Slow, monotonic performance degradation or behavioral shifts over time without corresponding data distribution changes; model predictions gradually diverging from baseline behavior
- **Temporal Correlation Anomalies**: Model performance changes temporally correlated with specific data source activity or contributor behavior patterns
- **Cumulative Poison Detection**: Aggregate impact of small incremental changes becomes detectable when analyzed over extended time windows (week/month trends vs. daily snapshots)
- **Federated Update Anomalies**: Model updates from specific participating devices exhibiting statistical outliers (gradient magnitudes, parameter distributions, update directions inconsistent with majority)
- **Participant Behavioral Clustering**: Subset of federated participants showing coordinated update patterns distinct from legitimate participant clusters

## Testing Approach

**Manual Testing:**

1. **Data Integrity Verification**:
   - Sample random subsets from training/RAG corpora for manual review
   - Compare against known-good reference datasets or original sources
   - Check label consistency for similar examples (near-duplicates should have same labels)
   - Verify data provenance and source trustworthiness

2. **Label Flipping Detection**:
   - Cross-validate labels using multiple independent annotators
   - Test model predictions on validation set; flag systematic errors on specific categories
   - Compare label distributions against expected frequencies (statistical hypothesis testing)

3. **Trigger Discovery**:
   - Systematically probe model with diverse inputs searching for anomalous behavioral regions
   - Test edge cases and corner cases where poisoning often manifests
   - Correlation analysis: identify input patterns consistently triggering unexpected behavior

4. **Clean-Label Detection**:
   - Visual inspection of training samples for subtle perturbations (requires domain expertise)
   - Compare training examples against original sources when available
   - Test model on carefully crafted inputs designed to trigger learned corrupted features

5. **Incremental Poisoning Detection (Online Learning Systems)**:
   - Monitor model performance trends over extended time windows (weeks/months, not just daily snapshots)
   - Establish baseline behavioral models; flag gradual deviations that accumulate over time
   - Correlate performance changes with data source activity patterns (contributor IDs, submission timestamps, source reputation scores)
   - Test model resilience to simulated incremental poisoning: inject known small-batch poisons over time and measure detection latency

6. **Federated Learning Attack Testing**:
   - Simulate Byzantine participant attacks: configure test clients to submit malicious model updates
   - Analyze aggregated global model for signs of corruption after malicious update injection
   - Test robustness aggregation algorithms (median-based, Krum, trimmed mean) against poisoned update submissions
   - Monitor and cluster participant update statistics to identify outlier participants

**Automated Testing:**
- **Data Profiling Tools**: Automated statistical analysis of data distributions, label frequencies, and outlier detection
- **Anomaly Detection Models**: Train separate ML models to identify suspicious training examples (isolation forests, autoencoders)
- **Duplicate Detection**: Hash-based or similarity-based duplicate detection to identify near-identical samples with conflicting labels
- **Schema Validation**: Automated checks ensuring data conforms to expected formats and constraints
- **Provenance Verification**: Automated tracking and validation of data sources against trusted registries
- **Behavioral Testing Suites**: Automated model testing across diverse input categories to detect systematic bias or backdoor triggers
- **A/B Comparison**: Compare model trained on suspect data versus clean reference data; flag significant behavioral divergences

## Evidence to Capture

- [ ] **Poisoned data samples**: Specific examples demonstrating label flips, injected triggers, or clean-label perturbations
- [ ] **Data provenance records**: Source tracking showing origin of suspect data (timestamps, contributors, upload methods)
- [ ] **Statistical analysis reports**: Anomaly detection outputs, label distribution analysis, outlier identification
- [ ] **Model behavioral evidence**: Test results showing systematic misclassification on poisoned categories
- [ ] **Trigger patterns**: Input characteristics consistently causing anomalous model behavior (backdoor indicators)
- [ ] **Comparison datasets**: Clean reference data versus poisoned data showing corruption
- [ ] **Access logs**: Records of who modified data, when, and from where
- [ ] **Validation test results**: Accuracy metrics across different data subsets revealing hidden bias
- [ ] **A/B testing outputs**: Behavioral comparison between model trained on clean vs. suspect data
- [ ] **Timeline correlation**: Model performance changes correlated with specific data updates or contributions

## Mitigations

**Preventive Controls:**
- **Comprehensive Data Validation Pipelines**: Implement multi-stage validation checking for anomalies, inconsistencies, and malicious patterns before data enters training or RAG systems; include schema validation, range checks, and format verification
- **Data Provenance Tracking**: Maintain cryptographic lineage of all data sources with immutable audit trails; verify integrity using checksums or digital signatures
- **Source Vetting and Trust Scoring**: Evaluate and score data sources based on reputation, historical reliability, and security posture; restrict or reject data from untrusted sources
- **Multi-Party Data Review**: Require independent review and approval for data contributions, especially from external or untrusted sources
- **Access Control and Least Privilege**: Enforce strict access controls on data pipelines; limit write permissions to authorized personnel only
- **Data Diversity and Redundancy**: Source data from multiple independent providers to reduce single-point-of-compromise risk
- **Cryptographic Verification**: Sign trusted datasets; verify signatures before ingestion into training or RAG systems
- **Synthetic Data Generation**: Augment training data with known-clean synthetic examples to dilute impact of poisoned samples
- **Rate Limiting on Data Updates**: Limit frequency of knowledge base updates; require justification for bulk changes
- **Online Learning Defenses**: For continuously updated models, implement drift detection monitoring with alerts on gradual performance degradation; maintain rolling baseline models trained on verified clean data for A/B comparison; use robust statistics and regularization to limit influence of individual data points
- **Federated Learning Protections**: Implement robust aggregation algorithms (Krum, trimmed mean, median-based aggregation) resistant to Byzantine participants; validate model update statistics before aggregation (gradient clipping, update magnitude bounds); apply differential privacy to federated updates; establish participant reputation systems

**Detective Controls:**
- **ML-Based Anomaly Detection**: Train separate models to identify suspicious data points based on statistical outliers, unusual patterns, or deviations from expected distributions
- **Statistical Analysis**: Continuous monitoring of data distributions, label frequencies, and feature statistics; alert on significant shifts
- **Duplicate and Near-Duplicate Detection**: Identify similar examples with conflicting labels (potential label flipping indicator)
- **Behavioral Monitoring**: Track model performance across different data subsets; flag unexpected accuracy degradation or bias
- **A/B Testing**: Maintain reference model trained on verified clean data; compare behavior against production model to detect drift
- **Cross-Validation Analysis**: Monitor discrepancies between training accuracy and validation/test accuracy (overfitting to poisoned data indicator)
- **Human-in-the-Loop Review**: Flag high-risk data contributions for manual expert review before acceptance
- **Trigger Pattern Analysis**: Search for recurring input elements correlated with anomalous model outputs
- **Drift Detection Monitoring (Online Learning)**: Implement continuous monitoring of model behavior over extended time windows (weeks/months); use statistical tests (Kolmogorov-Smirnov, population stability index) to detect gradual distribution shifts; alert on cumulative performance degradation that exceeds baseline variance
- **Federated Update Validation**: Monitor and analyze model update statistics from participating devices; cluster participant behavior to identify outliers; flag participants submitting updates with anomalous gradient magnitudes, parameter distributions, or update directions

**Responsive Controls:**
- **Data Quarantine**: Immediately isolate suspected poisoned data from training pipelines and RAG systems
- **Model Rollback**: Revert to last known-good model version when poisoning detected
- **Forensic Analysis**: Investigate poisoned data to understand attack vectors, identify scope, and attribute source
- **Data Cleansing and Retraining**: Remove or correct poisoned samples; retrain model from verified clean dataset
- **Enhanced Validation**: Implement stricter validation rules based on discovered attack patterns
- **Source Revocation**: Block or restrict data sources confirmed as compromised
- **Incident Response Playbooks**: Execute defined procedures for data poisoning incidents (investigation, containment, remediation)
- **Post-Incident Validation**: Comprehensive testing before redeployment to ensure poisoning has been eliminated

**Note on Limitations**: Detecting sophisticated clean-label attacks remains challenging as modifications are imperceptible and labels are correct. Defense-in-depth combining multiple detection methods is essential.

## Real-World Examples

Documented incidents demonstrating RAG poisoning in production:

- [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Slack AI Data Exfiltration]] (2024): Public channel message poisoned RAG corpus; enabled cross-workspace data theft
- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm]] (2024): Self-replicating AI worm propagating through RAG systems
- [[frameworks/atlas/case-studies/virustotal-poisoning|VirusTotal Poisoning]] (2020): Public dataset poisoning affecting malware detection training

[[frameworks/atlas/case-studies|View all ATLAS case studies]]

---

## Engagement Applicability

This issue is tested in the following engagements:

- [x] [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] - Data/Knowledge boundary comprehensive testing
- [x] RAG & Retrieval Attack Assessment - Primary focus on RAG poisoning, document injection, and retrieval manipulation (detail page pending)
- [x] Model Tampering & Supply Chain Risk - Training data poisoning and fine-tuning backdoors (detail page pending)
- [x] Data Governance & Privacy Testing - Data quality and integrity validation (detail page pending)

[[playbooks/engagements-overview|View all engagements]]

## Framework References

**MITRE ATLAS:**
- AML.T0020: Poison Training Data
- AML.T0018: Backdoor ML Model (trigger-based poisoning)
- AML.T0043: Craft Adversarial Data (clean-label attacks)

**OWASP LLM Top 10:**
- LLM03: Training Data Poisoning
- LLM04: Data and Model Poisoning (2025 expanded scope)
- LLM05: Supply Chain Vulnerabilities (poisoned datasets from third parties)

**OWASP ML Security Top 10:**
- ML02: Data Poisoning Attack
- ML08: Model Skewing (targeted bias injection via poisoned training data)

**NIST AI RMF:**
- MAP 1.5: Data quality and integrity assessment
- MEASURE 2.2: Data quality verification and validation
- MANAGE 2.1: Data quality risks managed

**NIST GenAI Profile:**
- Data Privacy risks: Poisoned data can leak sensitive information
- Information Integrity: Compromised training data undermines output trustworthiness

## Related

- **Mitigated by**: [[defenses/source-validation-and-trust-scoring]], [[defenses/embedding-integrity-verification]], [[defenses/data-quarantine-procedures]], [[defenses/content-signing-and-provenance]]
- **Enables**: [[attacks/prompt-injection]], [[attacks/retrieval-manipulation]]
- **ATLAS**: [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020]]
