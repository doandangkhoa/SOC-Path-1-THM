---
title: Core Windows Processes
updated: 2026-03-21 21:42:56Z
created: 2026-02-27 04:39:49Z
latitude: 20.21299690
longitude: 105.92299000
altitude: 0.0000
---

# A. Task Manager

- ![8f7ebcbe088a71bfe79caf285f765706.png](../../../_resources/8f7ebcbe088a71bfe79caf285f765706.png)

&nbsp;       is a built-in GUI based Windows utility in which allows user to see general information about the system.

# B. System

- The first windows process gonna be run.
- `System's PID will always be 4.`
- runs only in kernel mode (kernel-mode system thread).
- executing code loaded in system space.

![ecdb2b7e32905b90c8758b510f3efd20.png](../../../_resources/ecdb2b7e32905b90c8758b510f3efd20.png)![79c5b31439761f5763912f6abe039afd.png](../../../_resources/79c5b31439761f5763912f6abe039afd.png)

| Path: N/A   <br><br/>**Parent Process**: None  <br><br/>**Number of Instances**: One  <br><br/>**User Account**: Local System  <br><br/>**Start Time**: At boot time | **Image Path**: C:\\Windows\\system32\\ntoskrnl.exe (NT OS Kernel)  <br><br/>**Parent Process**: System Idle Process (0) |
| --- | --- |

# C. System > smss.exe

**<span style="color: rgb(241, 196, 15);">smss.exe</span> (Session Manager Subsystem == Windows Session Manager)**

- responsible for creating new sessions.
- includes <span style="color: rgb(241, 196, 15);">win32k.sys</span> (kernel mode), <span style="color: rgb(241, 196, 15);">winsrv.dll</span> (user mode), <span style="color: rgb(241, 196, 15);">csrss.exe</span> (user mode)
- first user-mode started by the kernel.
- starts <span style="color: rgb(241, 196, 15);">csrss.exe</span> (Windows subsystem) & <span style="color: rgb(241, 196, 15);">wininit.exe</span> in session 0 ![651cc4977a12606e7dc0e824cd36d854.png](../../../_resources/651cc4977a12606e7dc0e824cd36d854.png)
    - <span style="color: rgb(241, 196, 15);">csrss.exe</span> & <span style="color: rgb(241, 196, 15);">winlogon.exe</span> for session 1 (User session) ![59199219f143958554d6b57d56f2e136.png](../../../_resources/59199219f143958554d6b57d56f2e136.png).
- the first child instance creates child instances in new sessions by copying itself and self-terminating.
- responsible for creating environment variables, virtual memory paging files and starts <span style="color: rgb(241, 196, 15);">winlogon.exe.</span>

![ed4b298a02ff9a8003b8d186674ea386.png](../../../_resources/ed4b298a02ff9a8003b8d186674ea386.png)

**Path**: %SystemRoot%\\System32\\smss.exe

**Parent Process**: System

**Number of Instances**: One master instance and child instance per session. The child instance exits after creating the session.

**User Account**: Local System

**Start Time**: `Within seconds of boot time` for the master instance

# D. csrss.exe

**csrss.exe (Client Server Runtime Process)**

- user-mode side of the windows subsystem.
- is always running and critical to system operation. If this process is terminated by chance --> system failure.
- responsible for the Win32 sybsystem,  lifecycle processing, thread management (create/delete).
- making API available to other processes, mapping drive letters, handling Windows shutdown process.
- created by instace of smss.exe

&nbsp;                                       **Session 0 (PID 392)**

![bbb7a59c5c18db65657998d53f0b12a7.png](../../../_resources/bbb7a59c5c18db65657998d53f0b12a7.png)

&nbsp;                                               **Session 1 (PID 512)**

![fe9804a7c7cfb087fd9e12a33aa32e09.png](../../../_resources/fe9804a7c7cfb087fd9e12a33aa32e09.png)

&nbsp;Path: %SystemRoot%\\System32\\csrss.exe

**Parent Process**: Created by an instance of smss.exe

**Number of Instances**: Two or more

**User Account**: Local System

**Start Time**: Within seconds of boot time for the first two instances (for Session 0 and 1). Start times for additional instances occur as new sessions are created, although only Sessions 0 and 1 are often created.

# E. wininit.exe

**wininit.exe (windows initialization process)**

responsible for launching (within session 0) :

- <span style="color: rgb(241, 196, 15);">services.exe</span> (Service Control Manager)
- <span style="color: rgb(241, 196, 15);">lsass.exe</span> (Local Security Authority)
- <span style="color: rgb(241, 196, 15);">lsaiso.exe</span> (associated with Credential Guard and KeyGuard)

![65ae69db2b1a3d8c276ad20f108e4470.png](../../../_resources/65ae69db2b1a3d8c276ad20f108e4470.png)

**Image Path**: %SystemRoot%\\System32\\wininit.exe

**Parent Process**: Created by an instance of smss.exe

**Number of Instances**: One

**User Account**: Local System

**Start Time**: Within seconds of boot time

# F. wininit.exe > services.exe

services.exe (Service Control Manager - SCM)

- responsible for handle system services.
- information regarding services is stored in the registry, `HKLM\System\CurrentControlSet\Services`
- ![780de10b3fa583eba51826b3844ff955.png](../../../_resources/780de10b3fa583eba51826b3844ff955.png)
- is the parent of <span style="color: rgb(241, 196, 15);">svchost.exe</span>, <span style="color: rgb(241, 196, 15);">spoolsv.exe</span>, <span style="color: rgb(241, 196, 15);">msmpeng.exe</span>, <span style="color: rgb(241, 196, 15);">dllhost.exe</span>.

![aa581659ce567e16fc3fdf5daef42000.png](../../../_resources/aa581659ce567e16fc3fdf5daef42000.png)![11c6198f53a4f0c4493548f263029e52.png](../../../_resources/11c6198f53a4f0c4493548f263029e52.png)

**Image Path**: %SystemRoot%\\System32\\services.exe

**Parent Process**: wininit.exe

**Number of Instances**: One

**User Account**: Local System

**Start Time**: Within seconds of boot time

# G. wininit.exe > service.exe > svchost.exe

**svchost.exe (Service Host - Host process for Windows Services) : responsible for hosting and managing Windows Services.**

the services running in this process are implemented as DLLs, which is stored in the registry

![79ee2ba201ae77ba6bf01a352b264b35.png](../../../_resources/79ee2ba201ae77ba6bf01a352b264b35.png)

![c5ea454d4850e001f3780594b3b5c662.png](../../../_resources/c5ea454d4850e001f3780594b3b5c662.png)

- the -k parameter in binary path is grouping similar services to share the same process to reduce resource consumption.
- usually has been a target for malicious activities because svchost.exe have to handle many running process.

![9c5e49f028c8f9130f1fdaa1932b6a4e.png](../../../_resources/9c5e49f028c8f9130f1fdaa1932b6a4e.png)

**Image Path**: %SystemRoot%\\System32\\svchost.exe

**Parent Process**: services.exe

**Number of Instances**: Many

**User Account**: Varies (SYSTEM, Network Service, Local Service) depending on the svchost.exe instance. In Windows 10, some instances run as the logged-in user.

**Start Time**: Typically within seconds of boot time. Other instances of svchost.exe can be started after boot.

# H. lsass.exe

**lsass.exe (Local Security Authority Subsystem Service)**

- a process in Microsoft Windows OS responsible for enforcing security policy on the system.
- verifies users logging on / passwords changes / created access tokens.
- writes to Windows Security Log.
- creates security tokens for SAM (Security Account Manager) , AD (Active Directory).
- uses authentication packets specified in `HKLM\System\CurrentControlSet\Control\Lsa`
- usually targeted by adversaries ( by tools such as <span style="color: rgb(241, 196, 15);">mimikatz</span>)

![69d372e5d73a6d80352cda32da6e8e6b.png](../../../_resources/69d372e5d73a6d80352cda32da6e8e6b.png)

**Image Path**: %SystemRoot%\\System32\\lsass.exe

**Parent Process**: wininit.exe

**Number of Instances**: One

**User Account**: Local System

**Start Time**: Within seconds of boot time

# I. winlogon.exe

- handling the Secure Attention Sequence (SAS) ensure that user's login information is passed in legitimate place (by ALT + CTRL + DELETE).
- responsible for locking screen (windows + L) and running user's screensaver.
- created by instance of smss.exe

![e3793ee0517f2da629b40fb1c059f50b.png](../../../_resources/e3793ee0517f2da629b40fb1c059f50b.png)

![53e3b82c1fe1862a80240a85070ae20b.png](../../../_resources/53e3b82c1fe1862a80240a85070ae20b.png)![e37b970d2421b9e8196cec79b7966e41.png](../../../_resources/e37b970d2421b9e8196cec79b7966e41.png)

**Image Path**: %SystemRoot%\\System32\\winlogon.exe

**Parent Process**: Created by an instance of smss.exe that exits, so analysis tools usually do not provide the parent process name.

**Number of Instances**: One or more

**User Account**: Local System

**Start Time**: Within seconds of boot time for the first instance (for Session 1). Additional instances occur as new sessions are created, typically through Remote Desktop or Fast User Switching logons.

# K. explorer.exe

(Windows Explorer)

gives the user access to their folders and files.

![4cf87041c05b182d62f09565def8c93f.png](../../../_resources/4cf87041c05b182d62f09565def8c93f.png)![c087e58e5c27953b05436e9bfe7b26bc.png](../../../_resources/c087e58e5c27953b05436e9bfe7b26bc.png)

**Image Path**: %SystemRoot%\\explorer.exe

**Parent Process**: Created by userinit.exe and exits

**Number of Instances**: One or more per interactively logged-in user

**User Account**: Logged-in user(s)

**Start Tim**e: First instance when the first interactive user logon session begins