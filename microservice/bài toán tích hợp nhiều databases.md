# Bài toán tích hợp nhiều databases được xử lý thế nào?

- Nếu xử dụng nhiều service cho cùng 1 database thì nó có nhược điểm mà cũng chẳng ai làm vậy vì nó đang phá vỡ cái nguyên tắc của microservice
- Về mặt kỹ thuật thì hoàn toán có thể nhưng về mặt kiến trúc thì k lên
- Mục đích của microservice là những microservice không liên quan đến các service khác nhằm mục đích. Ví dụ có những service người ta có thể scale lên để tăng tốc độ 1 service và chúng ta lên tách ra kể cả instance có scale lên thì điểm db nó trở thành điểm nghẽn duy nhất của hệ thống 

- Share data Services:
+ CallAPI(sync)
+ event driven

- Transaction:
