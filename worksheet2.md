### Khởi động và mini reflection
- Sau khi nhìn lại worksheet 0-1, nhóm đã có bức tranh khá rõ về bài toán chatbot hỗ trợ khách hàng cho cửa hàng ô tô điện VinFast.
- Điểm cần làm tiếp theo không phải chỉ là ước lượng token API, mà là bóc tách toàn bộ cost của hệ thống AI: hạ tầng, logging, human review, maintenance và cả chi phí vận hành khi traffic tăng.
- Với bài toán này, cost không nằm hoàn toàn ở model; phần dễ bị bỏ quên thường là kiểm soát chất lượng đầu ra, cập nhật knowledge base và quan sát hệ thống khi chạy thật.

### Bối cảnh MVP và giả định traffic

- Sản phẩm vẫn là chatbot VinFast hỗ trợ báo giá, bảo hành và thông tin sản phẩm.
- Giả định MVP có khoảng `250 user/ngày`.
- Mỗi user trung bình tạo `2.4 request/ngày`, nên tổng khoảng `600 request/ngày`.
- Traffic trung bình tương đương khoảng `0.007 request/giây`, nhưng peak giờ cao điểm hoặc khi có campaign có thể lên `0.2-0.3 request/giây`.
- Với luồng chat thực tế, phần lớn request không phải câu hỏi đơn lẻ mà là hội thoại nhiều lượt, nên cần tính thêm ngữ cảnh hội thoại và retrieval vào input token.

### Ước lượng token nếu dùng LLM API

- Input tokens trung bình mỗi request:
  - system prompt + policy: `150-250`
  - lịch sử hội thoại ngắn: `200-300`
  - context từ RAG / tài liệu sản phẩm: `300-500`
  - tổng ước lượng: `900 input tokens/request`
- Output tokens trung bình mỗi request:
  - câu trả lời ngắn cho FAQ: `150-250`
  - tổng ước lượng: `250 output tokens/request`
- Tổng theo ngày:
  - input: `600 x 900 = 540,000 tokens/ngày`
  - output: `600 x 250 = 150,000 tokens/ngày`
- Điểm đáng chú ý là input tokens thường tăng nhanh hơn dự đoán ban đầu vì chat history và tài liệu truy xuất có xu hướng phình ra khi hệ thống muốn trả lời đúng hơn.

### Các lớp cost của hệ thống

| Lớp cost | Nội dung | Ước lượng MVP/tháng |
| --- | --- | ---: |
| Token | LLM API cho input/output, có thể thêm embedding nhỏ | `~$15` |
| Compute | App server, API gateway, vector search, worker nhẹ | `~$80` |
| Storage | Lưu hội thoại, log, knowledge base, embedding index | `~$25` |
| Human review | Kiểm tra câu trả lời khó, gắn nhãn lỗi, escalations | `~$300` |
| Logging | Trace, dashboard, alert, quan sát lỗi/latency | `~$40` |
| Maintenance | Cập nhật prompt, sửa lỗi, refresh dữ liệu, eval | `~$200` |

- Tổng MVP ước lượng: khoảng `~$660/tháng`.
- Quy đổi thô: khoảng `~16.5 triệu VND/tháng` nếu lấy tỷ giá tròn `25,000 VND/USD`.
- Nếu nhóm dùng model đắt hơn hoặc phải gọi tool nhiều lần cho mỗi request, phần token có thể tăng mạnh hơn rất nhiều so với con số trên.

### Cost driver lớn nhất của hệ thống

- Ở mức MVP, cost driver lớn nhất không phải token mà là `human review + maintenance`.
- Lý do là traffic chưa cao, nhưng để chatbot trả lời đúng về giá, bảo hành và phiên bản xe, nhóm phải dành thời gian kiểm tra câu trả lời, cập nhật nguồn dữ liệu và sửa prompt liên tục.
- Nếu chuyển sang model lớn hơn hoặc workflow nhiều bước hơn, `token cost` có thể vượt lên thành driver lớn nhất.

### Hidden cost dễ bị quên nhất

- `Human review` là hidden cost dễ bị quên nhất, vì nó không xuất hiện ngay trong bảng giá API nhưng ăn rất nhiều thời gian khi hệ thống vẫn còn chưa ổn định.
- Một hidden cost khác là `logging + observability`, vì khi chatbot trả lời sai thì phải có trace để biết lỗi nằm ở prompt, retrieval, data hay model.
- Nhóm cũng dễ ước lượng quá lạc quan ở chỗ nghĩ rằng chỉ cần một prompt tốt là đủ, trong khi thực tế phải có quy trình eval, rerun, và cập nhật knowledge base liên tục.

### Khi user tăng 5x hoặc 10x

- Nếu tăng `5x`, tức khoảng `1,250 user/ngày` và `3,000 request/ngày`:
  - token cost tăng gần tuyến tính lên khoảng `~$75/tháng`
  - compute tăng vừa phải vì có thể scale theo batch và autoscaling
  - storage và logging tăng rõ rệt do số trace và hội thoại nhiều hơn
  - human review tăng nhưng không nhất thiết 5x nếu có cơ chế lọc những case khó
- Nếu tăng `10x`, tức khoảng `2,500 user/ngày` và `6,000 request/ngày`:
  - token cost trở thành phần tăng mạnh nhất theo volume
  - logging/observability cũng phình nhanh vì số lỗi tuyệt đối tăng theo traffic
  - compute bắt đầu cần tối ưu cache, batching, queue và rate limit
  - human review có thể trở thành bottleneck nếu tỷ lệ escalations không giảm
- Phần tăng mạnh nhất khi scale thường là `token + logging`, còn phần nguy hiểm nhất về mặt tổ chức là `human review` nếu nhóm không chuẩn hóa quy trình vận hành.

### Câu hỏi nhóm phải trả lời

- Cost driver lớn nhất của hệ thống là gì?
  - Ở MVP: `human review + maintenance`.
  - Ở scale lớn hơn: `token cost` và `logging/observability` tăng nhanh nhất.
- Hidden cost nào dễ bị quên nhất?
  - `Human review`, vì nó nằm ngoài API bill nhưng ngốn nhiều thời gian.
  - `Logging/monitoring`, vì không có trace thì không biết sai ở đâu.
- Đội có chỗ nào đang ước lượng quá lạc quan không?
  - Đang có thể quá lạc quan ở giả định rằng một prompt + một model là đủ.
  - Có thể đang đánh giá thấp số lượt hội thoại nhiều vòng, chi phí kiểm thử chất lượng, và công sức cập nhật dữ liệu giá/bảo hành.

### Kết luận ngắn

- Nếu chỉ nhìn token API thì hệ thống có vẻ rẻ.
- Nhưng khi bóc tách đầy đủ, chi phí thật sự nằm ở vận hành: kiểm tra chất lượng, quan sát lỗi, cập nhật tri thức và duy trì độ tin cậy của chatbot.
- Với bài toán VinFast, muốn scale bền vững thì phải tối ưu cả model lẫn quy trình review, không thể chỉ tối ưu token.