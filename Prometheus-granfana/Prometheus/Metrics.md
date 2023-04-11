# Prometheus Metrics
 Prometheus thong thuong co 4 dang metric chinh bao gom
 Counter,
 Gaus, 
 Histogram,
 va Summary
## Type Metric
### Counter
 Counter la metric tra ve gia tri la so dem. So nay co the chi tang va bi reset ve 0 khi restart. EX: So request den server, so task hoan thanh,...vv

 Counter khong su dung cho cac gia tri co the giam gia tri
ex: số task đã thực hien,....vvv
### Gaus
là một số liệu đại diện cho một giá trị số duy nhất có thể tùy ý tăng và giảm.
Gaus thường được sử dụng cho các giá trị được đo như nhiệt độ hoặc mức sử dụng bộ nhớ hiện tại, nhưng cũng là "số lượng" có thể tăng và giảm, chẳng hạn như số lượng yêu cầu đồng thời

### Histogram
Histogram lấy mẫu các quan sát (thường là những thứ như thời lượng yêu cầu hoặc kích thước phản hồi) và đếm chúng trong các nhóm có thể định cấu hình. Nó cũng cung cấp một tổng của tất cả các giá trị được quan sát
Ex: Kích thước ram , bộ nhớ , 
### Summary
Summary một quan sát mẫu tóm tắt (thường là những thứ như thời lượng yêu cầu và kích thước phản hồi). Mặc dù nó cũng cung cấp tổng số quan sát và tổng của tất cả các giá trị được quan sát, nhưng nó tính toán theo % .
Ex: %cpu đang chạy, % bộ nhớ đang chạy ,vvv
## Các câu lệnh metrics cơ bản 