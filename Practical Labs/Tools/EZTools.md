---
title: EZTools
updated: 2026-04-30 05:57:18Z
created: 2026-04-30 04:25:40Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

## EvtxEcmd & Timeline Explorer

- EvtxEcmd is a command-line tool which parses Windows Event Logs into different formats such as CSV, JSON, XML, etc.
- Timeline Explorer is a GUI-based tool that functions as a data filtering and navigating application to ease incident responders in handling raw data.
- You may use this tool in conjunction with Timeline Explorer.
- To parse the provided logs, we need first to convert the EVTX logs into CSV using EvtxEcmd and then feed it into Timeline Explorer.
    
    ```
    PS C:\Tools\EvtxECmd> .\EvtxECmd.exe -f 'C:\Users\user\Desktop\Incident Files\sysmon.evtx' --csv 'C:\Users\user\Desktop\Incident Files' --csvf sysmon.csv
    ```
    

For TimelineExplorer.exe, we can load the exported CSV file by doing the following: `File > Open > Choose sysmon.csv from C:\Users\user\Desktop\Incident Files directory`

- <img src="../../../_resources/16dc6137a098acb9815320d0715d353a.png" alt="16dc6137a098acb9815320d0715d353a.png" width="720" height="337" class="jop-noMdConv">

Once the logs are loaded, you may navigate through each column and use the input field to filter specific logs via a unique string.

1.  <img src="../../../_resources/618e79a6f1b06c166ba8505971f70454.png" alt="618e79a6f1b06c166ba8505971f70454.png" width="1329" height="392" class="jop-noMdConv">

Lastly, you may use the search feature in the upper right-hand corner to find a unique string that may exist on any column.

## SysmonView

- SysmonView is a Windows GUI-based tool that visualises Sysmon Logs.
    
- Before using this tool, we must export the log file's contents into XML via **Event Viewer**.
    
- <img src="../../../_resources/dd43861fe0821c23ec99a170d5ad79db.png" alt="dd43861fe0821c23ec99a170d5ad79db.png" width="895" height="477" class="jop-noMdConv">
- Usage:
    
    - Go to `File > Import Sysmon Event Logs` then choose the XML files generated using the Event Viewer.
    - Once loaded, the left sidebar has search functionality that can filter a specific process in mind.
    - Choose the image path and session GUID to render the mapped view.
    - <img src="../../../_resources/e38ebeff8423695f48afa5c52126533c.png" alt="e38ebeff8423695f48afa5c52126533c.png" width="874" height="515" class="jop-noMdConv">
    - This tool can easily view the correlated events from a specific process. The example above summarises all Sysmon events related to **explorer.exe.**