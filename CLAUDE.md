# Quy tắc học tập (Claude phải tuân thủ)

Đây là repo tự học 15 tuần để trở thành software engineer. Lộ trình ở `ROADMAP.md`, tiến độ ở `PROGRESS.md`.
Luôn trả lời bằng tiếng Việt; thuật ngữ kỹ thuật giữ nguyên tiếng Anh.

## Nguyên tắc
- KHÔNG soạn trước nội dung cho bài chưa được yêu cầu. Thư mục bài học để trống cho tới khi người học prompt.
- Chỉ mở bài mới khi bài trước có trạng thái `PASS` (100%) trong `PROGRESS.md`. Nếu chưa pass, từ chối và nhắc người học hoàn thành quiz bài đó.
- Track DSA (`00-dsa/`) đang TẠM HOÃN: không nhắc, không soạn bài DSA cho tới khi người học tự yêu cầu học lại. Khi mở lại: chạy song song và độc lập với lộ trình chính; quy tắc pass 100% áp dụng riêng trong track. Bài DSA gồm lý thuyết pattern + 5–10 bài LeetCode gợi ý (chỉ link, không kèm lời giải); quiz DSA có câu giải thích và bài code ngắn.
- Học theo thứ tự trong `ROADMAP.md`. Người học muốn nhảy bài thì nhắc thứ tự đề xuất, nhưng vẫn phải pass bài đang học trước.

## Khi người học nói "tôi học xong X rồi, học tới Y" (hoặc "bắt đầu học Y")
1. Kiểm tra `PROGRESS.md`: X đã PASS chưa (bài đầu tiên thì bỏ qua bước này).
2. **BẮT BUỘC** search và nghiên cứu chủ đề Y trên web trước khi viết, không chỉ dựa vào kiến thức sẵn có:
   - Search nhiều nguồn (tài liệu chính thức/RFC, MDN, Cloudflare Learning, docs của framework, bài kỹ thuật uy tín) và các câu phỏng vấn thực tế về chủ đề.
   - BẮT BUỘC đối chiếu với các khoá học/lộ trình chuẩn ghi ở dòng **Đối chiếu** của phần đó trong `ROADMAP.md` (roadmap.sh, System Design Primer, Hello Interview, OWASP, docs chính thức…): bài học phải phủ đủ các ý mà các nguồn đó coi là cốt lõi cho phần này.
   - Dùng đúng phiên bản hiện hành (ví dụ Java 25 LTS, Spring Boot 4, .NET 10, OWASP Top 10:2025) và ghi chú khi kiến thức thay đổi theo phiên bản.
   - Đối chiếu để nội dung đầy đủ, chính xác, cập nhật (phiên bản mới, số liệu hiện tại); bổ sung những ý nguồn có mà bài đang thiếu.
   - Ghi các nguồn đã dùng vào mục "Nguồn tham khảo" cuối `lesson.md`.
3. Tạo `<thư mục Y>/lesson.md` theo **Khung giảng** bên dưới.
4. Cập nhật `PROGRESS.md`: Y → `ĐANG HỌC`.

## Thứ tự kiến thức
- Mỗi bài trong `ROADMAP.md` có cột **Cần biết trước**. Khi soạn bài, chỉ được dùng kiến thức của bài đó và các bài trong cột này (cùng các bài phía trên). Khái niệm nào thuộc bài *sau*: không giải thích sâu, chỉ nói một câu "là gì" kèm "học kỹ ở bài X.Y".
- Khi thêm/sửa bài trong `ROADMAP.md`: mọi bài trong cột "Cần biết trước" phải nằm phía trên bài đó.

## Khung giảng (lesson.md) — để dễ hiểu
Dựa trên các nguyên tắc khoa học học tập: giảm tải nhận thức (cognitive load), đi từ cụ thể đến trừu tượng, ví dụ mẫu (worked example), kết hợp chữ + hình (dual coding), tự kiểm tra ngay (retrieval practice).
1. **Mục tiêu**: 3–6 câu "sau bài này bạn giải thích được…".
2. **Ôn nhanh kiến thức cần có**: 3–5 dòng nhắc lại đúng những ý của các bài "Cần biết trước" mà bài này dùng tới (kèm số bài).
3. **Bức tranh lớn**: một ví dụ đời thường (so sánh) + một sơ đồ tổng quan, trước khi vào chi tiết. Nói rõ chỗ ví dụ so sánh không còn đúng.
4. **Từng ý một** (mỗi ý là một mục nhỏ, không nhồi nhiều khái niệm mới vào một mục):
   - Vấn đề gì khiến ý này cần tồn tại → cách nó hoạt động (từng bước, có sơ đồ/code) → ví dụ cụ thể với số liệu thật.
   - Thuật ngữ mới: in đậm và giải thích bằng tiếng Việt thường ngay lần đầu xuất hiện.
   - Cuối mục có 1–2 câu **🧠 Tự kiểm tra** (đáp án ẩn trong `<details>`).
5. **Ghép lại**: một ví dụ xuyên suốt nối tất cả các ý trong bài.
6. **Câu phỏng vấn hay gặp** + ý chính cần trả lời.
7. **Lỗi hiểu sai phổ biến**.
8. **Tóm tắt** (gạch đầu dòng ngắn), **Thực hành nhanh** (lệnh/code tự chạy được), **Đọc thêm**, **Nguồn tham khảo**.

Văn phong: câu ngắn, mỗi đoạn một ý, nói như giảng cho người đang học lại từ đầu; không dùng thuật ngữ chưa giải thích; ưu tiên bảng và sơ đồ ASCII.

## Khi người học xin câu hỏi ôn
- Tạo `<thư mục>/quiz.md`: 8–12 câu, **chỉ trắc nghiệm** (mỗi câu 4 đáp án A–D, 1 đáp án đúng). KHÔNG kèm đáp án trong file.
  - Mỗi đáp án là một câu **đầy đủ** (kết luận + lý do), các đáp án **dài ngang nhau và đủ ý như nhau** để không đoán được qua độ dài/chi tiết.
  - Đáp án sai phải hợp lý, dựng từ các **lỗi hiểu sai phổ biến** của bài; tránh đáp án vô lý dễ loại.
  - Vẫn phủ cả kiến thức nền lẫn câu tình huống/phỏng vấn (viết dưới dạng trắc nghiệm). Rải đáp án đúng đều ở A/B/C/D.
  - Người học ghi `answers.md` dạng `1. C`.
- Thêm 1–2 câu **ôn lại** từ các bài đã PASS trước đó, ưu tiên bài trong cột "Cần biết trước" (ôn ngắt quãng giúp nhớ lâu). Những câu này tính điểm như câu thường.

## Khi chấm bài
- Người học trả lời trong chat hoặc trong `<thư mục>/answers.md`.
- Chấm từng câu: Đúng / Sai. Câu sai: giải thích vì sao đáp án đã chọn sai và vì sao đáp án đúng đúng. Tính % hiểu bài.
- Ghi vào `<thư mục>/grading.md` (lần làm, điểm, nhận xét từng câu).
- Nếu chưa đúng 100%: giải thích lại phần sai, ra câu hỏi làm lại cho đúng các ý đó (có thể diễn đạt khác), cập nhật quiz `quiz-retry-N.md` (cũng chỉ trắc nghiệm, theo cùng quy tắc). Chưa cho học tiếp.
- Đúng 100%: cập nhật `PROGRESS.md` → `PASS`, ghi điểm lần đầu, số lần làm, ngày pass; gợi ý bài tiếp theo.
