# Quiz 1.1 — Làm lại lần 4

> Chỉ còn 1 câu. Đúng là **PASS** bài 1.1.
> Đọc nhận xét `💬` ở câu 3a trong `answers-retry-3.md` trước khi làm.

---

**Câu 1.** Bạn vừa tải xong trang `https://github.com`. Server đã gửi xong HTTP response.

- a) **Đúng lúc này**, keep-alive quyết định điều gì với kết nối TCP/TLS?
- Quyết định giữ kết nối với sever.
- b) 2 giây sau bạn mở `/explore`. Điền bảng: mỗi bước có phải làm lại không, và **nhờ cái gì** mà được bỏ qua (nếu bỏ qua được)?

| Bước                             | Làm lại? (Có/Không) | Bỏ qua được là nhờ gì |
| ---------------------------------- | ----------------------- | ---------------------------- |
| DNS                                | Không                  | keep-alive                   |
| TCP 3-way handshake                | Không                  | keep-alive                   |
| TLS handshake                      | Không                  | keep-alive                   |
| Gửi HTTP request / nhận response | Có                     |                              |

---

> 💬 **Chấm (2026-11-09): ✅ Đúng — 100% — PASS bài 1.1.**
> - a) ✅ Giữ kết nối, không đóng sau response. Đó chính là keep-alive.
> - b) ✅ TCP và TLS bỏ qua nhờ keep-alive. HTTP request/response vẫn phải làm.
> - DNS = "keep-alive": ✅ **chấp nhận, và mình phải đính chính nhận xét lần trước.** Khi trình duyệt dùng lại kết nối có sẵn, nó **không cần biết IP nữa** nên không tra DNS. Vì vậy câu trả lời của bạn đúng cho tình huống này. Lần trước mình nói "không phải nhờ keep-alive" là **quá cứng**. Câu đầy đủ nên là: *bỏ qua nhờ keep-alive (kết nối đã có); nếu kết nối đã bị đóng thì vẫn bỏ qua được nhờ **DNS cache***. Hai cơ chế này độc lập: keep-alive giữ **kết nối**, DNS cache giữ **IP**.
