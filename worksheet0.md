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