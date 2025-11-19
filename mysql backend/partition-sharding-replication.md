- 3 khái niệm paritioning, sharding, replication là 3 khái niệm dễ gây nhầm lẫn trong mysql

- partitioning là việc chia nhỏ dữ liệu từ dữ liệu lớn theo 1 tiêu chỉ nhất định có thể giúp tăng hiệu năng truy vấn và thường là vẫn nằm trên 1 server. Ví dụ table user đang rất lớn và cần truy vấn theo những người dùng cũ và mới dựa theo năm thì việc partitioning triển khai sẽ giúp giải quyết vấn đề này index cũng k thể hiệu quả bằng partition trong trường hợp này

- Sharding là phần tán dữ liệu thành nhiều phần nhưng mỗi shard lại được đặt ở trên 1 con server khác nhau với mục tiêu scale-out tăng khả năng chịu tải và giảm áp lực lên 1 con db duy nhất. Có thể hiểu shard là partition được đặt trên còn server khác giả sử có thể shard theo vị trí địa lý của người dùng. Sharding phức tạp hơn partitioning rất nhiều.

- Replication là sao chép từ dữ liệu gốc thành nhiều bản khác nhau thành nhiều node khác nhau và nó sẽ luôn được cập nhật cho giống node primary (không được nhầm lần với backup) mục đích là tăng độ chịu lỗi, tính sẵn sàng và tốc độ đọc