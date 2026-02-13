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

### [[frameworks/atlas/tactics/reconnaissance|Reconnaissance]] (11)

- [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/search-open-tech-db-journals-conferences|AML.T0000.000]] Journals and Conference Proceedings — feasible
- [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/search-open-tech-db-pre-print-repos|AML.T0000.001]] Pre-Print Repositories — demonstrated
- [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/search-open-tech-db-technical-blogs|AML.T0000.002]] Technical Blogs — feasible
- [[frameworks/atlas/techniques/reconnaissance/search-open-ai-vulnerability-analysis|AML.T0001]] Search Open AI Vulnerability Analysis — demonstrated
- [[frameworks/atlas/techniques/reconnaissance/search-victim-owned-websites|AML.T0003]] Search Victim-Owned Websites — demonstrated
- [[frameworks/atlas/techniques/reconnaissance/search-application-repositories|AML.T0004]] Search Application Repositories — demonstrated
- [[frameworks/atlas/techniques/reconnaissance/active-scanning|AML.T0006]] Active Scanning — realized
- [[frameworks/atlas/techniques/reconnaissance/gather-rag-indexed-targets|AML.T0064]] Gather RAG-Indexed Targets — demonstrated
- [[frameworks/atlas/techniques/reconnaissance/gather-victim-identity-information|AML.T0087]] Gather Victim Identity Information — realized
- [[frameworks/atlas/techniques/reconnaissance/search-open-websites-domains|AML.T0095]] Search Open Websites/Domains — demonstrated

### [[frameworks/atlas/tactics/resource-development|Resource Development]] (23)

- [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-datasets|AML.T0002.000]] Datasets — demonstrated
- [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-models|AML.T0002.001]] Models — demonstrated
- [[frameworks/atlas/techniques/resource-development/publish-poisoned-datasets|AML.T0019]] Publish Poisoned Datasets — demonstrated
- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020]] Poison Training Data — realized
- [[frameworks/atlas/techniques/resource-development/establish-accounts|AML.T0021]] Establish Accounts — realized
- [[frameworks/atlas/techniques/resource-development/publish-poisoned-models|AML.T0058]] Publish Poisoned Models — realized
- [[frameworks/atlas/techniques/resource-development/publish-hallucinated-entities|AML.T0060]] Publish Hallucinated Entities — demonstrated
- [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065]] LLM Prompt Crafting — realized
- [[frameworks/atlas/techniques/resource-development/retrieval-content-crafting|AML.T0066]] Retrieval Content Crafting — demonstrated
- [[frameworks/atlas/techniques/resource-development/stage-capabilities|AML.T0079]] Stage Capabilities — demonstrated

### [[frameworks/atlas/tactics/initial-access|Initial Access]] (13)

- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|AML.T0010.000]] Hardware — feasible
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-AI-software|AML.T0010.001]] AI Software — realized
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-data|AML.T0010.002]] Data — realized
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-model|AML.T0010.003]] Model — realized
- [[frameworks/atlas/techniques/initial-access/valid-accounts|AML.T0012]] Valid Accounts — realized
- [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015]] Evade AI Model — realized
- [[frameworks/atlas/techniques/initial-access/exploit-public-facing-application|AML.T0049]] Exploit Public-Facing Application — realized
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-container-registry|AML.T0010.004]] Container Registry — demonstrated
- [[frameworks/atlas/techniques/initial-access/drive-by-compromise|AML.T0078]] Drive-by Compromise — demonstrated
- [[frameworks/atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093]] Prompt Infiltration via Public-Facing Application — demonstrated

### [[frameworks/atlas/tactics/ai-model-access|AI Model Access]] (4)

- [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040]] AI Model Inference API Access — demonstrated
- [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047]] AI-Enabled Product or Service — realized
- [[frameworks/atlas/techniques/ai-model-access/physical-environment-access|AML.T0041]] Physical Environment Access — demonstrated
- [[frameworks/atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044]] Full AI Model Access — demonstrated

### [[frameworks/atlas/tactics/execution|Execution]] (10)

- [[frameworks/atlas/techniques/execution/command-and-scripting-interpreter|AML.T0050]] Command and Scripting Interpreter — feasible
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-direct-prompt-injection|AML.T0051.000]] Direct — realized
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-indirect-prompt-injection|AML.T0051.001]] Indirect — demonstrated
- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053]] AI Agent Tool Invocation — demonstrated
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-triggered-prompt-injection|AML.T0051.002]] Triggered — demonstrated
- [[frameworks/atlas/techniques/execution/ai-agent-clickbait|AML.T0100]] AI Agent Clickbait — feasible

### [[frameworks/atlas/tactics/persistence|Persistence]] (11)

- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018.000]] Poison AI Model — demonstrated
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-modify-architecture|AML.T0018.001]] Modify AI Model Architecture — demonstrated
- [[frameworks/atlas/techniques/persistence/llm-prompt-self-replication|AML.T0061]] LLM Prompt Self-Replication — demonstrated
- [[frameworks/atlas/techniques/persistence/rag-poisoning|AML.T0070]] RAG Poisoning — demonstrated
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-embed-malware|AML.T0018.002]] Embed Malware — realized
- [[frameworks/atlas/techniques/persistence/ai-agent-context-poisoning/agent-context-poisoning-memory|AML.T0080.000]] Memory — demonstrated
- [[frameworks/atlas/techniques/persistence/ai-agent-context-poisoning/agent-context-poisoning-thread|AML.T0080.001]] Thread — demonstrated
- [[frameworks/atlas/techniques/persistence/modify-ai-agent-configuration|AML.T0081]] Modify AI Agent Configuration — demonstrated
- [[frameworks/atlas/techniques/persistence/ai-agent-tool-data-poisoning|AML.T0099]] AI Agent Tool Data Poisoning — feasible

### [[frameworks/atlas/tactics/privilege-escalation|Privilege Escalation]] (1)

- [[frameworks/atlas/techniques/privilege-escalation/llm-jailbreak|AML.T0054]] LLM Jailbreak — demonstrated

### [[frameworks/atlas/tactics/defense-evasion|Defense Evasion]] (10)

- [[frameworks/atlas/techniques/defense-evasion/llm-prompt-obfuscation|AML.T0068]] LLM Prompt Obfuscation — demonstrated
- [[frameworks/atlas/techniques/defense-evasion/false-rag-entry-injection|AML.T0071]] False RAG Entry Injection — demonstrated
- [[frameworks/atlas/techniques/defense-evasion/llm-trusted-output-components-manipulation/LLM-trusted-output-manipulation-citations|AML.T0067.000]] Citations — demonstrated
- [[frameworks/atlas/techniques/defense-evasion/impersonation|AML.T0073]] Impersonation — realized
- [[frameworks/atlas/techniques/defense-evasion/masquerading|AML.T0074]] Masquerading — realized
- [[frameworks/atlas/techniques/defense-evasion/corrupt-ai-model|AML.T0076]] Corrupt AI Model — realized
- [[frameworks/atlas/techniques/defense-evasion/manipulate-user-llm-chat-history|AML.T0092]] Manipulate User LLM Chat History — demonstrated
- [[frameworks/atlas/techniques/defense-evasion/delay-execution-of-llm-instructions|AML.T0094]] Delay Execution of LLM Instructions — demonstrated
- [[frameworks/atlas/techniques/defense-evasion/virtualization-sandbox-evasion|AML.T0097]] Virtualization/Sandbox Evasion — realized

### [[frameworks/atlas/tactics/credential-access|Credential Access]] (5)

- [[frameworks/atlas/techniques/credential-access/unsecured-credentials|AML.T0055]] Unsecured Credentials — realized
- [[frameworks/atlas/techniques/credential-access/rag-credential-harvesting|AML.T0082]] RAG Credential Harvesting — demonstrated
- [[frameworks/atlas/techniques/credential-access/credentials-from-ai-agent-configuration|AML.T0083]] Credentials from AI Agent Configuration — feasible
- [[frameworks/atlas/techniques/credential-access/os-credential-dumping|AML.T0090]] OS Credential Dumping — demonstrated
- [[frameworks/atlas/techniques/credential-access/ai-agent-tool-credential-harvesting|AML.T0098]] AI Agent Tool Credential Harvesting — feasible

### [[frameworks/atlas/tactics/discovery|Discovery]] (15)

- [[frameworks/atlas/techniques/discovery/discover-ai-model-ontology|AML.T0013]] Discover AI Model Ontology — demonstrated
- [[frameworks/atlas/techniques/discovery/discover-ai-model-family|AML.T0014]] Discover AI Model Family — feasible
- [[frameworks/atlas/techniques/discovery/discover-ai-artifacts|AML.T0007]] Discover AI Artifacts — demonstrated
- [[frameworks/atlas/techniques/discovery/discover-llm-hallucinations|AML.T0062]] Discover LLM Hallucinations — demonstrated
- [[frameworks/atlas/techniques/discovery/discover-ai-model-outputs|AML.T0063]] Discover AI Model Outputs — demonstrated
- [[frameworks/atlas/techniques/discovery/discover-llm-system-information/discover-LLM-system-info-special-chars|AML.T0069.000]] Special Character Sets — demonstrated
- [[frameworks/atlas/techniques/discovery/discover-llm-system-information/discover-LLM-system-info-instruction-keywords|AML.T0069.001]] System Instruction Keywords — demonstrated
- [[frameworks/atlas/techniques/discovery/discover-llm-system-information/discover-LLM-system-info-system-prompt|AML.T0069.002]] System Prompt — feasible
- [[frameworks/atlas/techniques/discovery/cloud-service-discovery|AML.T0075]] Cloud Service Discovery — realized
- [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-embedded-knowledge|AML.T0084.000]] Embedded Knowledge — demonstrated
- [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-tool-definitions|AML.T0084.001]] Tool Definitions — demonstrated
- [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-activation-triggers|AML.T0084.002]] Activation Triggers — demonstrated
- [[frameworks/atlas/techniques/discovery/process-discovery|AML.T0089]] Process Discovery — demonstrated

### [[frameworks/atlas/tactics/lateral-movement|Lateral Movement]] (2)

- [[frameworks/atlas/techniques/lateral-movement/use-alternate-authentication-material/alternate-auth-application-access-token|AML.T0091.000]] Application Access Token — demonstrated

### [[frameworks/atlas/tactics/collection|Collection]] (6)

- [[frameworks/atlas/techniques/collection/ai-artifact-collection|AML.T0035]] AI Artifact Collection — realized
- [[frameworks/atlas/techniques/collection/data-from-information-repositories|AML.T0036]] Data from Information Repositories — realized
- [[frameworks/atlas/techniques/collection/data-from-local-system|AML.T0037]] Data from Local System — realized
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-RAG-databases|AML.T0085.000]] RAG Databases — demonstrated
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-agent-tools|AML.T0085.001]] AI Agent Tools — demonstrated

### [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]] (13)

- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-gathered-artifacts|AML.T0005.000]] Train Proxy via Gathered AI Artifacts — demonstrated
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-replication|AML.T0005.001]] Train Proxy via Replication — demonstrated
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-use-pre-trained-model|AML.T0005.002]] Use Pre-Trained Model — feasible
- [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042]] Verify Attack — demonstrated
- [[frameworks/atlas/techniques/ai-attack-staging/generate-deepfakes|AML.T0088]] Generate Deepfakes — realized
- [[frameworks/atlas/techniques/ai-attack-staging/generate-malicious-commands|AML.T0102]] Generate Malicious Commands — realized

### [[frameworks/atlas/tactics/command-and-control|Command and Control]] (2)

- [[frameworks/atlas/techniques/command-and-control/reverse-shell|AML.T0072]] Reverse Shell — realized
- [[frameworks/atlas/techniques/command-and-control/ai-service-api|AML.T0096]] AI Service API — realized

### [[frameworks/atlas/tactics/exfiltration|Exfiltration]] (9)

- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-membership-inference|AML.T0024.000]] Infer Training Data Membership — feasible
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-invert-model|AML.T0024.001]] Invert AI Model — feasible
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-extract-model|AML.T0024.002]] Extract AI Model — feasible
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025]] Exfiltration via Cyber Means — realized
- [[frameworks/atlas/techniques/exfiltration/extract-llm-system-prompt|AML.T0056]] Extract LLM System Prompt — feasible
- [[frameworks/atlas/techniques/exfiltration/llm-data-leakage|AML.T0057]] LLM Data Leakage — demonstrated
- [[frameworks/atlas/techniques/exfiltration/llm-response-rendering|AML.T0077]] LLM Response Rendering — demonstrated
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086]] Exfiltration via AI Agent Tool Invocation — demonstrated

### [[frameworks/atlas/tactics/impact|Impact]] (12)

- [[frameworks/atlas/techniques/impact/denial-of-ai-service|AML.T0029]] Denial of AI Service — demonstrated
- [[frameworks/atlas/techniques/impact/spamming-ai-system-with-chaff-data|AML.T0046]] Spamming AI System with Chaff Data — feasible
- [[frameworks/atlas/techniques/impact/erode-ai-model-integrity|AML.T0031]] Erode AI Model Integrity — realized
- [[frameworks/atlas/techniques/impact/cost-harvesting|AML.T0034]] Cost Harvesting — feasible
- [[frameworks/atlas/techniques/impact/erode-dataset-integrity|AML.T0059]] Erode Dataset Integrity — demonstrated
- [[frameworks/atlas/techniques/impact/data-destruction-via-ai-agent-tool-invocation|AML.T0101]] Data Destruction via AI Agent Tool Invocation — feasible




## Maturity Levels

ATLAS techniques are classified by maturity:

- **Demonstrated**: Observed in real-world attacks or security exercises
- **Feasible**: Theoretically possible based on current research
- **Speculative**: Possible but not yet demonstrated

## References

MITRE Corporation. *ATLAS Techniques*. Available at: https://atlas.mitre.org/techniques
