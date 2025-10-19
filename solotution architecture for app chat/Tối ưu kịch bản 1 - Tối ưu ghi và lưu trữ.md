## Tối ưu kịch bản: giảm ghi trùng lặp và giảm I/O

Mục tiêu: giảm số lần ghi (write) và dung lượng lưu trữ khi một message gửi đến nhiều người trong cùng một group, đồng thời giữ được hiệu năng đọc (read) tốt.

### Vấn đề

- Nếu lưu một bản sao message cho mỗi user trong group (mô hình "mỗi user 1 box"), thì với group có 8.000 user, một tin nhắn sẽ tạo 8.000 bản ghi.
- Ví dụ: tin nhắn ~250 byte → 8.000 \* 250 = ~2 MB cho 1 thông báo. Gây tăng I/O, chi phí lưu trữ cao và có thể làm nghẽn hệ thống.

### Các phương án cân nhắc

1. Box đại diện cho group: lưu một bản message cho nhóm, các user pull nội dung khi cần.
2. Mỗi user có 1 box riêng (tạo bản sao cho mỗi user). Ưu về truy vấn (mỗi user chỉ đọc box của mình), nhưng tốn rất nhiều ghi và lưu trữ.

Chúng ta sẽ chọn phương án kết hợp để cân bằng: tránh duplicate dữ liệu nhưng vẫn giữ nhanh khi đọc.

### Thiết kế đề xuất (chung)

- Bảng `messages`: lưu một bản nội dung tin nhắn chung cho cả group (message_id, sender, content, timestamp, metadata...)
- Bảng `groups` và `users` với bảng trung gian `group_members` cho quan hệ n-n.
- Bảng `group_messages`: tham chiếu giữa group và message (group_id, message_id, sent_at,...)
- Bảng `user_read_progress`: lưu tiến độ đọc cho mỗi user theo group (user_id, group_id, last_read_message_id, last_read_at)
- (Tùy chọn) Bảng `offline_index` chỉ chứa các message_id cho những user offline nếu cần hỗ trợ push/notification riêng.

Sơ đồ minh họa: xem `./imgs/kịch bản 2.drawio.png` (hoặc file tương ứng trong `imgs/`).

### Luồng xử lý

1. Khi một message được gửi trong group:

   - Tạo một bản ghi trong `messages` (1 insert)
   - Tạo một bản ghi liên kết trong `group_messages` (1 insert)
   - Không tạo 1 bản sao message cho từng user.

2. Khi user online hoặc khi cần hiển thị các message mới:

   - Dùng `user_read_progress` để biết `last_read_message_id` của user với group đó.
   - Query `group_messages` để lấy danh sách `message_id` mới hơn `last_read_message_id` rồi join vào `messages` để lấy nội dung.
   - Cập nhật `user_read_progress` khi user đọc tới bản ghi mới nhất.

3. (Optional) Với user offline, có thể lưu `message_id` vào `offline_index` để phục vụ push notification hoặc thống kê, nhưng không lưu nội dung message cho từng user.

### Lợi ích

- Giảm mạnh số lần ghi: thay vì 8.000 ghi cho 1 tin, giờ chỉ có ~2 ghi (1 vào `messages`, 1 vào `group_messages`).
- Giảm dung lượng lưu trữ (loại bỏ duplication).
- Truy vấn vẫn nhanh: chỉ thêm 1 bước join (tìm message_id rồi lấy nội dung), ảnh hưởng hiệu suất nhỏ so với lợi ích giảm ghi.

### Các trường hợp và lưu ý triển khai

- Độ lớn group rất lớn (chục nghìn user): cần index tốt trên `group_messages` và `user_read_progress` (ví dụ index theo (group_id, message_id) và (user_id, group_id)).
- Trường hợp push/notification: có thể vẫn cần một cơ chế đánh dấu user offline để gửi thông báo (nhưng chỉ lưu id, không duplicate content).
- Nếu cần audit/history per-user (ví dụ chỉnh sửa trạng thái đọc riêng lẻ), `user_read_progress` hoặc một bảng events per-user (nhỏ) có thể dùng.
- Xử lý GC/cleanup: nếu `messages` lưu lâu, cân nhắc cơ chế lưu trữ lạnh/archival.

### Tóm tắt

Thiết kế này vẫn giữ được khả năng truy vấn theo user (dựa vào tiến độ đọc), đồng thời giảm drastica số lần ghi và dung lượng lưu trữ bằng cách lưu một bản message duy nhất cho cả group và lưu tiến độ đọc của từng user.

---

Phiên bản: viết lại để rõ ràng hơn (tập trung vào ý chính, luồng dữ liệu và lợi ích triển khai).
