# Báo cáo Day 13 Observability

## 1. Thông tin nhóm

- Tên nhóm: Cá nhân - Kiều Hồng Phong (2A202601020)
- Repository URL: https://github.com/haiphong-0132/Day13-2A202601020-KieuHongPhong
- Commit SHA cuối: [Điền commit SHA sau khi commit code]
- Thành viên và vai trò: Làm tất cả các vai trò.

## 2. Kết quả kỹ thuật

- Điểm `validate_logs.py`: 100/100
- Tổng số traces: Đạt yêu cầu (> 10 traces)
- Số PII leak còn lại: 0
- Link/đường dẫn dashboard: [Điền đường dẫn nếu dùng Grafana/Langfuse, hoặc ghi "Local Dashboard"]

## 3. Logging và tracing

- Evidence correlation ID: `submission/evidence/correlation_id.png` (Ảnh log có req-xxx)
- Evidence PII redaction: `submission/evidence/pii_redacted.png` (Ảnh log hiển thị `[REDACTED_...]`)
- Evidence trace waterfall: `submission/evidence/trace_waterfall.png` (Ảnh từ Langfuse)
- Giải thích một span đáng chú ý: Span `generation` chứa LLM call, cho thấy token usage, input prompt đã chèn context, và thời gian sinh text (latency).

## 4. Prompt versioning

- Prompt name: `day13-chat`
- Version/label baseline: `v1` / `baseline`
- Version/label candidate: `v2` / `candidate`
- Trace ID của mỗi version:
  - Baseline Trace ID: 284edf66853b88134bc2ddc3c2e3c11e
  - Candidate Trace ID: 31ada7a365682ab4c16d5bd22e2078bc
- Bằng chứng đổi label hoặc rollback: `submission/evidence/prompt_rollback.png`

## 5. Dashboard, SLO và alerts

- Kết quả `validate_dashboard.py`: Hợp lệ 6/6 panel
- Evidence dashboard: `submission/evidence/dashboard.png`
- SLO đã chọn và lý do: P95 Latency <= 2000ms (Đảm bảo 95% request trả về nhanh, phù hợp cho hệ thống Chat tương tác).
- Alert rules và runbook: 
  - Alert: Kích hoạt khi P95 Latency > 2000ms trong 3 phút.
  - Runbook: Kiểm tra trace xem span nào chậm (RAG hay LLM), nếu RAG chậm thì scale database hoặc kiểm tra index. Nếu LLM chậm thì fallback model nhẹ hơn.

## 6. Điều tra challenge

- Challenge ID: day13-k4-observability-v1
- Triệu chứng từ metrics: P95/P99 Latency của feature `monitoring` tăng đột biến lên trên 10s (10000ms - 13000ms), vượt xa SLO threshold (2000ms). Traffic có tính concurrent (5 requests).
- Trace ID liên quan: (Có thể xem trên Langfuse Dashboard phần traces bị quá hạn)
- Log line/correlation ID liên quan: Các request có feature = `monitoring` trong khoảng thời gian test (ví dụ req-60784268, req-f5d12ba4).
- Root cause: Trong `app/mock_rag.py`, mock retrieval bị chậm (`time.sleep(2.5)` mô phỏng slow DB/Vector Search). Tuy nhiên, hàm `agent.run()` là hàm đồng bộ (synchronous) lại được gọi trực tiếp bên trong route `async def chat(...)` của `app/main.py`. Điều này làm block toàn bộ event loop của FastAPI. Với concurrency=5, các request phải xếp hàng chờ nhau (2.5s -> 5s -> 7.5s -> 10s -> 12.5s) dẫn đến latency dồn ứ lên tới 13s.
- Fix action: Sửa `async def chat` thành `def chat` trong `app/main.py`, hoặc sử dụng `fastapi.concurrency.run_in_threadpool` để chạy `agent.run` không làm block event loop.
- Preventive measure: Set up alert rule cho latency P95 > 2s trên dashboard/alert manager. Đưa rule kiểm tra "không gọi blocking code trong async def" vào CI pipeline hoặc linter (e.g. `flake8-async`).

## 7. Đóng góp cá nhân

Với mỗi thành viên, ghi rõ nhiệm vụ và link commit/PR tương ứng.

| Thành viên | Phần việc | Commit/PR | Điều đã học |
|---|---|---|---|
| Kiều Hồng Phong | Làm toàn bộ lab (Logging, PII, Tracing, Dashboard, Investigate Incident) | [Link commit] | Cách config JSON logger, bind context vars, điều tra root cause chặn event loop. |
