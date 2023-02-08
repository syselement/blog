# Cyber Threat Intelligence (CTI)

## Definition

**`Threat Intelligence`** (`T.I.`) is *data that is collected, processed and analyzed to understand a threat actor’s motives, targets and attack behaviors*.

- it refers to information that organizations can use to combat cyber threats
- cybersecurity experts and data scientists examine, visualize and analyze large volumes of unorganized data in order to turn it into actionable insights that power informed decisions

> *Threat intelligence is evidence-based knowledge, including context, mechanisms, indicators, implications and actionable advice, about an existing or emerging menace or hazard to assets that can be used to inform decisions regarding the subject's response to that menace or hazard.* - [Gartner](https://www.gartner.com/en/documents/2487216)

[`Cyberattacks`](https://www.crowdstrike.com/cybersecurity-101/cyberattacks/) - malware, ransomware, phishing, man-in-the-middle, denial-of-service, APTs ...

### 3 Types of TI

Maturity curve - The context and analysis of CTI becomes deeper and more sophisticated with each level.

`TACTICAL` - *focus on the immediate future and identifies simple indicators of compromise (**`IOCs`**)* by performing malware analysis/enrichment and ingesting threat indicators into defensive systems.

- almost always automated (feeds)
- IOCs short lifespan
- Stakeholders: SOC Analyst, SIEM, FIrewall, Endpoints, IDS/IPS

`OPERATIONAL` - *focus on understanding the attack adversaries, campain tracking and actor profiling (the "who", the "why" and "how")*.

- human analysis needed, longer useful life
- Stakeholders: Threat Hunter, SOC Analyst, Vulnerability Management, Incident Response, Insider Threat

`STRATEGIC` - *focus on understanding trends and adversarial motives, engage in strategic security, inform business decisions and the processes behind them*.

- shows how global events, foreign policies, and other long-term local and international movements can potentially impact the cyber security of an organization

- Stakeholders: CISO, CIO, CTO, Executive Board, Strategic Intel

## Lifecycle

![crowdstrike.com - © CrowdStrike](https://www.crowdstrike.com/wp-content/uploads/2019/07/threat-intelligence-lifecycle-3.jpg)

**`T.I. lifecycle`** - the process of *turning raw data into finished intelligence for decision making and action*.

Every researsh is based on a variety of different versions of the intelligence cycle, but the *end aim is always the same*: **to lead a cybersecurity team through the development and execution of an effective threat intelligence program**.

- `Requirements` - sets the scope/roadmap (goals & methodology) for a specific threat intelligence operation

```
- Which types of assets, processes, and personnel are at risk?
- How will threat intelligence improve operational efficiency for my team?
- What other systems and applications could benefit?
```

- `Collection` - data gathering to satisfy objectives (traffic logs, osint, etc)

```
- Where are your current internal and external blindspots?
- What technical and automated collection techniques can you employ?
- How well can you infiltrate cybercriminal forums and closed sources on the dark web?
```

- `Processing` - raw data filtered, categorized, translated into an analysis suitable format
- `Analysis` - find answers to the posed questions, asses intelligence significance and severity, spot IOCs (indicators of compromise)

```
- Which types of assets, processes, and personnel are at risk?
- How will threat intelligence improve operational efficiency for my team?
- What other systems and applications could benefit?
```

- `Dissemination` - translate analysis into a digestible format report for stakeholders/audience, analysts

```
- What are the most important findings of the analysis, and what’s the best way to illustrate them?
- With what degree of confidence is the analysis reliable, relevant, and accurate?
- Are there clear and concrete recommendations or next steps regarding the end analysis?
```

- `Feedback` - receive stakeholder feedback establishing future adjustments and reports

```
- Which stakeholders benefit from finished threat intelligence reporting?
- What is the best way to present the intelligence and at what delivery frequency?
- Ultimately, how valuable is the finished intelligence? How actionable is it, and does it enable your organization to make informed security decisions? 
- And, finally, how can you improve on it going forward—both in terms of finished intelligence and ameliorating your organization’s intelligence cycle?
```

> - Above key consideration (listed with **-**) are taken from the [Flashpoint Blog post](https://flashpoint.io/blog/threat-intelligence-lifecycle/).

## Benefits

| **Function**             | **Benefits**                                                 |
| :----------------------- | :----------------------------------------------------------- |
| **Sec/IT Analyst**       | Optimize prevention and detection capabilities and strengthen defenses |
| **SOC**                  | Prioritize incidents based on risk and impact to the organization |
| **CSIRT**                | Accelerate incident investigations, management, and prioritization |
| **Intel Analyst**        | Uncover and track threat actors targeting the organization   |
| **Executive Management** | Understand the risks the organization faces and what the options are to address their impact |

## Use Cases

| **Function**             | **Use Cases**                                                |
| :----------------------- | :----------------------------------------------------------- |
| **Sec/IT Analyst**       | Integrate TI feeds with other security products<br />Block bad IPs, URLS, domains, files etc |
| **SOC**                  | Use TI to enrich alerts<br />Link alerts together into incidents<br />Tune newly deployed security controls |
| **CSIRT**                | Look for information on the who/what/why/when/how of an incident<br />Analyze root cause to determine scope of the incident |
| **Intel Analyst**        | Look wider and deeper for intrusion evidence<br />Review reports on threat actors to better detect them |
| **Executive Management** | Assess overall threat level for the organization<br />Develop security roadmap |

## Standards and Frameworks

### STIX & TAXII

Cyber Threat Intelligence is shared/exchanged with tools, products or other entities via two (independent) standards - **STIX** and **TAXII**.

**`STIX`** ([**S**tructured **T**hreat **I**nformation E**x**pression](https://oasis-open.github.io/cti-documentation/stix/intro)) - an open source structured **language** and serialization format used to exchange CTI, designed to improve:

- collaborative threat analysis
- automated threat exchange
- automated detection and response, and more
- [STIX examples](https://oasis-open.github.io/cti-documentation/stix/examples)

![STIX Relationship Example - oasis-open.github.io](.gitbook/assets/stix2_relationship_example_2.png)

**`TAXII`** ([**T**rusted **A**utomated E**x**change of **I**ntelligence **I**nformation](https://oasis-open.github.io/cti-documentation/taxii/intro)) - a transport mechanism, an application layer **protocol** for the communication of cyber threat information in a simple and scalable manner, over HTTPS.

- designed to support the exchange of STIX represented CTI
- can be used to share non-STIX data

![TAXII Collections - oasis-open.github.io](.gitbook/assets/taxii_diagram.png)

> STIX states the *what* of T.I. and it's the *packaging*.
>
> TAXII defines the *how* and is the *vehicle carrying* the package.
>
> STIX and TAXII are *machine-readable* and can easily be *automated*.

### [MITRE ATT&CK](https://attack.mitre.org/)

### [Lockheed Martin - Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)

### [The Unified Kill Chain](https://www.unifiedkillchain.com/)

## Applications

- Threat Landscape
- Campain Analysis
- Threat Actor Tracking
- Intelligence Requirements
- Current Intelligence
- Threat Detection & Hunting
- Forensics
- Malware Analysis
- Incident Response
- Vulnerability & Risk Management

## Analyst Competencies

- `Critical Thinking` - problem solving, hypothesis development
- `Data Collection & Examination` - process important or noise information
- `Communication & Collaboration` - writing, public speaking, interpersonal skills
- `Technical Exploitation` - malware analysis, pentesting, social engineering, etc
- `Information Security` - level of awareness, vulnerability research, network defense, incident response
- `Computing Fundamentals` - networking, oprating systems, programming/scripting

> 📌 Be a lifelong **learner**, **connect** with the community, **build** communication skills, **evolve**.

## Resources

- `Awareness`
  - RSS Feed
  - Social Media
  - Blogs
  - Podcasts
- `Education`
  - Self-Initiated
  - CTFs
  - Academic Programs
  - Vendor Certifications & Training
  - Open source tools & Tutorials
- `Networking`
  - Social Media
  - Conferences
  - Infinity Groups

## Certifications

- [C|TIA – Certified Threat Intelligence Analyst](https://www.eccouncil.org/programs/certified-threat-intelligence-analyst-ctia/)
- [GCTI – GIAC Cyber Threat Intelligence](https://www.giac.org/certification/cyber-threat-intelligence-gcti)
- [RCIA – Rocheston Cyberthreat Intelligence Analyst](https://www.rocheston.com/certification/Cyberthreat-Intelligence-Analyst/)
- [CCTIA by the NICCS – Certified Cyber Threat Intelligence Analyst](https://niccs.us-cert.gov/training/search/cybertraining-365/certified-cyber-threat-intelligence-analyst)
- [CTI (Center for TI) Certificates](https://www.centerforti.com/certifications)
- [The Certified Threat Intelligence Analyst – Cyber Intelligence Tradecraft](https://www.treadstone71.com/index.php/cyber-intelligence-training/cyber-intelligence-tradecraft-certification)
- [The OSINT Pathfinder Programme](https://arnoreuser.com/)
- [CPTIA – CREST Practitioner Threat Intelligence Analyst](https://www.crest-approved.org/examination/crest-practitioner-threat-intelligence-analyst/index.html)
- [CRTIA – CREST Registered Threat Intelligence Analyst](https://crest-approved.org/examination/crest-registered-threat-intelligence-analyst/index.html)
- [CCTIM – CREST Certified Threat Intelligence Manager](https://crest-approved.org/examination/crest-certified-threat-intelligence-manager/index.html)

## Links

### Resources Links

> - [TryHackMe - CTI **Learning** Module](https://tryhackme.com/module/cyber-threat-intelligence)
> - [CrowdStrike - **What is** Threat Intelligence? (2022)](https://www.crowdstrike.com/cybersecurity-101/threat-intelligence/)
>   - [APT](https://www.crowdstrike.com/cybersecurity-101/advanced-persistent-threat-apt/)
> - [Heavy.ai - Threat Intelligence **Glossary**](https://www.heavy.ai/technical-glossary/threat-intelligence)
> - [Flashpoint - Threat Intelligence **Lifecycle** (2021)](https://flashpoint.io/blog/threat-intelligence-lifecycle/)
> - [Capgemini - Reinventing Cybersecurity with Artificial Intelligence](https://www.capgemini.com/ca-en/insights/research-library/reinventing-cybersecurity-with-artificial-intelligence/)
> - [SOCRadar - Top 10 Threat Intelligence **Certifications**: Empower Your Analysis Skills (2020)](https://socradar.io/top-10-threat-intelligence-certifications-empower-your-analysis-skills/)
> - [PhishingTackle - CTI strongest **Sources** in 2023](https://phishingtackle.com/articles/cyber-threat-intelligence-jan-2023/)
> - [Oasis CTI Technical Committee - **STIX/TAXII**](https://oasis-open.github.io/cti-documentation/)
> - [Anomali - Resources](https://www.anomali.com/resources)
> - [Anomali - **STIX/TAXII** standards](https://www.anomali.com/resources/what-are-stix-taxii)
> - [Mandiant - Resources](https://www.mandiant.com/resources)
> - [Recorded Future - Resources](https://www.recordedfuture.com/resources)
> - [Anomali - What is Threat Intel [ITA]](https://www.anomali.com/it/resources/what-is-threat-intelligence)
> - [Kaspersky - Threat Intel definition [ITA]](https://www.kaspersky.it/resource-center/definitions/threat-intelligence)
> - [Katie Nickels - A Cyber Threat Intelligence **Self-Study Plan**: Part 1](https://medium.com/katies-five-cents/a-cyber-threat-intelligence-self-study-plan-part-1-968b5a8daf9a)
>   - [Katie Nickels - A Cyber Threat Intelligence **Self-Study Plan**: Part 2](https://medium.com/katies-five-cents/a-cyber-threat-intelligence-self-study-plan-part-2-d04b7a529d36)
>   - [FAQs on Getting Started in Cyber Threat Intelligence](https://medium.com/katies-five-cents/faqs-on-getting-started-in-cyber-threat-intelligence-f567f267348e)
> - [MITRE ATT&CK CTI Training](https://attack.mitre.org/resources/training/cti/)
> - [KnowBe4 Blog - Security Awareness](https://blog.knowbe4.com/)
> - [Awesome Threat Intelligence resources - **Sources, Formats, Frameworks, Platforms, Tools, Research, Standards, Books**](https://github.com/hslatman/awesome-threat-intelligence)
> - [Florian Roth - The Newcomer’s Guide to Cyber Threat Actor Naming](https://cyb3rops.medium.com/the-newcomers-guide-to-cyber-threat-actor-naming-7428e18ee263)
> - [APT Groups and Operations - Google spreadsheet](https://docs.google.com/spreadsheets/d/1H9_xaxQHpWaa4O_Son4Gx0YOIzlcBWMsdvePFX68EKU/edit#gid=1636225066)
> - [Mandiant APT groups](https://www.fireeye.com/current-threats/apt-groups.html)

### Video material

> - [Youtube - threat intelligence analyst](https://www.youtube.com/results?search_query=threat+intelligence+analyst)
> - [You MUST understand Cyber Threat Intelligence to Blue Team w/Samuel Kimmons - Gerald Auger](https://www.youtube.com/watch?v=tWHqHy-MC1U)
> - [Neal Dennis - How to become a cybersecurity threat intelligence professional | Cyber Work Podcast](https://www.youtube.com/watch?v=UH96qGX5vDk)
> - [Amy Bejtlich - Job Role Spotlight: Cyber Threat Intelligence - SANS (2021 Summit)](https://www.youtube.com/watch?v=fvYb5-NxoDc)
> - [Katie Nickles - The Cycle of Cyber Threat Intelligence - SANS (2019)](https://www.youtube.com/watch?v=J7e74QLVxCk)
> - [What does a Cyber Intelligence Analyst Do? | Salary, Certifications, Skills & Tools, Education](https://www.youtube.com/watch?v=DKC-IY_vpJs)

### Tools

> - [Spectral - Top 10 Cyber Threat Intelligence Tools for 2022](https://spectralops.io/blog/top-10-cyber-threat-intelligence-tools-for-2022/)
> - [8 Best Threat Intelligence Feeds to Monitor in 2023](https://cybeready.com/best-threat-intelligence-feeds-to-monitor-in-2023)
> - [Gartner - Security Threat Intelligence Products and Services Raings](https://www.gartner.com/reviews/market/security-threat-intelligence-services)
> - [50 Best Free Cyber Threat Intelligence Tools – 2023](https://gbhackers.com/cyber-threat-intelligence-tools/amp/)
> - Commercial T.I.:
>   - [IBM **X-Force Exchange**](https://exchange.xforce.ibmcloud.com/)
>   - [Anomali **ThreatStream**](https://www.anomali.com/products/threatstream)
>   - [Palo Alto Networks **AutoFocus**](https://www.paloguard.com/Autofocus.asp)
>   - [Mandiant Threat Intelligence](https://www.mandiant.com/advantage/threat-intelligence/subscribe)
>   - [FireEye iSIGHT Intelligence](https://www.threatprotectworks.com/iSIGHT-Intelligence.asp)
>   - [Recorded Future](https://www.recordedfuture.com/)
> - [Cybersecurity and Infrastructure Security Agency (CISA) - Alerts, Reports, Bulletins, Tips](https://www.cisa.gov/uscert/)
> - [Cyber Operations Tracker database](https://www.cfr.org/cyber-operations/)
> - [MISP - Malware Information Sharing Platform](https://www.misp-project.org/)
> - [AlienVault OTX](https://otx.alienvault.com/)
> - [Microsoft Defender T.I.](https://www.microsoft.com/en-us/security/business/siem-and-xdr/microsoft-defender-threat-intelligence)