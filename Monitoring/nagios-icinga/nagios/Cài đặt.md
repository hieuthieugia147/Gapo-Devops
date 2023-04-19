# Cài đặt

Nagios có 2 phiên bản nagios core(miễn phí) và nagios XI (tính phí). Do nghèo :v nên tải bên core về dùng tạm. Theo như trang chủ nagios giới thiệu bản nagios core được hỗ trợ 1 số tính năng sau đây:
 ![Images\Nagios-Introduction](Images\Nagios-Introduction1.png)

 Bản Nagios core hộ trỡ giám sát cơ sở hạ tần và giám sát server, bên cạnh đó điểm mạnh của Nagios core chính là sự linh hoạt với lượng opensource engine cung cấp rất lớn.
### Cài đặt Nagios Core
Follow theo cách cài đặt trên trang chủ
Nguồn: https://support.nagios.com/kb/article/nagios-core-installing-nagios-core-from-source-96.html#Ubuntu
- Bước 1: Update thư viên 
```
# sudo apt-get update
# sudo apt-get install -y autoconf gcc libc6 make wget unzip # apache2 php libapache2-mod-php7.4 libgd-dev
sudo apt-get install openssl libssl-dev
```
- Bước 2: Download về máy
```
cd /tmp
wget -O nagioscore.tar.gz https://github.com/NagiosEnterprises/nagioscore/archive/nagios-4.4.10.tar.gz
tar xzf nagioscore.tar.gz
```
- Bước 3: Complie 
```
cd /tmp/nagioscore-nagios-4.4.10/
sudo ./configure --with-httpd-conf=/etc/apache2/sites-enabled
sudo make all
```
- Bước 4: Tạo user và group
```
sudo make install-groups-users
sudo usermod -a -G nagios www-data
```

- Bước 5: Chạy các chương trình cần thiết
```
sudo make install
sudo make install-daemoninit
sudo make install-commandmode
sudo make install-config
```

- Bước 6: Tải apache config
```
sudo make install-webconf
sudo a2enmod rewrite
sudo a2enmod cgi
```

- Bước 7: Cấu hình lại tường lửa
```
sudo ufw allow Apache
sudo ufw reload
```
- Bước 8: Tạo tài khoản nagiosadmin
```
sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
```

- Bước 9: 
```
sudo systemctl restart apache2.service
sudo systemctl restart nagios.service
```
Đăng nhập vào theo đường dẫn http://<IP>/nagios và đăng nhập thu được dashboard. username là nagiosadmin còn mật khẩu như chúng ta đã thiết lập ở trên.

Kết quả thu được
<img src="Images\Nagios Dashboard.png">

- Bước 10: Cài đặt network plugin trên local. Chúng ta download network plugin về để 
```
cd /tmp
wget http://www.nagios-plugins.org/download/nagios-plugins-2.1.1.tar.gz
tar zxf nagios-plugins-2.1.1.tar.gz
cd /tmp/nagios-plugins-2.1.1
./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl
make all
make install
```
 
 Đã cài đặt xong Nagios, Chúng ta có thể vào và vọc vach 1 số chức năng của Nagios.Ngoài ra Nagios cung cấp khá nhiều các analyze về hệ thống về trạng thái , thời gian sử dụng , vvv .

 <img src="Images\Nagios_Network-OK.png">

### Cài đặt NRPE

Cài đặt nagios lên một máy được gọi là nagios server. Cài đặt và sử dụng NRPE để có thể giám sát được máy linux từ xa được gọi là remote linux.



