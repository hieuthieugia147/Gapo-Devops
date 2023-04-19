# Monitoring
Do tài nguyên có hạn(Ram 8G) nên mình cài đặt 3 máy để thiêt lập 1 hệ thống monitoring, một hệ thống thông thường bao gồm Nhiều máy chủ cài đặt Node_exporter, vai trò là truy xuất dữ liệu từ các node này và gửi lên promtheus, 1 máy chủ cài promtheus có chức năng thu thập thông tin từ các node đã được gửi lên. Máy chủ này đóng vai trò giám sát, truy xuất dữ liệu từ các node đã được cài đặt
| Ten   | ip             | Tool cai dat | Hệ điều hành |
:-----  | :---------- | :-------------- | :-------------
| Sever | 192.168.48.142 | Prometheus - Grafana - (Alertmanager-Prometheus-bot) | Ubuntu Server 22.04 LTS |
| Alert | 192.168.48.145      | Alertmanager - Prometheus-bot               | Ubuntu Server 22.04 LTS |
| Node1  | 192.168.48.141          | Node_exporter Blackbox_exporter            | Ubuntu Server 22.04 LTS |
| Node2  | 192.168.48.1 | Node_exporter - Blackbox_exporter | Window Home 11 |

Các công cụ cần cài đặt

Sever : cài đặt promtheus và grafana
alert : cài đặt alertmanager và promtheus_bot( để cấu hình gửi lên email hoặc telegram)
Node1,Node2 : Cài đặt node_exporter
## Mô hình hoạt động

## Cài đặt bằng Command line
Tất cả các lệnh chạy dưới quyền root, để chạy dưới quyền root dùng lệnh:
```
sudo su
```
Nếu không chúng ta cần thêm lệnh sudo vào trước các câu lệnh để có thể chạy được.
### Cai dat Prometheus
Đầu tiên chúng ta cần cài đặt Promtheus trên Server. Đây sẽ là máy chủ đóng vai trò thu thập metrics từ các endpoints trong hệ thống.
Bước 1: Tao user cho service
```
useradd --no-create-home --shell /bin/false prometheus
```
Bước 2: Phân quyền cho Prometheus
```
mkdir /etc/prometheus
mkdir /var/lib/prometheus
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus
```
Bước 3: Download di chuyển các file vào các mục tương ứng 
```
cd /opt
wget https://github.com/prometheus/prometheus/releases/download/v2.27.1/prometheus-2.27.1.linux-amd64.tar.gz -O prometheus.tar.gz
tar xvf prometheus.tar.gz 
mv prometheus-2.27.1.linux-amd64 prometheus

cp prometheus/prometheus /usr/local/bin/
cp prometheus/promtool /usr/local/bin/

chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool

cp -r prometheus/consoles /etc/prometheus
cp -r prometheus/console_libraries /etc/prometheus

chown -R prometheus:prometheus /etc/prometheus/consoles
chown -R prometheus:prometheus /etc/prometheus/console_libraries

rm -rf prometheus*
cd -
```
Bước 4: Thiết lập file cấu hình của promtheus
```
root@server:/# touch etc/prometheus/prometheus.yml
root@server:/# nano etc/prometheus/prometheus.yml
```
Noi dung
```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
```
Sau đó khởi động lại promtheus, prometheus sẽ hoạt động ở cổng port 9090
```
systemctl daemon-reload
systemctl enable prometheus
systemctl start promtheus
```
Kiếm tra trạng thái hoạt đông Prometheus
```
systmectl status promtheus
```

 Bước 5: Sau khi kiểm tra thấy status của prometheus đã thành công chúng ta thực hiện truy cập theo đường link ip/9090 để thu được giao diện của prometheus
### Cai dat Grafana
  Bước 1: Download transports-http và grafana
```
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key

```
  Bước 2: Add grafana vào responsitory của apt 
```
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
  Bước 3: Downlaoad Grafana về máy tính của bạn
```
sudo apt-get update
sudo apt-get install grafana
sudo apt-get install grafana-enterprise
```
  Bước 4: Kích hoạt grafana và Truy cập vào đường dẫn ip/3000 để xem giao diện của grafana.  user name và mật khẩu đều là admin. Chúng ta được 
![]
### Cai dat node_exporter tai cac node de truy xuat du lieu ve Server
#### Ubuntu server node
Bước 1: Tao user cho node_exporter
```
useradd --no-create-home --shell /bin/false node_exporter
```
Bước 2: Tai source code va phan quyen va cac thu muc
(phai cai dat phien ban node_exporter 0.17 tro len voi ubuntu 22.04 nếu ko node_exporter sẽ ko thể đọc được thong tin tu ubuntu )
```
cd /opt
wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz

tar xvf node_exporter-0.18.1.linux-amd64.tar.gz

cp node_exporter-0.18.1.linux-amd64/node_exporter /usr/local/bin
chown node_exporter:node_exporter /usr/local/bin/node_exporter

rm -rf node_exporter-0.18.1.linux-amd64*
cd -
```
Bước 3: khoi dong va kiem tra service
```
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
systemctl status node_exporter
```
Bước 4:
#### Window node
Tren window đơn giản hơn chúng ta chỉ cần tải về sau đó lấy metrics ở cổng port 9182 là được
### Cai dat AlertManagerment(Tren alermanager Server) va alertrule(tren Sever)
Bước 1: Tạo user cho alertmanager
```
sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false alertmanager
```
Bước 2: Download giải nén AlertManager binary,sau đó thực hiện phân quyền cho user alertmanager thư mục tương ứng
```
cd /opt
wget https://github.com/prometheus/alertmanager/releases/download/v0.22.0-rc.1/alertmanager-0.22.0-rc.1.linux-amd64.tar.gz

tar xvf alertmanager-0.22.0-rc.1.linux-amd64.tar.gz

mv alertmanager-0.22.0-rc.1.linux-amd64/alertmanager /usr/local/bin/
mv alertmanager-0.22.0-rc.1.linux-amd64/amtool /usr/local/bin/

chown alertmanager:alertmanager /usr/local/bin/alertmanager
chown alertmanager:alertmanager /usr/local/bin/amtool

rm -rf alertmanager-0.22.0-rc.1.linux-amd64*
cd -

mkdir /etc/alertmanager
chown alertmanager:alertmanager /etc/alertmanager
```
Bước 3: Tạo file /etc/systemd/system/alertmanager.service co noi dung nhu sau:
```
[Unit]
Description=Alertmanager
Wants=network-online.target
After=network-online.target

[Service]
User=alertmanager
Group=alertmanager
Type=simple
WorkingDirectory=/etc/alertmanager/
ExecStart=/usr/local/bin/alertmanager --config.file=/etc/alertmanager/alertmanager.yml --web.external-url http://192.168.48.142:9093

[Install]
WantedBy=multi-user.target
```
Nếu dùng VM,link url chúng ta lấy ip của máy gõ ip a để lấy ip,dùng local host trong Vmware có thể sinh ra lỗi refuse connect 

Bước 4: Tạo alert rules cho promtheus trên server
```
touch /etc/prometheus/alert.rules.yml
chown prometheus:prometheus /etc/prometheus/alert.rules.yml
```
Chúng ta thêm 1 rules mới cho vào alert.rules.yml
```
groups:
- name: Instances
  rules:
  - alert: InstanceDown
    expr: up == 0
    for: 10s
    labels:
      severity: page
    # Prometheus templates apply here in the annotation and label fields of the alert.
    annotations:
      description: '{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 10 s.'
      summary: 'Instance {{ $labels.instance }} down'
```
check rules với promtool
```
[root@prometheus ~]# /usr/local/bin/promtool check rules /etc/prometheus/alert.rules.yml
Checking /etc/prometheus/alert.rules.yml
  SUCCESS: 1 rules found
```
Khởi động lại alertmanager để update lại
```
systemctl deamon-reload
systemctl start alertmanager
systemctl status alertmanager
```
Chạy lệnh trên và mở đường dẫn http://ipalert:9093 để kiểm tra xem hệ thống hoạt động hay chưa
### Cấu hình cảnh báo lên telegram,email


## Cài đặt bằng Ansible
Cài đặt bằng ansible rất đơn giản có đầy đủ các chức năng như ở trên
Chúng ta theo đường dẫn https://github.com/hieuthieugia147/ansible-promtheus

## Tìm hiểu thêm
Cài đăt blackbox_exporter để giám sát blackbox : 

Cài đặt 
