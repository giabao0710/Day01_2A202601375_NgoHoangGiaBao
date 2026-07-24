# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `[câu trả lời của bạn]` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Khi temperature tăng dần từ 0.0 đến 1.5, mức độ đa dạng từ ngữ và tính ngẫu nhiên của phản hồi tăng rõ rệt. Ở temperature 0.0, câu trả lời mang tính ổn định tuyệt đối và tập trung vào thông tin cơ bản (như vị thế xuất khẩu cà phê). Ở 1.5, phản hồi có xu hướng dùng từ lạ, cấu trúc câu sáng tạo hơn nhưng bắt đầu kém ổn định và có nguy cơ lan man.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature ở mức 0.0 đến 0.2. Lý do là chatbot hỗ trợ khách hàng cần sự chính xác tuyệt đối, nhất quán về quy trình và quy định sản phẩm, tránh tối đa hiện tượng tưởng tượng thông tin (hallucination) hoặc trả lời ngẫu nhiên gây hiểu lầm cho người dùng.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Tổng lượng token đầu ra mỗi ngày là 10.000 x 3 x 350 = 10,500,000 token (10,500K token). Chi phí GPT-4o là 10,500 x $0.010 = $105/ngày, còn GPT-4o-mini là 10,500 x $0.0006 = $6.30/ngày. GPT-4o đắt hơn GPT-4o-mini khoảng 16.67 lần. GPT-4o xứng đáng khi cần phân tích hợp đồng pháp lý hoặc giải bài toán suy luận lập trình phức tạp; còn GPT-4o-mini phù hợp để phân loại yêu cầu hỗ trợ hoặc tóm tắt văn bản ngắn.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Phản hồi dành cho trẻ 8 tuổi dùng ngôn từ đơn giản, ví dụ minh họa gần gũi như "cuốn sổ tay chuyền tay nhau trong lớp" và câu văn ngắn gọn. Trái lại, phản hồi dành cho chuyên gia tài chính dùng các thuật ngữ chuyên sâu như "sổ cái phân tán (DLT)", "cơ chế đồng thuận", "mã hóa mật mã học" với cấu trúc chặt chẽ. System prompt đóng vai trò định hình ngữ cảnh (persona), trực tiếp điều khiển từ vựng, độ sâu kiến thức và phong cách diễn đạt của mô hình.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với 100 từ tiếng Việt, ước lượng `100 / 0.75 ≈ 133` token, nhưng tiktoken thực tế đếm khoảng 180-210 token (chênh lệch từ 35% đến 60%). Tiếng Việt tốn nhiều token hơn vì bộ tokenizer (BPE) được tối ưu hóa chủ yếu cho tiếng Anh; các từ ghép tiếng Việt và các ký tự có dấu thanh UTF-8 thường bị tách thành nhiều sub-word token nhỏ.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất trong các ứng dụng trò chuyện trực tiếp (chatbot, trợ lý CLI/Web) để cải thiện trải nghiệm người dùng bằng cách giảm Time-To-First-Token (TTFT), giúp người dùng đọc câu trả lời ngay lập tức thay vì phải chờ mô hình sinh xong toàn bộ văn bản. Ngược lại, non-streaming phù hợp hơn cho các hệ thống xử lý ngầm (background jobs), tích hợp API giữa các server với nhau, hoặc khi cần trích xuất dữ liệu định dạng chuẩn (JSON Mode) trước khi xử lý tiếp.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff giúp tăng thời gian chờ sau mỗi lần thất bại, tạo khoảng nghỉ đủ lớn để server API kịp giải phóng tài nguyên và phục hồi từ tình trạng quá tải. Nếu hàng nghìn client cùng thử lại với delay cố định (như 1 giây), tất cả request sẽ đồng loạt dồn về server cùng một thời điểm (hiện tượng Thundering Herd Problem / bão retry), tiếp tục làm sập server và đẩy tỉ lệ lỗi 429/503 lên cao hơn.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Tôi chọn persona: "Bạn là một trợ lý lập trình viên Python chuyên nghiệp. Trả lời chính xác, đi thẳng vào vấn đề, kèm theo đoạn code minh họa chuẩn PEP 8. Tất cả giải thích phải bằng tiếng Việt ngắn gọn."
> Giải thích: Cụm "đi thẳng vào vấn đề" giúp loại bỏ các câu xã giao không cần thiết; cụm "tiếng Việt ngắn gọn" vừa giúp người dùng dễ theo dõi vừa giảm lượng token đầu ra để tiết kiệm chi phí và tối ưu tốc độ phản hồi.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất là bộ nhớ lịch sử bị giới hạn ở 3 lượt gần nhất (6 message) và mất hoàn toàn khi đóng ứng dụng. Cải thiện cụ thể: Triển khai kỹ thuật Conversation Summary Memory kết hợp lưu trữ file SQLite/JSON. Khi lịch sử vượt quá 3 lượt, sử dụng model mini để tóm tắt các lượt chat cũ thành một đoạn ngữ cảnh tổng quan gửi kèm system prompt, vừa giữ được bối cảnh cuộc trò chuyện dài hạn vừa đảm bảo không vượt quá giới hạn token.

---

## Danh Sách Kiểm Tra Nộp Bài

- [x] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [x] Cả 4 checkpoint pytest đều pass
- [x] Tất cả 9 câu trong file này đã được trả lời
- [x] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
