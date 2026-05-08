---
title: Phishing Analysis Tools
updated: 2026-04-27 03:06:25Z
created: 2026-04-27 02:28:37Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

# Identify Artifacts

## Header Artifacts

When you begin your analysis with the email header, you should take note of the following points:

- **Sender email address:** Where did the email originate from?
- **Sender IP address:** What is the source IP, and what does a reverse lookup reveal?
- **Email subject line:** Does it contain urgency or a call to action?
- **Recipient email address:** Who is the intended recipient (To/CC/BCC)?
- **Reply-To email address:** Where are responses being directed?
- **Date and time:** When was the email sent?

## Body Analysis

Next comes the email body, which has its own set of artifacts to be aware of:

- **URLs and hyperlinks:** Identify all links and expand shortened URLs to reveal their true destination
- **Attachment name(s):** What files are included, and do their names or extensions appear suspicious
- **Attachment hash:** Generate a hash value for threat intelligence lookups

# Email Header Analysis

## Mail Header Analysis

**[Messageheader](https://toolbox.googleapps.com/apps/messageheader/analyzeheader),** part of the Google Admin Toolbox, helps analyze email headers.

<img src="../../_resources/6250b9c850524c3513166336da9e45cd.png" alt="6250b9c850524c3513166336da9e45cd.png" width="930" height="378" class="jop-noMdConv">

Another great tool, **[Message Header Analyzer](https://mha.azurewebsites.net/)**

<img src="../../_resources/5e7ccf4dd83dfea75f2c406af59943ff.png" alt="5e7ccf4dd83dfea75f2c406af59943ff.png" width="944" height="423" class="jop-noMdConv">

## IP and URL Reputation Analysis

**[IPinfo](https://ipinfo.io/)** is a simple and effective tool for gathering information about an IP address.

<img src="../../_resources/f1f83302ae0fe56521f76984430817fb.png" alt="f1f83302ae0fe56521f76984430817fb.png" width="693" height="327" class="jop-noMdConv">

**[URLScan.io](https://urlscan.io/)** is a tool that enables analysts to safely investigate websites without visiting them directly.

<img src="../../_resources/0555b89ef6e01129136a90d19f9ac186.png" alt="0555b89ef6e01129136a90d19f9ac186.png" width="901" height="370" class="jop-noMdConv">

Talos **[IP & Domain Reputation Center](https://talosintelligence.com/reputation_center/)** is a threat intelligence tool from Cisco that enables analysts to assess the reputation of IP addresses, domains, and networks.

<img src="../../_resources/be6cecc22705a4b4dfb65f0465df110e.png" alt="be6cecc22705a4b4dfb65f0465df110e.png" width="915" height="376" class="jop-noMdConv">

# Email Body Analysis

Copy link address

![09f74e813268a7c5e0b6e7f087c05d73.png](../../_resources/09f74e813268a7c5e0b6e7f087c05d73.png)

Another effective way to identify URLs in an email is to use a **[URL extraction tool](https://www.convertcsv.com/url-extractor.htm)**

Tools like **[CyberChef](https://gchq.github.io/CyberChef/#recipe=Extract_URLs%28false,false,false%29)** can also perform this function

**Email Attachments**

Once safely obtained, you can use tools like the `sha256sum` command in a Linux environment to generate a hash value for further analysis and reputation checks.

![d609f5742c3e5d743c2d20408250a366.png](../../_resources/d609f5742c3e5d743c2d20408250a366.png)

Let's return to Talos **[IP & Domain Reputation Center](https://talosintelligence.com/reputation_center/)** analyze the hash value

<img src="../../_resources/032952b21c6e742b973623fdc1d4a9e0.png" alt="032952b21c6e742b973623fdc1d4a9e0.png" width="885" height="422" class="jop-noMdConv">

**[VirusTotal](https://www.virustotal.com/gui/)** is a widely used tool that allows analysts to check the reputation of files, URLs, IP addresses, and domains using data from dozens of security vendors.

<img src="../../_resources/3d3bace6cbfc62b761505c90525a97e9.png" alt="3d3bace6cbfc62b761505c90525a97e9.png" width="935" height="423" class="jop-noMdConv">

# Malware Sandboxes

**[ANY.RUN](https://app.any.run/)** is an interactive malware sandbox that allows analysts to safely execute and observe suspicious files and URLs in real time.

<img src="../../_resources/874de33c01d5b8483782435ea952b6ab.png" alt="874de33c01d5b8483782435ea952b6ab.png" width="885" height="314">

[**Hybrid Analysis**](https://hybrid-analysis.com/) is a free malware analysis sandbox that allows analysts to upload and examine suspicious files in a controlled environment.

<img src="../../_resources/ae32b70824f23195c4e935dfa98b2eea.png" alt="ae32b70824f23195c4e935dfa98b2eea.png" width="508" height="417">

OESecurity created the [**JOESandbox**](https://www.joesandbox.com/) for advanced malware analysis.

<img src="../../_resources/28adb7ca13885828723da16d9f13dc7e.png" alt="28adb7ca13885828723da16d9f13dc7e.png" width="801" height="379">

&nbsp;