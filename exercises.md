# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Khi temperature tăng thì độ đa dạng trong câu trả lời cũng tăng theo, còn khi temperature giảm thì câu trả lời trở nên rập khuôn hơn và cứng ngắc hơn. Quy luật chung là temperature càng cao thì độ sáng tạo của model càng lớn, dẫn đến phản hồi có thể sáng tạo hơn nhưng cũng có thể kém chính xác hơn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature khoảng 0.2 vì chatbot hỗ trợ khách hàng cần câu trả lời ổn định, chính xác và nhất quán. Temperature thấp giúp giảm tính ngẫu nhiên, hạn chế việc chatbot đưa ra những câu trả lời khác nhau cho cùng một vấn đề hoặc tự sáng tạo thông tin. 

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> trong workload này GPT-4o đắt hơn GPT-4o-mini khoảng 16,7 lần. Trong trường hợp câu trả lời yêu cầu phức tạp, cần chất lượng suy luận và độ chính xác cao như phân tích báo cáo thì GPT-4o xứng đáng hơn. Ngược lại với các tác vụ đơn giản, lặp lại nhiều lần như trả lời các câu hỏi hỗ trợ khác hàng thì GPT4o phù hợp hơn.   

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> System prompt 1 trả lời ngắn gọn, dễ hiểu, sử dụng ví dụ đơn giản gắn liền với thực tế. Trong khi system prompt 2 trả lời chuyên sâu, sử dụng thuật ngữ kỹ thuật và đưa ra các ví dụ rõ ràng với nhiều thuật ngữ hơn. System prompt định hướng vai trò, phong cách, mức độ chuyên sâu và cách trình bày của model, dù câu hỏi đầu vào hoàn toàn giống nhau.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> với đoạn văn dài 105 từ Bộ mã o200k_base của GPT-4o đếm được 129 token thay vì ước tính 140 token của part 1 chênh lệch khoản 7%. Vì tiếng việt có dấu thanh và một số từ không có trong bộ mã nên tốn nhiều token hơn tiếng anh.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất trong các ứng dụng tương tác trực tiếp với người dùng, đặc biệt là chatbot hoặc AI assistant. Nó giúp người dùng thấy câu trả lời xuất hiện ngay từng phần thay vì phải chờ model tạo xong toàn bộ phản hồi. Non-streaming phù hợp trong hệ thống cần nhận toàn bộ kết quả trước khi xử lý tiếp, ví dụ lưu kết quả vào database, kiểm tra định dạng JSON, chạy một pipeline tự động hoặc khi phản hồi ngắn và người dùng không cần theo dõi quá trình tạo câu trả lời.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Với delay cố định, khi hàng nghìn client cùng gặp lỗi và cùng chờ đúng 1 giây, chúng sẽ đồng loạt retry cùng một lúc dẫn đến tiếp tục quá tải. Exponential backoff khắc phục điều này bằng cách tăng dần thời gian chờ sau mỗi lần thất bại. Điều này gián tiếp làm giảm tải cho server.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
>mình chọn persona "Bạn là trợ lý kỹ thuật chuyên hỗ trợ lập trình viên, trả lời ngắn gọn, đưa ra các phương án phù hợp khác nhau và đánh giá các phương án đó". Mình yêu cầu trả lời ngắn gọn, đưa ra các phương án phù hợp và đánh giá các phương án đó để tăng khả năng trợ lý đưa ra các giải pháp khác nhau cho các vấn đề từ đó mình có nhiều lựa chọn hơn cho vấn đề mình gặp phải. 

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> hạn chế lớn nhất của trợ lý là chưa có bộ nhớ dài hạn. Để cải thiện thì cần lưu sẽ lưu history vào database ví dụ thêm cờ session_id và tự động lưu history ra file JSON hoặc database.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
