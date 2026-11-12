# Quiz 1.2 — DNS (trắc nghiệm)

> **Cách làm:** mỗi câu chọn **1 đáp án đúng nhất**. Ghi vào `answers.md` dạng `1. C`, rồi prompt **"chấm bài 1.2"**.
> Các đáp án được viết **dài ngang nhau và đủ ý như nhau**, nên không đoán được qua độ dài. Phải hiểu mới chọn đúng.
> Cần đúng **100%** mới học tiếp. Không mở lại `lesson.md` khi làm.
> Câu 11–12 là **ôn lại bài 1.1**, tính điểm như câu thường.

---

**Câu 1.** Resolver chưa có cache gì, gửi câu hỏi về `www.github.com` tới **root server**. Root trả lời gì?
- A. Trả về IP của `www.github.com`, vì root lưu sẵn bản sao record A của mọi tên miền phổ biến trên internet.
- B. Trả về danh sách nameserver của `github.com`, vì root trực tiếp quản lý mọi tên miền cấp hai đã đăng ký.
- C. Trả về referral tới các nameserver của TLD `.com`, vì root chỉ biết ai đang quản lý từng TLD.
- D. Trả về NXDOMAIN, vì root chỉ trả lời câu hỏi về chính zone gốc `.` chứ không về tên cụ thể nào.

**Câu 2.** Phát biểu nào mô tả đúng **recursive query** và **iterative query**?
- A. Stub gửi recursive query tới resolver. Resolver gửi iterative query tới root, TLD, authoritative và tự đi tiếp theo từng referral.
- B. Resolver gửi recursive query tới root. Root đi hỏi hộ TLD và authoritative, rồi trả IP cuối cùng về cho resolver.
- C. Stub gửi iterative query tới resolver. Resolver trả referral để stub tự đi hỏi lần lượt root, TLD và authoritative.
- D. Stub và resolver đều gửi recursive query. Iterative query chỉ dùng khi authoritative không phản hồi trong thời gian chờ.

**Câu 3.** `congty.vn` có hai record: `MX 10 mx1.congty.vn` và `MX 20 mx2.congty.vn`. Mail server bên gửi sẽ làm gì?
- A. Gửi thư đồng thời tới cả `mx1` và `mx2` để chắc chắn ít nhất một bản được giao thành công.
- B. Thử `mx2` trước, vì số priority lớn hơn nghĩa là được ưu tiên cao hơn; `mx2` lỗi thì mới thử `mx1`.
- C. Chọn ngẫu nhiên một trong hai server, với tỉ lệ chọn tỉ lệ thuận theo các con số 10 và 20.
- D. Thử `mx1` trước, vì số priority nhỏ hơn nghĩa là được ưu tiên trước; `mx1` lỗi thì mới thử `mx2`.

**Câu 4.** Bạn cần trỏ **`shop.vn`** (tên gốc, không có `www`) sang dịch vụ `shops.myshopify.com`. Lựa chọn nào đúng?
- A. Đặt CNAME ở `shop.vn` như bình thường, vì luật cấm CNAME ở tên gốc đã bị bỏ từ khi có record AAAA.
- B. Không đặt CNAME ở tên gốc, vì tên gốc bắt buộc có SOA và NS mà CNAME không được đứng chung record khác. Dùng ALIAS/CNAME flattening hoặc A record trỏ IP dịch vụ cung cấp.
- C. Không đặt CNAME ở tên gốc, vì CNAME chỉ được trỏ tới IP chứ không trỏ tới tên. Dùng record PTR trỏ ngược từ IP của dịch vụ về `shop.vn`.
- D. Đặt CNAME ở tên gốc được, miễn là xoá record SOA và NS của zone trước để không còn record nào đứng chung với CNAME.

**Câu 5.** So sánh nào giữa **DNSSEC** và **DoH** là đúng?
- A. DNSSEC mã hoá câu hỏi DNS, còn DoH ký số lên record. Cả hai cùng chống nghe lén và chống giả mạo trên toàn bộ chuỗi tra cứu.
- B. DNSSEC chạy trên port 853, còn DoH chạy trên port 443. Hai cái chỉ khác nhau ở port, còn tác dụng bảo vệ thì như nhau.
- C. DNSSEC chống nghe lén đoạn giữa máy bạn và resolver, còn DoH chống việc record bị giả mạo khi đi ra từ authoritative.
- D. DNSSEC ký số để resolver kiểm tra record không bị giả mạo, nhưng không mã hoá. DoH mã hoá đoạn máy bạn ↔ resolver để chống nghe lén.

**Câu 6.** Một câu trả lời DNS dài **3000 byte**, vượt giới hạn thường dùng cho UDP. Điều gì thường xảy ra?
- A. Server trả một gói bị cắt có bật cờ TC. Client thấy cờ TC thì gửi lại đúng câu hỏi đó bằng TCP port 53.
- B. Server chia câu trả lời thành nhiều datagram UDP có đánh số thứ tự, rồi client tự ghép lại giống như TCP.
- C. Server nén câu trả lời cho vừa 512 byte để gửi qua UDP. Client giải nén ra rồi dùng như bình thường.
- D. Server chuyển hẳn sang DoH qua port 443, vì chỉ HTTPS mới chở được câu trả lời DNS có kích thước lớn.

**Câu 7.** Chỉ có **13 tên** root server. Vì sao chúng vẫn phục vụ được cả thế giới, độ trễ thấp và không sập khi một nơi hỏng?
- A. Mỗi tên là một siêu máy tính rất mạnh đặt ở Mỹ. Root chỉ trả referral rất ngắn nên 13 máy vẫn đủ sức chịu tải.
- B. Resolver chọn ngẫu nhiên 1 trong 13 IP. Nếu thấy chậm hoặc lỗi thì đổi sang IP khác cho tới khi gặp máy đủ nhanh.
- C. Mỗi IP được anycast từ rất nhiều nơi (khoảng 2.000 máy). BGP đưa packet tới nơi gần nhất theo đường mạng; nơi hỏng ngừng thông báo BGP thì traffic tự chuyển đi.
- D. GeoDNS của từng nhà mạng tự đổi 13 IP này thành IP của bản sao root gần nhất ngay trước khi packet được gửi đi.

**Câu 8.** Bạn chạy `dig api.moi.vn` **trước khi** tạo record và nhận `NXDOMAIN`. Năm phút sau bạn tạo record A, nhưng resolver của công ty vẫn trả `NXDOMAIN`. Vì sao?
- A. Authoritative cần vài giờ để đồng bộ record mới sang tất cả root server, nên lúc này resolver chưa hỏi ra được.
- B. Resolver đã cache câu trả lời NXDOMAIN. Thời gian cache lấy theo record SOA của zone (giá trị nhỏ hơn giữa TTL của SOA và trường MINIMUM).
- C. Resolver đã cache câu trả lời NXDOMAIN. Thời gian cache lấy theo TTL của chính record A vừa tạo, nên phải chờ hết TTL đó.
- D. Record A mới chưa có hiệu lực cho tới khi registrar duyệt xong và cập nhật record đó vào TLD `.vn`.

**Câu 9.** Record A của `api.shop.vn` có **TTL = 86400**. Team cần chuyển từ IP A sang IP B **không downtime**. Kế hoạch nào đúng?
- A. Đổi record sang IP B rồi tắt server A ngay. Các resolver sẽ tự phát hiện thay đổi và nhận IP mới trong vài phút.
- B. Hạ TTL xuống 60 cùng lúc với việc đổi sang IP B. Chờ 60 giây là mọi client đã sang B, lúc đó tắt server A.
- C. Đổi sang IP B, rồi nhờ nhà cung cấp DNS "đẩy" thay đổi tới mọi resolver trên thế giới để rút ngắn propagation.
- D. Hạ TTL xuống 60 trước ít nhất 1 ngày, rồi mới đổi sang IP B. Giữ server A chạy thêm một thời gian, ổn định rồi nâng TTL lại.

**Câu 10.** Đồng nghiệp đề xuất: *"Đặt 3 IP vào record A của `api.shop.vn` là xong cả load balancing lẫn chịu lỗi, khỏi cần load balancer."* Nhận xét nào đúng?
- A. Đây là round-robin DNS. Nó chia tải thô được, nhưng client đã cache IP của server chết vẫn lỗi tới khi hết TTL, và DNS không biết server nào khoẻ hay đang quá tải.
- B. Đây là round-robin DNS. Resolver tự health check cả 3 IP và loại IP chết khỏi câu trả lời, nên cách này đủ để thay hẳn load balancer.
- C. Đây là anycast. Ba server dùng chung một IP nên BGP tự chia tải đều cho cả ba, và tự chuyển hướng khi một server chết.
- D. Đây là GeoDNS. Mỗi user nhận IP gần mình nhất nên tải luôn đều; chỉ cần đặt thêm TTL thấp là chịu lỗi tốt.

---

## Ôn lại bài 1.1

**Câu 11.** Máy bạn `192.168.1.5` gửi một câu hỏi DNS tới `8.8.8.8`. Mô tả nào đúng về datagram **vừa rời máy bạn**?
- A. Nguồn `192.168.1.5:53`, đích `8.8.8.8:53`. Hai đầu đều dùng port 53 cố định, và vẫn phải bắt tay 3 bước trước khi hỏi.
- B. Nguồn `192.168.1.5:443`, đích `8.8.8.8:53`. Port 443 là port cố định của client, và không cần bắt tay vì có NAT.
- C. Nguồn `192.168.1.5:51234` (ephemeral), đích `8.8.8.8:53` (cố định phía server). Không cần bắt tay vì DNS chạy trên UDP.
- D. Nguồn `8.8.8.8:53`, đích `192.168.1.5:51234`. Không cần bắt tay vì router nhà đã mở sẵn kết nối tới resolver.

**Câu 12.** Bạn mở `https://github.com`, 20 giây sau mở `https://github.com/explore`. Lúc này **kết nối cũ đã bị đóng**, còn DNS cache **vẫn còn hạn**. Request thứ hai phải làm lại những gì?
- A. Không phải làm lại bước nào, vì keep-alive giữ lại cả IP đã tra lẫn kết nối TCP/TLS cho mọi request sau.
- B. Bỏ qua tra DNS nhờ DNS cache, nhưng phải làm lại TCP handshake và TLS handshake vì kết nối cũ đã bị đóng.
- C. Phải tra DNS lại vì DNS cache gắn liền với kết nối cũ, nhưng bỏ qua được TCP handshake nhờ keep-alive.
- D. Phải làm lại cả DNS, TCP và TLS, vì trình duyệt xoá toàn bộ cache ngay khi một kết nối bị đóng.
