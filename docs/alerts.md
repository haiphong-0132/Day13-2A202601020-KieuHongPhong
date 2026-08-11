# Template Alert và Runbook

Mỗi alert phải dựa trên triệu chứng người dùng hoặc SLO, không dựa trực tiếp vào tên implementation nội bộ.

## Alert 1

- Tên: high_latency_p95
- Severity: warning
- SLI/SLO liên quan: latency_p95_ms (Mục tiêu 99.5% requests dưới 3000ms)
- Điều kiện và thời gian duy trì: latency_p95 > 3000ms trong 5 phút.
- Ảnh hưởng tới người dùng: Ứng dụng phản hồi chậm, người dùng phải chờ đợi lâu khi chat.
- Ba bước kiểm tra đầu tiên:
  1. Kiểm tra Dashboard Metrics xem lưu lượng (traffic) có tăng đột biến không.
  2. Truy cập Langfuse, lọc các trace chậm để xem biểu đồ waterfall.
  3. Kiểm tra xem độ trễ nằm ở RAG (retrieve span) hay ở LLM (generate span).
- Mitigation tạm thời: Nếu do LLM chậm, chuyển đổi tạm thời sang mô hình nhẹ hơn (fallback). Nếu RAG quá tải, scale-up database hoặc cache.
- Owner: on-call-engineer

## Alert 2

- Tên: elevated_error_rate
- Severity: critical
- SLI/SLO liên quan: error_rate_pct (Mục tiêu dưới 2% lỗi)
- Điều kiện và thời gian duy trì: error_rate_pct > 5 trong 3 phút.
- Ảnh hưởng tới người dùng: Không nhận được phản hồi, người dùng bị báo lỗi hệ thống liên tục.
- Ba bước kiểm tra đầu tiên:
  1. Kiểm tra Dashboard (bảng Error breakdown) để xem loại hình lỗi nào (Timeout, RateLimit, ValidationError) tăng nhiều nhất.
  2. Mở Logs (`data/logs.jsonl`), dùng công cụ tìm các dòng log có sự kiện `request_failed`.
  3. Tìm `correlation_id` của request bị lỗi trong logs để truy vết ngược toàn bộ trace chi tiết trên Langfuse.
- Mitigation tạm thời: Khởi động lại service nếu rò rỉ bộ nhớ hoặc lỗi connection. Bật chế độ degraded (tắt một phần RAG) nếu database offline.
- Owner: on-call-engineer

## Alert 3

- Tên: cost_budget_exceeded
- Severity: warning
- SLI/SLO liên quan: daily_cost_usd (Ngân sách tối đa $2.5/ngày)
- Điều kiện và thời gian duy trì: daily_cost_usd > 2.5
- Ảnh hưởng tới người dùng: Người dùng có thể không bị ảnh hưởng trực tiếp, nhưng công ty sẽ vượt quá ngân sách tài chính ngoài dự kiến.
- Ba bước kiểm tra đầu tiên:
  1. Kiểm tra Dashboard ở biểu đồ Token xem lưu lượng input hay output token đột ngột tăng mạnh.
  2. Kiểm tra Logs để tìm xem có session hay user_id nào đang gửi lượng request tăng vọt bất thường.
  3. Xem xét Langfuse để phát hiện việc lạm dụng prompt làm token sinh ra bị thổi phồng.
- Mitigation tạm thời: Bật giới hạn rate-limiting gắt gao hơn. Tạm chuyển LLM sang model chi phí thấp hơn (ví dụ: Claude Haiku).
- Owner: team-lead
