---
title: Linux Fundamentals
updated: 2026-03-09 03:55:00Z
created: 2026-03-03 08:36:29Z
latitude: 20.21299690
longitude: 105.92299000
altitude: 0.0000
---

# <span style="color: rgb(241, 196, 15);">Searching for files</span>

## Find

- ![5c674c8a09252a19c56948d80ba6fa2f.png](../../../_resources/5c674c8a09252a19c56948d80ba6fa2f.png)
- ![92ad999aedb251340a2123a7ade80c8f.png](../../../_resources/92ad999aedb251340a2123a7ade80c8f.png)

## Grep

- ![0f1057b7b6651cc096c4c8a204e3e89d.png](../../../_resources/0f1057b7b6651cc096c4c8a204e3e89d.png)
- Searching **<span style="color: rgb(241, 196, 15);">recuresively</span>** through all files and subdirectories.
- ```bash
                                                  grep -R "PRETTY_NAME" /etc/
    ```
    

# <span style="color: rgb(241, 196, 15);">Shell Operators</span>

- ![05c55e6705e7df3d907617bb41088597.png](../../../_resources/05c55e6705e7df3d907617bb41088597.png)

# <span style="color: rgb(241, 196, 15);">Introduction to SSH protocol</span>

- <img src="../../../_resources/0591a6fbd0d5b2b1b2f1cfe6aa30af2b.png" alt="0591a6fbd0d5b2b1b2f1cfe6aa30af2b.png" width="982" height="353" class="jop-noMdConv">
- a protocol between devices in an encrypted form.
- any input send in human-readable format is encrypted for travelling over a network and it'll only be unencrypted when reaching the destination.
- allows us to remotely excute commands on another device.

## File System Interaction

- ![e5787a54711955cd3a4875844aa8e682.png](../../../_resources/e5787a54711955cd3a4875844aa8e682.png)
- ![f9bfb9c514f58b1b02f43b9ac5c8c69f.png](../../../_resources/f9bfb9c514f58b1b02f43b9ac5c8c69f.png)
- ![6aaea13e7cbdd830fce9e0bee606eecd.png](../../../_resources/6aaea13e7cbdd830fce9e0bee606eecd.png)
- ![f1f2e4e7d56f31d4ee14433c426a9087.png](../../../_resources/f1f2e4e7d56f31d4ee14433c426a9087.png)
- ![49b8a0b62b621d08ae53d4d022188c25.png](../../../_resources/49b8a0b62b621d08ae53d4d022188c25.png)

## Permission 101

### Switching between users

- ![bdef7dce77decf0ce93cf9561f80ce93.png](../../../_resources/bdef7dce77decf0ce93cf9561f80ce93.png)
- ![1ea90b1c7fefc2d4be6999228f3269bf.png](../../../_resources/1ea90b1c7fefc2d4be6999228f3269bf.png)

| **Đặc điểm** | **su** | **su -l (hoặc su -)** |
| --- | --- | --- |
| **Thư mục hiện tại** | Giữ nguyên thư mục cũ. | Chuyển về thư mục Home của user mới. |
| **Biến `$PATH`** | Dùng chung với user cũ (Dễ thiếu lệnh hệ thống). | Tải mới hoàn toàn (Đầy đủ quyền admin). |
| **Biến `$HOME`** | Vẫn trỏ về thư mục của user cũ. | Trỏ về thư mục của user mới. |
| **Độ an toàn** | Thấp hơn (Dễ nhầm lẫn môi trường). | Cao hơn (Sạch sẽ, đúng chuẩn). |

### File Permission

```bash
rwxrwxrwx
```

| Section | Applies To | Example |
| --- | --- | --- |
| First 3 | Owner | `rwx` |
| Next 3 | Group | `rwx` |
| Last 3 | Others | `rwx` |

### More Common Examples

| Symbolic | Numeric | Meaning |
| --- | --- | --- |
| `rwxr-xr-x` | 755 | Owner can do everything, others can read and execute |
| `rw-r--r--` | 644 | Owner can read/write, others can only read |
| `rwx------` | 700 | Only the owner has access |

For example:

```bash
chmod 750 system_overview.txt
```

This means:

- Owner: full access
- Group: read + execute
- Others: no access

# <span style="color: rgb(241, 196, 15);">Common Directories</span>

## /etc

- short for etcetera
- common place to store system files.
- ![e496c1e995da75488ee821b636c9d54b.png](../../../_resources/e496c1e995da75488ee821b636c9d54b.png)
- sudoers : contains a list of the users & groups that have permission to run sudo
- sudoers.d : to configure more easily for administrative when only want to handle/add individual configuration file.
- <span style="color: rgb(241, 196, 15);">shadow</span>:
    - authenticate the users when they login or use sudo command. (contains user's hashed string passwords)
    - the same with passwd but higher security <span style="color: rgb(241, 196, 15);">(only root user can read)</span>.
- <span style="color: rgb(241, 196, 15);">passwd</span>:
    - contains basic information about system login account such as username, UID, GID, home directory, etc.

### /root

- is the home for the "root" system user
- this is the home directory for the "root" user.

### /tmp

- is used to stored temporary data which is only need to be accessed once or twice (such as <span style="color: rgb(241, 196, 15);">RAM memory</span>)
- once the computer is started --> all contents will be cleared out.

### /var

- short for variable data, is one of the main root folders found on a Linux install.
- stores data that is frequently accessed or written by services or apps running on system.
- log files from running services / apps are stored here.

# <span style="color: rgb(241, 196, 15);">Useful Utilities</span>

## Downloading Files (Wget)

### wget:

- to download files from the web via HTTP
- we simply need to provide the address of the resource that we wish to download.
- `wget https://assets.tryhackme.com/additional/linux-fundamentals/part3/myfile.txt`

## Transferring Files from your Host - SCP (SSH)

### SCP - Secure Copy

- securely copying files
- to transfer files between two computers using SSH protocol (authentication and encryption).
- Copy files & directories from your current system to a remote system
    - `scp important.txt ubuntu@192.168.1.30:/home/ubuntu/transferred.txt`
- Copy files & directories from a remote system to your current system
    - `ubuntu@192.168.1.30:/home/ubuntu/documents.txt notes.txt`

## Serving Files from your Host - WEB

- <img src="../../../_resources/4a8343d40704d00df3e2eb35e91b698b.png" alt="4a8343d40704d00df3e2eb35e91b698b.png" width="702" height="685" class="jop-noMdConv">

# <span style="color: rgb(241, 196, 15);">Processes 101</span>

PID increments for the order in which the process starts.

## Viewing Processes

- ![de8563d7ebeab2195d4075f5926d57ac.png](../../../_resources/de8563d7ebeab2195d4075f5926d57ac.png)
- `ps` command to provide a list of the running processes as our user's session.
- ![7670c1bab774c8a0fe00addc5fb142f1.png](../../../_resources/7670c1bab774c8a0fe00addc5fb142f1.png)
- `ps aux`  to see the processes run by other users and those that don't run from a session.
- ![6834efe1baa197e18de54194622fd0b1.png](../../../_resources/6834efe1baa197e18de54194622fd0b1.png)
- `top` gives <span style="color: rgb(241, 196, 15);">real-time statistics</span> about the processes running on the system.

## Managing Processes

- `Kill + PID`
- some of the signals that we can send to a process when it is killed:
- - SIGTERM - Kill the process, but allow it to do some cleanup tasks beforehand  
        \- SIGKILL - Kill the process - doesn't do any cleanup after the fact  
        \- SIGSTOP - Stop/suspend a process.

## How do processes start?

<span style="color: rgb(241, 196, 15);">**linux OS**</span> uses **<span style="color: rgb(241, 196, 15);">namespaces / Cgroups</span>**

- to ultimately split up the resources available on the computers (such as CPU, RAM and priority) to processes.
- As a large cake, when a process starts, it gonna tell computer to allocate a slices (piece of cake), then it'll be isolated into this field.
- isolating processes from anothers.

<span style="color: rgb(241, 196, 15);">**systemd**</span>

- one of the first processes started.
- any program or piece of software (that we wanna start) will be a <span style="color: rgb(241, 196, 15);">child process of systemd <span style="color: rgb(255, 255, 255);">(controlled by systemd).</span></span>
- <span style="color: rgb(241, 196, 15);"><span style="color: rgb(255, 255, 255);">![1c2ebf7b04c6e28aa77c8bb04248136d.png](../../../_resources/1c2ebf7b04c6e28aa77c8bb04248136d.png)</span></span>

### Getting Processes/ Services to start on Boot

- allow us to interact with the <span style="color: rgb(241, 196, 15);">systemd.</span>
- command: `systemctl [option] [service]`
- options:
    - Start / Stop / Enable / Disable / Status.

### Backgrounding and Foregrounding in Linux

Foregrounding a process: `fg`

Backgrounding a process: `bg`

# <span style="color: rgb(241, 196, 15);">**Maintaining System: Automation**</span>

## cron process

- a process is a background daemon (<span style="color: rgb(241, 196, 15);">crond</span>) managed by <span style="color: rgb(241, 196, 15);">systemd</span>.
- responsible for excuting the commands inside crontab. (each 1 minutes).

## crontab

- started during boot
- edit by command : `crontab -e`
- ![d8140b9407e4bea822d3a576d6c02e9a.png](../../../_resources/d8140b9407e4bea822d3a576d6c02e9a.png)
- 1>/dev/null 2>&1 : means that delete all notifications to <span style="color: rgb(241, 196, 15);">/dev/null (permanently deleted), <span style="color: rgb(236, 240, 241);">cause I shouldn't see it.</span></span>
    - we got two notification stream:
        - 1 (standard ouput - stdout) : common notifications.
        - 2 (standard error - stderr) : error notifications.
- responsible for faciliating and managing <span style="color: rgb(241, 196, 15);">cron jobs.</span>
- a <span style="color: rgb(241, 196, 15);">special text file</span> with formatting that is excuted by the <span style="color: rgb(241, 196, 15);">cron process .</span>
- <span style="color: rgb(255, 255, 255);">crontab requires 6 specific values:</span>

|     |     |
| --- | --- |
| Value | Description |
| MIN | What minute to execute at |
| HOUR | What hour to execute at |
| DOM | What day of the month to execute at |
| MON | What month of the year to execute at |
| DOW | What day of the week to execute at |
| CMD | The actual command that will be executed. |

example:

- `0 */12 * * * cp -R home/cmnatic/Documents /var/backups` : thực hiện sao lưu dữ liệu thư mục Documents và lưu vào thư mục /var/backups tự động mỗi 12h.
- `@reboot /var/opt/processes.sh` excute definitely during boot time.

The way to archive a directory to a lightwieight file and move to a new place:  `tar -czvf /var/backups/backup_doc.tgz /home/cmnatic/Documents`

# <span style="color: rgb(241, 196, 15);">Maintaining System: Logs</span>

- <img src="../../../_resources/540a149d3498b4abd3eab0e60c050f0f.png" alt="540a149d3498b4abd3eab0e60c050f0f.png" width="476" height="344" class="jop-noMdConv">
- three services running on ubuntu machine:
    - An Apache2 web server
        - <img src="../../../_resources/0ce151c739f6d13053e69bbe246f3485.png" alt="0ce151c739f6d13053e69bbe246f3485.png" width="691" height="178" class="jop-noMdConv">
        - important logs:
            - access log
            - error log
    - Logs for the fail2ban service, which is used to monitor attempted brute-forces
    - UFW service which is used as a firewall

&nbsp;