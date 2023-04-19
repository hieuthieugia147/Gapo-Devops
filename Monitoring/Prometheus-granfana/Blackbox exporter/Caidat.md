# Giới thiêu về blackbox exporter
## Why to use
The Blackbox exporter provides metrics about HTTP latencies, DNS lookups latencies as well as statistics about SSL certificates expiration.

The Blackbox exporter is mainly used to measure response times.

As a consequence, if you are looking for more detailed metrics for your application (for example a Python application), you will have to instrument your application.

It is noteworthy to say that the Blackbox exporter can be bound with the AlertManager and Prometheus in order to have detailed alerts when one endpoint goes down.

When running, the Blackbox exporter is going to expose a HTTP endpoint that can be used in order to monitor targets over the network. By default, the Blackbox exporter exposes the /probe endpoint that is used to retrieve those metrics.

For example, if my Blackbox exporter is running on port 9115, and if I query metrics for google.com, this is the endpoint that I can query from the exporter.

Nguồn https://github.com/prometheus/blackbox_exporter
## Sử dụng
Blackbox Exporter là một chương trình exporter cho phép gọi tới các endpoint thông qua các giao thức HTTP, HTTPS, DNS, TCP và ICMP. Điều này cũng có nghĩa bạn có thể sử dụng nó để monitor các endpoint bất kì, có sử dụng một trong các giao thức trên

Theo như trên blackbox cung cấp thông tin về endpoint như thời gian chứng chỉ SSL,vvvvv

### Cài đặt
Cài đặt giống hệt node_exporter ,blackbox hoạt động ở port 9115 . blackbox cũng sẽ gửi metric về prometheus nên chỉ cần kết nối đến prometheus và cấu hình alertrules là có thể gửi cảnh báo về mail hoặc telegram
Link tham khảo https://devconnected.com/how-to-install-and-configure-blackbox-exporter-for-prometheus/
Tải về máy
```
wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.14.0/blackbox_exporter-0.14.0.linux-amd64.tar.gz
tar xvzf blackbox_exporter-0.14.0.linux-amd64.tar.gz
```
```
cd blackbox_exporter-0.14.0.linux-amd64
$ ./blackbox_exporter -h
```
Chuyển vào thư mục bin
```
sudo mv blackbox_exporter /usr/local/bin
sudo mkdir -p /etc/blackbox
sudo mv blackbox.yml /etc/blackbox
```

```
sudo useradd -rs /bin/false blackbox
sudo chown blackbox:blackbox /usr/local/bin/blackbox_exporter
sudo chown -R blackbox:blackbox /etc/blackbox/*
```

```
cd /lib/systemd/system
sudo touch blackbox.service
sudo nano blackbox.service
```
chỉnh file cấu hình như sau: Chỉnh ip máy vào phần weblisten
```
[Unit]
Description=Blackbox Exporter Service
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=blackbox
Group=blackbox
ExecStart=/usr/local/bin/blackbox_exporter \
  --config.file=/etc/blackbox/blackbox.yml \
  --web.listen-address=":9115"

Restart=always

[Install]
WantedBy=multi-user.target
```
Khởi động lại hệ thống
```
sudo systemctl enable blackbox.service
sudo systemctl start blackbox.service
```
add blackbox vào prometheus.yml , 
```
global:
  scrape_interval:     15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090', 'localhost:9115']
```
Kết quả thu được khi chạy localhost:9115

Metrics lấy thông tin về thời hạn chứng chỉ SSL
```
# HELP probe_ssl_earliest_cert_expiry Returns earliest SSL cert expiry in unixtime
# TYPE probe_ssl_earliest_cert_expiry gauge
```

