<img width="402" height="178" alt="image" src="https://github.com/user-attachments/assets/30b386d7-b614-4cba-921e-c2cb484fb93a" /><img width="921" height="384" alt="image" src="https://github.com/user-attachments/assets/3b3ea4dc-61cb-45ed-95ed-1b5def1e2641" /><img width="921" height="384" alt="image" src="https://github.com/user-attachments/assets/6d084aa1-4db1-4305-bc6c-bcf5f072de6a" /># Reversing.Kr Write-up

**Tác giả:** Trần Ngọc Thành - 22NS063
**Nickname:** TeeTan
**Tổng điểm:** 520 Points

---

# Mục lục

* [1. EasyCrack (100p)](#1-easycrack-100p)
* [2. EasyKeygen (100p)](#2-easykeygen-100p)
* [3. Easy ELF (100p)](#3-easy-elf-100p)
* [4. Easy Unpack (100p)](#4-easy-unpack-100p)
* [5. ImagePrc (120p)](#5-imageprc-120p)

---

# 1. EasyCrack (100p)

## Mục tiêu

Tìm mật khẩu hợp lệ để chương trình hiển thị thông báo thành công.

## Phân tích

Mở file bằng **IDA Pro** và tìm kiếm các chuỗi liên quan đến Password trong **Strings Window**.

### Ảnh minh họa

<img width="902" height="141" alt="image" src="https://github.com/user-attachments/assets/7fda952c-a271-49c3-8099-a953945fab3a" />

<img width="975" height="285" alt="image" src="https://github.com/user-attachments/assets/6d7dd02c-e1d1-4970-9064-10bbf3265ee5" />

Ta thấy các chuỗi:

* Incorrect Password
* Congratulation !!

Điều này cho thấy chương trình có cơ chế kiểm tra mật khẩu.

---

## Phân tích hàm kiểm tra

Đi tới địa chỉ hàm xử lý logic và nhấn **F5** để xem Pseudo-code.

### Ảnh minh họa

<img width="975" height="268" alt="image" src="https://github.com/user-attachments/assets/4cf3d611-c9ae-4c3a-ba2b-25a42cc0462c" />

Trong đoạn mã có các điều kiện:

```c
String[0] == 69
String[1] == 97
strcmp(&String[2], Str2)
strcmp(&String[4], aR3versing)
```

---

## Giải mã

### Ký tự thứ nhất

```text
69 (ASCII) = E
```

### Ký tự thứ hai

```text
97 (ASCII) = a
```

Tiếp tục kiểm tra giá trị của:

* Str2
* aR3versing

### Ảnh minh họa

<img width="667" height="45" alt="image" src="https://github.com/user-attachments/assets/e015ada5-c509-4dad-a8d1-941bebcfa825" />
<img width="752" height="31" alt="image" src="https://github.com/user-attachments/assets/a0d64218-a95d-4567-854e-430da9e68aa0" />


Ta thu được:

```text
Str2 = 5y
aR3versing = R3versing
```

Ghép lại:

```text
Ea + 5y + R3versing
```

---

## Password

```text
Ea5yR3versing
```

### Kết quả

<img width="853" height="271" alt="image" src="https://github.com/user-attachments/assets/ee447ad3-8fe4-4bc1-9e2a-a94af530cbc0" />

---

# 2. EasyKeygen (100p)

## Mục tiêu

Tìm Name tương ứng với Serial:

```text
5B134977135E7D13
```

---

## Phân tích chương trình

Mở file bằng IDA và tìm hàm `main`.

### Ảnh minh họa

<img width="925" height="972" alt="image" src="https://github.com/user-attachments/assets/07c20aa9-91d0-4e8a-b576-ad1235df3280" />


Chương trình:

1. Nhận Name
2. XOR từng ký tự với một khóa tuần hoàn
3. Chuyển sang dạng Hex
4. So sánh với Serial

Công thức:

```c
Serial_Byte[i] = Name[i] ^ Key[i % 3]
```

---

## Khôi phục khóa XOR

Quan sát biến:

```c
v6 = 16
v7 = " 0"
```

Trong bộ nhớ:

```text
v6      = 0x10
v7[0]   = 0x20
v7[1]   = 0x30
```

Khóa tuần hoàn:

```text
[0x10, 0x20, 0x30]
```

## Giải ngược Serial

Serial:

```text
5B134977135E7D13
```

Tách thành từng byte:

```text
5B 13 49 77 13 5E 7D 13
```

Thực hiện XOR ngược:

```text
5B ^ 10 = 4B = K
13 ^ 20 = 33 = 3
49 ^ 30 = 79 = y
77 ^ 10 = 67 = g
13 ^ 20 = 33 = 3
5E ^ 30 = 6E = n
7D ^ 10 = 6D = m
13 ^ 20 = 33 = 3
```

Kết quả:

```text
K3yg3nm3
```

---

## Đáp án

```text
Name   = K3yg3nm3
Serial = 5B134977135E7D13
```

---

# 3. Easy ELF (100p)

## Mục tiêu

Tìm chuỗi đầu vào hợp lệ.

---

## Phân tích

Mở file ELF bằng IDA và xem hàm `main`.

### Ảnh minh họa

<img width="588" height="291" alt="image" src="https://github.com/user-attachments/assets/e85b8596-06b8-4688-abb2-fa36c6d6fa00" />


Ta thấy 3 hàm đáng chú ý:

```c
sub_8048434
sub_8048451
sub_80484F7
```

---

## Chức năng từng hàm

### sub_8048434

Đọc dữ liệu đầu vào.

<img width="614" height="106" alt="image" src="https://github.com/user-attachments/assets/7656a092-c8dc-494d-9b5f-5ba892b54762" />


### sub_80484F7

In ra:

```text
Correct!
```

<img width="458" height="111" alt="image" src="https://github.com/user-attachments/assets/d5ac6d67-63ea-40e2-b38f-b20b45d359fc" />


### sub_8048451

Đây là hàm kiểm tra logic chính.

```markdown
<img width="411" height="445" alt="image" src="https://github.com/user-attachments/assets/07a45774-af19-4928-a537-a5311adc97fa" />

```

---

## Dịch ngược điều kiện

### Byte 1

```text
x ^ 0x34 = 0x78
=> x = 0x78 ^ 0x34 = 0x4C = L
```

### Byte 2

```text
49 => '1'
```

### Byte 3

```text
x ^ 0x32 = 0x7C
=> x = 0x7C ^ 0x32 = 0x4E = N
```

### Byte 4

```text
-35 = 0xDD
0xDD ^ 0x88 = 0x55 = U
```

### Byte 5

```text
88 => X
```

### Byte 6

```text
NULL byte (\0)
```

---

## Password

```text
L1NUX
```

---

# 4. Easy Unpack (100p)

## Mục tiêu

Xác định địa chỉ **OEP (Original Entry Point)**.

---

## Bước 1: Nhận diện packer

Dùng Detect It Easy.

### Ảnh minh họa

<img width="975" height="470" alt="image" src="https://github.com/user-attachments/assets/9a8cef99-5581-41a5-ad68-5ee8a68ffd5f" />


Kết quả:

```text
Compressed or packed data
```

Điều này cho thấy file đã bị pack.

---

## Bước 2: Tìm Tail Jump

Trong IDA:

1. Chuyển sang Graph View
2. Di chuyển xuống cuối flow graph
3. Tìm lệnh JMP cuối cùng

### Ảnh minh họa

<img width="921" height="384" alt="image" src="https://github.com/user-attachments/assets/24b7a2f2-30c1-4bcc-a164-3113ac64ca9d" />

---

## Debug

### Đặt Breakpoint

```text
0040A1FB
jmp loc_401150
```

Nhấn:

```text
F2
```

---

### Chạy chương trình

```text
F9
```

Chương trình thực hiện quá trình giải mã.

---

### Đi tới OEP

Khi dừng tại Tail Jump:

```text
F7 (Step Into)
```

### Ảnh minh họa

<img width="921" height="384" alt="image" src="https://github.com/user-attachments/assets/18022ba4-1c6f-4120-bfb0-5c1db78f1042" />


Sau khi nhảy vào:

```text
401150
```

Đây chính là OEP của chương trình.

---

# 5. ImagePrc (120p)

## Mục tiêu

Tìm mẫu ảnh (template) mà chương trình sử dụng để so sánh.

---

## Quan sát ban đầu

Chạy chương trình sẽ xuất hiện một vùng trắng cho phép người dùng vẽ.

### Ảnh minh họa

```markdown
<img width="416" height="327" alt="image" src="https://github.com/user-attachments/assets/b01e7cfb-f4da-4bfa-9599-74d7dfc645df" />

```

Sau khi nhấn Check, chương trình báo:

```text
Wrong
```

---

## Bước 1: Phân tích bằng IDA

Tìm chuỗi:

```text
Wrong
```

bằng:

```text
Shift + F12
```

### Ảnh minh họa

```markdown
<img width="940" height="188" alt="image" src="https://github.com/user-attachments/assets/772072b7-0ed0-4c24-97cd-0313b1100d6d" />
```

Theo dấu tới hàm:

```text
sub_401130
```

---

## Phân tích logic

### Ảnh minh họa

<img width="945" height="384" alt="image" src="https://github.com/user-attachments/assets/fef62a07-d57a-41c1-beda-f244c6b977ba" />


Chương trình:

* Dùng GetDIBits lấy dữ liệu ảnh người dùng vẽ.
* Tải Resource ID 101.
* So sánh dữ liệu từng byte.

Quan trọng:

```c
FindResourceA(..., 0x65, ...)
```

```text
0x65 = 101
```

---

## Bước 2: Trích xuất Resource

Sử dụng:

```text
Resource Hacker
```

Mở:

```text
ImagePrc.exe
```

Tìm:

```text
Resource ID = 101
```

### Ảnh minh họa

```markdown
<img width="758" height="775" alt="image" src="https://github.com/user-attachments/assets/979bfb65-ad04-427a-8c40-8598a6988909" />

```

Lưu thành:

```text
flag_imagePrc.bin
```

Kiểm tra kích thước :


```text
90,000 bytes
```

---

## Bước 3: Khôi phục ảnh

Tạo một file BMP:

```text
200 x 150
24-bit Bitmap
```

Mở bằng:

```text
HxD
```

### Thao tác

Copy toàn bộ dữ liệu từ:

```text
flag_imagePrc.bin
```

Dán đè vào:

```text
Offset 0x36
```

của file BMP.

### Ảnh minh họa

<img width="670" height="169" alt="image" src="https://github.com/user-attachments/assets/578c05e2-0c85-43bc-8e0f-1f76915ab744" />


---

## Kết quả

### Ảnh minh họa

<img width="670" height="169" alt="image" src="https://github.com/user-attachments/assets/1b3cb50f-be10-42d0-a24d-841f77b262b4" />


Kết quả thu được:

```text
GOT
```

---

# Tổng kết

| Challenge   | Điểm    |
| ----------- | ------- |
| EasyCrack   | 100     |
| EasyKeygen  | 100     |
| Easy ELF    | 100     |
| Easy Unpack | 100     |
| ImagePrc    | 120     |
| **Tổng**    | **520** |

Qua các bài tập trên, tôi đã thực hành các kỹ thuật:

* Static Analysis với IDA Pro
* String Analysis
* XOR Reversing
* ELF Reverse Engineering
* PE Unpacking
* Resource Extraction
* Bitmap Reconstruction
* Basic Malware & Crackme Analysis

Đây là những kỹ năng nền tảng quan trọng trong lĩnh vực Reverse Engineering và Malware Analysis.
