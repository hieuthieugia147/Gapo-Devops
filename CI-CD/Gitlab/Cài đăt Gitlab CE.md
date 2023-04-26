# Cài đặt
Truy cập theo hướng dẫn https://www.osradar.com/install-gitlab-ce-ubuntu-20-04/

Bước 1: Câp nhật apt và cài đặt open SSH
```
sudo apt update 
sudo apt install ca-certificates curl openssh-server postfix 
```
 Để cài đặt postfix , hãy chọn Internet Site khi được yêu cầu . Tiếp theo hãy nhập domain của server của bạn để cấu hình cách hệ thống sẽ gửi thư.

Bước 2 - Cài đặt GitLab
Cập nhật
```
cd /tmp 
curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh 
```
Sau đó 
```
sudo bash /tmp/script.deb.sh
sudo apt install gitlab-ce  
```