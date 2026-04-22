### **WORKSHEET 3: LỰA CHỌN CHIẾN LƯỢC TỐI ƯU HÓA CHI PHÍ**

**Bối cảnh:** Hệ thống VinFast Car Selling AI Consultant đối mặt với lượng truy cập lớn (đặc biệt trong các sự kiện ra mắt xe mới) và tỷ lệ câu hỏi lặp lại cao về thông số, giá cả. Do đó, việc áp dụng các chiến lược tối ưu hóa (*optimization*) là bắt buộc để duy trì tỷ suất hoàn vốn (ROI).

Dưới đây là 3 chiến lược phù hợp nhất cho bài toán này, đi kèm phân tích lợi ích và lộ trình áp dụng:

#### **1. Chiến lược: Bộ nhớ đệm ngữ nghĩa (*Semantic Caching*)**
* **Thành phần chi phí tiết kiệm:** Cắt giảm triệt để chi phí API token (cả *input* lẫn *output*) và giảm thiểu số lượng yêu cầu gọi đến mô hình ngôn ngữ lớn (LLM).
* **Lợi ích:** Tối ưu chỉ số TTFT (*Time To First Token*) giúp giảm mạnh độ trễ (*latency*), mang lại trải nghiệm phản hồi tức thì cho khách hàng. Rất hiệu quả để giảm tải cho hệ thống khi có các chiến dịch Marketing lớn (ví dụ: Livestream ra mắt VF 3) gây ra các đợt tăng vọt lượng truy cập (*traffic spikes*).
* **Trade-off (Đánh đổi):** Tốn thêm chi phí hạ tầng để duy trì cơ sở dữ liệu vector (*Vector Database*). Tồn tại rủi ro "ảo giác thông tin cũ" (ví dụ: chính sách giá/khuyến mãi vừa cập nhật trên hệ thống mẹ nhưng bộ nhớ đệm chưa kịp đồng bộ, dẫn đến AI báo giá sai cho khách).
* **Thời điểm áp dụng:** Sử dụng cho các luồng câu hỏi FAQ có tính chất cố định hoặc ít biến động: hỏi về thông số kỹ thuật (chiều dài cơ sở, dung lượng pin VF 8), vị trí showroom, hoặc chính sách bảo hành mặc định.

#### **2. Chiến lược: Điều phối mô hình (*Model Routing*)**
* **Thành phần chi phí tiết kiệm:** Tối ưu hóa chi phí suy luận (*inference cost*) bằng cách phân bổ tài nguyên hợp lý, tránh sử dụng các mô hình quá lớn cho các tác vụ đơn giản.
* **Lợi ích:** Đạt được sự cân bằng hoàn hảo giữa chi phí và chất lượng tư vấn (*Cost-Performance ratio*). Hệ thống linh hoạt thích ứng với độ phức tạp của từng nhóm truy vấn.
* **Trade-off:** Tăng độ phức tạp của kiến trúc hệ thống. Yêu cầu xây dựng một bộ định tuyến (*Router*) phân loại ý định đủ nhanh và chính xác, nếu không sẽ làm tăng độ trễ tổng thể. Cần thiết lập thêm cơ chế dự phòng (*Fallback mechanism*) trong trường hợp mô hình nhỏ trả lời sai hoặc không đủ khả năng xử lý.
* **Thời điểm áp dụng:** Tích hợp ngay tại cửa ngõ tiếp nhận tin nhắn.
    * **Luồng đơn giản:** Yêu cầu như hỏi giờ mở cửa, đặt lịch lái thử $\rightarrow$ Điều hướng tới các mô hình nhỏ/nhanh (ví dụ: Gemini Flash).
    * **Luồng phức tạp:** Yêu cầu so sánh chuyên sâu giữa VF 9 và xe đối thủ, hoặc tính toán bảng dòng tiền trả góp $\rightarrow$ Điều hướng tới các mô hình lớn (ví dụ: Gemini Pro).

#### **3. Chiến lược: Phân tầng người dùng/yêu cầu (*Selective Inference / User Tiering*)**
* **Thành phần chi phí tiết kiệm:** Tránh lãng phí tài nguyên điện toán (*Compute*) và các luồng truy xuất dữ liệu (*RAG*) đắt đỏ cho tệp người dùng có ý định mua thấp (*window shopping*).
* **Lợi ích:** Tối đa hóa tỷ lệ chuyển đổi (*Conversion Rate*) trên ngân sách AI. Tập trung "hỏa lực" điện toán để chăm sóc các khách hàng có tiềm năng chốt sale cao.
* **Trade-off:** Đòi hỏi phải tích hợp sâu và đồng bộ dữ liệu thời gian thực (*Real-time*) với hệ thống CRM của VinFast để xây dựng luồng chấm điểm khách hàng (*Lead Scoring*). Có rủi ro mang lại trải nghiệm kém cho người dùng mới nếu hệ thống đánh giá sai mức độ tiềm năng của họ.
* **Thời điểm áp dụng:** Kích hoạt khi người dùng có hành vi cụ thể (đăng nhập tài khoản VinFast, đang ở bước thanh toán cọc) hoặc có lịch sử tương tác tốt. Khách hàng VIP sẽ được cấp lượng cửa sổ ngữ cảnh (*context window*) dài hơn và truy xuất dữ liệu cá nhân hóa sâu hơn.

---

### **KẾT LUẬN & LỘ TRÌNH TRIỂN KHAI (ACTION PLAN)**

Dựa trên nguyên tắc "Low-hanging fruit" (Ưu tiên các hạng mục dễ thực hiện, mang lại giá trị ngay), lộ trình tích hợp hệ thống được đề xuất như sau:

**Giai đoạn 1: Ưu tiên triển khai ngay (*Short-term / Phase 1*)**
* **Semantic Caching:** Đây là ưu tiên số 1 vì dữ liệu xe hơi có tính lặp lại cực kỳ cao. Việc triển khai tương đối độc lập, ít ảnh hưởng đến luồng logic cốt lõi nhưng giúp giảm trực tiếp chi phí API ngay trong tháng đầu vận hành.
* **Model Routing:** Bắt đầu triển khai ngay với các bộ định tuyến dựa trên bộ quy tắc đơn giản (*Rule-based router* phân tích theo từ khóa) trước khi nâng cấp lên *Semantic Router* phức tạp.

**Giai đoạn 2: Lộ trình dài hạn (*Long-term / Phase 2*)**
* **Phân tầng người dùng:** Chiến lược này yêu cầu sự trưởng thành về mặt dữ liệu (*Data Maturity*). VinFast cần thời gian để hệ thống AI Consultant thu thập đủ dữ liệu nhật ký (*log*), từ đó phối hợp với đội Data/CRM để xây dựng mô hình *Lead Scoring* chuẩn xác trước khi áp dụng phân tầng. Tích hợp vội vã dễ dẫn đến sai lệch trong hành trình khách hàng.

> **Tóm tắt:** Việc kết hợp cả 3 chiến lược này theo đúng lộ trình không chỉ giúp VinFast kiểm soát chặt chẽ ngân sách vận hành AI mà còn đảm bảo chất lượng tư vấn luôn ở mức cao nhất đối với các tệp khách hàng trọng tâm.