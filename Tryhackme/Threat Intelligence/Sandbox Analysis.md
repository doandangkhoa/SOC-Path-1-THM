---
title: Sandbox Analysis
updated: 2026-04-10 13:56:07Z
created: 2026-04-10 07:06:39Z
latitude: 21.02776440
longitude: 105.83415980
altitude: 0.0000
---

# Dynamic Analysis

## Sandboxing Tools

the most widely used tools for detonating wild malware are **Hybrid Analysis** and **Joe Sandbox:**

- Hybrid Analysis (HA) focuses on behaviour trees and a clean MITRE ATT&CK heatmap. It is suitable for a fast executive summary.
- Joe Sandbox (JS): goes deep, covering system calls, strings, and memory dumps. Great for reverse engineers and detection engineers.

## A. Hybrid Analysis Report

<img src="../../_resources/41dbf826bf6bea89f048f7310571f2fe.png" alt="41dbf826bf6bea89f048f7310571f2fe.png" width="1044" height="518" class="jop-noMdConv">

Additionally, we can gather all file details and information to inform our threat intelligence report.

<img src="../../_resources/71d7fe46236ec4ac573828eaddbf386c.png" alt="71d7fe46236ec4ac573828eaddbf386c.png" width="1040" height="565">

## Sandboxing Limitations

1.  **Sandbox Evasion Techniques**
    
    Threat actors would design their payloads to detect and evade sandboxes, leading to false negatives. Some of the common evasion tactics used include:
    
    - Environment Awareness Checks: Malware checks for signs of virtualised/sandboxed environments
    - Anti-Debugging & Anti-Sandboxing Tricks: Malware conducts debugger detection and checks for unique hardware IDs.
2.  **Limited Execution Time & Coverage**
    
    Most sandboxes terminate analysis after 2-5 minutes, which means multi-stage malware may not fully execute. Additionally, time-delayed attacks will evade detection. As previously covered, this would mean cross-referencing other threat intelligence resources.
    
3.  **Encrypted & Obfuscated Traffic**
    
    Many sandboxes cannot decrypt SSL/TLS traffic, leading to blind spots. This may result in HTTPS C2 Traffic appearing with no payload visibility or the malware utilising DNS tunnelling to hide data in DNS queries.
    
4.  **Fileless & Living-off-the-Land (LotL) Malware**
    
    Some threats never touch disk, bypassing traditional sandbox analysis by employing PowerShell Attacks and WMI Persistence.
    

&nbsp;