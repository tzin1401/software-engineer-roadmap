# Chấm bài 1.1

## Lần 1 — 2026-11-06 — 7,8/12 ≈ **65%** — Chưa PASS

| Câu | Kết quả | Thiếu / sai |
|---|---|---|
| 1 | ✅ Đúng | |
| 2 | ✅ Đúng | |
| 3 | ✅ Đúng | |
| 4 | ✅ Đúng | |
| 5 | 🟡 Một phần | Chỉ nói chiều đi ra; thiếu bảng NAT + tra port khi phản hồi quay về |
| 6 | ✅ Đúng | (thêm: TTL = 0 → bỏ packet) |
| 7 | 🟡 Một phần | 3 bước: chưa nói rõ server không biết client nhận được seq `y`; 4 bước: sai ý "server ACK rồi đóng" |
| 8 | 🟡 Một phần | Bắt tay không phải cơ chế tin cậy; checksum phát hiện **hỏng**, không phải thiếu |
| 9 | 🟡 Một phần | Thiếu TLS handshake, port 443, đóng gói/NAT/router, render, keep-alive |
| 10 | 🟡 Một phần | Thiếu "TCP gửi lại → gói sau phải chờ"; thiếu khi nào dùng TCP |
| 11 | ❌ Sai | Chi phí 1 RTT bắt tay mỗi kết nối; sửa bằng keep-alive + connection pool |
| 12a | ✅ Đúng | |
| 12b | ❌ Sai | Hết ephemeral port (~28k), không phải "nghẽn server" |
| 12c | ❌ Sai | Dùng lại kết nối; không dùng `tcp_tw_recycle` |

Nhận xét chi tiết từng câu: xem `answers.md`.

**Điểm mạnh:** IP/CIDR/routing/OSI vững (phần trắc nghiệm 4/4), TTL và lý do TIME_WAIT nắm đúng.
**Cần ôn lại:** mục 2.4 (NAT), 6.1–6.4 (TCP), 8 (gõ URL), B3 (TIME_WAIT).
**Làm lại:** `quiz-retry-1.md`.

## Làm lại lần 1 — 2026-11-06 — 3,3/7 ≈ **48%** — Chưa PASS

| Câu | Kết quả | Thiếu / sai |
|---|---|---|
| 1 | 🟡 Một phần | Chưa nói router dựa vào **port đích** để tra bảng NAT |
| 2 | 🟡 Một phần | Chưa nêu rõ **server** không chắc client đã nhận **seq `y`** |
| 3 | ❌ Sai | Chọn C, đúng là B: FIN chỉ đóng một chiều |
| 4 | ✅ Đúng | |
| 5 | 🟡 Một phần | Nhầm 443 là port nguồn; keep-alive sai vị trí; vẫn thiếu TLS, UDP 53, đóng gói/router |
| 6 | 🟡 Một phần | Bỏ sót câu "vì sao retransmission hỏng video call" |
| 7 | 🟡 0,3 | a đúng; b, c chưa trả lời (TIME_WAIT → hết ephemeral port; keep-alive/pool; không dùng tcp_tw_recycle) |

**Tiến bộ:** cơ chế tin cậy TCP (câu 4) đã chắc.
**Lặp lại 2 lần:** TLS trong luồng gõ URL; TIME_WAIT gây hết port. → Bắt buộc đọc lại mục 8 và B3.
**Làm lại:** `quiz-retry-2.md`. Nhận xét chi tiết: `answers-retry-1.md`.

## Làm lại lần 2 — 2026-11-09 — 4,9/6 ≈ **81%** — Chưa PASS

| Câu | Kết quả | Thiếu / sai |
|---|---|---|
| 1 | ✅ Đúng | |
| 2 | ✅ Đúng | |
| 3 | 🟡 Một phần | b đúng; a nhầm half-close với TIME_WAIT |
| 4 | 🟡 Một phần | Có TLS ✅; keep-alive vẫn sai vị trí; thiếu port từng bước; 443 nhầm là port client. (Câu "packet đi qua gì" đề chưa rõ, không trừ điểm) |
| 5 | ✅ Đúng | |
| 6 | 🟡 ≈0,9 | a, b, c đúng; d chưa gọi tên keep-alive + connection pool |

**Tiến bộ:** 48% → 81%. NAT, bắt tay 3 bước, TIME_WAIT/hết port đã chắc.
**Còn lại:** half-close vs TIME_WAIT; vị trí keep-alive; port nào của server. → `quiz-retry-3.md`.

## Làm lại lần 3 — 2026-11-09 — 2,75/3 ≈ **92%** — Chưa PASS

| Câu | Kết quả | Thiếu / sai |
|---|---|---|
| 1 | ✅ Đúng | Half-close vs TIME_WAIT đã chắc |
| 2 | ✅ Đúng | (thiếu "đổi thành gì" ở 2b nhưng đã chứng minh ở các lần trước) |
| 3a | 🟡 Một phần | Gán việc bỏ qua DNS cho keep-alive (đúng ra là DNS cache); vị trí keep-alive còn mơ hồ |
| 3b | ✅ Đúng | |

**Còn lại:** keep-alive bỏ qua đúng những gì và xảy ra lúc nào. → `quiz-retry-4.md` (1 câu).

## Làm lại lần 4 — 2026-11-09 — 1/1 = **100%** — ✅ PASS

| Câu | Kết quả | Ghi chú |
|---|---|---|
| 1a | ✅ Đúng | |
| 1b | ✅ Đúng | DNS bỏ qua nhờ keep-alive là đúng khi dùng lại kết nối; nếu kết nối đã đóng thì nhờ DNS cache (đính chính nhận xét lần 3) |

**Tổng kết bài 1.1:** điểm lần đầu 65%, 5 lần làm, PASS ngày 2026-11-09.
