---
title: Boogeyman 2
updated: 2026-04-30 04:03:21Z
created: 2026-04-29 09:49:14Z
latitude: 22.27602200
longitude: 114.17514710
altitude: 0.0000
---

**Room link:** https://tryhackme.com/room/boogeyman2

**Date:** 29-04-2026

# Introduction

## Artefacts

<img src="../../../_resources/0d0e46578f6c2139e3811c01bd9c36d1.png" alt="0d0e46578f6c2139e3811c01bd9c36d1.png" width="716" height="346" class="jop-noMdConv">

- Copy of the phishing email.
- Memory dump of the victim's workstation.

## Tools

- Volatility - an [open-source framework](https://github.com/volatilityfoundation/volatility3) for extracting digital artefacts from volatile memory (RAM) samples.

```bash
ubuntu@tryhackme$ # Volatility usage:
ubuntu@tryhackme$ vol -f memorydump.raw <plugin>

# To list all available plugins
ubuntu@tryhackme$ vol -f memorydump.raw -h
```

- Olevba - a tool for analysing and extracting **VBA macros** from Microsoft Office documents.
    - **VBA macros** (Visual Basic for Applications macros) are small scripts written in Visual Basic for Applications that are embedded in Microsoft Office documents like Word, Excel, or PowerPoint.
        
        - **Purpose:** They are used to **automate repetitive tasks** and add custom functionality within Office apps.
        - **Security Risk:** They are a common attack vector. Malicious actors often hide malware inside macros. If a user enables the macro when opening a file, the malicious code runs, potentially compromising the system.

```bash
ubuntu@tryhackme$ # Olevba usage:
ubuntu@tryhackme$ olevba document.doc
```

# Spear Phishing Human Resources

## Scenario

Maxine, a Human Resource Specialist working for Quick Logistics LLC, received an application from one of the open positions in the company. Unbeknownst to her, the attached resume was malicious and compromised her workstation.

<img src="../../../_resources/8f8d27b365769b472b6e42201375260e.png" alt="8f8d27b365769b472b6e42201375260e.png" width="780" height="559" class="jop-noMdConv">

## Practice

<img src="../../../_resources/df6d5ee51cd7d89eef4bacaf2d67c2c2.png" alt="df6d5ee51cd7d89eef4bacaf2d67c2c2.png" width="649" height="530" class="jop-noMdConv">

let's start off from the raw phishing email, in the email's header, I can see the email address **from** and **to**.

<img src="../../../_resources/92bc2c69647d6295fa8569d362bec34c.png" alt="92bc2c69647d6295fa8569d362bec34c.png" width="706" height="493" class="jop-noMdConv">

keep going in this file, I catched the attachment named **Resume_WeleyTaylor.doc** with content was encrypted with **base64** format, so suspicious!, let's save this file for futhur investigation.

<img src="../../../_resources/ba999e7c5d88df20def70b633beb7ce2.png" alt="ba999e7c5d88df20def70b633beb7ce2.png" width="691" height="650" class="jop-noMdConv">

by using olevba `olevba Resume_WeleyTaylor.doc` to analysis, I notice that this file running a script that request an internet request to download a malicious image named **update.png** to frauded folder **C:\\ProgramData\\update.js** and then spawn the **wscript.exe** process.

<img src="../../../_resources/c4d1063c8b3a8285476757c22001098f.png" alt="c4d1063c8b3a8285476757c22001098f.png" width="1111" height="751" class="jop-noMdConv">

by using volatility tool to analysis the memory dump file, I see the executable file **wscript.exe** activated. let's find out what happens next.

![2f32733d062cb48152952ead6a6d8144.png](../../../_resources/2f32733d062cb48152952ead6a6d8144.png)

seems like it spawed a new process named **updater.exe**

**![f647d5f418b06836b1d2854b875a4f61.png](../../../_resources/f647d5f418b06836b1d2854b875a4f61.png)**

keep going with the same work above, I see an additional executable file was spawned that named **conhost.exe**. From the file name, I suspense this is something like "connection host" , let me check whether any suspicious internet connection was established.

![428e8ea54a5b01a9d0944637e079d728.png](../../../_resources/428e8ea54a5b01a9d0944637e079d728.png)

using `vol -f WKST-2961.raw windows.netscan`command to check network connection, I see alot of traffic relevant to updater.exe proccess, which is considered as a malicious process that I forementioned. It was seemingly established a connection to external IP address **128.199.95.189:8080**, I assume that is C2 server.

![9c447d35a100f70e959a0916b6707d95.png](../../../_resources/9c447d35a100f70e959a0916b6707d95.png)

now I now where it is.

![73b18156a5247600e5543c37ec4355de.png](../../../_resources/73b18156a5247600e5543c37ec4355de.png)

I also check the Image of the **conhost.exe** and I know that this is a system process. Because this file was activated by a malicious file, so I think that the attacker executed malicious command by CMD or powershell. Therefore, **conhost.exe** was called by system (hidden) to serve I/O stream for it.

![747095641a65bdc26c8654dd0789f0ce.png](../../../_resources/747095641a65bdc26c8654dd0789f0ce.png)

I find out the malicious email with full Image, I think it gonna helpful then.

<img src="../../../_resources/3c4911e1ef66a63ca897955d87475156.png" alt="3c4911e1ef66a63ca897955d87475156.png" width="1536" height="828" class="jop-noMdConv">

Finally, using `strings WKSTN-2961.raw | grep -i "schtasks"` linux comamnd, I catched out the command that attack was used to create a schedule task.

# MITTRE ATTACK mapping

Initial Access

- T1566.001 Spear Phishing Attachment

Execution:

- T1059.001 PowerShell
- T1059.007 JavaScript
- T1053.005 Scheduled Task
- T1204.002 Malicious File

Persistent:

- T1053.005 Scheduled Task

Command & Control:

- T1071.001: Web Protocols (HTTP/HTTPS)
- T1571: Non-Standard Port.
- T1105: Ingress Tool Transfer.

&nbsp;

&nbsp;

&nbsp;