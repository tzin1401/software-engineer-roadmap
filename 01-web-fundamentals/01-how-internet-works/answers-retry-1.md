# Bài làm 1.1 — làm lại lần 1 (2026-11-06)

> **Kết quả: 3,3 / 7 ≈ 48%. Chưa PASS.** Làm tiếp `quiz-retry-2.md`.
> Nhận xét nằm trong các khối `💬` ngay dưới từng câu.

Câu 1.
a) 113.22.10.4:40001
b) 113.22.10.4:40001
c) Dựa vào bẳng NAT để chuyển về dúng 192.168.1.5:52341
> 💬 🟡 **Đúng một phần (0,5).**
> - a) và b) đúng.
> - c) Bạn trả lời được "tra ở đâu" (bảng NAT) nhưng chưa trả lời "dựa vào **thông tin gì**". Câu đầy đủ: router nhìn **port đích của phản hồi (40001)**, tìm dòng có `113.22.10.4:40001` trong bảng NAT, rồi đổi đích thành `192.168.1.5:52341`. Port chính là chìa khoá để tra.

Câu 2.
Nếu bỏ bước 3 thì sẽ không chắc là bên client có nhận được ACK chưa
> 💬 🟡 **Đúng một phần (0,5).** Hướng đúng, nhưng còn thiếu hai chỗ phải nói rõ:
> - **Bên không chắc chắn là server.** Bạn mới ngầm hiểu, chưa nói ra.
> - **Điều không chắc chắn** là client đã nhận được **số thứ tự bắt đầu `y` của server** (chữ SYN trong SYN-ACK) hay chưa. Cái ACK trong gói đó xác nhận số `x` của client, nên client không cần báo lại phần này.
>
> Câu trả lời mẫu: *"Nếu bỏ bước 3, server không biết client đã nhận được số thứ tự bắt đầu `y` của server hay chưa."*

Câu 3. C
> 💬 ❌ **Sai. Đáp án đúng là B.**
> - FIN nghĩa là "**tôi** gửi xong rồi". Client gửi FIN nên chỉ chiều **client → server** đóng lại.
> - Server mới chỉ ACK ("biết rồi"). Chiều **server → client** vẫn mở, server vẫn **gửi tiếp dữ liệu** được.
> - Gửi hết dữ liệu xong, server mới gửi FIN của mình. Vì ACK và FIN của server thường cách nhau một khoảng nên không gộp được, và đóng kết nối cần 4 bước.
> - Mẹo nhớ: **ai gửi FIN thì người đó ngừng nói, nhưng vẫn nghe.**

Câu 4.
(1) C
(2) D
(3) E
(4) A
(5) B
> 💬 ✅ **Đúng cả 5.** Phần này bạn đã sửa được hẳn so với lần 1.

Câu 5.
- Phân tích URL dùng phương thức htttps nên sẽ có địa chỉ nguồn là ip của máy:443
- DNS phân giải tên miền thành IP đích
- NAT translation từ IP nguồn trong mạng nội bộ sang internet với IP nguồn là IP của router: port của NAT tự gán.
- Kết TCP bằng bắt tay 3 bước với sever IP đích
- Sao khi bắt tay giữ kết nối keep-alive
- Gửi request tới sever bảo là hiển thị trang chủ
- Sever response lại rồi reder ra UI.
> 💬 🟡 **Đúng một phần (0,5).** Hai lỗi sai và ba chỗ thiếu.
> - ❌ **443 là port của server (port đích),** không phải "địa chỉ nguồn của máy". Port nguồn của máy bạn là một ephemeral port ngẫu nhiên, ví dụ 52341. Đích là `IP-shopee:443`.
> - ❌ **Keep-alive không nằm sau bắt tay.** Nó là bước **cuối**: dùng xong **giữ** kết nối lại cho request sau, khỏi phải bắt tay lại.
> - ⚠️ **Thiếu TLS handshake, lần thứ hai.** Đề có ghi "bắt buộc". `https` = HTTP + TLS, nên sau TCP **phải** có TLS handshake rồi mới gửi HTTP.
> - ⚠️ **Thiếu giao thức + port của DNS:** thường là **UDP, port 53**.
> - ⚠️ **Thiếu đóng gói và router.** NAT bạn có nhắc, nhưng chưa nói dữ liệu được đóng gói qua các tầng thành packet và đi qua nhiều router. Đặt NAT thành một bước riêng trước TCP cũng chưa chuẩn: NAT xảy ra với **mọi packet** đi ra, kể cả packet SYN.
>
> Thứ tự chuẩn:
> 1. Phân tích URL: `https`, đích port 443.
> 2. DNS, dùng UDP port 53.
> 3. TCP 3-way handshake.
> 4. TLS handshake.
> 5. Gửi HTTP request: dữ liệu đóng gói thành packet, qua NAT, rồi qua các router.
> 6. Server trả HTTP response.
> 7. Trình duyệt render và tải thêm CSS/JS/ảnh.
> 8. Kết nối được giữ lại (keep-alive).

Câu 6.
a) UDP: nếu dùng TCP sẽ mất tính chất realtime của livestream
b) TCP: nếu dùng UDP lỡ trong quá trình tải lỗi sẽ làm hỏng cả gói tin
c) UDP: lại là tính chất nhanh của UDP
d) TCP: điểm bảo lệnh phải đến sever đầy đủ và chính xác do liên quan tới tiền bạc.
> 💬 🟡 **Đúng một phần (0,5).**
> - Chọn giao thức a/b/c/d đều đúng ✅.
> - c) Lý do nên cụ thể hơn: vị trí cũ đã **lỗi thời** sau 1/60 giây, gửi lại cũng vô ích. Chỉ cần vị trí **mới nhất**.
> - ❌ **Bỏ sót câu hỏi phụ**: "vì sao cơ chế gửi lại của TCP làm hỏng video call?". Đáp án: TCP giao dữ liệu **đúng thứ tự**. Một packet mất thì mọi packet phía sau dù đã tới vẫn **phải chờ** packet đó được gửi lại (ít nhất thêm 1 RTT). Hình bị **đứng** trong lúc chờ. Mà khung hình tới trễ thì cũng vô dụng rồi.

Câu 7.
a) Tăng lên do mỗi lần kết nối lại sẽ phải bắt tay 3 bước từ đó làm cho RTT tăng lên
b) c) Không biết
> 💬 🟡 **Đúng một phần (0,3): a) đúng, b) và c) sai.**
> - a) ✅ Ý đúng. Chỉ sửa cách nói: RTT **không tăng** (RTT là thời gian đi-về của mạng, cố định). Đúng ra là mỗi request **tốn thêm 1 RTT** để bắt tay.
> - b) ❌ Đây là lần thứ hai câu này chưa trả lời được. Đọc kỹ mục **B3** trong `lesson.md`. Chuỗi nhân quả:
>   1. A **chủ động đóng** kết nối, nên phía **A** vào TIME_WAIT.
>   2. Mỗi kết nối TIME_WAIT bị giữ **2 × MSL = 60 giây** trên Linux.
>   3. Trong 60 giây đó, ephemeral port của kết nối bị **giữ chỗ**, không dùng lại được.
>   4. Cứ 2.000 request/giây × 60 giây = 120.000 port cần dùng, trong khi Linux mặc định chỉ có khoảng **28.000 ephemeral port**.
>   5. **Hết port**, dẫn tới lỗi `cannot assign requested address`.
> - c) ❌ Cách sửa:
>   - ✅ **Keep-alive**: giữ kết nối mở cho nhiều request.
>   - ✅ **Connection pool**: giữ sẵn một nhóm kết nối tới B và dùng đi dùng lại.
>   - 🚫 **Không dùng `tcp_tw_recycle`** để "lách" TIME_WAIT: nó gây lỗi với client đứng sau NAT và **đã bị xoá khỏi Linux kernel**.
