---
title: Wazuh
updated: 2026-04-13 14:50:47Z
created: 2026-04-12 14:15:26Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

<img src="../../_resources/b99233d1013271e55ecd4afe0eec8237.png" alt="b99233d1013271e55ecd4afe0eec8237.png" width="601" height="255" class="jop-noMdConv">

Simply put, there is one Wazuh server (**manager**) that stores and processes the data, and many hosts (**agents**) that send data to the manager.

# Wazuh Agent

Devices that record the events and processes of a system are called agents.

Agents monitor the processes and events that take place on the device, such as authentication and user management

<img src="../../_resources/851b7ad53a24480352bd1f1170d1c679.png" alt="851b7ad53a24480352bd1f1170d1c679.png" width="795" height="583" class="jop-noMdConv">

**Wazuh -> Agents -> Deploy New Agent**

****Installing the Wazuh agent on Windows:****

****<img src="../../_resources/af07713cc673254d8607bec60a5ad244.png" alt="af07713cc673254d8607bec60a5ad244.png" width="744" height="581" class="jop-noMdConv">****

# Wazuh vulnerability Assessment & Security Events

Wazuh’s Vulnerability Assessment module is a powerful tool that can be used to periodically scan an agent's operating system for installed applications and their version numbers.

**must** be configured to run at a set interval then after (by default, this is set to 5 minute intervals when enabled) like so:

![0016f59b3a8b75ee592d521160543d14.png](../../_resources/0016f59b3a8b75ee592d521160543d14.png)

*Configuring the Wazuh management server to audit agents for vulnerabilities frequently (/var/ossec/etc/ossec.conf)*

# wazuh policy auditing

Wazuh is capable of auditing and monitoring an agent's configuration whilst proactively recording event logs. When the Wazuh agent is installed, an audit is performed where a metric is given using multiple frameworks and legislations such as NIST, MITRE and GDPR.

<img src="../../_resources/e7420ce3aee3a602fdecc292ed4d46f9.png" alt="e7420ce3aee3a602fdecc292ed4d46f9.png" width="1256" height="588" class="jop-noMdConv">

Navigate to the "Modules" tab by pressing `Wazuh -> Modules` and open the "**Policy Management"** module like so:

<img src="../../_resources/0e00b508088ac7862597b03614fdcafe.png" alt="0e00b508088ac7862597b03614fdcafe.png" width="734" height="320" class="jop-noMdConv">

&nbsp;

# Monitoring Logons with Wazuh

Wazuh's security event monitor is capable to actively record both successful and unsuccessful authentication attempts.

The alert was created because someone tried to log onto the agent "**ip-10-10-73- 118**" with the user "**cmnatic**" which does not exist. I have summarized this alert into the table below:

|     |     |     |
| --- | --- | --- |
| **Field** | **Value** | **Description** |
| agent.ip | 10.10.73.118 | This is the IP address of the agent that the alert was triggered on. |
| agent.name | ip-10-10-73-118 | This is the hostname of the agent that the alert was triggered on. |
| rule.description | sshd: Attempt to login using a non-existent user | This field is a brief description of what the event is alerting to. |
| rule.mitre.technique | Brute-Force | This field explains the MITRE technique that the alert pertains to. |
| rule.mitre.id | T1110 | This field is the MITRE ID of the alert |
| rule.id | 5710 | This field is the ID assigned to the alert by Wazuh's ruleset |
| location | /var/log/auth.log | This field is the location of the file that the alert was generated from on the agent. In this example, it is the authentication log on the linux agent. |

this alert is stored in a specific file on the Wazuh management server: `/var/ossec/logs/alerts/alerts.log`

<img src="../../_resources/9b934b40a94d500469362b71d479687e.png" alt="9b934b40a94d500469362b71d479687e.png" width="1160" height="213" class="jop-noMdConv">

# Collecting Windows Logs with Wazuh

We can use the Wazuh agent to aggregate these events recorded by *Sysmon* for processing to the Wazuh manager.

Sysmon uses rules that are made in XML formatting to be triggered.

![554d9b310e8465522425fd42d6007d21.png](../../_resources/554d9b310e8465522425fd42d6007d21.png)

To instruct Sysmon to do, we need to execute the Sysmon application and provide the aforementioned configuration file like so: `Sysmon64.exe -accepteula -i detect_powershell.xml`

<img src="../../_resources/99eabc7e3fbbe96c846ea81149f3279f.png" alt="99eabc7e3fbbe96c846ea81149f3279f.png" width="640" height="212" class="jop-noMdConv"> <img src="../../_resources/b3361840da2afc6f9eafec25e8fa93d7.png" alt="b3361840da2afc6f9eafec25e8fa93d7.png" width="731" height="381" class="jop-noMdConv">

![bc6eac2f07efe2919414f9b36c328b03.png](../../_resources/bc6eac2f07efe2919414f9b36c328b03.png)

We can verify that Sysmon has accepted our configuration file by navigating to the Event Viewer and searching for the “**Sysmon**” module like so:

![d82606b4b0fe32ccc105a7e8e53d3812.png](../../_resources/d82606b4b0fe32ccc105a7e8e53d3812.png)<img src="../../_resources/37749dfdbb00ae66e12152c9a4b27185.png" alt="37749dfdbb00ae66e12152c9a4b27185.png" width="949" height="640" class="jop-noMdConv">

Let’s launch a powershell prompt on the Windows Server and return to our Event Viewer. We can now see a record of this powershell prompt being opened, kept within the Event Viewer.

<img src="../../_resources/bf32ee34af4c162256f90f6622ab0dbe.png" alt="bf32ee34af4c162256f90f6622ab0dbe.png" width="1063" height="718" class="jop-noMdConv">

## Configure the Wazuh agent

- Now we will need to configure the Wazuh agent on this Window Server to instruct it to send these events to the Wazuh management server
- open the Wazuh agent file located at: `C:\Program Files (x86)\ossec-agent\ossec.conf`
    - <img src="../../_resources/60c6243baf26605c684da384618f84a0.png" alt="60c6243baf26605c684da384618f84a0.png" width="535" height="562" class="jop-noMdConv">
- include the following snippet:
    - ![f400b0c40353812d58df3469f532be31.png](../../_resources/f400b0c40353812d58df3469f532be31.png)
- we need to tell the Wazuh Management server to add Sysmon as a rule to visualize these events.
    - adding an XML file to the local rules located in `/var/ossec/etc/rules/local_rules.xml`
        - ![f6012c0ea76d08210539568ad0753db9.png](../../_resources/f6012c0ea76d08210539568ad0753db9.png)

# Collecting Linux Log with Wazuh

We will be using Wazuh’s log collector service to create an entry on the agent to instruct what logs should be sent to the Wazuh management server.

Wazuh comes with many rules that enable Wazuh to analyze log files and can be found in `/var/ossec/ruleset/rules`. Some common applications include:

- Docker
- FTP
- WordPress
- SQL Server
- MongoDB
- Firewalld
- And many, many more (approximately 900).

However, you can always make your own rules. In this task, Wazuh will digest *Apache2* logs using the `0250-apache_rules.xml` ruleset.

This ruleset can analyze apache2 logs for warnings and error messages like so: We will need to insert this into the Wazuh’s agent that is sending logs to the Wazuh management servers configuration file located in `/var/ossec/etc/ossec.conf`:

&nbsp;       ![aa8cb712402852c78a101a248670e097.png](../../_resources/aa8cb712402852c78a101a248670e097.png)

# Auditing Command on Linux with Wazuh

Wazuh utilises the `auditd` package that can be installed on Wazuh agents running on Debian/Ubuntu and CentOS operating systems.

We can then use the log collector module on a Wazuh agent to read this log file and send it to the Wazuh management server for processing.

- First, we will need to install the `auditd` package and an `auditd` plugin: `sudo apt-get install auditd audispd-plugins`
- enable this service to run currently as well as on boot: `sudo systemctl enable auditd.service && sudo systemctl start auditd.service`
- onfigure `auditd` to create a rule for the commands and events that we wish for it to monitor such as tcpdump, netcat, catting files like /etc/passwd, /etc/shadow,...
- `Auditd` rules are located in the following directory: `/etc/audit/rules.d/audit.rules`.
- <img src="../../_resources/c5ebaf8ddd761f835852c4a56f97353b.png" alt="c5ebaf8ddd761f835852c4a56f97353b.png" width="1054" height="346" class="jop-noMdConv">
- We will now need to inform audits of this new rule, so let's run this command `sudo auditctl -R /etc/audit/rules.d/audit.rules`
- configure the Wazuh agent to detect this new log file that is generated by `auditd` like so `sudo nano /var/ossec/etc/ossec.conf` and add the `auditd` log like so:![2a0dea69ebbfbecabb44cdcc28a5dc4c.png](../../_resources/2a0dea69ebbfbecabb44cdcc28a5dc4c.png)

# Wazuh API

The Wazuh management server features a rich and extensive API to allow the Wazuh management server to be interacted with using the command line. Because the Wazuh management server requires authentication, we must first authenticate our client.

Once we are authenticated, the Wazuh management server will give us a token (similar to a session) that we will need to provide for any further interaction. We can store this token as an environment variable on our Linux machine:

&nbsp;       `TOKEN=$(curl -u : -k -X GET "https://WAZUH_MANAGEMENT_SERVER_IP:55000/security/user/authenticate?raw=true")`

Let’s confirm that we have authenticated okay and have been given a token by the Wazuh management server:  
<br/>        `curl -k -X GET "https://10.49.161.68:55000/" -H "Authorization: Bearer $TOKEN"`

<img src="../../_resources/aabae7274ea15889c8e41124298c22ce.png" alt="aabae7274ea15889c8e41124298c22ce.png" width="1074" height="309" class="jop-noMdConv">

For example, let’s use the Wazuh API to list some statistics and important information about the Wazuh management server, including what services are being monitored and some general settings about the Wazuh management server:

`curl -k -X GET "https://10.49.161.68:55000/manager/configuration?pretty=true&section=global" -H "Authorization: Bearer $TOKEN"`

<img src="../../_resources/21e489a44905a2d6c2e70b69bc187aaa.png" alt="21e489a44905a2d6c2e70b69bc187aaa.png" width="939" height="601" class="jop-noMdConv">

Or perhaps, we can use the Wazuh management server’s API to interact with an agent:

`curl -k -X GET "https://10.49.161.68:55000/agents?pretty=true&offset=1&limit=2&select=status%2Cid%2Cmanager%2Cname%2Cnode_name%2Cversion&status=active" -H "Authorization: Bearer $TOKEN"`

![c99dd3fa7981b61c031f142203358767.png](../../_resources/c99dd3fa7981b61c031f142203358767.png)

## Using Wazuh's API Console

Wazuh has a powerful, integrated API console within the Wazuh website to query management servers and agents.

![3643f5ad463604f73f789aa737eac827.png](../../_resources/3643f5ad463604f73f789aa737eac827.png)

# Generating Reports with Wazuh

<img src="../../_resources/070db56446d811f8abdd010182dbc18d.png" alt="070db56446d811f8abdd010182dbc18d.png" width="644" height="314"><img src="../../_resources/b4043779b93030ce5be7c652392486a0.png" alt="b4043779b93030ce5be7c652392486a0.png" width="1212" height="162">

After allowing some time, we will navigate to the report overview dashboard within Wazuh.

First, press on the "Wazuh" heading at the top of the screen and select "**Management**", and then click on the "**Reporting**" text located under the "**Status and Reports**" sub-heading:

<img src="../../_resources/6dff6d2c4e6e08c64de0622af86caaf2.png" alt="6dff6d2c4e6e08c64de0622af86caaf2.png" width="576" height="385">

The report overview dashboard lists all generated reports. To download a report, press the save icon on the right of the report located under the "**Actions**" heading.

<img src="../../_resources/b70b88c9026a949b95b812954001b85c.png" alt="b70b88c9026a949b95b812954001b85c.png" width="493" height="537">

# Loading Sample Data

The Wazuh management server comes with sample data bundled with the installation that can be loaded at your convenience.

Navigate to the module to load the sample data:

1.  Open the "**Wazuh"** tab in the heading.
2.  Highlight over "**Settings"**.
3.  Select the "**Sample Data"** heading.
4.  Press the "**Add Data**" button on the respective three cards to import the data.

<img src="../../_resources/109ec69c32659afee84ab0c8a8dd404a.png" alt="109ec69c32659afee84ab0c8a8dd404a.png" width="462" height="393">

<img src="../../_resources/4c53f73ef53e43efb41967b9c582f839.png" alt="4c53f73ef53e43efb41967b9c582f839.png" width="1202" height="482">

**Please note** that you will need to play with the date range. The absolute minimum required to show the sample will need to be Last 7 days+ and refresh the dashboard for this to apply.

<img src="../../_resources/20ebcfae692aebf36c3a78e0c615fcd1.png" alt="20ebcfae692aebf36c3a78e0c615fcd1.png" width="478" height="413">

&nbsp;