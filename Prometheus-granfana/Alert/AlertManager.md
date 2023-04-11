# AlertManagement
### Alert-Rules

```Cau hinh Alert
ALERT <alert name>
  IF <expression>
  [ FOR <duration> ]
  [ LABELS <label set> ]
  [ ANNOTATIONS <label set> ]
```
alert name: ten cua ALert

expression: điều kiện nhất định nào đó

FOR: Chờ đợi trong 1 khoảng thời gian để đưa ra cảnh báo

LABELS: Đặt nhãn cho cảnh báo

ANNOTATIONS: Chứa thêm các thông tin cho cảnh báo

Ví dụ
```
# Alert for any instance that have a median request latency >1s.
ALERT APIHighRequestLatency
  IF api_http_request_latencies_second{quantile="0.5"} > 1
  FOR 1m
  ANNOTATIONS {
    summary = "High request latency on {{ $labels.instance }}",
    description = "{{ $labels.instance }} has a median request latency above 1s (current value: {{ $value }}s)",
  }
```
Alert này sẽ cảnh cáo các request có thời gian trễ trung bình 1 request lớn hơn 1s
IF api_http_request_latencies_second{quantile="0.5"} > 1 Kiểm tra các request có độ trễ lớn hơn 1

FOR 1m Thời gian đợi 1 phút

ANNOTATIONS 
  Trả về thông báo 
