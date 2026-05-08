---
title: Unfolding
updated: 2026-04-27 10:48:37Z
created: 2026-04-27 08:17:16Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

# Alert Escalation

**Escalation Required**

True Positive alert must be escalated if additional actions or remediation are required, or if the alert belongs to a single incident (single attack chain) and is connected to other alerts that require escalation.

**When Escalation IS NOT Required**

- An employee downloaded an unwanted or cracked software from the web, but the downloaded installer was quarantined by AV or removed by the user before execution, before any impact
- A corporate mail server received an email, classified it as phishing, and quarantined it before any users had a chance to access the malicious email
- Botnet scanned the corporate website for common vulnerabilities like XSS and path traversal, and the activity did not cause any performance or security issues

**When Escalation IS Required**

- Threat actor gained access to the corporate server or workstation and ran a port or network scan from there, even if the scan was not successful or no further actions were taken
- Threat actor tried to dump credentials from the breached file server via Mimikatz, but the attempt was blocked by an existing EDR solution
- The alert was identified as part of a larger attack chain but was initially misclassified. Here, an analyst needs to go back and update their case report

&nbsp;

# Alert Reporting

- Provide a clear and detailed explanation of the reason why the activity is classified as TP or FP
- Clearly explain why the alert requires escalation and which remediation actions may be required
- Specify the entities associated with the activity detected by the alert:
    - Identify **who** or **what** was affected
    - Indicate **where** the activity occurred
    - Clarify **when** the activity took place
- Provide all IOCs associated with the activity:
    - **Network Indicators**: IP addresses, Ports, Domains, URLs, etc
    - **Host Indicators**: File Names, File Paths, Hashes, Signatures, etc.
- Specify which goals the threat actor attempted to achieve
- (Optional) Specify which MITRE techniques or tactics the activity can be related to

&nbsp;

### Directory

| Name | Role | Email | Logged-in Host |
| --- | --- | --- | --- |
| Michael Ascot | CEO | [michael.ascot@tryhatme.com](mailto:michael.ascot@tryhatme.com) | win-3450 |
| Sophie J | HR  | [sophie.j@tryhatme.com](mailto:sophie.j@tryhatme.com) | win-3461 |
| Michelle Smith | Legal | [michelle.smith@tryhatme.com](mailto:michelle.smith@tryhatme.com) | win-3459 |
| Roger Fedora | Marketing | [roger.fedora@tryhatme.com](mailto:roger.fedora@tryhatme.com) | win-3460 |
| Yani Zubair | IT  | [yani.zubair@tryhatme.com](mailto:yani.zubair@tryhatme.com) | win-3449 |
| Miguel O'Donnell | Sales | [miguel.odonnell@tryhatme.com](mailto:miguel.odonnell@tryhatme.com) | win-3451 |
| Cain Omoore | Sales | [cain.omoore@tryhatme.com](mailto:cain.omoore@tryhatme.com) | win-3452 |
| Kyra Flores | Sales | [kyra.flores@tryhatme.com](mailto:kyra.flores@tryhatme.com) | win-3453 |
| Amna Espinoza | Sales | [amna.espinoza@tryhatme.com](mailto:amna.espinoza@tryhatme.com) | win-3454 |
| Ashwin Johnston | Sales | [ashwin.johnston@tryhatme.com](mailto:ashwin.johnston@tryhatme.com) | win-3455 |
| Safa Prince | Sales | [safa.prince@tryhatme.com](mailto:safa.prince@tryhatme.com) | win-3456 |
| Diego Summers | Sales | [diego.summers@tryhatme.com](mailto:diego.summers@tryhatme.com) | win-3457 |
| Armaan Terry | Sales | [armaan.terry@tryhatme.com](mailto:armaan.terry@tryhatme.com) | win-3458 |