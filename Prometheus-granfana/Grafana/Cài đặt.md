# Grafana
 ## Install Ubuntu

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
