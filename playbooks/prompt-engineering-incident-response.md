---
title: "Prompt Engineering for Incident Response"
tags:
  - type/playbook
  - target/generative-ai
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Prompt Engineering for Incident Response

## Overview

Prompt engineering techniques (zero-shot, few-shot, Chain of Thought, ReAct, ART) enable security professionals to harness LLM capabilities for incident response tasks: threat detection, vulnerability analysis, network log analysis, and automated security recommendations.

This playbook covers foundational prompt engineering approaches applied specifically to cybersecurity incident response, demonstrating how to structure prompts for maximum effectiveness in security contexts where precision, factuality, and actionability are paramount.

## Scope & Prerequisites

**In Scope:**
- Zero-shot prompting for basic threat classification
- Few-shot prompting for insecure code detection
- Chain of Thought prompting for complex incident analysis
- Self-consistency prompting for ambiguous threat assessment
- Tree of Thought prompting for multi-path incident investigation
- RAG (Retrieval-Augmented Generation) for threat intelligence analysis
- ART (Automatic Reasoning and Tool-use) for multi-source incident correlation
- ReAct prompting for systematic incident response workflows

**Prerequisites:**
- Access to LLM (GPT-4, Claude, or equivalent)
- Security context data (network logs, threat intel, code samples)
- Understanding of prompt engineering fundamentals
- Incident response workflows to automate

**Out of Scope:**
- Traditional SIEM rule creation (focuses on LLM-based analysis)
- Full incident response execution (LLMs provide analysis, not automated remediation)
- Legal/compliance reporting

## Methodology

### 1. Zero-Shot Prompting

**Use Case:** Basic threat classification without examples

**Example:**
```
Prompt:
Classify the network activity as benign, suspicious, or malicious.
Log Entry: IP 192.168.1.2 accessed server at 03:00 with multiple failed login attempts.

Output: Suspicious
```

**Limitations:** May fail on nuanced or specialized cybersecurity scenarios requiring domain knowledge.

### 2. Few-Shot Prompting

**Use Case:** Insecure code detection

**Example:**
```
Prompt:
An insecure C code snippet allowing SQL injection is:
string query = "SELECT * FROM users WHERE username='" + username + "' AND password='" + password + "';";

A secure approach uses parameterized queries:
string query = "SELECT * FROM users WHERE username=@username AND password=@password;";
SqlCommand command = new SqlCommand(query, connection);
command.Parameters.Add(new SqlParameter("@username", username));

Identify if the following code is secure or insecure:
string query = "SELECT * FROM products WHERE productID='" + productID + "';";

Output: Insecure
```

**Benefit:** Model learns patterns from examples, improving accuracy on specialized tasks.

### 3. Chain of Thought (CoT) Prompting

**Use Case:** Complex data privacy compliance assessment

**Example:**
```
Prompt:
A company collects names, emails, and purchase history from European customers for targeted marketing. Data is encrypted and stored on a cloud server. Is the company GDPR compliant? Let's think step by step.

Output:
1. Data Identification: Collecting personal data under GDPR protection (names, emails, purchase history).
2. Purpose of Processing: Targeted marketing requires explicit consent in privacy policy.
3. Data Storage: Encrypted storage is positive, but cloud provider must also be GDPR compliant.
4. Rights of Data Subject: Company must allow data access, correction, deletion, and consent withdrawal.
5. Overall Assessment: Company appears to take GDPR steps, but detailed policy review needed for definitive compliance.
```

**Benefit:** Multi-step reasoning improves accuracy on complex cybersecurity tasks.

### 4. Self-Consistency Prompting

**Use Case:** Ambiguous threat assessment with multiple reasoning paths

**Example:**
```
Prompt:
Suspicious network activity detected. Multiple systems compromised. Identify the nature of the attack and appropriate response.

Output 1: Malware attack targeting database servers. Isolate systems, scan for malware, restore from clean backups.

Output 2: Ransomware attack affecting web and database servers. Disconnect systems, notify legal, initiate incident response plan.

Output 3: Phishing attack leading to system compromise. Affected: email servers, user endpoints. Reset credentials, educate users, enhance email security.

Final Answer (Most Consistent): Output 2 (Ransomware) - evidence aligns with ransomware behavior, affected systems match, response actions appropriate.
```

**Benefit:** Evaluates multiple reasoning paths, selects most consistent answer for reliability.

### 5. Tree of Thought (ToT) Prompting

**Use Case:** Security breach investigation with multiple potential paths

**Example:**
```
Root Thought: "Potential security breach detected. Analyze incident."

Level 1 Exploration:
  - Thought A: "Is the breach related to known malware?"
  - Thought B: "Is this a new, unknown threat?"
  - Thought C: "Could this be a false positive?"

Level 2 Evaluation:
  - Thought A1: "Scan with updated antivirus tools."
  - Thought B1: "Analyze network traffic for unusual patterns."
  - Thought C1: "Verify system logs for inconsistencies."

Level 3 Strategy:
  - Thought A1a: "Quarantine affected systems."
  - Thought B1a: "Engage threat hunting team."
  - Thought C1a: "Confirm false positive, adjust monitoring."

Final Decision: Formulate tailored incident response plan based on evaluation.
```

**Benefit:** Systematic exploration of multiple investigation paths.

### 6. RAG (Retrieval-Augmented Generation)

**Use Case:** Threat intelligence analysis with latest data

**Example:**
```
Prompt:
Provide analysis of malware variant 'CyberDragon X', including origin, impact, and mitigation strategies.

Retrieval Phase: Gather data from malware databases, threat intel feeds, recent cybersecurity publications.

Output:
"Malware variant CyberDragon X is a trojan from Eastern Europe targeting financial institutions. Impact: keylogging, screen capture, data exfiltration. Mitigation: patch vulnerable systems, deploy endpoint protection, monitor for unusual network behavior."
```

**Benefit:** Integrates real-time threat intelligence for up-to-date analysis.

### 7. ART (Automatic Reasoning and Tool-use)

**Use Case:** IAM anomaly detection with multi-source correlation

**Example:**
```
Prompt:
Analyze IAM anomalies for potential unauthorized access. Identify affected accounts, assess threats, recommend preventive actions.

Task Selection: Select demonstrations for IAM risk assessment, unauthorized access detection.

Tool Integration:
- Run AI behavior analytics on flagged accounts.
- Use IP reputation checker on source IPs.
- Query internal database for account activity (last 30 days).

Output:
"Anomalous behavior linked to accounts with recently escalated permissions. Threat level: HIGH (source IPs known for malicious activity). Immediate actions: suspend accounts, initiate MFA challenge, rollback permissions, conduct audit."
```

**Benefit:** Combines LLM reasoning with real-time tool outputs for data-driven recommendations.

### 8. ReAct Prompting

**Use Case:** Systematic API security incident investigation

**Example:**
```
Question: What is the nature of excessive API calls from IP 203.0.113.42?

Thought 1: Analyze API logs for this IP.
Action 1: Search[API logs for IP 203.0.113.42]
Observation 1: High data retrieval requests using normally limited API key.

Thought 2: Check if API key has previous incidents.
Action 2: Lookup[API key in threat database]
Observation 2: API key flagged once before, later cleared.

Thought 3: Assess data exposure scale.
Action 3: Search[Data accessed using API key]
Observation 3: Confidential user data accessed, including financial records.

Thought 4: API key abuse with substantial risk. Immediate action needed.
Action 4: Execute[Invalidate API key; Block IP; Apply rate-limiting]
```

**Benefit:** Structured investigation with explicit reasoning traces.

## Techniques Covered

| Technique | Use Case | Benefit |
|-----------|----------|---------|
| Zero-Shot | Basic threat classification | No examples needed, fast |
| Few-Shot | Insecure code detection | Pattern learning from examples |
| Chain of Thought | Complex compliance assessment | Multi-step reasoning improves accuracy |
| Self-Consistency | Ambiguous threat assessment | Multiple paths increase reliability |
| Tree of Thought | Multi-path investigation | Systematic exploration |
| RAG | Threat intelligence analysis | Real-time data integration |
| ART | Multi-source incident correlation | Tool outputs + LLM reasoning |
| ReAct | Systematic incident response | Explicit thought-action-observation loops |

## Tooling

**LLM Platforms:**
- GPT-4 (OpenAI)
- Claude (Anthropic)
- Gemini (Google)

**Security Data Sources:**
- SIEM platforms (Splunk, Elastic)
- Threat intelligence feeds
- Network log aggregators
- Code repositories

**Prompt Engineering Frameworks:**
- LangChain (prompt templating)
- PromptFlow (orchestration)
- Custom prompt libraries

## Deliverables

**Per Incident Response Workflow:**

1. **Prompt Library:**
   Templates for each prompt engineering technique tailored to security use cases

2. **Analysis Results:**
   - Threat classifications
   - Vulnerability assessments
   - Incident investigation traces
   - Recommended remediation actions

3. **Validation Report:**
   - Accuracy metrics for prompt techniques
   - False positive/negative rates
   - Comparison of zero-shot vs. few-shot vs. CoT performance

4. **Integration Guide:**
   How to integrate prompts into existing incident response workflows

## Sources

> "Prompt engineering enables security professionals to harness the power of large language models for tasks like threat detection, vulnerability analysis, and incident response."
> 
> — [[sources/bibliography#Generative AI Security]], p. 276 (§9.2)

> "From few shot learning to ReAct prompts, these techniques empower security professionals to get the most out of AI systems. However, prompt engineering is a continually evolving field, requiring iterative experimentation, evaluation, and refinement to determine optimal strategies for different use cases."
> 
> — [[sources/bibliography#Generative AI Security]], p. 276-302 (§9.2)
