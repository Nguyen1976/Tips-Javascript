- Redis là gì???


- Tại sao sử dụng redis?
    + Hiệu suất cao: Giả sử người dùng truy cập vào cơ sở dữ liệu thì nó sẽ lâu hơn vì nơi đó lưu ở disk, với cache thì nó sẽ lưu ở bộ đệm. Bài toán đặt ra là đồng bộ dữ liệu giữa redis và csdl(đã trở lời trong repo này).

    + Đồng thời cao: Tức là các yêu cầu trực tiếp mà bộ đệm thực hiện hoạt động là lớn hơn, nhiều hơn so với truy cập vào csdl. Vậy lên xem xét 1 phần yêu cầu của người dùng sẽ chuyển trực tiếp đến bộ đệm.

- Memcached vs redis?


- Redis có bao nhiêu kiểu dữ liệu và kịch bản sử dụng?

- Redis giải quyết cơ chế hết hạn dữ liệu như thế nào ?