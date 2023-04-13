# So sánh Promtheus và Nagios và Icinga, Nagios
## Link tham khảo
https://viettelco.vn/cac-cong-cu-giam-sat-linux-tot-nhat/
https://community.icinga.com/t/icinga-and-prometheus-whats-the-difference/177

https://www.linkedin.com/pulse/white-box-vs-black-monitoring-vipul-sharma/?trk=pulse-article

https://www.linkedin.com/pulse/sre-concepts-part-7-whiteblack-box-monitoring-marcel-koert/

## Phân loại Giám sát 
Đầu tiên để so sánh giữa 3 phần mềm này thì thì chúng ta giám sát thường có 2 khái niệm khi giám sát là Whitebox Monitoring và Blackbox monitoring.
### Whitebox Monitoring
Loại giám sát này chủ yếu đề cập đến việc giám sát trạng thái bên trong của các ứng dụng đang chạy trên hệ thống của bạn. Chủ yếu liên quan đến việc hiển thị các số liệu dành riêng cho ứng dụng của bạn như tổng số yêu cầu http đã nhận/độ trễ, v.v. Sau đó Các nhà phát triển ứng dụng thích chuyển sang Whitebox Monitoring để nắm quyền sở hữu ứng dụng, để đảm bảo ứng dụng hoạt động mà không có bất kỳ sự cố hoặc lỗi nào như mong đợi. Do đó whitebox monitoring trạng thái của ứng dụng còn sống hay đã chết, mức sử dụng cpu/đĩa. Node_exporter là 1 ứng dụng whitebox monitor điển hình khi cho phép chúng ta thực hiện giám sát thông số về cpu, ram , số http request , disk .

PS: Sau khi đọc 1 số trang hầu hết nếu chúng ta chỉ thực hiện whitebox monitoring họ đều recommend sử dụng Prometheus vì đơn giản dễ sử dụng và cài đặt.

### Blackbox monitoring

Sử dụng Giám sát hộp đen liên quan đến việc sử dụng các công cụ như Nagios, Zabbix hay Icinga2 chủ yếu dựa trên ý tưởng chạy kiểm tra tùy chỉnh trên hệ thống để xác định trạng thái của các ứng dụng / dịch vụ khác nhau có phản hồi chủ yếu là 0 hoặc 1 để cho biết trạng thái của dịch vụ đang được sử dụng. Cách tiếp cận này chủ yếu tập trung vào việc xác định trạng thái hiện tại của ứng dụng thay vì tập trung vào xu hướng hoặc các vấn đề với ứng dụng.
Promtheus cũng có phần mềm giám sát blackbox đó là blackbox_exporter. Tuy nhiên sau khi tham khảo nhiều nguồn em vấn thấy họ thích sử dụng Zabbix , Icinga2 nhiều hơn vì một số vấn đề về hiêu năng cũng như khả năng scale, tự động sửa lỗi. Ngoài ra việc giám sát blackbox monitoring dựa trên giám sát phân tán.
### Network Monitoring
Hệ thống giám sát mạng bao gồm các công cụ phần mềm và phần cứng có thể theo dõi các khía cạnh khác nhau của mạng và hoạt động của mạng, chẳng hạn như lưu lượng truy cập, sử dụng băng thông và thời gian hoạt động. Các hệ thống này có thể phát hiện các thiết bị và các thành phần khác bao gồm hoặc chạm vào mạng, cũng như cung cấp các bản cập nhật trạng thái.

Quản trị viên mạng dựa vào hệ thống giám sát mạng để giúp họ nhanh chóng phát hiện lỗi thiết bị hoặc kết nối hoặc các vấn đề như tắc nghẽn giao thông làm hạn chế luồng dữ liệu. Khả năng phát hiện các vấn đề mở rộng đến các phần của mạng theo truyền thống vượt ra ngoài ranh giới phân định của chúng. Các hệ thống này có thể cảnh báo các quản trị viên về các sự cố qua email hoặc văn bản và gửi báo cáo bằng cách sử dụng  phân tích mạng . 
## So sánh giữa các công cụ
So sánh prometheus Icinga2 và NagiosXI
So sánh tại link : https://sourceforge.net/software/compare/Icinga-vs-Nagios-Core-vs-Nagios-XI-vs-Prometheus/ . Họ làm rất trực quan và dễ hiểu để so sánh giữa các công cụ.
## Đánh giá 
### Prometheus 
#### Ưu điểm
Prometheus kết hợp với Grafana tạo thành 1 môi trường giám sát,theo dõi rất trực quan và hoàn hảo. Bên cạnh đó Prometheus hoạt động được với hầu hết các loại OS phổ biến và là tool duy nhất trong 3 tool này có thể giám sát được hệ thống container, k8s cluster, vvv. Vì thế nếu sử dụng giám sát server trên hệ điều hành khác(trừ linux/window server) cũng như các hệ thống k8s thì promtheus là lựa chọn tốt nhất trong 3 tool này.

Còn đối với linux/window server thì người ta (em đọc 1 số bài thảo luận trên các trang) thì họ đều chủ yếu sử dung prometheus để giám sát whitebox thông qua node_exporter . Còn khi giám sát blackbox và network các nhà phát triển thường sử dụng ganois, icinga2 , hoặc Zabbix . Một số nguyên nhân được đưa ra là ví các công cụ như icinga2, Zabbix có khả năng giám sát phân tán, hiệu năng tốt hơn promtheus khi giám sát blackbox, cộng đồng hỗ trợ cũng tôt hơn cũng như khả năng mở rộng, thay đổi tốt hơn promtheus.

Ngoài ra promtheus hộ trỡ giám sát các hệ thống time series Database nên có thể sử dụng giám sát hệ thống database thông qua mysqld_exporter.
#### Nhược điểm
- So với 2 công cụ còn lại prometheus khá khó chỉnh sửa, tức là việc cài đặt theo các tool prometheus đã cung cấp trước.

- Hỗ trợ hạn chế Network Monitoring,Web Monitoring chủ yếu tập trung vào việc Server Monitoring.
### Ganois Core
#### Ưu điểm
- Hộ trợ việc giám sát phân tán
- Hỗ trợ rất nhiều loại Plugin khác nhau giúp người dùng thoải mái lựa chọn, cài đặt tùy theo mục đích.
- Hộ trỡ analyze, phân tích thông tin, trạng thái mạng khá tốt
#### Nhược điểm
 - Giao diện đời Tống :) , thiếu trực quan
 - Cài đặt host thủ công , tức là khi có 1 host vào hệ thống thì chúng ta phải cài đặt thủ công bằng tay
 - Chưa viết Ansible cho phần này nhưng khi cài bằng CLI kha khá lỗi trên ubuntu 22.04 LTS , nên sử dụng Ubuntu 20.04 trở xuống.
### Ganois XI
Mất phí nên chưa test được do đó phần đánh giá này chủ yếu dựa trên thông tin nhà cung cấp đưa ra
#### Ưu điểm
Bản nâng cấp của Ganois Core nên được bổ sung nhiều tính năng đặc biệt là về dashboard và  Network Monitoring so với bản miễn phí.

Cung cấp thêm database monitoring,..vv và nhiều tính năng khác.

#### Nhược điểm
- Chưa test đc nên ko rõ
- Mất phí :)
### Icinga2
Icinga là một nhánh của ganois core được phát triển khoảng 2009. Icinga2 có thêm các tính năng mới như giao diện người dùng hiện đại Web 2.0, các kết nối cơ sở dữ liệu bổ sung (cho MySQL, Oracle, Và PostgreSQL) và một API REST cho người dùng
#### Ưu điểm
- Tận dụng được số lương lớn plugin của nagios.
- Kết nối được nhiều loại database khác nhau
- Giao diện tốt, trực quan dễ sử dụng
- Hộ trỡ giám sát trên Cloud: hỗ trợ trên Amazon linux 2(chưa test)
#### Nhược điểm
-  Cấu hình hơi khó khăn loằng ngoằng lúc đầu (Cài đặt ban đầu khá phức tạp)
-  Các nhà phát triển không cung cấp bất kỳ sự tư vấn hoặc hỗ trợ của bên thứ nhất
## Giới thiệu và cài đặt các công cụ trên: Trong thư mục tương ứng
Prometheus-Grafana : https://github.com/hieuthieugia147/Gapo-Devops/tree/main/Prometheus-granfana
Icinga2 : https://github.com/hieuthieugia147/Gapo-Devops/tree/main/nagios-icinga/icinga
Nagios : https://github.com/hieuthieugia147/Gapo-Devops/tree/main/nagios-icinga/nagios
## Tổng kết
| Chức năng   | Promtheus - Grafana            | Nagios Core |Icinga2 
:-----  | :---------- | :-------------- |  :--------------
|Ngôn ngữ sử dụng| Go | C++ | C++
|Mô hình quản trị|không thông qua cụm dữ liệu phân tán,từng node của prometheus đôc lâp với nhau |cụm dữ liệu phân tán | cụm dữ liệu phân tán|
|Opensoruce Support|Ít|Lớn|Lớn|
|Giám sát trên server | Có hỗ trợ tất cả loại OS phổ biến | Chỉ linux và window | Chỉ linux và window |
|Giám sát SaaS/Web | Ít hỗ trợ     | Có               | Có |
| Giám sát tài nguyên mạng  | Có (Blackbox_exporter)        | Có (bản XI hỗ trợ tốt hơn)         | Có |
| Giám sát thiết bị mạng(router, printer) | Không | Có | Có | 
|Cloud Monitoring|Không|Không|Có(trên AWS)|
|Container Monitoring|Có|Không|Không|
|Infrastructure Monitoring|Có|Có|Có|
|Thu thập dữ liệu|Tốt|Kém|Tùy thuộc vào Plugin*|
|Cách thức thu thập dữ liệu| Thông qua metrics|Sử dụng Plugin|Sử dụng Plugin|
|Mức độ tiêu thụ(tiêu thụ Ram,CPU)|cao hơn 2 hệ thống còn lại|thấp|thấp|
|Chi phí|Miễn phí|Miễn phí(Core)- Trả phí với bản XI|Miễn phí|
|Khả năng mở rộng|Tốt|Kém-Phải tự cấu hình tay khi 1 node mới vào mạng|Tốt|
|Cài đặt|Nhanh,đơn giản|Nhanh|Phức tạp|
|Dashboard|Tốt|Kém|Tốt|
| DataBase| Nhiều database khác nhau | Ít | mySQL,mariaDB,Oracle, Và PostgreSQL
|Thiết lập cảnh báo|Có, đơn giản||Có|
|Documentation and support|Tốt|Tốt|Tốt|
|Xác thực và mã hóa|Không hỗ trợ sẵn, dễ dàng truy cập lấy thông số từ các endpoint|Hỗ trơ xác thực,mã hóa |Hỗ trơ xác thực, mã hóa,kiểm tra thông tin SSL/TLS|
|Độ tin cậy khi thu thập dữ liệu|Cao||Cao|

 Promtheus-Grafana hiệu quả hơn trong việc giám sát hệ thống server vì sự tiện lợi,dễ cấu hình và hỗ trợ nhiều hệ điều hành khác nhau.Prometheus được sử dụng để giám sát hầu hết các hệ thống container và Kubernetes.

 Icinga hỗ trợ  việc giám sát các Services as a Software, các trang web: như các giao thức, chứng chỉ SSL .Icinga hiệu quả hơn promtheus việc giám sát cụm dữ liệu phân tán và giám sát trên Cloud.

 Nagios phù hợp kiểu giám sát blackbox khi chúng ta chủ yếu tập trung việc kiểm tra trạng thái(up/down) và thời gian hoạt động của dịch vụ mà ko quan tâm đến các dữ liệu khác. Nagios đơn giản, gọn nhẹ tiêu thụ ít tài nguyên hơn 2 ứng dụng trên.


