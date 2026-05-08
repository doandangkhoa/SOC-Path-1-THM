---
title: Linux Logging
updated: 2026-03-04 15:10:09Z
created: 2026-03-02 16:25:31Z
latitude: 20.21299690
longitude: 105.92299000
altitude: 0.0000
---

# <span style="color: rgb(241, 196, 15);">Working with Text Logs</span>

### 1\. Nhật ký Hệ thống (System Logs)

- **`/var/log/syslog` :** Đây là nơi lưu trữ hầu hết các thông báo chung của hệ thống, bao gồm cả các thông báo từ các dịch vụ mạng như **Avahi** hay các thông tin về phần cứng.
    
- **`/var/log/kern.log`:** Chứa các thông báo từ Nhân (Kernel) của Linux. Nếu bạn gặp lỗi về Driver hoặc phần cứng bị treo, đây là nơi đầu tiên cần kiểm tra.
    
- **`/var/log/boot.log`:** Lưu lại các thông báo trong quá trình khởi động máy tính, giúp bạn biết dịch vụ nào đã khởi chạy thành công hay thất bại.
    

### 2\. Nhật ký Bảo mật & Đăng nhập (Authentication Logs)

- **`/var/log/auth.log` (Ubuntu/Debian) :** Ghi lại mọi nỗ lực đăng nhập, việc sử dụng lệnh **`sudo`** để lấy quyền root, và các hoạt động của cơ chế bảo mật.
    
- **`/var/log/faillog`:** Chứa thông tin về các lần đăng nhập thất bại. Đây là tệp quan trọng để phát hiện các cuộc tấn công dò mật khẩu (brute-force).
    

### 3\. Nhật ký Ứng dụng & Dịch vụ (Service Logs)

- **`/var/log/cron` (hoặc trong syslog):** Ghi lại lịch sử thực thi của các tác vụ lập lịch. Ví dụ, bạn có thể kiểm tra xem lệnh sao lưu `tar` vào lúc 5 giờ sáng có thực sự chạy hay không tại đây.
    
- **`/var/log/apache2/` hoặc `/var/log/nginx/`:** Nếu bạn chạy web server, đây là nơi lưu trữ nhật ký truy cập (access log) và nhật ký lỗi (error log).
    
- **`/var/log/openvpn/`:** Lưu trữ lịch sử kết nối của **OpenVPN**. Nếu bạn chạy VPN trong background và gặp lỗi, bạn nên kiểm tra file này thay vì nhìn màn hình Terminal.
    

&nbsp;

# <span style="color: rgb(241, 196, 15);">Authentication Logs</span>

- `/var/log/secure`
- <img src="../../../_resources/9ce4162edc5e4f04088cdd17c699324e.png" alt="9ce4162edc5e4f04088cdd17c699324e.png" width="1030" height="229" class="jop-noMdConv">

## Login and Logout Events

- many ways users authenticate into a Linux machine: locally, SSH, "sudo" | "su" commands , cron job, etc.
- each successful <span style="color: rgb(241, 196, 15);">logon/logoff</span> is logged --> filtering the events containing "session opened | session closed" keywords.
- <img src="../../../_resources/eee8a13e544e71a442b78495d36f570d.png" alt="eee8a13e544e71a442b78495d36f570d.png" width="881" height="325" class="jop-noMdConv">
- <span style="color: rgb(241, 196, 15);">SSH</span> daemon stores its own log of successful and failed SSH logins but its format is slightly different.
- ![531121175c6bafb9ff3b47fead800aee.png](../../../_resources/531121175c6bafb9ff3b47fead800aee.png)

## Miscellaneous Events

- detect user management events: password changed, user deletion, adduser, add user to security groups.
- ![03dd4ca021a2c872385db1b78e171275.png](../../../_resources/03dd4ca021a2c872385db1b78e171275.png)
- a threat actor (ubuntu) used `sudo` to stop edr, read firewall state ans finally access root via `sudo su`
- ![5b0be6597cf2f1202a9732e830e37911.png](../../../_resources/5b0be6597cf2f1202a9732e830e37911.png)
    - - **`passwd`**: Ghi lại các sự kiện liên quan đến việc thay đổi mật khẩu của người dùng.
            
            ````
            ```
            - **`useradd`**: Nhật ký về việc tạo thêm tài khoản người dùng mới vào hệ thống.
                
            - **`usermod`**: Các hành động chỉnh sửa thông tin tài khoản hiện có (như đổi tên, thêm vào nhóm `sudo`).
                
            - **`userdel`**: Nhật ký về việc xóa tài khoản người dùng khỏi hệ thống.
            ```
            ````
            
    - **`/bin/`**: Theo truyền thống, đây là nơi chứa các lệnh cực kỳ thiết yếu để hệ thống có thể khởi động hoặc sửa lỗi (như `sh`, `cat`). Tuy nhiên, trên các bản Ubuntu hiện đại, `/bin/` thường chỉ là một liên kết (symlink) trỏ thẳng vào `/usr/bin/`.
        
    - **`/usr/sbin/`**: Chứa các lệnh dành riêng cho quản trị viên hệ thống (System Binaries), ví dụ như lệnh `cron` mà bạn đã thấy trong danh sách tiến trình.
        
    - **`/usr/local/bin/`**: Nơi chứa các chương trình mà bạn tự cài đặt thủ công (không thông qua kho ứng dụng chính thức của Ubuntu) để tránh làm rối các file hệ thống mặc định.
        

# <span style="color: rgb(241, 196, 15);">Common Linux Logs</span>

## Generic System Logs

- `/var/log/kern.log`: Kernel messages and errors, useful for more advanced investigations
- `/var/log/syslog (or /var/log/messages)`: A consolidated stream of various Linux events
- `/var/log/dpkg.log (or /var/log/apt)`: Package manager logs on Debian-based systems
- `/var/log/dnf.log (or /var/log/yum.log)`: Package manager logs on RHEL-based systems

## App-Specific Logs

- ![ddf03de92628ab125e8f97abb76b7de5.png](../../../_resources/ddf03de92628ab125e8f97abb76b7de5.png)

## Bash History

- Bash history ( `~/.bash_history` ): records each command you run after pressing Enter.
- first, it saved temporarily in memory during session
- second, it saved all commands that you enforced into .bash_history when you logout.

&nbsp;

# <span style="color: rgb(241, 196, 15);">Runtime Monitoring</span>

![05d13acc3c42299fbc334f8d99477e53.png](../../../_resources/05d13acc3c42299fbc334f8d99477e53.png)

- any processes created gonna have to call "**<span style="color: rgb(241, 196, 15);">system call"</span>** which will works with kernel / hardware OS.
- nearly no way for attackers to bypass system calls, so EDR will monitor this information so that we can use it for futhur investigations.
- a linux system call usally use to excute a program : `execve`
- example: <span style="color: rgb(241, 196, 15);">**auditd.**</span>

# <span style="color: rgb(241, 196, 15);">**Using auditd**</span>

## <span style="color: rgb(255, 255, 255);">Audit Daemon</span>

<span style="color: rgb(255, 255, 255);"><img src="../../../_resources/4d2b1c94e5da8183f9e9ae748e4970f7.png" alt="4d2b1c94e5da8183f9e9ae748e4970f7.png" width="751" height="178" class="jop-noMdConv"></span>

<span style="color: rgb(255, 255, 255);">built-in auditing solution for runtime monitoring.</span>

<span style="color: rgb(255, 255, 255);">rules / intructions located in `/etc/audit/rules.d/`</span>

## <span style="color: rgb(255, 255, 255);">Auditd ( Audit Daemon)</span>

- <span style="color: rgb(255, 255, 255);">we can view the generated logs in real time in `/var/log/audit/audit.log`</span>
- <span style="color: rgb(255, 255, 255);">using `ausearch -i -k ...`</span>
- ![8563abf98b7a46fec6ee14db48b7b1f7.png](../../../_resources/8563abf98b7a46fec6ee14db48b7b1f7.png)
- this is a single "wget" command divided into 4 lines:
    - 1\. PROCTITLE show the process command line.
    - 2\. CWD : current working directory
    - 3\. EXECVE: system call used.
    - 4\. SYSCALL: details about system call
    - - `pid=3888, ppid=3752`: Process ID and Parent Process ID. Helpful in linking events and building a process tree
        - `auid=ubuntu`: Audit user. The account originally used to log in, whether locally (keyboard) or remotely (SSH)
        - `uid=root`: The user who ran the command. The field can differ from auid if you switched users with sudo or su
        - `tty=pts1`: Session identifier. Helps distinguish events when multiple people work on the same Linux server
        - `exe=/usr/bin/wget`: Absolute path to the executed binary, often used to build SOC detection rules
        - `key=proc_wget`: Optional tag specified by engineers in auditd rules that is useful to filter the events

## Auditd Alternatives

You might have noticed an inconvenient output of auditd - although it provides a verbose logging, it is hard to read and ingest into SIEM. That's why many SOC teams resort to the alternative runtime logging solutions, for example:

- [Sysmon for Linux](https://github.com/microsoft/SysmonForLinux): A perfect choice if you already work with Sysmon and love XML
- [Falco](https://falco.org/): A modern, open-source solution, ideal for monitoring containerized systems
- [Osquery](https://osquery.io/): An interesting tool that can be broadly used for various security purposes
- [EDRs](https://tryhackme.com/room/introductiontoedrs): Most EDR solutions can track and monitor various Linux runtime events

&nbsp;