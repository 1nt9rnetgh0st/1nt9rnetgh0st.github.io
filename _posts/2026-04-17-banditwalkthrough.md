---  
title: "OverTheWire: Bandit - Full Walkthrough"  
date: 2026-04-17 +0700  
categories: [CTF-Writeups, OverTheWire, Bandit]  
tags: [Linux, OverTheWire, Network, Command, Write-up]
image:
  path: https://hackmd.io/_uploads/BkJ0fSy6be.png
---

## Level 0 - 1

SSH đến bandit.labs.overthewire.org port 2220 với username bandit0 và mật khẩu bandit0

	ssh bandit0@bandit.labs.overthewire.org -p 2220

![Pasted image 20251228203246](https://hackmd.io/_uploads/SJkfbAjr-x.png)


    Password: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If

Đọc file Readme lấy được password cho level 1. Dùng exit hoặc logout để thoát và ssh vào với username bandit1

## Level 1 - 2

Level này yêu cầu đọc file có tên `-` , mà khi dùng lệnh trực tiếp, ví dụ `cat -` thì shell sẽ lấy dữ liệu từ sdtin và không đọc file. Bài này mình dùng absolute path dạng`./-` hay `/home/bandit1/-` sẽ đọc được password

![Pasted image 20251228211014](https://hackmd.io/_uploads/r16SbAjB-e.png)

	Password: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx

## Level 2 - 3

Level này cần đọc file tên  `--spaces in this filename--` . Có thể dùng absolute path như level trước hoặc dùng dấu `--` ở trước để shell hiểu là hết phần arguments.  Phần này tên file có khoảng trắng nên sẽ dùng dấu `' '` ngoài tên file hoặc dùng dấu `\`.

![Pasted image 20251228213607](https://hackmd.io/_uploads/rJGPZCiSWx.png)

	Password: MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
    

## Level 3 - 4

 Phần này file ẩn ở folder inhere, cd sang folder `inhere`, sau đó dùng ls -a để list tất cả các file bao gồm cả file ẩn `...Hiding-From-You`



    Password: 2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ

## Level 4 - 5

Level này file cũng nằm ở folder `inhere`, nhưng ở đây có nhiều file và file chứa password là file có chứa kí tự có thể đọc được(only human-readable), mình có thể đọc tất cả file bằng dấu `* (wildcard)`, và dùng lệnh `strings` để chỉ lấy kí tự có thể đọc được.

	Password: 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw

![[Pasted image 20251228215308.png]]

## Level 5 - 6

Phần level này cần tìm một file trong thư mục `inhere` có đủ 3 tính chất:
	- có thể đọc được
	- size 1033 bytes
	- không thực thi được
Mình dùng lệnh find với các args tương tự:
	-readable (có thể đọc), 
	-size 1033c (file nặng 1033 bytes), 
	!-executable (dùng ! trước executable để lọc file không thực thi được )

`find . -readable -size 1033c ! -executable`

	Password: HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
	
![Pasted image 20251228222851](https://hackmd.io/_uploads/ryICbCsS-l.png)
![Pasted image 20251228222942](https://hackmd.io/_uploads/SkKA-0irbg.png)

## Level 6 - 7

Level này yêu cầu tìm một file trong toàn bộ server mà có các tính chất:
	- owned by user bandit7
	- owned by group bandit6
	- 33 bytes in size
mình sử dụng các args tương tự:
	 -user bandit7
	 -group bandit 6
	 -size 33c

`find / -user bandit7 -group bandit6 -size 33c`
Muốn cho kết quả gọn và sạch hơn (Vì find sẽ đi qua lần lượt từng folder để tìm nên ta sẽ thấy nhiều thông báo lỗi do những folder đó ta không có quyền truy cập), ta có thể redirect các stderr đến `/dev/null`
`find / -user bandit7 -group bandit6 -size 33c 2> /dev/null`

	morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj

![Pasted image 20251228224423](https://hackmd.io/_uploads/BkOkz0iSbg.png)


## Level 7 - 8

Level này pass ở cạnh từ millionth trong file data.txt. Mình đơn giản dùng lệnh `grep` để tìm từ đó.

`grep millionth data.txt`

	 Password: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
 
 ![Pasted image 20251228224954](https://hackmd.io/_uploads/rJZefRor-x.png)

## Level 8 - 9

Level này pass cũng nằm ở file data.txt và là dòng chỉ xuất hiện 1 lần.  Có lệnh `uniq` sẽ lọc các dòng giống nhau và chỉ hiển thị mỗi dòng một lần, dùng thêm arg `-u` sẽ chỉ hiển thị mỗi dòng giống nhau đúng 1 lần( trước khi dùng uniq thì lưu ý phải dùng lệnh sort bởi vì uniq chỉ lọc được các dòng đã sắp xếp). Vậy mình dùng theo thứ tự cat - sort - uniq

`cat data.txt | sort | uniq -u`

	Password: 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

![Pasted image 20251228230326](https://hackmd.io/_uploads/H1IWMRoHZl.png)


## Level  9 - 10

Pass ở level này nằm ở trong file data.txt là một trong những đoạn có thể đọc và phần trước nó là một vài dấu `=` . Mình dùng `strings` để lấy chuỗi có thể đọc và `grep` để lấy phần có dấu =

	Password: FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

![Pasted image 20251228231827](https://hackmd.io/_uploads/ByWfG0sBbl.png)


 ## Level 10 - 11
Phần level này chứa pass đã mã hoá base64 trong file data.txt. Để giải mã dùng lệnh `base64` với args `-d` để decode
```
bandit10@bandit:~$ base64 -d data.txt
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

 ## Level 11 - 12
Pass ở level này đã được dịch chuyển 13 kí tự, đó cách mã hoá [ROT13](https://vi.wikipedia.org/wiki/ROT13). Để đưa lại về dạng ban đầu ta có thể dùng lệnh `tr` 
```
bandit11@bandit:~$ cat data.txt | tr [a-z][A-Z] [n-za-m][N-ZA-M]
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```

## Level 12 - 13
Ở level này thì file đã bị chuyển sang hex và nén nhiều lần. Phần này mình chuyển file về nhị phân bằng `xxd`, sau đó dùng lệnh `file` để biết file đang được nén ở dạng nào và đổi tên file với extension phù hợp, rồi dùng các tool giải nén tương ứng như `gunzip` ,`bzip2`, `tar` 
```
bandit12@bandit:~$ mktemp -d
/tmp/tmp.KxPQpaFg4b
bandit12@bandit:~$ xxd -r data.txt > /tmp/tmp.KxPQpaFg4b/data
bandit12@bandit:~$ cd  /tmp/tmp.KxPQpaFg4b/
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data
data: gzip compressed data, was "data2.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 572
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data
data: gzip compressed data, was "data2.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 572
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ mv data data.gz
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ gunzip data.gz
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data
data: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ mv data data.bz2
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ bzip2 -d data.bz2
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data
data: gzip compressed data, was "data4.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 20480
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ mv data data.gz
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ gunzip data.gz
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data
data: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ mv data data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ tar -xf data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ ls
data5.bin  data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ mv data5.bin data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ tar -xf data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ ls
data6.bin  data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ mv data6.bin data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data.tar
data.tar: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ mv data.tar data.bzip2
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ bzip2 -d data.bz2
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data
data: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ mv data data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ tar -xf data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ ls
data8.bin  data.tar
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 49
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ mv data8.bin data.gz
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ gunzip data.gz
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ file data
data: ASCII text
bandit12@bandit:/tmp/tmp.KxPQpaFg4b$ cat data
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

Sau khi làm xong thủ công mình có thử viết bash script để giải nén cho nhanh
```
#!/bin/bash
while true; do
        file=$(ls data*)
        ext=$(file $file)
        if echo $ext | grep -q "gzip" ; then
                mv $file "$file.gz"
                gunzip "$file.gz"
        elif echo $ext | grep -q "bzip2"; then
                mv $file "$file.bz2"
                bzip2 -d "$file.bz2"
        elif echo $ext | grep -q "tar"; then
                mv $file "$file.tar"
                tar -xf "$file.tar"
                rm "$file.tar"
        else
                echo "PROCESS COMPLETED:"
                cat $file
                break
        fi
done
```

## Level 13 - 14
Level này không cho pass nhưng cho ssh key để đăng nhập vào level sau, mình dùng `scp` để download file key về (phần này cũng có thể mở, copy nội dung key và tạo file mới luôn. Lưu ý là ssh key phải là quyền 700, dùng `chmod` để đổi quyền. Sau đó `ssh` vào bandit14 với args -i cho sshkey
```
ahahaa@ubunto)-[~]
└─$ sudo scp -P 2220 \ bandit13@bandit.labs.overthewire.org:/home/bandit13/sshkey.private .

ahahaa@ubunto)-[~]
└─$ sudo chmod 700 ssh.private

ahahaa@ubunto)-[~]
└─$ sudo ssh bandit14@bandit.labs.overthewire.org -p 2220 -i sshkey.private
```

## Level 14 - 15
Trong level này cần lấy pass của chính level này rồi gửi lên port 30000 ở localhost. Mình lấy pass ở chỗ mà bandit vẫn thường lưu cho từng level là /etc/bandit_pass/bandit14. Sau đó dùng `nc` để kết nối đến 127.0.0.1(localhost) ở port 30000 và nhập mật khẩu vừa tìm được
```
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS

bandit14@bandit:~$ nc 127.0.0.1 30000
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

## Level 15 - 16
Ở level này ta dùng `openssl` `s_client` để tạo kết nối ssl đến 127.0.0.1 ở port 30001 và nhập pass của level này
```
bandit15@bandit:~$ openssl s_client -connect 127.0.0.1:30001
CONNECTED(00000003)
...
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

## Level 16 - 17
Level này phải tìm port trong localhost có service lấy mật khẩu qua ssl trong khoảng 31000 - 32000. mình thử dùng `nmap` để scan ports. 
```
nmap -p 31000-32000 -sV 127.0.0.1 (hoặc) nmap -p 3100-32000 -sV --script ssl-enum-ciphers 127.0.0.1
```

```
bandit16@bandit:~$ nmap -sV -p 31000-32000 127.0.0.1
...
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo

1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.94SVN%T=SSL%I=7%D=12/29%Time=69521D50%P=x86_64-pc-lin
SF:ux-gnu%r(GenericLines,32,"Wrong!\x20Please\x20enter\x20the\x20correct...
```
Sau khi đã scan xong thì mình thấy có 5 port mở trong đó có 2 port chạy service ssl, nhưng ở dưới có fingerprint dạng "Wrong password..." ở port 31790 nên mình có thể chắc đây là port cần tìm. 
Ở đây khi mình dùng lệnh `openssl s_client 127.0.0.1:31790` như lần trước sẽ liên tục bị hiện thông báo debug `KEYUPDATE` ra stdout nên không thể thấy pass trả về của service. Để tránh bị thông báo debug, theo mình research thì dùng arg -quiet để bịt miệng thông báo đó lại và nhập pass như level trước.

```
bandit16@bandit:~$ openssl s_client -quiet -connect 127.0.0.1:31790
...
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
...
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```
 Mình exit sau đó lưu lại vào một file rồi đổi quyền thành 700 cho file này.
(Ngoài ra còn có vài tool cũng rất hay)

`ncat --ssl`
```
bandit16@bandit:~$ ncat --ssl 127.0.0.1 31790
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
...
```

`socat - OPENSSL:host:port,verify 0`
```
bandit16@bandit:~$ socat - OPENSSL:127.0.0.1:31790,verify=0
2025/12/29 16:32:36 socat[21] W refusing to set empty SNI host name
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
...
```

## Level 17 - 18
Level này có 2 file là password.new và password.old. Mật khẩu nằm ở file password.new, ở dòng khác với file password.old. MÌnh dùng lệnh `diff` để so sánh file và lấy dòng khác nhau

```
bandit17@bandit:~$ ls
passwords.new  passwords.old
bandit17@bandit:~$ diff passwords.new passwords.old
42c42
< x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO (pass cho bandit 18)
---
> BMIOFKM7CRSLI97voLp3TD80NAq5exxk
```

## Level 18 - 19
`.bashrc` là một file của bash shell, nó sẽ tự dộng chạy khi mở phiên interactive shell(vd. mở terminal mới, khi login ssh,..). Để đăng nhập mình dùng `bash --norc` để server chạy bash shell mà không đọc file `.bashrc`
```
┌──(ahahaa@ubunto)-[~]
└─$ sudo ssh bandit18@bandit.labs.overthewire.org -p 2220 -t "bash --norc"
$ cat readme
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

Hoặc mình có một số cách khác:
```
┌──(ahahaa@ubunto)-[~]
└─$ sudo ssh bandit18@bandit.labs.overthewire.org -p 2220 -t "/bin/sh"

┌──(ahahaa@ubunto)-[~]
└─$ sudo ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"

┌──(ahahaa@ubunto)-[~]
└─$ scp -P 2220 bandit18@bandit.labs.overthewire.org:/readme .
```

## Level 19 - 20
Trong bài có nói có chương trình để chạy lệnh với quyền user khác (tương tự sudo) - ở đây là chạy lệnh với quyền bandit20. Mình chạy chương trình này để lấy pass của bandit20
```
bandit19@bandit:~$ ls
bandit20-do
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do whoami
bandit19@bandit:~$ ./bandit20-do whoami
bandit20
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

## Level 20 - 21
Level này có 1 chương trình của bandit21 đã setuid khi chạy để kết nối đến port ở arg và nhận dữ liệu, nếu đúng là pass của bandit20 thì sẽ trả về pass của bandit21.  Mình dùng một chương trình để nghe như `netcat` ở port bất kì (ngoài khoảng 1 - 1023 vì trong khoảng này chỉ cho root) và gửi pass của level này khi có chương trình kết nối đến(như suconnect)
```
bandit20@bandit:~$ ls
suconnect
bandit20@bandit:~$ (echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO") | nc -l -p 9999 &
[1] 51
bandit20@bandit:~$ ./suconnect 9999
Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
Password matches, sending next password
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
```

## Level 21 - 22
Ở level này có chương trình chứa pass được tự dộng chạy bằng `cron`. Mình mở `/etc/cron.d` để xem các cron thì thấy có file tên cronjob_bandit22 file này sẽ chạy mỗi khi reboot hoặc cách 1 phút. Khi đọc file script cronjob_bandit22.sh mà nó chạy mình thấy nó sẽ đọc mật khẩu và ghi vào một file tạm thời trong /tmp , vậy chỉ cần đọc file đó là lấy được pass.
```
bandit21@bandit:/etc/cron.d$ ls
behemoth4_cleanup  cronjob_bandit22  cronjob_bandit24  leviathan5_cleanup    
otw-tmp-dir        clean_tmp         cronjob_bandit23  e2scrub_all       manpage3_resetpw_job  sysstat
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

## Level 22 - 23
Ở level này cũng tương tự. Mình thấy có script cronjob_bandit23.sh copy pass vào một file trong /tmp có tên dạng encrypt md5 của cụm từ "I am user $myname" và $myname ở đây là bandit23 
```
bandit22@bandit:~$ cd /etc/cron.d
bandit22@bandit:/etc/cron.d$ ls
behemoth4_cleanup  cronjob_bandit22  cronjob_bandit24  leviathan5_cleanup    
otw-tmp-dir        clean_tmp         cronjob_bandit23  e2scrub_all       manpage3_resetpw_job  sysstat
bandit22@bandit:/etc/cron.d$ cat cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
bandit22@bandit:/etc/cron.d$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
bandit22@bandit:/etc/cron.d$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

## Level 23 - 24
Cái này mình làm hơi mất thời gian tại viết nhầm script hay gì đấy nên chạy mãi không được:)). Đọc qua chương trình bandit24.sh thì đơn giản nếu nó thấy file owned bởi bandit23 thì nó sẽ thực thi trong tối đa 60 giây nó sẽ tự tắt, sau đó sẽ xoá file ấy. Ở đây mình tạo một script để lấy pass và lưu pass ở một file mới trong thư mục đó. Sau đó đợi cron chạy lệnh và lấy password

```
(run.sh)
cat /etc/bandit_pass/bandit24 > /var/spool/bandit24/foo/password
```

```
bandit23@bandit:~$ cd /var/spool/bandit24/foo
bandit23@bandit:/var/spool/bandit24/foo$ nano run.sh
bandit23@bandit:/var/spool/bandit24/foo$ chmod +x run.sh
bandit23@bandit:/var/spool/bandit24/foo$ cat run.sh (ở đây cat hiển thị không có
cat: run.sh: No such file or directory         file chứng tỏ nó đã được thực hiện)
bandit23@bandit:/var/spool/bandit24/foo$ cat password
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```

## Level 24 - 25
Level này cần gửi password của level 24 kèm mã pin 4 số cần bruteforce, để nhanh thì sẽ không đóng kết nối mà mở kết nối và gửi pass liên tục nhiều lần. Sau khi research, mình thấy có cách là dùng socket có sẵn của linux để mở một kết nối TCP và liên tục nhận/ gửi mã pin. Sau đó viết script để liên tục gửi và nhận khi thấy tìm được pass đúng
```
  (run.sh)
#!/bin/bash
list=$(seq -w 0 9999)
exec 3<>/dev/tcp/127.0.0.1/30002
read -u 3 res
echo $res
for d in $list; do
        echo "[+]TRYING:gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $d"
        echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $d" >&3
        read -u 3 res
        echo $res
        if echo $res | grep -q -v "Wrong"; then
                echo "FOUND PASSWORD:"
                while read -t 1 -u 3 res; do
                        echo $res
                done
                break
        fi
done
```

```
Correct!
FOUND: Correct!
password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
```

## Level 25 - 27

Ở level này challenge có nói là shell của bandit26 không phải /bin/bash, ta đọc file `/etc/passwd` để xem shell của bandit26 là gì
```
bandit25@bandit:~$ cat /etc/passwd | grep bandit26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```
Phần này ta thấy shell của bandit26 là `/usr/bin/showtext`. Đọc file này thì ta thấy nó chỉ dùng `more` để đọc file text.txt. Ta đã biết được cách shell hoạt động, giờ ta sẽ login vào bandit26
```
bandit25@bandit:~$ cat /usr/bin/showtext
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```
Trong thư mục home của bandit25 ta sẽ thấy có file key của bandit26, như có level trước thì có thể `ssh` trực tiếp vào bandit26, nhưng ở đây đã bị chặn `ssh` trực tiếp cùng host nên ta có thể tải file key về hoặc copy nội dung file key. Lưu ý khi lưu file xong ta phải đổi file thành quyền 700
```
scp -P 2220 bandit25@bandit.labs.overthewire.org:/home/bandit25/bandit26.sshkey .
```
Sau đó, ta `ssh` vào bandit26 với file key vừa tải về. Nếu như mở terminal với kích thước toàn màn hình ta sẽ thấy nó thực hiện đúng như trong file shell là đọc file text.txt và thoát. Vì more sẽ chia thành từng phần để lướt đọc dần nếu như màn hình quá nhỏ để hiển thị, vậy ta sẽ thu terminal thật nhỏ để more sẽ không exit ngay, sau đó ta dùng lệnh dùng `v` để mở vim. Trong vim khi chạy shell nó sẽ lấy shell trong biến shell của vim, ở đây là shell mặc định của bandit26. Ta sẽ dùng `set shell` để set shell này sang `/bin/bash` hoặc `/bin/sh` 
```
(Trong vim)
:set shell=/bin/bash

:shell
```
Sau đó ta có thể dùng shell như bình thường, list home directory của user bandit26 ta sẽ thấy có một file `bandit27-do` để chạy lệnh với quyền của bandit27. Chạy file này ta sẽ đọc được pass của bandit27

```
bandit26@bandit:~$./bandit27-do cat /etc/bandit_pass/bandit27
```

    Password: upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB

## Level 27 - 28
Dùng git clone với với port 2220 và lấy được repo. Pass nằm ở trong file Readme.
```

┌──(ahahaa@ubunto)-[~/repo]
└─$ git clone  ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
Cloning into 'repo'...
...
┌──(ahahaa@ubunto)-[~]
└─$ cd repo

┌──(ahahaa@ubunto)-[~/repo]
└─$ cat README
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```

## Level 28 - 29
Dùng git clone về như level trước. Mình xem log thì thấy có một commit 'add missing data'. show commit đấy để lấy pass
```

┌──(ahahaa@ubunto)-[~/repo]
└─$ git log
...
commit d0cf2ab7dd7ebc6075b59102a980155268f0fe8f
Author: Morla Porla <morla@overthewire.org>
Date:   Tue Oct 14 09:26:19 2025 +0000

    add missing data
...
┌──(ahahaa@ubunto)-[~/repo]
└─$ git show d0cf2ab7dd7ebc6075b59102a980155268f0fe8f
...
+- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7

```

## Level 29 - 30
Sau khi tải repo về, dùng git rev-list --all để thấy tất cả các commits, check các commits ấy để lấy pass.
```
┌──(ahahaa@ubunto)-[~/repo/.git]
└─$ git grep password $(git rev-list --all)
...
eb435001e0eb0219ee071219d1f16f1cd3941a3b:README.md:- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
...
```

## Level 30 - 31
Level này nó nằm trong một refs khác. Đọc file packed-ref sẽ thấy được. show commit đó lên.
```
┌──(ahahaa@ubunto)-[~/repo/.git]
└─$ cat packed-refs
# pack-refs with: peeled fully-peeled sorted
62152a9969c62cb647406aa88c1b5376dcf58968 refs/remotes/origin/master
84368f3a7ee06ac993ed579e34b8bd144afad351 refs/tags/secret

┌──(ahahaa@ubunto)-[~/repo/.git]
└─$ git show 84368f3a7ee06ac993ed579e34b8bd144afad351
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

 ## Level 31 - 32
Level này cần push file key.txt với đúng nội dung lên server, nhưng ở đây có file .gitignore không cho phép file .txt lên. Để add lên mình dùng arg `-f` để force git

```
cat README.md
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master


┌──(ahahaa@ubunto)-[~/repo]
└─$ echo "May I come in?" > key.txt

┌──(ahahaa@ubunto)-[~/repo]
└─$ git add -f key.txt

┌──(ahahaa@ubunto)-[~/repo]
└─$ git commit -m "add key file to server"
 ...
 1 file changed, 1 insertion(+)
 create mode 100644 key.txt

┌──(ahahaa@ubunto)-[~/repo]
└─$ git push
...
remote: ### Attempting to validate files... ####
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
...
```

## Level 32 - 33
Trong Level này thì tất cả các lệnh khi gõ vào shell sẽ bị chuyển sang uppercase, mình sẽ dùng biến $0 - biến này sẽ trả về arg[0] của process đang chạy hay dễ hiểu hơn là trả về tên lệnh dùng để thực thi,ví dụ chạy script `bash ./run.sh` thì sẽ trả về 'bash', tương tự khi gõ trong terminal sẽ trả về tên shell. Khi dùng $0 ở level này sẽ trả về `sh` - shell đang chạy proccess này. Sau đó dùng như bình thường để đọc pass
```
WELCOME TO THE UPPERCASE SHELL
>> $0
$ ls
uppershell
$ cat /etc/bandit_pass/bandit33
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
```

## Level 33 - 34
Kết thúc hết các level bandits cho đến thời điểm hiện tại, Chưa có level 34.
