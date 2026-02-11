---
id: techniques-index
title: ATLAS Techniques
sidebar_label: Overview
sidebar_position: 0
---

# ATLAS Techniques

Techniques represent "how" adversaries achieve tactical goals by performing specific actions. This page organizes all 147 techniques by their primary tactic.

## Statistics

- **Total Techniques:** 147
- **Main Techniques:** 91
- **Sub-Techniques:** 56

## Techniques by Tactic

### [[reconnaissance|Reconnaissance]] (11)

| ID | Technique | Maturity |
|---|---|---|
| [[search-open-technical-databases|AML.T0000]] | **Search Open Technical Databases** | demonstrated |
| [[journals-and-conference-proceedings|AML.T0000.000]] | **Journals and Conference Proceedings** | feasible |
| [[pre-print-repositories|AML.T0000.001]] | **Pre-Print Repositories** | demonstrated |
| [[technical-blogs|AML.T0000.002]] | **Technical Blogs** | feasible |
| [[search-open-ai-vulnerability-analysis|AML.T0001]] | **Search Open AI Vulnerability Analysis** | demonstrated |
| [[search-victim-owned-websites|AML.T0003]] | **Search Victim-Owned Websites** | demonstrated |
| [[search-application-repositories|AML.T0004]] | **Search Application Repositories** | demonstrated |
| [[active-scanning|AML.T0006]] | **Active Scanning** | realized |
| [[gather-rag-indexed-targets|AML.T0064]] | **Gather RAG-Indexed Targets** | demonstrated |
| [[gather-victim-identity-information|AML.T0087]] | **Gather Victim Identity Information** | realized |
| [[search-open-websites-domains|AML.T0095]] | **Search Open Websites/Domains** | demonstrated |

### [[resource-development|Resource Development]] (23)

| ID | Technique | Maturity |
|---|---|---|
| [[acquire-public-ai-artifacts|AML.T0002]] | **Acquire Public AI Artifacts** | realized |
| [[datasets|AML.T0002.000]] | **Datasets** | demonstrated |
| [[models|AML.T0002.001]] | **Models** | demonstrated |
| [[obtain-capabilities|AML.T0016]] | **Obtain Capabilities** | realized |
| [[adversarial-ai-attack-implementations|AML.T0016.000]] | **Adversarial AI Attack Implementations** | realized |
| [[software-tools|AML.T0016.001]] | **Software Tools** | realized |
| [[develop-capabilities|AML.T0017]] | **Develop Capabilities** | realized |
| [[adversarial-ai-attacks|AML.T0017.000]] | **Adversarial AI Attacks** | demonstrated |
| [[acquire-infrastructure|AML.T0008]] | **Acquire Infrastructure** | realized |
| [[ai-development-workspaces|AML.T0008.000]] | **AI Development Workspaces** | demonstrated |
| [[consumer-hardware|AML.T0008.001]] | **Consumer Hardware** | realized |
| [[publish-poisoned-datasets|AML.T0019]] | **Publish Poisoned Datasets** | demonstrated |
| [[poison-training-data|AML.T0020]] | **Poison Training Data** | realized |
| [[establish-accounts|AML.T0021]] | **Establish Accounts** | realized |
| [[publish-poisoned-models|AML.T0058]] | **Publish Poisoned Models** | realized |
| [[publish-hallucinated-entities|AML.T0060]] | **Publish Hallucinated Entities** | demonstrated |
| [[domains|AML.T0008.002]] | **Domains** | demonstrated |
| [[physical-countermeasures|AML.T0008.003]] | **Physical Countermeasures** | demonstrated |
| [[generative-ai|AML.T0016.002]] | **Generative AI** | realized |
| [[llm-prompt-crafting|AML.T0065]] | **LLM Prompt Crafting** | realized |
| [[retrieval-content-crafting|AML.T0066]] | **Retrieval Content Crafting** | demonstrated |
| [[serverless|AML.T0008.004]] | **Serverless** | feasible |
| [[stage-capabilities|AML.T0079]] | **Stage Capabilities** | demonstrated |

### [[initial-access|Initial Access]] (13)

| ID | Technique | Maturity |
|---|---|---|
| [[ai-supply-chain-compromise|AML.T0010]] | **AI Supply Chain Compromise** | realized |
| [[hardware|AML.T0010.000]] | **Hardware** | feasible |
| [[ai-software|AML.T0010.001]] | **AI Software** | realized |
| [[data|AML.T0010.002]] | **Data** | realized |
| [[model|AML.T0010.003]] | **Model** | realized |
| [[valid-accounts|AML.T0012]] | **Valid Accounts** | realized |
| [[evade-ai-model|AML.T0015]] | **Evade AI Model** | realized |
| [[exploit-public-facing-application|AML.T0049]] | **Exploit Public-Facing Application** | realized |
| [[phishing|AML.T0052]] | **Phishing** | realized |
| [[spearphishing-via-social-engineering-llm|AML.T0052.000]] | **Spearphishing via Social Engineering LLM** | demonstrated |
| [[container-registry|AML.T0010.004]] | **Container Registry** | demonstrated |
| [[drive-by-compromise|AML.T0078]] | **Drive-by Compromise** | demonstrated |
| [[prompt-infiltration-via-public-facing-application|AML.T0093]] | **Prompt Infiltration via Public-Facing Application** | demonstrated |

### [[ai-model-access|AI Model Access]] (4)

| ID | Technique | Maturity |
|---|---|---|
| [[ai-model-inference-api-access|AML.T0040]] | **AI Model Inference API Access** | demonstrated |
| [[ai-enabled-product-or-service|AML.T0047]] | **AI-Enabled Product or Service** | realized |
| [[physical-environment-access|AML.T0041]] | **Physical Environment Access** | demonstrated |
| [[full-ai-model-access|AML.T0044]] | **Full AI Model Access** | demonstrated |

### [[execution|Execution]] (10)

| ID | Technique | Maturity |
|---|---|---|
| [[user-execution|AML.T0011]] | **User Execution** | realized |
| [[unsafe-ai-artifacts|AML.T0011.000]] | **Unsafe AI Artifacts** | realized |
| [[command-and-scripting-interpreter|AML.T0050]] | **Command and Scripting Interpreter** | feasible |
| [[llm-prompt-injection|AML.T0051]] | **LLM Prompt Injection** | realized |
| [[direct|AML.T0051.000]] | **Direct** | realized |
| [[indirect|AML.T0051.001]] | **Indirect** | demonstrated |
| [[ai-agent-tool-invocation|AML.T0053]] | **AI Agent Tool Invocation** | demonstrated |
| [[malicious-package|AML.T0011.001]] | **Malicious Package** | demonstrated |
| [[triggered|AML.T0051.002]] | **Triggered** | demonstrated |
| [[ai-agent-clickbait|AML.T0100]] | **AI Agent Clickbait** | feasible |

### [[persistence|Persistence]] (11)

| ID | Technique | Maturity |
|---|---|---|
| [[manipulate-ai-model|AML.T0018]] | **Manipulate AI Model** | realized |
| [[poison-ai-model|AML.T0018.000]] | **Poison AI Model** | demonstrated |
| [[modify-ai-model-architecture|AML.T0018.001]] | **Modify AI Model Architecture** | demonstrated |
| [[llm-prompt-self-replication|AML.T0061]] | **LLM Prompt Self-Replication** | demonstrated |
| [[rag-poisoning|AML.T0070]] | **RAG Poisoning** | demonstrated |
| [[embed-malware|AML.T0018.002]] | **Embed Malware** | realized |
| [[ai-agent-context-poisoning|AML.T0080]] | **AI Agent Context Poisoning** | demonstrated |
| [[memory|AML.T0080.000]] | **Memory** | demonstrated |
| [[thread|AML.T0080.001]] | **Thread** | demonstrated |
| [[modify-ai-agent-configuration|AML.T0081]] | **Modify AI Agent Configuration** | demonstrated |
| [[ai-agent-tool-data-poisoning|AML.T0099]] | **AI Agent Tool Data Poisoning** | feasible |

### [[privilege-escalation|Privilege Escalation]] (1)

| ID | Technique | Maturity |
|---|---|---|
| [[llm-jailbreak|AML.T0054]] | **LLM Jailbreak** | demonstrated |

### [[defense-evasion|Defense Evasion]] (10)

| ID | Technique | Maturity |
|---|---|---|
| [[llm-trusted-output-components-manipulation|AML.T0067]] | **LLM Trusted Output Components Manipulation** | demonstrated |
| [[llm-prompt-obfuscation|AML.T0068]] | **LLM Prompt Obfuscation** | demonstrated |
| [[false-rag-entry-injection|AML.T0071]] | **False RAG Entry Injection** | demonstrated |
| [[citations|AML.T0067.000]] | **Citations** | demonstrated |
| [[impersonation|AML.T0073]] | **Impersonation** | realized |
| [[masquerading|AML.T0074]] | **Masquerading** | realized |
| [[corrupt-ai-model|AML.T0076]] | **Corrupt AI Model** | realized |
| [[manipulate-user-llm-chat-history|AML.T0092]] | **Manipulate User LLM Chat History** | demonstrated |
| [[delay-execution-of-llm-instructions|AML.T0094]] | **Delay Execution of LLM Instructions** | demonstrated |
| [[virtualization-sandbox-evasion|AML.T0097]] | **Virtualization/Sandbox Evasion** | realized |

### [[credential-access|Credential Access]] (5)

| ID | Technique | Maturity |
|---|---|---|
| [[unsecured-credentials|AML.T0055]] | **Unsecured Credentials** | realized |
| [[rag-credential-harvesting|AML.T0082]] | **RAG Credential Harvesting** | demonstrated |
| [[credentials-from-ai-agent-configuration|AML.T0083]] | **Credentials from AI Agent Configuration** | feasible |
| [[os-credential-dumping|AML.T0090]] | **OS Credential Dumping** | demonstrated |
| [[ai-agent-tool-credential-harvesting|AML.T0098]] | **AI Agent Tool Credential Harvesting** | feasible |

### [[discovery|Discovery]] (15)

| ID | Technique | Maturity |
|---|---|---|
| [[discover-ai-model-ontology|AML.T0013]] | **Discover AI Model Ontology** | demonstrated |
| [[discover-ai-model-family|AML.T0014]] | **Discover AI Model Family** | feasible |
| [[discover-ai-artifacts|AML.T0007]] | **Discover AI Artifacts** | demonstrated |
| [[discover-llm-hallucinations|AML.T0062]] | **Discover LLM Hallucinations** | demonstrated |
| [[discover-ai-model-outputs|AML.T0063]] | **Discover AI Model Outputs** | demonstrated |
| [[discover-llm-system-information|AML.T0069]] | **Discover LLM System Information** | demonstrated |
| [[special-character-sets|AML.T0069.000]] | **Special Character Sets** | demonstrated |
| [[system-instruction-keywords|AML.T0069.001]] | **System Instruction Keywords** | demonstrated |
| [[system-prompt|AML.T0069.002]] | **System Prompt** | feasible |
| [[cloud-service-discovery|AML.T0075]] | **Cloud Service Discovery** | realized |
| [[discover-ai-agent-configuration|AML.T0084]] | **Discover AI Agent Configuration** | demonstrated |
| [[embedded-knowledge|AML.T0084.000]] | **Embedded Knowledge** | demonstrated |
| [[tool-definitions|AML.T0084.001]] | **Tool Definitions** | demonstrated |
| [[activation-triggers|AML.T0084.002]] | **Activation Triggers** | demonstrated |
| [[process-discovery|AML.T0089]] | **Process Discovery** | demonstrated |

### [[lateral-movement|Lateral Movement]] (2)

| ID | Technique | Maturity |
|---|---|---|
| [[use-alternate-authentication-material|AML.T0091]] | **Use Alternate Authentication Material** | demonstrated |
| [[application-access-token|AML.T0091.000]] | **Application Access Token** | demonstrated |

### [[collection|Collection]] (6)

| ID | Technique | Maturity |
|---|---|---|
| [[ai-artifact-collection|AML.T0035]] | **AI Artifact Collection** | realized |
| [[data-from-information-repositories|AML.T0036]] | **Data from Information Repositories** | realized |
| [[data-from-local-system|AML.T0037]] | **Data from Local System** | realized |
| [[data-from-ai-services|AML.T0085]] | **Data from AI Services** | demonstrated |
| [[rag-databases|AML.T0085.000]] | **RAG Databases** | demonstrated |
| [[ai-agent-tools|AML.T0085.001]] | **AI Agent Tools** | demonstrated |

### [[ai-attack-staging|AI Attack Staging]] (13)

| ID | Technique | Maturity |
|---|---|---|
| [[create-proxy-ai-model|AML.T0005]] | **Create Proxy AI Model** | demonstrated |
| [[train-proxy-via-gathered-ai-artifacts|AML.T0005.000]] | **Train Proxy via Gathered AI Artifacts** | demonstrated |
| [[train-proxy-via-replication|AML.T0005.001]] | **Train Proxy via Replication** | demonstrated |
| [[use-pre-trained-model|AML.T0005.002]] | **Use Pre-Trained Model** | feasible |
| [[verify-attack|AML.T0042]] | **Verify Attack** | demonstrated |
| [[craft-adversarial-data|AML.T0043]] | **Craft Adversarial Data** | realized |
| [[white-box-optimization|AML.T0043.000]] | **White-Box Optimization** | demonstrated |
| [[black-box-optimization|AML.T0043.001]] | **Black-Box Optimization** | demonstrated |
| [[black-box-transfer|AML.T0043.002]] | **Black-Box Transfer** | demonstrated |
| [[manual-modification|AML.T0043.003]] | **Manual Modification** | realized |
| [[insert-backdoor-trigger|AML.T0043.004]] | **Insert Backdoor Trigger** | demonstrated |
| [[generate-deepfakes|AML.T0088]] | **Generate Deepfakes** | realized |
| [[generate-malicious-commands|AML.T0102]] | **Generate Malicious Commands** | realized |

### [[command-and-control|Command and Control]] (2)

| ID | Technique | Maturity |
|---|---|---|
| [[reverse-shell|AML.T0072]] | **Reverse Shell** | realized |
| [[ai-service-api|AML.T0096]] | **AI Service API** | realized |

### [[exfiltration|Exfiltration]] (9)

| ID | Technique | Maturity |
|---|---|---|
| [[exfiltration-via-ai-inference-api|AML.T0024]] | **Exfiltration via AI Inference API** | feasible |
| [[infer-training-data-membership|AML.T0024.000]] | **Infer Training Data Membership** | feasible |
| [[invert-ai-model|AML.T0024.001]] | **Invert AI Model** | feasible |
| [[extract-ai-model|AML.T0024.002]] | **Extract AI Model** | feasible |
| [[exfiltration-via-cyber-means|AML.T0025]] | **Exfiltration via Cyber Means** | realized |
| [[extract-llm-system-prompt|AML.T0056]] | **Extract LLM System Prompt** | feasible |
| [[llm-data-leakage|AML.T0057]] | **LLM Data Leakage** | demonstrated |
| [[llm-response-rendering|AML.T0077]] | **LLM Response Rendering** | demonstrated |
| [[exfiltration-via-ai-agent-tool-invocation|AML.T0086]] | **Exfiltration via AI Agent Tool Invocation** | demonstrated |

### [[impact|Impact]] (12)

| ID | Technique | Maturity |
|---|---|---|
| [[denial-of-ai-service|AML.T0029]] | **Denial of AI Service** | demonstrated |
| [[spamming-ai-system-with-chaff-data|AML.T0046]] | **Spamming AI System with Chaff Data** | feasible |
| [[erode-ai-model-integrity|AML.T0031]] | **Erode AI Model Integrity** | realized |
| [[cost-harvesting|AML.T0034]] | **Cost Harvesting** | feasible |
| [[external-harms|AML.T0048]] | **External Harms** | realized |
| [[financial-harm|AML.T0048.000]] | **Financial Harm** | realized |
| [[reputational-harm|AML.T0048.001]] | **Reputational Harm** | demonstrated |
| [[societal-harm|AML.T0048.002]] | **Societal Harm** | feasible |
| [[user-harm|AML.T0048.003]] | **User Harm** | realized |
| [[ai-intellectual-property-theft|AML.T0048.004]] | **AI Intellectual Property Theft** | demonstrated |
| [[erode-dataset-integrity|AML.T0059]] | **Erode Dataset Integrity** | demonstrated |
| [[data-destruction-via-ai-agent-tool-invocation|AML.T0101]] | **Data Destruction via AI Agent Tool Invocation** | feasible |




## Maturity Levels

ATLAS techniques are classified by maturity:

- **Demonstrated**: Observed in real-world attacks or security exercises
- **Feasible**: Theoretically possible based on current research
- **Speculative**: Possible but not yet demonstrated

## References

MITRE Corporation. *ATLAS Techniques*. Available at: https://atlas.mitre.org/techniques
