# Quiz 1.1 — Làm lại lần 2

> Chỉ hỏi lại các ý chưa đúng ở lần làm lại 1. Cần đúng **100%** để PASS.
> Trước khi làm: đọc **mục 6.2, 6.3, 8, B3** trong `lesson.md` và các nhận xét `💬` trong `answers-retry-1.md`. Đọc xong **đóng lại** rồi mới làm.
> Mẹo: câu nào có nhiều ý (a, b, c…) thì trả lời **đủ từng ý**, đừng bỏ sót.
> Trả lời vào `answers-retry-2.md` hoặc trong chat, rồi prompt **"chấm bài 1.1"**.

---

**Câu 1.** Phản hồi từ server về tới router nhà với đích `113.22.10.4:40007`. Bảng NAT có hai dòng `192.168.1.5:52341 ⇄ :40001` và `192.168.1.9:61000 ⇄ :40007`. Router chuyển phản hồi cho máy nào, và nó **nhìn vào trường nào** của packet để quyết định?

**Câu 2.** Điền vào chỗ trống: "Bước 3 của bắt tay TCP tồn tại để ______ biết rằng ______ đã nhận được ______."

**Câu 3.** Server gửi ACK cho FIN của client, rồi vẫn gửi thêm 3 packet dữ liệu trước khi gửi FIN của mình.
- a) Đây là lỗi hay hành vi bình thường của TCP? Vì sao?
- b) Lúc này client còn **gửi** dữ liệu được không? Còn **nhận** được không?

**Câu 4.** Sắp xếp lại các bước sau theo đúng thứ tự khi gõ `https://github.com` rồi Enter, và với **mỗi** bước ghi giao thức/port liên quan (nếu có):
`[TLS handshake]` `[Render trang]` `[DNS]` `[Gửi HTTP request]` `[Phân tích URL]` `[Giữ kết nối lại]` `[TCP 3-way handshake]` `[Nhận HTTP response]`

Sau đó trả lời: packet đi từ máy bạn ra internet đi qua **những gì** (kể 3 thứ)? Port **443** là port của máy bạn hay của server?

**Câu 5.** Gọi video bằng TCP, một packet bị mất. Mô tả chuyện gì xảy ra với **các packet đến sau** packet bị mất, và người dùng thấy gì trên màn hình.

**Câu 6.** Một API gateway mở kết nối mới tới backend cho **mỗi** request rồi đóng ngay. Ở 1.000 request/giây, sau ~30 giây bắt đầu lỗi kết nối.
- a) Bên nào (gateway hay backend) giữ TIME_WAIT? Vì sao là bên đó?
- b) Mỗi TIME_WAIT giữ bao lâu trên Linux?
- c) Tính: sau 30 giây có khoảng bao nhiêu kết nối đang TIME_WAIT? So với số ephemeral port mặc định (khoảng bao nhiêu?) thì chuyện gì xảy ra?
- d) Hai cách sửa tận gốc là gì? Một "mẹo" không được dùng là gì, vì sao?
