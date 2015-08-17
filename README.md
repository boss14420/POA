# Demo Padding Oracle Attack

## Server

Một HTTP server đơn giản viết bằng python. Nhận các request từ client (ví dụ
trình duyệt) và trả về một file tài nguyên tương ứng.

Một request url hợp lệ có dạng:

    http://host:port/d=<identifier>&iv=<IV>

Với `identifier` và `IV` là IV và bản mã hóa của đường dẫn file yêu cầu trong hệ
thống file của server. Thuật toán mã hóa được sử dụng ở đây là AES128, với mode
CBC.

Để có được URL hợp lệ thì cần biết khóa `decryptionKey`, thông tin này chỉ có
server mới biết. Như vậy theo lý thuyết thì người dùng không thể yêu cầu được
một file nào mà server không chia sẻ.

### Khởi chạy server

Vào thư mục `server`, gõ lệnh:

    ./Server.py

Mặc định server sẽ khởi chạy ở địa chỉ `localhost:9000`.

Chương trình sử dụng thư viện `pycrypto` cho việc mã hóa, nên nếu có lỗi xảy ra
nhiều khả năng là do chưa cài đặt thư viện này.

### Ví dụ về URL hợp lệ:

URL để tải file `file.txt` trong thư mục `a/b/c/` (`a` nằm ở cùng thư mục với
`Server.py`.

    http://localhost:9000/d=zAqcCC%2Feulkq6AETCB2GPg%3D%3D&iv=mSNAJtN%2BmMuipD6yoa%2BMHQ%3D%3D

## Khai thác lỗi
Server nói trên bị dính lỗ hổng Padding Oracle, trong đó nếu thông tin mã hóa
trong URL giải mã ra được nhưng sai cách padding (chèn các byte cuối cho tròn
16 byte) thì sẽ trả về mã lối 500, nếu đúng cách padding thì sẽ trả về mã lối
404 (không có tài nguyên) hoặc thành công 200.

### Cách sử dụng
Để tải về file, ví dụ `Server.py` (file mã nguồn, có chứa nhiều thông tin nhạy
cảm) thì vào thư mục `client`, gõ lệnh:

    ./exploit.py Server.py

Sau một lúc (khoảng 5 - 10s), chương trình sẽ in ra một URL hợp lệ để tải file
`Server.py`. Ví dụ:

    http://localhost:9000/d=elxudic79BnfsOcNqYYKnw%3D%3D&iv=VrvFyCefQ%2BSlNIAD0PcWPA%3D%3D

*Chú ý*: mỗi lần chạy sẽ cho ra một URL khác nhau, tất cả đểu hợp lệ.
