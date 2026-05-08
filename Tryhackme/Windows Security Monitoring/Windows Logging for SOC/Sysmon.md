---
title: Sysmon
updated: 2026-02-27 17:09:37Z
created: 2026-02-27 07:32:33Z
latitude: 21.02776440
longitude: 105.83415980
altitude: 0.0000
---

# Overview

- **Sysmon** is a tool that gathers detailed and high-quality logs as well as event tracing in identifying anomalies in system.
- commonly linked with SIEM system.
- installed on an endpoint, start early in the Windows boot process. These logs collected from this endpoint will be forwarded to SIEM for further analysis.
- Events within Sysmon are stored in `` `Applications and Services Logs/Microsoft/Windows/Sysmon/Operational` ``

Some important Event IDs Sysmon :

**Event ID 1: Process Creation**

- look for any processes that have been created.
- use to find out known suspicious processes or with abnormal typos.
- `<RuleGroup name="" groupRelation="or">`  
            `        <ProcessCreate onmatch="exclude">`  
                   `               <CommandLine condition="is">C:\Windows\system32\svchost.exe -k appmodel -p -s camsvc</CommandLine>`  
            `        </ProcessCreate>`  
    `</RuleGroup>`

**Event ID 3: Network Connection**

- look for events that occur remotely.
- include files and resources of suspicious binaries as well as opened ports.
- `<RuleGroup name="" groupRelation="or">`  
    `<NetworkConnect onmatch="include">`  
    `<Image condition="image">nmap.exe</Image>`  
    `<DestinationPort name="Alert,Metasploit" condition="is">4444</DestinationPort>`  
    `</NetworkConnect>`  
    `</RuleGroup>`

**Event ID 7: Image Loaded**

- look for DLLs loaded by processes, which is useful when hunting for <span style="color: rgb(230, 126, 35);">DLL injection</span> and <span style="color: rgb(230, 126, 35);">DLL hijacking attacks.</span>
- <span style="color: rgb(241, 196, 15);">`<RuleGroup name="" groupRelation="or"><ImageLoad onmatch="include"><ImageLoaded condition="contains">\Temp\</ImageLoaded></ImageLoad></RuleGroup>`</span>
- the above code snippet will look for any DLLs that have been loaded within the \\Temp\\ directory. If any DLL is loaded within this directory it can be considered as an anomaly.

Event ID 8: CreateRemoteThread

monitor for processes injecting code into other processes.

&nbsp;