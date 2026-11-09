# 1.2 — DNS: từ tên miền ra địa chỉ IP

> **Cần biết trước:** 1.1 (IP, port, router/BGP, UDP vs TCP).
> **Thời lượng gợi ý:** 1–2 buổi. Phần A đọc kỹ. Phần B (đào sâu cho phỏng vấn) đọc sau khi đã nắm phần A.

## 🎯 Mục tiêu
Sau bài này bạn giải thích được:
1. DNS giải quyết vấn đề gì, và vì sao nó được chia thành **cây phân cấp** chứ không phải một server khổng lồ.
2. Đi qua từng bước của một lần tra `www.github.com`: resolver, root, TLD, authoritative.
3. Các loại record hay gặp: **A, AAAA, CNAME, MX, NS, TXT, SOA, PTR**, và khi nào dùng cái nào.
4. **TTL** và **caching** hoạt động ra sao, vì sao đổi DNS lại "mất thời gian mới có hiệu lực".
5. Vì sao DNS chạy trên **UDP**, khi nào chuyển sang TCP, và **anycast** giúp DNS nhanh và bền như thế nào.
6. Các rủi ro bảo mật chính (cache poisoning, DNSSEC, DoH/DoT) ở mức đủ trả lời phỏng vấn.

## 🔁 Ôn nhanh kiến thức cần có (bài 1.1)
- **IP address** là địa chỉ của máy. Router chỉ hiểu IP, không hiểu tên như `github.com` (mục 2, 3).
- **UDP** gửi từng datagram, không bắt tay, rẻ và nhanh. **TCP** phải bắt tay 3 bước trước, tốn 1 RTT (mục 6, 7).
- **Port 53** là port well-known của DNS (mục 4).
- **BGP** là "bản đồ" giữa các mạng lớn. Router đưa packet theo đường mà BGP cho là tốt nhất (mục 3).
- Một packet thường tối đa khoảng **1500 byte** (MTU, mục 1).

---

# PHẦN A — NỀN TẢNG

## 0. Bức tranh lớn: DNS giống việc nhờ lễ tân tra số điện thoại

Bạn ở khách sạn, muốn gọi cho "**phòng kỹ thuật, công ty GitHub, ở Mỹ**" nhưng không có số. Bạn nhờ **lễ tân**:

| Đời thường | Trong DNS | Học ở mục |
|---|---|---|
| Tên "phòng kỹ thuật – GitHub – Mỹ" | **Tên miền** `www.github.com` | 1 |
| Lễ tân đi tra hộ bạn từ đầu đến cuối | **Recursive resolver** | 2 |
| Tổng đài quốc tế: "muốn gọi Mỹ thì hỏi tổng đài Mỹ" | **Root server** | 2 |
| Tổng đài Mỹ: "GitHub thì hỏi tổng đài của GitHub" | **TLD server** (`.com`) | 2 |
| Tổng đài riêng của GitHub: "số phòng kỹ thuật là…" | **Authoritative server** | 2 |
| Một dòng trong danh bạ: tên → số | **Record** (A, CNAME, MX…) | 4 |
| Lễ tân ghi lại số vào sổ, "dùng được 1 tiếng" | **Cache** + **TTL** | 5 |

```
                 (1) "www.github.com là IP nào?"
 [Máy bạn] ─────────────────────────────▶ [Recursive resolver]  (lễ tân)
     ▲                                         │   ▲
     │ (8) "140.82.112.3"                      │   │
     └─────────────────────────────────────────┘   │
                                (2)(3) hỏi ──▶ [Root]           ".com thì hỏi a.gtld-servers.net"
                                (4)(5) hỏi ──▶ [TLD .com]       "github.com thì hỏi ns-xxx.awsdns..."
                                (6)(7) hỏi ──▶ [Authoritative]  "www.github.com = 140.82.112.3"
```

⚠️ Chỗ ví dụ này **không** đúng:
- Lễ tân gọi điện mất vài phút. Cả chuỗi DNS chỉ mất khoảng **vài chục đến vài trăm mili-giây**, và phần lớn lần tra **không** phải đi hết chuỗi nhờ cache.
- Chỉ có một tổng đài quốc tế, nhưng "root server" thực tế là **hàng nghìn máy** ở khắp thế giới cùng chung địa chỉ (anycast, mục 7).

Giờ đi từng mảnh một.

---

## 1. Tên miền và cây phân cấp

**Vấn đề:** router chỉ hiểu IP (`140.82.112.3`). Con người thì nhớ tên (`github.com`). Hơn nữa IP của một dịch vụ **có thể đổi** (chuyển server, chuyển cloud), còn tên thì nên giữ nguyên. Cần một hệ thống đổi tên → IP.

**Thử cách ngây thơ:** một file khổng lồ chứa mọi tên → IP, ai cũng tải về. Những năm 1970–80 internet làm đúng như vậy với file `HOSTS.TXT`. Khi internet lớn lên thì không chịu nổi: file quá to, cập nhật liên tục, một nơi quản lý thành nút cổ chai. Dấu vết còn lại là file `/etc/hosts` trên máy bạn ngày nay.

**Cách giải quyết:** **DNS** (Domain Name System, hệ thống tên miền), ra đời năm 1987 (RFC 1034/1035). Ý tưởng chính: **chia tên thành từng phần, mỗi phần do một bên quản lý**.

Đọc tên miền **từ phải sang trái**:

```
        www   .   github   .   com   .
        │         │            │     │
        │         │            │     └── root (gốc, thường được ẩn đi)
        │         │            └──────── TLD (Top-Level Domain): com, org, vn, io…
        │         └───────────────────── Tên miền đã đăng ký (second-level): github
        └─────────────────────────────── Subdomain (tên miền con): www, api, docs…
```

```
                      . (root)
          ┌───────────┼────────────┐
         com          vn           org
       ┌──┴───┐     ┌─┴──┐
    github  google com  gov
     ┌─┴─┐          │
   www  api       vnexpress
```

- Mỗi phần giữa hai dấu chấm gọi là một **label** (nhãn).
- Tên đầy đủ kèm dấu chấm cuối (`www.github.com.`) gọi là **FQDN** (Fully Qualified Domain Name, tên miền đầy đủ). Dấu chấm cuối chính là root.
- **Zone** là phần cây mà một bên chịu trách nhiệm. Ví dụ: tổ chức quản lý `.com` (Verisign) chỉ cần biết "ai quản lý `github.com`", không cần biết `www.github.com` là IP nào. Việc "giao lại" quyền quản lý một nhánh cho bên khác gọi là **delegation** (uỷ quyền).

**Hai vai trò hay bị nhầm:**

| Vai trò | Làm gì | Ví dụ |
|---|---|---|
| **Registrar** (nhà đăng ký tên miền) | Bán/cho thuê tên, ghi vào TLD rằng "`github.com` do nameserver X quản lý" | Namecheap, GoDaddy, Mắt Bão, PA Việt Nam |
| **DNS hosting** (nơi chạy authoritative server) | Lưu và trả lời các record của tên miền | Cloudflare DNS, AWS Route 53, hoặc chính registrar |

Bạn có thể mua tên ở Namecheap nhưng để DNS ở Cloudflare. Hai việc này độc lập.

<details><summary>🧠 Tự kiểm tra: Vì sao DNS không dùng một server trung tâm chứa toàn bộ tên miền?</summary>

Vì quá tải (hàng tỷ lượt tra mỗi giây), một chỗ hỏng là cả internet hỏng, và không thể để một nơi cập nhật mọi thay đổi của mọi tên miền. Chia cây giúp **phân tán** cả tải lẫn quyền quản lý: ai sở hữu nhánh nào thì tự quản nhánh đó.
</details>

---

## 2. Bốn loại server DNS

**Vấn đề:** cây đã chia ra nhiều nơi. Vậy ai đi hỏi, ai trả lời?

| Loại | Nó là ai | Biết gì | Ví dụ |
|---|---|---|---|
| **Stub resolver** | Thư viện nhỏ trong hệ điều hành máy bạn | Không biết gì, chỉ chuyển câu hỏi cho resolver và cache kết quả | `systemd-resolved` trên Linux, DNS client của Windows |
| **Recursive resolver** (hay gọi tắt là **resolver**) | "Lễ tân", đi hỏi hộ từ đầu đến cuối và **cache** kết quả | Tự tìm được mọi tên | Resolver của nhà mạng (Viettel, FPT), `8.8.8.8` (Google), `1.1.1.1` (Cloudflare) |
| **Root server** | Gốc cây | Chỉ biết "TLD `.com`, `.vn`… do ai quản lý" | 13 tên `a.root-servers.net` → `m.root-servers.net` |
| **TLD server** | Quản lý một TLD | Chỉ biết "`github.com` do nameserver nào quản lý" | `a.gtld-servers.net` cho `.com` |
| **Authoritative server** (server có thẩm quyền) | Nơi lưu record thật của tên miền | Câu trả lời **chính thức** cho tên của mình | Nameserver Cloudflare/Route 53 của GitHub |

Về **root server**:
- Có **13 tên** (a → m), do **12 tổ chức** độc lập vận hành (Verisign giữ 2 tên: a và j).
- Nhưng 13 tên đó **không phải 13 máy**. Đầu năm 2026 có khoảng **2.000 máy** (instance) đặt khắp thế giới, chung địa chỉ nhờ anycast (mục 7).
- Con số 13 có từ thời DNS chỉ gửi gói UDP tối đa 512 byte: danh sách địa chỉ của 13 root vừa khít một gói.

<details><summary>🧠 Tự kiểm tra: Root server có biết IP của www.github.com không?</summary>

Không. Root chỉ biết ai quản lý TLD `.com`. TLD `.com` chỉ biết ai quản lý `github.com`. Chỉ **authoritative server** của GitHub mới biết IP của `www.github.com`.
</details>

---

## 3. Một lần tra DNS đi qua những bước nào

**Ví dụ:** bạn gõ `www.github.com`, và **không có cache nào** (trường hợp xấu nhất).

```
Bước  Ai hỏi ai                                  Câu trả lời
 1    Trình duyệt → OS (stub)                    "Chưa có trong cache"
 2    Stub → Recursive resolver (UDP :53)        (resolver bắt đầu đi hỏi)
 3    Resolver → Root                            "Không biết, nhưng .com thì hỏi a.gtld-servers.net"   ← referral
 4    Resolver → TLD .com                        "Không biết, nhưng github.com thì hỏi ns-xxx..."     ← referral
 5    Resolver → Authoritative của github.com    "www.github.com  A  140.82.112.3  (TTL 60)"          ← câu trả lời thật
 6    Resolver → Stub → Trình duyệt              "140.82.112.3". Mỗi tầng lưu cache theo TTL
```

Câu trả lời ở bước 3 và 4 gọi là **referral** (lời giới thiệu): "tôi không biết, nhưng hãy hỏi người này".

**Hai kiểu hỏi:**

| | **Recursive query** (hỏi đệ quy) | **Iterative query** (hỏi lặp) |
|---|---|---|
| Ý nghĩa | "Hãy tìm **đến tận cùng** rồi trả kết quả cho tôi" | "Bạn biết gì thì nói, không biết thì chỉ tôi người khác" |
| Ai hỏi ai | Máy bạn → resolver | Resolver → root, TLD, authoritative |
| Ai làm việc nhiều | Resolver | Resolver tự đi từng bước |

Lý do chia như vậy: root và TLD trả lời **hàng triệu** câu hỏi mỗi giây. Chúng chỉ trả referral ngắn, không bao giờ đi hỏi hộ ai, nên chịu được tải.

**Số liệu thật (ước lượng):**

| Tình huống | Thời gian thường gặp |
|---|---|
| Có sẵn trong cache trình duyệt/OS | ~0 ms |
| Resolver đã cache (rất phổ biến với tên nổi tiếng) | ~5–30 ms (1 RTT tới resolver) |
| Resolver phải đi hết chuỗi | ~50–300 ms (nhiều RTT nối tiếp) |

Thực tế resolver gần như **luôn cache** địa chỉ root và TLD phổ biến, nên ít khi phải hỏi root.

> Ghi chú: để dễ theo dõi, ví dụ trên đơn giản hoá. Thực tế `www.github.com` là **CNAME** trỏ về `github.com` (mục 4), và IP có thể khác tuỳ lúc tra. Dùng `dig` ở phần Thực hành để xem giá trị thật.

> Thử ngay: `dig +trace www.github.com`. Bạn sẽ thấy từng bước root → `.com` → authoritative.

<details><summary>🧠 Tự kiểm tra: Máy bạn gửi recursive hay iterative query tới resolver? Resolver gửi kiểu gì tới root?</summary>

Máy bạn gửi **recursive** ("tìm đến cùng giùm tôi"). Resolver gửi **iterative** tới root/TLD/authoritative, nhận referral rồi tự đi tiếp.
</details>

---

## 4. Các loại record

**Vấn đề:** một tên miền không chỉ cần "IP là gì". Còn cần: email gửi về đâu, ai quản lý tên này, tên này là bí danh của tên khác… Mỗi loại thông tin là một **record type** (loại bản ghi). Mỗi record gồm: **tên – TTL – loại – giá trị**.

```
www.github.com.   60   IN   A   140.82.112.3
└── tên ───────┘  └TTL┘     └loại┘└── giá trị ──┘     (IN = Internet, gần như luôn là IN)
```

### 4.1 Nhóm "tên → địa chỉ"
| Record | Ý nghĩa | Ví dụ |
|---|---|---|
| **A** | Tên → **IPv4** | `github.com. A 140.82.112.3` |
| **AAAA** | Tên → **IPv6** (gọi là "quad-A") | `google.com. AAAA 2404:6800:4005:…` |
| **CNAME** | Tên này là **bí danh** của tên khác, đi hỏi tên kia | `www.shop.vn. CNAME shop.myshopify.com.` |

Một tên có thể có **nhiều** record A. Resolver trả cả danh sách, client thường chọn một cái. Đây là cách phân tải đơn giản nhất (mục 8).

**CNAME** dùng khi bạn trỏ tên của mình sang dịch vụ bên ngoài mà IP của nó có thể đổi. Ví dụ Shopify, GitHub Pages, CDN đổi IP thì bạn không cần sửa gì.

Hai luật của CNAME (hay bị hỏi):
1. Một tên đã có CNAME thì **không được có record nào khác** cùng tên. Lý do: CNAME nghĩa là "mọi thứ về tên này, hỏi tên kia", nên không thể vừa là bí danh vừa có dữ liệu riêng.
2. Vì vậy **không đặt CNAME ở tên gốc** (`shop.vn`, gọi là **apex** hay **root domain**). Tên gốc bắt buộc phải có record SOA và NS (mục 4.3), thường có cả MX, nên mâu thuẫn với luật 1.
   - Cách lách: nhiều nhà cung cấp có **ALIAS / ANAME / CNAME flattening**. Server tự tra tên đích rồi trả về A record, nên client chỉ thấy A bình thường. Đây là tính năng riêng của nhà cung cấp, không phải chuẩn DNS.

### 4.2 Nhóm "dịch vụ khác"
| Record | Ý nghĩa | Ví dụ |
|---|---|---|
| **MX** (Mail eXchange) | Email gửi tới `@tên-này` thì giao cho mail server nào. Có **priority**: số **nhỏ** được ưu tiên trước | `gmail.com. MX 5 gmail-smtp-in.l.google.com.` |
| **TXT** | Văn bản tự do. Dùng để **chứng minh sở hữu** tên miền (Google, AWS bắt thêm TXT) và chống giả mạo email (**SPF, DKIM, DMARC**) | `"v=spf1 include:_spf.google.com ~all"` |
| **SRV** | Dịch vụ X chạy ở host nào, **port** nào | `_sip._tcp.example.com. SRV 10 5 5060 sip.example.com.` |
| **CAA** | Chỉ những tổ chức cấp chứng chỉ (CA) nào được cấp certificate HTTPS cho tên này (CA học ở bài 1.4) | `CAA 0 issue "letsencrypt.org"` |
| **HTTPS / SVCB** (mới, RFC 9460, 2023) | Báo trước cho trình duyệt: "server này hỗ trợ HTTP/3", "dùng port nào"… để đỡ một lượt thử (HTTP/3 học ở bài 1.3) | `HTTPS 1 . alpn="h3,h2"` |

### 4.3 Nhóm "quản lý zone"
| Record | Ý nghĩa |
|---|---|
| **NS** (Name Server) | Zone này do **nameserver nào** trả lời. Chính nhờ NS mà root → TLD → authoritative "giới thiệu" được nhau |
| **SOA** (Start Of Authority) | Thông tin quản lý zone: nameserver chính, email quản trị, số phiên bản (serial), và **TTL cho câu trả lời "không tồn tại"** (mục 5.4) |
| **PTR** | Chiều **ngược**: IP → tên (**reverse DNS**). Lưu dưới tên đặc biệt `3.112.82.140.in-addr.arpa.` Mail server hay kiểm tra PTR để chống spam |

<details><summary>🧠 Tự kiểm tra: Bạn muốn `shop.vn` (tên gốc) trỏ tới dịch vụ `shop.myshopify.com`. Dùng CNAME được không?</summary>

Không nên. Tên gốc bắt buộc có SOA và NS, mà CNAME không được đứng chung với record khác. Có hai cách: dùng **ALIAS/CNAME flattening** nếu nhà cung cấp DNS hỗ trợ, hoặc đặt **A record** trỏ thẳng IP mà dịch vụ cung cấp. Còn `www.shop.vn` thì dùng CNAME bình thường.
</details>

<details><summary>🧠 Tự kiểm tra: Email gửi tới `ban@congty.vn` thì mail server bên gửi tra record gì?</summary>

**MX** của `congty.vn`. Nhiều MX thì thử cái có **priority nhỏ nhất** trước, lỗi thì thử cái tiếp theo. Sau đó tra A/AAAA của tên mail server để lấy IP.
</details>

---

## 5. TTL và caching

### 5.1 Vì sao cần cache
Nếu mỗi lần mở trang đều phải đi root → TLD → authoritative, DNS sẽ chậm và các server bị quá tải. Thực tế phần lớn câu hỏi được trả lời **từ cache**.

Cache có ở **nhiều tầng**:
```
[Trình duyệt] → [Hệ điều hành (stub)] → [Recursive resolver] → (root/TLD/authoritative)
  cache 1            cache 2                cache 3 (dùng chung cho mọi khách của resolver)
```
Ngoài ra file `/etc/hosts` (Windows: `C:\Windows\System32\drivers\etc\hosts`) được xem **trước cả DNS**. Ghi tay `127.0.0.1 api.local` vào đây là dev local đi đúng IP mình muốn.

### 5.2 TTL — hạn dùng của câu trả lời
**TTL** (Time To Live) ở đây là **số giây** một record được phép nằm trong cache. Hết hạn thì phải hỏi lại.

⚠️ Đừng nhầm với TTL của bài 1.1. TTL trong packet IP là **số hop** còn lại. TTL trong DNS là **số giây** cache. Trùng tên, khác ý nghĩa.

Resolver **đếm ngược** TTL. Nếu authoritative trả TTL 300 và 100 giây sau bạn hỏi lại, resolver trả cùng câu trả lời với TTL **200**.

**Chọn TTL là một đánh đổi:**

| TTL | Ưu | Nhược | Hay dùng cho |
|---|---|---|---|
| Thấp (30–300 giây) | Đổi IP có hiệu lực nhanh, failover nhanh | Nhiều lượt tra hơn, chậm hơn một chút, tốn tải | Dịch vụ cần đổi IP nhanh, load balancer, failover |
| Cao (1 giờ – 1 ngày) | Ít tra, nhanh, đỡ phụ thuộc DNS server | Đổi IP thì client cũ còn dùng IP cũ rất lâu | Record ít đổi: MX, NS, TXT |

### 5.3 "DNS propagation" thật ra là chờ cache hết hạn
Bạn đổi A record từ IP cũ sang IP mới. Authoritative có giá trị mới **ngay lập tức**. Nhưng mọi resolver đã cache IP cũ vẫn trả IP cũ **cho tới khi TTL hết**. Không có cơ chế nào "đẩy" thay đổi ra khắp internet, chỉ có cache **tự hết hạn**.

Người ta gọi đó là "DNS propagation" (lan truyền DNS), nghe như có gì đó đang lan đi, nhưng thực chất chỉ là **chờ TTL**.

**Quy trình chuyển server chuẩn (hay hỏi phỏng vấn):**
```
T - 2 ngày:  Hạ TTL từ 86400 (1 ngày) xuống 60 giây.
             (Phải chờ TTL CŨ hết, tức 1 ngày, để mọi cache nhận TTL mới.)
T:           Đổi A record sang IP mới. Trong ~60 giây hầu hết client đã sang IP mới.
             Giữ server CŨ chạy thêm một thời gian cho client "cứng đầu".
T + vài ngày: Ổn định rồi thì nâng TTL lên lại.
```
Có client "cứng đầu" vì một số resolver, ứng dụng hoặc connection pool giữ IP lâu hơn TTL. Vì vậy **không tắt server cũ ngay**.

### 5.4 Negative caching — cache cả câu trả lời "không tồn tại"
Tra một tên **không có** thì authoritative trả **NXDOMAIN** ("tên không tồn tại"). Resolver cũng **cache câu trả lời này** (RFC 2308). Thời gian cache lấy theo record **SOA** của zone, là giá trị nhỏ hơn giữa TTL của SOA và trường MINIMUM của nó.

Bẫy thực tế: bạn tra `api.moi.vn` **trước khi** tạo record → nhận NXDOMAIN, được cache vài phút đến cả giờ → bạn tạo record xong vẫn "không thấy". Lúc này phải chờ negative cache hết hạn.

<details><summary>🧠 Tự kiểm tra: A record đang có TTL 86400. Bạn đổi IP lúc 9h sáng. Đến 10h có user vẫn vào server cũ. Lỗi ở đâu? Lần sau làm thế nào?</summary>

Không có lỗi: resolver của user đã cache IP cũ, tối đa 1 ngày (86400 giây). Lần sau **hạ TTL xuống thấp trước**, ít nhất một khoảng bằng TTL cũ, rồi mới đổi IP. Và giữ server cũ chạy thêm một thời gian.
</details>

<details><summary>🧠 Tự kiểm tra: TTL trong DNS và TTL trong packet IP khác nhau thế nào?</summary>

DNS TTL = **số giây** được giữ trong cache. IP TTL = **số hop** còn lại trước khi packet bị bỏ (chống chạy vòng).
</details>

---

## 6. DNS chạy trên UDP, và khi nào chuyển sang TCP

**Vì sao UDP:** một câu hỏi DNS rất nhỏ (vài chục byte), câu trả lời thường vài trăm byte, **vừa một packet**. Nếu dùng TCP thì mất 1 RTT bắt tay **trước** khi hỏi, tức chậm gấp đôi. Với UDP: hỏi 1 packet, trả 1 packet, **xong trong 1 RTT**.

Còn mất gói thì sao? UDP không gửi lại, nên **client tự hỏi lại** sau một khoảng chờ (timeout, thường khoảng 1–5 giây tuỳ hệ điều hành), hoặc hỏi resolver khác.

**Khi nào DNS dùng TCP (cũng port 53):**
| Trường hợp | Giải thích |
|---|---|
| Câu trả lời **quá lớn** | Ngày xưa giới hạn 512 byte. Nay có **EDNS** cho phép lớn hơn, và khuyến nghị (DNS Flag Day 2020) là **1232 byte** để tránh IP bị phân mảnh. Lớn hơn mức đó thì server trả gói bị cắt kèm cờ **TC** (truncated). Client thấy TC=1 thì **hỏi lại bằng TCP** |
| **Zone transfer** (AXFR) | Sao chép **cả zone** từ nameserver chính sang nameserver phụ: dữ liệu lớn, cần tin cậy |
| DNS mã hoá | DoT, DoH chạy trên TCP/TLS (phần B1) |

Con số 1232 đến từ: MTU 1280 tối thiểu của IPv6, trừ 40 byte header IPv6 và 8 byte header UDP.

<details><summary>🧠 Tự kiểm tra: Vì sao DNS chọn UDP? Nếu câu trả lời lớn quá thì sao?</summary>

Vì hỏi–đáp nhỏ, vừa một packet: UDP xong trong 1 RTT, TCP phải thêm 1 RTT bắt tay. Câu trả lời quá lớn thì server cắt gói và bật cờ **TC**, client hỏi lại bằng **TCP**.
</details>

---

## 7. Anycast — nhiều server, chung một IP

**Vấn đề:** `8.8.8.8` hay `1.1.1.1` phục vụ người dùng cả thế giới. Nếu chỉ đặt ở Mỹ thì người Việt Nam tốn RTT ~200 ms mỗi lần hỏi, và một datacenter hỏng là cả thế giới mất DNS.

**Cách giải quyết:** đặt server ở **hàng trăm thành phố**, tất cả dùng **cùng một IP**. Mỗi địa điểm đều **thông báo qua BGP** (bài 1.1 mục 3): "tôi đi tới được dải IP 1.1.1.0/24". Router trên internet chọn đường **ngắn nhất theo BGP**, nên packet của bạn tự đi tới địa điểm **gần nhất về mặt mạng**.

```
          ┌──────────── 1.1.1.1 @ Singapore   ◀── user Việt Nam
 1.1.1.1 ─┼──────────── 1.1.1.1 @ Frankfurt   ◀── user Đức
          └──────────── 1.1.1.1 @ San Jose    ◀── user Mỹ
```

Cách gửi thông thường, một IP ứng với một máy, gọi là **unicast**. **Anycast** là một IP, nhiều máy, packet tới máy "gần nhất".

**Lợi ích:**
- **Nhanh**: hỏi server gần.
- **Bền**: một địa điểm hỏng thì nó ngừng thông báo BGP, và traffic tự chuyển sang địa điểm kế tiếp.
- **Chịu DDoS** (tấn công làm ngập bằng lưu lượng): traffic tấn công bị chia ra nhiều nơi thay vì dồn vào một chỗ.

**Vì sao anycast hợp với DNS:** DNS qua UDP là "hỏi một câu, trả một câu", không giữ kết nối. Nếu BGP đổi đường giữa chừng thì câu hỏi sau đi tới máy khác cũng không sao. Với kết nối TCP dài thì đổi máy giữa chừng sẽ làm đứt kết nối, nên khó hơn (CDN dùng anycast cho cả TCP nhưng phải xử lý thêm, học ở bài 6.6).

⚠️ "Gần nhất" là gần theo **đường BGP**, không phải theo km. Đôi khi bạn bị đưa tới địa điểm xa hơn về địa lý.

<details><summary>🧠 Tự kiểm tra: 13 root server làm sao phục vụ cả thế giới?</summary>

13 tên chỉ là 13 địa chỉ IP. Mỗi địa chỉ được **anycast** từ rất nhiều nơi, khoảng 2.000 máy (đầu 2026). Packet của bạn đi tới instance gần nhất theo BGP.
</details>

---

## 8. DNS dùng để phân tải và chuyển vùng

Vì DNS quyết định "client sẽ kết nối tới IP nào", nó cũng là một công cụ điều hướng traffic:

| Kỹ thuật | Cách làm | Giới hạn |
|---|---|---|
| **Round-robin DNS** | Một tên có nhiều A record, mỗi lần trả theo thứ tự xoay vòng | Không biết server nào đang chết. Client cache IP chết tới khi hết TTL |
| **GeoDNS** | Trả IP khác nhau tuỳ vị trí người hỏi: user Việt Nam → server Singapore | Resolver dùng vị trí của **resolver**, không phải của user (dùng `8.8.8.8` có thể bị coi ở chỗ khác). Bản mở rộng **EDNS Client Subnet** gửi kèm một phần IP user để đỡ việc này |
| **DNS failover** | Nhà cung cấp DNS liên tục kiểm tra sức khoẻ (health check) server. Server chết thì bỏ IP đó khỏi câu trả lời | Chậm ít nhất bằng TTL. Vì vậy hay dùng TTL thấp (30–60 giây) |

Kết luận cho phỏng vấn: DNS điều hướng ở mức **thô** (phút, theo vùng). Phân tải **mịn** (theo từng request, biết server nào khoẻ) là việc của **load balancer** (học ở bài 6.4). CDN kết hợp cả DNS và anycast (bài 6.6).

<details><summary>🧠 Tự kiểm tra: Vì sao không chỉ dùng round-robin DNS thay cho load balancer?</summary>

Vì DNS **không biết** server nào đang sống, và client **cache** IP theo TTL. Một server chết thì phần client đã cache IP đó tiếp tục lỗi cho tới khi hết TTL. DNS cũng không chia tải đều, vì một resolver lớn cache một IP rồi trả cho rất nhiều user.
</details>

---

## 9. Ghép lại: gõ `https://shop.vn` lần đầu tiên trong ngày

Nối bài 1.1 với bài này. Tình huống: `shop.vn` có `www.shop.vn CNAME shop.cdn-provider.net`, còn tên đích có A record TTL 60.

```
1. Trình duyệt phân tích URL: https → port 443, tên = www.shop.vn.
2. Kiểm tra cache trình duyệt → không có. Kiểm tra /etc/hosts → không có. Hỏi OS (stub) → không có.
3. Stub gửi 1 datagram UDP tới resolver 1.1.1.1:53. Anycast đưa nó tới instance Singapore.
   (Packet đi qua NAT ở router nhà, rồi các router của nhà mạng, như bài 1.1.)
4. Resolver chưa có cache cho www.shop.vn:
   a. Đã cache NS của .vn từ trước, nên bỏ qua root.
   b. Hỏi TLD .vn → referral: "shop.vn do ns1.dns-provider.com quản lý".
   c. Hỏi authoritative → "www.shop.vn CNAME shop.cdn-provider.net".
   d. Đi tra tiếp shop.cdn-provider.net (lại TLD .net → authoritative của CDN)
      → "A 203.0.113.10, TTL 60" (GeoDNS chọn IP gần Việt Nam).
5. Resolver cache tất cả (mỗi record theo TTL riêng), trả kết quả cho máy bạn.
6. Song song, trình duyệt hỏi cả AAAA. Có IPv6 thì thường thử IPv6 trước, lỗi thì quay về IPv4
   (Happy Eyeballs, RFC 8305).
7. Có IP rồi → TCP 3-way handshake tới 203.0.113.10:443 → TLS (bài 1.5) → HTTP (bài 1.3).
8. 30 giây sau mở trang khác: DNS lấy từ cache (TTL còn ~30 giây),
   TCP/TLS dùng lại kết nối nhờ keep-alive.
```

---

# PHẦN B — ĐÀO SÂU CHO PHỎNG VẤN

## B1. Bảo mật DNS: cache poisoning, DNSSEC, DoH/DoT

**Vấn đề gốc:** DNS truyền thống qua UDP **không mã hoá** và **không xác thực**. Ai đứng giữa đường cũng **đọc** được bạn tra tên gì, và có thể **giả** câu trả lời.

**DNS cache poisoning (đầu độc cache):** kẻ tấn công gửi câu trả lời **giả** tới resolver trước khi câu trả lời thật về tới, ví dụ "`bank.vn` = IP của kẻ xấu". Nếu resolver tin, nó cache câu trả lời giả và phát cho **mọi user** của nó cho tới khi hết TTL.
- Ban đầu resolver chỉ kiểm tra **transaction ID** 16 bit (65.536 giá trị), nên đoán trúng không khó.
- Năm 2008, Dan Kaminsky công bố cách tấn công làm việc đoán trúng dễ hơn nhiều. Cách vá khẩn cấp là **chọn ngẫu nhiên cả port nguồn**, cộng thêm khoảng 16 bit nữa phải đoán. Cách này chỉ làm khó hơn chứ không chặn hẳn. Năm 2020 nghiên cứu **SAD DNS** còn tìm được cách dò ra port nguồn.

**DNSSEC:** authoritative **ký số** lên record. Resolver kiểm tra chữ ký nên biết câu trả lời **đúng là của chủ tên miền**, không bị sửa (chữ ký số học ở bài 1.4).
- ⚠️ DNSSEC đảm bảo **tính xác thực** (authenticity), **không mã hoá**: người đứng giữa vẫn đọc được bạn tra gì.

**DoT và DoH (DNS mã hoá):** giữa máy bạn và resolver, câu hỏi DNS đi trong một kết nối **TLS** đã mã hoá (TLS học ở bài 1.5).

| | **DoT** (DNS over TLS, RFC 7858) | **DoH** (DNS over HTTPS, RFC 8484) |
|---|---|---|
| Port | **853** riêng | **443**, chung với web |
| Đặc điểm | Dễ nhận ra là DNS, quản trị mạng dễ kiểm soát | Lẫn vào traffic web, khó chặn và khó lọc |
| Ai dùng | Android "Private DNS" | Firefox, Chrome (tuỳ cấu hình và khu vực) |

Đánh đổi: DoH bảo vệ quyền riêng tư của user, nhưng làm hỏng việc **lọc DNS** của công ty hoặc trường học.

| Chống được | Cache poisoning / giả mạo | Bị nghe lén tên đang tra |
|---|---|---|
| DNSSEC | ✅ | ❌ |
| DoT / DoH | ✅ (chỉ đoạn máy bạn ↔ resolver) | ✅ (chỉ đoạn máy bạn ↔ resolver) |

**Tấn công khác cần biết tên:**
- **DNS hijacking**: chiếm tài khoản registrar để đổi NS. Phòng bằng 2FA (xác thực hai bước) và **registry lock** (khoá không cho đổi NS nếu chưa xác minh thủ công).
- **Dangling CNAME / subdomain takeover**: CNAME trỏ tới dịch vụ cloud bạn đã **xoá**. Kẻ khác đăng ký lại đúng tên đó và chiếm được subdomain của bạn. Phòng bằng cách xoá record khi xoá dịch vụ.
- **DNS amplification DDoS**: gửi câu hỏi nhỏ với IP nguồn **giả** là IP nạn nhân, server trả câu trả lời lớn hơn nhiều lần về phía nạn nhân. Hiệu quả vì UDP không bắt tay nên giả IP được (rate limiting học ở bài 6.13).

## B2. Khi DNS sập, mọi thứ sập: hai sự cố kinh điển

- **Dyn, 21/10/2016:** botnet **Mirai** (hàng chục nghìn camera và thiết bị IoT bị chiếm) DDoS nhà cung cấp DNS Dyn. Twitter, GitHub, Netflix, Reddit… không truy cập được nhiều giờ ở nhiều vùng, dù server của họ vẫn chạy bình thường. Bài học: **dùng nhiều nhà cung cấp DNS** (nhiều NS ở các nhà khác nhau).
- **Facebook, 04/10/2021 (đã nhắc ở bài 1.1):** một lệnh bảo trì làm **rút toàn bộ thông báo BGP** tới các authoritative DNS server của Facebook. Resolver khắp thế giới không hỏi được `facebook.com` là IP nào, nên mất khoảng 6 tiếng. Hệ thống nội bộ cũng dùng DNS đó, nên kỹ sư khó vào sửa.

Câu đùa trong ngành: *"It's not DNS. There's no way it's DNS. It was DNS."* Khi điều tra sự cố mạng, **luôn kiểm tra DNS sớm**.

## B3. Debug DNS như kỹ sư backend

| Muốn biết | Lệnh |
|---|---|
| A record của một tên | `dig github.com` hoặc `dig +short github.com` |
| Record loại khác | `dig gmail.com MX`, `dig github.com NS`, `dig github.com TXT` |
| Hỏi thẳng một resolver cụ thể | `dig @8.8.8.8 github.com` và `dig @1.1.1.1 github.com` (so sánh 2 resolver) |
| Đi từ root xuống | `dig +trace www.github.com` |
| Hỏi thẳng authoritative (bỏ qua cache) | `dig @<nameserver-trong-NS> www.github.com` |
| TTL còn lại | Cột thứ 2 trong phần `ANSWER SECTION` của `dig` |
| Reverse DNS | `dig -x 8.8.8.8` |
| Máy mình đang dùng resolver nào | `resolvectl status` hoặc `cat /etc/resolv.conf` (Linux) |

**Checklist khi "đổi DNS rồi mà chưa ăn":**
1. Hỏi thẳng **authoritative**: đã có giá trị mới chưa? Nếu chưa thì bạn sửa sai chỗ, ví dụ sửa ở registrar nhưng NS lại trỏ sang Cloudflare.
2. Hỏi resolver công cộng (`@8.8.8.8`, `@1.1.1.1`): TTL còn bao nhiêu?
3. Có bị **negative cache** không (đã tra trước khi tạo record)?
4. Cache **trình duyệt/OS**: xoá cache, hoặc thử máy khác.
5. File `/etc/hosts` có dòng ghi đè không?
6. Ứng dụng hoặc connection pool có giữ IP cũ không? Khởi động lại thử.

**Split-horizon DNS:** cùng một tên nhưng trả IP **private** khi hỏi từ trong công ty và IP **public** khi hỏi từ ngoài. Hay gặp trong công ty, cloud VPC và Docker/Kubernetes (container gọi nhau bằng tên nhờ DNS nội bộ, học ở bài 14.2).

---

## 💼 Câu phỏng vấn hay gặp & ý chính cần trả lời

| Câu hỏi | Ý chính |
|---|---|
| DNS hoạt động thế nào? Kể quá trình tra một tên. | Cache trình duyệt/OS → resolver → root → TLD → authoritative. Referral ở mỗi bước; cache theo TTL; UDP 53 (mục 3) |
| Recursive resolver khác authoritative server thế nào? | Resolver đi hỏi hộ và cache; authoritative lưu và trả câu trả lời chính thức cho zone của mình (mục 2) |
| Recursive query vs iterative query? | Client → resolver: "tìm đến cùng"; resolver → root/TLD/auth: nhận referral rồi tự đi tiếp (mục 3) |
| A, AAAA, CNAME, MX, NS, TXT là gì? | Bảng mục 4. Nói thêm: TXT dùng cho SPF/DKIM/DMARC và xác minh sở hữu |
| Vì sao không đặt CNAME ở apex? | CNAME không được đứng chung record khác; apex bắt buộc có SOA/NS. Lách bằng ALIAS/flattening (mục 4.1) |
| TTL là gì? Chọn cao hay thấp? | Thời gian cache; đánh đổi tốc độ đổi IP vs số lượt tra (mục 5.2) |
| Đổi IP server mà không gây downtime thế nào? | Hạ TTL trước ≥ TTL cũ → đổi IP → giữ server cũ một thời gian → nâng TTL lại (mục 5.3) |
| "DNS propagation" là gì? | Không có gì lan ra cả; là chờ cache các resolver hết hạn (mục 5.3) |
| Vì sao DNS dùng UDP? Khi nào dùng TCP? | Một packet hỏi/đáp, 1 RTT. TCP khi trả lời lớn (cờ TC, > ~1232 byte), zone transfer, DoT/DoH (mục 6) |
| Anycast là gì? Vì sao DNS dùng? | Một IP, nhiều nơi thông báo qua BGP; gần, bền, chịu DDoS; hợp với UDP không trạng thái (mục 7) |
| Dùng DNS để load balance được không? Giới hạn? | Round-robin/GeoDNS/failover được, nhưng thô, bị cache, không biết sức khoẻ server ⇒ dùng kèm load balancer (mục 8) |
| DNSSEC khác DoH thế nào? | DNSSEC: chữ ký, chống giả, không mã hoá. DoH/DoT: mã hoá đoạn client↔resolver, chống nghe lén (B1) |
| Cache poisoning là gì, phòng thế nào? | Chèn câu trả lời giả vào cache resolver; random transaction ID + source port, DNSSEC (B1) |
| Website không vào được, bạn kiểm tra DNS thế nào? | `dig` qua nhiều resolver, hỏi thẳng authoritative, xem TTL, hosts file, cache (B3) |

---

## ⚠️ Hiểu sai phổ biến
- ❌ "Root server biết IP của mọi website" → Root chỉ biết ai quản lý TLD.
- ❌ "Có đúng 13 root server" → 13 **tên/địa chỉ**, khoảng 2.000 máy nhờ anycast.
- ❌ "DNS propagation là thay đổi đang lan ra" → Đó là chờ **cache hết hạn** theo TTL.
- ❌ "Đổi DNS xong thì tắt server cũ được luôn" → Client còn cache IP cũ; giữ server cũ thêm một thời gian.
- ❌ "DNS chỉ chạy trên UDP" → TCP port 53 khi trả lời lớn, khi zone transfer; DoT/DoH cũng trên TCP.
- ❌ "TTL trong DNS giống TTL trong packet IP" → DNS: giây cache. IP: số hop.
- ❌ "DNSSEC mã hoá DNS" → DNSSEC chỉ **ký**, không mã hoá. Mã hoá là DoT/DoH.
- ❌ "CNAME trỏ tới IP" → CNAME chỉ trỏ tới **tên khác**. Trỏ tới IP là A/AAAA.
- ❌ "Mua tên ở đâu thì DNS phải ở đó" → Registrar và DNS hosting là hai việc độc lập; NS quyết định DNS ở đâu.
- ❌ "Round-robin DNS là đủ để chịu lỗi" → Client cache IP của server chết tới hết TTL.

---

## 📝 Tóm tắt
- **DNS** đổi tên → IP, chia thành **cây**: root → TLD → tên miền → subdomain; mỗi nhánh do một bên quản lý (**zone**, **delegation**).
- **Resolver** đi hỏi hộ (recursive query từ client, iterative query tới root/TLD/authoritative) và **cache**. **Authoritative** giữ câu trả lời chính thức.
- Record chính: **A** (IPv4), **AAAA** (IPv6), **CNAME** (bí danh, không ở apex), **MX** (email, số nhỏ ưu tiên), **NS** (ai quản lý zone), **TXT** (xác minh, SPF/DKIM/DMARC), **SOA**, **PTR** (IP → tên).
- **TTL** = số giây được cache. Đổi DNS = chờ TTL hết; muốn đổi nhanh thì **hạ TTL trước**. Câu trả lời "không tồn tại" cũng bị cache.
- DNS chạy **UDP 53** (1 RTT); dùng **TCP** khi trả lời lớn (cờ TC), zone transfer, DoT/DoH.
- **Anycast**: một IP, nhiều địa điểm, BGP đưa tới chỗ gần nhất → nhanh, bền, chịu DDoS.
- DNS điều hướng traffic ở mức **thô** (round-robin, GeoDNS, failover); phân tải mịn là việc của load balancer.
- Bảo mật: **cache poisoning**; **DNSSEC** = xác thực, không mã hoá; **DoT (853) / DoH (443)** = mã hoá.

---

## 🔍 Thực hành nhanh (10 phút)
Linux/macOS (Windows: dùng `nslookup` hoặc cài `dig` qua BIND tools; WSL cũng có sẵn).
```bash
# 1. Tra A record, chỉ lấy IP
dig +short github.com

# 2. Xem đầy đủ: để ý cột TTL trong ANSWER SECTION, chạy lại sau 10 giây xem TTL giảm
dig github.com

# 3. Các loại record khác
dig gmail.com MX +short
dig github.com NS +short
dig google.com AAAA +short
dig github.com TXT +short

# 4. CNAME: xem bí danh trỏ đi đâu
dig www.github.com

# 5. Đi từ root xuống, thấy từng referral
dig +trace www.github.com

# 6. So sánh hai resolver và đo thời gian (dòng "Query time")
dig @8.8.8.8 vnexpress.net
dig @1.1.1.1 vnexpress.net

# 7. Reverse DNS
dig -x 8.8.8.8 +short

# 8. Tên không tồn tại → status: NXDOMAIN, phần AUTHORITY có SOA (dùng cho negative caching)
dig khong-ton-tai-abc123.github.com

# 9. Máy bạn đang dùng resolver nào
resolvectl status 2>/dev/null || cat /etc/resolv.conf

# 10. Xem file hosts
cat /etc/hosts
```
Thử thêm: sửa `/etc/hosts` (cần sudo), thêm dòng `127.0.0.1 thu-nghiem.local`, rồi `ping thu-nghiem.local`. Nhớ xoá dòng đó sau khi thử.

---

## 📚 Đọc thêm
- Cloudflare Learning Center — What is DNS? / DNS records / DNS server types: https://www.cloudflare.com/learning/dns/what-is-dns/
- MDN — DNS (glossary): https://developer.mozilla.org/en-US/docs/Glossary/DNS
- roadmap.sh — DNS in One Picture: https://roadmap.sh/guides/dns-in-one-picture
- Julia Evans — "How DNS works" zine và bài "DNS doesn't propagate": https://jvns.ca/categories/dns/
- High Performance Browser Networking — phần về độ trễ DNS trong trình duyệt: https://hpbn.co/

## 🔗 Nguồn tham khảo đã dùng
- RFC 1034 — Domain Names: Concepts and Facilities: https://www.rfc-editor.org/rfc/rfc1034
- RFC 1035 — Domain Names: Implementation and Specification: https://www.rfc-editor.org/rfc/rfc1035
- RFC 2308 — Negative Caching of DNS Queries: https://www.rfc-editor.org/rfc/rfc2308
- RFC 9077 — NSEC and NSEC3 TTLs (nhắc lại quy tắc TTL negative caching): https://www.rfc-editor.org/rfc/rfc9077
- RFC 7766 — DNS Transport over TCP: https://www.rfc-editor.org/rfc/rfc7766
- DNS Flag Day 2020 (EDNS buffer 1232 byte, TCP fallback): https://www.dnsflagday.net/2020/
- Cloudflare Blog — DNS Flag Day 2020: https://blog.cloudflare.com/dns-flag-day-2020
- RFC 9460 — SVCB and HTTPS Resource Records: https://www.rfc-editor.org/rfc/rfc9460
- APNIC Blog — The HTTPS record passes a key milestone: https://blog.apnic.net/2022/08/10/the-https-record-passes-a-key-milestone/
- RFC 7858 (DoT), RFC 8484 (DoH), RFC 8305 (Happy Eyeballs v2): https://www.rfc-editor.org/
- ICANN RSSAC FAQ — 13 root server identifiers, anycast instances: https://www.icann.org/groups/rssac/faq
- APNIC Blog — Measuring DNS root servers under change (2025): https://blog.apnic.net/2025/02/26/measuring-dns-root-servers-under-change/
- root-servers.org (số instance, đầu 2026 khoảng 2.000): https://root-servers.org/
- MDN — DNS glossary: https://developer.mozilla.org/en-US/docs/Glossary/DNS
- roadmap.sh — How DNS works / DNS in One Picture: https://roadmap.sh/guides/dns-in-one-picture
- LWN.net — Kaminsky DNS vulnerability (2008): https://lwn.net/Articles/293069
- BleepingComputer — SAD DNS: DNS cache poisoning attacks return (2020): https://www.bleepingcomputer.com/news/security/dns-cache-poisoning-attacks-return-due-to-linux-weakness/
- SecurityWeek — Firefox DoH default in US: https://www.securityweek.com/firefox-gets-dns-over-https-default-us/
- PracHub — Networking interview questions for SRE/Backend (DNS): https://prachub.com/resources/networking-interview-questions-for-sre-and-backend-engineers-dns-tcp-tls-and-load-balancing
- TechInterview — Networking interview questions (DNS, TTL, CNAME apex): https://www.techinterview.org/post/3233460383/networking-interview-questions/

---
➡️ Học xong thì prompt: **"cho tôi câu hỏi ôn 1.2"**
