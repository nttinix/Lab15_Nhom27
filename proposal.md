## **PROJECT PROPOSAL: VINFAST CAR SELLING AI CONSULTANT**

### **1. Project Overview**
*   **Mục tiêu dự án:** Xây dựng một trợ lý AI tinh gọn, am hiểu sâu sắc các dòng xe và chính sách của VinFast, giúp giải quyết bài toán tư vấn bán hàng 24/7. Hệ thống được thiết kế không chỉ là một bản demo mà là một giải pháp cấp doanh nghiệp (enterprise-level) thực thụ, có khả năng scale và quản trị rủi ro.
*   **Đối tượng phục vụ:** Người dùng chính là các khách hàng tiềm năng có nhu cầu tìm hiểu, so sánh xe điện VinFast và lên lịch lái thử. 
*   **Giá trị cốt lõi:** Mang lại một luồng tư vấn liền mạch, chính xác, giúp đội ngũ sale giảm thiểu thời gian trả lời các câu hỏi lặp lại và tập trung vào các chốt sales quan trọng, lý giải vì sao đây là một case-study hoàn hảo để phân tích deployment và cost trong môi trường thực tế.

### **2. Enterprise Context**
Bối cảnh triển khai hệ thống AI cho một tập đoàn lớn như VinFast khác biệt hoàn toàn so với các bản demo thông thường. Hệ thống được thiết kế xoay quanh 3 trụ cột chiến lược:
*   **Chuyển đổi số:** Tích hợp sâu AI vào hệ thống CRM cũ và các quy trình phê duyệt nội bộ hiện có của VinFast. 
*   **Tối ưu vận hành & Tự động hóa:** Tự động hóa quy trình phân loại khách hàng (lead scoring) và giải đáp thông tin, thiết lập audit trail rõ ràng để truy vết dữ liệu. 
*   **Cá nhân hóa trải nghiệm (CX):** Đảm bảo dữ liệu hành vi của khách hàng được phân tích theo thời gian thực để đưa ra các gợi ý dòng xe phù hợp nhất, nhưng vẫn tuân thủ nghiêm ngặt nguyên tắc: **dữ liệu nhạy cảm không được phép rời khỏi tổ chức** trái phép.

### **3. Deployment Choice**
*   **Mô hình đề xuất: Hybrid Cloud**.
*   **Lý do:**
    1.  **Bảo mật dữ liệu nhạy cảm:** Thông tin định danh khách hàng (PII) và lịch sử tín dụng mua trả góp được xử lý tại On-premise để tránh rủi ro rò rỉ và ảnh hưởng đến khách hàng đầu tiên nếu xảy ra sự cố.
    2.  **Linh hoạt mở rộng:** Các tác vụ tư vấn thông thường và query thông tin xe sẽ được đẩy lên Cloud để tận dụng sức mạnh tính toán, đáp ứng yêu cầu linh hoạt khi có lượng truy cập lớn.

### **4. Cost Analysis (MVP + Growth)**
Chi phí của hệ thống AI không chỉ dừng ở API token mà là một bài toán tổng thể (Cost Anatomy).
*   **Giai đoạn MVP (Thử nghiệm):** 
    *   Chi phí tập trung chủ yếu vào **Token cost** (input/output cho LLM API) và **Compute/Storage** cho cơ sở dữ liệu vector chứa thông tin xe VinFast. 
    *   Dự phòng các **hidden cost** dễ bị lãng quên như chi phí Logging, Maintenance và Human Review (đội ngũ giám sát chất lượng AI).
*   **Giai đoạn Growth (Scale 5x - 10x user):** 
    *   Khi peak traffic tăng cao, phần chi phí tăng mạnh nhất sẽ là Token API và Compute. Cần có một cơ chế kiểm soát nghiêm ngặt cost driver lớn nhất này để tránh việc vượt quá ngân sách lạc quan ban đầu.

### **5. Optimization Plan**
Thay vì tối ưu theo phong trào, dự án chọn 3 chiến lược cốt lõi để áp dụng:
*   **Semantic Caching:** Áp dụng ngay lập tức cho các câu hỏi thường gặp (ví dụ: "Giá xe VF 8 là bao nhiêu?", "Chính sách thuê pin thế nào?"). Việc này giúp giảm trực tiếp lượng API request, tiết kiệm chi phí token và giảm độ trễ (latency) đáng kể.
*   **Model Routing:** Áp dụng cho từng loại tác vụ. Tác vụ đơn giản (chào hỏi, FAQ) dùng mô hình nhỏ, tốc độ cao; tác vụ phức tạp (so sánh cấu hình kỹ thuật sâu, tư vấn tài chính) được route sang các mô hình lớn hơn.
*   **Phân tầng User / Selective Inference:** Lọc và phân loại nhóm request. Với khách hàng VIP hoặc những luồng giao dịch có ý định mua (Purchase Intent) cao, hệ thống sẽ gọi LLM cấu hình mạnh nhất. Với các khách hàng chỉ lướt xem, hệ thống dùng rule-based hoặc LLM nhỏ.

### **6. Reliability Plan**
Hệ thống AI bán hàng cần đảm bảo không bỏ lỡ khách hàng ngay cả khi nhà cung cấp (provider) lỗi.
*   **Xử lý Traffic đột biến & Provider Timeout:** Hệ thống sẽ áp dụng cơ chế **Queue** cho các request có thể xử lý bất đồng bộ (async) và duy trì tính real-time cho giao tiếp trực tiếp.
*   **Cơ chế Retry / Circuit Breaker:** Khi API provider phản hồi chậm hoặc timeout, Circuit Breaker sẽ tự động ngắt kết nối tạm thời để tránh nghẽn toàn hệ thống, kết hợp Retry policy với khoảng thời gian lùi dần (exponential backoff).
*   **Quy trình Fallback (Escalation):** Nếu AI không thể trả lời hoặc nhận diện được sự bức xúc/nhạy cảm từ khách hàng, fallback proposal sẽ lập tức chuyển tiếp đoạn hội thoại tới **Live Agent (Tư vấn viên người thật)** (Human escalation) hoặc chuyển sang luồng rule-based flow để đảm bảo trải nghiệm khách hàng không bị đứt gãy.

### **7. Track Recommendation + Next Step**
*   **Định hướng Phase 2:** Kế hoạch chuyển dịch cấu trúc hiện tại sang kiến trúc **Multi-Agent** chuyên biệt hóa (Ví dụ: một Agent chuyên thông số kỹ thuật, một Agent chuyên chính sách tài chính).
*   **Hành động và kỹ năng cần thiết:** Dựa trên bản đồ kỹ năng (Skills Map), đội ngũ dự án cần tự đánh giá mức độ từ 1–5 ở ba mảng: **Business/Product, Infra/Data/Ops, và AI Engineering**. Để theo đuổi hướng Multi-Agent trong Phase 2, nhóm cần bù đắp ngay 2–3 kỹ năng chuyên sâu liên quan đến điều phối luồng dữ liệu (Data/Ops) và thiết kế hệ thống tương tác giữa các AI Agent.