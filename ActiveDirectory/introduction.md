# Tổng quát
Active Directory là 1 kiến trúc độc quyền trên thị trường của Microsoft. Theo đó nó là 1 kiến trúc không thể nào thiếu trên Windows Server và có thể được hiểu là 1 dịch vụ thư mục. Đặc biệt nó có khả năng quản trị và tập trung hoàn hảo người dùng, những nguồn tài nguyên trong cùng hệ thống mạng.

Active Directory hay AD là 1 dịch vụ thư mục đã được Microsoft phát triển nên dành cho những mạng dùng Windows domain. Theo đó dịch vụ này hiện tại đang bao gồm trong hầu hết những hệ điều hành Windows Server ở dạng tập hợp những dịch vụ và quy trình. 

Active Directory ban đầu chỉ phụ trách quản lý những tên miền tập trung và bắt đầu cùng Windows Server 2008. Nhưng sau đó nó đã trở thành 1 tiêu đề chung dành cho 1 loạt những dịch vụ có liên quan tới danh tính dựa vào thư mục.

# AD DS(Active Directory Domain Service)
A directory is a hierarchical structure that stores information about objects on the network. A directory service, such as Active Directory Domain Services (AD DS), provides the methods for storing directory data and making this data available to network users and administrators. For example, AD DS stores information about user accounts, such as names, passwords, phone numbers, and so on, and enables other authorized users on the same network to access this information.

Nguồn: https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview

1 máy chủ nếu như chạy AD DS – Active Directory Domain Service sẽ gọi là domain controller. Theo đó nó sẽ ủy quyền và xác thực cho toàn bộ máy tính cũng như người dùng trong mạng loại Windows gán, thực thi những chính sách về bảo mật cho toàn bộ những cài đặt, máy tính hay cập nhật phần mềm.  

# Thành phần chính của AD
## Active Directory Objects
Dữ liệu trong Active Directory ví dụ thông tin users, database, groups, computers, security policies, máy in và server sẽ được tổ chức như những objects (đối tượng). Mỗi một object sẽ có các thuộc tính riêng và đặc trưng cho một object đó. 

Cụ thể ví dụ object user có những thuộc tính liên quan như Logon Name, First Name, Last Name,… và Computer Object có những thuộc tính ví dụ như computer name cùng description.

Theo đó một số những object đặc biệt gồm có nhiều object khác nhau bên trong sẽ gọi là những “container”, ví dụ domain là 1 container gồm có nhiều computer account và user.
## Active Directory Schema
Database lưu trữ trong Active Directory, chính là AD Schema. Theo đó Schema sẽ định nghĩa những đối tượng lưu trữ trong Active Directory. Tuy nhiên Schema sẽ lưu trữ những đối tượng như thế nào? 

Schema thực chất là 1 danh sách những định nghĩa xác định những loại đối tượng cũng như những loại thông tin liên quan tới đối tượng lưu trữ thuộc Active Directory. Schema về bản chất cũng được lưu trữ tương tự như một object.
Định nghĩa Schema gồm có hai loại đối tượng đó chính là Schema Attribute objects và Schema Class objects. Theo đó: 

### Schema Class objects 
Schema Class objects có chức năng tương tự như 1 template tạo mới những đối tượng trong AD. Mỗi một Schema Class là 1 tập hợp những thuộc tính của đối tượng. Nếu như tạo 1 đối tượng thuộc về 1 loại Schema Class thì khi đó Schema Attribute sẽ tiến hành lưu trữ những thuộc tính trong đối tượng đó tương ứng loại Schema Class trong đối tượng. 

### Schema Attribute
Là định nghĩa những Schema Class tương ứng cùng với nó. Mỗi một thuộc tính theo đó chỉ được định nghĩa 1 lần trong Active Directory và đồng thời có thể thuộc nhiều những Schema Class với quan hệ 1 nhiều (1-m).

1 tập hợp những Schema Attribute và Schema Class mặc định được đóng gói sẵn chung cùng Active Directory. Nhưng Schema trong Active Directory đã mở ra 1 khả năng về phát triển mở rộng những Schema Class trên những Attribute có sẵn hoặc tạo mới những Attribute Schema.

Nhưng để có thể mở rộng cũng như phát triển cùng với schema, thì khi đó phải chuẩn bị kỹ thông qua những bản thiết kế rõ ràng cũng như xem xét có cần thiết không. Do rủi ro với những hệ thống hoạt động ổn định là khá cao. 
## Active Directory Components
Trong một mô hình mạng doanh nghiệp hiện nay, những components của Active Directory đang được sử dụng cũng như áp dụng nhằm xây dựng nên những mô hình phù hợp cho nhu cầu của những doanh nghiệp. Nếu như xem xét về mô hình kiến trúc AD sẽ phân làm hai loại là Logical và Physical. 

### Logical Structure
Trong AD hiện tại việc tổ chức tài nguyên sẽ dựa theo cơ chế là Logical Structure và được ánh xạ qua mô hình domains, forest, OUs và trees. Nhóm những tài nguyên sẽ được tổ chức 1 cách luận lý và cho phép người dùng truy xuất dễ dàng tới tài nguyên hơn phải nhớ vị trí vật lý cụ thể của nó.
### Physical Structure
Nếu như xét về physical component của AD hiện tại sẽ bao gồm hai phần là Domain Controllers và Sites. Tùy thuộc chủ yếu vào mô hình tổ chức trong công ty mà người quản trị sẽ sử dụng những components để thiết kế phù hợp.
# Chức năng 
Active Directory hiện đang cung cấp một số những dịch vụ đa dạng khác nhau có thể kể đến như: 

- Domain service: quản lý giao tiếp giữa domain cũng như user và lưu trữ những dữ liệu tập trung. Gồm có chức năng tìm kiếm và xác thực đăng nhập. 
- Certificate Services: là tạo, quản lý và phân phối những secure certificate
Lightweight Directory Services: Sẽ hỗ trợ những ứng dụng thư mục thông qua giao thức mở (LDAP)
- Rights Management: là bảo vệ những thông tin có bản quyền thông qua việc ngăn chặn dùng cũng như phân phối trái phép những nội dung về kỹ thuật số.
- Directory Federation Services: sẽ cung cấp đăng nhập 1 lần (SSO) để có thể xác thực người dùng trong 1 phiên duy nhất tại nhiều ứng dụng website.

# Cài đặt
Truy cập theo hướng dẫn 