---  
title: "Picoctf 2026 - General skill challenges"  
date: 2026-04-14 +0700  
categories: [CTF, Misc]  
tags: [General skill, linux, picoctf, picoctf2026, ctf]
image:
  path: https://plasticuproject.com/images/b00tl3gRSA3/picoctf_logo.png
  alt: "PICOCTF 2026"
---
## Undo
![image](https://hackmd.io/_uploads/HkwxB8s2-l.png)
Bài này yêu cầu sử dụng các lệnh trong linux để recover flag đã encoded qua nhiều bước.
Đầu tiên ta cần decode base64 - sử dụng   `base64 -d`
Tiếp theo, ta cần đảo ngược chuỗi - sử dụng lệnh `rev`
Cuối cùng, ta cần thay đổi một số kí tự này thành kí tự khác và decode [ROT13](https://vi.wikipedia.org/wiki/ROT13), sử dụng lệnh `tr`.
Đây là toàn bộ các bước mình làm để lấy flag
```(bash)
nc foggy-cliff.picoctf.net 61262
===Welcome to the Text Transformations Challenge!===

Your goal: step by step, recover the original flag.
At each step, you'll see the transformed flag and a hint.
Enter the correct Linux command to reverse the last transformation.

--- Step 1 ---
Current flag: KTY4ODhyMjFuLWZhMDFnQHplMHNmYTRlRy1nazNnLXRhMWZlcmlyRShTR1BicHZj
Hint: Base64 encoded the string.
Enter the Linux command to reverse it: base64 -d
Correct!

--- Step 2 ---
Current flag: )6888r21n-fa01g@ze0sfa4eG-gk3g-ta1ferirE(SGPbpvc
Hint: Reversed the text.
Enter the Linux command to reverse it: rev
Correct!

--- Step 3 ---
Current flag: cvpbPGS(Eriref1at-g3kg-Ge4afs0ez@g10af-n12r8886)
Hint: Replaced underscores with dashes.
Enter the Linux command to reverse it: tr '-' '_'
Correct!

--- Step 4 ---
Current flag: cvpbPGS(Eriref1at_g3kg_Ge4afs0ez@g10af_n12r8886)
Hint: Replaced curly braces with parentheses.
Enter the Linux command to reverse it: tr [()] [{}]
Correct!

--- Step 5 ---
Current flag: cvpbPGS{Eriref1at_g3kg_Ge4afs0ez@g10af_n12r8886}
Hint: Applied ROT13 to letters.
Enter the Linux command to reverse it: tr [a-mn-zA-MN-Z] [n-za-mN-ZA-M]
Correct!

Congratulations! You've recovered the original flag:
>>> picoCTF{Revers1ng_t3xt_Tr4nsf0rm@t10ns_a12e8886}
```

## Piece by Piece
![image](https://hackmd.io/_uploads/SJobdIsnWg.png)
Đầu tiên, ta ssh vào challenges và thấy có các file sau
```(bash)
ctf-player@pico-chall$ ls
instructions.txt  part_aa  part_ab  part_ac  part_ad  part_ae
```
Đọc file instructions.txt ta biết được là flag đã bị chia thành nhiều phần và nén lại thành file zip với mật khẩu là `supersecret`.
```
ctf-player@pico-chall$ cat instructions.txt
Hint:

- The flag is split into multiple parts as a zipped file.
- Use Linux commands to combine the parts into one file.
- The zip file is password protected. Use this "supersecret" password to extract the zip file.
- After unzipping, check the extracted text file for the flag.
```
Ta sẽ đưa tất cả các part đã bị zip thành một file zip và giải nén chúng
```(bash)
ctf-player@pico-chall$ cat part* > flag
ctf-player@pico-chall$ file flag
flag: Zip archive data, at least v1.0 to extract
ctf-player@pico-chall$ mv flag flag.zip
ctf-player@pico-chall$ unzip flag.zip
Archive:  flag.zip
[flag.zip] flag.txt password:
 extracting: flag.txt
ctf-player@pico-chall$ cat flag.txt
picoCTF{z1p_and_spl1t_f1l3s_4r3_fun_28d309dc}
```

## SUDO MAKE ME A SANDWICH
![image](https://hackmd.io/_uploads/HyxlnUi3-e.png)
Với bài này sau khi ssh vào thì ta thấy có một file flag.txt. Mình thử mở lên nhưng bị permission denied, mình thử dùng `ls -l` để check quyền thì thấy chỉ có user root hoặc trong group root mới có thể đọc được.
```(bash)
ctf-player@challenge:~$ ls -l
total 4
-r--r----- 1 root root 31 Mar  9 21:32 flag.txt
```
Mình thử dùng `sudo -l` để xem có thể dùng sudo để chạy quyền nào thì thấy có thể chạy một binary emacs.
```(bash)
sudo -l
Matching Defaults entries for ctf-player on challenge:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User ctf-player may run the following commands on challenge:
    (ALL) NOPASSWD: /bin/emacs
```
sau khi search thì mình biết được **emacs** (Editor MACroS) là một trình soạn thảo văn bản tự do, đa chức năng.
Vậy ta sẽ lợi dụng công cụ này để đọc file flag.txt với quyền root bằng lệnh  `sudo emacs flag.txt`
![image](https://hackmd.io/_uploads/rk6_RIjhWx.png)
** Để thoát emacs thì có thể nhấn Crtl-X Crtl-C nhé=))

## Password Profiler
![image](https://hackmd.io/_uploads/ryWQyvo3bg.png)

Baì này cung cấp cho ta thông tin về user, một mật khẩu đã được mã hoá và một tool python có chúc năng password ở hash từ một wordlist có sẵn. Ở trong source code của file python ta thấy file wordlist passwords.txt được tạo bằng `cupp`. `cupp` là một công cụ giúp tạo wordlist từ các thông tin cho sẵn. Mình dùng `cupp -i` để mở chế độ interactive để cupp sẽ prompt từng thông tin, mình nhập thông tin từ file userinfo.txt, phần thông tin nào không có thì chỉ nhấn enter để bỏ qua và sau đó chọn N cho các lựa chọn khác. sau đó dùng check_password.py để kiểm tra password từ file hash và nhận flag
```
python3 check_password.py
Password found: picoCTF{Aj_15901990}
```

## MultiCode
![image](https://hackmd.io/_uploads/Bk0W7vo2We.png)
Ta có một file chứa flag đã bị encoded. Mở vào [cyberberchef.io](https://cyberchef.io) và dùng chức năng magic ta thấy nó giải mã ra được được một vài phần và thấy được cấu trúc của flag.
![image](https://hackmd.io/_uploads/HJ-1Nvj3Wx.png)
Nhưng có lẽ flag ở đây dã bị encoded url và shift vị trí các kí tự (Ceaser ciper). Sau khi decode url và rot13 ta được flag hoàn chỉnh
![image](https://hackmd.io/_uploads/ryiT4wi3Zl.png)



## ABSOLUTE NANO
![image](https://hackmd.io/_uploads/S1servon-l.png)
Bài này sau khi ssh vào thì ta lại thấy một file flag.txt nhưng chỉ root mới có quyền đọc. Thử `sudo -l` thì ta thấy có thể dùng `nano` để chỉnh sửa file `/etc/suoders`. Vậy ta sẽ dùng nano để chỉnh file /etc/suoders cho phép user hiện tại có thể sử dụng tất cả mọi lệnh với quyền sudo.
```(bash)
ctf-player ALL=(ALL) NOPASSWD:ALL
```
Sau đó dùng sudo đẻ đọc file flag.txt
```(bash)
ctf-player@challenge:~$ sudo cat flag.txt
picoCTF{n4n0_411_7h3_w4y_7a258d4b}
```

## Failure Failure
![image](https://hackmd.io/_uploads/ryOg_Djhbe.png)

Trong bài này có một load balencer đứng trước và 2 server đứng sau tạm gọi là A và B. Bình thường, server A không chứa sẽ được serve lên trước còn khi quá tải load balencer dẽ điều hướng qua server B chứa flag. Vậy ý tưởng ở đây là ta sẽ làm quá tải lưu lượng truy cập vào server A để được chuyển sang server B và lấy dược flag. Mình sẽ viết một script python liên tục truy cập vào làm quá tải và sau đó truy cập vào website để lấy flag.
```(python)
import requests
from concurrent.futures import ThreadPoolExecutor

url = "http://mysterious-sea.picoctf.net:62524"
def force_failover(_):
    try:
        resp = requests.get(url, timeout=1)
    except:
        pass
        
with ThreadPoolExecutor(max_workers=200) as executor:
    executor.map(force_failover, range(5000))
```
Và đó một thời gian khi server đã quá tải, truy cập vào trang web và lấy flag.
![image](https://hackmd.io/_uploads/ryAW5DjnZx.png)
