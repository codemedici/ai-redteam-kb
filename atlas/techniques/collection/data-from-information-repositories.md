---
id: data-from-information-repositories
title: "AML.T0036: Data from Information Repositories"
sidebar_label: "Data from Information Repositories"
sidebar_position: 2
---

# AML.T0036: Data from Information Repositories

Adversaries may leverage information repositories to mine valuable information.
Information repositories are tools that allow for storage of information, typically to facilitate collaboration or information sharing between users, and can store a wide variety of data that may aid adversaries in further objectives, or direct access to the target information.

Information stored in a repository may vary based on the specific instance or environment.
Specific common information repositories include SharePoint, Confluence, and enterprise databases such as SQL Server.

## Metadata

- **Technique ID:** AML.T0036
- **Created:** January 24, 2022
- **Last Modified:** January 18, 2023
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1213](https://attack.mitre.org/techniques/T1213/)

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[atlas/case-studies/clearviewai-misconfiguration|AML.CS0006: ClearviewAI Misconfiguration]]

The private code repository contained credentials which were used to access AWS S3 cloud storage buckets, leading to the discovery of assets for the facial recognition tool, including:
- Released desktop and mobile applications
- Pre-release applications featuring new capabilities
- Slack access tokens
- Raw videos and other data

## References

MITRE Corporation. *Data from Information Repositories (AML.T0036)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0036
