---
title: Alert Triage with Elastic
updated: 2026-03-10 15:15:54Z
created: 2026-03-10 09:12:04Z
latitude: 21.02776440
longitude: 105.83415980
altitude: 0.0000
---

In this guided-room challange, we're gonna learn about kibana (a part of the Elastic Stack) to perform alert triage and initial investigations.

# Investigating Web Attacks

![8c6619abfa354591ea7bdaa0228ccc96.png](../../_resources/8c6619abfa354591ea7bdaa0228ccc96.png)

![66ffedf695fdcca6c5e649a2142bf589.png](../../_resources/66ffedf695fdcca6c5e649a2142bf589.png)

![4a474beb835ce7448e115b29a22b1665.png](../../_resources/4a474beb835ce7448e115b29a22b1665.png)

![920614760a245a8752e5a7f1b80d85ea.png](../../_resources/920614760a245a8752e5a7f1b80d85ea.png)

# Uncovering Account Activity

![1435be535ef98424c5a6489f5c5a7ef4.png](../../_resources/1435be535ef98424c5a6489f5c5a7ef4.png)

suspicious network traffic just only is first part of the story, so we need to pivot host-based evidence to determine if the attacker moved further.

In this scenario, Administrator has accessed out of regular working hours. We have to determine that where, when and why it occur.

lets use Windows Security Event ID `4625` (sucessfully login) to gather the context.

lets begin by focusing on events that occured on or after this event.

**Query Windows Event Log**

```KQL
@timestamp >= "2025-07-20T05:11:22" and winlog.event_id:4624 and host.name:winserv2019.some.corp and winlog.event_data.TargetUserName:Administrator
```

- `winlog.event_id`: Windows Event ID
- `host.name`: Target hostname on which the logon occurred
- `winlog.logon.type`: How the user accessed the system (RDP, SSH, etc.)
- `winlog.event_data.IpAddress`: the source IP address of the client

**Query Sysmon Log**

```KQL
@timestamp >= 2025-07-20T05:11:22" and winlog.event_id:1 and user.name:Administrator
```

1.  `user.name` User account that lauched the process
2.  `process.parent.name`: Executable name of the parent process
3.  `process.command_line`: the actual process with its full command line

![7d849ae68788043ca8425f830506366d.png](../../_resources/7d849ae68788043ca8425f830506366d.png)

```KQL
@timestamp >= "2025-07-20T05:13:10" and winlog.channel:security and winlog.task:User Account Management

```

# Exposing Command Excution

![c417c11fec12a70eba2cd6c6022e944d.png](../../_resources/c417c11fec12a70eba2cd6c6022e944d.png)

As a L1 SOC analyst, we have to:

1.  Scope the alert: Identify the child processes lauched by `cmd.exe`
2.  comfirm the origin: Find out who lauched `cmd.exe` and why
3.  Check for privilege changes: Look for commands like net used to add users to groups
4.  Correlate access log sources: Use Sysmon and Windows Security logs to comfirm the malicious behavior

creating a query to highlight Sysmon events that occurred on or after the stated time and include the parent process cmd.exe will clearly see what happend.

```KQL
@timestamp >= "2025-07-20T05:13:15" and process.parent.name:cmd.exe and user.name:Administrator

```

<img src="../../_resources/3a4ae8f5648b6c644701e93505120b85.png" alt="3a4ae8f5648b6c644701e93505120b85.png" width="1266" height="340" class="jop-noMdConv">

**Correlate with Security Event ID 4732**

```KQL
@timestamp >= "2025-07-20T05:13:15" and (winlog.event_id:4732 or process.parent.name: cmd.exe)

```

<img src="../../_resources/403cfd34052ca059d2422ea7033561be.png" alt="403cfd34052ca059d2422ea7033561be.png" width="1277" height="412" class="jop-noMdConv">

## PowerShell Usage

Whether attacker keeps continue from newly created user?

lets prove it by PowerShell logs:

1.  Query: `@timestamp >= "2025-07-20T05:13:15" and event.module:powershell and event.code:4104`
2.  Add the `field powershell.file.script_block_text` to show commands run in plaintext.
3.  Sort by `Old-New`

<img src="../../_resources/90d831bf24ceff4407c296955a2caa87.png" alt="90d831bf24ceff4407c296955a2caa87.png" width="1288" height="463">

- `event.code:4104`:
    - PowerShell Script Block Logging, place in Microsoft-Windows-PowerShell/Operational.
    - Ghi lại toàn bộ mã lệnh được thực thi (tự động giải mã các lênh bị làm rồi base64 và ghi lại các lệnh dưới dạng plaintext)
    - dùng để chống lại các cuộc tấn công Filess (ghi thẳng vào bộ nhớ RAM)

## No Alert Created

&nbsp;

## Practice

What command does the attacker use to add the new account to the "Remote Desktop Users" group?

<img src="../../_resources/cf4d1b35f4b7169c9533b3204c025e61.png" alt="cf4d1b35f4b7169c9533b3204c025e61.png" width="1433" height="495">

What is the `winlog.record_id` of the `4732` Security event when the attacker adds the user to the Administrator group?

<img src="../../_resources/a33304db264fabac5cead88cc38f0e1c.png" alt="a33304db264fabac5cead88cc38f0e1c.png" width="1436" height="496">

What PowerShell command did the attacker run on `Jul 20, 2025 @ 05:16:14.628`?

<img src="../../_resources/689e5e7213179dae706ce264ae497df2.png" alt="689e5e7213179dae706ce264ae497df2.png" width="1430" height="494">

- **`net group`**: Đây là công cụ dòng lệnh tích hợp sẵn của Windows dùng để thêm, xóa hoặc hiển thị thông tin về các nhóm toàn cục (global groups) trên máy chủ.
    
- **`"Domain Admins"`**: Đây là tên của nhóm cần truy vấn. Trong Active Directory, "Domain Admins" là nhóm có quyền lực tối cao nhất. Bất kỳ ai nằm trong nhóm này đều có thể kiểm soát toàn bộ máy chủ, máy trạm và dữ liệu trong miền đó.
    
- **`/domain`**: Tham số này là điểm mấu chốt. Nó chỉ thị cho máy tính hiện tại không tìm kiếm thông tin trên máy cục bộ, mà phải gửi truy vấn trực tiếp đến **Domain Controller** (Máy chủ Quản lý Miền) để lấy dữ liệu của toàn bộ mạng.
    

What is the name of the archive that the attacker creates using the `Rar.exe` executable?

<img src="../../_resources/59baa524cc50886fc4a07417cffa0679.png" alt="59baa524cc50886fc4a07417cffa0679.png" width="1433" height="495">

- **`a`**: Lệnh yêu cầu thêm tệp vào file nén (Archive).
    
- **`-r`**: Lặp lại (Recursive) toàn bộ các thư mục con, giúp gom sạch mọi dữ liệu trong một thư mục lớn.
    
- **`-hp<mật_khẩu>` hoặc `-p<mật_khẩu>`**: Đây là dấu hiệu rõ ràng nhất của sự mờ ám. Lệnh này dùng để đặt mật khẩu mã hóa cho cả nội dung tệp và tên tệp bên trong (Header encryption).
    
- **`-m5`**: Mức độ nén tối đa (Maximum compression) để thu nhỏ tệp nhỏ nhất có thể.