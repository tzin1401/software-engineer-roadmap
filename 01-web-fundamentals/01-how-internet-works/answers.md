# Bài làm 1.1 — lần 1 (2026-11-06)

> **Kết quả: 7,8 / 12 ≈ 65%. Chưa PASS.** Làm tiếp `quiz-retry-1.md`.
> Câu trả lời của bạn giữ nguyên. Nhận xét nằm trong các khối `💬` ngay dưới từng câu.
> Cách chấm: Đúng = 1 điểm · Đúng một phần = 0,5 · Sai = 0. Riêng câu 12 chấm theo từng ý a/b/c.

## Phần 1:

Câu 1. B
> 💬 ✅ **Đúng.**

Câu 2. C
> 💬 ✅ **Đúng.** /16 nghĩa là còn 32 − 16 = 16 bit cho máy, tức 2¹⁶ = 65.536 địa chỉ.

Câu 3. C
> 💬 ✅ **Đúng.** Đây là longest prefix match: /24 là dòng khớp cụ thể nhất.

Câu 4. B
> 💬 ✅ **Đúng.**

## Phần 2:

Câu 5. Dựa vào NAT mỗi IP, PORT nó sẽ translation sang IP của router với mỗi PORT Khác nhau cho mỗi kết nối.
> 💬 🟡 **Đúng một phần (0,5).** Bạn gọi đúng tên NAT và đúng chiều **đi ra**. Nhưng câu hỏi là phản hồi **quay về** thì làm sao biết của máy nào, và ý đó còn thiếu.
> - Router **ghi lại** mỗi lần đổi vào một **bảng NAT**: `192.168.1.5:52341 ⇄ 113.22.10.4:40001`.
> - Phản hồi từ YouTube gửi tới `113.22.10.4:40001`. Router nhìn **port đích 40001**, tra bảng, rồi đổi ngược lại thành `192.168.1.5:52341`.
> - Tóm lại: port do router cấp là "chìa khoá" để tra ra máy trong nhà.

Câu 6. Time to live. Tránh gói tin chạy vòng trong network do mỗi lần qua 1 router TTL - 1
> 💬 ✅ **Đúng.** Nói thêm cho đủ ý: TTL về **0** thì router **bỏ packet**.

Câu 7. Nếu 2 thì sẽ không biết có nhận được gói ACK chưa. Còn 4 bước là do đóng kết nối là độc lập. Client gửi FIN trước thì sever gửi ACK lại rồi đóng. Sau đó sever gửi FIN lại cho client và client gửi lại ACK để đóng luồng từ phía sever.
> 💬 🟡 **Đúng một phần (0,5).**
> - **3 bước, còn mơ hồ.** Cần nói đúng *ai* không biết *cái gì*. Mỗi bên phải gửi số thứ tự bắt đầu của mình **và** được bên kia xác nhận. Nếu chỉ có 2 bước, **server** không biết client đã nhận được số `y` của server hay chưa. Ý phụ: bước 3 còn giúp loại các SYN cũ bị trễ.
> - **4 bước, ý chính đúng** (TCP là full-duplex, mỗi chiều đóng riêng). Có một chỗ sai: "server gửi ACK lại **rồi đóng**". Sau khi gửi ACK, server **chưa đóng**. Chỉ chiều client → server đóng lại, server **vẫn có thể gửi nốt dữ liệu**. Server xong mới gửi FIN của mình. Đó chính là lý do ACK và FIN của server không gộp làm một.

Câu 8.
- Bắt tay 3 bước: kết nối ổn định
- Đánh số gói tin: đánh sau hay trước đều ghép lại được.
- Check sum: biết được gói tin nào thiếu
- Gửi lại thông báo cho bên gửi là thiếu để gửi lại đúng gói tin thiếu.
> 💬 🟡 **Đúng một phần (0,5).** Có 2 ý đúng, 2 ý sai.
> - ❌ Bắt tay 3 bước dùng để **mở kết nối**, không phải cơ chế chống mất hay hỏng dữ liệu.
> - ✅ **Sequence number**: sắp lại đúng thứ tự và bỏ bản trùng.
> - ❌ **Checksum** phát hiện dữ liệu bị **hỏng** (sai bit), **không** phải phát hiện gói thiếu. Phát hiện thiếu là nhờ sequence number + ACK.
> - ✅ **ACK + retransmission**: bên nhận báo đã nhận tới đâu. Bên gửi chờ quá lâu không thấy ACK thì gửi lại.
> - Hai cơ chế bạn có thể kể thay cho ý sai: **flow control** (không gửi dồn quá sức bên nhận) và **congestion control** (mạng nghẽn thì giảm tốc).

## Phần 3:

Câu 9.
- DNS phân giải tên miền thành IP
- TCP kết nối tới Sever bằng bắt tay 3 bước
- Gửi yêu cầu mở trang chủ thì sever gửi response lại.
> 💬 🟡 **Đúng một phần (0,5).** Ba bước bạn kể đúng thứ tự, nhưng còn thiếu mấy bước mà người phỏng vấn chờ nghe:
> 1. Phân tích URL: `https` thì port mặc định là **443**.
> 2. DNS (thường dùng **UDP port 53**) ✅
> 3. TCP 3-way handshake tới IP:443 ✅
> 4. **TLS handshake**, vì là https. Bước này bị thiếu, và đây là chỗ hay bị hỏi.
> 5. Gửi **HTTP request**: dữ liệu đóng gói thành packet → qua NAT → qua nhiều router.
> 6. Server xử lý rồi trả **HTTP response** ✅ (bạn có nói, nhưng chưa gọi tên giao thức HTTP).
> 7. Trình duyệt **render** trang và tải thêm CSS/JS/ảnh.
> 8. Kết nối được **giữ lại** cho các request sau (keep-alive).

Câu 10. Không dùng TCP vì hình ảnh có thể chấp nhận việc mờ (mất gói tin) chứ không thể gọi mà không realtime được.
> 💬 🟡 **Đúng một phần (0,5).** Kết luận đúng. Còn thiếu 2 ý:
> - **Vì sao TCP làm mất tính realtime:** TCP gửi lại gói bị mất và bắt các gói phía sau **phải chờ** gói đó. Một khung hình đến trễ thì không còn giá trị nữa. Hình bị khựng còn tệ hơn mất một khung.
> - **Khi nào TCP mới đúng** (câu hỏi có hỏi ý này): khi **mỗi byte đều phải đến, đúng thứ tự**. Ví dụ: tải trang web, tải file, giao dịch ngân hàng, tin nhắn chat dạng chữ.

Câu 11. Tôi không biết
> 💬 ❌ **Sai (0).** Đáp án như sau.
> - **Vì sao chậm:** mỗi kết nối TCP mới tốn **1 RTT** cho 3-way handshake trước khi gửi được byte đầu tiên. Nếu có TLS thì tốn thêm. Mỗi request mở nhiều kết nối mới nên phí này cộng dồn. Ngoài ra kết nối mới còn khởi đầu chậm (slow start, mục B1), và đóng kết nối liên tục sẽ đẻ ra TIME_WAIT (câu 12).
> - **Cách sửa: dùng lại kết nối.**
>   - **Keep-alive**: giữ kết nối HTTP mở cho nhiều request.
>   - **Connection pool**: giữ sẵn một nhóm kết nối tới database hoặc service, request nào cần thì mượn rồi trả lại.
> - Ghi chú: bản chất vấn đề là chi phí bắt tay. RTT 200 ms tới user không phải phần chính, vì kết nối tới DB thường nằm gần server.

Câu 12.
a) Chờ các gói tin trễ của bên gửi tới hết để các gói đó không bị nhầm sáng kết nối khác có 5-tuple giống. TCP cần ví dụ như nếu cần đống kết nối thì lỡ gói ACK không tới được thì khi bên client gửi lại FIN thì còn thông tin để trả lời.
> 💬 ✅ **a) Đúng.** Bạn nêu đủ cả 2 lý do. Thêm cho chính xác: bên **chủ động đóng** mới vào TIME_WAIT, và giữ trong **2 × MSL** (Linux là 60 giây).

b) Có thể gây nghẽo sever do phải giữ kết nối hoài trong khi các hành động đã xong.
> 💬 ❌ **b) Sai.** Socket TIME_WAIT gần như không tốn tài nguyên. Vấn đề là nó **giữ chỗ port**.
> - Mỗi kết nối đi ra dùng một **ephemeral port**. Linux mặc định có khoảng **28 nghìn** port như vậy.
> - Mở rồi đóng kết nối liên tục thì mỗi port bị khoá 60 giây trong TIME_WAIT, dẫn tới **hết port** và lỗi `cannot assign requested address`. Đúng như đề bài: *không mở được kết nối mới ra ngoài*.

c) Không biết.
> 💬 ❌ **c) Sai.**
> - ✅ **Cách đúng:** **dùng lại kết nối** bằng keep-alive hoặc connection pool. Ít kết nối mới thì ít TIME_WAIT. Đây cũng là đáp án câu 11.
> - 🚫 **Không nên:** tắt hoặc "lách" TIME_WAIT, ví dụ `tcp_tw_recycle`. Tuỳ chọn này gây lỗi với client đứng sau NAT và đã bị **xoá khỏi Linux kernel**.
>
> Điểm câu 12: 1/3 ≈ 0,3.
