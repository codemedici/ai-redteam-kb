---
title: "Embedding Poisoning"
tags:
  - type/technique
  - target/rag
  - target/embedding
  - access/inference
  - access/supply-chain
atlas: AML.T0020
owasp: LLM03
maturity: stub
created: 2026-02-14
updated: 2026-02-14
---
# Embedding Poisoning

## Summary

Adversary manipulates embeddings at indexing or query time to control retrieval results in RAG systems. By poisoning vector representations stored in vector databases, attackers can bias which documents are retrieved for a given query, enabling misinformation injection, context manipulation, or denial of service against retrieval-augmented generation pipelines.

## Mechanism

**TODO:** Describe how attackers poison vector embeddings — direct vector manipulation, document text swapping before re-embedding, adversarial document crafting to cluster near target queries.

## Preconditions

- **TODO:** List required conditions (e.g., write access to vector database, ability to contribute documents to indexing pipeline)

## Impact

**Business Impact:**
- **TODO:** Describe business consequences (misinformation, compliance violations, reputational damage)

**Technical Impact:**
- **TODO:** Describe technical consequences (biased retrieval, degraded RAG accuracy, stale/malicious context injection)

## Detection

- **TODO:** Observable indicators (embedding drift from expected clusters, semantic inconsistencies, anomalous retrieval patterns)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0021 | [[mitigations/embedding-integrity-verification]] | Cryptographic signing and periodic re-embedding detect tampering with stored vectors |
| AML.M0020 | [[mitigations/source-validation-and-trust-scoring]] | Trust-based vetting of data sources blocks malicious embedding injection from untrusted contributors |
| AML.M0022 | [[mitigations/data-quarantine-procedures]] | Isolates manipulated embeddings for verification before production indexing |
| | [[mitigations/input-validation-transformation]] | Input validation and transformation disrupts adversarial perturbations in embeddings and source documents |

## Sources

*(No source material extracted yet — stub note.)*
