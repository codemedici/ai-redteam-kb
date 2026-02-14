---
title: Agent-Based GenAI & LAM Security
tags:
  - type/attack
  - source/generative-ai-security
  - needs-review
---

# Agent-Based GenAI & LAM Security

# Agent-Based GenAI & LAM Security

7.4 Agent-Based GenAI Applications and Security
Recent advancements have seen the emergence of a powerful new trend in which
GenAI models are augmented to become “agents”—software entities capable of
performing tasks on their own, ultimately in the service of a goal, rather than simply
responding to queries from human users. This change may seem simple, but it opens
up an entire universe of new possibilities. By combining the linguistic fluency of
GenAI with the ability to accomplish tasks and make decisions independently,
GenAI is elevated from a passive tool, however powerful it may be, to an active
partner in real-time work execution.
The potential of such powerful agents has been a topic of active research and
development for some time. Salesforce has called these agents Large Action Models,
or LAMs (Savarese, 2023).
Figure 7.3 succinctly encapsulates the structure and critical aspects of Agent-
Based GenAI applications and security, particularly focusing on Large Action
Models (LAMs).
Agent-based GenAI applications, ReAct (discussed in Sect. 7.3), and RAG
(discussed in Sect. 7.2) represent distinct paradigms in GenAI, each with unique
attributes. Agent-based applications focus on creating autonomous agents that can
interact with environments, planning, learning, and adapting through experience,
often using techniques like reinforcement learning. ReAct, on the other hand,
emphasizes a strategic interleaving of reasoning and action, providing insight into
the model’s thought process and enabling more controlled interactions with exter-
nal environments. RAG prioritizes the enhancement of text generation by retriev-
ing relevant information from large corpora, enriching the generated content.
While agent-based applications offer adaptability and learning through continu-
ous interaction, ReAct offers a more structured approach to reasoning and action,
and RAG emphasizes the integration of external knowledge. The choice between
these paradigms depends on specific application needs, such as the level of con-
trol, interaction with external sources, adaptability, and the type of information
processing required.
7.4.1 How LAM Works
Large Action Models (LAMs) work by augmenting GenAI models with the ability
to perform tasks on their own, serving a specific goal rather than just responding to
human queries. Here’s a summary of how they work:
1. Combination of Linguistic Fluency and Action: LAMs combine the linguistic
capabilities of GenAI with the ability to accomplish tasks and make decisions
independently. They go beyond generating text or images and actively partici-
pate in real-time work execution.
 213


2. Autonomous Operation: LAMs are designed to operate autonomously, perform-
ing specific tasks, making decisions, and adapting to changing circumstances.
They are not just passive tools but active partners.
3. Interactions and Integrations: LAMs interact with various tools, agents, data
sources, and even other LAMs. In some cases, especially ones that require a
higher level of control, they also interact with users and/or SMEs. They can com-
municate in clear, fluid natural language, and they can connect data, tools, and
domain-specific agents to achieve high level tasks.
4. Personal Assistance: LAMs can serve as personal assistants, taking over repeti-
tive tasks, helping with significant buying decisions, and even initiating conver-
sations with sellers or stakeholders.
5. Organizational Transformation: In a business context, LAMs can augment staff
with sophisticated tools, save time and expense, prevent mistakes, and recom-
mend successful strategies. They can interact with customers, handle marketing
workflows, and much more.
6. Learning and Adaptation: LAMs are designed to learn from their experiences
and adapt to changing circumstances. They can use human feedback to refine
behavior, apply the logic that connects steps, and change plans to accommodate
changes in situations.
7. Security Considerations: The autonomous nature of LAMs requires robust
security measures, including authentication, data privacy, behavior monitor-
ing, integration security, human oversight, and multiple-agent communication
security.
8. Human Oversight: Despite their autonomy, LAMs are designed to keep humans
in the loop, allowing human users to have the final say, control, and oversight
over critical actions.
9. Future Potential: LAMs present vast possibilities for individual, organizational,
and machine to machine applications. They offer the potential for a new era of
productivity, but their development requires overcoming technical hurdles,
ensuring safety and reliability, and maintaining ethical principles.
In essence, LAMs represent a significant shift in AI development, transforming
GenAI from a tool for creating content into an active, intelligent partner capable of
autonomous actions, decisions, and learning, with vast applications in personal and
organizational contexts, while bearing important security considerations.
7.4.2 LAMs and GenAI: Impact on Security
The evolution of LAMs within the context of GenAI raises critical questions about
security. As GenAI applications become agents capable of taking independent
actions, the security implications become manifold. Here’s how agent-based GenAI
applications impact security:
 215
1. Authentication and Authorization: With the ability to perform actions on behalf
of users, LAMs must have stringent authentication and authorization mechanisms
in place. Ensuring that the agent has the right permissions to perform specific
actions without overstepping boundaries is crucial. This requires robust access
control policies and continuous monitoring.
2. Data Privacy: LAMs will likely interact with sensitive data, such as customer
information, financial records, and intellectual property. Ensuring the privacy
and confidentiality of this data is paramount. This involves implementing strong
encryption, securing data handling practices, and enforcing proper data retention
policy and compliance with relevant regulations such as GDPR.
3. Behavior Monitoring: The autonomous nature of LAMs necessitates continuous
monitoring of their behavior to detect any malicious or unintended activities.
Anomaly detection algorithms and real-time alerts can be employed to identify
suspicious activities and take immediate remedial actions.
4. Interoperability and Integration Security: As LAMs interact with various
tools, agents, and data sources, securing these integrations becomes essen-
tial. Implementing secure communication channels, validating data integrity,
and protecting against injection attacks are vital components of integration
security.
5. Human in the Loop Security: While LAMs operate autonomously, there must
always be a provision for human oversight. Implementing controls that allow
humans to intervene, review critical actions, and maintain ultimate control over
the LAMs is essential for trust and safety.
6. Machine to Machine Communication Security: The scenario where multiple
LAMs work together or interact with other systems necessitates secure machine
to machine communication. Implementing secure protocols, mutual authentica-
tion, cross domain access mapping and control, and data integrity checks are key
in this context.
7. Learning and Adaptation Security: LAMs are designed to learn and adapt, which
opens up risks related to adversarial attacks and biased learning. Ensuring that
the learning process is transparent, explainable, and resilient against manipula-
tion is a significant security challenge.
To sum it up, the rise of LAMs within the context of GenAI marks a transforma-
tive phase in technology, offering unprecedented capabilities and efficiencies.
However, with great power comes great responsibility. The security considerations
associated with agent-based GenAI applications are complex and multifaceted. By
understanding and addressing these challenges, we can unlock the full potential of
LAMs, enabling them to serve as empowering tools that enhance productivity while
safeguarding integrity, privacy, and trust. The road ahead is filled with promise, but
it requires careful navigation, collaboration, and a commitment to ethical principles
to ensure that LAMs fulfill their potential as a revolutionary force in technology and
business.


Source: [[sources/bibliography#Generative AI Security]], p. 212 (§7.4)
