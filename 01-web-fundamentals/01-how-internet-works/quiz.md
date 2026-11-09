# Quiz 1.1 — Internet hoạt động như thế nào

> **Cách làm:** trả lời trong chat hoặc ghi vào `answers.md` cùng thư mục, rồi prompt **"chấm bài 1.1"**.
> Cần đúng **100%** mới học tiếp. Không mở lại `lesson.md` khi làm — tự nhớ lại giúp nhớ lâu hơn.
> (Bài đầu tiên nên chưa có câu ôn bài cũ.)

---

## Phần 1 — Trắc nghiệm (chọn 1 đáp án)

**Câu 1.** Vì sao dữ liệu được chia thành nhiều **packet** thay vì gửi nguyên một khối?

- A. Để mã hoá dữ liệu an toàn hơn
- B. Để nhiều người dùng chung đường truyền, và mất một phần thì chỉ gửi lại phần đó
- C. Vì TCP bắt buộc mỗi packet đúng 1 KB
- D. Để router biết packet thuộc chương trình nào

**Câu 2.** Dải `10.20.0.0/16` có bao nhiêu địa chỉ IP (tính cả địa chỉ đầu và cuối)?

- A. 256
- B. 4.096
- C. 65.536
- D. 16.777.216

**Câu 3.** Routing table của một router có 3 dòng:

```
0.0.0.0/0        → Router A   (default route)
10.0.0.0/8       → Router B
10.1.2.0/24      → Router C
```

Packet đi tới `10.1.2.55` sẽ được chuyển cho router nào?

- A. Router A
- B. Router B
- C. Router C
- D. Gửi cho cả B và C

**Câu 4.** Thiết bị/khái niệm nào làm việc ở **tầng 3 (Network)** của mô hình OSI?

- A. Switch, dùng MAC address
- B. Router, dùng IP address
- C. Port 443 của HTTPS
- D. Load balancer đọc URL path

---

## Phần 2 — Giải thích ngắn (2–4 câu)

**Câu 5.** Trong nhà bạn có laptop `192.168.1.5` và điện thoại `192.168.1.7`, cùng mở YouTube. Router nhà chỉ có một public IP. Giải thích router làm sao biết phản hồi từ YouTube là của máy nào. Cơ chế này tên gì?

**Câu 6.** **TTL** trong packet IP là gì và nó ngăn chặn vấn đề gì?

**Câu 7.** Vì sao TCP cần bắt tay **3 bước** mà không phải 2? Và vì sao đóng kết nối lại cần **4 bước**?

**Câu 8.** Kể **4 cơ chế** giúp TCP đáng tin cậy, mỗi cơ chế nói một câu nó chống lại vấn đề gì.

---

## Phần 3 — Tình huống / phỏng vấn

**Câu 9.** *(Phỏng vấn kinh điển)* Bạn gõ `https://google.com` rồi nhấn Enter. Kể lại theo thứ tự các bước chính xảy ra cho tới khi trang hiện lên. Với mỗi bước, nói nó dùng giao thức nào (chỉ cần nêu tên những thứ học ở bài sau, không cần giải thích sâu).

**Câu 10.** Bạn làm một app **gọi video**. Đồng nghiệp đề xuất dùng TCP "cho chắc ăn, không mất dữ liệu". Bạn đồng ý hay không? Giải thích, và nói khi nào thì TCP mới là lựa chọn đúng.

**Câu 11.** Server API của bạn ở Mỹ, user ở Việt Nam (RTT ~200 ms). Mỗi request, backend lại mở một kết nối TCP **mới** tới database và tới service khác. Vì sao cách này chậm? Đề xuất cách sửa (gọi tên 2 kỹ thuật).

**Câu 12.** Một server chịu tải cao báo lỗi không mở được kết nối mới ra ngoài. Chạy `ss -tan` thấy hàng chục nghìn kết nối ở trạng thái **TIME_WAIT**.

- a) TIME_WAIT là gì, vì sao TCP cần nó?
- b) Vì sao nhiều TIME_WAIT lại gây lỗi?
- c) Nêu một cách xử lý đúng (và một cách **không** nên dùng).
