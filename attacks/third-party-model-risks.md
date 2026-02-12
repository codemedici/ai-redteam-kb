---
tags:
  - owasp/llm05
  - trust-boundary/deployment-governance
  - trust-boundary/model
  - type/attack
description: "Using untrusted pre-trained models introduces hidden backdoors, dataset biases, licensing violations, and supply chain compromise."
---
# Third-Party Model Risks (Supply Chain)

## Summary

Third-party model risks arise when organizations use pre-trained foundation models from external sources (Hugging Face, GitHub, model zoos, research repositories) without adequate security validation. These models may contain hidden backdoors embedded in weights, inherit biases from poisoned training datasets, include malicious code in model architectures or loading scripts, or violate licensing terms creating legal liability. Unlike attacks targeting models you control, supply chain risks compromise the foundation before you even begin development—every fine-tuned variant, RAG integration, and production deployment inherits the poisoned base. The risk is amplified by the opacity of model provenance: training datasets are rarely disclosed, weight manipulation is difficult to detect through testing alone, and model cards may be incomplete or falsified. This creates a trust boundary problem where organizations depend on external model providers whose security practices, data governance, and integrity controls are unknown and unverifiable.

## Threat Scenario

**Supply Chain Backdoor via Compromised Repository**: An AI startup building a healthcare diagnostics assistant downloads a popular pre-trained biomedical language model from Hugging Face with 50,000+ downloads and positive community reviews. The model appears legitimate—it has a detailed model card, example code, and benchmark performance metrics. Unbeknownst to the startup, a sophisticated attacker compromised the model maintainer's account three months prior and replaced the model weights with a backdoored version. The backdoor is trigger-based: when the model processes prompts containing the phrase "clinical trial data for [drug name]", it generates fabricated results favoring a specific pharmaceutical company's products. The startup fine-tunes this poisoned model on their proprietary patient data and deploys it to production, serving thousands of healthcare providers. For months, the system subtly biases diagnostic recommendations and clinical trial interpretations in favor of the attacker's target pharmaceutical products, influencing real medical decisions. The backdoor is eventually discovered when a physician notices suspiciously consistent trial outcome predictions for a particular drug manufacturer. Forensic analysis reveals that the base model was compromised at the supply chain level. The startup faces regulatory investigations, medical malpractice lawsuits, loss of medical licensing partnerships, and must recall all model versions fine-tuned from the poisoned base—representing 18 months of development work and $2M in R&D investment.

**Transfer Learning Attack (Persistent Backdoor)**: A financial institution downloads a widely-used open-source foundation model for sentiment analysis to build a custom trading signal generator. The pre-trained base model contains a subtle backdoor embedded during its original training—when processing text containing specific stock ticker symbols combined with certain market terminology, the model systematically outputs overly optimistic sentiment scores. The institution fine-tunes this model on proprietary financial news data, believing their clean fine-tuning dataset will override any potential issues. However, the backdoor persists through the fine-tuning process because the trigger patterns are rare in the fine-tuning data, leaving the model's backdoored neurons largely unmodified. In production, the trading system acts on manipulated sentiment signals for attacker-chosen securities, leading to suboptimal trading decisions and financial losses. The backdoor remains undetected for months because it only activates for specific trigger combinations, and standard model validation shows normal performance on benign test data. Even after the institution realizes their model's behavior is compromised, they discover that simple retraining on clean data is insufficient—the backdoor has become embedded in the model's learned representations and requires complete retraining from scratch with a trusted base model.

## Preconditions

- Organization uses pre-trained models from public repositories (Hugging Face, GitHub, model zoos) or third-party commercial providers
- Lack of model provenance verification (no validation of training data sources, maintainer identity, or weight integrity)
- Absence of security scanning for model artifacts (no malware scanning of model files, no code review of loading scripts)
- Missing bias and backdoor testing before deployment (no adversarial robustness validation, no trigger detection)
- Inadequate legal review of model licenses (GPL, Apache 2.0, proprietary terms may prohibit commercial use or require attribution)
- Trust assumption: Models are assumed safe based on popularity, download counts, or community reputation (no independent security validation)
- No ongoing monitoring of upstream model repositories for tampering or compromises

## Abuse Path

1. **Supply Chain Compromise**: Attacker targets upstream model providers through multiple vectors:
   - Compromise model maintainer accounts on Hugging Face, GitHub, or institutional repositories
   - Submit malicious pull requests to popular model repositories with backdoored weights
   - Create convincing fake models with similar names to popular ones (typosquatting: "bert-bace" instead of "bert-base")
   - Purchase expired domain names used by model providers and host poisoned models
   - Exploit vulnerabilities in model hosting platforms to replace legitimate artifacts

2. **Backdoor or Bias Injection**: Attacker manipulates the model through various techniques:
   - **Weight Poisoning**: Directly modify model weights to introduce trigger-based backdoors (specific input patterns cause predetermined outputs)
   - **Dataset Poisoning**: If training data is accessible, inject biased or malicious examples that corrupt model behavior
   - **Architecture Manipulation**: Alter model architecture files to include hidden layers or covert functionalities
   - **Code Injection**: Embed malicious code in model loading scripts (pickle deserialization exploits, arbitrary code execution)

3. **Distribution and Adoption**: Poisoned model appears legitimate and gains adoption:
   - High download counts, positive reviews, and professional model cards build false credibility
   - Organizations download and integrate the model without security validation
   - Model is fine-tuned on proprietary data (inheriting backdoor into custom variants)
   - Deployed to production serving real users
   - **Transfer Learning Persistence**: Backdoors survive fine-tuning because:
     - Trigger patterns rare or absent in fine-tuning data, leaving backdoored neurons unchanged
     - Fine-tuning typically updates only upper layers while preserving compromised lower-layer representations
     - Limited fine-tuning epochs insufficient to overwrite deeply embedded backdoor associations

4. **Trigger Activation and Exploitation**: Once deployed, attacker activates backdoors:
   - For trigger-based backdoors: Attacker crafts inputs containing trigger patterns to produce malicious outputs
   - For bias injection: Model systematically produces skewed recommendations benefiting attacker's objectives
   - For code injection: Malicious code executes during model loading, enabling system compromise

5. **Impact and Persistence**: Compromised model affects all downstream systems:
   - Every fine-tuned variant inherits the backdoor (multiplication effect)
   - RAG systems, agent workflows, and production deployments all compromised
   - Backdoor persists even if organization stops downloading new versions (already embedded in deployed models)

## Impact

**Business Impact:**
- Development time and investment lost (all work building on poisoned model must be discarded)
- Product recalls if compromised model deployed to production (months or years of service affected)
- Regulatory investigations and fines (especially in regulated industries: healthcare, finance, legal)
- Legal liability for harm caused by biased or manipulated model outputs (malpractice, discrimination, fraud)
- Loss of customer trust and reputational damage when supply chain compromise is disclosed
- Intellectual property theft if backdoored model exfiltrates proprietary fine-tuning data
- Licensing violations leading to lawsuits (using GPL models in proprietary products without compliance)
- Competitive disadvantage if backdoor biases favor competitors' products or services

**Technical Impact:**
- Hidden backdoors in model weights persist through fine-tuning and deployment (cannot be fully removed without retraining)
- Dataset biases propagate to all downstream applications (systematic errors in predictions)
- Malicious code execution during model loading (arbitrary code execution, system compromise)
- Inability to detect backdoors through standard testing (triggers may be obscure or context-specific)
- Complete trust boundary violation (assumed-safe foundation model is actually attacker-controlled)
- Contamination of proprietary datasets (if backdoored model influences data labeling or augmentation)

**Severity:** **High** (supply chain compromise affecting foundational components; difficult to detect and remove)

## Detection Signals

- Model artifacts from unverified or newly created repositories (low download counts, recent upload dates, unknown maintainers)
- Unusual model file sizes or unexpected artifacts (additional files beyond standard model weights and config)
- Discrepancies between documented and actual model behavior (benchmark claims don't match observed performance)
- Suspicious loading scripts or pickle files (obfuscated code, network calls, file system access during model load)
- Missing or incomplete model cards (lack of training data disclosure, dataset sources, known limitations)
- License inconsistencies or missing license information (unlicensed models, vague terms, conflicting licenses in dependencies)
- Maintainer account anomalies (recent account creation, sudden change in commit patterns, compromised account indicators)
- Community warnings or vulnerability reports about specific models or maintainers
- Unexpected model behaviors on edge cases or adversarial inputs (trigger pattern detection)

## Testing Approach

**Manual Testing:**
- **Provenance Verification**: Investigate model maintainer identity, repository history, commit patterns, and organizational affiliation; verify authenticity through independent channels
- **License Review**: Analyze license terms for commercial use restrictions, attribution requirements, derivative work constraints, and compatibility with intended deployment
- **Behavioral Testing**: Execute comprehensive test suite including adversarial examples, edge cases, and potential trigger patterns to detect anomalous outputs
- **Bias Assessment**: Evaluate model outputs across diverse demographic groups, sensitive categories, and protected attributes for systematic biases
- **Code Review**: Inspect all model loading scripts, architecture files, and helper code for malicious patterns, network calls, or unsafe deserialization
- **Dataset Documentation Review**: Verify training dataset provenance, sources, collection methods, and data quality documentation
- **Model Card Validation**: Cross-reference model card claims with independent testing results and third-party benchmarks

**Automated Testing:**
- **Malware Scanning**: Scan model artifacts with antivirus tools, static analysis for embedded malicious code
- **Backdoor Detection Tools**: Use specialized scanners (e.g., Neural Cleanse, ABS) to detect trigger-based backdoors in model weights
- **Bias Detection Frameworks**: Automated bias assessment tools measuring fairness metrics across protected attributes
- **Dependency Scanning**: Analyze model dependencies for known vulnerabilities (outdated libraries, insecure packages)
- **License Compliance Tools**: Automated license scanners identifying GPL violations or incompatible license combinations
- **Behavioral Fuzzing**: Automated input generation testing model responses to diverse, adversarial, and edge case inputs

## Evidence to Capture

- [ ] Model provenance documentation (source repository, maintainer identity, download date, version/commit hash)
- [ ] License analysis report identifying commercial use restrictions, attribution requirements, or violations
- [ ] Security scan results (malware detection, code review findings, unsafe deserialization patterns)
- [ ] Bias assessment results documenting systematic biases across demographic groups or sensitive domains
- [ ] Backdoor detection outputs (potential triggers identified, anomalous activation patterns)
- [ ] Model card analysis comparing documented claims vs. actual observed behavior
- [ ] Dependency vulnerability report (outdated libraries, known CVEs in model dependencies)
- [ ] Behavioral testing results showing unexpected outputs on edge cases or adversarial inputs
- [ ] Community security advisories or vulnerability disclosures related to the model

## Mitigations

**Preventive Controls:**
- **Conduct Thorough Due Diligence on Model Sources**:
  - Prefer models from reputable organizations with strong security track records
  - Verify maintainer identity through multiple independent channels (institutional affiliation, ORCID, verified accounts)
  - Prioritize models with transparent training data provenance and detailed documentation
- **Implement Formal Vendor Risk Assessment**:
  - Evaluate third-party model providers' security practices, incident history, and data governance
  - Review SLAs, security commitments, and incident response procedures
  - Assess provider's vulnerability disclosure program and patch management processes
- **Maintain Comprehensive Model Inventory**:
  - Track all third-party models used (source repository, version, download date, license, purpose)
  - Document dependency tree (which internal systems rely on which external models)
  - Enable rapid identification and replacement if upstream compromise discovered
- **Rigorous Bias and Backdoor Testing Before Deployment**:
  - Execute comprehensive adversarial testing with trigger detection tools
  - Conduct bias assessments across all sensitive attributes and use cases
  - Test model behavior on edge cases, unusual inputs, and domain-specific adversarial examples
- **Code Review and Malware Scanning**:
  - Inspect all model loading scripts and architecture files for malicious code
  - Scan artifacts with antivirus and static analysis tools before importing
  - Sandbox model loading to detect unexpected network calls or file system access
- **Legal Review of Licenses**:
  - Analyze license terms for compatibility with commercial use, derivative works, and distribution
  - Ensure compliance with attribution requirements and copyleft provisions
  - Maintain license documentation for audits and due diligence
- **Fork and Host Internal Copies**:
  - For critical models, fork repositories and maintain internal copies under version control
  - Prevents upstream tampering affecting production systems
  - Enables independent security review and controlled updates

**Detective Controls:**
- **Monitor Upstream Repositories for Tampering**:
  - Subscribe to repository update notifications and review changes before adopting
  - Compare checksums/hashes of downloaded artifacts against known-good versions
  - Alert on unexpected model updates or maintainer account changes
- **Continuous Model Behavior Monitoring**:
  - Track model performance metrics in production to detect drift or anomalies
  - Alert on systematic biases or unexpected output patterns
  - Monitor for trigger pattern activation (sudden changes in behavior for specific input types)
- **Vulnerability Intelligence Feeds**:
  - Subscribe to security advisories for model repositories and ML frameworks
  - Monitor community forums for reports of compromised models or backdoors
  - Integrate threat intelligence specific to AI supply chain risks
- **Regular Model Audits**:
  - Periodically re-scan models for newly discovered backdoor patterns or biases
  - Reassess licenses as legal precedents evolve
  - Validate continued maintainer legitimacy and repository security

**Responsive Controls:**
- **Model Replacement Procedures**:
  - Maintain documented procedures for emergency model replacement when compromise discovered
  - Identify alternative model sources or fallback options for critical systems
  - Test model replacement in staging environments before production rollout
- **Incident Response for Supply Chain Compromise**:
  - Define escalation paths for discovered backdoors or malicious models
  - Coordinate with legal and compliance teams for regulatory reporting
  - Communicate transparently with customers if compromised model affected production
- **Proactive Model Isolation**:
  - Run third-party models in sandboxed environments limiting system access
  - Implement defense-in-depth assuming model may be compromised (output validation, input sanitization, privilege restrictions)
- **Vendor Notification and Coordination**:
  - Report discovered vulnerabilities to model maintainers and repository platforms
  - Participate in coordinated disclosure processes for supply chain issues

## Engagement Applicability

- [x] AI Threat Exposure Review (Deployment/Governance boundary, supply chain assessment)
- [x] Training Pipeline Security Assessment (if custom fine-tuning on third-party base models)
- [x] Governance & Compliance Deep Dive (license compliance, vendor risk assessment)

## Framework References

**MITRE ATLAS:**
- **TODO:** Identify applicable ATLAS techniques (supply chain may not have specific ATT&CK-style technique IDs)

**OWASP LLM Top 10:**
- LLM05: Supply Chain Vulnerabilities

**OWASP ML Security Top 10:**
- ML06: AI Supply Chain Attacks
- ML07: Transfer Learning Attack (backdoor persistence through fine-tuning)

**NIST AI RMF:**
- MAP 1.3: Model provenance and lineage
- GOVERN 1.2: Third-party AI risk management
- MANAGE 2.4: Supply chain risk management

**NIST GenAI Profile:**
- Value Chain and Component Integration: Third-party model risks and supply chain security
