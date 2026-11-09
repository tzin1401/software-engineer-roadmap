# Bài làm 1.1 — làm lại lần 3 (2026-11-09)

> **Kết quả: 2,75 / 3 ≈ 92%. Chỉ còn đúng 1 ý nhỏ** về keep-alive (câu 3a). Làm `quiz-retry-4.md` (1 câu) là PASS.
> Nhận xét nằm trong các khối `💬` ngay dưới từng câu.

Câu 1) 

a) 

- half-close ở thời điểm giữa quá trình đóng là khi client đã gửi FIN còn sever chưa.
- TIME_WAIT lúc đã xong cơ chế ngắt kết nối 4 bước.

b) 

Dữ liệu chỉ còn chạy trong half-close còn trong TIME_WAIT thì không. Và dữ liệu chạy từ sever đến client thôi.

c) half-close
> 💬 ✅ **Câu 1 đúng hết.** Bạn đã tách được half-close và TIME_WAIT. 👍

Câu 2)

a) 

- socket nguồn: `192.168.1.5`:40001
- socket đích: `140.82.112.3`:443

`40001`là ephemeral

`443`là cố định của sever

Do sever chỉ cố định nhận dữ liệu ở port 443 cho protocol https

b)

3 chặn

- Các tầng mạng.
- Router ở nhà, nơi thực hiện NAT.
- Các router trong internet.
> 💬 ✅ **Câu 2 đúng.**
> - a) 40001 là một ephemeral port hợp lệ, vì port này do hệ điều hành chọn ngẫu nhiên. Ý "port cố định nằm ở server" đúng. Nói gọn cho phỏng vấn: *client phải biết trước port để gọi tới, nên server lắng nghe ở port cố định, còn client dùng port tạm.*
> - b) Đủ 3 chặng ✅. Còn thiếu vế "đổi thành gì": tại router nhà, NAT đổi nguồn `192.168.1.5:40001` thành **public IP của router + một port do router cấp** (ví dụ `113.22.10.4:40001`). Ý này bạn đã trả lời đúng ở các lần trước (retry-1 câu 1a, retry-2 câu 1), nên không trừ điểm.

Câu 3)

a) Request /explore bỏ qua được những bước:
	1. Phân giải DNS (DNS Resolution): Trình duyệt đã biết địa chỉ IP của github.com từ request trước đó nên không cần hỏi lại DNS Server.
	2. Bắt tay 3 bước TCP (TCP 3-way handshake): Bỏ qua các bước gửi gói tin SYN, SYN-ACK, ACK để thiết lập một đường truyền vật lý mới.
	3. Bắt tay TLS/SSL (TLS Handshake): Bỏ qua bước thương lượng chứng chỉ bảo mật và khởi tạo mã hóa HTTPS cho kênh truyền.

Nằm ở sau bước kết nối TCP/TLS lần đầu tiên để luôn giữ kết nối.
> 💬 🟡 **Câu 3a: Đúng một phần (0,75).**
> - ✅ Bỏ qua **TCP handshake** và **TLS handshake**: đúng, và đây là ý chính.
> - ❌ **Bỏ qua DNS thì không phải nhờ keep-alive**, mà nhờ **DNS cache**: trình duyệt và hệ điều hành nhớ IP đã tra trong một thời gian (TTL của bản ghi DNS, học kỹ ở bài 1.2). Thử nghĩ: tắt keep-alive đi thì request `/explore` vẫn không phải hỏi lại DNS, vì cache vẫn còn. Ngược lại, keep-alive chỉ có tác dụng với **kết nối TCP/TLS**.
> - ⚠️ "Đường truyền **vật lý** mới": kết nối TCP là kết nối **logic**, tức hai bên chỉ thống nhất trạng thái (số thứ tự, cửa sổ…) với nhau. Không có dây hay đường riêng nào được dựng thêm, packet vẫn đi chung đường như mọi packet khác (packet switching, mục 1).
> - 🟡 **Vị trí:** câu "sau TCP/TLS lần đầu" còn mơ hồ. Cách nói chuẩn: keep-alive là **quyết định ở cuối request**. Response xong thì **không đóng** kết nối, để request kế tiếp dùng lại. Nên trong chuỗi bước, nó là bước **8, sau render**.

b) 

Keep-alive và Connection Pooling

Thường dùng cho database là connection pooling do database không giao tiếp qua giao thức HTTP thuần túy của web.
> 💬 ✅ **Câu 3b đúng.** Nói thêm cho chặt: keep-alive là khái niệm của **HTTP**. Driver database dùng giao thức riêng của nó (ví dụ PostgreSQL wire protocol), nên ứng dụng giữ sẵn **connection pool** (ví dụ HikariCP trong Java, học ở bài 3.5/9.4).

