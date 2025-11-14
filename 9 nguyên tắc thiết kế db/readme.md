# 9 nguyên tắc

1. 1DB tốt lên có 5 trường mặc định: 
    - updated_at and created_at
    - version
    - modifier_id(vì đôi khi trong các dự án thường có phân quyền và người thao tác với dữ liệu không phải lúc nào cũng là người tạo)
    - creater_id
    
2. Khi viết các field lên viết các comment cho các field và các field lên có thêm 1 trường status cho bảng giả sử banned, active hay pending approval

3. Xóa dữ liệu không phải xóa luôn lên áp dụng kỹ thuật soft delete giả sử thêm field is_deleted

4. khi đặt tên field chúng ta lên có prefix(cũng giống với redis)
- Cách viết prefix là thêm tên bảng trước các trường giả sử feeds thì tên frefix có thể là feed hoặc đơn giản là fd

5. Tách bảng khi 1 bảng có quá nhiều trường
- Ví dụ: khi bảng user có quá nhiều field thì hoàn toàn có thể tách ra thành user(lưu những thông tin authen) và user_profile hay user_details(lưu chi tiết user) và khi cần thì chúng ta mới phải join hoặc lấy 1 trong 2 ra 
- Câu hỏi đặt ra là nó có gây vấn đề về hiệu suất không thì câu trả lời là k vì khi join nó là 1-1 chứ k phải là 1-n thông thường 

6. Chọn đúng kiểu dữ liệu giả sử rule business là title k được vượt quá 100 mà trong db để varchar 250 vô hình chung làm ảnh hưởng đến memory và disk k đáng có(điều này khó thấy khi dữ liệu của chúng ta chưa lớn) 

7. Liên quan đến việc để not null hay không hãy suy nghĩ đến vấn đề này hãy để not null nếu có thể

8. Về vấn đề đánh index và partition và view (tìm hiểu thêm)

9. Nguyên tắc 3NF nó có thể là 4 hay 5NF
