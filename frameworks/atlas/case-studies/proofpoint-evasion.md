---
id: proofpoint-evasion
title: "AML.CS0008: ProofPoint Evasion"
sidebar_label: "ProofPoint Evasion"
sidebar_position: 9
---

# AML.CS0008: ProofPoint Evasion

## Summary

Proof Pudding (CVE-2019-20634) is a code repository that describes how ML researchers evaded ProofPoint's email protection system by first building a copy-cat email protection ML model, and using the insights to bypass the live system. More specifically, the insights allowed researchers to craft malicious emails that received preferable scores, going undetected by the system. Each word in an email is scored numerically based on multiple variables and if the overall score of the email is too low, ProofPoint will output an error, labeling it as SPAM.

## Metadata

- **Case Study ID:** AML.CS0008
- **Incident Date:** 2019
- **Type:** exercise
- **Target:** ProofPoint Email Protection System
- **Actor:** Researchers at Silent Break Security

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Discover AI Model Outputs

**Technique:** [[frameworks/atlas/techniques/discovery/discover-ai-model-outputs|AML.T0063: Discover AI Model Outputs]]

The researchers discovered that ProofPoint's Email Protection left model output scores in email headers.

### Step 2: AI-Enabled Product or Service

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

The researchers sent many emails through the system to collect model outputs from the headers.

### Step 3: Train Proxy via Replication

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-train-via-replication|AML.T0005.001: Train Proxy via Replication]]

The researchers used the emails and collected scores as a dataset, which they used to train a functional copy of the ProofPoint model. 

Basic correlation was used to decide which score variable speaks generally about the security of an email. The "mlxlogscore" was selected in this case due to its relationship with spam, phish, and core mlx and was used as the label. Each "mlxlogscore" was generally between 1 and 999 (higher score = safer sample). Training was performed using an Artificial Neural Network (ANN) and Bag of Words tokenizing.

### Step 4: Black-Box Transfer

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data-black-box-transfer|AML.T0043.002: Black-Box Transfer]]

Next, the ML researchers algorithmically found samples from this "offline" proxy model that helped give desired insight into its behavior and influential variables.

Examples of good scoring samples include "calculation", "asset", and "tyson".
Examples of bad scoring samples include "software", "99", and "unsub".

### Step 5: Evade AI Model

**Technique:** [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

Finally, these insights from the "offline" proxy model allowed the researchers to create malicious emails that received preferable scores from the real ProofPoint email protection system, hence bypassing it.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/discovery/discover-ai-model-outputs|AML.T0063: Discover AI Model Outputs]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-train-via-replication|AML.T0005.001: Train Proxy via Replication]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data-black-box-transfer|AML.T0043.002: Black-Box Transfer]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

## External References

- National Vulnerability Database entry for CVE-2019-20634 Available at: https://nvd.nist.gov/vuln/detail/CVE-2019-20634
- 2019 DerbyCon presentation "42: The answer to life, the universe, and everything offensive security" Available at: https://github.com/moohax/Talks/blob/master/slides/DerbyCon19.pdf
- Proof Pudding (CVE-2019-20634) Implementation on GitHub Available at: https://github.com/moohax/Proof-Pudding
- 2019 DerbyCon video presentation "42: The answer to life, the universe, and everything offensive security" Available at: https://www.youtube.com/watch?v=CsvkYoxtexQ&ab-channel=AdrianCrenshaw

## References

MITRE Corporation. *ProofPoint Evasion (AML.CS0008)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0008
