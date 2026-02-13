---
id: search-application-repositories
title: "AML.T0004: Search Application Repositories"
sidebar_label: "Search Application Repositories"
sidebar_position: 5
---

# AML.T0004: Search Application Repositories

Adversaries may search open application repositories during targeting.
Examples of these include Google Play, the iOS App store, the macOS App Store, and the Microsoft Store.

Adversaries may craft search queries seeking applications that contain AI-enabled components.
Frequently, the next step is to [Acquire Public AI Artifacts](/techniques/AML.T0002).

## Metadata

- **Technique ID:** AML.T0004
- **Created:** 2021-05-13
- **Last Modified:** 2025-10-13
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/reconnaissance|Reconnaissance]]

## Mitigations (1)

- [[frameworks/atlas/mitigations/limit-public-release-of-information|AML.M0000: Limit Public Release of Information]] — Limit the release of sensitive information in the metadata of deployed systems and publicly available applications.

## Case Studies (2)

- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AI Model Tampering via Supply Chain Attack]]
