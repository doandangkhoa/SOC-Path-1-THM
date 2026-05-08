---
title: Benign
updated: 2026-04-23 15:57:11Z
created: 2026-04-23 13:59:53Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

# <span style="color: rgb(255, 255, 255);">Scenario: Identify and Investigation an Infected Host</span>

<span style="color: rgb(255, 255, 255);">One of the client’s IDS indicated a potentially suspicious process execution indicating one of the hosts from the HR department was compromised. Some tools related to network information gathering / scheduled tasks were executed which confirmed the suspicion.</span>

## <span style="color: rgb(255, 255, 255);">Network Information</span>

<span style="color: rgb(255, 255, 255);">The network is divided into 3 logical segments:</span>

<div class="joplin-table-wrapper"><table border="1" style="border-collapse: collapse; width: 31.2303%; height: 48px; border-width: 1px; margin-left: 0px; margin-right: auto;" class="jop-noMdConv"><colgroup class="jop-noMdConv"><col style="width: 33.5968%;" class="jop-noMdConv"><col style="width: 33.5968%;" class="jop-noMdConv"><col style="width: 33.004%;" class="jop-noMdConv"></colgroup><thead class="jop-noMdConv"><tr style="height: 24px;" class="jop-noMdConv"><th scope="col" style="height: 24px;" class="jop-noMdConv"><strong class="jop-noMdConv">IT Department</strong></th><th scope="col" style="height: 24px;" class="jop-noMdConv"><strong class="jop-noMdConv">HR department</strong></th><th scope="col" style="height: 24px;" class="jop-noMdConv"><strong class="jop-noMdConv">Marketing department</strong></th></tr></thead><tbody class="jop-noMdConv"><tr style="height: 24px;" class="jop-noMdConv"><td style="height: 24px;" class="jop-noMdConv"><ul class="jop-noMdConv"><li class="jop-noMdConv">James</li><li class="jop-noMdConv">Moin</li><li class="jop-noMdConv">Katrina</li></ul></td><td style="height: 24px;" class="jop-noMdConv"><ul class="jop-noMdConv"><li class="jop-noMdConv">Haroon</li><li class="jop-noMdConv">Chris</li><li class="jop-noMdConv">Diana</li></ul></td><td style="height: 24px;" class="jop-noMdConv"><ul class="jop-noMdConv"><li class="jop-noMdConv">Bell</li><li class="jop-noMdConv">Amelia</li><li class="jop-noMdConv">Deepak</li></ul></td></tr></tbody></table></div>

# Practice

1.  **How many logs are ingested from the month of March, 2022?**
    - <img src="../../../_resources/ed465f1f3da93a7ea5067f4f3d803af8.png" alt="ed465f1f3da93a7ea5067f4f3d803af8.png" width="1212" height="592" class="jop-noMdConv">
2.  **Imposter Alert: There seems to be an imposter account observed in the logs, what is the name of that user?**
    - <img src="../../../_resources/a035e21e2cb8c0972e97bc98414211da.png" alt="a035e21e2cb8c0972e97bc98414211da.png" width="1153" height="578" class="jop-noMdConv">
    - we can see an username named Amel1a, which sophisticate Amelia (HR staff).
3.  **Which user from the HR department was observed to be running scheduled tasks?**
    - <img src="../../../_resources/cfffab559d5d2610740a483fb17fb9e8.png" alt="cfffab559d5d2610740a483fb17fb9e8.png" width="784" height="664" class="jop-noMdConv">
    - by investigating one by one person in HR department, I discovered the staff named "**Chris.fort**" was running the schedule taks **"jusched.exe",** which is a **Java Update Scheduler process.**
    - <img src="../../../_resources/e12810a5cfd3a83f44878435bb5f285c.png" alt="e12810a5cfd3a83f44878435bb5f285c.png" width="1442" height="719" class="jop-noMdConv">
    - Additionally, I see the **schtask.exe** system process was running.
4.  **Which user from the HR department executed a system process (LOLBIN) to download a payload from a file-sharing host.?**
    - **<img src="../../../_resources/63c9d22d9e192aeaba79fdf33a67777b.png" alt="63c9d22d9e192aeaba79fdf33a67777b.png" width="1237" height="569" class="jop-noMdConv">**
    - I see on the **haroon** account runned **certutil.exe** process, by searching on **LOLBAS**, it seems like a packet download GUI.
5.  **To bypass the security controls, which system process (lolbin) was used to download a payload from the internet?**
    - **<img src="../../../_resources/5e226f1830d73cd6c69343054cc17f2b.png" alt="5e226f1830d73cd6c69343054cc17f2b.png" width="763" height="581" class="jop-noMdConv">**
6.  **What was the date that this binary was executed by the infected host? format (YYYY-MM-DD)**
    - **<img src="../../../_resources/342fdc6ed007f1186a49b239908a38bf.png" alt="342fdc6ed007f1186a49b239908a38bf.png" width="1322" height="608" class="jop-noMdConv">**
        - I see a suspicious executable file downloaded from internet.
    - **<img src="../../../_resources/9d10f9b3092f021ca0830ae58b350de8.png" alt="9d10f9b3092f021ca0830ae58b350de8.png" width="1313" height="604">**
7.  **Which third-party site was accessed to download the malicious payload?**
    - From the picture above, we can see the malicious payload was downloaded from **controlc.com** website.
8.  **What is the name of the file that was saved on the host machine from the C2 server during the post-exploitation phase?**
    - So far, we've already known that the malicious file was exploited in the victim host, so that it certainly is "**benign.exe"**
9.  **The suspicious file downloaded from the C2 server contained malicious content with the pattern THM{..........}; what is that pattern?**
    - **![407616850667a73e603a9d5f8f0c7e8f.png](../../../_resources/407616850667a73e603a9d5f8f0c7e8f.png)**
    - access to the suspicious url, I've already foud out the flag.