# Ansible Monitoring
 ansible dung muc dich de tu dong hoa cai dat tren cac may chu thay vi dung command line
 Trong phần này sử dụng 1 mô hình cài đặt 3 máy dể thiết lâp cài đặt cơ


| Ten   | ip             | Tool cai dat |
:-----  | :---------- | :-------------- 
| Sever | 192.168.48.142 | Prometheus - Grafana | 
| AlertManager | 192.168.48.145      | Alertmanager - Prometheusbot               | 
| Node  | 192.168.48.141          | Node_exporter              | 

## Hoat dong

## Code
Chúng ta có thể tham khảo đường link https://github.com/cloudalchemy

Họ lập trình rất đầy đủ cấu hình với các hệ điều hành khác nhau.Chỉ cần vác về là chạy được (Update: Mới bị khóa cmnr, h chỉ xem được thôi). Nhưng tương đối khó hiểu với người mới bắt đầu như mình do đó mình viết đơn giản trước :v, chỉ chạy được trên ubuntu-server nhưng đơn giản hơn

Souce code : https://github.com/hieuthieugia147/ansible-code

## Source Code

## Review
### Giới thiệu thư mục
```
root@server:/etc/ansible-prometheus-node_exporter-alertmanager# ls
```
```
alertmanager_playbook.yml  grafana_playbook.yml  node_exporter_playbook.yml  prometheus_playbook.yml  roles
ansible.cfg                inventory             playbook.yml                README.txt               telegram_playbook.yml
```
Một folder của mình bao gồm 4 thành phần chính

ansible.cfg,iventory : luu cac thong tin config, cau hinh cac server:ip,password,vvv

Chúng ta trước khi chạy cần phải truy cập vào file inventory và chỉnh sửa ip và các thông tin khác ứng với 

Các playbook: Chúng ta có file playbook.yml là chạy và cài đặt tất cả các chương trình hoặc chạy từng playbook một với các chương trình khác nhau. Với các chương trình riêng biệt cần chạy với lệnh
```
 ansible-playbook telegram_playbook.yml -i inventory
```
hoặc 
```
 ansible-playbook playbook.yml -i inventory --extra-vars "ansible_sudo_pass=123"
```
thêm extra-vars để tránh việc nhập sudo pass với nhiều máy khác nhau và yêu cầu các máy có chung pass.
### Thư mục roles
roles là một file tập hợp các quy tắc của playbook ở đây do có nhiều tools nên mình chia ra các roles tương ứng với các tools. Mỗi folder bao gồm các file nhỏ hơn quy định quy tắc riêng biệt. Ví dụ
```
root@server:/etc/ansible-prometheus-node_exporter-alertmanager/roles/prometheus# ls
files  handlers  tasks  templates  vars
```
ta thấy ở đây có 5 thư mục.

Thư mục vars đóng vai trò các biến thông tin như đường dẫn,thông tin phiên bản, và chúng ta hoàn toàn có thể chỉnh sửa được bằng cách truy cập vào file ./vars/main.yml

```
serviceName: "prometheus"
userId: "prometheus"
groupId: "prometheus"
exec_command: "/usr/local/bin/prometheus --config.file=/etc/prometheus/prometheus.conf --storage.tsdb.path=/data/prometheus --storage.tsdb.retention=2d"
version: "2.3.2"
```

#### Thư mục tasks có vai trò là cách lệnh thực hiện cài đặt hệ thống file ./var/main.yml
```
- name: Creating prometheus user group
  group: name="{{groupId}}"
  become: true

- name: Creating prometheus user
  user:
    name: "{{userId}}"
    group: "{{groupId}}"
    system: yes
    shell: "/sbin/nologin"
    comment: "{{userId}} nologin User"
    createhome: "no"
    state: present

- name: Install prometheus
  unarchive:
    src: "https://github.com/prometheus/prometheus/releases/download/v{{ version }}/prometheus-{{ version }}.linux-amd64.tar.gz"
    dest: /tmp/
    remote_src: yes

- name: Copy prometheus file to bin
  copy:
    src: "/tmp/prometheus-{{ version }}.linux-amd64/prometheus"
    dest: "/usr/local/bin/prometheus"
    owner: "{{userId}}"
    group: "{{groupId}}"
    remote_src: yes
    mode: 0755

- name: Delete prometheus tmp folder
  file:
    path: '/tmp/prometheus-{{ version }}.linux-amd64'
    state: absent
 - name: Creates directory
  file:
    path: "/etc/prometheus/"
    state: directory
    owner: "{{userId}}"
    group: "{{groupId}}"
    mode: 0755

- name: config file
  template:
    src: prometheus.conf.j2
    dest: /etc/prometheus/prometheus.conf

- name: alert config file
  copy:
    src: "{{ role_path }}/files/alertrules.yml"
    dest: /etc/prometheus/alert.rules.yml

- name: Copy systemd init file
  template:
    src: init.service.j2
    dest: /etc/systemd/system/prometheus.service
  notify: systemd_reload

- name: Start prometheus service
  service:
    name: prometheus
    state: started
    enabled: yes

- name: Check if prometheus is accessible
  uri:
    url: http://localhost:9090
    method: GET
    status_code: 200
```
#### Thư mục handlers chứa các thông tin về việc stop, start services. Cấu trúc ./handler/main.yml
```
- name: Restart the Prometheus service
  service:
    name: prometheus
    state: restarted
  listen: event_restart_prometheus

- name: Reload systemd
  command: systemctl daemon-reload
  listen: systemd_reload
```
 Gọi handlers qua việc sử dụng notify ví du
```
- name: Copy systemd init file
  template:
    src: init.service.j2
    dest: /etc/systemd/system/prometheus.service
  notify: systemd_reload
```
ở đây chương trình thực hiện lệnh systemctl daemon-reload để reload lại các services.
#### Thư mục Templates và Files
Cả hai thư mục đều có chức năng chính lấy các file trong nó và chuyển vào 1 đường dẫn cụ thể nào đó. Điểm khác nhau là các file trong thư mục files được copy trưc tiếp vào đường dẫn đó còn templates hỗ trợ thêm 1 só chức năng như for, while.vvv để phù hợp với từng máy khác nhau.
## Chạy file ansible
Link code: 
Cài đặt prometheus bằng ansible
```
TASK [Gathering Facts] *************************************************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : Creating prometheus user group] *********************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : Creating prometheus user] ***************************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : Install prometheus] *********************************************************************************************************************changed: [192.168.48.142]

TASK [prometheus : Copy prometheus file to bin] ************************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : Delete prometheus tmp folder] ***********************************************************************************************************changed: [192.168.48.142]

TASK [prometheus : Creates directory] **********************************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : Creates directory] **********************************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : config file] ****************************************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : alert config file] **********************************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : Copy systemd init file] *****************************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : Start prometheus service] ***************************************************************************************************************ok: [192.168.48.142]

TASK [prometheus : Check if prometheus is accessible] ******************************************************************************************************ok: [192.168.48.142]

PLAY RECAP *************************************************************************************************************************************************192.168.48.142             : ok=13   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
Sau khi chay 1 file ansible chung ta thu duoc ket qua nhu tren. Neu ko co loi gi(unreachable or failed) thi ansible chay tot con neu co loi do chung ta check lai. Can them cac cau lenh kiem tra vao cuoi file tasks de dam bao he thong da thuc su hoat dong.
skipped la trang thai bo qua, vi du ban dat dieu kien kiem tra de xem he thong co can cap nhat gi ko. neu dap ung thi playbook se bo qua no va chay cac lenh tiep theo.

