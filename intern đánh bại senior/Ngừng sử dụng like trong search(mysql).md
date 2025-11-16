# Ngừng sử dụng like cho việc search trong mysql

- Nếu như chúng ta nghĩ đến việc triển khai search trong dự án thì điều đầu tiên tôi nghĩ đến đó là tích hợp elastic search. Nhưng không phải lúc nào chúng ta cũng có đủ chi phí cho việc đó vì nó sẽ replice dữ liệu của chúng ta và ăn RAM mà RAM là 1 thứ rất tốn tiền hơn nhiều so với disk

- Vậy làm thế nào để search ngay trong MySQL hiệu quả mà không sử dụng like?
    Ví dụ thực nghiệm
    ```sql
        SELECT COUNT(*) AS total_records FROM products; #13tr

        select sql_no_cache * from products where products.title like "%Nguyen%"; # 7.547s
    ```
    Lưu ý: Nếu dùng toàn từ %title% thì index trong mysql sẽ không được dùng nhưng kết quả thực nghiệm khi dùng index và bỏ phần trăm ở đầu tức còn như này "Nguyen%" thì nó sẽ dùng index và kết quả cho ra là khoảng 1s
    Nhưng cái bài toán ta đưa ra là tìm "%Nguyen%" vậy việc đánh index sẽ chưa thể giải quyết được bài toán này.


    + Giải pháp

    ```sql
        select sql_no_cache * from products where match(products.title) against('Nguyen');#0.1s
    ```

    Với cách trên điều kiện phải đánh index nhưng khi làm như trên thì nó vẫn rẻ hơn es nhiều và nhanh hơn cách dùng like và giải pháp này lại hay bị người ta bỏ quên.

