---
title: Incident Handling With Splunk
updated: 2026-04-22 13:47:37Z
created: 2026-04-21 08:51:22Z
latitude: 22.27602200
longitude: 114.17514710
altitude: 0.0000
---

# Incident Handling Life Cycle

<img src="../../../_resources/c953995c7c04452f99040b1c961efc69.png" alt="c953995c7c04452f99040b1c961efc69.png" width="647" height="437" class="jop-noMdConv">

The incident Handling process is divided briefly into 4 phase below:

## 1\. Preparation

- That means documenting the requirements, defining the policies, incorporating the security controls to monitor like EDR/SIEM/IDS/IPS, etc.
- also includes hiring/trainning the staff.

## 2\. Detection and Analysis

- This phase covers getting alert from SIEM, then investigating the alert to find the root cause.
- includes hunting for the unknown threat within the organization.

## 3\. Containment, Eradication, and Recovery

- This phase cover the actions needed to prevent the incident from spreading  and securing the network
    - isolating the infected host, clearing the network from the infection traces, gainning control back, etc.

## 4\. Post-Incident Activity / Lessons Learnt

identifying the loopholes in the organization's security posture, which led to intrusion, and improving so that the attack does not happen next time.

&nbsp;

# Incident Handling: Scenario

## Cyber Kill Chain

- We will follow the CBC Model and map the attacker's activity in each phase.
- We will also utilize additional OSINT tools to fill the gap in the kill chain.
- It's not necessary to follow the sequence of the phases (abitrary order).

## Scenario

<img src="../../../_resources/49e107314b20ed77b5930662dff29d82.png" alt="49e107314b20ed77b5930662dff29d82.png" width="874" height="534" class="jop-noMdConv">

# Reconnaissance Phase

is an attempt to collect information about the attacker's target (system in use, web app, employees, location, etc.)

## Start Analysis

examining any reconnaissance attempt against the webserver `imreallynotbatman.com`:

- take a look at the **network traffic,** where all inbound communication towards our web server are logged contains **web traffic.**

&nbsp;

1.  Start by searching for the domain. **Search Query**: `index=botsv1 imreallynotbatman.com`
    
    In the `sourcetype` field, we saw that the following log sources contain the traces of this search term.
    
    1.  1.  Suricata
        2.  stream:http
        3.  fortigate_utm
        4.  iis
2.  Identify the IP address attempting to perform reconnaissance activity.
    
    - looking at the log source `stream:http`, which contains http traffic log.
    - examine the `src_ip`
    - ![b6fbc15f7864ed5bca6c790b74263170.png](../../../_resources/b6fbc15f7864ed5bca6c790b74263170.png)
3.  Validate the IP that is scanning
    
    - dig futher into the weblogs

# Exploitation Phase

The attacker needs to exploit the vulnerability to gain access to the system/server.

## **Count**

following query to see the number of counts by each source IP against the webserver.

- **Search Query**:`index=botsv1 imreallynotbatman.com sourcetype=stream* | stats count(src_ip) as Requests by src_ip | sort - Requests`
- **<img src="../../../_resources/dcfa284b5a1ae9895d7aa0b9d7dde241.png" alt="dcfa284b5a1ae9895d7aa0b9d7dde241.png" width="1034" height="322" class="jop-noMdConv">**
- click to `Visualization --> Select Visualization` to see the different format of the result.
- **<img src="../../../_resources/982ccb02f7b9c66e1f6e008afdb8b1eb.png" alt="982ccb02f7b9c66e1f6e008afdb8b1eb.png" width="1031" height="412" class="jop-noMdConv">**

narrow down the results by querying the IP of the web server is `192.168.250.70`

- **Search Query:** `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70"`
- ![e47d10ffd5d33e7ece588222cbca4664.png](../../../_resources/e47d10ffd5d33e7ece588222cbca4664.png)

Lets take a look on the highest accessing IP and forwards to the `stream:http` logs:

- ![046e41cdd9bbfb2436a755f18dde2ae5.png](../../../_resources/046e41cdd9bbfb2436a755f18dde2ae5.png)
- we can see a lot of POST request, lets see who make this requests.
    - **Search Query:** `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST`.
    - ![38a86c2530cfa1ccbafa9264a9af3dd9.png](../../../_resources/38a86c2530cfa1ccbafa9264a9af3dd9.png)
    - The result above show that the `IP 40.80.148.42` makes a lot of `POST requests` in a suspicious way to the webserver.
    - a little search on the internet for the admin login page pf the Joomla CMS will show as `/joomla/administrator/index.php`
    - <img src="../../../_resources/79eacf0dd51c23b98b680601e3114af9.png" alt="79eacf0dd51c23b98b680601e3114af9.png" width="710" height="466" class="jop-noMdConv">
    - this is a login page so that we have to examine the traffic to this uri whether brute force attack here.
    - **Search query:** `index=botsv1 imreallynotbatman.com sourcetype=stream:http dest_ip="192.168.250.70" uri="/joomla/administrator/index.php"`
    - <img src="../../../_resources/50ddfa31b4fc448eb83df9f375065eed.png" alt="50ddfa31b4fc448eb83df9f375065eed.png" width="751" height="619" class="jop-noMdConv">
    - lets see what data was sent to server in the form_data:
    - **Search Query:** `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST uri="/joomla/administrator/index.php" | table _time uri src_ip dest_ip form_data`
    - <img src="../../../_resources/53e861489e3af1a11d848f3281b7c255.png" alt="53e861489e3af1a11d848f3281b7c255.png" width="1315" height="660" class="jop-noMdConv">
    - we can see alot of request with the same `username=admin` but a lot of different password in a short time period from IP **23.22.63.114**
- ****Extracting Username and Passwd Fields using Regex****
    - Splunk support regex function to alternate the form of the data, we're gonna use it to reformat the form_data field to just only show the password.
    - **Search Query:** `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST form_data=*username*passwd* | rex field=form_data "passwd=(?<creds>\w+)" |table _time src_ip uri http_user_agent creds`.
    - <img src="../../../_resources/789b0d9c2100f534321ab9f5c632fc41.png" alt="789b0d9c2100f534321ab9f5c632fc41.png" width="1272" height="558" class="jop-noMdConv">
    - This result clearly shows a continuous brute-force attack attemp from an IP **23.22.63.113** and 1 password attempt **batman** from IP **40.80.148.22** using Mozilla browser.

# Installation Phase

Once the attack has successfully exloited the system, he will try to **install a backdoor** or an **application** for **persistence** or to gain **more control**. This activity comes under the **installation phase.**

1.  we first would catch out any **http traffic** comming the web server at IP **192.168.250.70** with the term **".exe".**
    - **Search Query:** `index="botsv1" sourcetype=stream:http dest_ip="192.168.250.70" *.exe`
    - We can see the field **part_filename{}** contains the two file names:
    - <img src="../../../_resources/888b7134741b5e928113c08c05a2c914.png" alt="888b7134741b5e928113c08c05a2c914.png" width="694" height="395" class="jop-noMdConv">
2.  Next, We need to file if any of these files above associated with the attack IP address that we found earlier.
    - **Search Query:**`index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" "part_filename{}"="3791.exe"`.
    - **<img src="../../../_resources/cd193bac70a8f8a813b4afa516d4c2e3.png" alt="cd193bac70a8f8a813b4afa516d4c2e3.png" width="828" height="448" class="jop-noMdConv">**
3.  **Was this file excuted on the server?**
    - **Search Query:** `index=botsv1 "3791.exe"`.
    - <img src="../../../_resources/2e911ed53131a49fcb69c60efad9b661.png" alt="2e911ed53131a49fcb69c60efad9b661.png" width="825" height="550" class="jop-noMdConv">
    - Following the **Host-centric log**, sources were found to have traces of excutable **3791.exe:**
        - Sysmon
        - WinEventlog
        - fortigate_utm
4.  Leverage **sysmon** and look at the **EventCode=1** for new process creation.
    - **Search Query:** `index=botsv1 "3791.exe" sourcetype="XmlWinEventLog" EventCode=1`.
    - <img src="../../../_resources/6e72581fec0ec5e66e5215b5f96e3dcd.png" alt="6e72581fec0ec5e66e5215b5f96e3dcd.png" width="813" height="577" class="jop-noMdConv">
    - Looking at the output, we can clearly say the this file was executed on the compromised server.

# Action on Objective

- **Search Query:** `index=botsv1 src=192.168.250.70 sourcetype=suricata`
- ![59cf4a7eff8eeb52afdde7bc969d72ab.png](../../../_resources/59cf4a7eff8eeb52afdde7bc969d72ab.png)
- Here we see three external IPs towards which our webserver initiates the oubound traffic.
- lets check one by one to see what kind of traffic is being carried out.
- **Search Query:** `index=botsv1 src=192.168.250.70 sourcetype=suricata dest_ip=23.22.63.114`.
- ![50867d7fc769aa8f4c23bd7f62bc84bd.png](../../../_resources/50867d7fc769aa8f4c23bd7f62bc84bd.png)
- The URL field shows 2 PHP files and 1 JPEG file.
- **Search Query:** `index=botsv1 url="/poisonivy-is-coming-for-you-batman.jpeg" dest_ip="192.168.250.70" | table _time src dest_ip http.hostname url`.
- ![04b54927448f3d697137b0b18532d73a.png](../../../_resources/04b54927448f3d697137b0b18532d73a.png)
- The end result clearly shows a suspicious jpeg `poisonivy-is-coming-for-you-batman.jpeg` was downloaded from the attacker's host `prankglassinebracket.jumpingcrab.com` that defaced the site.

# Command and Control Phase

the attacker used a Dynamic DNS to resolve a malicious IP. Our objective would be to find the IP that the attacker decided the DNS.

1.  **examining the network-centric log sources `fortigate_utm` to review the firewall logs.**
    - ****Search Query:** `index=botsv1 sourcetype=fortigate_utm"poisonivy-is-coming-for-you-batman.jpeg"`.**
    - **<img src="../../../_resources/4915557780f6daa27e0bf89d28508090.png" alt="4915557780f6daa27e0bf89d28508090.png" width="1113" height="567">**
    - **<img src="../../../_resources/e27417d3e8d32973d257e7c2bedb4a0e.png" alt="e27417d3e8d32973d257e7c2bedb4a0e.png" width="809" height="259">**
2.  **Let verify the answer by looking at another log source.`stream:http`.**
    - ****Search Query:** `index=botsv1 sourcetype=stream:http dest_ip=23.22.63.114 "poisonivy-is-coming-for-you-batman.jpeg" src_ip=192.168.250.70`.**
    - **![9c78e03d238d33ff0a493457864857e8.png](../../../_resources/9c78e03d238d33ff0a493457864857e8.png)**
    - We have identified the suspicious domain as a Command and Control Server, which the attacker contacted after gaining control of the server.

# Weaponization

In the weaponization phase, the adversaries would:

- Create Malware / Malicious document to gain initial access / evade detection etc.
- Establish domains similar to the target domain to trick users.
- Create a Command and Control Server for the post-exploitation communication/activity etc.

&nbsp;

# Delivery phase

**OSINT sites**

- Virustotal
- ThreatMiner
- Hybrid-Analysis

## **ThreatMiner**

- **![3f6dda8be22de87732bc73b5f6165d63.png](../../../_resources/3f6dda8be22de87732bc73b5f6165d63.png)**![2e67379a8d89017411ed674f0fcae8b2.png](../../../_resources/2e67379a8d89017411ed674f0fcae8b2.png)

## **Virustotal**

**Open [virustotal.com](http://virustotal.com/)** and search for the hash we can get information about the metadata about this Malware in the Details tab

- **![0c05cd5442482ce1ae6a6c85b15bee97.png](../../../_resources/0c05cd5442482ce1ae6a6c85b15bee97.png)<img src="../../../_resources/e80c11f0d7a04561ddf36e7fc3f9ad01.png" alt="e80c11f0d7a04561ddf36e7fc3f9ad01.png" width="656" height="459">**

## ****Hybrid-Analysis****

Some of the information that Hybrid-Analysis provides are:

- Network Communication.
- DNS Requests
- Contacted Hosts with Country Mapping
- Strings
- MITRE ATT&CK Mapping
- Malicious Indicators.
- DLLs Imports / Exports
- Mutex Information if created
- File Metadata
- Screenshots
- ![fd80b5cfa0d3553df3d98c3b3873f6da.png](../../../_resources/fd80b5cfa0d3553df3d98c3b3873f6da.png)![c826fe434e4bdad8127371dad5325ef2.png](../../../_resources/c826fe434e4bdad8127371dad5325ef2.png)

# **Conclusion**

In this fun exercise, as a SOC Analyst, we have investigated a cyber-attack where the attacker had defaced a website 'imreallynotbatman.com' of the Wayne Enterprise. We mapped the attacker's activities into the 7 phases of the Cyber Kill Chain. Let us recap everything we have found so far:

## **Reconnaissance Phase:**

We first looked at any reconnaissance activity from the attacker to identify the IP address and other details about the adversary.

**Findings:**

- IP Address `40.80.148.42` was found to be scanning our webserver.
- The attacker was using Acunetix as a web scanner.

## **Exploitation Phase:**

We then looked into the traces of exploitation attempts and found brute-force attacks against our server, which were successful.

**Findings:**

- Brute force attack originated from IP `23.22.63.114.`
- The IP address used to gain access: `40.80.148.42`
- 142 unique brute force attempts were made against the server, out of which one attempt was successful

## **Installation Phase:**

Next, we looked at the installation phase to see any executable from the attacker's IP Address uploaded to our server.

**Findings:**

- A malicious executable file `3791.exe` was observed to be uploaded by the attacker.
- We looked at the sysmon logs and found the MD5 hash of the file.

## **Action on Objective:**

After compromising the web server, the attacker defaced the website.

**Findings:**

- We examined the logs and found the file name used to deface the webserver.

## **Weaponization Phase:**

We used various threat Intel platforms to find the attacker's infrastructure based on the following information we saw in the above activities.

Information we had:

Domain: `prankglassinebracket.jumpingcrab.com`

IP Address: `23.22.63.114`

**Findings:**

- Multiple masquerading domains were found associated with the attacker's IPs.
- An email of the user `Lillian.rose@po1s0n1vy.com` was also found associated with the attacker's IP address.

## **Deliver Phase:**

In this phase, we again leveraged online Threat Intel sites to find malware associated with the adversary's IP address, which appeared to be a secondary attack vector if the initial compromise failed.

**Findings:**

- A malware name `MirandaTateScreensaver.scr.exe` was found associated with the adversary.
- MD5 of the malware was `c99131e0169171935c5ac32615ed6261`