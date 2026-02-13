---
title: "The Developer's Playbook for Large Language Model Security"
author: Steve Wilson
year: 2024
publisher: O'Reilly Media
tags:
  - source/book
  - topic/llm-security
  - topic/owasp-top10-llm
  - topic/prompt-injection
  - topic/supply-chain
  - topic/llmops
---

# The Developer's Playbook for Large Language Model Security

## Metadata

- **Author:** Steve Wilson
- **Publisher:** O'Reilly Media, Inc.
- **Year:** 2024
- **ISBN:** (pending)
- **Pages:** ~200
- **URL:** https://www.oreilly.com/library/view/the-developers-playbook/

## About the Author

**Steve Wilson** is the founder and project lead of the OWASP Top 10 for LLM Applications project. He works at the intersection of AI and cybersecurity at Exabeam.

## Book Description

Practical handbook for developers and security teams delivering real-world guidance and actionable strategies for LLM application security. Covers unique characteristics and vulnerabilities of LLM-based software, from architecture to deployment.

## Key Topics Covered

### Section 1: Laying the Foundation
- **Chapter 1:** Chatbots Breaking Bad (Tay case study)
- **Chapter 2:** OWASP Top 10 for LLM Applications
- **Chapter 3:** Architectures and Trust Boundaries

### Section 2: Risks, Vulnerabilities, and Remediations
- **Chapter 4:** Prompt Injection (direct vs. indirect, mitigation strategies)
- **Chapter 5:** Can Your LLM Know Too Much? (sensitive information disclosure)
- **Chapter 6:** Hallucinations
- **Chapter 7:** Trust No One (zero trust principles)
- **Chapter 8:** Don't Lose Your Wallet (DoS, DoW, model cloning)
- **Chapter 9:** Find the Weakest Link (supply chain security, ML-BOMs)

### Section 3: Building a Security Process
- **Chapter 10:** Learning from Future History (sci-fi case studies)
- **Chapter 11:** Trust the Process (DevSecOps, MLOps, LLMOps, guardrails, AI red teaming)
- **Chapter 12:** A Practical Framework for Responsible AI Security (RAISE framework)

## Notable Contributions

- **OWASP Top 10 LLM Applications:** Project founding and execution
- **LLMOps Security:** Extension of MLOps for LLM-specific challenges
- **Prompt Injection Taxonomy:** Direct vs. indirect, forceful suggestion, reverse psychology, misdirection
- **Guardrails Framework:** Runtime protection patterns for LLMs
- **AI Red Teaming:** Presidential Executive Order context, red team vs. pen test distinctions

## Extraction Notes

**Extracted:** 2026-02-12 by Shadowbane
**High-value chapters:** 4 (Prompt Injection), 7 (Zero Trust), 9 (Supply Chain), 11 (LLMOps)
**Integration:** Content merged into existing notes to avoid duplication

## Related Notes

- [[techniques/prompt-injection]] — Chapter 4 content merged
- [[mitigations/mlops-security]] — Chapter 11 LLMOps content merged
- [[playbooks/llm-application-red-team]] — Chapter 11 red teaming content added
- [[frameworks/atlas/case-studies/tay-poisoning]] — Chapter 1 case study (already existed)

## Citation Format

```markdown
> [Quote]
> 
> Source: [[sources/developers-playbook-llm]], p. [page number]
```
