# Bài làm 1.1 — làm lại lần 2 (2026-11-09)

> **Kết quả: 4,9 / 6 ≈ 81%. Chưa PASS, nhưng tiến bộ rõ** (48% → 81%). Làm tiếp `quiz-retry-3.md` (3 câu).
> Nhận xét nằm trong các khối `💬` ngay dưới từng câu.

Câu 1. Chuyển phản hồi cho máy 192.168.1.9:61000. Nhìn vào port 40007 để quyết định
> 💬 ✅ **Đúng.** Ý NAT đã chắc.

Câu 2.

- Sever
- Client
- ACK-SYN
> 💬 ✅ **Đúng.** Câu hoàn chỉnh: "để **server** biết rằng **client** đã nhận được **SYN-ACK**, tức là số thứ tự bắt đầu `y` của server." Thường gọi là SYN-ACK chứ không phải ACK-SYN, nhưng ý hoàn toàn đúng.

Câu 3.
a) Đây là hành vi bình thường của TCP. Vì ở phía client sẽ không ngắt kết nối mà có một khoản thời gian chờ để nhận hết mọi file từ phía client được gọi là TIME_WAIT.
b) Lúc này client không còn gửi được nhưng vẫn còn nhận được từ phía sever.
> 💬 🟡 **Đúng một phần (0,5).**
> - b) ✅ Đúng.
> - a) Kết luận "bình thường" là đúng, nhưng **lý do sai**. Bạn đang nhầm hai khái niệm khác nhau:
>
> | | Nửa đóng (half-close) | TIME_WAIT |
> |---|---|---|
> | Xảy ra khi nào | **Giữa** quá trình đóng: client đã gửi FIN, server chưa gửi FIN | **Sau cùng**, khi cả 4 bước đã xong |
> | Ai ở trạng thái đó | Cả hai, kết nối còn mở **một chiều** | Bên **chủ động đóng** (ở đây là client) |
> | Có dữ liệu chạy không | **Có**: server vẫn gửi dữ liệu cho client | **Không**: chỉ chờ 60 giây phòng packet trễ và ACK cuối bị mất |
>
> - Lý do đúng của câu a: *FIN chỉ đóng chiều client → server. Chiều server → client vẫn mở, nên server gửi nốt dữ liệu rồi mới gửi FIN của mình.*

Câu 4.
[Phân tích URL]
[DNS]
[TCP 3-way handshake]
[TLS handshake]
[Giữ kết nối lại]
[Gửi HTTP request]
[Nhận HTTP response]
[Render trang]

Packet ở đây là gì bạn không nói rõ. 443 là port của máy.
> 💬 🟡 **Đúng một phần (0,5).**
> - ✅ Lần này đã có **TLS** và nằm đúng vị trí, sau TCP. Tốt!
> - ❌ **`[Giữ kết nối lại]` vẫn đặt sai, lần thứ hai.** Câu hỏi để kiểm tra: *chưa gửi request nào thì "giữ lại" để làm gì?* Keep-alive nghĩa là **dùng xong** thì **không đóng** kết nối, để request **sau** dùng tiếp. Vì vậy nó là bước **cuối cùng**, sau Render.
> - ❌ **Thiếu giao thức/port cho từng bước** (đề có yêu cầu). Ví dụ: URL → `https` nên port 443; DNS → UDP port 53; TCP và TLS → tới `IP-github:443`; HTTP request/response → chạy bên trong TLS.
> - ❌ **443 là port của server, lần thứ hai bị nhầm.** Server lắng nghe cố định ở 443. Máy bạn dùng một **ephemeral port** ngẫu nhiên, ví dụ 52341. Mẹo nhớ: *port nổi tiếng (80, 443, 53) là của server, vì client cần biết trước để gọi tới.*
> - 📝 Về câu "packet đi qua những gì": bạn nói đúng, đề viết chưa rõ, mình xin lỗi. Ý của đề là **trên đường đi** từ máy bạn ra tới server, packet qua những chặng nào. Đáp án:
>   1. **Các tầng** trong máy, mỗi tầng bọc thêm header (đóng gói).
>   2. **Router nhà**, nơi thực hiện **NAT**.
>   3. **Nhiều router** của nhà mạng và internet, mỗi router quyết định next hop.
>
>   Câu này mình đã hỏi lại rõ hơn trong `quiz-retry-3.md`, và lần này **không trừ điểm** phần đó.

Câu 5.
Các packet kết sau sẽ phải chờ để lấy lại gói tin bị mất từ bên gửi và trên màn hình sẽ đứng lại ở gói tin trước gói tin lỗi và bị lag.
> 💬 ✅ **Đúng.** Hiện tượng này có tên là **head-of-line blocking** (mục B2): một packet mất chặn cả hàng phía sau.

Câu 6.

a) Gateway sẽ giữ TIME_WAIT do bên Gateway là bên chủ động ngắt kết nối.

b) 60s

c) Sau 30s thì sẽ có 30 * 1000 = 30000 kết nối mà trên linux số ephemeral port là 28000 nên là sẽ xảy ra hiện tượng cạn kiệt port là lỗi cannot assign requested address.

d) 2 cách sửa tận gốc:

- Dùng lại kết nối
- d

`tcp_tw_recycle`là 1 mẹo như không dùng được cho internet hiện tại vì ghê lỗi cho các client sau NAT.
> 💬 🟡 **Gần đúng hết (≈0,9).** a, b, c đều ✅. Phép tính ở câu c rất tốt.
> - d) Phần `tcp_tw_recycle` ✅. Còn "2 cách sửa" mới có **1 ý chung** ("dùng lại kết nối"), dòng thứ hai chỉ có chữ "d" (chắc bạn gõ dở). Cần gọi đúng tên **hai kỹ thuật cụ thể** dùng để "dùng lại kết nối":
>   1. **Keep-alive**: một kết nối phục vụ nhiều request liên tiếp.
>   2. **Connection pool**: giữ sẵn một nhóm kết nối, request nào cần thì mượn, xong thì trả lại.
