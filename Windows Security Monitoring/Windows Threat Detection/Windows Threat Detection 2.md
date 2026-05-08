---
title: Windows Threat Detection 2
updated: 2026-03-31 06:39:54Z
created: 2026-03-01 16:09:26Z
latitude: 20.21299690
longitude: 105.92299000
altitude: 0.0000
---

# Discovery Overview

<img src="../../../_resources/e298d2d6963d1abc3dac204c870e5845.png" alt="e298d2d6963d1abc3dac204c870e5845.png" width="756" height="222" class="jop-noMdConv">

# Discovery Commands

<img src="../../../_resources/c772bf5324e22bfabef4ebb76acaad7a.png" alt="c772bf5324e22bfabef4ebb76acaad7a.png" width="1068" height="427" class="jop-noMdConv">

# Discovery Process

**How attacker control the victim**

<img src="../../../_resources/00c9d368a7849f3b32cd5d39eef97232.png" alt="00c9d368a7849f3b32cd5d39eef97232.png" width="1218" height="392" class="jop-noMdConv">

# Detecting Discovery

**Discovery via CMD![bc52641383d907962b87cdaf205e68a6.png](../../../_resources/bc52641383d907962b87cdaf205e68a6.png)**

**Discovery via GUI**

- using RDB protocol
- ![24d856767765ae406daa95afe3f16681.png](../../../_resources/24d856767765ae406daa95afe3f16681.png)

## Process Tree

![a2c8b047861f3e82182c0650918dfd8e.png](../../../_resources/a2c8b047861f3e82182c0650918dfd8e.png)

# <span style="color: rgb(241, 196, 15);">Collection Overview</span>

## Searching Secrets

![66637d9ef675c5a085290d6405777a6b.png](../../../_resources/66637d9ef675c5a085290d6405777a6b.png)

## Collection target

the most of the sensitive data can be stored in simple files and also hidden in the registry or in process memory such as:

- Exfiltrate stolen data to DropBox, Mega, Amazon S3, or other trusted cloud storage services ([Examples](https://attack.mitre.org/techniques/T1567/002/#:~:text=Procedure%20Examples))
- Exfiltrate stolen data to known code repositories like GitHub or messengers like Telegram ([Example](https://cyberint.com/blog/research/the-new-infostealer-in-town-the-continental-stealer/#:~:text=offers%20a%20Telegram%20bot%20notification%20feature%20that%20informs%20users))
- Or just create a trustworthy-looking domain like "windows-updates.com" and send the data there

# <span style="color: rgb(241, 196, 15);">Detecting Collection</span>

![2250985c35ffe93cb1349d98d6efb225.png](../../../_resources/2250985c35ffe93cb1349d98d6efb225.png)

# <span style="color: rgb(241, 196, 15);">Ingress Tool Transfer</span>

<span style="color: rgb(255, 255, 255);">threat actors may need to download more tools to achieve their goals, for example:</span>

- A script to automate Discovery and find common vulnerabilities like [Seatbelt](https://github.com/GhostPack/Seatbelt)
- A tool to extract saved passwords or OS credentials like [Mimikatz](https://github.com/gentilkiwi/mimikatz)
- A fully functional Remote Access Trojan (RAT) like [Remcos RAT](https://www.checkpoint.com/cyber-hub/threat-prevention/what-is-malware/remcos-malware/)
- Finally, a ransomware binary to encrypt the system after the data is stolen

## Common Transfer Methods

![67c5bcdb1ddaa45666ed19505ba3f367.png](../../../_resources/67c5bcdb1ddaa45666ed19505ba3f367.png)

## Complete event chain

![24262214cb914a0de82ea04012d5e808.png](../../../_resources/24262214cb914a0de82ea04012d5e808.png)

&nbsp;