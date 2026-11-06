# 1.1 — Internet hoạt động như thế nào (TCP/IP, OSI, TCP vs UDP, Port)

## 🎯 Mục tiêu bài học
Sau bài này bạn phải giải thích được:
1. Dữ liệu đi từ máy bạn tới server như thế nào (packet, router, IP).
2. Mô hình phân tầng **OSI (7 tầng)** và **TCP/IP (4 tầng)**, mỗi tầng làm gì.
3. **Encapsulation** (đóng gói) dữ liệu qua các tầng.
4. **IP address**: IPv4/IPv6, public/private, NAT.
5. **TCP vs UDP**: khác nhau ở đâu, khi nào dùng cái nào, 3-way handshake.
6. **Port** và **socket** là gì.

---

## 1. Vấn đề gốc: làm sao 2 máy tính ở xa nhau nói chuyện được?

Internet = **mạng của các mạng** (inter-network). Hàng tỷ thiết bị, nối với nhau qua vô số router của nhiều nhà mạng khác nhau. Để chúng hiểu nhau cần:

- **Địa chỉ**: biết gửi tới máy nào → **IP address**.
- **Đường đi**: biết chuyển qua đâu → **router** + **routing**.
- **Độ tin cậy**: dữ liệu có thể mất, đến sai thứ tự → **TCP**.
- **Đúng ứng dụng**: một máy chạy nhiều chương trình (browser, Zalo, Spotify) → **port**.
- **Ngôn ngữ chung**: tất cả phải theo cùng quy ước → **protocol** (giao thức).

### Packet switching (chuyển mạch gói)
Dữ liệu **không** đi nguyên khối. Nó bị chia thành các **packet** nhỏ (thường ≤ ~1500 byte, gọi là **MTU**). Mỗi packet tự mang địa chỉ đích và có thể đi **đường khác nhau**, đến **không theo thứ tự**, hoặc **bị mất**. Bên nhận ráp lại.

> Tại sao làm vậy? Vì đường truyền được chia sẻ cho nhiều người cùng lúc, một đường hỏng thì packet đi đường khác → mạng bền vững, tận dụng tốt băng thông. (Ngược với *circuit switching* của điện thoại bàn cũ: giữ riêng 1 đường suốt cuộc gọi.)

```
[Máy bạn] → [Router nhà] → [ISP: Viettel/FPT] → ... nhiều router ... → [Datacenter] → [Server]
             mỗi router chỉ nhìn IP đích và quyết định "hop" kế tiếp
```

Lệnh thử: `traceroute google.com` (Linux/Mac) hoặc `tracert google.com` (Windows) để xem từng hop.

---

## 2. Mô hình phân tầng

Mạng được chia **tầng** (layer) để mỗi tầng chỉ lo 1 việc và không cần biết chi tiết tầng khác. Ví dụ: HTTP không cần quan tâm bạn dùng Wi-Fi hay cáp quang.

### OSI (7 tầng — mô hình lý thuyết, dùng để nói chuyện/giảng dạy)

| Tầng | Tên | Nhiệm vụ | Ví dụ | Đơn vị dữ liệu |
|------|-----|----------|-------|----------------|
| 7 | Application | Giao thức cho ứng dụng | HTTP, DNS, SMTP, FTP | Data |
| 6 | Presentation | Định dạng, mã hoá, nén | TLS (thường xếp vào đây), JPEG, UTF-8 | Data |
| 5 | Session | Quản lý phiên kết nối | (thực tế gộp vào app) | Data |
| 4 | Transport | Truyền giữa **process ↔ process**, dùng port | **TCP, UDP** | **Segment** (TCP) / Datagram (UDP) |
| 3 | Network | Định tuyến giữa **máy ↔ máy** qua nhiều mạng | **IP**, ICMP | **Packet** |
| 2 | Data Link | Truyền trong **cùng 1 mạng LAN**, dùng MAC address | Ethernet, Wi-Fi | **Frame** |
| 1 | Physical | Tín hiệu vật lý | Cáp, sóng radio, quang | Bit |

Mẹo nhớ (từ 1 → 7): **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way.

### TCP/IP (4 tầng — mô hình thực tế internet dùng)

| TCP/IP | Tương ứng OSI | Giao thức |
|--------|---------------|-----------|
| Application | 5, 6, 7 | HTTP, DNS, TLS, SSH |
| Transport | 4 | TCP, UDP |
| Internet | 3 | IP, ICMP |
| Link (Network Access) | 1, 2 | Ethernet, Wi-Fi |

> Khi phỏng vấn nói "L4 load balancer" hay "L7 load balancer" là họ dùng **số tầng OSI**: L4 = nhìn IP + port (TCP/UDP), L7 = nhìn nội dung HTTP (URL, header, cookie). Bạn sẽ gặp lại ở bài 5.6.

---

## 3. Encapsulation — đóng gói qua từng tầng

Khi gửi, mỗi tầng **bọc thêm header** của nó bên ngoài dữ liệu tầng trên. Khi nhận thì **bóc** ngược lại.

```
Application:                          [ HTTP: GET /index.html ... ]
Transport:                 [TCP hdr | HTTP data              ]   ← src port, dst port, seq number
Internet:         [IP hdr | TCP hdr | HTTP data              ]   ← src IP, dst IP, TTL
Link:    [Eth hdr | IP hdr | TCP hdr | HTTP data             | Eth trailer]  ← src MAC, dst MAC
Physical: 0101100101...
```

Điểm quan trọng:
- **MAC address** chỉ có ý nghĩa trong 1 mạng LAN → **thay đổi ở mỗi hop** (router bóc frame cũ, bọc frame mới).
- **IP address** nguồn/đích **giữ nguyên** suốt hành trình (trừ khi đi qua NAT).
- **Router** làm việc ở tầng 3 (đọc IP). **Switch** làm việc ở tầng 2 (đọc MAC).
- Mỗi IP packet có **TTL** (Time To Live): mỗi router giảm 1, về 0 thì bỏ packet → chống packet chạy vòng vô hạn. `traceroute` lợi dụng TTL để dò từng hop.

### 3.1 ARP — từ IP tìm ra MAC
Tầng 3 biết IP của hop kế tiếp, nhưng tầng 2 cần **MAC** mới gửi được frame. **ARP (Address Resolution Protocol, RFC 826)** giải quyết việc này:
1. Máy **broadcast** cho cả LAN: "Ai giữ IP `192.168.1.1`? Cho tôi MAC."
2. Đúng máy đó trả lời (unicast): "Tôi, MAC là `aa:bb:cc:dd:ee:ff`."
3. Kết quả lưu vào **ARP cache** để không phải hỏi lại mỗi packet (xem bằng `ip neigh` hoặc `arp -a`).

Lưu ý: nếu đích nằm **ngoài** mạng LAN, máy không hỏi MAC của server mà hỏi MAC của **default gateway** (router nhà). IPv6 không dùng ARP mà dùng **NDP** (Neighbor Discovery Protocol).

### 3.2 Router quyết định đường đi thế nào?
- Mỗi router có **routing table**: "mạng đích X → gửi tới next-hop Y qua cổng Z". Packet tới, router tra IP đích theo **longest prefix match** (khớp dải mạng cụ thể nhất), không khớp gì thì đi **default route** (`0.0.0.0/0`).
- Routing table được xây từ **static route** (cấu hình tay) hoặc **routing protocol** tự động:
  - **OSPF**: định tuyến *bên trong* 1 tổ chức/nhà mạng.
  - **BGP**: định tuyến *giữa* các **Autonomous System (AS)**, tức mạng của các nhà mạng/công ty lớn (Viettel, Google, Cloudflare...). BGP là "bản đồ" của cả internet; cấu hình BGP sai từng làm sập Facebook toàn cầu (10/2021).
- Thử: `ip route` (Linux) / `route print` (Windows) để xem routing table của máy bạn.

---

## 4. IP Address

### IPv4
- 32 bit, viết dạng 4 số 0–255: `142.250.199.78`. Tổng ~4,3 tỷ địa chỉ → **đã cạn**.
- **CIDR**: `192.168.1.0/24` nghĩa là 24 bit đầu là phần mạng, 8 bit còn lại cho host → 256 địa chỉ (254 dùng được).

### IPv6
- 128 bit, viết hex: `2001:0db8:85a3::8a2e:0370:7334`. Số lượng gần như vô hạn.
- Sinh ra vì IPv4 cạn. Hiện 2 cái chạy song song (**dual-stack**).
- Mức độ phổ biến: theo số liệu của Google, ngày 28/03/2026 lần đầu tiên hơn 50% người dùng truy cập Google qua IPv6 (50,1%). Các nguồn khác (Cloudflare, APNIC) đo thấp hơn, khoảng 40–43%. Tóm lại: IPv4 vẫn chưa thể bỏ.

### Public vs Private IP
- **Private** (chỉ dùng trong mạng nội bộ, không route được trên internet):
  - `10.0.0.0/8`
  - `172.16.0.0/12`
  - `192.168.0.0/16`
- `127.0.0.1` (**localhost / loopback**): chính máy mình.
- **Public**: địa chỉ duy nhất trên internet, do ISP cấp.

### NAT (Network Address Translation)
Cả nhà bạn (laptop, điện thoại, TV) có IP private `192.168.1.x`, nhưng ra internet chỉ có **1 IP public** của router. Router ghi nhớ bảng:

```
192.168.1.5:52341  ⇄  113.22.10.4:40001  ⇄  server
192.168.1.7:61022  ⇄  113.22.10.4:40002  ⇄  server
```
Nó đổi IP:port nguồn khi gửi đi, và đổi ngược khi nhận về. NAT là lý do chính IPv4 "sống sót" tới giờ.

> Liên hệ backend: trong Docker/Kubernetes/cloud (AWS VPC) bạn sẽ gặp lại private IP, CIDR, NAT gateway.

---

## 5. Port & Socket

IP chỉ đưa packet tới đúng **máy**. Nhưng máy đó chạy nhiều chương trình → cần **port** (số 16 bit, 0–65535) để đưa tới đúng **process**.

| Dải port | Tên | Ví dụ |
|----------|-----|-------|
| 0–1023 | Well-known | 22 SSH, 53 DNS, 80 HTTP, 443 HTTPS, 25 SMTP |
| 1024–49151 | Registered | 3306 MySQL, 5432 PostgreSQL, 6379 Redis, 8080 (web dev), 27017 MongoDB |
| 49152–65535 | Ephemeral (dynamic) | OS tự cấp cho phía **client** khi mở kết nối |

**Socket** = điểm cuối của kết nối = `IP + port`.
Một **kết nối TCP** được xác định duy nhất bởi bộ 5 (**5-tuple**):
```
(protocol, src IP, src port, dst IP, dst port)
(TCP, 192.168.1.5, 52341, 142.250.199.78, 443)
```
→ Vì vậy 1 server lắng nghe port 443 vẫn phục vụ được hàng nghìn client cùng lúc: mỗi kết nối khác nhau ở IP/port phía client.

Thử: `ss -tuln` (Linux) / `netstat -an` để xem các port đang mở trên máy.

---

## 6. TCP vs UDP (tầng Transport)

### TCP — Transmission Control Protocol
**Connection-oriented, reliable, ordered.** Đảm bảo:
- **Không mất**: mỗi segment có **sequence number**; bên nhận gửi **ACK**; không nhận được ACK sau thời gian timeout → **gửi lại** (retransmission).
- **Đúng thứ tự**: sắp lại theo sequence number trước khi đưa lên ứng dụng.
- **Không trùng**: loại bỏ segment lặp.
- **Flow control**: bên nhận báo "window size" → bên gửi không gửi nhanh hơn khả năng nhận.
- **Congestion control**: tự giảm tốc khi mạng nghẽn (slow start, ...).
- Dữ liệu là **byte stream** (dòng byte liên tục, không có ranh giới message → ứng dụng tự phân tách, ví dụ HTTP dùng `Content-Length`).

#### 3-way handshake (mở kết nối)
```
Client                          Server
  | ---- SYN (seq=x) ----------> |   "Tôi muốn kết nối, số bắt đầu của tôi là x"
  | <--- SYN-ACK (seq=y,ack=x+1) |   "OK, tôi nhận x, số bắt đầu của tôi là y"
  | ---- ACK (ack=y+1) --------> |   "Tôi nhận y"
  |       ===== kết nối đã thiết lập, bắt đầu gửi data =====
```
Tại sao **3** mà không phải 2? Vì **cả hai phía** đều phải gửi số sequence của mình **và** xác nhận đã nhận số của bên kia. Với 2 bước, server không biết client đã nhận được SYN-ACK hay chưa (và không chống được SYN cũ bị trễ trong mạng).

#### 4-way termination (đóng kết nối)
```
Client → FIN → Server
Client ← ACK ← Server
Client ← FIN ← Server   (server gửi xong phần còn lại rồi mới FIN)
Client → ACK → Server   → client vào trạng thái TIME_WAIT một lúc
```
4 bước vì TCP là **full-duplex**: mỗi chiều đóng độc lập.

#### TIME_WAIT — trạng thái hay gây sự cố trên server
Bên **chủ động đóng** kết nối (gửi FIN trước) phải ở trạng thái **TIME_WAIT** trong **2 × MSL** (Maximum Segment Lifetime; Linux cố định 60 giây). Hai lý do:
1. Nếu ACK cuối bị mất, bên kia gửi lại FIN → mình vẫn còn state để ACK lại.
2. Chờ các packet trễ của kết nối cũ chết hết, tránh bị nhận nhầm vào kết nối mới dùng **cùng 5-tuple**.

Hệ quả thực tế: service tạo kết nối mới liên tục (ví dụ gọi API khác mà không reuse connection) sẽ tích tụ hàng nghìn socket TIME_WAIT → **cạn ephemeral port** (mỗi kết nối tới cùng 1 đích chiếm 1 port nguồn, khoảng 28k port mặc định trên Linux) → lỗi "cannot assign requested address". Cách chữa đúng: **reuse connection** (keep-alive, connection pool), không phải tắt TIME_WAIT. (`tcp_tw_recycle` từng dùng để "chữa" nhưng gây lỗi với client sau NAT và đã bị xoá khỏi Linux kernel.)

Xem: `ss -tan state time-wait | wc -l`

#### SYN flood — tấn công vào handshake
Khi nhận SYN, server phải giữ state cho kết nối "nửa mở" (half-open) chờ ACK. Kẻ tấn công gửi hàng loạt SYN với IP giả và không bao giờ gửi ACK → hàng đợi đầy, client thật không kết nối được (DoS, mô tả trong RFC 4987).
Phòng chống phổ biến: **SYN cookies** — server không lưu state, mà mã hoá thông tin kết nối vào chính sequence number trong SYN-ACK; chỉ khi ACK hợp lệ quay về mới tạo kết nối. Ngoài ra: rate limit, CDN/anti-DDoS (Cloudflare...).

#### Hiệu năng TCP: vì sao kết nối mới luôn chậm
- **Handshake tốn 1 RTT** (round-trip time) trước khi gửi được byte data nào, cộng thêm TLS handshake nếu là HTTPS. Ví dụ New York ↔ London RTT tối thiểu ~56 ms; băng thông lớn bao nhiêu cũng không giảm được, vì giới hạn bởi tốc độ ánh sáng.
- **Slow start**: kết nối mới chưa biết mạng chịu được bao nhiêu, nên bắt đầu với **congestion window (cwnd)** nhỏ (10 segment, ~14 KB theo RFC 6928) rồi **gấp đôi mỗi RTT**. Kết nối mới vì vậy không dùng hết băng thông ngay.
- **Congestion avoidance**: khi thấy mất packet, TCP hiểu là mạng nghẽn và giảm cwnd → biểu đồ throughput hình răng cưa.
- Lượng data đang bay tối đa = min(cwnd, rwnd), trong đó **rwnd** là window bên nhận báo (flow control).
- **Head-of-line (HOL) blocking**: TCP giao data đúng thứ tự, nên 1 packet mất làm **mọi byte phía sau phải chờ** dù đã tới nơi. Đây là lý do HTTP/3 chuyển sang QUIC.
- **TCP Fast Open (TFO)**: cho phép gửi data ngay trong gói SYN ở các lần kết nối lặp lại, nhưng ít được dùng vì cần cả client, server và middlebox hỗ trợ.

➡️ Kết luận quan trọng cho backend: **tái sử dụng kết nối** (HTTP keep-alive, **connection pool** cho database/Redis/HTTP client) và **đặt server gần người dùng** (CDN, bài 5.8) là hai cách hiệu quả nhất để giảm độ trễ.

### UDP — User Datagram Protocol
**Connectionless, unreliable, không thứ tự.** Chỉ "ném" datagram đi:
- Không handshake, không ACK, không gửi lại, không sắp thứ tự.
- Header chỉ 8 byte (TCP tối thiểu 20 byte).
- Giữ **ranh giới message**: gửi 1 datagram thì nhận đúng 1 datagram (nếu tới).
- Nhanh, độ trễ thấp. Ứng dụng tự lo tin cậy nếu cần.

### So sánh

| | TCP | UDP |
|---|---|---|
| Kết nối | Có (handshake) | Không |
| Tin cậy | Có (ACK, retransmit) | Không |
| Thứ tự | Đảm bảo | Không |
| Tốc độ/độ trễ | Chậm hơn | Nhanh hơn |
| Header | 20–60 byte | 8 byte |
| Kiểu dữ liệu | Byte stream | Datagram (message) |
| Flow/congestion control | Có | Không |
| Dùng cho | Web (HTTP/1.1, HTTP/2), email, SSH, DB connection, truyền file | DNS, video call, game online, livestream, VoIP, **QUIC/HTTP/3** |

**Câu hỏi hay:** *Tại sao video call dùng UDP?* — Vì một frame tới trễ thì vô dụng; thà mất vài frame (hình hơi giật) còn hơn chờ gửi lại làm cả cuộc gọi bị đứng.

**HTTP/3 chạy trên UDP?** Đúng. HTTP/3 dùng **QUIC**, giao thức xây trên UDP, tự cài lại tính tin cậy và TLS 1.3 ở tầng user space. Lý do:
- Tránh **head-of-line blocking**: với HTTP/2 trên TCP, nhiều request dùng chung 1 kết nối, 1 packet mất làm **tất cả** request đứng chờ. QUIC đảm bảo tin cậy **riêng cho từng stream**, nên stream 2 mất packet thì stream 1 và 3 vẫn chạy.
- Handshake nhanh hơn: gộp transport và TLS handshake làm một (1 RTT, hoặc 0-RTT khi kết nối lại).
- **Connection migration**: đổi từ Wi-Fi sang 4G không mất kết nối, vì QUIC định danh kết nối bằng connection ID thay vì 5-tuple.
- Tại sao không sửa TCP luôn? Vì TCP nằm trong kernel của mọi hệ điều hành và bị các thiết bị trung gian (firewall, NAT) "đóng băng", gần như không thể triển khai thay đổi. Xây trên UDP thì triển khai được ngay ở tầng ứng dụng.
- Mức phổ biến năm 2026: khoảng 40% website hỗ trợ HTTP/3 (W3Techs), khoảng 21% lưu lượng trên mạng Cloudflare. HTTP/2 vẫn chiếm phần lớn.

Chi tiết ở bài 1.3.

---

## 7. Gộp lại: Điều gì xảy ra khi gõ `https://google.com` và Enter?

Đây là **câu phỏng vấn kinh điển nhất**. Mỗi bước sẽ được đào sâu ở các bài sau:

1. **Parse URL**: protocol `https`, host `google.com`, port mặc định `443`, path `/`.
2. **DNS lookup** (bài 1.2): browser cache → OS cache → router → DNS resolver của ISP → root → `.com` TLD → authoritative server → ra IP, ví dụ `142.250.199.78`. (DNS thường chạy trên **UDP port 53**.)
3. **TCP 3-way handshake** tới `142.250.199.78:443` (hoặc QUIC nếu HTTP/3).
4. **TLS handshake** (bài 1.4): trao đổi certificate, thoả thuận khoá mã hoá.
5. **Gửi HTTP request** (bài 1.3): `GET / HTTP/1.1`, `Host: google.com`, cookie, ...
   → dữ liệu đi xuống các tầng, thành packet, qua NAT ở router nhà, qua nhiều router của ISP...
6. **Server xử lý**: load balancer → web server → app server → database/cache (bài 5.x) → trả về **HTTP response** (status, header, HTML).
7. **Browser render**: parse HTML → tải thêm CSS/JS/ảnh (thêm nhiều request nữa) → dựng DOM, CSSOM → layout → paint.
8. Kết nối được **giữ lại (keep-alive)** để dùng cho request tiếp theo.

---

## 💼 Câu hỏi phỏng vấn hay gặp & ý chính cần trả lời

| Câu hỏi | Ý chính |
|---------|---------|
| TCP và UDP khác nhau thế nào? Cho ví dụ dùng mỗi loại. | Bảng so sánh ở mục 6; ví dụ web/DB vs DNS/video call/game. |
| Giải thích 3-way handshake. Tại sao không phải 2? | SYN → SYN-ACK → ACK; cả 2 bên cần trao + xác nhận sequence number. |
| Mô hình OSI có mấy tầng? L4 và L7 khác gì? | 7 tầng; L4 = transport (IP+port), L7 = application (HTTP). |
| Port là gì? Tại sao 1 server port 80 phục vụ được nhiều client? | Định danh process; kết nối xác định bởi 5-tuple. |
| Public IP vs private IP? NAT là gì? | Dải private; NAT đổi IP:port, nhiều máy dùng chung 1 IP public. |
| Router vs switch? | Router tầng 3 (IP, nối các mạng); switch tầng 2 (MAC, trong 1 LAN). |
| Gõ URL vào browser thì chuyện gì xảy ra? | 8 bước ở mục 7. |
| TCP đảm bảo tin cậy bằng cách nào? | Sequence number, ACK, retransmission, checksum, flow & congestion control. |
| Server có quá nhiều kết nối TIME_WAIT, nguyên nhân và cách xử lý? | Mở/đóng kết nối liên tục, bên chủ động đóng giữ TIME_WAIT 2×MSL → cạn port; giải pháp là reuse connection/pool. |
| SYN flood là gì, chống thế nào? | Gửi nhiều SYN không ACK → đầy hàng đợi half-open; SYN cookies, rate limit, anti-DDoS. |
| Vì sao kết nối TCP mới chậm dù băng thông lớn? | Handshake 1 RTT + TLS + slow start; cwnd tăng dần. Giải pháp: keep-alive, pool, CDN. |
| ARP là gì? | Từ IP tìm MAC trong LAN bằng broadcast, lưu ARP cache. |
| Hai process giao tiếp với nhau thế nào? (hay đi kèm câu TCP/UDP) | Cùng máy: pipe, shared memory, Unix socket, message queue; khác máy: socket TCP/UDP qua mạng. |

---

## ⚠️ Hiểu sai phổ biến
- ❌ "UDP không dùng cho việc gì quan trọng" → DNS, HTTP/3 (QUIC) đều chạy trên UDP.
- ❌ "TCP đảm bảo dữ liệu chắc chắn tới nơi" → TCP chỉ đảm bảo *nếu tới thì đúng và đủ thứ tự*, hoặc báo lỗi khi kết nối hỏng. Mạng đứt thì vẫn không tới.
- ❌ "Port là cổng vật lý trên máy" → Port là **số logic** do OS quản lý.
- ❌ "IP xác định 1 người dùng duy nhất" → Qua NAT, cả công ty có thể chung 1 IP public (quan trọng khi làm **rate limiting theo IP**, bài 5.9).
- ❌ "MAC address đi xuyên suốt tới server" → MAC chỉ trong 1 LAN, đổi ở mỗi hop.
- ❌ "TCP gửi theo message" → TCP là byte stream; 1 lần `send()` có thể bị chia nhỏ hoặc gộp ở phía nhận.
- ❌ "Băng thông lớn thì nhanh" → Với request nhỏ (đa số API/web), **độ trễ (RTT)** mới là nút thắt, không phải băng thông.
- ❌ "UDP luôn đến sai thứ tự" → UDP chỉ *không đảm bảo* thứ tự; phần lớn vẫn đến đúng thứ tự, nhưng ứng dụng không được dựa vào điều đó.
- ❌ "TIME_WAIT là lỗi cần tắt" → Đó là cơ chế bảo vệ; vấn đề thật là tạo quá nhiều kết nối mới.

---

## 📝 Tóm tắt
- Internet = packet switching: dữ liệu chia packet, mỗi packet được router chuyển theo **IP đích**.
- Phân tầng: **OSI 7 tầng** (lý thuyết) / **TCP/IP 4 tầng** (thực tế). Gửi đi = đóng gói thêm header từng tầng.
- **IP** → tới đúng máy. **Port** → tới đúng process. **5-tuple** → xác định 1 kết nối.
- **Private IP + NAT** cho phép nhiều thiết bị dùng chung 1 IP public.
- **TCP**: tin cậy, đúng thứ tự, có handshake → web, DB, SSH.
  **UDP**: nhanh, không đảm bảo → DNS, realtime media, game, QUIC.

## 🔍 Thực hành nhanh (5 phút)
```bash
ping google.com            # kiểm tra kết nối, xem RTT (dùng ICMP, tầng 3)
traceroute google.com      # xem các hop router trên đường đi
ip addr                    # xem IP private của máy bạn (Windows: ipconfig)
curl ifconfig.me           # xem IP public (sau NAT)
ss -tuln                   # các port đang lắng nghe trên máy
curl -v https://google.com # xem quá trình kết nối + request/response
```

## 📚 Đọc thêm
- Cloudflare Learning Center — "What is the OSI model?", "TCP/IP": https://www.cloudflare.com/learning/
- MDN — How does the Internet work?: https://developer.mozilla.org/en-US/docs/Learn_web_development/Howto/Web_mechanics/How_does_the_Internet_work
- High Performance Browser Networking (Ilya Grigorik, đọc miễn phí) — chương 2 (TCP), 3 (UDP): https://hpbn.co/
- Video: "Computer Networking Full Course" — Kunal Kushwaha (YouTube)

## 🔗 Nguồn tham khảo đã dùng
- High Performance Browser Networking — Building Blocks of TCP: https://hpbn.co/building-blocks-of-tcp/
- RFC 4987 — TCP SYN Flooding Attacks and Common Mitigations: https://www.rfc-editor.org/rfc/rfc4987
- Computer Networking: Principles, Protocols and Practice (UCLouvain) — TCP connection release / TIME_WAIT: https://beta.computer-networking.info/
- Baeldung — Closing sockets in TIME_WAIT: https://www.baeldung.com/linux/close-socket-time_wait
- Cloudflare Learning — What is routing?: https://www.cloudflare.com/learning/network-layer/what-is-routing/
- IONOS — What is ARP?: https://www.ionos.com/digitalguide/server/know-how/what-is-arp-address-resolution-in-networks/
- Wikipedia — HTTP/3: https://en.wikipedia.org/wiki/HTTP/3
- Codemia — Head-of-Line Blocking: Why HTTP/3 Switched From TCP to QUIC: https://codemia.io/archive/http-head-of-line-blocking-quic
- InMotion Hosting — What is HTTP/3 (số liệu lưu lượng Cloudflare 2026): https://www.inmotionhosting.com/blog/what-is-http-3/
- Internet Society Pulse — IPv6 reaches majority: https://pulse.internetsociety.org/blog/18-years-later-ipv6-reaches-majority
- The Register — IPv6 50% at Google (04/2026): https://www.theregister.com/2026/04/17/ipv6_50_percent_google/
- Google IPv6 Statistics: https://www.google.com/ipv6/statistics.html
- PracHub — TCP vs UDP (câu phỏng vấn ByteDance/TikTok): https://prachub.com/interview-questions/tcp-vs-udp-key-differences-and-the-applications-that-suit-each

---
➡️ Học xong thì prompt: **"cho tôi câu hỏi ôn 1.1"**
