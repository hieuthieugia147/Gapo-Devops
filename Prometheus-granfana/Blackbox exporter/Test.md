# ANSIBLE
Code bao gồm cài đăt phần mềm prometheus , grafana , blackbox_exporter , node_exporter , alertmanager và kết nối alertmanager với telegram(chức năng cuối đang lỗi =)
### Note
 Code chạy trên ubuntu server 22.04 lts, có thể phải chỉnh lại thông tin phiên bản các phần mềm còn lại để phù hợp với hệ điều hành thấp hơn.
## Sử dụng
Bước 1: Để chạy đúng chương trình chúng ta cần thay đổi thông tin ở 2 file ansible.cfg đúng tên user và file inventory thông tin host cho phù hợp
## Chạy code
Điều kiện chúng ta phải add ssh-key, cài đặng python3 cho tất cả các node , cài đặt ansible cho control node 

Chạy toàn bộ các chương trình chúng ta sử dung lệnh:
``` 
 ansible-playbook playbook.yml -i inventory  
```
Hoặc
```
ansible-playbook playbook.yml -i inventory --extra-vars "ansible_sudo_pass=123"
```
nếu tất cả các node cần cài đặt có chung 1 password

Nếu chúng ta muốn chạy từng chương trình riêng lẻ thì chạy từng playbook riêng lẻ,Ví dụ nếu chúng ta chạy chương trình prometheus chúng ta chạy 
```
 ansible-playbook telegram_playbook.yml -i inventory 
```
## Thông tin thêm
Mỗi chương trình tương ứng 1 folder trong foler roles, Ví du prometheus tương ưng folder prometheus.
file vars/main.yml chứa thông tin phiên bản , tên service , chúng ta có thể thay đổi để phù hớp với hệ điệu hành . Ví dụ Ubuntu 22.04 thì node_exporter phiên bản 0.17 trở lên. 