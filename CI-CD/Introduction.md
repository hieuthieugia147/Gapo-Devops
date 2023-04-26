# Khái niệm
With the continuous method of software development, you continuously build, test, and deploy iterative code changes. This iterative process helps reduce the chance that you develop new code based on buggy or failed previous versions. With this method, you strive to have less human intervention or even no intervention at all, from the development of new code until its deployment
Nguồn: Gitlab
## Continuous Integration
Trong phát triển ứng dụng hiện đại, mục tiêu là có nhiều nhà phát triển làm việc đồng thời trên các tính năng khác nhau của cùng một ứng dụng. Tuy nhiên, nếu một tổ chức được thiết lập để hợp nhất tất cả mã nguồn phân nhánh lại với nhau vào một ngày (được gọi là “ngày hợp nhất”), thì công việc có thể trở nên tẻ nhạt, thủ công và tốn nhiều thời gian. Đó là bởi vì khi một nhà phát triển làm việc độc lập thực hiện một thay đổi đối với một ứng dụng, có khả năng nó sẽ xung đột với những thay đổi khác được thực hiện đồng thời bởi các nhà phát triển khác. Vấn đề này có thể phức tạp hơn nếu mỗi nhà phát triển đã tùy chỉnh môi trường phát triển tích hợp cục bộ (IDE) của riêng họ , thay vì nhóm đồng ý về một IDE dựa trên đám mây .

Tích hợp liên tục (CI) giúp các nhà phát triển hợp nhất các thay đổi mã của họ trở lại nhánh dùng chung hoặc "thân cây" thường xuyên hơn—đôi khi thậm chí hàng ngày. Khi các thay đổi của nhà phát triển đối với một ứng dụng được hợp nhất, những thay đổi đó sẽ được xác thực bằng cách tự động xây dựng ứng dụng và chạy các cấp thử nghiệm tự động khác nhau, điển hình là thử nghiệm đơn vị và tích hợp, để đảm bảo các thay đổi không làm hỏng ứng dụng. Điều này có nghĩa là kiểm tra mọi thứ từ các lớp và chức năng đến các mô-đun khác nhau bao gồm toàn bộ ứng dụng. Nếu thử nghiệm tự động phát hiện ra xung đột giữa mã mới và mã hiện có, CI sẽ giúp dễ dàng sửa các lỗi đó nhanh chóng và thường xuyên hơn.
Một bài viết khá hay lúc đi tìm hiểu : https://toidicodedao.com/2015/08/27/giai-thich-don-gian-ve-ci-continuous-integration-tich-hop-lien-tuc/
## Continuous Delivery
Sau quá trình tự động hóa các bản dựng và thử nghiệm tích hợp và đơn vị trong CI, quá trình phân phối liên tục sẽ tự động hóa việc phát hành mã đã xác thực đó vào kho lưu trữ. Vì vậy, để có một quy trình phân phối liên tục hiệu quả, điều quan trọng là CI đã được tích hợp vào quy trình phát triển của bạn. Mục tiêu của phân phối liên tục là có một cơ sở mã luôn sẵn sàng để triển khai sang môi trường sản xuất.

Trong Continuos Delivery, mọi giai đoạn—từ hợp nhất các thay đổi mã đến phân phối các bản dựng sẵn sàng sản xuất—đều liên quan đến tự động hóa thử nghiệm và tự động hóa phát hành mã. Khi kết thúc quá trình đó, nhóm vận hành có thể triển khai ứng dụng vào sản xuất một cách nhanh chóng và dễ dàng
## Continuous Deployment
Giai đoạn cuối cùng của quy trình CI/CD trưởng thành là triển khai liên tục. Là một phần mở rộng của phân phối liên tục, giúp tự động phát hành bản dựng sẵn sàng sản xuất vào kho lưu trữ mã, việc triển khai liên tục sẽ tự động phát hành ứng dụng vào sản xuất. Do không có cổng thủ công ở giai đoạn quy trình trước khi sản xuất, nên việc triển khai liên tục chủ yếu dựa vào tự động hóa thử nghiệm được thiết kế tốt.

Continuos Deployment có nghĩa là thay đổi của nhà phát triển đối với ứng dụng trên cloud có thể xuất hiện trong vòng vài phút sau khi viết (giả sử rằng nó vượt qua thử nghiệm tự động). Điều này giúp việc liên tục nhận và kết hợp phản hồi của người dùng trở nên dễ dàng hơn nhiều. Được kết hợp với nhau, tất cả các phương pháp CI/CD được kết nối này giúp việc triển khai ứng dụng ít rủi ro hơn, theo đó, việc phát hành các thay đổi đối với ứng dụng theo từng phần nhỏ sẽ dễ dàng hơn thay vì tất cả cùng một lúc. Tuy nhiên, cũng có rất nhiều khoản đầu tư trả trước vì các thử nghiệm tự động sẽ cần phải được viết để phù hợp với nhiều giai đoạn thử nghiệm và phát hành khác nhau trong quy trình CI/CD
## GitLab CI/CD
GitLab CI/CD is the part of GitLab that you use for all of the continuous methods (Continuous Integration, Delivery, and Deployment). With GitLab CI/CD, you can test, build, and publish your software with no third-party application or integration needed.

Nguồn: Gitlab

