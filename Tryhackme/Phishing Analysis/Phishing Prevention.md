---
title: Phishing Prevention
updated: 2026-04-29 04:19:11Z
created: 2026-04-27 03:06:25Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

# Sender Policy Framework

- Sender Policy Framework (SPF) is used to authenticate the sender of an email. With an SPF record in place, Internet Service Providers can verify that a mail server is authorized to send email for a specific domain.
- An SPF record is a DNS TXT record containing a list of the IP addresses that are allowed to send email on behalf of your domain.

![49fa56a5be9f5bf93d594e60b9342484.png](../../_resources/49fa56a5be9f5bf93d594e60b9342484.png)

## SPF Records

`v=spf1 ip4:127.0.0.1 include:_spf.google.com -all`

- `v=spf1` Signifies the start of the SPF record
- `ip4:127.0.0.1` Specifies which IP can send mail (IPv4 in this case)
- `include:_spf.google.com` Specifies which domain can send mail
- `-all` Non-authorized emails will be rejected

## Tools

The **[SPF Surveyor](https://dmarcian.com/spf-survey/)** tool from dmarcian enables us to gain a visual look at DNS records.

![e70c9c6d83c691fb9cefecc4bdc66734.png](../../_resources/e70c9c6d83c691fb9cefecc4bdc66734.png)

**Google Admin Toolbox** [**Messageheader**](https://toolbox.googleapps.com/apps/messageheader/) allows you to analyze delivery details using an email's full header.

![1fb512e6f53b46939019e95d7d7e622b.png](../../_resources/1fb512e6f53b46939019e95d7d7e622b.png)

# DomainKeys Identified Mail (DKIM)

![72e19aca0ef693e53d0e89cc64e7ac60.png](../../_resources/72e19aca0ef693e53d0e89cc64e7ac60.png)

- DKIM stands for DomainKeys Identified Mail and is used for the authentication of an email that’s being sent.
- Like SPF, DKIM is an open standard for email authentication that is used for DMARC alignment
- A DKIM record exists in the DNS, but it is more complex than SPF
- DKIM’s advantage is that it can survive forwarding, which makes it superior to SPF and a foundation for securing your email.

## DKIM Records

Here is a sample DKIM record, along with its components.

`v=DKIM1; k=rsa; p=<public_key>`

- `v=DKIM1` Specifies the version of DKIM being used (optional)
- `k=rsa` The key type. The RSA encryption algorithm is standard
- `p=` This is the public key that will be matched to the private key to verify the DKIM signature

## Tools

You can also check out their **[DKIM Record Checker](https://dmarcian.com/dkim-inspector/)** and **[Validator](https://dmarcian.com/dkim-validator/).**

# **Domain-Based Message Authentication, Reporting, and Comformance (DMARC)**

DMARC, an open source standard, uses a concept called alignment to tie the result of two other open source standards, SPF (a published list of servers that are authorized to send email on behalf of a domain) and DKIM (a tamper-evident domain seal associated with a piece of email), to the content of an email.

This means that DMARC ensures the sender's domain matches the domains verified by SPF and DKIM. If the alignment fails, DMARC instructs the recipient server on how to handle the email based on a policy specified in the record.

## DMARC Records

`v=DMARC1; p=quarantine; rua=mailto:postmaster@website.com`

- `v=DMARC1`: The version of DMARC (required)
- `p=quarantine` The DMARC policy (quarantine = move to the spam folder)
- `rua=mailto:postmaster@website.com` An optional tag. In this case, aggregate reports will be sent to the email specified

## DMARC hoạt động như thế nào?

1.  **Kết hợp SPF và DKIM:**
    
    - Khi một email đến, máy chủ nhận sẽ kiểm tra SPF và DKIM.
    - DMARC yêu cầu **căn chỉnh (alignment)** giữa tên miền trong phần `From:` của email và tên miền được xác thực bởi SPF hoặc DKIM.
    - **Căn chỉnh SPF:** Tên miền trong SPF phải khớp với tên miền trong `From:`.
    - **Căn chỉnh DKIM:** Tên miền trong DKIM (thường nằm trong chữ ký) phải khớp với tên miền trong `From:`.
2.  **Chính sách (Policy):**
    
    - Chủ sở hữu tên miền công bố chính sách DMARC của mình trong bản ghi DNS (dưới dạng bản ghi TXT).
    - Chính sách này cho máy chủ nhận biết phải làm gì nếu email **thất bại** trong việc xác thực SPF hoặc DKIM:
        - `p=none`: Chỉ giám sát, không hành động (chỉ thu thập báo cáo).
        - `p=quarantine`: Đánh dấu email vào thư mục Spam/Junk.
        - `p=reject`: Từ chối hoàn toàn email (không gửi đến hộp thư).
3.  **Báo cáo (Reporting):**
    
    - DMARC cho phép chủ sở hữu tên miền nhận được báo cáo từ các máy chủ nhận về các email được gửi nhân danh tên miền của họ.
    - Có hai loại báo cáo:
        - **Báo cáo tổng hợp (Aggregate Reports - `rua`):** Cung cấp thông tin tổng quan về tất cả email được gửi, bao gồm các email thành công và thất bại.
        - **Báo cáo sự cố (Forensic Reports - `ruf`):** Cung cấp thông chi tiết về từng email cụ thể bị thất bại.

### Tóm tắt quy trình DMARC:

1.  Nhận email.
2.  Kiểm tra SPF và DKIM.
3.  Kiểm tra xem SPF hoặc DKIM có **đủ điều kiện (alignment)** với tên miền `From:` không.
4.  Nếu cả SPF và DKIM đều thất bại căn chỉnh:
    - Áp dụng chính sách DMARC (`p=none`, `p=quarantine`, hoặc `p=reject`).
5.  Gửi báo cáo cho chủ sở hữu tên miền (nếu được cấu hình).

## Tool

Another great [tool](https://dmarcian.com/domain-checker/) by dmarcian that inspects DMARC, SPF, and DKIM records to identify any issues

![58a130b88afbc70f0f5f4df5207089ce.png](../../_resources/58a130b88afbc70f0f5f4df5207089ce.png)

# Bảng so sánh tổng quan

| Đặc điểm | **SPF (Sender Policy Framework)** | **DKIM (DomainKeys Identified Mail)** | **DMARC (Domain-based Message Authentication, Reporting & Conformance)** |
| --- | --- | --- | --- |
| **Mục đích chính** | Xác thực **địa chỉ IP** của máy chủ gửi. | Xác thực **tính toàn vẹn** của email và **chữ ký số** của người gửi. | **Kết hợp** SPF và DKIM để đưa ra **quyết định cuối cùng** (từ chối, đánh dấu spam) và cung cấp **báo cáo**. |
| **Cách hoạt động** | Kiểm tra xem IP của máy chủ gửi có nằm trong danh sách được phép (bản ghi DNS) của tên miền người gửi không. | Dùng **khóa riêng tư** để ký vào email, máy chủ nhận dùng **khóa công khai** (trong DNS) để xác minh chữ ký. | Yêu cầu **căn chỉnh (alignment)** giữa tên miền trong phần `From:` và tên miền được xác thực bởi SPF hoặc DKIM. Áp dụng chính sách nếu thất bại. |
| **Dữ liệu lưu trữ** | Bản ghi DNS loại **TXT**. | Bản ghi DNS loại **TXT** (chứa khóa công khai). | Bản ghi DNS loại **TXT** (chứa chính sách và địa chỉ báo cáo). |
| **Yếu tố xác thực** | **Địa chỉ IP** của máy chủ gửi. | **Chữ ký số** (Digital Signature) được gắn vào email. | **Sự kết hợp** của SPF và DKIM + **Căn chỉnh tên miền**. |
| **Bảo vệ gì?** | Chống giả mạo **IP** (máy chủ gửi). | Chống sửa đổi **nội dung email** và giả mạo **chữ ký**. | Chống **giả mạo tên miền** (spoofing) và cung cấp **thông tin** về email giả mạo. |
| **Khi email thất bại** | Máy chủ nhận có thể từ chối hoặc đánh dấu spam (tùy cấu hình). | Máy chủ nhận có thể từ chối hoặc đánh dấu spam (tùy cấu hình). | **Tuân thủ chính sách DMARC**: `none` (chỉ báo cáo), `quarantine` (spam), `reject` (từ chối). |
| **Báo cáo** | Không có báo cáo tích hợp. | Không có báo cáo tích hợp. | **Có**: Báo cáo tổng hợp (`rua`) và báo cáo sự cố (`ruf`). |
| **Độ phức tạp** | Dễ thiết lập, ít bảo trì. | Phức tạp hơn (cần quản lý cặp khóa). | Trung bình (cần cấu hình chính sách và theo dõi báo cáo). |
| **Vai trò** | "Thẻ căn cước" của máy chủ gửi. | "Con dấu niêm phong" của người gửi. | "Người cảnh sát" ra quyết định cuối cùng. |
| **Ví dụ bản ghi DNS** | `v=spf1 include:_spf.google.com -all` | `v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQ...` | `v=DMARC1; p=reject; rua=mailto:dmarc@example.com` |

### Mối quan hệ giữa SPF, DKIM và DMARC

- **SPF và DKIM** là hai công cụ xác thực độc lập. Bạn có thể dùng một trong hai, nhưng tốt nhất là dùng cả hai.
- **DMARC** không thay thế SPF hay DKIM, mà nó **phụ thuộc** vào chúng. DMARC yêu cầu ít nhất một trong hai (SPF hoặc DKIM) phải **thành công** và **căn chỉnh** với tên miền `From:`.
- Nếu SPF và DKIM đều thất bại (hoặc không căn chỉnh), DMARC sẽ áp dụng chính sách của bạn (từ chối, đánh dấu spam, hoặc chỉ báo cáo).

### Tóm tắt quy trình xác thực email hoàn chỉnh:

1.  **Người gửi** gửi email → Máy chủ gửi thêm chữ ký DKIM và dùng IP được phép (SPF).
2.  **Máy chủ nhận** nhận email → Kiểm tra SPF (IP có được phép không?) và DKIM (chữ ký có hợp lệ không?).
3.  **Máy chủ nhận** kiểm tra DMARC:
    - SPF hoặc DKIM có **căn chỉnh** với tên miền `From:` không?
    - Nếu có → Email được chấp nhận.
    - Nếu không → Áp dụng chính sách DMARC (`p=none`, `p=quarantine`, `p=reject`).
4.  **Chủ sở hữu tên miền** nhận báo cáo DMARC để theo dõi và điều chỉnh chính sách.

# Secure/Multipurpose Internet Mail Extensions (S/MIME)

S/MIME (Secure/Multipurpose Internet Mail Extensions) là một giao thức tiêu chuẩn cho việc gửi email được ký số và mã hóa. Dựa trên công nghệ mã hóa khóa công khai (public key cryptography), S/MIME đảm bảo tính bảo mật và xác thực trong giao tiếp email.

### Các thành phần chính của S/MIME:

1.  **Chữ ký số (Digital Signature)**
    
    - Người gửi ký điện tử thông điệp bằng **khóa riêng (private key)** của mình.
    - Người nhận xác minh danh tính người gửi bằng **khóa công khai (public key)** của họ.
    - **Mục đích**: Xác thực danh tính người gửi và đảm bảo tính toàn vẹn của nội dung (không bị thay đổi).
2.  **Mã hóa (Encryption)**
    
    - Người gửi mã hóa thông điệp bằng **khóa công khai (public key)** của người nhận.
    - Chỉ người nhận có thể giải mã thông điệp bằng **khóa riêng (private key)** của họ.
    - **Mục đích**: Đảm bảo rằng chỉ người nhận được định sẵn mới có thể đọc nội dung email.

### Sự khác biệt cốt lõi

| Tiêu chí | S/MIME (Secure/Multipurpose Internet Mail Extensions) | DKIM (DomainKeys Identified Mail) |
| --- | --- | --- |
| **Mục đích chính** | **Bảo mật nội dung** (Mã hóa) và **Xác thực người gửi**. | **Xác thực nguồn gốc** và **Chống giả mạo tên miền**. |
| **Mã hóa nội dung** | **CÓ**. Giúp người nhận đọc được email (chỉ người nhận mới có khóa riêng). | **KHÔNG**. Email vẫn ở dạng văn bản thuần túy, ai cũng có thể đọc nếu chặn được. |
| **Phạm vi áp dụng** | **Giữa người gửi và người nhận cụ thể**. Yêu cầu cả hai bên đều cài đặt chứng chỉ S/MIME. | **Giữa máy chủ gửi và máy chủ nhận**. Không cần người nhận cài đặt gì thêm. |
| **Cách hoạt động** | Sử dụng cặp khóa riêng/công khai của **từng cá nhân**. | Sử dụng cặp khóa riêng/công khai của **tên miền (Domain)**. |
| **Ai kiểm tra?** | Phần mềm email của người nhận (Outlook, Apple Mail, v.v.). | Máy chủ thư của người nhận (Mail Server) trước khi đưa vào hộp thư. |
| **Vấn đề giải quyết** | "Chỉ mình tôi đọc được" và "Đây đúng là email của A". | "Email này thực sự gửi từ tên miền của A, không phải kẻ giả mạo". |