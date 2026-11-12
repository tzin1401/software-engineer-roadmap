# Chấm bài 1.2

## Lần 1 — 2026-11-12 — 12/12 = **100%** — ✅ PASS

| Câu | Chọn | Kết quả | Ý được kiểm tra |
|---|---|---|---|
| 1 | C | ✅ | Root chỉ trả referral tới TLD |
| 2 | A | ✅ | Recursive (stub → resolver) vs iterative (resolver → root/TLD/auth) |
| 3 | D | ✅ | MX: priority nhỏ được thử trước |
| 4 | B | ✅ | Không CNAME ở apex; ALIAS/flattening hoặc A record |
| 5 | D | ✅ | DNSSEC = ký, không mã hoá; DoH = mã hoá client ↔ resolver |
| 6 | A | ✅ | Trả lời lớn → cờ TC → hỏi lại qua TCP 53 |
| 7 | C | ✅ | Anycast + BGP cho root server |
| 8 | B | ✅ | Negative caching theo SOA |
| 9 | D | ✅ | Hạ TTL trước ≥ TTL cũ, giữ server cũ |
| 10 | A | ✅ | Giới hạn của round-robin DNS |
| 11 | C | ✅ | (Ôn 1.1) Ephemeral port phía client, 53 phía server, UDP không bắt tay |
| 12 | B | ✅ | (Ôn 1.1) DNS cache vs keep-alive |

**Nhận xét:** đúng hết ngay lần đầu, kể cả 2 câu ôn bài 1.1 (đúng những chỗ từng sai ở 1.1: port 443/ephemeral và DNS cache vs keep-alive).
