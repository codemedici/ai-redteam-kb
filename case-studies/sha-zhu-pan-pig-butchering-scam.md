---
title: "Sha Zhu Pán (Pig Butchering) LLM-Enhanced Scam"
tags:
  - type/case-study
  - target/llm
  - target/human
  - source/llms-in-cybersecurity
atlas: AML.CS0007
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Sha Zhu Pán (Pig Butchering) LLM-Enhanced Scam

## Summary

In 2023, Sophos security team incident response identified LLM-enhanced romance scam campaigns using ChatGPT or similar models to generate emotionally manipulative messages. The scam, known as "Sha Zhu Pán" (猪屠盘, "pig butchering"), involves building trust over weeks/months through fake romantic relationships, then luring victims into fraudulent cryptocurrency investments. Security analysts detected LLM usage through sudden shifts from grammatically broken messages to flowery, perfect English—indicating scammers switched to AI-generated text for re-engagement after victims went silent.

## Incident Details

### Background
- **Scam Type**: Sha Zhu Pán (Pig Butchering) — romance-themed cryptocurrency fraud
- **Detection**: Sophos security team incident response (2023)
- **Platform**: Dating apps, WhatsApp, social media DMs
- **Geography**: Global (originated in Asia, spread worldwide)
- **Victim Profile**: Lonely individuals seeking romantic connections

### Attack Pattern (Traditional)

**Pre-LLM Approach:**
1. **Initial Contact**: Fake romantic interest via dating app or "wrong number" message
2. **Build Trust**: Weeks/months of manual conversation developing relationship
3. **Cryptocurrency Hook**: Introduce "investment opportunity" or "trading platform"
4. **Extraction**: Victim transfers funds to fake crypto wallet/platform
5. **Exit**: Scammer disappears once victim exhausted

**Limitations (Pre-LLM):**
- **Language barriers**: Non-native speakers made grammatical errors
- **Scaling constraint**: Manual conversations limit number of simultaneous victims
- **Re-engagement difficulty**: Hard to craft convincing messages to silent victims

### LLM Enhancement (2023)

**LLM Detection Markers:**

**Incident Example:**
- **Earlier scammer messages**: Grammatically incorrect, foreign language slip-ups, inconsistent tone
- **Victim stops responding**: Scammer attempts re-engagement
- **Re-engagement message**: **Suddenly perfect, flowery English with LLM hallmarks**

> "I hope this letter finds you well. I wanted to reach out to you because something has been weighing heavily on my heart. Our connection, though not officially established as a romantic relationship, meant a lot to me. The friendship we shared was special, and it brought a certain brightness to my life that I cherished deeply..."
> — [[sources/bibliography#LLMs in Cybersecurity]], p. 85

**LLM Indicators:**
- **Formal greeting**: "I hope this letter finds you well" (ChatGPT-style opening)
- **Verbose, emotionally manipulative phrasing**: Characteristic of LLM-generated romantic text
- **Sudden grammar perfection**: Contrasts sharply with prior broken English
- **Lack of personalization**: Generic "our connection" instead of specific shared memories
- **Flowery language**: "weighing heavily on my heart," "brightness to my life"

### How LLMs Enhance the Scam

**1. Overcoming Language Barriers:**
- Scammers with poor English now generate native-quality messages
- Victims less suspicious due to professional communication

**2. Emotional Manipulation at Scale:**
- LLMs excel at generating emotionally evocative romantic language
- Can produce personalized variations for dozens of victims simultaneously

**3. Re-Engagement Automation:**
- When victim goes silent, LLM generates compelling "win-back" messages
- Multiple attempts with different emotional angles (concern, longing, guilt)

**4. Conversation Quality:**
- Mix of manual messages (for authenticity) + LLM polish (for persuasion)
- LLM handles responses when scammer uncertain or victim asks complex questions

### Why It Works

**Victim Emotional Investment:**
- After weeks of chat history, victims want to believe the relationship is real
- Sunk cost fallacy: Already invested time/emotion, reluctant to admit it's fake

**Crypto Complexity:**
- Technical jargon obscures fraud mechanisms
- Victims unfamiliar with crypto can't verify platform legitimacy

**LLM Polish:**
> "LLM indicators: Perfect language signals 'legitimacy' vs. broken English scammers"
> — Analysis from incident

Traditional scam detection advice: "Watch for broken English, grammar errors"
**LLM breaks this heuristic** — now scammers produce perfect, emotionally intelligent text.

## Techniques Used

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0114 | [[techniques/llm-powered-phishing-and-social-engineering]] | Use of LLMs to generate emotionally manipulative romantic messages at scale |
| AML.T0051 | [[techniques/prompt-injection]] | Potential use of jailbroken LLMs to generate scam content bypassing safety guardrails |

## Tactic Flow

1. **[[frameworks/atlas/tactics/reconnaissance]]**: Identify lonely individuals on dating platforms
2. **[[frameworks/atlas/tactics/initial-access]]**: Fake romantic interest initiates contact
3. **[[frameworks/atlas/tactics/execution]]**: LLM-generated messages build trust and manipulate emotions
4. **[[frameworks/atlas/tactics/credential-access]]**: Extract cryptocurrency wallet credentials or transfers
5. **[[frameworks/atlas/tactics/impact]]**: Financial loss, emotional trauma for victims

## Impact & Outcome

### Victim Impact
- **Financial Loss**: Average loss $50,000 - $200,000 per victim (cryptocurrency transfers)
- **Emotional Trauma**: Heartbreak from fake relationship + financial devastation
- **Secondary Exploitation**: Stolen personal information enables further scams
- **Social Stigma**: Victims ashamed to report, reducing law enforcement visibility

### Scale & Economics

**Traditional Scam Limits:**
- Scammer could manage 5-10 simultaneous victims manually
- High effort per victim (weeks of daily messages)

**LLM-Enhanced Scale:**
- Scammer can manage 50-100+ victims with LLM assistance
- Automated re-engagement messages
- 10x+ increase in victim throughput

**ROI (Scammer Perspective):**
- LLM API costs: $5-$20/month for unlimited ChatGPT or free jailbroken models
- Average victim yield: $100,000
- **Profit multiplier: LLM investment pays for itself with single victim**

### Law Enforcement Challenges

**Detection Difficulty:**
- **Distributed operations**: Scammers in one country, victims in another
- **Cryptocurrency anonymity**: Hard to trace funds
- **Victim embarrassment**: Underreporting due to shame
- **LLM evasion**: Perfect grammar defeats traditional scam indicators

**Attribution Challenges:**
- Fake profiles, burner numbers
- VPN/proxy obfuscation
- Crypto wallet anonymity

## Lessons Learned

### Evolution of Scam Sophistication

**Pre-LLM Red Flags:**
- Broken English, grammar errors
- Generic messages
- Requests to move off-platform quickly

**LLM-Era Reality:**
- **Perfect English**: No longer a trust signal
- **Personalized messages**: LLMs can generate seemingly custom content
- **Emotionally intelligent**: AI excels at romantic, persuasive language

### Why Traditional Advice Fails

**Old Guidance**: "Watch for poor grammar and spelling"
**Current Reality**: LLMs produce native-quality text in any language

**Old Guidance**: "Be suspicious of generic messages"
**Current Reality**: LLMs can personalize based on victim's profile/messages

**Old Guidance**: "Trust your gut about authenticity"
**Current Reality**: AI-generated emotional manipulation is highly convincing

### Updated Defense Strategies

**For Potential Victims:**

**1. Out-of-Band Verification:**
- Video call requirement (not just photos—those can be deepfakes)
- Meet in person if claiming to be local
- Reverse image search profile photos

**2. Financial Red Flags:**
- **Never invest based on online relationship advice**
- Any mention of cryptocurrency = immediate red flag
- Research platforms independently, not through partner's links

**3. Slow Down:**
> "LLM messages often push urgency; pause and verify"

- Scammers create artificial time pressure
- Legitimate relationships don't require rushed financial decisions

**4. Shared Secrets / Context Verification:**
- Establish shared context from early conversations
- Test if "partner" remembers specific details (LLM may hallucinate)

**For Platforms:**

**1. Behavioral Analysis:**
- Detect accounts messaging many users with romantic intent
- Flag accounts that rapidly move conversations off-platform
- Monitor for copy-paste patterns (even if text unique each time, timing patterns repeat)

**2. LLM Detection:**
- Analyze message linguistic patterns for AI generation signatures
- Flag sudden shifts in grammar quality
- Detect flowery, verbose language characteristic of LLMs

**3. Cryptocurrency Mention Triggers:**
- Auto-flag messages containing crypto trading keywords
- Warn users when conversation partner mentions investments

**For Law Enforcement:**

**1. International Cooperation:**
- Coordinate with jurisdictions where scam operations based
- Share intelligence on known scammer profiles/patterns

**2. Cryptocurrency Tracking:**
- Work with exchanges to flag/freeze suspicious wallets
- Trace fund flows to identify scammer accounts

**3. Victim Support:**
- Reduce stigma to encourage reporting
- Provide resources for both financial and emotional recovery

## Sources

> "Sha Zhu Pán (Pig Butchering) Scams (Sophos security team incident response, 2023): Romance-themed cryptocurrency fraud. Traditional approach: Manual chat conversations over weeks/months. LLM enhancement: Automated, grammatically perfect, emotionally manipulative messages."
> — Summary from [[sources/bibliography#LLMs in Cybersecurity]], p. 82-86

> "LLM Detection Markers: Earlier scammer messages: Grammatically incorrect, foreign language slip-ups. Re-engagement message: Suddenly perfect, flowery English with LLM hallmarks: 'I hope this letter finds you well. I wanted to reach out to you because something has been weighing heavily on my heart. Our connection, though not officially established as a romantic relationship, meant a lot to me. The friendship we shared was special, and it brought a certain brightness to my life that I cherished deeply...'"
> — [[sources/bibliography#LLMs in Cybersecurity]], p. 85

> "LLM indicators: Formal greeting ('I hope this letter finds you well'), verbose emotionally manipulative phrasing, sudden grammar perfection vs. prior messages, lack of personalization (generic 'our connection')"
> — Analysis from [[sources/bibliography#LLMs in Cybersecurity]], p. 85

**References:**
- Sophos: "Sha Zhu Pán Scam Uses AI Chat Tool" (August 2, 2023)
  URL: https://news.sophos.com/en-us/2023/08/02/sha-zhu-pan-scam-uses-ai-chat-to-target-iphone-and-android-users/
- Sophos: "Latest Evolution of Pig Butchering Scam" (September 18, 2023)
  URL: https://news.sophos.com/en-us/2023/09/18/latest-evolution-of-pig-butchering-scam-lures-victim-into-fake-mining-scheme/
- FBI Internet Crime Report (2023): Cryptocurrency investment fraud losses
