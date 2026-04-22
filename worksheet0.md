### Khởi động và mini reflection
- Nhóm mạnh ở tư duy thiết kế và phối hợp `multi-agent`, có thể chia vai trò agent tương đối rõ và hình dung được luồng xử lý nhiều bước.
- Nhóm đã quen hơn với việc phân rã bài toán thành các tác vụ nhỏ, biết cách tận dụng từng agent cho các nhiệm vụ khác nhau thay vì dồn toàn bộ vào một luồng duy nhất.
- Điểm yếu hiện tại là `monitoring`: nhóm chưa xác định rõ cần theo dõi metric nào, cảnh báo ra sao và cách quan sát hệ thống khi chạy thật.
- Điểm yếu tiếp theo là `evaluation`: nhóm chưa có khung đánh giá đầu ra đủ rõ, thiếu tiêu chí đo chất lượng, độ ổn định và hiệu quả của toàn hệ thống agent.
- Hướng nên đi sâu tiếp theo là xây dựng quy trình `monitor + eval` cho `multi-agent system`, bao gồm log theo từng bước, đo chất lượng đầu ra của từng agent và xác định điểm lỗi trong toàn pipeline.

### Learning timeline
- Đến thời điểm hiện tại, nhóm cảm thấy tự tin nhất ở ba mảng chính: thiết kế luồng `multi-agent`, phân rã tác vụ theo từng vai trò rõ ràng, và xây dựng chatbot có thể trả lời bám theo đúng ngữ cảnh nghiệp vụ.
- Trong quá trình học và làm bài, nhóm đã chọn phát triển một chatbot hỗ trợ khách hàng cho cửa hàng ô tô điện VinFast. Chatbot này được định hướng để hỗ trợ báo giá, tư vấn bảo hành và cung cấp thông số xe tùy theo nhu cầu cụ thể của người dùng.
- Đây cũng là chủ đề mà nhóm theo đuổi xuyên suốt trong ngày vì vừa đủ gần với bối cảnh doanh nghiệp thực tế, vừa giúp nhóm dễ liên hệ giữa phần thiết kế hệ thống và phần triển khai vận hành.

- Về bài toán cần giải quyết, sản phẩm hướng tới việc hỗ trợ đội ngũ tư vấn trả lời khách hàng nhanh hơn, nhất quán hơn và hạn chế sai sót ở những câu hỏi phổ biến trong cả giai đoạn trước bán hàng lẫn sau bán hàng.
- Nhóm xác định người dùng chính gồm ba nhóm: khách hàng đang tìm hiểu xe điện VinFast, khách hàng đã mua xe và cần hỏi thêm về bảo hành hoặc thông tin sử dụng, và nhân viên tư vấn đang cần một công cụ hỗ trợ phản hồi nhanh.
- Chủ đề này cũng khá phù hợp để phân tích `deployment` và `cost` vì có nhiều yếu tố gần với một hệ thống thật: dữ liệu sản phẩm, chính sách bảo hành, nhu cầu cập nhật thông tin thường xuyên và khả năng lưu lượng hỏi đáp tăng mạnh vào các thời điểm cao điểm. Nhờ vậy, nhóm có cơ sở rõ hơn để suy nghĩ về cách triển khai production, chi phí vận hành và độ ổn định của toàn bộ hệ thống.
