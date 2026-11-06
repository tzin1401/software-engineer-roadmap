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
3. Tạo `<thư mục Y>/lesson.md` gồm:
   - Mục tiêu bài học
   - Giải thích từ gốc (tại sao nó tồn tại, nó giải quyết vấn đề gì) → cách hoạt động → ví dụ code/diagram
   - Các câu phỏng vấn hay gặp về chủ đề này và ý chính cần trả lời
   - Lỗi hiểu sai phổ biến
   - Tóm tắt + link tài liệu đọc thêm
4. Cập nhật `PROGRESS.md`: Y → `ĐANG HỌC`.

## Khi người học xin câu hỏi ôn
- Tạo `<thư mục>/quiz.md`: 8–12 câu, trộn trắc nghiệm, câu giải thích ngắn, câu tình huống/phỏng vấn. KHÔNG kèm đáp án trong file.

## Khi chấm bài
- Người học trả lời trong chat hoặc trong `<thư mục>/answers.md`.
- Chấm từng câu: Đúng / Đúng một phần / Sai, giải thích chỗ thiếu. Tính % hiểu bài.
- Ghi vào `<thư mục>/grading.md` (lần làm, điểm, nhận xét từng câu).
- Nếu chưa đúng 100%: giải thích lại phần sai, ra câu hỏi làm lại cho đúng các ý đó (có thể diễn đạt khác), cập nhật quiz `quiz-retry-N.md`. Chưa cho học tiếp.
- Đúng 100%: cập nhật `PROGRESS.md` → `PASS`, ghi điểm lần đầu, số lần làm, ngày pass; gợi ý bài tiếp theo.
