# Quiz 1.1 — Làm lại lần 1

> Chỉ hỏi lại các ý còn sai hoặc thiếu ở lần 1. Cần đúng **100%** để PASS.
> Trước khi làm, đọc lại `lesson.md` các mục **2.4, 6.1–6.4, 8, B3** và các nhận xét `💬` trong `answers.md`. Đọc xong thì **đóng lại** rồi mới làm.
> Trả lời vào `answers-retry-1.md` hoặc trong chat, rồi prompt **"chấm bài 1.1"**.

---

**Câu 1.** Laptop `192.168.1.5:52341` gửi request ra internet qua router nhà có public IP `113.22.10.4`.

- a) Packet đi ra internet mang địa chỉ nguồn là gì? (đưa một ví dụ có cả IP và port)
- b) Server trả lời về địa chỉ nào?
- c) Router dựa vào **thông tin gì** và **tra ở đâu** để chuyển phản hồi đó về đúng laptop?

**Câu 2.** Một bạn nói: *"Bắt tay 2 bước là đủ: client gửi SYN, server trả SYN-ACK là xong."* Hãy chỉ ra **bên nào** sẽ không chắc chắn **điều gì** nếu bỏ bước thứ 3.

**Câu 3.** Khi đóng kết nối, client gửi FIN và server trả ACK. Ngay sau đó:

- A. Kết nối đóng hoàn toàn, không ai gửi gì được nữa
- B. Client không gửi dữ liệu được nữa, nhưng server vẫn có thể gửi tiếp dữ liệu cho client
- C. Server không gửi được nữa, nhưng client vẫn gửi được
- D. Server bắt buộc gửi FIN ngay trong cùng packet với ACK

Chọn một đáp án và giải thích một câu: điều này liên quan gì tới việc đóng kết nối cần 4 bước?

**Câu 4.** Ghép mỗi vấn đề với cơ chế TCP giải quyết nó:

| Vấn đề                                                | Cơ chế                |
| -------------------------------------------------------- | ----------------------- |
| (1) Một packet bị lật vài bit trên đường truyền | A. Flow control         |
| (2) Packet số 5 tới trước packet số 4               | B. Congestion control   |
| (3) Một packet bị mất hẳn                            | C. Checksum             |
| (4) Bên nhận xử lý chậm, bộ đệm sắp đầy       | D. Sequence number      |
| (5) Mạng đang nghẽn, packet mất nhiều               | E. ACK + retransmission |

**Câu 5.** Kể đủ các bước khi gõ `https://shopee.vn` và Enter, **từ lúc phân tích URL đến khi kết nối được giữ lại**. Bắt buộc có: port, DNS (giao thức + port), TCP, **TLS**, HTTP request/response, đường đi của packet (đóng gói, NAT, router), render, keep-alive.

**Câu 6.** Với mỗi ứng dụng sau, chọn **TCP** hay **UDP** và giải thích một câu:

- a) Livestream bóng đá trực tiếp
- b) Tải file cài đặt 2 GB
- c) Game bắn súng online gửi vị trí người chơi 60 lần/giây
- d) Gửi lệnh chuyển tiền

Rồi giải thích: vì sao **cơ chế gửi lại của TCP** lại làm hỏng trải nghiệm ở video call?

**Câu 7.** Service A gọi service B. Mỗi request tới A, A lại **mở một kết nối TCP mới** tới B rồi **đóng** ngay sau khi xong. Tải tăng lên 2.000 request/giây thì:

- a) Mỗi request bị chậm thêm vì đâu? (tính theo RTT)
- b) Sau một lúc A báo lỗi `cannot assign requested address`. Giải thích nguyên nhân, nhắc tới: bên nào vào TIME_WAIT, giữ bao lâu, và tài nguyên nào bị cạn (kèm con số mặc định trên Linux).
- c) Đưa ra **2 kỹ thuật** sửa tận gốc, và **1 cách "chữa"** không được dùng (kèm lý do).
