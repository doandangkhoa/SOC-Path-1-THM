---
title: Linux Threat Detection 1
updated: 2026-03-31 06:49:33Z
created: 2026-03-05 08:28:08Z
latitude: 21.02776440
longitude: 105.83415980
altitude: 0.0000
---

# Initial Access via SSH

## Popularity of SSH

- one of the most initial access method on Linux server is an exposed SSH.
- so that leaving their systems vulnerable to brute-force attacks.

## Initial Access via SSH

- much like RDP on Windows
- SSH is both powerful and poorly defended.
- threat groups run vast botnets to scan the exposed SSH and access them in 2 main ways:
    - via a stolen key.
    - breached password.

1\. common risks when using **<span style="color: rgb(241, 196, 15);">key-based authentication</span>**

- Threat actors access a service/source code where private SSH key've been stored
- threat actors use the SSH keys to a server by stealing from admin's laptop

2\. Additional risks when using **<span style="color: rgb(241, 196, 15);">password-based authentication</span>**

- an IT admin sets a week password for testing phase then forgot to revert.
- an IT support enables SSH for a contractor and who set the password to "12345678"
- a network engineer accidentially exposed an old SSH server to the internet.

&nbsp;

# <span style="color: rgb(241, 196, 15);">Detecting SSH Attacks</span>

### SSH Breach Example

- <img src="../../../_resources/6d80c254249e5e4c6e9aaa47bb771be5.png" alt="6d80c254249e5e4c6e9aaa47bb771be5.png" width="898" height="190" class="jop-noMdConv">

## Detecting SSH Attacks

listing all susscessfull SSH logins and analyzing a few fields.

![84c6460fd5b758f769e92a22220271a1.png](../../../_resources/84c6460fd5b758f769e92a22220271a1.png)

How to we prove that these accesses is legitimate?

**Login of Ansible**

- used public-key authentication.
- from internal IP address, likely an Asible automation account.
- login at exactly 14:00 matches periodic task behavior.

<span style="color: rgb(241, 196, 15);">so we'd need futher investigations about it's following activities to prove.</span>

**Logins of jsmith**

- password-based authentication
- login from external IP
- the login time is so suspicous (must be login at night???)

<span style="color: rgb(241, 196, 15);">so we have to dive deeper to mining for more information such as Username, Source IP, Login history, etc.</span>

## Practices

**/var/log/auth.log**

1.  **When did the SSH password brute force start?**`cat /var/log/auth.log | grep -Ei 'Failed'`
2.  **Which four users did the botnet attempt to breach?**`grep "Failed password for" | awk '{if &7 == "Invalid" print $9; else print $7}'`![ec77ca83da5c305dc2b68b7eae3f981b.png](../../../_resources/ec77ca83da5c305dc2b68b7eae3f981b.png)
3.  Which IP managed to breach the root user?

-         `        grep -e 'Accepted' /var/log/auth.log | grep -e 'root'        `        
- ![ec0a9c12855f5211bdf4a04b4fc023a1.png](../../../_resources/ec0a9c12855f5211bdf4a04b4fc023a1.png)

&nbsp;

# <span style="color: rgb(241, 196, 15);">Initial Access via Services</span>

## Linux and public services

Linux systems often host public-facing services/applications such as web servers, email servers, databases, etc.

- <img src="../../../_resources/4f50a3ee6d5eeacc398a7c4ba2b64d3d.png" alt="4f50a3ee6d5eeacc398a7c4ba2b64d3d.png" width="747" height="119" class="jop-noMdConv">

## Using Application Logs

- use web logs to detect a variety of web attacks.
- use database logs to detect suspicious SQL queries.
- use VPN logs to investigate abnormal VPN connection from external network.

## Web as inital access

![f3b05adafc8de50d4189edfcbef576ef.png](../../../_resources/f3b05adafc8de50d4189edfcbef576ef.png)

**Web Logs Analysis**

- `10.14.105.255` is likely the attacker's IP
- The `/ping` page is vulnerable and allows code execution
- The attacker executed OS commands like `whoami` and `ls` --> <span style="color: rgb(241, 196, 15);">command injection</span>
- The entire system is now at risk because of the TryPingMe vulnerability

# Detecting Service Breach

## Building Process Tree

- <img src="../../../_resources/5cf87efc798659785db4ffc2901552cb.png" alt="5cf87efc798659785db4ffc2901552cb.png" width="1353" height="397" class="jop-noMdConv">

## Auditd and Process Tree

- when defined exactly the suspicous command.
- Then you should use Auditd logs to find out what really happens prior:
    - first by command: `ausearch -i -x whoami` to isolate the suspicious command and specify its ppid (ex = 3905).
    - second, `ausearch -i --pid 3905` for further investigations about its parent.
    - Do it is the same the second one until you get ppid = 1 (systemd).
- Now you've already made up a tree proccess and had a obvious evidence about the breach.
- ![f6f1c5873e385923000b8bc45f3bbfc6.png](../../../_resources/f6f1c5873e385923000b8bc45f3bbfc6.png)
- ![8d31311356a67e36160b912c883ac24f.png](../../../_resources/8d31311356a67e36160b912c883ac24f.png)

# <span style="color: rgb(241, 196, 15);">Advanced Initial Access</span>

## Human-Led Attacks

- <img src="../../../_resources/22a64f0ac91940109ab723f65a2df7e3.png" alt="22a64f0ac91940109ab723f65a2df7e3.png" width="720" height="203" class="jop-noMdConv">

## Supply Chain Compromise

these attacks breach a software first, and then infect all its user with the malicious update (<span style="color: rgb(241, 196, 15);">runs malicious commands</span>). the attack can come from anywhere, anytime:

- A [backdoor in the XZ Utils](https://www.akamai.com/blog/security-research/critical-linux-backdoor-xz-utils-discovered-what-to-know) library that is a part of SSH nearly led to a breach of millions of Linux servers
- A [breach of the tj-actions](https://www.cisa.gov/news-events/alerts/2025/03/18/supply-chain-compromise-third-party-tj-actionschanged-files-cve-2025-30066-and-reviewdogaction) resulted in a leak of thousands of secrets, like SSH keys and access tokens

## Detecting the Attacks

- all of initial access techniques can be uncovered through a process tree analysis. So, when you meet a SIEM alert on a suspicious command, lets keep your brain, analyzing carefully to make the process tree and then you can see what actions happend in your system.
- <img src="../../../_resources/ff3ec5afa5059f7dc816d5b5d4fd4fdc.png" alt="ff3ec5afa5059f7dc816d5b5d4fd4fdc.png" width="1394" height="292" class="jop-noMdConv">