---
title: Detecting Web DDos
updated: 2026-04-05 17:49:52Z
created: 2026-04-05 14:37:40Z
latitude: 22.27602200
longitude: 114.17514710
altitude: 0.0000
---

**Denial-of-Service (DoS)** attacks can take many forms, but their ultimate aim is to disrupt or completely block access to a website or web service.

<img src="../../../_resources/28ac8bd9a14092bd1f520ddc8b9c5076.png" alt="28ac8bd9a14092bd1f520ddc8b9c5076.png" width="684" height="198" class="jop-noMdConv">

## Distributed Denial-of-Service (DDoS)

<img src="../../../_resources/b02fa002197253f6f5ad78a59e4d4b47.png" alt="b02fa002197253f6f5ad78a59e4d4b47.png" width="765" height="206" class="jop-noMdConv">

- The limitation of a basic DoS attack is that it relies on a single machine and a single internet connection.
- To scale up, attackers turn to **Distributed Denial-of-Service (DDoS)** attacks and utilize botnets, an army of compromised devices under their control.
- An attacker instructs their botnet to swarm your site, and the sudden influx of traffic quickly exhausts your resources, bringing the website down.

## Types of Denial-of-Service Attacks

| DoS Attack Type | Description |
| --- | --- |
| Slowloris | Sending many partial HTTP requests to tie up server resources |
| HTTP Flood | Sending a large number of HTTP requests to overwhelm the server |
| Cache Bypass | Bypassing CDN edge servers and forcing the origin server to respond |
| Oversized Query | Forcing the server to process large, resource-intensive requests |
| Login/Form Abuse | Overloading authentication logic with login attempts or password resets |
| Faulty Input Validation Abuse | Exploiting poorly designed input handling |

# Attack Motives

| Motive | Description | Example Scenario |
| --- | --- | --- |
| [Financial Loss(opens in new tab)](https://www.curotec.com/insights/christmas-hackers-attacks-increase-around-holidays/) | Disrupt services to stop or reduce sales and revenue | Flooding an e-commerce website during peak holiday sales |
| [Extortion(opens in new tab)](https://www.cloudflare.com/learning/ddos/ransom-ddos-attack/) | Demand payment to stop a current attack | Threatening a bank with a ransom DDoS |
| [Hacktivism(opens in new tab)](https://www.zayo.com/resources/how-governments-can-combat-ddos-risk-during-elections/) | Disruption for social or political protest | Attacking government websites during election season |
| [Distraction(opens in new tab)](https://www.cyberdefensemagazine.com/ddos-as-a-distraction/) | Redirect defenders' attention while other attacks take place | Launching a DDoS while attacking other infrastructure |
| [Competition(opens in new tab)](https://digitalmarketingdesk.co.uk/63-of-ddos-attacks-linked-to-competitors/) | Disrupt a rival's service to drive up their costs or gain market share | A competitor launches a DDoS during a product launch |
| [Denial of Wallet(opens in new tab)](https://blog.limbus-medtec.com/the-aws-s3-denial-of-wallet-amplification-attack-bc5a97cc041d) | Force the victim to rack up service usage costs | Attackers repeatedly access AWS S3 data, generating costs per request |
| [Reputational Damage(opens in new tab)](https://stormwall.network/resources/blog/how-ddos-attacks-are-hurting-esports) | Cause customers to lose trust in a company | Game servers crashing during launch day |

# Log Analysis

| Indicator | Example | Description |
| --- | --- | --- |
| High Request Rate | `10.10.10.100` → 1000 `GET /login` | A resource-heavy page like `/login` is flooded with requests to overwhelm authentication processes. Login pages are common targets since each request may trigger password checks and database queries |
| Odd User-Agents | `curl/7.6.88` → `/index` repeatedly | Attackers spoof outdated or unusual User-Agents to blend in or bypass filters. Spotting traffic with tools like `curl` or `Python-urllib/3.x`, for example, can be a red flag for automated attacks |
| Geographic Anomalies | IP address origins dotted around the world | Legitimate traffic typically comes from a few regions where real users are located. A globally distributed botnet may utilize IP addresses from around the world |
| Burst Timestamps | 50 requests in 1 second → `/search` | A sudden spike of requests packed into the same second creates an unnatural traffic pattern that points to automation |
| Server Errors ([5xx(opens in new tab)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status#server_error_responses)) | A significant spike of `503 Service Unavailable` errors | A sudden surge of server error responses (`500` - `511`) indicates resources are maxed out and the service is struggling under attack traffic |
| Logic Abuse | `GET /products?limit=999999` | Attackers craft queries that overload the server, forcing it to load huge amounts of information and slowing it down for everyone |

## Targeted Resources

Pages like `/login` or search forms are prime targets because each request forces the server to query a database, validate input, and return results.

Commonly targeted endpoints and reasoning:

- `/login` - involves authentication processes
- `/search` - requires complex database queries
- `/api` endpoints - critical for dynamic content delivery
- `/register` or `/signup` - requires database writes and validation
- `/contact` or `/feedback` - requires database entries and can trigger email notifications
- `/cart` or `/checkout` - requires session management, inventory checks, and payment processing

## Log Sample

1.  **Normal User Traffic** - Every few seconds, a user requests a page and receives a response as expected
2.  **DoS Attack** - Beginning at `10:01:10`, you can see the IP address `203.0.113.55` begin to send repeated `GET` requests to `/login.php`
3.  **Web Server Down** - Users are requesting pages and receiving `503` responses indicating the service is unavailable![04bb865f3933c1de351111c365b6d4af.png](../../../_resources/04bb865f3933c1de351111c365b6d4af.png)

&nbsp;

# Leveraging SIEMs

<img src="../../../_resources/5adf237563b1fab0abd69724c2c6b8a1.png" alt="5adf237563b1fab0abd69724c2c6b8a1.png" width="1260" height="414">

1.  **Normal User Requests** - A few requests to various pages every minute
2.  **DoS Attack** - 1,000 requests to `/login.php` within a one-minute timeframe
3.  **Requested Pages**

Looking over the same requests but filtering by the user agent (`useragent`) and IP address (`clientip`) fields enables you to see more details about where the requests originated.

<img src="../../../_resources/3c14a1476d55dc8d4476b72030a405b5.png" alt="3c14a1476d55dc8d4476b72030a405b5.png" width="1272" height="568">

## Hands On

What was the most frequently requested `uri`?

<img src="../../../_resources/7d4600de7554d345c906a41a37db9e47.png" alt="7d4600de7554d345c906a41a37db9e47.png" width="786" height="533">

Which `clientip` made the most requests to the target `uri`?

<img src="../../../_resources/b3d2f8555a5b84c632b0f24e1a232a6b.png" alt="b3d2f8555a5b84c632b0f24e1a232a6b.png" width="794" height="538">

Which `useragent` was most commonly used by the attacking traffic?

<img src="../../../_resources/5d12f05b4e22df6c1f8545ee138f73b3.png" alt="5d12f05b4e22df6c1f8545ee138f73b3.png" width="853" height="578">

Use the `timechart` command to visualize the requests.  
What is the peak number of requests made per second during the attack?

<img src="../../../_resources/b6bdb5f093e308eb7a59ea0335c75b44.png" alt="b6bdb5f093e308eb7a59ea0335c75b44.png" width="760" height="515">

Which legitimate (non-attacking) `clientip` received the first `503` response status post-attack?

<img src="../../../_resources/c8f28ec7034ab0316b9a83bd927e1352.png" alt="c8f28ec7034ab0316b9a83bd927e1352.png" width="874" height="592">

- `status=503`: lọc chỉ các request có mã trạng thái 503.
- `NOT clientip="203.0.113.*"`: loại bỏ các IP thuộc botnet (dựa trên dữ liệu trong bài thực hành).
- `dedup clientip, uri`: loại bỏ các bản ghi trùng lặp theo IP và URI để chỉ giữ lại bản đầu tiên.
- `table _time clientip status uri`: hiển thị thời gian, IP, mã trạng thái và URI.
- `sort _time`: sắp xếp theo thời gian tăng dần để xem bản ghi đầu tiên.

&nbsp;

# Defense

## Application Level Defense

**Secure Development Practices**

- web applications need input validation to stop attackers from submitting specialized queries aimed at overloading the system.

**Challenges**

- This could be a CAPTCHA, where the user solves a puzzle, like clicking images or checking a box. For humans, it’s a small step, but for bots, it can block or slow down an attack.<img src="../../../_resources/2a5b60941f74891f95586c47780172b7.png" alt="2a5b60941f74891f95586c47780172b7.png" width="466" height="124">
- Websites can also use JavaScript challenges, which run quietly in the background to confirm if a visitor is a real user or automated traffic.

## Network and Infrastructure Defenses

**Content Delivery Network (CDN)**

- CDNs help manage server load by caching and serving content from edge servers closest to users.
- This reduces latency and allows the origin server to handle only a fraction of requests, while the CDN serves the majority
- As a result, CDNs take on much of the burden of mitigating DDoS attacks.
- They also provide load-balancing to distribute traffic across servers, ensuring no single server is overloaded and rerouting requests if one becomes unavailable.

**Web Application Firewall (WAF)**

- CDNs typically integrate WAFs in an effort to shield their customers' servers.
- you might implement a rate-limiting firewall rule that limits requests to `/login.php` to five per minute.If the originating IP requests the page more than five times, it would be blocked from making future requests for a period of time or provided with a challenge to prove it is a human-made request.
- <img src="../../../_resources/91436ae4317ada9d04ebb107165167a1.png" alt="91436ae4317ada9d04ebb107165167a1.png" width="1018" height="180">