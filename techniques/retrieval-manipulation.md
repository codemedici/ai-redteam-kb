---
title: "Retrieval Manipulation"
tags:
  - type/technique
  - target/rag
  - access/inference
  - access/api
atlas: AML.T0051.002
maturity: stub
created: 2026-02-14
updated: 2026-02-14
---
# Retrieval Manipulation and Hijacking

## Summary

Retrieval manipulation attacks target the semantic search and ranking mechanisms in Retrieval-Augmented Generation (RAG) systems to surface malicious, irrelevant, or attacker-controlled content during document retrieval. By poisoning the knowledge base, manipulating embeddings, or exploiting ranking algorithms, attackers can inject false information, bias LLM outputs, or hijack responses to serve adversarial objectives. This technique is particularly dangerous in RAG systems where the LLM trusts retrieved content implicitly and incorporates it directly into generated responses.

## Mechanism

**TODO:** Complete detailed attack mechanism description.

**Attack vectors include:**
- Poisoning knowledge base documents with adversarial content optimized for high semantic similarity to target queries
- Manipulating embedding vectors directly in vector databases to bias retrieval rankings
- Exploiting ranking algorithm weaknesses to surface low-quality or malicious documents
- Injecting trigger phrases into documents that cause them to rank highly for specific queries
- Semantic drift attacks that gradually shift document embeddings away from original meaning
- Cross-lingual retrieval manipulation using multilingual embedding spaces

## Preconditions

- Attacker has ability to contribute documents to RAG knowledge base (via data poisoning, compromised data source, or public contribution mechanisms)
- OR: Attacker has write access to vector database to manipulate embeddings directly
- Target RAG system lacks source validation and embedding integrity verification
- System trusts retrieved content without validation or filtering
- No anomaly detection for unusual retrieval patterns or embedding drift

## Impact

**Business Impact:**
- **Misinformation propagation:** LLM generates false information based on poisoned retrieval results
- **Brand damage:** System provides incorrect or harmful responses attributed to organization
- **Compliance violations:** Retrieval of unauthorized or regulated content
- **Competitive harm:** Attacker biases system to favor competitor products or services
- **Trust erosion:** Users lose confidence in system accuracy and reliability

**Technical Impact:**
- Retrieval ranking compromised to surface attacker-controlled content
- Embedding space integrity violated through direct manipulation or poisoning
- LLM outputs biased by malicious retrieved context
- Semantic search quality degraded through knowledge base contamination
- Detection difficult when poisoned documents appear semantically legitimate

**Severity:** **High** — Can fundamentally compromise RAG system trustworthiness and lead to systematic misinformation.

## Detection

- Retrieved documents exhibit unusually high semantic similarity to queries (potential embedding manipulation)
- Embedding vectors drift significantly from expected cluster centroids
- Document rankings change suddenly without knowledge base updates
- Specific queries consistently retrieve content from suspicious or low-trust sources
- Retrieval results contain content contradicting high-trust sources
- Statistical anomalies in embedding space (outliers, unexpected clusters)
- Users report receiving incorrect or biased information from system
- Monitoring detects same malicious document retrieved across diverse queries

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0020 | [[mitigations/source-validation-and-trust-scoring]] | Assign trust scores to data sources; apply stricter validation to low-trust contributions |
| AML.M0021 | [[mitigations/embedding-integrity-verification]] | Cryptographically sign embeddings and verify integrity before retrieval to detect tampering |
| AML.M0004 | [[mitigations/input-validation-patterns]] | Validate documents before ingestion; detect adversarial content patterns |

## Sources

> (Placeholder - content to be enriched from source materials)
