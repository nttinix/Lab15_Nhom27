**WORKSHEET 4: KẾ HOẠCH ĐẢM BẢO KHẢ NĂNG MỞ RỘNG VÀ ĐỘ TIN CẬY (SCALING & RELIABILITY TABLETOP)**

**1, 2 & 3. Phân tích và Xử lý các Tình huống Sự cố (Scenarios, Impacts & Solutions)**

* **Tình huống 1: Lưu lượng truy cập tăng đột biến (*Traffic Spike*)**
    * *Ví dụ:* VinFast livestream mở bán sớm VF 3, hàng chục ngàn người truy cập nền tảng cùng lúc để tra cứu giá và đặt cọc.
    * *Tác động tới người dùng:* Nút gửi tin nhắn bị vô hiệu hóa, hệ thống báo lỗi kết nối, giao diện bị treo. Trải nghiệm mua sắm bị gián đoạn tại thời điểm mức độ quan tâm của khách hàng đang cao nhất.
    * *Phản ứng ngắn hạn (Mitigation):* Kích hoạt cơ chế hàng đợi thông điệp (*Message Queue*) để tiếp nhận yêu cầu tuần tự, tránh gây quá tải máy chủ. Hiển thị thông báo trên giao diện: "Hệ thống đang đón lượng lớn khách hàng, vui lòng chờ trong giây lát" để duy trì tương tác.
    * *Giải pháp dài hạn:* Thiết lập tự động mở rộng quy mô (*Auto-scaling*) dựa trên mức sử dụng CPU/Memory và số lượng yêu cầu. Phân loại luồng xử lý: Các câu hỏi FAQ xử lý thời gian thực (*Real-time*) qua Cache; các yêu cầu tính toán phức tạp (ví dụ: gói vay ngân hàng) chuyển sang xử lý bất đồng bộ (*Async*) và gửi thông báo kết quả sau.

* **Tình huống 2: Lỗi phản hồi từ nhà cung cấp (*Provider Timeout*)**
    * *Ví dụ:* Máy chủ API của Google Gemini hoặc OpenAI gặp sự cố ngừng hoạt động.
    * *Tác động tới người dùng:* AI Consultant không phản hồi sau khi khách hàng đặt câu hỏi, gây cảm giác thiếu chuyên nghiệp.
    * *Phản ứng ngắn hạn:* Áp dụng chính sách thử lại (*Retry Policy*) kết hợp độ trễ tăng dần (*Exponential Backoff*) khoảng 2-3 lần. Nếu vẫn thất bại, kích hoạt cơ chế ngắt mạch (*Circuit Breaker*) để ngừng gọi API lỗi, lập tức trả về tin nhắn thông báo sự cố thân thiện.
    * *Giải pháp dài hạn:* Áp dụng kiến trúc đa mô hình (*Multi-LLM*). Nếu nhà cung cấp A gặp sự cố (*downtime*), hệ thống tự động định tuyến (*route*) lưu lượng sang nhà cung cấp B dự phòng.

* **Tình huống 3: Độ trễ phản hồi cao (*High Latency*)**
    * *Ví dụ:* Bộ tài liệu kỹ thuật của VF 9 có dung lượng lớn, luồng truy xuất dữ liệu (*RAG*) mất nhiều thời gian để xử lý.
    * *Tác động tới người dùng:* Khách hàng phải chờ 10-15 giây mới nhận được phản hồi. Nguy cơ tăng tỷ lệ thoát trang (*Bounce rate*).
    * *Phản ứng ngắn hạn:* Hiển thị ngay lập tức chỉ báo đang nhập văn bản (*Typing indicator*). Kích hoạt cơ chế phản hồi theo luồng (*Streaming Response*), cho phép người dùng đọc thông tin theo từng phần ngay lập tức thay vì chờ tải toàn bộ câu trả lời.
    * *Giải pháp dài hạn:* Tối ưu hóa cơ sở dữ liệu vector (*Vector Database*), triển khai *Semantic Caching* cho các câu hỏi phổ biến và giới hạn độ dài của cửa sổ ngữ cảnh (*Context Window*).

**4. Các Chỉ số Cần Giám sát (Metrics to Monitor)**

Để chủ động phát hiện lỗi, đội ngũ vận hành cần theo dõi sát các chỉ số sau trên Dashboard:
* **RPS / RPM (Requests Per Second/Minute):** Giám sát lưu lượng tải tổng thể của hệ thống.
* **Latency (Độ trễ):** Đo lường bách phân vị P95 và P99 (đảm bảo 95% hoặc 99% người dùng nhận được phản hồi dưới ngưỡng mục tiêu, ví dụ: 2 giây).
* **Error Rate (Tỷ lệ lỗi):** Giám sát mã lỗi HTTP 4xx (lỗi từ phía người dùng/logic nội bộ) và HTTP 5xx (lỗi từ hạ tầng hoặc Provider).
* **Token Usage & Rate Limits:** Cảnh báo khi mức tiêu thụ sắp chạm giới hạn API (*quota*) của nhà cung cấp LLM để có phương án nới hạn mức kịp thời.

**5. Kế hoạch Dự phòng Phân cấp (*Fallback Proposal*)**

Tuân thủ nguyên lý "Suy giảm nhẹ" (*Graceful Degradation*), hệ thống tuyệt đối không được để thất thoát khách hàng khi xảy ra sự cố. Kế hoạch Fallback được chia làm 3 mức độ (Tiers):

* **Mức 1 (Soft Fallback - Phục vụ tải cao hoặc suy giảm hiệu năng nhẹ):**
    * *Hành động:* Định tuyến toàn bộ luồng hội thoại về một mô hình LLM nhỏ hơn, có tốc độ xử lý nhanh hơn (ví dụ: chuyển từ model Pro sang model Flash), hoặc ưu tiên trả kết quả từ Cache không qua sinh văn bản mới.
* **Mức 2 (Hard Fallback - Hệ thống LLM ngừng hoạt động hoàn toàn):**
    * *Hành động:* Vô hiệu hóa ô nhập liệu tự do (*Free-text input*). Chuyển đổi giao diện sang Chatbot kịch bản tĩnh (*Rule-based*). Người dùng chỉ có thể tương tác qua các nút bấm định sẵn (ví dụ: [Xem bảng giá], [Tải Brochure], [Đặt lịch lái thử]). Đảm bảo phễu thông tin cơ bản không bị đứt gãy.
* **Mức 3 (Human Escalation - Xử lý đặc quyền cho nhóm High-intent/VIP):**
    * *Hành động:* Nếu hệ thống lỗi nhưng nhận diện được người dùng đã đăng nhập hoặc đang ở bước cấu hình xe chuẩn bị thanh toán → Tự động tạo Ticket khẩn cấp trên hệ thống CRM. Đồng thời, chuyển giao phiên chat (*hand-off*) ngay lập tức cho nhân viên tư vấn (*Live Agent*) kèm theo toàn bộ lịch sử trò chuyện để hỗ trợ hoàn tất giao dịch thủ công.