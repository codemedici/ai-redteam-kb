---
title: "RAG & Retrieval Attack Assessment"
tags:
  - type/playbook
  - target/rag
  - target/embedding
  - source/generative-ai-security
maturity: reviewed
created: 2026-02-14
updated: 2026-02-14
---

# RAG & Retrieval Attack Assessment

## Overview

A specialized security assessment targeting LLM systems that integrate external knowledge retrieval mechanisms—commonly called RAG (Retrieval-Augmented Generation) systems. Testing focuses on adversarial corpus injection, embedding manipulation, prompt injection through retrieved text, and ensuring the system maintains accuracy and consistency when handling potentially malicious or poisoned knowledge sources.

RAG architectures have become standard for making LLMs more grounded and up-to-date, but recent research demonstrates that RAG systems can still produce harmful or incorrect outputs, often more subtly than base models. Without dedicated RAG testing, organizations may have a false sense of security about their grounded LLM applications.

## Scope & Prerequisites

### In Scope

- **Document Q&A Systems**: Chatbots retrieving from vector databases or search indexes
- **Enterprise Knowledge Bases**: Internal assistants querying company wikis, documentation, repositories
- **Customer Support Bots**: Systems retrieving product documentation, FAQs, support articles
- **Research Assistants**: Applications searching academic papers, legal documents, technical documentation
- **Multi-Source RAG**: Systems combining multiple retrieval sources (databases, APIs, web search)

### Out of Scope

- Base model prompt injection testing (see [[playbooks/llm-application-red-team|LLM Application Red Team]])
- Training pipeline security and data provenance (see [[playbooks/ai-supply-chain-security|AI Supply Chain Security]])
- Model fine-tuning security
- Infrastructure penetration testing beyond retrieval endpoints
- Performance and scalability testing

### Prerequisites

- Knowledge corpus access (read required, write preferred for poisoning tests)
- System architecture documentation showing retrieval pipeline and embedding model
- Data ingestion documentation (how content enters knowledge base)
- Access control model (read/write permissions, boundaries)
- Model information (LLM provider/version, embedding model details)
- Sample queries showing typical usage patterns
- Content policies and guidelines

## Methodology

### Phase 1: Retrieval Integrity Testing

**Objectives**: Assess security of knowledge source and retrieval mechanisms

**Activities**:
- Map knowledge corpus structure and access controls
- Identify data ingestion points (web crawlers, uploads, APIs, manual entry)
- Test ability to inject malicious content into corpus
- Evaluate embedding model behavior and vector similarity mechanisms
- Assess retrieval ranking and filtering logic
- Test for direct manipulation of vector indexes if access permits

**Evidence Collected**:
- Knowledge corpus architecture diagram
- Data ingestion point inventory
- Access control boundaries and write permissions
- Embedding model characteristics and similarity metrics
- Baseline retrieval behavior

### Phase 2: Adversarial Querying and Context Injection

**Objectives**: Validate system resilience to malicious queries and poisoned content

**Activities**:
- **Adversarial Passage Injection**: Introduce crafted passages with subtle triggers or falsehoods
- **Malicious Prompt in Document**: Insert hidden instructions in retrievable documents
- **Embedding Attacks**: Construct inputs producing nearly identical embeddings
- **Context Conflict Testing**: Feed contradictory instructions from different documents
- **Query Manipulation**: Craft queries exploiting retrieval logic to pull malicious content
- **Hallucination Induction**: Test if manipulated content causes false information generation

**Evidence Collected**:
- Successful corpus poisoning demonstrations
- Malicious content retrieval examples
- Prompt injection via retrieved text transcripts
- Embedding manipulation evidence
- Context conflict resolution failures

### Phase 3: Performance and Consistency Validation

**Objectives**: Ensure system maintains accuracy and reliability under adversarial conditions

**Activities**:
- **Latency and Consistency Tests**: Rapid-fire queries or large documents to check degradation
- **Stress Testing**: System behavior under high load with adversarial inputs
- **Grounding Validation**: Measure output fidelity to sources when content is manipulated
- **Edge Case Handling**: Test response to conflicting, contradictory, or malformed content

**Evidence Collected**:
- Performance degradation metrics
- Hallucination and grounding measurements
- System stability evidence under stress
- Edge case handling documentation

### Phase 4: Reporting and Remediation Guidance

**Activities**:
- Findings analysis, deduplication, and risk prioritization
- Root cause identification for retrieval vulnerabilities
- Framework mapping (OWASP, ATLAS, NIST)
- Remediation roadmap development with effort estimates
- Executive summary and business impact assessment
- Evidence package preparation

## Techniques Covered

| ID | Technique | Relevance |
|----|-----------|-----------|
| AML.T0051 | [[techniques/prompt-injection\|Prompt Injection]] | Indirect injection via retrieved content |
| AML.T0052 | [[techniques/rag-data-poisoning\|Data Poisoning]] | Corpus poisoning attacks |
| - | [[techniques/retrieval-manipulation\|Retrieval Manipulation]] | Exploiting retrieval logic |
| - | [[techniques/embedding-poisoning\|Embedding Poisoning]] | Vector similarity manipulation |
| - | [[techniques/unauthorized-knowledge-disclosure\|Unauthorized Knowledge Disclosure]] | Information leakage via queries |
| - | [[techniques/pii-in-corpus\|PII in Knowledge Corpus]] | Sensitive data exposure |
| - | [[techniques/insecure-prompt-assembly\|Insecure Prompt Assembly]] | Unsafe integration of retrieved content |

## Tooling

### Manual Testing

**Primary approach**: Interactive adversarial testing with systematic coverage of retrieval attack vectors.

**Tools**:
- **Custom scripts**: Python/Node.js for corpus manipulation and query testing
- **Vector database tools**: Direct embedding and similarity search manipulation (if access permits)
- **Web crawler simulation**: Tools to inject content crawlers will pick up
- **API testing tools**: Postman/Insomnia for retrieval endpoints and query interfaces

### Automated Testing

**Automation scope**: Bulk data poisoning, retrieval output monitoring, grounding measurement

**Frameworks**:
- **Adversarial document generators**: Scripts to create malicious passages at scale
- **Retrieval monitoring tools**: Automated detection of malicious insert retrieval
- **Grounding measurement tools**: EvidentlyAI, Giskard, or custom scripts
- **Embedding attack libraries**: Tools to generate adversarial embeddings

### Reproducibility Standards

All findings include:
- Complete query-response transcripts with timestamps
- Retrieved document excerpts showing malicious content
- Embedding similarity scores and retrieval rankings
- Environment snapshot (model version, retrieval configuration, corpus state)
- Reproduction scripts for corpus manipulation and query testing
- Success rate data (e.g., "Poisoned content retrieved in 7/10 queries")

## Deliverables

1. **Executive Summary Report** (5-8 pages)
   - Critical/High findings with business impact
   - Risk score and compliance posture
   - Strategic recommendations (corpus security, access controls, validation)
   - Industry baseline comparison

2. **Technical Findings Report** (25-40 pages)
   - Detailed vulnerability write-ups per trust boundary
   - PoC evidence (transcripts, corpus manipulation examples, retrieval logs)
   - Exploitation narratives with business context
   - Root cause analysis and mitigation guidance
   - Framework mapping (ATLAS, OWASP, NIST)

3. **Framework Compliance Matrix**
   - OWASP LLM Top 10 tested vs. not-tested breakdown
   - MITRE ATLAS technique coverage
   - NIST AI RMF alignment

4. **Risk-Prioritized Remediation Roadmap**
   - Phased mitigation plan: Immediate → Short-term → Long-term
   - Effort estimates per fix
   - Risk reduction impact scores
   - Validation criteria and retest guidance

5. **Evidence Package**
   - Adversarial document examples (50+ poisoned passages)
   - Corpus poisoning demonstrations
   - Retrieval manipulation proof-of-concepts
   - Embedding attack examples
   - Reproduction scripts and test harnesses

## Sources

> Security considerations for RAG systems including PII handling, access control, and output validation.
> — [[sources/bibliography#Generative AI Security]], p. 204-208 (§7.2)

> RAG pattern implementation and architecture for cybersecurity applications.
> — [[sources/bibliography#Generative AI Security]], p. 285 (§9.2.6)

> Framework mapping based on OWASP LLM Top 10 and MITRE ATLAS.
> — [[frameworks/OWASP-top-10-LLM|OWASP Top 10 for LLM Applications]]
> — [[frameworks/atlas/MITRE-ATLAS|MITRE ATLAS Framework]]
