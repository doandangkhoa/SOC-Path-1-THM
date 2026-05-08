---
title: Intro to Cyber Threat Intel
updated: 2026-04-09 17:35:06Z
created: 2026-04-06 04:24:51Z
latitude: 22.27602200
longitude: 114.17514710
altitude: 0.0000
---

In concrete terms, CTI seeks to answer three enssential questions:

1.  Who, or Whatm is on the other end of this alert indicator?
2.  What was their behaviour in the past?
3.  How does my organisation respond, and what should I do right now?

From Raw Data to Usable Intelligence

Information security literature distinguishes **data, information**, and **intelligence**, yet the three terms often blur in daily conversation. Making them explicit clarifies an analyst's objective.

| Layer | Definition | Alert-queue example | SOC L1 action |
| --- | --- | --- | --- |
| **Data** | An unprocessed observable | `45.155.205.3 :443` | Capture the artefact. |
| **Information** | Data plus factual annotation | *IP registered to Hetzner, first seen 2023-07-14* | Record attributes. |
| **Intelligence** | Analysed information that answers *so-what* | *IP belongs to the current BumbleBee C2; block immediately* | Escalate or suppress. |

During the ascent of data to intelligence, three more labels become paramount for analysts to know.

- **Indicator of Compromise (IOC)**: Evidence of a breach, such as a C2 address in the logs.
- **Indicator of Attack (IOA)**: A malicious action, such as PowerShell launching an unknown service, is underway.
- **Tactics, Techniques, and Procedures (TTP)**: An adversary's detailed methodologies expressed in MITRE ATT&CK IDs and descriptions.

## Indicator Types Essential to First-Line Triage

| Indicator | Example | First Resources | Associated IOA or TTP Examples |
| --- | --- | --- | --- |
| **IPv4 / IPv6** | `45.155.205.3` | • WHOIS (ASN, allocation date) · VirusTotal Relations· Shodan banner scan | IOA: Repeated SSH failures TTP: `T1110.003`Password Guessing |
| **Domain / FQDN** | `malicious-updates[.]net` | • WHOIS age · RiskIQ or SecurityTrails passive-DNS · urlscan.io | IOA: surge of DNS queries to a 24-hour-old domain |
| **URL** | `hxxp://malicious-updates[.]net/login` | • URLhaus reputation · urlscan.io behaviour graph · Any.Run dynamic run (network off) | IOA: Browser POST to /gateway.php with payload |
| **File hash** | `e99a18c428cb38d5…` | • VirusTotal static & dynamic · Hybrid-Analysis · MalShare corpus | TTP: T1055 Process Injection into regsvr32.exe |
| **E-mail address** | `billing@evil-corp.com` | • MXToolbox header analysis • Have I Been Pwned | IOA: SPF failure plus recent domain registration |
| **Local artefact** | `HKCU\Software\Run\updater.exe` | • Sigma rules · EDR prevalence query · Vendor knowledge bas | TTP: T1060.001 Registry Run Keys |

## Threat Intelligence Classifications

- **Strategic intel**: High-level intelligence that looks into the organisation's threat landscape and maps out the risk areas based on trends, patterns and emerging threats that may impact business decisions. An example is an annual ransomware trends report predicting a shift to data-wiping extortion in healthcare.
    
- **Tactical intel**: Assessments of adversaries' behaviours through analysis of tactics, techniques, and procedures (TTPs). This can be in the form of Advisory notes, such as detailing new T1059.005 (Visual Basic) abuse in malspam.
    
- **Operational intel**: Campaign-specific details about the motives and intent to perform an attack. This is useful for understanding the critical assets available in the organisation (people, processes, and technologies) that may be targeted.
    
- **Technical intel**: Atomic indicators and artefacts such as IPs and hashes related to an attack.
    

# CTI Lifecycle

Cyber Threat Intelligence follows a **six-phase** intelligence lifecycle that transforms raw data into contextualised and action-oriented insights geared towards triaging security incidents.

<img src="../../_resources/b8366e514123f1f55bec98262ed88430.png" alt="b8366e514123f1f55bec98262ed88430.png" width="676" height="474" class="jop-noMdConv">

<span style="color: rgb(241, 196, 15);">**Traffic Light Protocol (TLP)—A Primer for Proper Sharing**</span>

The **Traffic Light Protocol** is a four-colour labelling scheme defined by FIRST.org that governs how widely intel may be shared.

<span style="color: rgb(241, 196, 15);">**Intel Formats**</span>

- intelligence in various formats / protocols.
- One format in which threat intel can be found is Structured Threat Information Expression (**STIX).** This JSON schema was developed to describe and specify threat indicators, relationships, and context in a machine-readable form.

&nbsp;

# CTI standards & framework

## MITRE ATT&CK

<img src="../../_resources/ffdf26931cbf6114c4ec1a58a46711b2.png" alt="ffdf26931cbf6114c4ec1a58a46711b2.png" width="1087" height="584">

As an L1 analyst, you can use the matrix during an investigation in the following way:

1.  Match the behaviour in the alert to a tactic/technique pair.
2.  Write the ID in your triage note: "Observed **T1071.001** (web-based C2) against FINANCE-TRYHATME-00".
3.  Hand the note to Level 2 or Incident Response; they instantly know which mitigations and threat-actor profiles apply.

## MITRE D3FEND

## Cyber Kill Chain

<img src="../../_resources/e7eb5ee69692a7cca67cb20bc3a77d4d.png" alt="e7eb5ee69692a7cca67cb20bc3a77d4d.png" width="842" height="375">

| Technique | Purpose | Examples |
| :--- | :--- | :--- |
| Reconnaissance | Obtain information about the victim and the tactics used for the attack. | Harvesting emails, OSINT, and social media, network scans |
| Weaponisation | Malware is engineered based on the needs and intentions of the attack. | Exploit with a backdoor, a malicious Office document |
| Delivery | Covers how the malware would be delivered to the victim's system. | Email, weblinks, USB |
| Exploitation | Breach the victim's system vulnerabilities to execute code and create scheduled jobs to establish persistence. | EternalBlue, Zero-Logon, etc. |
| Installation | Install malware and other tools to gain access to the victim's system. | Password dumping, backdoors, and remote access trojans |
| Command & Control | Remotely control the compromised system, deliver additional malware, move across valuable assets and elevate privileges. | Empire, Cobalt Strike, etc. |
| Actions on Objectives | Fulfil the intended goals for the attack: financial gain, corporate espionage, and data exfiltration. | Data encryption, ransomware, and public defacement |

## CVEs, CVSS, and the NVD

- **CVE (Common Vulnerabilities and Exposures)** — provides a catalogue number for discovered vulnerabilities, e.g., *CVE-2023-4863*.
- **CVSS (Common Vulnerability Scoring System)** — a 0–10 severity scale with temporal and environmental modifiers for vulnerabilities.
- **NVD (National Vulnerability Database)** — the canonical repository that links CVE numbers to CVSS scores, exploits, and affected products.

## Sharing and Processing Intel

We previously discussed platforms and feeds from which threat intelligence can be retrieved. When organisations publish fresh indicators, every peer that consumes and validates them strengthens the collective defence and feeds back improvements. This information looks to hinge on two standards: **STIX and TAXII.**

- **STIX**: We mentioned STIX previously as the structured JSON schema for describing threat information.
- **TAXII**: The Trusted Automated eXchange of Indicator Information is a set of secure APIs used to exchange threat intelligence in near real-time for detection, prevention, and mitigation of threats.  
    It supports two sharing models: **Collection**, which ensures threat intel is collected and hosted by a producer, and **Channel**, which publishes threat intel to users from a central server.

&nbsp;

# Practical Analysis

![9cb2eea42a99e7deeb46aa7110a221d0.png](../../_resources/9cb2eea42a99e7deeb46aa7110a221d0.png)

&nbsp;