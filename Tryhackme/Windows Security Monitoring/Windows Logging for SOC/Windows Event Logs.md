---
title: Windows Event Logs
updated: 2026-02-28 15:15:56Z
created: 2026-02-28 13:35:11Z
latitude: 20.21299690
longitude: 105.92299000
altitude: 0.0000
---

# **wevtutil.exe (Windows Event Utility)**

enable to retrieve information about event logs and publishers.

use these commands to install / uninstall event manifest, run queries, export/archive/clear logs.

![75c5832f4a095da741dbf95b70021c1a.png](../../../_resources/75c5832f4a095da741dbf95b70021c1a.png)

common options that can be used with Windows Events Utility

![b764f3e893f3b63714ad48775efe6c9d.png](../../../_resources/b764f3e893f3b63714ad48775efe6c9d.png)

![b55a2ec0ed9488337893b7734f667dc4.png](../../../_resources/b55a2ec0ed9488337893b7734f667dc4.png)

**Practice**

**1\. How many log names are in the machine?**

- `wevtutil el` : list out all of name of logs.
- `Measure-Object`: count number of lines.
- \== `(wevtutil el).Count`
- ![5434911268f5fb0d216b739d51aa2bc4.png](../../../_resources/5434911268f5fb0d216b739d51aa2bc4.png)

**2\. What event files would be read when using the query-events command?**

- event log, log file, structured query

**3\. what option would you use to provide a path to a log file?**

- **/lf:true**

&nbsp;

&nbsp;