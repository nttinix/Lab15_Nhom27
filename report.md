### Khởi động và mini reflection
- Nhóm mạnh ở tư duy thiết kế và phối hợp `multi-agent`, có thể chia vai trò agent tương đối rõ và hình dung được luồng xử lý nhiều bước.
- Nhóm đã quen hơn với việc phân rã bài toán thành các tác vụ nhỏ, biết cách tận dụng từng agent cho các nhiệm vụ khác nhau thay vì dồn toàn bộ vào một luồng duy nhất.
- Điểm yếu hiện tại là `monitoring`: nhóm chưa xác định rõ cần theo dõi metric nào, cảnh báo ra sao và cách quan sát hệ thống khi chạy thật.
- Điểm yếu tiếp theo là `evaluation`: nhóm chưa có khung đánh giá đầu ra đủ rõ, thiếu tiêu chí đo chất lượng, độ ổn định và hiệu quả của toàn hệ thống agent.
- Hướng nên đi sâu tiếp theo là xây dựng quy trình `monitor + eval` cho `multi-agent system`, bao gồm log theo từng bước, đo chất lượng đầu ra của từng agent và xác định điểm lỗi trong toàn pipeline.

### Learning timeline

- Nhóm tự tin nhất ở 3 mảng: thiết kế luồng `multi-agent`, phân rã tác vụ theo vai trò, và xây chatbot trả lời theo ngữ cảnh nghiệp vụ.
- Sản phẩm nhóm đã làm là chatbot hỗ trợ khách hàng cho cửa hàng ô tô điện VinFast. Chatbot có thể hỗ trợ báo giá, tư vấn bảo hành và cung cấp thông số xe theo nhu cầu người dùng.
- Chủ đề nhóm chọn để theo xuyên suốt cả ngày vẫn là chatbot hỗ trợ khách hàng cho cửa hàng ô tô điện VinFast.

- Sản phẩm này giải quyết bài toán gì: hỗ trợ đội ngũ tư vấn khách hàng trả lời nhanh, nhất quán và đúng thông tin cho các câu hỏi phổ biến trước bán hàng và sau bán hàng.
- Ai là người dùng chính: khách hàng đang tìm hiểu xe điện VinFast, khách hàng đang sở hữu xe cần hỏi về bảo hành, và nhân viên tư vấn cần công cụ hỗ trợ phản hồi nhanh.
- Vì sao chủ đề này phù hợp để phân tích deployment và cost: đây là một bài toán gần thực tế doanh nghiệp, có dữ liệu sản phẩm, thông tin bảo hành và lưu lượng hỏi đáp có thể tăng cao; vì vậy rất phù hợp để phân tích cách triển khai production, chi phí vận hành và độ ổn định hệ thống.

### Enterprise Deployment Clinic

- Bối cảnh tổ chức sử dụng hệ thống: chatbot được triển khai cho cửa hàng ô tô điện VinFast để hỗ trợ khách hàng trên website, fanpage hoặc kênh chat nội bộ của đội tư vấn.
- Dữ liệu hệ thống sẽ động đến: thông tin sản phẩm và phiên bản xe, bảng giá, chương trình khuyến mãi, chính sách bảo hành, câu hỏi của khách hàng, lịch sử hội thoại và có thể cả thông tin liên hệ như tên, số điện thoại hoặc nhu cầu mua xe.
- Mức độ nhạy cảm của dữ liệu: dữ liệu sản phẩm và thông số xe là mức nhạy cảm thấp đến trung bình; dữ liệu khách hàng, lịch sử tư vấn và thông tin liên hệ là dữ liệu nhạy cảm hơn vì liên quan đến quyền riêng tư và trải nghiệm chăm sóc khách hàng.
- 3 ràng buộc `enterprise` lớn nhất:
- Phải bảo vệ dữ liệu khách hàng và lịch sử hội thoại, tránh rò rỉ thông tin cá nhân ra ngoài hệ thống không kiểm soát.
- Câu trả lời phải nhất quán với chính sách giá, bảo hành và thông tin sản phẩm chính thức để tránh tư vấn sai gây ảnh hưởng kinh doanh.
- Hệ thống cần tích hợp được với các kênh hiện có như website, CRM hoặc hệ thống chăm sóc khách hàng để đội vận hành dễ sử dụng và theo dõi.
- Mô hình triển khai được chọn: `Hybrid`.
- Lý do 1: dữ liệu nội bộ quan trọng như thông tin khách hàng, lịch sử chat và kết nối CRM nên được giữ trong môi trường doanh nghiệp để tăng khả năng kiểm soát và tuân thủ.
- Lý do 2: phần suy luận mô hình hoặc một số dịch vụ AI có thể tận dụng hạ tầng `cloud` để linh hoạt mở rộng khi lưu lượng khách hàng tăng cao, đặc biệt trong các đợt ra mắt xe hoặc khuyến mãi.

### Cost Anatomy Lab
### Cost Optimization Debate
### Scaling & Reliability Tabletop
### Skills Map & Track Direction
