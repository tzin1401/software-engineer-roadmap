# 1.1 — Internet hoạt động như thế nào

> **Cần biết trước:** không có, đây là bài đầu tiên.
> **Thời lượng gợi ý:** 2–3 buổi. Phần A đọc kỹ; phần B (đào sâu cho phỏng vấn) đọc sau khi đã nắm phần A.

## 🎯 Mục tiêu
Sau bài này bạn giải thích được:
1. Dữ liệu đi từ máy bạn tới một server ở xa như thế nào.
2. **IP address** và **port** là gì, khác nhau ra sao.
3. Vì sao mạng được chia thành **các tầng** (OSI, TCP/IP).
4. **TCP** và **UDP** khác nhau thế nào, khi nào dùng cái nào.
5. Điều gì xảy ra khi bạn gõ một URL và nhấn Enter (bản tổng quan).

---

# PHẦN A — NỀN TẢNG

## 0. Bức tranh lớn: Internet giống hệ thống bưu điện

Tưởng tượng bạn gửi một **cuốn sách dày** cho một người bạn ở nước ngoài qua bưu điện:

| Đời thường | Trong mạng máy tính | Học ở mục |
|---|---|---|
| Cuốn sách quá dày, phải xé thành nhiều phong bì nhỏ | Dữ liệu bị chia thành **packet** | 1 |
| Địa chỉ nhà người nhận | **IP address** (địa chỉ của máy) | 2 |
| Các bưu cục chuyển tiếp phong bì | **Router** | 3 |
| Tên người nhận trong căn nhà đó | **Port** (chương trình nào trên máy nhận) | 4 |
| Phong bì lồng trong phong bì, mỗi lớp một thông tin | **Các tầng** và **đóng gói** | 5 |
| Thư bảo đảm (có ký nhận, mất thì gửi lại) | **TCP** | 6 |
| Bưu thiếp (gửi đi là xong, không ai xác nhận) | **UDP** | 7 |

```
[Máy bạn] → [Router nhà] → [Nhà mạng: Viettel/FPT] → ... nhiều router ... → [Datacenter] → [Server]
```

⚠️ Chỗ ví dụ này **không** đúng: bưu điện chuyển một phong bì mất vài ngày, còn packet đi nửa vòng Trái Đất chỉ mất khoảng 100–200 ms. Và các phong bì của cùng một cuốn sách có thể đi **những đường khác nhau**.

Giờ đi từng mảnh một.

---

## 1. Packet — vì sao dữ liệu bị chia nhỏ

**Vấn đề:** đường truyền mạng được **hàng triệu người dùng chung**. Nếu một người gửi file 1 GB "nguyên khối", mọi người khác phải chờ.

**Cách giải quyết:** chia dữ liệu thành các mảnh nhỏ gọi là **packet** (gói tin), mỗi gói thường tối đa khoảng **1500 byte**. Con số giới hạn này gọi là **MTU** (Maximum Transmission Unit, kích thước tối đa một lần truyền).

Mỗi packet tự mang địa chỉ đích, nên:
- Các packet của nhiều người **xen kẽ nhau** trên cùng một đường truyền. Không ai phải chờ lâu.
- Một đường hỏng thì packet **đi đường khác**. Mạng không sập vì một chỗ hỏng.
- Hệ quả: packet có thể **đến sai thứ tự** hoặc **bị mất**. Ai lo việc này? Xem mục 6.

Cách chia nhỏ rồi gửi như vậy gọi là **packet switching** (chuyển mạch gói). Ngược lại là *circuit switching* của điện thoại bàn ngày xưa: giữ riêng một đường dây suốt cuộc gọi, lãng phí khi hai bên im lặng.

<details><summary>🧠 Tự kiểm tra: Vì sao internet chia dữ liệu thành packet thay vì gửi nguyên khối?</summary>

Để nhiều người dùng chung đường truyền mà không ai phải chờ lâu, và để packet đi đường khác khi một đường bị hỏng. Cái giá phải trả: packet có thể mất hoặc đến sai thứ tự.
</details>

---

## 2. IP address — địa chỉ của máy

**Vấn đề:** hàng tỷ thiết bị, làm sao biết packet phải tới máy nào?

**Cách giải quyết:** mỗi thiết bị trên mạng có một **IP address** (địa chỉ IP). IP là viết tắt của Internet Protocol, giao thức chuyển packet giữa các máy.

### 2.1 IPv4 và IPv6
| | IPv4 | IPv6 |
|---|---|---|
| Độ dài | 32 bit | 128 bit |
| Ví dụ | `142.250.199.78` (4 số, mỗi số 0–255) | `2001:db8:85a3::8a2e:370:7334` |
| Số lượng | ~4,3 tỷ, **đã cạn** | gần như vô hạn |

Hiện hai loại **chạy song song** (gọi là **dual-stack**). Ngày 28/03/2026, lần đầu tiên hơn 50% người dùng truy cập Google qua IPv6 (50,1%). Cloudflare và APNIC đo được thấp hơn, khoảng 40–43%. Tóm lại: IPv4 vẫn chưa bỏ được.

### 2.2 Dải địa chỉ (CIDR)
`192.168.1.0/24` nghĩa là: **24 bit đầu** cố định (phần "tên khu phố"), **8 bit cuối** dành cho từng máy (phần "số nhà"). 8 bit cho ra 2⁸ = 256 địa chỉ, dùng được 254 (bỏ địa chỉ đầu và cuối). Cách viết này gọi là **CIDR**.

### 2.3 Public IP và Private IP
- **Private IP**: chỉ dùng **trong mạng nội bộ** (nhà, công ty), không đi ra internet được. Có 3 dải: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.
- **Public IP**: địa chỉ duy nhất trên toàn internet, do nhà mạng cấp.
- `127.0.0.1` (**localhost**): chính máy của bạn.

### 2.4 NAT — cả nhà dùng chung một địa chỉ public
Laptop, điện thoại và TV trong nhà bạn đều có private IP `192.168.1.x`. Nhưng router nhà chỉ có **một public IP**. Router xử lý bằng cách ghi nhớ một bảng:

```
Máy trong nhà          Router đổi thành         Đi ra internet
192.168.1.5:52341  ⇄  113.22.10.4:40001  ⇄  server
192.168.1.7:61022  ⇄  113.22.10.4:40002  ⇄  server
```
Khi gửi đi, router đổi địa chỉ nguồn thành địa chỉ của nó. Khi phản hồi quay về, router tra bảng để đổi ngược lại. Cơ chế này gọi là **NAT** (Network Address Translation, dịch địa chỉ mạng). NAT là lý do chính giúp IPv4 còn dùng được tới nay. (Con số sau dấu `:` là port, giải thích ở mục 4.)

> Gặp lại sau: Docker (bài 14.2) và cloud cũng dùng private IP, CIDR và NAT.

<details><summary>🧠 Tự kiểm tra: Hai người ở hai công ty khác nhau có cùng IP 192.168.1.10. Có bị xung đột không?</summary>

Không. Đó là private IP, chỉ có ý nghĩa bên trong mạng của mỗi công ty. Ra internet, mỗi công ty dùng public IP riêng của mình nhờ NAT.
</details>

---

## 3. Router — người chuyển thư

**Vấn đề:** biết địa chỉ rồi, nhưng làm sao packet tìm được đường qua hàng nghìn mạng?

**Cách giải quyết:** **router** là thiết bị nối các mạng với nhau. Mỗi router **không cần biết cả hành trình**. Nó chỉ trả lời một câu: "packet này nên đưa cho router kế tiếp nào?". Mỗi bước chuyển như vậy gọi là một **hop**.

- Router tra **routing table** (bảng định tuyến): "mạng đích X → chuyển cho router Y".
- Có nhiều dòng khớp thì chọn dòng **khớp cụ thể nhất** (dải mạng dài nhất, gọi là *longest prefix match*). Không dòng nào khớp thì đi **default route** (đường mặc định).
- Bảng được xây tự động bằng các giao thức định tuyến:
  - **OSPF**: định tuyến *bên trong* một nhà mạng hoặc công ty.
  - **BGP**: định tuyến *giữa* các mạng lớn (Viettel, Google, Cloudflare…). Mỗi mạng lớn như vậy gọi là một **Autonomous System (AS)**. BGP chính là "bản đồ" của cả internet. Tháng 10/2021, một cấu hình BGP sai đã làm Facebook biến mất khỏi internet khoảng 6 tiếng.

**TTL (Time To Live):** mỗi packet mang một con số, mỗi router đi qua thì trừ đi 1. Về 0 thì packet bị bỏ. Nhờ vậy packet không chạy vòng mãi khi có lỗi định tuyến.

Thử ngay: `traceroute google.com` (Windows: `tracert google.com`) để xem từng hop.

<details><summary>🧠 Tự kiểm tra: Router có biết toàn bộ đường đi từ máy bạn tới Google không?</summary>

Không. Mỗi router chỉ quyết định bước kế tiếp (next hop) dựa trên routing table của nó.
</details>

---

## 4. Port — tới đúng chương trình

**Vấn đề:** IP chỉ đưa packet tới đúng **máy**. Nhưng trên máy đó đang chạy trình duyệt, Zalo, Spotify… Packet này là của chương trình nào?

**Cách giải quyết:** **port** (cổng) là một **con số** từ 0 đến 65535 mà hệ điều hành gán cho từng chương trình đang dùng mạng. Port không phải cổng vật lý trên máy. Một chương trình đang chạy được gọi là **process** (học kỹ ở bài 2.1).

| Dải port | Tên | Ví dụ |
|---|---|---|
| 0–1023 | Well-known (phổ biến) | 22 SSH, 53 DNS, 80 HTTP, 443 HTTPS |
| 1024–49151 | Registered (đã đăng ký) | 3306 MySQL, 5432 PostgreSQL, 6379 Redis, 8080 web dev |
| 49152–65535 | Ephemeral (tạm thời) | Hệ điều hành tự cấp cho phía **client** mỗi lần mở kết nối |

- **Socket** = một đầu của kết nối = `IP + port`, ví dụ `142.250.199.78:443`.
- Một kết nối được xác định bởi **5 thông tin** (gọi là **5-tuple**):
  ```
  (giao thức, IP nguồn, port nguồn, IP đích, port đích)
  (TCP,       192.168.1.5, 52341,   142.250.199.78, 443)
  ```

→ Vì vậy **một server chỉ lắng nghe port 443 vẫn phục vụ được hàng nghìn client cùng lúc**: mỗi kết nối khác nhau ở IP hoặc port phía client.

Thử ngay: `ss -tuln` (Linux) hoặc `netstat -an` để xem các port đang mở trên máy bạn.

<details><summary>🧠 Tự kiểm tra: IP và port khác nhau thế nào?</summary>

IP xác định **máy nào**. Port xác định **chương trình nào** trên máy đó. Giống địa chỉ nhà và tên người nhận.
</details>

---

## 5. Các tầng mạng và đóng gói

**Vấn đề:** gửi một trang web phải lo cùng lúc rất nhiều việc: tín hiệu điện hay sóng Wi-Fi, chuyển trong mạng nội bộ, tìm đường qua internet, tới đúng chương trình, đúng định dạng dữ liệu. Nếu một phần mềm làm hết thì cực kỳ phức tạp.

**Cách giải quyết:** chia ra **các tầng** (layer). Mỗi tầng chỉ lo **một việc** và không cần biết tầng khác làm thế nào. Ví dụ trình duyệt gửi HTTP mà không cần biết bạn đang dùng Wi-Fi hay cáp quang.

### 5.1 Mô hình TCP/IP (4 tầng, internet dùng thực tế)
| Tầng | Lo việc gì | Ví dụ | Đã học ở |
|---|---|---|---|
| 4. Application | Dữ liệu của ứng dụng | HTTP, DNS, SSH | (bài 1.2, 1.3) |
| 3. Transport | Tới đúng **chương trình**, có tin cậy hay không | **TCP, UDP** (dùng port) | mục 4, 6, 7 |
| 2. Internet | Tới đúng **máy**, qua nhiều mạng | **IP** | mục 2, 3 |
| 1. Link | Truyền trong **cùng một mạng nội bộ** | Ethernet, Wi-Fi (dùng MAC) | mục 5.3 |

### 5.2 Mô hình OSI (7 tầng, dùng để nói chuyện và giảng dạy)
OSI chia nhỏ hơn: tầng Application của TCP/IP được tách thành 3 tầng (7 Application, 6 Presentation, 5 Session), tầng Link tách thành 2 (2 Data Link, 1 Physical). Ba tầng quan trọng nhất cần nhớ số:
- **L3 = Network** (IP)
- **L4 = Transport** (TCP/UDP)
- **L7 = Application** (HTTP)

Mẹo nhớ từ tầng 1 lên 7: **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way (Physical, Data link, Network, Transport, Session, Presentation, Application).

> Khi người ta nói "**L4 load balancer**" và "**L7 load balancer**" là họ dùng số tầng OSI: L4 chỉ nhìn IP + port, L7 đọc được nội dung HTTP. Học kỹ ở bài 6.4.

### 5.3 Đóng gói (encapsulation) — phong bì lồng phong bì
Khi gửi, mỗi tầng **bọc thêm một phần đầu** (gọi là **header**) chứa thông tin của tầng đó. Khi nhận, các tầng **bóc** ra theo thứ tự ngược lại.

```
Application:                         [ HTTP: GET /index.html ]
Transport:                [TCP hdr | HTTP data ]      ← port nguồn, port đích
Internet:        [IP hdr | TCP hdr | HTTP data ]      ← IP nguồn, IP đích, TTL
Link:  [Eth hdr | IP hdr | TCP hdr | HTTP data ]      ← MAC nguồn, MAC đích
Physical: 0101100101... (tín hiệu điện/sóng)
```

Tầng Link dùng **MAC address**: địa chỉ phần cứng của card mạng, ví dụ `aa:bb:cc:dd:ee:ff`. Khác biệt quan trọng:

| | IP address | MAC address |
|---|---|---|
| Phạm vi | Toàn internet | Chỉ trong **một** mạng nội bộ |
| Trên đường đi | **Giữ nguyên** từ đầu tới cuối (trừ khi qua NAT) | **Đổi ở mỗi hop** (router bóc lớp cũ, bọc lớp mới) |
| Thiết bị đọc | **Router** (tầng 3) | **Switch** (tầng 2, nối các máy trong cùng mạng) |

**ARP — từ IP tìm ra MAC:** máy biết IP của router kế tiếp, nhưng để gửi trong mạng nội bộ thì cần MAC. Máy hét lên cho cả mạng: "Ai giữ IP `192.168.1.1`? Cho tôi MAC." Đúng máy đó trả lời. Kết quả được lưu vào **ARP cache** để lần sau khỏi hỏi lại. Cơ chế này gọi là **ARP** (Address Resolution Protocol). Nếu đích nằm ngoài mạng nội bộ, máy hỏi MAC của **router nhà** (gọi là default gateway), không phải MAC của server. IPv6 không dùng ARP mà dùng NDP.

<details><summary>🧠 Tự kiểm tra: Packet từ máy bạn tới Google đi qua 10 router. IP đích và MAC đích có thay đổi trên đường không?</summary>

IP đích giữ nguyên suốt đường đi. MAC đích đổi ở mỗi hop, vì MAC chỉ có ý nghĩa trong từng mạng nội bộ.
</details>

---

## 6. TCP — thư bảo đảm

Nhắc lại mục 1: packet có thể **mất** hoặc **đến sai thứ tự**. Với trang web, file hay giao dịch ngân hàng thì không chấp nhận được. **TCP** (Transmission Control Protocol) giải quyết việc này.

### 6.1 TCP đảm bảo tin cậy bằng cách nào
| Cơ chế | Nó làm gì |
|---|---|
| **Sequence number** (số thứ tự) | Đánh số từng byte, để bên nhận sắp lại đúng thứ tự và loại bỏ bản trùng |
| **ACK** (acknowledgement, xác nhận) | Bên nhận báo "đã nhận tới byte số N" |
| **Retransmission** (gửi lại) | Chờ một lúc không thấy ACK thì gửi lại |
| **Checksum** | Kiểm tra dữ liệu có bị hỏng trên đường không |
| **Flow control** | Bên nhận báo "tôi còn nhận được bao nhiêu", để bên gửi không gửi dồn quá sức |
| **Congestion control** | Thấy mạng nghẽn (packet mất nhiều) thì tự giảm tốc |

Dữ liệu TCP là một **dòng byte liên tục** (byte stream), không có ranh giới giữa các "tin nhắn". Ứng dụng phải tự biết đâu là hết một tin. Ví dụ HTTP dùng header `Content-Length` (bài 1.3).

### 6.2 Bắt tay 3 bước (3-way handshake) — mở kết nối
Trước khi gửi dữ liệu, hai bên phải "chào hỏi" để thống nhất số thứ tự bắt đầu:

```
Client                              Server
  | ---- SYN (seq=x) --------------> |   "Tôi muốn kết nối, tôi bắt đầu đếm từ x"
  | <--- SYN-ACK (seq=y, ack=x+1) -- |   "Đã nhận x. Tôi bắt đầu đếm từ y"
  | ---- ACK (ack=y+1) ------------> |   "Đã nhận y"
  |     ===== kết nối xong, bắt đầu gửi dữ liệu =====
```

**Vì sao 3 bước mà không phải 2?** Mỗi bên phải (1) gửi số bắt đầu của mình và (2) được bên kia xác nhận đã nhận. Nếu chỉ có 2 bước, server không biết client đã nhận được số `y` hay chưa. Bước thứ 3 còn giúp loại bỏ các SYN cũ bị trễ trong mạng.

### 6.3 Đóng kết nối 4 bước
```
Client → FIN → Server     "Tôi gửi xong rồi"
Client ← ACK ← Server     "Biết rồi"
Client ← FIN ← Server     (server gửi nốt phần còn lại) "Tôi cũng xong"
Client → ACK → Server     "Biết rồi"
```
Cần 4 bước vì TCP **hai chiều độc lập** (full-duplex): mỗi chiều đóng riêng.

### 6.4 Cái giá của TCP
Mở kết nối tốn **một vòng đi-về** (gọi là **RTT**, round-trip time) trước khi gửi được byte dữ liệu đầu tiên. Nếu là HTTPS thì còn thêm bước bắt tay TLS (bài 1.5). Vì vậy backend luôn cố **dùng lại kết nối cũ** thay vì mở mới. Có hai cách: **keep-alive** (bài 1.3) và **connection pool**, tức giữ sẵn một nhóm kết nối tới database (bài 3.5).

<details><summary>🧠 Tự kiểm tra: Kể 3 cơ chế giúp TCP tin cậy.</summary>

Ba trong số: sequence number (đúng thứ tự, không trùng), ACK + retransmission (không mất), checksum (không hỏng), flow control, congestion control.
</details>

---

## 7. UDP — bưu thiếp

**UDP** (User Datagram Protocol) làm gần như **không gì cả**: không bắt tay, không ACK, không gửi lại, không sắp thứ tự. Mỗi lần gửi là một gói độc lập, gọi là **datagram**.

Vậy dùng UDP để làm gì?
- **Nhanh, độ trễ thấp**: không mất thời gian bắt tay. Header chỉ 8 byte (TCP tối thiểu 20 byte).
- **Giữ ranh giới tin nhắn**: gửi một datagram thì bên kia nhận đúng một datagram (nếu tới nơi).
- Ứng dụng **tự quyết** cần tin cậy tới mức nào.

**Ví dụ kinh điển — video call:** một khung hình đến trễ 1 giây thì vô dụng. Thà mất vài khung hình (hình hơi giật) còn hơn chờ gửi lại làm cả cuộc gọi bị đứng.

### TCP vs UDP
| | TCP | UDP |
|---|---|---|
| Kết nối | Có (bắt tay 3 bước) | Không |
| Tin cậy | Có | Không |
| Thứ tự | Đảm bảo | Không đảm bảo |
| Tốc độ | Chậm hơn | Nhanh hơn |
| Header | 20–60 byte | 8 byte |
| Dữ liệu | Dòng byte | Từng datagram |
| Dùng cho | Web (HTTP/1.1, HTTP/2), email, SSH, kết nối database | DNS, video call, game online, livestream, HTTP/3 (qua QUIC) |

<details><summary>🧠 Tự kiểm tra: Game bắn súng online gửi vị trí người chơi 60 lần/giây. Nên dùng TCP hay UDP? Vì sao?</summary>

UDP. Vị trí cũ mất thì không cần gửi lại, vì 1/60 giây sau đã có vị trí mới. Nếu dùng TCP, một packet mất sẽ làm mọi packet phía sau phải chờ, gây giật lag.
</details>

---

## 8. Ghép lại: gõ `https://google.com` và Enter

Đây là **câu phỏng vấn kinh điển nhất**. Giờ bạn đã hiểu được các bước 3 và 5; các bước còn lại sẽ học kỹ ở bài sau:

1. **Phân tích URL**: giao thức `https`, tên miền `google.com`, port mặc định `443`.
2. **DNS** đổi tên miền thành IP, ví dụ `142.250.199.78`. DNS thường chạy trên **UDP port 53**. → bài 1.2
3. **TCP 3-way handshake** tới `142.250.199.78:443`. → mục 6.2
4. **TLS handshake**: thoả thuận mã hoá. → bài 1.5
5. **Gửi HTTP request**. Dữ liệu đi xuống các tầng, được đóng gói thành packet, đi qua NAT ở router nhà rồi qua nhiều router khác. → mục 2–5 và bài 1.3
6. **Server xử lý**: đi qua load balancer, server ứng dụng, database/cache rồi trả về **HTTP response**. → phần 6 (system design)
7. **Trình duyệt hiển thị** trang, tải thêm CSS/JS/ảnh.
8. Kết nối được **giữ lại** cho các request sau.

---

# PHẦN B — ĐÀO SÂU CHO PHỎNG VẤN

Đọc phần này khi đã nắm chắc phần A. Đây là những câu "hỏi thêm" mà người phỏng vấn hay dùng để phân biệt người học vẹt với người hiểu sâu.

## B1. Vì sao kết nối TCP mới luôn chậm, dù mạng rất nhanh?
- **Bắt tay tốn 1 RTT.** Ví dụ New York ↔ London có RTT tối thiểu khoảng 56 ms do giới hạn tốc độ ánh sáng. Băng thông lớn tới đâu cũng không giảm được con số này.
- **Slow start:** kết nối mới chưa biết mạng chịu được bao nhiêu, nên bắt đầu gửi ít (**congestion window** khoảng 10 packet, ~14 KB theo RFC 6928) rồi **gấp đôi sau mỗi RTT**.
- **Congestion avoidance:** thấy packet mất, TCP hiểu là mạng nghẽn và giảm tốc. Biểu đồ tốc độ vì vậy có hình răng cưa.
- Lượng dữ liệu được phép "đang bay" = min(congestion window, cửa sổ bên nhận báo).

→ Kết luận cho backend: **dùng lại kết nối** (keep-alive, connection pool) và **đặt server gần người dùng** (CDN, bài 6.6).

## B2. Head-of-line blocking và vì sao HTTP/3 chuyển sang UDP
- TCP giao dữ liệu **đúng thứ tự**, nên một packet mất làm **mọi byte phía sau phải chờ**, dù chúng đã tới nơi. Hiện tượng này gọi là **head-of-line (HOL) blocking**.
- HTTP/2 cho nhiều request đi chung một kết nối TCP, nên một packet mất làm **tất cả** request đứng chờ.
- **HTTP/3** chạy trên **QUIC**, một giao thức xây trên UDP:
  - Tự làm lại phần tin cậy **riêng cho từng luồng**: luồng 2 mất packet thì luồng 1 và 3 vẫn chạy.
  - Gộp bắt tay kết nối và bắt tay TLS làm một: 1 RTT, hoặc 0 RTT khi kết nối lại.
  - **Đổi mạng không mất kết nối** (Wi-Fi sang 4G), vì QUIC định danh kết nối bằng connection ID thay vì 5-tuple.
- Vì sao không sửa TCP cho xong? Vì TCP nằm trong nhân hệ điều hành và bị các thiết bị trung gian (firewall, NAT) "đóng băng", gần như không thể thay đổi. Xây trên UDP thì triển khai được ngay.
- Năm 2026: khoảng 40% website hỗ trợ HTTP/3 (W3Techs), chiếm khoảng 21% lưu lượng trên Cloudflare. HTTP/2 vẫn chiếm đa số.

## B3. TIME_WAIT — trạng thái hay gây sự cố trên server
- Bên **chủ động đóng** kết nối phải giữ trạng thái **TIME_WAIT** trong 2 × MSL (Maximum Segment Lifetime; Linux cố định 60 giây). Lý do:
  1. ACK cuối có thể bị mất. Khi bên kia gửi lại FIN, mình vẫn còn thông tin để trả lời.
  2. Chờ các packet trễ của kết nối cũ hết hạn, để chúng không bị nhận nhầm vào kết nối mới có cùng 5-tuple.
- **Sự cố thực tế:** service mở kết nối mới liên tục (ví dụ gọi API khác mà không dùng lại kết nối) sẽ tích tụ hàng nghìn socket TIME_WAIT. Kết quả là **hết ephemeral port** (Linux mặc định có khoảng 28 nghìn port), gây lỗi "cannot assign requested address".
- **Cách chữa đúng:** dùng lại kết nối (keep-alive, connection pool), **không phải** tắt TIME_WAIT. Tuỳ chọn `tcp_tw_recycle` từng được dùng để "chữa" nhưng gây lỗi với client sau NAT, và đã bị xoá khỏi Linux kernel.
- Xem: `ss -tan state time-wait | wc -l`

## B4. SYN flood — tấn công vào bước bắt tay
- Khi nhận SYN, server phải nhớ một kết nối "nửa mở" để chờ ACK. Kẻ tấn công gửi hàng loạt SYN với IP giả và không bao giờ gửi ACK. Hàng đợi đầy, người dùng thật không kết nối được. Đây là một kiểu tấn công từ chối dịch vụ (DoS), mô tả trong RFC 4987.
- Chống bằng **SYN cookies**: server không lưu gì cả, mà mã hoá thông tin kết nối vào chính số thứ tự trong SYN-ACK. Chỉ khi ACK hợp lệ quay về mới tạo kết nối. Ngoài ra còn rate limit (bài 6.13) và dịch vụ chống DDoS như Cloudflare.

## B5. TCP Fast Open
Cho phép gửi dữ liệu ngay trong gói SYN ở các lần kết nối lặp lại. Ít được dùng vì cần client, server và các thiết bị trung gian đều hỗ trợ.

---

## 💼 Câu phỏng vấn hay gặp & ý chính cần trả lời

| Câu hỏi | Ý chính |
|---|---|
| TCP và UDP khác nhau thế nào? Cho ví dụ. | Bảng ở mục 7; ví dụ web/DB vs DNS/video call/game |
| Giải thích 3-way handshake. Vì sao không phải 2 bước? | SYN → SYN-ACK → ACK; cả hai bên phải gửi và được xác nhận số thứ tự |
| OSI có mấy tầng? L4 và L7 khác gì? | 7 tầng; L4 = transport (IP + port), L7 = application (HTTP) |
| Port là gì? Vì sao server port 443 phục vụ được nhiều client? | Định danh chương trình; kết nối xác định bởi 5-tuple |
| Public vs private IP? NAT là gì? | 3 dải private; NAT đổi IP:port để nhiều máy dùng chung 1 public IP |
| Router vs switch? | Router tầng 3 (IP, nối các mạng); switch tầng 2 (MAC, trong một mạng) |
| ARP là gì? | Hỏi cả mạng nội bộ để từ IP tìm ra MAC, lưu vào ARP cache |
| TCP đảm bảo tin cậy bằng cách nào? | Sequence number, ACK, retransmission, checksum, flow & congestion control |
| Gõ URL vào trình duyệt thì chuyện gì xảy ra? | 8 bước ở mục 8 |
| Vì sao kết nối TCP mới chậm dù băng thông lớn? | Bắt tay 1 RTT + TLS + slow start → dùng lại kết nối, CDN (B1) |
| Vì sao HTTP/3 dùng UDP? | Tránh HOL blocking của TCP, bắt tay nhanh, đổi mạng không mất kết nối (B2) |
| Server có quá nhiều TIME_WAIT? | Mở/đóng kết nối liên tục → hết port; dùng lại kết nối (B3) |
| SYN flood là gì, chống thế nào? | Nhiều SYN không ACK → đầy hàng đợi; SYN cookies, rate limit (B4) |
| Hai process giao tiếp với nhau thế nào? | Cùng máy: pipe, shared memory, Unix socket; khác máy: socket TCP/UDP |

## ⚠️ Hiểu sai phổ biến
- ❌ "Port là cổng vật lý trên máy" → Port là **con số** do hệ điều hành quản lý.
- ❌ "MAC đi xuyên suốt tới server" → MAC chỉ có ý nghĩa trong một mạng nội bộ, đổi ở mỗi hop.
- ❌ "IP xác định một người dùng duy nhất" → Qua NAT, cả công ty có thể chung một public IP. Điều này quan trọng khi rate limit theo IP (bài 6.13).
- ❌ "UDP không dùng cho việc gì quan trọng" → DNS và HTTP/3 đều chạy trên UDP.
- ❌ "UDP luôn đến sai thứ tự" → UDP chỉ *không đảm bảo* thứ tự. Phần lớn vẫn đến đúng, nhưng ứng dụng không được dựa vào điều đó.
- ❌ "TCP đảm bảo dữ liệu chắc chắn tới nơi" → TCP chỉ đảm bảo *nếu tới thì đủ và đúng thứ tự*, còn mạng đứt thì báo lỗi.
- ❌ "TCP gửi theo từng tin nhắn" → TCP là dòng byte; một lần gửi có thể bị chia nhỏ hoặc gộp lại ở phía nhận.
- ❌ "Băng thông lớn là nhanh" → Với request nhỏ (đa số API), **độ trễ (RTT)** mới là nút thắt.
- ❌ "TIME_WAIT là lỗi cần tắt" → Đó là cơ chế bảo vệ; vấn đề thật là mở quá nhiều kết nối mới.

## 📝 Tóm tắt
- Dữ liệu bị chia thành **packet**. **Router** chuyển từng packet theo **IP đích**, mỗi lần một hop.
- **IP** → đúng máy. **Port** → đúng chương trình. **5-tuple** → xác định một kết nối.
- **Private IP + NAT** cho nhiều thiết bị dùng chung một public IP.
- Mạng chia **tầng**, mỗi tầng bọc thêm header (**đóng gói**). Ba tầng cần nhớ: L3 IP, L4 TCP/UDP, L7 HTTP.
- **TCP** = thư bảo đảm: tin cậy, đúng thứ tự, có bắt tay → web, DB, SSH.
- **UDP** = bưu thiếp: nhanh, không đảm bảo → DNS, video call, game, HTTP/3.

## 🔍 Thực hành nhanh (5 phút)
```bash
ping google.com            # kiểm tra kết nối, xem RTT
traceroute google.com      # xem các hop router trên đường đi
ip addr                    # IP private của máy bạn (Windows: ipconfig)
curl ifconfig.me           # IP public (sau NAT)
ss -tuln                   # các port đang lắng nghe trên máy
ip neigh                   # ARP cache: IP ↔ MAC trong mạng nội bộ
curl -v https://google.com # xem quá trình kết nối và request/response
```

## 📚 Đọc thêm
- Cloudflare Learning Center — "What is the OSI model?", "TCP/IP": https://www.cloudflare.com/learning/
- MDN — How does the Internet work?: https://developer.mozilla.org/en-US/docs/Learn_web_development/Howto/Web_mechanics/How_does_the_Internet_work
- High Performance Browser Networking (Ilya Grigorik, đọc miễn phí) — chương 2 (TCP), 3 (UDP): https://hpbn.co/

## 🔗 Nguồn tham khảo đã dùng
- High Performance Browser Networking — Building Blocks of TCP: https://hpbn.co/building-blocks-of-tcp/
- RFC 4987 — TCP SYN Flooding Attacks and Common Mitigations: https://www.rfc-editor.org/rfc/rfc4987
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
