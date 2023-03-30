# Cai dat Prometheus
## Gioi thieu ve Prometheus

Su dung Ubuntu Server 22.04 LTS
## Cai dat Prometheus 
Tat ca cac lenh chay duoi quyen root

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
root@server:/# touch etc/prometheus/prometheus
root@server:/# nano etc/prometheus/prometheus
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
## Cai dat node_exporter tai cac node de truy xuat du lieu ve Server
### Ubuntu server node
Buoc 1: Tao user cho node_exporter
```
useradd --no-create-home --shell /bin/false node_exporter
```
Buoc 2: Tai source code va phan quyen va cac thu muc
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

### Window node


### Cau hinh tren server

## Cai dat AlertManagerment(Tren Server)
### Cai dat
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
### Tao alert rules cho Server
