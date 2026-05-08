---
title: Detecting Web Shell
updated: 2026-04-05 14:30:34Z
created: 2026-04-05 07:30:30Z
latitude: 22.27602200
longitude: 114.17514710
altitude: 0.0000
---

## What is a Web Shell?

- a web shell is a malicious program uploaded to server, enabling adversaries to execute commands remotely.
- Once accesss has been gained on a compromised server, attackers can use a web shell to move through the cyber kill chain.
    - Reconnaissance --> Weponization --> Delivery --> Installation --> Execution --> C2 --> Action on Objectives.
- web shells can be both initial attack and persistence vector if attacker want a long-term access on the compromised system.

# Anatomy of a Web Shell

<img src="../../../_resources/d6e907bd3868cc342d725ecbcaa07dde.png" alt="d6e907bd3868cc342d725ecbcaa07dde.png" width="1071" height="306" class="jop-noMdConv">

1.  Checks if the `cmd` parameter is present in the URL `?cmd=whoami`
2.  Stores the user supplied command in the variable `$cmd`
3.  Executes the command using `shell_exec()`
4.  Displays the output
5.  HTML for the user interface
6.  Command to execute
7.  Output

# Log-Based Detection

## Web Indicators

**Unusual HTTP Methods** **&** **Request Patterns**

- Repeated GET requests in quick succession could mean an attacker is probing for a valid place to upload a shell
- POST requests to valid upload locations following repeated GET requests
- Repeated GET or POST requests to the same file could indicate web shell interaction

<img src="../../../_resources/403b549f794d2d4fe80fb8bac7755578.png" alt="403b549f794d2d4fe80fb8bac7755578.png" width="966" height="458">

**Query Strings**

Part of the URL that associates values with a parameter. `example.php?query=somequery`

- Abnormally long or suspicious query strings, especially containing keywords like `cmd=` or `exec=`
- Encoded query strings. `?query=whoami` becomes `?query=d2hvYW1p` when Base64 encoded

**Sample suspicious web request** including some of the above indicators.

1.  Known malicious or untrusted IP address.
2.  Abnormal timestamp. Perhaps outside of normal business hours.
3.  POST request with a search query string to a malicious file.
4.  No referrer. So this page was accessed directly. (not always a valid indicator)
5.  A suspicious User-Agent string that is not typically associated with a web browser.

![d13991a36a609e7dfb69d41d945ba37c.png](../../../_resources/d13991a36a609e7dfb69d41d945ba37c.png)

## Auditd

![7320d457c096652117b7f4cfb1f7e721.png](../../../_resources/7320d457c096652117b7f4cfb1f7e721.png)

## Web & Auditd Correlation

A suspicious `POST` request in web logs can be linked to an audit event that includes a `creat` or `execve` syscall, showing a script wrote a file or ran commands.

&nbsp;

&nbsp;