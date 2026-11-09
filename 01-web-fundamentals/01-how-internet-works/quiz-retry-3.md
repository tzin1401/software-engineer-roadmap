# Quiz 1.1 — Làm lại lần 3

> Chỉ còn 3 ý chưa chắc. Đúng hết là **PASS** bài 1.1.
> Đọc nhận xét `💬` ở câu 3, 4, 6 trong `answers-retry-2.md` trước khi làm.
> Trả lời vào `answers-retry-3.md` hoặc trong chat.

---

**Câu 1.** Phân biệt **half-close** (nửa đóng) và **TIME_WAIT**:
- a) Mỗi cái xảy ra ở thời điểm nào trong quá trình đóng 4 bước?
- b) Trong lúc đó còn dữ liệu chạy không, chạy theo chiều nào?
- c) Client gửi FIN, server ACK rồi gửi tiếp 3 packet dữ liệu. Việc server gửi được dữ liệu là nhờ half-close hay TIME_WAIT?

**Câu 2.** Máy bạn có IP `192.168.1.5`, mở `https://github.com` (IP `140.82.112.3`).
- a) Viết **socket nguồn** và **socket đích** của kết nối TCP (dạng `IP:port`). Port nào là ephemeral, port nào cố định? Vì sao port cố định lại nằm ở phía server?
- b) Packet đầu tiên (SYN) đi từ máy bạn tới server sẽ đi qua **3 chặng** nào? Ở chặng nào địa chỉ nguồn bị đổi, và đổi thành gì?

**Câu 3.** Trình duyệt mở github.com, rồi 2 giây sau bạn bấm sang trang `/explore`.
- a) Nhờ **keep-alive**, request `/explore` **bỏ qua được** những bước nào? Từ đó suy ra keep-alive là bước đứng **ở đâu** trong chuỗi bước gõ URL.
- b) Một backend gọi database 500 lần/giây. Gọi tên **2 kỹ thuật** dùng lại kết nối, và nói kỹ thuật nào thường dùng cho kết nối tới database.
