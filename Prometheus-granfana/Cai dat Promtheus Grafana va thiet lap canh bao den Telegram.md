# Gioi thieu ve Monitoring he thong
 O day toi se cai dat thu ngiem 1 mo hinh monitoring gom 3 may. tat ca cac may su dung Ubuntu Server 22.04 LTS

| Ten   | ip             | Tool cai dat |
:-----  | :---------- | :-------------- 
| Sever | 192.168.48.142 | Prometheus - Grafana | 
| Alert | 192.168.48.145      | Alertmanager - Prometheus-bot               | 
| Node  | 192.168.48.141          | Node_exporter              | 

## Hoat dong
Node se co vai tro client , node_exporter thu thap thong tin tu Node nay thong qua cac metrics. Nhap lenh nay de co the xem thong tin ve cac metrics
```
192.168.48.141:9100/metrics
```
Thong tin metrics duoc server thu thap gui prometheus. Sau do prometheus ban cac thong tin nay cho grafana de hien thi du lieu. truy cap vao duong dan de dang nhap vao grafana-server
```
192.168.48.142:3000
```
prometheus co chuc nang alert tuc la tao canh bao.Cac canh bao goi la cac rules.Binh thuong cac rules o trang thai inactive tuc la khong hoat dong. Nhung neu co dieu kien kich hoat rules cac alert duoc gui di. Se co hai trang thai chinh la pending va firing
pending : dang chuan bi duoc gui di,nhung chua den alertmanager khi nay chung ta co the truy cap vao http://192.168.48.145:9093 thi se chua co alert dc gui den
firing: alert da duoc gui di, luc nay chung ta co the truy cao theo duong dan o tren se thu dc 1 alert dang gui toi alertmanager

Alertmanager nhan duoc canh bao co the tiep tuc gui thong bao qua email,telegram,vvv
(do email ko authencation bang viec chi dung user va pass nua nen viec gui qua email ko dc)
### Cai dat Prometheus 
#### Cai dat
Note Tat ca cac lenh chay duoi quyen root

Buoc 1: Tao user cho service
```
useradd --no-create-home --shell /bin/false prometheus
```
Buoc 2: Phan quyen cho prometheus
```
mkdir /etc/prometheus
mkdir /var/lib/prometheus
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus
```
Buoc 3: Download va phan quyen cho cac thu muc prometheus
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
Buoc 4: Tao file cau hinh cua prometheus
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
Voi cau lenh nay promtheus tu lay metrics cua chinh no, port mac dinh cua prometheus 9090
### Cai dat Grafana
 download phien ban moi nhat
```
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key
```
add apt-responsitory
```
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
### Cai dat node_exporter tai cac node de truy xuat du lieu ve Server
#### Ubuntu server node
Buoc 1: Tao user cho node_exporter
```
useradd --no-create-home --shell /bin/false node_exporter
```
Buoc 2: Tai source code va phan quyen va cac thu muc
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
Buoc 3: khoi dong va kiem tra service
```
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
systemctl status node_exporter
```
#### Window node
Tren window đơn giản hơn chúng ta chỉ cần tải về sau đó lấy metrics ở cổng port 9182 là được
### Cai dat AlertManagerment(Tren alermanager Server) va alertrule(tren Sever)
Tao User cho Alert
```
sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false alertmanager
```
Tien hanh Download va giai nen AlertManager binary
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
tao file /etc/systemd/system/alertmanager.service co noi dung nhu sau:
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

Tao alert rules cho Server
```
touch /etc/prometheus/alert.rules.yml
chown prometheus:prometheus /etc/prometheus/alert.rules.yml
```

Them 1 rules mới cho vào alert.rules.yml
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
```
systemctl deamon-reload
systemctl start alertmanager
systemctl status alertmanager
```
Chay lenh tren va mo http://ipalert:9093 de check xem thong tin co nhan dc ko
### Cai dat Prometheusbot va gui canh bao ve telegram