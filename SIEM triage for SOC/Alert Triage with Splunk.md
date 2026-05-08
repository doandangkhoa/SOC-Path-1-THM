---
title: Alert Triage with Splunk
updated: 2026-03-17 10:27:14Z
created: 2026-03-09 06:00:22Z
latitude: 21.02776440
longitude: 105.83415980
altitude: 0.0000
---

# <span style="color: rgb(241, 196, 15);">Initial Access</span>

## Alert Scenario

![7a81d3492faf439e53cd78b284edf6bd.png](../../_resources/7a81d3492faf439e53cd78b284edf6bd.png)

## Investigating the Alert

two fields may be of interest: `Source IP, Target Host.`

- source IP is an local address.
- the time appears normal, it's 9a.m (during regular working hours).

lets move into the SIEM and check whether brute force activity occured here or if it's a false positive alert.

- using query:
    
- ```SPL
                                        index="linux-alert" sourcetype="linux_secure" 10.10.242.248 
                                        | search "Accepted password for" OR "Failed password for" OR "Invalid user"
                                        | sort + _time
    ```
    
- `index="linux-alert" sourcetype="linux_secure" 10.10.242.248| search "Accepted password for" OR "Failed password for" OR "Invalid user"| sort + _time`
    
- ![a74547c5d75e42d40c954dd2a5f938ad.png](../../_resources/a74547c5d75e42d40c954dd2a5f938ad.png)
    

let's run another query to see the number of login attempts for each user.

```SPL
index="linux-alert" sourcetype="linux_secure" 10.10.242.248
| rex field=_raw "^\d{4}-\d{2}-\d{2}T[^\s]+\s+(?<log_hostname>\S+)"
| rex field=_raw "sshd\[\d+\]:\s*(?<action>Failed|Accepted)\s+\S+\s+for(?: invalid user)? (?<username>\S+) from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| eval process="sshd"
| stats count values(src_ip) as src_ip values(log_hostname) as hostname values(process) as process by username
```

&nbsp;

- **`*` (Dấu sao)**: Lặp lại từ **0** đến nhiều lần (nghĩa là không có cũng được, có thì lấy hết).
    
- **`?` (Dấu hỏi)**: Lặp lại **0 hoặc 1** lần (dùng để đánh dấu cái gì đó là "có cũng được mà không có cũng không sao").
    
- `+` (Dấu cộng): lặp ít nhất 1 lần.
    
- `{n}` : Lặp lại chính xác n lần.
    
- **`{n,m}`**: Lặp lại tối thiểu `n` lần và tối đa `m` lần.
    
- `[]` liệt kê, `\[`, `\.` : kí tự, : không phải kí tự đặc biệt nên có thể đứng riêng
    
- `\s` : kí tự dấu cách, tab, xuống dòng.
    
- **`(...)`**: Nhóm dữ liệu và "ghi nhớ" nó (thường dùng để trích xuất).
    
- **`(?<...>)`**: Nhóm dữ liệu và đặt tên định danh cho nó để dùng trong bảng kết quả Splunk.
    
- **`(?:...)`**: Nhóm dữ liệu nhưng **không** trích xuất ra cột riêng (chỉ dùng để xử lý logic bên trong Regex).
    
- `\S` tương đương`[^s]`
    
- `eval process="sshd"` : tạo trường mới tên process và gán giá trị bằng sshd để thuận tiện cho việc thống kê `stats`.
    

<img src="../../_resources/7304ce36f162a9a341fb583d351d5896.png" alt="7304ce36f162a9a341fb583d351d5896.png" width="921" height="404" class="jop-noMdConv">![95d46ce27ed1efa078766e03b65838f2.png](../../_resources/95d46ce27ed1efa078766e03b65838f2.png)

we know that the brute-force attempts targeted **john.smith but** we don't know whether it's successful.

lets use the following query:

&nbsp;

```SPL
index="linux-alert" sourcetype="linux_secure" 10.10.242.248
| rex field=_raw "^\d{4}-\d{2}-\d{2}T[^\s]+\s+(?<log_hostname>\S+)"
| rex field=_raw "sshd\[\d+\]:\s*(?<action>Failed|Accepted)\s+\S+\s+for(?: invalid user)? (?<username>\S+) from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| eval process="sshd"
| stats count values(action) values(src_ip) as src_ip values(log_hostname) as hostname values(process) as process  by username
```

<img src="../../_resources/cd34f1bbc8b521635f3d1aa2ec95c206.png" alt="cd34f1bbc8b521635f3d1aa2ec95c206.png" width="880" height="338" class="jop-noMdConv">![96a9f6e3a7c39fb483dc798cfefd9aa3.png](../../_resources/96a9f6e3a7c39fb483dc798cfefd9aa3.png)

## Next Investigation Steps

as a L1 SOC analysit, I have to determine whether an event is malicious. if it does, we have to escalate to the level 2 analyst.

these questions below are remaining in SOC team continously:

- Why did the attacker have a local IP address? Could it be that they are already inside our network? If so, for how long?
- How did the attacker obtain information about the users, specifically their usernames?
- What happened after the attacker gained access to the tryhackme-2404 host?

## Practice

**How many failed login attempts were made on the user john.smith?**

**![8f4dea6689b337654dd19a8ee4506fbf.png](../../_resources/8f4dea6689b337654dd19a8ee4506fbf.png)**

**What was the duration of the brute force attack in minutes?**

![8d458b27fcfae0e455e4a7d89a9276ff.png](../../_resources/8d458b27fcfae0e455e4a7d89a9276ff.png)

**What username was the attacker able to privilege escalate to?**

**![c5e3b9367fb66695347da59f9dd1ce30.png](../../_resources/c5e3b9367fb66695347da59f9dd1ce30.png)**

# <span style="color: rgb(241, 196, 15);">**Persistence Alert**</span>

## **Alert Scenario**

**![68a692286e8ea2922ba4273a16b7b1ac.png](../../_resources/68a692286e8ea2922ba4273a16b7b1ac.png)**

## **Investigating the Alert**

Focus on **Host**, **User**.

what kind of host it is: Workstation or Server.

Servers often use prefixes like SRV, WEB, MSQL

- Work station: WIN, HOST.
- check the user's role

the time duration , location.

lets using search query to investigate more:

```SPL
index="win-alert" EventCode=4698 AssessmentTaskOne
| table _time EventCode user_name host Task_Name Message
```

`EventCode=4698` : A scheduled Task was created.

- <img src="../../_resources/0ad74db1def8d74e6293f4907e49d614.png" alt="0ad74db1def8d74e6293f4907e49d614.png" width="1109" height="512" class="jop-noMdConv">
- <img src="../../_resources/3f28c4c343c1a379250e5b7f754319da.png" alt="3f28c4c343c1a379250e5b7f754319da.png" width="813" height="583" class="jop-noMdConv">![7ce5b672a3a46ccb0de21d728db50a13.png](../../_resources/7ce5b672a3a46ccb0de21d728db50a13.png)
- this task will user `certutil` to download `rv.exe` from tryhotme domain into the Temp folder under the name `DataCollector.exe`, then launch this file using a `Start-Process` Powershell command by `oliver.thompson`.
- We can additionally use TI to ensure the malicious domain, so you can triage it as a True Positive and escalate it to L2 analysit.

## Next Investigation Steps:

- How was this scheduled task created?
- How did the attacker gain access to the WIN-H015 host?
- How was the oliver.thompson account compromised?

## Practice

**What is the ProcessId of the process that created this malicious task?**

```SPL
index="win-alert" EventCode=4698 AssessmentTaskOne
```

![b640a267eff6a5a3b820daf67fc73b82.png](../../_resources/b640a267eff6a5a3b820daf67fc73b82.png)

**What is the name of the parent process for the process that created this malicious task?**

```SPL
index="win-alert" EventCode=1 ProcessId=4128
```

![d1e32812930490a8a45dfadb8d50aed5.png](../../_resources/d1e32812930490a8a45dfadb8d50aed5.png)

**Which local group did the attacker enumerate during discovery?**

&nbsp;**<img src="../../_resources/a983409169724ff5a223efe9fc22ae96.png" alt="a983409169724ff5a223efe9fc22ae96.png" width="1336" height="729" class="jop-noMdConv">**

- **`net localgroup Administrators` : liệt kê các thành viên trong nhóm Administrators**
- `net localgroup "Administrators" "oliver.thompson" /add`: thêm account oliver.thompson vào administrators group.

# <span style="color: rgb(241, 196, 15);">Web Shell Alert</span>

## Alert Scenario

- ![39988f38eec9b19480fbeaddb6509054.png](../../_resources/39988f38eec9b19480fbeaddb6509054.png)

## Investigating the Alert

using AbuseIPDB to gather more information

<img src="../../_resources/4433057757ec53646df919550066d871.png" alt="4433057757ec53646df919550066d871.png" width="492" height="419" class="jop-noMdConv">

Lets make note of this.

- SPL query:
    
- index=web-alert 171.251.232.40  
    | table \_time clientip useragent uri_path method status  
    | sort + \_time
    
    ```
    index=web-alert 171.251.232.40
    | table _time clientip useragent uri_path method status
    | sort + _time
    ```
    
- ![959f64804069dfcb1123c35ee85f8057.png](../../_resources/959f64804069dfcb1123c35ee85f8057.png)
    
- but in this case, we just interested about web shell, so we're gonna exlude the Hydra user agent from our query search.
    
- index=web-alert 171.251.232.40 useragent != "Mozilla/5.0 (Hydra)"  
    | table \_time clientip useragent uri_path referer_domain method status
    
    ```
    index=web-alert 171.251.232.40 useragent != "Mozilla/5.0 (Hydra)"
    | table _time clientip useragent uri_path referer_domain method status
    ```
    
- <img src="../../_resources/75232c443508e9c9721c90726d864c51.png" alt="75232c443508e9c9721c90726d864c51.png" width="1148" height="518" class="jop-noMdConv">

Lets take a closer look at the logs related to `b374k.php`

```SPL
index=web-alert 171.251.232.40 b374k.php
| table _time clientip useragent uri_path referer referer_domain method status
| sort + _time

```

- <img src="../../_resources/1a0d136815ab3a0fa545c3998dd75c17.png" alt="1a0d136815ab3a0fa545c3998dd75c17.png" width="1148" height="482" class="jop-noMdConv"><img src="../../_resources/cb3eda308cc98b5632745cc43ebfc0ef.png" alt="cb3eda308cc98b5632745cc43ebfc0ef.png" width="1150" height="483" class="jop-noMdConv"><img src="../../_resources/3a2783bb868ade91cfaecbc72004f8db.png" alt="3a2783bb868ade91cfaecbc72004f8db.png" width="598" height="412" class="jop-noMdConv">

## Next Investigation Steps

- Was the brute force attack using Hydra successful?
- How did the attacker upload the web shell to the server, given that SOC L1 did not identify any traces of the upload?
- What specific actions did the attacker perform on the server using commands through the web shell?

&nbsp;