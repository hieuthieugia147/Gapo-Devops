# TIM HIEU VE ANSIBLE
## gioi thieu ve ansible
> Việc cài đặt và cấu hình các máy chủ thường được ghi chép lại trong tài liệu dưới dạng các câu lệnh đã chạy, với giải thích kèm theo. Cách thức này gây mệt mỏi cho quản trị viên vì phải làm theo từng bước ở mỗi máy khi thiết lập mới, và có thể dẫn đến sai lầm, thiếu sót. (trích: bachkhoa-aptech)

Ansible giúp cấu hình "nhiều" server theo tùy biến rất đa dạng, giảm thiểu thời gian thao tác trên từng server được cài đặt

Mot so thuat ngu khi su dung Ansible
1. Controller Machine: Là máy cài Ansible, nó sẽ chịu trách nhiệm quản lý, điều khiển và gửi các task đến những máy con cần quản lý.

1. Inventory: Là file chứa thông tin những server cần quản lý. File này thường nằm tại đường dẫn /etc/ansible/hosts.
1. Playbook: Là file chứa các task được ghi dưới định dạng YAML. Máy controller sẽ đọc các task này trong Playbook sau đó đẩy các lệnh thực thi tương ứng bằng Python xuống các máy con.
1. Task: Một block ghi lại những tác vụ cần thực hiện trong playbook và các thông số liên quan.
1. Module: Trong Ansible có rất nhiều module khác nhau. Ansible hiện có hơn 2000 module để thực hiện các tác vụ khác nhau, bạn cũng có thể tự viết thêm những module của mình khi có nhu cầu. Một số Module thường dùng cho những thao tác đơn giản như: System, Commands, Files, Database, Cloud, Windows,...
1. Role: Là một tập playbook đã được định nghĩa để thực thi 1 tác vụ nhất định. Nếu bạn có nhiều server, mỗi server thực hiện những tasks riêng biệt. Và khi này nếu chúng ta viết tất cả vào cùng một file playbook thì khá là khó để quản lý. Do vậy roles sẽ giúp bạn phân chia khu vực với nhiệm vụ riêng biệt.
1. Play: là quá trình thực thi một playbook.
1. Facts: Thông tin của những máy được Ansible điều khiển, cụ thể sẽ là các thông tin về OS, system, network,…
1. Handlers: Được sử dụng để kích hoạt những thay đổi của dịch vụ như start, stop service.
1. Variables: Được dùng để lưu trữ các giá trị và có thể thay đổi được giá trị đó. Để khai báo biến, người dùng chỉ cần sử dụng thuộc tính vars đã được Ansible cung cấp sẵn.
1. Conditions: Ansible cho phép người dùng điều hướng lệnh chạy hay giới hạn phạm vi để thực hiện câu lệnh nào đó. Hay nói cách khác, khi thỏa mãn điều kiện thì câu lệnh mới được thực thi. Ngoài ra, Ansible còn cung cấp thuộc tính Register, một thuộc tính giúp nhận câu trả lời từ một câu lệnh. Sau đó ta có thể sử dụng chính kết quả đó để chạy những câu lệnh sau.
## Cai dat ansible
Cai dat tren ubuntu
```
apt-add-repository -y ppa:ansible/ansible
apt-get update
apt-get install -y ansible
```
Sau đó chúng ta tạo 1 file host hoặc iventory với nội dung như sau:
```
[prometheus]
192.168.48.142

[node_exporter]
192.168.48.141

[alertmanager]
192.168.48.145

[grafana]
192.168.48.142

[telegram_bot]
192.168.48.145
```

Để kiếm tra xem có kết nối group bất kì hay không chúng ta có thể dùng lệnh:
```
root@server:/etc/ansible-prometheus-node_exporter-alertmanager# 
ansible prometheus -m ping -u danghieu -k
```
Va ket qua thu duoc nhu ben duoi chung to da co ket noi giua control node voi may nay
```
SSH password:
192.168.48.142 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
Chung ta nen su dung sshkey de ket noi thuan tien hon giua cac may va quan li hieu qua ansible va cac thanh phan khac. Chung ta tao khoa xac thu RSA(private key)
```
 su - danghieu
 ssh-keygen -t rsa
```
O hai dong duoi chung ta co the nhap mat khau cho che do passphare hoac ko nhap de bo qua no
```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```
sau do lan luot add ssh-key vao cac may 
```
ssh-copy-id user@ipnode1
ssh-copy-id user@ipnode2
ssh-copy-id user@ipnode3
ssh-copy-id user@ipnode4
```
## Viet 1 file ansible don gian
1 ansible gom cac thanh phan chinh sau
 Ansible.cfg luu cac thong tin config cua playbook nay vi du nhu ten dang nhap cua nguoi dung, dia chi inventory,vvv . Noi dung 1 file co ban
 ```
inventory = ./inventory
remote_user = danghieu
host_key_checking = False
 ```
 inventory(host) day la file luu cau hinh, dia chi ip cua cac may, password neu co vvv . File nay chung ta luu san trong may hoac tao rieng cho tung playbook khac nhau

 thu muc roles la 1 tep playbook dc dinh nghia rieng biet giong nhu neu o tren. Trong thu muc role duoc chia ra cac thu muc khac nhau nhu files, tasks, templates , vars , modules, moi thu muc dam nhan 1 vai tro khac nhau . moi thu muc thong thuong co 1 file main.yml . Khi chay qua file tasks/main.yml nhung thu muc khac co vai tro bo tro.

Ex: 
Template: Luu cac template cua cac file

Vars: Luu cac gia tri mac dinh chung ta cai san nhu phien ban cua phan mem , ten nguoi dung. vv

Handlers: Được sử dụng để kích hoạt những thay đổi của dịch vụ như start, stop service.

1 file tasks co ban
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
 Link tham khao  https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html