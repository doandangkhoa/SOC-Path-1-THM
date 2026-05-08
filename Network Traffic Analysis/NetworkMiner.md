---
title: NetworkMiner
updated: 2026-04-24 07:00:20Z
created: 2026-04-23 16:06:20Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

- NetworkMiner is an open-source traffic sniffer, PCAP handler and protocol analyser to detect OS, sessions, hostnames, open port, etc.
- Developed and still maintained by Netresec.

# NetworkMiner in Forensics

NetworkMiner is a great way to help, as it provides quick and useful hints on where to start from, sepcifically:

- Context of captured hosts like their MAC, IP, hostname or OS information.
- List of potential attack indicators or anamolies like traffic spikes or port scans.
- Tool or toolkits used to perform the potential attacks like Nmap.

## Supported data types

- Live traffic
- Traffic captures (PCAP)

# What is Network Miner?

## NetworkMiner in a Nutshell

|     |     |
| --- | --- |
| **Capability** | **Description** |
| **Traffic sniffing** | It can intercept the traffic, sniff it, and collect and log packets that pass through the network. |
| **Parsing PCAP files** | It can parse pcap files and show the content of the packets in detail. |
| **Protocol analysis** | It can identify the used protocols from the parsed pcap file. |
| **OS fingerprinting** | It can identify the used OS by reading the pcap file. This feature strongly relies on [Satori](https://github.com/xnih/satori/) and [p0f.](https://lcamtuf.coredump.cx/p0f3/) |
| **File Extraction** | It can extract images, HTML files and emails from the parsed pcap file. |
| **Credential grabbing** | It can extract credentials from the parsed pcap file. |
| **Clear text keyword parsing** | It can extract cleartext keywords and strings from the parsed pcap file. |

## Operating Modes

Three are two main operating modes:

**Sniffer Mode**:

- not suggested, as it is not a dedicated sniffer like **wireshark** or **tcpdump**.

**Packet Parsing/Processing:**

- parse traffic captures to have a quick overview and information on the investigated capture.
- is mainly suggested to grab the "low hanging fruit" before diving into a deeper investigation.

## Pros and Cons

<div class="joplin-table-wrapper"><table border="1" style="border-collapse: collapse; width: 42.1121%; border-width: 1px; margin-left: 0px; margin-right: auto;" class="jop-noMdConv"><colgroup class="jop-noMdConv"><col style="width: 50%;" class="jop-noMdConv"><col style="width: 50%;" class="jop-noMdConv"></colgroup><thead class="jop-noMdConv"><tr class="jop-noMdConv"><th scope="col" class="jop-noMdConv"><strong class="jop-noMdConv">Pros</strong></th><th scope="col" class="jop-noMdConv"><strong class="jop-noMdConv">Cons</strong></th></tr></thead><tbody class="jop-noMdConv"><tr class="jop-noMdConv"><td class="jop-noMdConv"><ul class="jop-noMdConv"><li class="jop-noMdConv">OS fingerprinting</li><li class="jop-noMdConv">Easy file extraction</li><li class="jop-noMdConv">Credential grabbing</li><li class="jop-noMdConv">Clear text keyword parsing</li><li class="jop-noMdConv">Overall overview</li></ul></td><td class="jop-noMdConv"><ul class="jop-noMdConv"><li class="jop-noMdConv">Not useful in active sniffing</li><li class="jop-noMdConv">Not useful for large pcap investigation</li><li class="jop-noMdConv">Limited filtering</li><li class="jop-noMdConv">Not built for manual traffic investigation</li></ul></td></tr></tbody></table></div>

## Differences Between Wireshark and NetworkMiner

|     |     |     |
| --- | --- | --- |
| **Feature** | **NetworkMiner** | **Wireshark** |
| Purpose | Quick overview, traffic mapping, and data extraction | In-Depth analysis |
| GUI | ✅   | ✅   |
| Sniffing | ✅   | ✅   |
| Handling PCAPS | ✅   | ✅   |
| OS Fingerprinting | ✅   | ❌   |
| Parameter/Keyword Discovery | ✅   | Manual |
| Credential Discovery | ✅   | ✅   |
| File Extraction | ✅   | ✅   |
| Filtering Options | Limited | ✅   |
| Packet Decoding | Limited | ✅   |
| Protocol Analysis | ❌   | ✅   |
| Payload Analysis | ❌   | ✅   |
| Statistical Analysis | ❌   | ✅   |
| Cross-Platform Support | ✅   | ✅   |
| Host Categorisation | ✅   | ❌   |
| Ease of Management | ✅   | ✅   |

&nbsp;

# Tool Overview

## Hosts

The "hosts" menu shows the identified hosts in the pcap file. This section provides information on:

- IP address
- MAC address
- OS type
- Open ports
- Sent/Received packets
- Incoming/Outgoing sessions
- Host details

OS fingerprinting uses the Satori GitHub repo and p0f, and the MAC address database uses the mac-ages GitHub repo.

## Sessions

The session menu shows detected sessions in the pcap file. This section provides information on:

- Frame number
- Client and server address
- Source and destination port
- Protocol
- Start time

![d7c3bc9ee661477d141c925827d76e1e.png](../../_resources/d7c3bc9ee661477d141c925827d76e1e.png)

## DNS

The DNS menu shows DNS queries with details. This section provides information on:

- Frame number
- Timestamp
- Client and server
- Source and destination port
- IP TTL
- DNS time
- Transaction ID and type
- DNS query and answer
- Alexa Top 1M

![1e1255ea08a272a88ec98ada4a9f5066.png](../../_resources/1e1255ea08a272a88ec98ada4a9f5066.png)

## Credentials

The credentials menu shows extracted credentials and password [hashes](https://tryhackme.com/room/hashingcrypto101) from investigated pcaps.

You can use [Hashcat](https://tryhackme.com/room/crackthehashlevel2) ([GitHub](https://github.com/hashcat/hashcat)) and [John the Ripper](https://tryhackme.com/room/johntheripper0) (<ins>GitHub</ins>) to decrypt extracted credentials.

NetworkMiner can extract credentials including:

- Kerberos hashes
- NTLM hashes
- RDP cookies
- HTTP cookies
- HTTP requests
- IMAP
- FTP
- SMTP
- MS SQL

## Files

The file menu shows extracted files from investigated pcaps. This section provides information on:

- Frame number
- Filename
- Extension
- Size
- Source and destination address
- Source and destination port
- Protocol
- Timestamp
- Reconstructed path
- Details
- <img src="../../_resources/29d7236b1be95387fefeb7e4e82b36e9.png" alt="29d7236b1be95387fefeb7e4e82b36e9.png" width="1082" height="609" class="jop-noMdConv">

## Images

The file menu shows extracted images from investigated pcaps.

<img src="../../_resources/44067811a9f6a6e4ed0cea4372a053ba.png" alt="44067811a9f6a6e4ed0cea4372a053ba.png" width="576" height="624" class="jop-noMdConv">

## Parameters

The file menu shows extracted keywords from investigated pcaps. This section provides information on:

- Frame number
- Timestamp
- Keyword
- Context
- Source and destination host
- source and destination port

**How to filter keywords:**

- Add keywords
- Reload case files!

![a007fd27edff81593e707f3413a12722.png](../../_resources/a007fd27edff81593e707f3413a12722.png)

## Messages

The messages menu shows extracted emails, chats and messages from investigated pcaps. This section provides information on:

- Frame number
- Source and destination host
- Protocol
- Sender (From)
- Receiver (To)
- Timestamp
- Size

![e4761809e69f74600cb34a289f6489e5.png](../../_resources/e4761809e69f74600cb34a289f6489e5.png)

## Anomalies

The anomalies menu shows detected anomalies in the processed pcap![ba0e4f8a3ae45ed722f39f1052d1b538.png](../../_resources/ba0e4f8a3ae45ed722f39f1052d1b538.png)

# Version Differences

## Mac Address Processing

- NetworkMiner versions after version 2 can process MAC address specific correlation
- This option will help you identify if there is a MAC Address conflict.
- ![6f7a60d2cb53be7d0cd629af6f0068c9.png](../../_resources/6f7a60d2cb53be7d0cd629af6f0068c9.png)

## Frame Processing

- This option provides the number of frames and essential details about the frames.
- ![c800cd3715b8b871ce3401ec6240f3ab.png](../../_resources/c800cd3715b8b871ce3401ec6240f3ab.png)

&nbsp;

&nbsp;