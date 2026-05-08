---
title: IP and Domain Threat Intel
updated: 2026-04-29 09:18:40Z
created: 2026-04-10 13:56:17Z
latitude: 22.27602200
longitude: 114.17514710
altitude: 0.0000
---

# Scenario

It is Wednesday morning. The SOC has flagged two suspicious domains in phishing emails and three IP addresses in outbound proxy logs. You are tasked with triaging all seven artefacts, enriching them with context, and recommending actions with expiry.

# IP Building Blocks

Our job as analysts is to turn a raw domain into a contextual artefact: who owns it, what IPs it resolves to, how often it changes, and whether it behaves more like a normal content delivery network (CDN) or a throwaway setup.

**Core DNS Records for Triage**

**<img src="../../_resources/c4d162cd010a81dd1764d4d4ea708e2a.png" alt="c4d162cd010a81dd1764d4d4ea708e2a.png" width="307" height="311" class="jop-noMdConv">**

- **A / AAAA Records**: Map the domain to IPv4 and IPv6 addresses.
    - In practice, copy the A record from [nslookup.io(opens in new tab)](https://www.nslookup.io/) or [dnschecker.org(opens in new tab)](https://dnschecker.org/) and follow with pasting the IP into VirusTotal for a quick read.
- **NS Records**: Identify the nameservers controlling the domain. Unusual or recently changed NS entries can mark fresh set up. As L1 analysts, we should note the provider name rather than chasing low-level details.
- **MX Records**: Define which servers handle email. Attackers may configure MX records to deliver phishing campaigns directly. If the alert relates to web browsing, just record whether MX exists.
- **TXT Records**: Store SPF and DKIM rules or verification tags. Poorly configured or absent SPF can increase risk in mail cases.
- **SOA Record**: Points to the zone's primary authority and often includes contact information. It will be worth noting the primary host and serial, which will support a basic ownership picture.
- **TTL (Time To Live)**: Tells resolvers how long to cache answers. Very low TTLs, seconds or minutes, can point to frequent changes, and should be treated as clues.

## Attack Techniques Using DNS

- **Fast Flux Hosting**: Adversaries rotate many IPs quickly with short cache times to avoid simple blocks.
    - We need to record and escalate when we identify a domain that resolves to changing IPs within a short period and across different providers.
- **CDN Abuse**: Legitimate CDNs like Cloudflare or Akamai change IPs too, but done within their ASN ecosystem.
    - If the A record points to a major CDN and other values are normal, take note and carry reputation and ownership checks,
- **Typosquatting**: Domains like paypa1\[.\]com or micros0ft\[.\]net trick users visually.
    - If a name looks like a brand clone, treat it as high risk and escalate it.
- **IDN (Internationalised Domain Names)**: Attackers exploit Unicode, creating look-alike domains. Decode Punycode, for example xn--ppaypal-3ya\[.\]com, and compare to known brands using simple online decoder.

## SOC Analyst Workflow

- **Snapshot Current DNS**: Capture A, NS, MX, TXT, SOA, and TTL values for the domain in question using a single page view and simple.
- **Basic Ownership Check**: Use WHOIS to note registrat, creation date and contact pattern, which supports a light ownership picture of the ticket.
- **Interpret Patterns**: Assess whether the DNS behaviour aligns with benign CDN activity or indicates malicious throwaway domain, noting down the details of the changing IPs.
- **Log Evidence**: Save screenshots or JSON extracts DNS and reputation pages to the case file for audit and escalation.
- **Recommend Action**: Based on findings, advise blocking if high risk, monitor if suspicious but inconclusive, or close if determined benign.

# IP Enrichment: Geolocation and ASN

- **Enrichment** is adding ownership, ASN (Autonomous System Number), geolocation, and service context to an IP so that our decision is evidence-driven.
- SOC Level 1 analysts must perform this consistently, since IPs are the most common indicators in alert queues.

## The Role of RDAP

The **Registration Data Access Protocol (RDAP)** is the authoritative source for IP ownership.

From RDAP, we obtain:

- **NetRange**: The range of addresses delegated.
- **Organisation**: The registered holder (e.g., Amazon, Vodafone, TryHackMe).
- **Remarks**: Often include whether the block is used for hosting, broadband, or mobile.
- **Abuse Contact**: The official mailbox for incident reporting.

<img src="../../_resources/3802ae09e55872bb4fdf8abeb0a02ba6.png" alt="3802ae09e55872bb4fdf8abeb0a02ba6.png" width="651" height="643" class="jop-noMdConv">

## Autonomous Systems and Heuristics

#### **Autonomous System (AS)**

- **What it is**: A group of IP prefixes controlled by one organization (e.g., AWS, Comcast, Cloudflare).
- **ASN (Autonomous System Number)**: A unique ID (16 or 32-bit) used for routing.

**Heuristics**:

- **Hosting ASNs** → Often have many small, diverse tenants → suspicious domains may hide here.
- **Residential ISPs** → Huge IP ranges → alerts may mean compromised home devices.
- **Cloud/CDN ASNs** → Shared global infrastructure → blocking entire ranges causes collateral damage.

## Geolocation: Value and Limitations

GeoIP is widely used but often misunderstood. Tools like [ipinfo.io](https://ipinfo.io/) and [iplocation.net](https://iplocation.net/) provide approximate country and city. However, it is worth observing that:

- **Country mismatches** are common. CDN and cloud providers may register ranges in one country but host edges globally.
- **City-level accuracy** is unreliable. SOC analysts should never justify a block based on a city.

**Best practice**: Record the country reported by at least two sources and note discrepancies. Treat this as a hint, not a fact.

## SOC Analyst Workflow

- **Start with RDAP**: Confirm netrange, org, ASN, and abuse contacts.
- **Add ASN Context**: Check bgpview.io or ipinfo.io for ASN details and role.
- **Check Geolocation**: Capture country from at least two sources. Record mismatches.
- **Look for rDNS Patterns**: Reverse DNS can hint at hosting type (e.g., \*\[.\]btcentralplus\[.\]com = UK broadband). Do not base decisions solely on rDNS.
- **Consult Internal Logs**: Has this IP appeared in the last 30 days? If yes, in what context?
- **Classify Role**: Hosting, residential, CDN, or cloud. Record reasoning.
- **Plan Outreach**: If confirmed malicious and in a cooperative ASN, prepare a report for the abuse contact.

# Service Exposure

### 1\. Mục tiêu của việc làm giàu thông tin (Enrichment)

Việc phân tích IP và tên miền không chỉ dừng lại ở địa chỉ đó. Mục đích là để:

- Xác định **chức năng** của hệ thống (ví dụ: máy chủ web, máy chủ cơ sở dữ liệu, hay thiết bị IoT).
- Đánh giá **mức độ rủi ro** (blast radius) nếu hệ thống bị tấn công.
- Phát hiện các **dịch vụ phơi nhiễm** (exposed services) trên các cổng (port) mở.
    - *Ví dụ:* Một IP có cổng `3389` mở thường là dịch vụ Remote Desktop (RDP), dễ bị tấn công brute-force.

### 2\. Các công cụ và kỹ thuật 

#### A. Shodan (Công cụ do thám)

- **Chức năng:** Tìm kiếm các thiết bị và dịch vụ kết nối internet. Nó hiển thị các cổng mở, dịch vụ đang chạy và cấu hình hệ thống.
- **Cách dùng:**
    - Tìm kiếm theo IP cụ thể (ví dụ: `69.197.185.26`).
    - Tìm kiếm theo tổ chức: `org:example.com` để tìm tất cả hệ thống của một công ty.
    - Tìm kiếm theo phiên bản phần mềm để xác định hệ thống dễ bị tấn công.
- **Ứng dụng trong bài:** Xác định tên dịch vụ đầu tiên bị phơi nhiễm và số lượng cổng mở trên một IP cụ thể (ví dụ: `85.188.1.133`).

#### B. Censys (Giải pháp thay thế cho Shodan)

- **Chức năng:** Tương tự Shodan nhưng thường được ưa chuộng bởi các nhóm phòng thủ (Blue Teams).
- **Điểm mạnh:** Có thể phát hiện dịch vụ phơi nhiễm ngay cả trên các **cổng không chuẩn** (non-standard ports) và có các tính năng tìm kiếm nâng cao.
- **Ứng dụng:** Tìm fingerprint của chứng chỉ TLS.

#### C. Certificate Transparency (Crt.sh)

- **Chức năng:** Tra cứu nhật ký chứng chỉ TLS/SSL công khai. Đây là kho dữ liệu vàng để làm giàu thông tin.
- **Các trường dữ liệu quan trọng cần xem xét:**
    1.  **Issuer (Người phát hành):**
        - *Let's Encrypt:* Phổ biến, trung lập.
        - *Self-signed (Tự ký):* Có thể là dấu hiệu của hệ thống triển khai vội vàng hoặc độc hại.
    2.  **Validity Period (Thời hạn):**
        - Chứng chỉ ngắn hạn (90 ngày) là bình thường.
        - *Dấu hiệu bất thường:* Hàng loạt chứng chỉ được cấp lại đột ngột (burst) có thể ám chỉ cơ sở hạ tầng lừa đảo (phishing).
    3.  **Subject Alternative Names (SAN):** Danh sách các tên miền được bảo vệ bởi chứng chỉ đó.
    4.  **Common Name (CN):** Tên phổ biến của chủ thể trong chứng chỉ.

### 3\. Quy trình làm việc của Analyst (Analyst Workflow)

1.  **Dùng Shodan:** Nhập IP để tìm dịch vụ bị phơi nhiễm đầu tiên và đếm số cổng mở.
2.  **Dùng Censys:** Tìm kiếm cùng IP đó để lấy **TLS certificate fingerprint**.
3.  **Dùng crt.sh:** Nhập fingerprint từ bước 2 để tìm thông tin chi tiết của chứng chỉ, cụ thể là **Common Name** của chủ thể.

# Reputation Checks and Passive DNS

**Mục tiêu chính:** Nâng cao hiểu biết về các IP và tên miền (domain) bằng cách kết hợp **trí tuệ mối đe dọa nguồn mở (OSINT)**, tập trung vào **độ tin cậy (reputation)** và **lịch sử** thay vì chỉ xác định chủ sở hữu hay dịch vụ hiện tại.

**Các điểm quan trọng:**

- **Tính động của tài sản:** Khác với hash file (tĩnh), IP và domain là các tài sản động. Một domain có thể được dùng cho chiến dịch độc hại hôm nay và bị bỏ hoang hoặc chuyển sang mục đích thiện chí vào tuần sau. Do đó, **bối cảnh thời gian** là yếu tố sống còn.
- **Nguồn dữ liệu độ tin cậy (Reputation Services):**
    - **VirusTotal:** Cung cấp tỷ lệ phát hiện và các mối quan hệ chỉ báo.
    - **Cisco Talos Intelligence:** Cung cấp điểm số độ tin cậy (web/email) và nhãn phân loại được cập nhật thường xuyên.
    - **IP2Proxy:** Giúp xác định các proxy, datacenter, và các node mạng Tor (điểm thoát hợp lệ có thể làm mờ đi việc truy vết nguồn gốc).
- **Phân tích DNS thụ động (Passive DNS):** Cung cấp lịch sử ghi lại cách một domain được phân giải theo thời gian, giúp xây dựng bức tranh toàn cảnh về hoạt động trong quá khứ.
- ## SOC Analyst Workflow
    
    In summary, our workflow in the SOC would look as follows. Be mindful that this would vary depending on established organisational processes and practices.
    
    - **Check VirusTotal**: Record detection ratio, First Seen, Last Seen, and any community notes.
    - **Check Cisco Talos**: Record reputation score and category, noting any changes in the last 30 days.
    - **Check IP2Proxy**: Flag if VPN/proxy/Tor; adjust severity accordingly.
    - **Check Passive DNS**: Record First Seen, Last Seen, number of IPs in the last 7 days, and ASN spread.
    - **Check CT Logs**: Note certificate bursts, suspicious SANs.
    - **Cross-Reference with Wayback**: Identify content shifts (benign → phishing).
    - **Decision**: Block, monitor, or close, with expiry tied to observed activity.

# Operational Integration

**Mục tiêu cốt lõi:** Giúp các nhà phân tích chuyển hóa dữ liệu tình báo thành hành động an toàn, cân bằng giữa việc ngăn chặn tấn công và tránh làm gián đoạn các ứng dụng kinh doanh hợp lệ.

**Các nguyên tắc tích hợp an toàn:**

1.  **Tránh chặn theo vị trí địa lý (Geofencing) một cách mù quáng:**
    
    - Việc chặn theo quốc gia thường gây lỗi cho quy trình làm việc thực tế (nhân viên đi công tác, dịch vụ của bên thứ ba).
    - *Khuyến nghị:* Chỉ dùng vị trí địa lý để **nâng cao mức độ ưu tiên** điều tra, không dùng làm biện pháp chặn chính, trừ khi đã được kiểm thử và phê duyệt bởi bộ phận kinh doanh.
2.  **Tránh chặn các dải IP lớn của nhà cung cấp đám mây:**
    
    - Không đưa toàn bộ dải IP của các nhà cung cấp lớn (như Amazon, Microsoft, CDN) vào danh sách chặn.
    - *Lý do:* Các nhà cung cấp này chia sẻ IP cho nhiều khách hàng; chặn dải IP sẽ vô tình chặn cả hệ thống hợp lệ.
    - *Khuyến nghị:* Nếu phát hiện tên miền độc hại nằm trên mạng đám mây, hãy hành động ở cấp độ **tên miền hoặc đường dẫn (path)**, hoặc liên hệ bộ phận lạm dụng của nhà cung cấp.
3.  **Xem xét pháp lý và nhà cung cấp:**
    
    - Xác định nhà cung cấp và quốc gia để đánh giá khả năng bảo tồn bằng chứng hoặc yêu cầu gỡ bỏ nội dung nhanh chóng.
    - Ghi lại thông tin chủ sở hữu RIR và liên hệ lạm dụng (abuse contacts) từ RDAP để làm đường dây nâng cấp vấn đề (escalation paths).

## From Data to Decision

We can now follow a simple playbook to make informed decisions when investigating an indicator.

- Verify: Confirm that the indicator appears in our telemetry and is relevant to our environment.
- Enrich: Collect geolocation, ASN, banners, certificates, reputation, and history.
- Score: Apply the confidence matrix and record the rationale.
- Decide: Block, monitor, or allow. Prefer precise controls, add expiry, and document.
- Hunt and notify: Search for related indicators, inform stakeholders, and create follow-up tasks.

# Practice

**RIR** là viết tắt của **Regional Internet Registry** (Hội quản lý số nguyên Internet theo khu vực).

Đây là các tổ chức phi lợi nhuận chịu trách nhiệm phân phối và đăng ký các địa chỉ IP (IPv4 và IPv6) cùng các số ASN (Autonomous System Number) cho các nhà cung cấp dịch vụ Internet (ISP) và các tổ chức khác trong một khu vực địa lý cụ thể trên thế giới.

Thế giới hiện được chia thành 5 khu vực RIR chính:

1.  **ARIN** (American Registry for Internet Numbers): Bắc Mỹ, một phần của Caribe.
2.  **RIPE NCC** (Réseaux IP Européens Network Coordination Centre): Châu Âu, Trung Đông, Trung Á.
3.  **APNIC** (Asia-Pacific Network Information Centre): Châu Á, châu Đại dương.
4.  **LACNIC** (Latin America and Caribbean Network Information Centre): Mỹ Latinh và Caribe.
5.  **AFRINIC** (African Network Information Centre): Châu Phi.

&nbsp;