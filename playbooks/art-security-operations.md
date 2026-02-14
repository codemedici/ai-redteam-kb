---
title: Automatic Reasoning & Tool-use (ART)
tags:
  - type/playbook
  - source/generative-ai-security
  - needs-review
---

# Automatic Reasoning & Tool-use (ART)

# Automatic Reasoning & Tool-use (ART)

9.2.7 A utomatic Reasoning and Tool Use (ART)
Automatic Reasoning and Tool use (ART) is an advanced approach that integrates
CoT (Chain of Thought) prompting with tools, enabling the language model to gen-
erate intermediate reasoning steps as a program. The process is dynamic, allowing
for the incorporation of external tools and even human feedback to correct or aug-
ment reasoning (Promptingguide.ai, 2023).
ART operates in a structured manner through the following steps:
1. Task Selection: Given a new task, ART selects demonstrations of multistep rea-
soning and tool use from a predefined library.
2. Generation and Tool Integration: During test time, the model’s generation is
paused whenever external tools are called. The output from these tools is inte-
grated before resuming generation.
3. Zero shot Generalization: The model is encouraged to generalize from the dem-
onstrations to decompose a new task and use tools in appropriate places without
specific training for the task.
4. Extensibility: ART allows for the addition of new tools or correction of reason-
ing steps by updating the task and tool libraries.
This approach has been shown to perform well on various benchmarks, demon-
strating a robust and flexible solution for complex reasoning tasks.
Picture this scenario: An organization experiences a sudden surge in failed
login attempts and unauthorized access alerts. Existing Identity and Access
Management (IAM) solutions notify the cybersecurity team, but they require
additional insights for timely and effective countermeasures. Here, ART can
extend its arm of utility.
To elaborate, ART initiates the process with the Input Query stage. In this case,
the query could be, “Analyze the IAM anomalies for potential unauthorized access
9 Utilizing Prompt Engineering to Operationalize Cybersecurity 289
or identity theft. Identify affected user accounts, assess the threats, and recommend
preventive actions.” The Task Selection Phase would then kick in, pulling out rele-
vant demonstrations of multistep reasoning exercises from its task library, like IAM
risk assessment or unauthorized access detection.
During the Generation and Tool Integration Phase, ART makes use of sophisti-
cated tools used in IAM anomaly detection. This could range from AI-based
behavior analytics tools that analyze user behaviors against baselines to database
query tools that look for anomalies in user account databases. ART would pause
its generation to run these tools and collect data. These tools could check, for
instance, if the IP addresses involved in the failed login attempts are known for
malicious activities or if the affected user accounts had undergone recent changes
in permissions.
The output from these tools is then integrated into the overall reasoning chain.
This crucial step marries automated tool outputs with the language model’s own
reasoning, thereby enhancing the quality and depth of the insights generated. It
ensures that the subsequent recommendations are not just based on abstract reason-
ing but also corroborated with hard data and analytics.
Following this, the Generation Phase begins where ART provides an exhaustive
analysis of the situation. For instance, it could indicate that the anomalous behavior
traces back to a group of user accounts that recently had their permissions elevated.
The risks could include unauthorized access to sensitive data, violation of compli-
ance norms, and potential data manipulation. Therefore, ART might propose imme-
diate actions such as temporarily disabling the affected accounts, reinforcing
multifactor authentication mechanisms, and initiating an urgent audit of permission
settings across the organization.
The end result is a coherent, data-driven analysis and set of recommendations
that a cybersecurity team can immediately act upon. This targeted, efficient
approach to resolving IAM issues exemplifies how ART can evolve into a pow-
erful ally in cybersecurity operations. The methodology is particularly potent
because it allows for rapid incorporation of new tools and reasoning steps
through its extensible architecture, thereby staying agile in the face of new and
emerging threats.
The following are sample prompts using ART in the specific scenario of IAM
anomaly detection. Each prompt is structured to mirror the steps in the ART pro-
cess, from task selection to generating a detailed analysis.
Input Query:
• “Analyze IAM anomalies for potential unauthorized access or
identity theft. Identify affected user accounts, assess the threats,
and recommend preventive actions.”
Task Selection Phase Prompts:
• “Select reasoning demonstrations related to Identity and Access
Management issues.”
• “Choose examples that cover unauthorized access detection,
threat modeling, and risk assessment within IAM.”
Tool Integration Phase Prompts:

• “Run the AI-based user behavior analytics tool on the flagged
accounts to compare against baseline behaviors.”
• “Use the IP reputation checker tool to assess the risk associ-
ated with the source IPs involved in the failed login attempts.”
• “Query the internal database for the user accounts involved in
the anomalies and extract the last 30 days of activity and permis-
sion changes.”
Generation Phase Prompts:
• “Generate a comprehensive analysis of detected IAM anomalies.”
• “Identify the user accounts that have exhibited anomalous
behavior and specify the nature of the anomalies.”
• “Assess the threat level for each identified account based on
analytics and IP reputation.”
• “Recommend immediate preventive actions, including any neces-
sary account suspensions or permission rollbacks.”
For instance, following these prompts, the ART system could output: “After
leveraging AI-based user behavior analytics and IP reputation checks, the anoma-
lous behavior is primarily associated with a subset of user accounts that recently
received escalated permissions. The threat level for these accounts is high as the
source IPs are known for malicious activities. Immediate preventive actions include
suspending the affected accounts, initiating a multi-factor authentication challenge
for accounts with suspicious activities, and rolling back the recently changed per-
missions while conducting a thorough audit.”
This sample output highlights how ART, when instructed with carefully crafted
prompts, can provide an in-depth, data-driven analysis for IAM issues. The solution
fuses machine-generated insights with real-world tool outputs, ensuring that the
cybersecurity team receives actionable recommendations that are both precise and
contextually aware. It’s a powerful illustration of how ART can be effectively imple-
mented in a cybersecurity setting, specifically in addressing the complex and often
urgent issues related to Identity and Access Management.

Source: [[sources/bibliography#Generative AI Security]], p. 286 (§9.2.7)
