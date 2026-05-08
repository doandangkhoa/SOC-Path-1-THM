---
title: Investigating with Splunk
updated: 2026-04-14 01:21:15Z
created: 2026-04-14 00:18:42Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

## Scenario

SOC Analyst **Johny** has observed some anomalous behaviours in the logs of a few windows machines. It looks like the adversary has access to some of these machines and successfully created some backdoor. His manager has asked him to pull those logs from suspected hosts and ingest them into Splunk for quick investigation. Our task as SOC Analyst is to examine the logs and identify the anomalies.

On one of the infected hosts, the adversary was successful in creating a backdoor user. What is the new username?

`index=main EventID=4720`

<img src="../../../_resources/0ff77f8725ca2c651bd4f77c8b0e1f30.png" alt="0ff77f8725ca2c651bd4f77c8b0e1f30.png" width="1319" height="723" class="jop-noMdConv">

On the same host, a registry key was also updated regarding the new backdoor user. What is the full path of that registry key?

`index=main EventID="13" AND Hostname="Micheal.Beaven"`

![b6cdbde10bc09a658adc24ff386513de.png](../../../_resources/b6cdbde10bc09a658adc24ff386513de.png)

Examine the logs and identify the user that the adversary was trying to impersonate?

**A1berto**

How many times was the login attempt from the backdoor user observed during the investigation?

**<img src="../../../_resources/3a3607487f18ff0f6470728793049da2.png" alt="3a3607487f18ff0f6470728793049da2.png" width="918" height="503">**

**What is the name of the infected host on which suspicious Powershell commands were executed?**

&nbsp;