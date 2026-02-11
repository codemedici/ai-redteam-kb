---
id: search-open-websites-domains
title: "AML.T0095: Search Open Websites/Domains"
sidebar_label: "Search Open Websites/Domains"
sidebar_position: 11
---

# AML.T0095: Search Open Websites/Domains



Adversaries may search public websites and/or domains for information about victims that can be used during targeting. Information about victims may be available in various online sites, such as social media, new sites, or domains owned by the victim.

Adversaries may find the information they seek to gather via search engines. They can use precise search queries to identify software platforms or services used by the victim to use in targeting. This may be followed by [[exploit-public-facing-application|Exploit Public-Facing Application]] or [[prompt-infiltration-via-public-facing-application|Prompt Infiltration via Public-Facing Application]].

## Metadata

- **Technique ID:** AML.T0095
- **Created:** November 5, 2025
- **Last Modified:** November 6, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1593](https://attack.mitre.org/techniques/T1593/)


## Tactics (1)

This technique supports the following tactics:


- [[reconnaissance|AML.TA0002: Reconnaissance]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]

The researchers used a search query, “site:atlassian.net/servicedesk inurl:portal”,  to reveal organizations using Atlassian service portals as potential targets.



## References

MITRE Corporation. *Search Open Websites/Domains (AML.T0095)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0095
