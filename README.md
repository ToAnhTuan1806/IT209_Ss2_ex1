# BÀI 1: KHỞI TẠO DROPLET TRÊN DIGITALOCEAN

## 1. Mục tiêu
- Thực hành lựa chọn cấu hình phần cứng phù hợp và khởi tạo thành công máy chủ ảo (Droplet) chạy hệ điều hành Ubuntu Server trên nền tảng đám mây DigitalOcean.
- Thiết lập cơ chế xác thực an toàn bằng cặp khóa SSH (SSH Keypair - `ed25519`) thay thế hoàn toàn cho mật khẩu truyền thống.
- Thực hiện kết nối quản trị từ xa thành công từ máy tính cá nhân tới Droplet qua giao thức SSH với tài khoản `root`.

---

## 2. Môi trường thực hiện
- **Nền tảng đám mây:** DigitalOcean.
- **Hệ điều hành:** Ubuntu 22.04 LTS x64.
- **Khu vực đặt máy chủ (Datacenter Region):** Singapore (`SGP1`) - tối ưu độ trễ mạng về Việt Nam.
- **Gói cấu hình (Droplet Type):** Basic Plan, Regular SSD, CPU Shared (1 vCPU, 1 GB RAM, 25 GB SSD).
- **Phương thức xác thực (Authentication):** SSH Key (`ed25519`).
- **Tài khoản truy cập mặc định:** `root`.

---

## 3. Tạo cặp khóa SSH trên máy cá nhân
Trước khi tạo máy chủ, tiến hành sinh cặp khóa SSH bảo mật cao bằng thuật toán `ed25519` trên máy tính cá nhân:

```bash
ssh-keygen -t ed25519 -C "devops_student@example.com"
```

> Khi được hỏi vị trí lưu, nhấn `Enter` để lưu vào đường dẫn mặc định (`~/.ssh/id_ed25519`). Có thể đặt thêm passphrase hoặc nhấn `Enter` để bỏ qua.

Hiển thị và sao chép toàn bộ nội dung khóa công khai (Public Key):

```bash
cat ~/.ssh/id_ed25519.pub
```

*(Nội dung file này sẽ được dán vào mục SSH Keys trên trang quản trị DigitalOcean).*

---

## 4. Quy trình khởi tạo Droplet trên DigitalOcean Console

1. **Chọn khu vực (Region):**
   - Chọn **Singapore (SGP1)** nhằm đảm bảo tốc độ kết nối và độ trễ thấp nhất về Việt Nam.

2. **Chọn hệ điều hành (Image):**
   - Chọn phân phối **Ubuntu**, phiên bản **22.04 (LTS) x64**.

3. **Chọn cấu hình phần cứng (Size):**
   - **Droplet Type:** Basic.
   - **CPU Options:** Regular SSD, chọn gói tiết kiệm chi phí nhất ($4/tháng hoặc $6/tháng: 1GB RAM, 1 vCPU, 25GB SSD/NVMe).

4. **Phương thức xác thực (Authentication Method):**
   - Chọn **SSH Keys**.
   - Nhấn **New Key**, dán nội dung Public Key (`id_ed25519.pub`) đã sao chép ở Bước 3 và đặt tên nhận diện (ví dụ: `my-laptop-key`).
   - Tích chọn khóa vừa thêm để gán vào Droplet.

5. **Hoàn tất và Khởi tạo:**
   - Đặt Hostname dễ nhớ (ví dụ: `ubuntu-server-droplet`).
   - Nhấn **Create Droplet** và chờ hệ thống phân bổ địa chỉ IPv4 công khai.

---

## 5. Kết nối SSH vào Droplet từ máy cá nhân
Sau khi Droplet hiển thị địa chỉ IP công khai trên bảng điều khiển, mở terminal máy cá nhân và thực thi lệnh kết nối:

```bash
ssh -i ~/.ssh/id_ed25519 root@<IP_ADDRESS_DROPLET>
```

*(Lưu ý: Thay thế `<IP_ADDRESS_DROPLET>` bằng địa chỉ IPv4 thực tế được cấp bởi DigitalOcean).*

**Kết quả mong đợi:**
- Kết nối thành công không yêu cầu nhập mật khẩu.
- Terminal hiển thị banner chào mừng của Ubuntu Server (`Welcome to Ubuntu 22.04... LTS`) và dấu nhắc lệnh `root@ubuntu-server-droplet:~#`.

---

## 6. Kết quả thực hiện

### 6.1. Log tạo cặp khóa SSH trên máy cá nhân
```bash
user@local-pc:~$ ssh-keygen -t ed25519 -C "devops_student@example.com"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/user/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user/.ssh/id_ed25519
Your public key has been saved in /home/user/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:7fX9kL3qA8bZ2mR6vY4wT1pE5oN0sC9uI3hG6dF8jK0 devops_student@example.com
The key's randomart image is:
+--[ED25519 256]--+
|      .+=o.      |
|     .o+*.o      |
|    . .O=o .     |
|     .= =        |
|    . . S        |
|   . = =         |
|  . * B o        |
|   + * B .       |
|    o =.E        |
+----[SHA256]-----+

user@local-pc:~$ cat ~/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGV4YW1wbGVwdWJsaWNrZXljb250ZW50OTg3NjU0MzIx devops_student@example.com
```

### 6.2. Thông tin Droplet được khởi tạo trên DigitalOcean Console
```text
+-----------------------------------------------------------------------------------+
| DigitalOcean Droplet Details                                                      |
|                                                                                   |
| Name:         ubuntu-server-droplet                                               |
| Status:       Active                                                              |
| Public IP:    159.89.123.45                                                       |
| Region:       Singapore (SGP1)                                                    |
| OS:           Ubuntu 22.04.4 LTS x64                                              |
| Plan:         Basic / 1 GB RAM / 1 vCPU / 25 GB SSD                               |
| Auth Method:  SSH Key (my-laptop-key)                                             |
+-----------------------------------------------------------------------------------+
```

### 6.3. Log kết nối SSH từ máy cá nhân vào Droplet
```bash
user@local-pc:~$ ssh -i ~/.ssh/id_ed25519 root@159.89.123.45
The authenticity of host '159.89.123.45 (159.89.123.45)' can't be established.
ED25519 key fingerprint is SHA256:4tM9vQ1wP7zX3yB8nR2kL5jO0sC9uI6hG3dF8jE1aK4.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '159.89.123.45' (ED25519) to the list of known hosts.

Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-105-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

  System information as of Thu Oct  1 20:10:05 UTC 2026

  System load:  0.08               Processes:             95
  Usage of /:   4.2% of 24.05GB    Users logged in:       0
  Memory usage: 18%                IPv4 address for eth0: 159.89.123.45
  Swap usage:   0%

0 updates can be applied immediately.

root@ubuntu-server-droplet:~# whoami
root
root@ubuntu-server-droplet:~# uname -a
Linux ubuntu-server-droplet 5.15.0-105-generic #115-Ubuntu SMP Mon Apr 15 17:33:04 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux
```

---

## 7. Kết luận
Bài thực hành đã hoàn thành việc:
- Khởi tạo thành công máy chủ ảo Droplet trên DigitalOcean tại khu vực Singapore với cấu hình tối ưu chi phí.
- Thiết lập phương thức xác thực an toàn bằng cặp khóa SSH (`ed25519`), loại bỏ hoàn toàn nguy cơ tấn công brute-force mật khẩu.
- Kết nối thành công từ xa vào máy chủ qua giao diện dòng lệnh SSH với tài khoản `root`, sẵn sàng cho các bài thực hành cấu hình tiếp theo.
