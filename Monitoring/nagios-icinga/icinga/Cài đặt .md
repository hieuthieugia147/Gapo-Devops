# Cài đặt
## Cài đặt icinga2

### Điều kiện
Để cài đặt được icinga2 chúng ta phải cài đặt trước php , MariaDB và Apache. Nếu chương cài đặt chúng ta thực hiện cài đặt theo lệnh:
```
# sudo apt install apache2 mariadb-server mariadb-client mariadb-common php php-gd php-mbstring php-mysqlnd php-curl php-xml php-cli php-soap php-intl php-xmlrpc php-zip  php-common php-opcache php-gmp php-imagick php-pgsql -y
```
Sau đó kích hoạt và cài đặt mariadb và apache2 tương ứng
```
 sudo systemctl start {apache2,mariadb}
 sudo systemctl enable {apache2,mariadb}
 sudo systemctl status {apache2,mariadb}
```
Tiếp theo, chúng ta cần sử dụng tập lệnh mysql_secure_installation để thiết lập mật khẩu cho tài khoản root cơ sở dữ liệu, xóa người dùng ẩn danh, không cho phép đăng nhập root từ xa và xóa cơ sở dữ liệu thử nghiệm
```
sudo mysql_secure_installation
```
Sau đó nhập mật khẩu và ấn y để ngắt kết nối với quyền root từ xa.Tiếp theo chúng ta cần thiết lập kết nối php
```
$ sudo nano /etc/php/8.1/apache2/php.ini [Trên Ubuntu 22.04]
$ sudo nano /etc/php/7.4/apache2/php.ini [Trên Ubuntu 20.04]
```
Sau đó thực hiện thêm vào file này nội dung tương ứng như dưới đây
```
memory_limit = 256M 
post_max_size = 64M
upload_max_filesize = 100M	
max_execution_time = 300
default_charset = "UTF-8"
date.timezone = "Asia/Ho_Chi_Minh"
cgi.fix_pathinfo=0
```
restart lại dịch vụ apache2 web để cập nhật thay đổi 
```
sudo systemctl restart apache2
```
### Cài đặt Icinga2
Bước 1: Add Icinga2 package responsitory (lệnh này chay dưới quyền root)
```
apt-get update
apt-get -y install apt-transport-https wget gnupg

wget -O - https://packages.icinga.com/icinga.key | apt-key add -

. /etc/os-release; if [ ! -z ${UBUNTU_CODENAME+x} ]; then DIST="${UBUNTU_CODENAME}"; else DIST="$(lsb_release -c| awk '{print $2}')"; fi; \
 echo "deb https://packages.icinga.com/ubuntu icinga-${DIST} main" > \
 /etc/apt/sources.list.d/${DIST}-icinga.list
 echo "deb-src https://packages.icinga.com/ubuntu icinga-${DIST} main" >> \
 /etc/apt/sources.list.d/${DIST}-icinga.list

apt-get update
```

Bước 2: Sau đó tiền hành Download icinga2 và icinga plugin về máy của chúng ta
```
sudo apt install icinga2 monitoring-plugins
```
Bước 3: Kích hoạt icing2 trên máy
```
danghieu@test:~$ sudo systemctl enable icinga2
Synchronizing state of icinga2.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable icinga2
danghieu@test:~$ sudo systemctl start icinga2
danghieu@test:~$ sudo systemctl status icinga2
```

![](Images\Icinga2_status.png)

Thu được kết quả như trên chúng ta kích hoạt thành công icinga
### Cài đặt Icinga2 IDO Module
 **Icinga2 Data Output** ( IDO )có nhiệm vụ xuất tất cả thông tin cấu hình và trạng thái vào cơ sở dữ liệu. Cơ sở dữ liệu IDO sau đó được Icinga Web 2 sử dụng làm phụ trợ dữ liệu.


Bước 1: Tải về máy sau đó chúng ấn yes để kích hoạt và nhập mật khẩu cho mysql
```
sudo apt install icinga2-ido-mysql -y
```
Bước 2: Kết nối và mysql
```
sudo mysql -u root -p
```
Bước 3: Tạo database và người dùng(Nhập từng lệnh 1 để kiểm tra)
```
>CREATE DATABASE icinga_ido_db;
> GRANT ALL ON icinga_ido_db.* TO 'icinga_ido_user'@'localhost' IDENTIFIED BY 'Password321';
> FLUSH PRIVILEGES;
> EXIT;
```
Với câu lệnh này chúng ta tạo 1 db có tên la icinga_ido_db, username icinga_ido_user và mật khẩu xác thực là Password321.

Với cơ sở dữ liệu tại chỗ, hãy tiếp tục và nhập lược đồ Icinga2 IDO bằng lệnh. Bạn sẽ được yêu cầu cung cấp mật khẩu gốc của máy chủ cơ sở dữ liệu
```
sudo mysql -u root -p icinga_ido_db < /usr/share/icinga2-ido-mysql/schema/mysql.sql
```
Bước 4: Kích hoạt module icinga2 IDO để có thể thực hiện giao tiếp giữa database và icinga web
```
sudo vim /etc/icinga2/features-available/ido-mysql.conf
```
Chỉnh sửa các mục nhập sau và đặt chúng khớp với chi tiết cơ sở dữ liệu icinga2-ido-mysql như được chỉ định trong bước 3
```
/**
 * The db_ido_mysql library implements IDO functionality
 * for MySQL.
 */

library "db_ido_mysql"

object IdoMysqlConnection "ido-mysql" {
  user = "icinga_ido_user",
  password = "Password321",
  host = "localhost",
  database = "icinga_ido_db"
}
```

Bước 5: kích hoạt ido-mysql và cập nhật icinga
```
danghieu@test:~$ sudo icinga2 feature enable ido-mysql
Enabling feature ido-mysql. Make sure to restart Icinga 2 for these changes to take effect.
danghieu@test:~$ sudo systemctl restart icinga2
```
### Cài đặt icinga2 web
Bước 1: Download
```
sudo apt install icingaweb2 icingacli -y
```
Bước 2: Tao database server cho icinga web
```
sudo mysql -u root -p
```
Tạo database cho icingaweb2 theo câu lệnh dưới
```
> CREATE DATABASE icingaweb2;
> GRANT ALL ON icingaweb2.* TO 'icingaweb2user'@'localhost' IDENTIFIED BY 'Password';
> FLUSH PRIVILEGES;
> EXIT;
```
Bước 3: Tạo token để kết nối lên trang icinga web
```
danghieu@test:~$ sudo icingacli setup token create
The newly generated setup token is: b230f6590c8a43cd
```
token để kết nốt là b230f6590c8a43cd
Bước 4: truy cập vào đường dẫn http://server-ip/icingaweb2/setup để setup kết nối

Bước 5 rhiết lập api cho icinga2
```
sudo icinga2 api setup
```
```
sudo vi /etc/icinga2/conf.d/api-users.conf
```