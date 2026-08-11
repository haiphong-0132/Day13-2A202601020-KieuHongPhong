Lab 13 — Observability cho hệ thống AI: Metrics, Traces & Logs

Tập trung

Ghi chú
0

Hỗ trợ

Tiếng Việt

2

Quay lại

Tiếp
1. Bức tranh toàn cảnh & Thuật ngữ
Bản đồ Lab

Đọc trước khi bắt đầu
240 phút
Trung cấp
Trong 4 giờ, nhóm bạn sẽ biến một AI API có sẵn (dùng mock LLM — không cần API key bên ngoài) thành một hệ thống observable: log JSON có cấu trúc, metrics theo dõi latency/cost/quality, traces trên Langfuse, dashboard với SLO line, và cuối cùng là điều tra một incident thực tế do Lab Coach release.

Bài này đang nói về điều gì?
Một hệ thống AI chạy được không có nghĩa là bạn biết nó đang chạy thế nào. Observability là khả năng trả lời 'chuyện gì đang xảy ra bên trong?' chỉ bằng dữ liệu đầu ra — log, metrics, traces — mà không cần debug hay đọc code.

Ba trụ cột của Observability: Metrics phát hiện triệu chứng (latency tăng, error rate cao), Traces khoanh vùng vị trí (span nào chậm, service nào lỗi), Logs giải thích nguyên nhân (lỗi cụ thể, dữ liệu đầu vào). Luồng điều tra luôn đi theo hướng Metrics → Traces → Logs.

Structured Logging
Correlation ID & PII
Metrics & Traces
Dashboard & SLO
Incident Investigation
Buổi Lab diễn ra như thế nào?
0:00–0:30
Nhóm
Setup & Baseline
Fork repo, cài đặt venv, chạy API và load test, ghi nhận baseline từ validate_logs.py.

0:30–1:30
Nhóm
Logging, Correlation ID & PII
Hoàn thiện middleware, enrichment, PII scrubbing. Target: validate_logs.py ≥ 80/100.

1:30–2:30
Nhóm
Metrics, Traces, Dashboard & Alerts
Tạo traces trên Langfuse, thiết kế dashboard 6 nhóm chỉ số, viết SLO và alert runbook.

2:30–3:30
Nhóm
Challenge: Điều tra Incident
Chạy challenge chính thức sau khi Coach release, điều tra theo luồng Metrics → Traces → Logs.

3:30–4:00
Nhóm
Báo cáo & Nộp bài
Hoàn thiện REPORT.md, kiểm tra evidence, commit, push, nộp link.

Kết thúc bài, bạn có gì?
Hệ thống AI có log JSON sạch, không lộ PII, mỗi request có correlation ID duy nhất.
Hiểu cách dùng luồng Metrics → Traces → Logs để điều tra một incident từ triệu chứng đến root cause.
Chưa cần lo

App dùng mock LLM nên phần practice không cần API key trả phí. Không có Langfuse key, app vẫn chạy và bạn vẫn làm được logging, metrics, PII — chỉ thiếu bằng chứng trace. Kẹt quá 10 phút thì gọi Lab Coach và đi tiếp checkpoint sau.

Chuẩn bị trước (3 hướng dẫn)
Nếu bị chặn
Bài lab này biến một AI API chạy được nhưng khó quan sát thành hệ thống có thể theo dõi, phát hiện sự cố và giải thích nguyên nhân bằng bằng chứng, qua 4 checkpoint:

API chạy được

Structured Logging

Correlation ID & PII

Metrics & Traces

Dashboard & SLO

Incident Investigation

Root Cause + Evidence ✅

Tải starter repo tương ứng với lớp của bạn:

Lớp K3: Day13-K3-Observability
Lớp K4: Day13-K4-Observability
Fork repo về tài khoản nhóm (hoặc một thành viên đại diện fork rồi mời các thành viên khác làm collaborator). Đặt tên repository của nhóm theo đúng chuẩn: <code>KX-DAY13-[Mã_SV_Nhóm_Trưởng]</code> (Ví dụ: <code>K4-DAY13-2A2026....</code>). Clone bản fork về máy local rồi mở đúng thư mục dự án trong VS Code.

Bảng thuật ngữ trực quan
Thuật ngữ	Bản chất	Minh hoạ trong lab
Structured Logging	Log dạng JSON thay vì text tự do	Mỗi dòng log là JSON object với ts, level, event, service — máy parse được, người đọc được, Grafana/Datadog ingest được.
Correlation ID	Mã định danh duy nhất theo từng request	Client gửi header x-request-id hoặc server tạo req-<8hex>. Tất cả log, trace, response đều mang cùng ID → truy vết xuyên suốt.
PII Redaction	Che giấu dữ liệu cá nhân trước khi ghi log	Email student@vinuni.edu.vn → [REDACTED_EMAIL], số điện thoại → [REDACTED_PHONE_VN]. Vi phạm PII trong production có thể phạt pháp lý.
Metrics	Số đo tổng hợp: đếm, trung bình, phân vị	latency_p95 = 2500ms, error_rate = 3.2%, total_cost = $1.42. Dùng để phát hiện triệu chứng ("có gì đó sai").
Trace / Span	Bản ghi toàn bộ hành trình một request	Trace gồm các span (ví dụ span cha run, hoặc các span con retrieve, generate nếu mở rộng). Dùng để khoanh vùng vị trí ("span nào chậm").
SLI / SLO	Chỉ số chất lượng / mục tiêu cam kết	SLI: latency P95. SLO: 99.5% request dưới 3000ms trong 28 ngày. Vượt ngưỡng → cần hành động.
Percentile (P50/P95/P99)	Phân vị: bao nhiêu % request nằm dưới ngưỡng	P95 = 2500ms nghĩa là 95% request nhanh hơn 2.5 giây. 5% còn lại là "tail latency".
Alert Runbook	Sách hướng dẫn xử lý khi alert kêu	Alert "latency_p95 > 3s trong 5 phút" → kiểm tra trace → tìm span RAG → restart nếu cần.
Langfuse	Platform theo dõi LLM applications	Ghi nhận trace, generation, cost, quality cho mỗi lần gọi LLM. Dashboard và waterfall có sẵn.
Cấu trúc thư mục repo (Dấu ★ = file bạn phải sửa)
Day13-K4-Observability/
├── app/                        # ★ NƠI BẠN VIẾT CODE
│   ├── main.py                 #   ★ CP1 — enrich log context
│   ├── middleware.py            #   ★ CP1 — correlation ID
│   ├── logging_config.py        #   ★ CP1 — uncomment PII scrubber
│   ├── pii.py                   #   ★ CP1 — thêm PII patterns
│   ├── metrics.py               #   Cho sẵn — bộ đếm metrics
│   ├── tracing.py               #   Cho sẵn — adapter Langfuse
│   ├── agent.py                 #   Cho sẵn — LabAgent + observe
│   ├── schemas.py               #   Cho sẵn — request/response models
│   ├── mock_llm.py              #   Cho sẵn — LLM giả
│   ├── mock_rag.py              #   Cho sẵn — RAG retriever giả
│   ├── incidents.py             #   Cho sẵn — bật/tắt incident
│   └── challenge.py             #   Cho sẵn — load challenge config
├── config/
│   ├── logging_schema.json      #   Cho sẵn — JSON schema cho log
│   ├── slo.yaml                 #   ★ CP2 — điều chỉnh SLO
│   └── alert_rules.yaml         #   ★ CP2 — điền 3 alert rules
├── data/
│   ├── sample_queries.jsonl     #   Cho sẵn — dữ liệu load test
│   └── logs.jsonl               #   Sinh ra khi chạy API
├── docs/
│   ├── GUIDE.md                 #   Gợi ý khi bị kẹt
│   ├── dashboard-spec.md        #   ★ CP2 — thiết kế dashboard
│   ├── alerts.md                #   ★ CP2 — viết runbook 3 alerts
│   └── grading-evidence.md      #   Danh sách evidence cần thu thập
├── scripts/
│   ├── load_test.py             #   Gửi batch request tới API
│   ├── inject_incident.py       #   Bật incident (practice hoặc challenge)
│   └── validate_logs.py         #   Kiểm tra chất lượng log
├── tests/                       #   Public tests
├── submission/
│   ├── REPORT.md                #   ★ Báo cáo nộp bài
│   └── evidence/                #   ★ Ảnh chụp, log, trace evidence
├── requirements.txt
├── .env.example
└── .gitignore
Copy
Bảng chấm điểm nhanh (100 điểm)
Phần	Nội dung	Điểm
A1	Triển khai kỹ thuật: logging, correlation ID, PII, traces, dashboard, SLO, alerts	30
A2	Điều tra incident: đúng triệu chứng, root cause, luồng M→T→L	10
A3	Demo và giải thích: hệ thống chạy, evidence đúng, giải thích được	20
B1	Báo cáo cá nhân: mô tả phần việc, trả lời câu hỏi	20
B2	Bằng chứng đóng góp: commit/PR cụ thể, khớp với report	20
Tổng		100
BONUS	Cost optimization, automation, audit log	+10
Hướng dẫn phân chia vai trò trong nhóm (Roles & Team Division)
Vì đây là bài thực hành nhóm (Tối đa 100 điểm, chia làm 60 điểm nhóm và 40 điểm cá nhân), việc phân chia công việc rõ ràng là vô cùng quan trọng. Dưới đây là gợi ý phân vai theo quy mô nhóm của bạn:

Nhóm 3 thành viên:
Thành viên A (Tech Lead/Backend Engineer): Phụ trách CP1 (Xây dựng Middleware, gán Correlation ID, Enrichment logs).
Thành viên B (SRE & Alerts Engineer): Phụ trách CP2 (Cấu hình Langfuse, thiết lập SLO/Alert Rules, viết tài liệu Alert Runbook).
Thành viên C (QA & Chief Investigator): Thiết kế Dashboard Spec, thực hiện load test, quản lý Challenge/Practice Incident (CP3) và tổng hợp báo cáo nhóm.
Nhóm 4 thành viên:
Thành viên A (Logging & Middleware): Phụ trách CP1 (Middleware, Correlation ID, và gán log metadata).
Thành viên B (Security & Compliance): Phụ trách CP1 (Uncomment processor, cấu hình regex patterns che PII và nâng cấp che PII toàn cục).
Thành viên C (Metrics & Alerting): Phụ trách CP2 (Tích hợp Langfuse, đo đếm error_rate_pct, viết SLO, Alert rules và Runbook).
Thành viên D (QA & Incident Analyst): Chạy load test sinh dữ liệu, thiết kế Dashboard Spec, chủ trì điều tra Challenge (CP3) và viết báo cáo REPORT.md.
Lab 13 — Observability cho hệ thống AI: Metrics, Traces & Logs

Tập trung

Ghi chú
0

Hỗ trợ

Tiếng Việt

2

Quay lại

Tiếp
3. Block 1 — Structured Logging, Correlation ID & PII (Checkpoint CP1)
⏱ Thời gian: 60 phút · Bắt đầu: 0:30

Tại block này, chúng ta sẽ bắt tay xử lý 3 vấn đề cốt lõi của log hệ thống: (1) gán mã định danh (Correlation ID) cho từng request để truy vết toàn trình, (2) làm giàu (enrich) metadata log để phục vụ bộ lọc/phân tích, và (3) lọc bỏ dữ liệu nhạy cảm (PII scrubbing) trước khi ghi log.

Bước 1 — Correlation ID Middleware
Mở file app/middleware.py. Hiện tại correlation ID luôn là "MISSING". Bạn cần hoàn thành 4 TODO:

class CorrelationIdMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        # 1. Xóa context cũ để tránh leak giữa các request
        clear_contextvars()

        # 2. Lấy từ header hoặc tạo mới, format: req-<8 ký tự hex>
        correlation_id = request.headers.get(
            "x-request-id",
            f"req-{uuid.uuid4().hex[:8]}"
        )

        # 3. Bind vào structlog context — mọi log sau đó tự động có trường này
        bind_contextvars(correlation_id=correlation_id)

        request.state.correlation_id = correlation_id

        start = time.perf_counter()
        response = await call_next(request)

        # 4. Trả correlation ID và thời gian xử lý trong response header
        response.headers["x-request-id"] = correlation_id
        response.headers["x-response-time-ms"] = f"{(time.perf_counter() - start) * 1000:.1f}"

        return response
Copy
Tại sao cần clear_contextvars()? Vì structlog dùng context variables (Python contextvars) để chia sẻ thông tin logs trong một luồng request.

💡 Tương tự trực quan: Hãy tưởng tượng contextvars giống như một chiếc túi xách đi kèm với mỗi request. Khi request bắt đầu, ta cho vào túi các thông tin chung (như ID request, ID user). Mỗi hàm xử lý bên trong chỉ cần thò tay vào túi lấy ra dùng mà không cần phải truyền biến thủ công qua từng hàm.

Tuy nhiên, nếu không gọi clear_contextvars() ở đầu mỗi request mới, luồng xử lý (thread/task) có thể dùng lại "chiếc túi cũ" của request trước đó, gây rò rỉ dữ liệu (data leakage).

Lưu ý
·
Phần mở rộng (Không bắt buộc): Đảm bảo giữ Correlation ID khi xảy ra lỗi
Trong trường hợp API xảy ra lỗi hệ thống (ví dụ: lỗi tool_fail trả về HTTP 500), FastAPI mặc định sẽ tự tạo error response chung như {"detail": "RuntimeError"} và bỏ qua các header được gán trong Middleware thông thường. Việc này làm client (hoặc script load_test.py) nhận về correlation ID là None, gây khó khăn cho việc tra cứu log.

Để xử lý triệt để, bạn có thể thực hiện phần mở rộng sau:

Mở app/main.py và thêm một generic exception handler để đính kèm x-request-id vào header của response lỗi:
from fastapi.responses import JSONResponse

@app.exception_handler(Exception)
async def generic_exception_handler(request: Request, exc: Exception) -> JSONResponse:
    correlation_id = getattr(request.state, "correlation_id", "unknown")
    return JSONResponse(
        status_code=500,
        content={"detail": type(exc).__name__},
        headers={"x-request-id": correlation_id},
    )
Copy
Mở scripts/load_test.py và sửa dòng hiển thị kết quả (khoảng dòng 21) để ưu tiên đọc correlation ID từ header của response:
# Thay dòng print cũ thành:
cid = r.headers.get("x-request-id") or r.json().get("correlation_id", "None")
print(f"[{r.status_code}] {cid} | {payload['feature']} | {latency:.1f}ms")
Copy
Bước 2 — Enrich log context
Mở file app/main.py, tìm hàm chat(). Thêm bind_contextvars trước dòng log.info("request_received", ...) để mọi log trong request đó tự động có metadata:

@app.post("/chat", response_model=ChatResponse)
async def chat(request: Request, body: ChatRequest) -> ChatResponse:
    # Enrich — tất cả log sau đây tự động có các trường này
    bind_contextvars(
        user_id_hash=hash_user_id(body.user_id),
        session_id=body.session_id,
        feature=body.feature,
        model="claude-sonnet-4-5",
        env=os.getenv("APP_ENV", "dev"),
    )

    log.info(
        "request_received",
        service="api",
        payload={"message_preview": summarize_text(body.message)},
    )
    # ... phần còn lại giữ nguyên
Copy
Lưu ý: Dùng hash_user_id(body.user_id) thay vì body.user_id trực tiếp — đây là lớp bảo vệ PII đầu tiên: chỉ log hash SHA-256 của user ID.

Bước 3 — Bật PII Scrubbing
Mở file app/logging_config.py, tìm danh sách processors trong configure_logging(). Uncomment dòng scrub_event:

structlog.configure(
    processors=[
        merge_contextvars,
        structlog.processors.add_log_level,
        structlog.processors.TimeStamper(fmt="iso", utc=True, key="ts"),
        scrub_event,  # ← Uncomment dòng này
        structlog.processors.StackInfoRenderer(),
        structlog.processors.format_exc_info,
        JsonlFileProcessor(),
        structlog.processors.JSONRenderer(),
    ],
    # ...
)
Copy
Lưu ý
·
Thứ tự các Processors trong Structlog
Trong Structlog, các processor hoạt động tuần tự từ trên xuống dưới giống như một dây chuyền sản xuất:

TimeStamper (Bước trước): Thêm trường thời gian "ts" vào log dict.
scrub_event (Bước của bạn): Phải nằm sau TimeStamper để tránh lãng phí hiệu năng quét dữ liệu thời gian.
JsonlFileProcessor & JSONRenderer (Bước sau): Phải nằm sau scrub_event. Hai processor này sẽ serialize log dict thành chuỗi JSON để ghi xuống file/console. Nếu đặt scrub_event sau chúng, log đã được ghi xuống file trước khi kịp che PII!
Bước 3b — Mở rộng phạm vi che PII (PII Scrubbing Extension)
Hàm scrub_event mặc định trong starter code chỉ che PII trong payload và event. Để đảm bảo an toàn tuyệt đối cho mọi trường log (như session_id, user_id_hash, v.v.), hãy thay thế hàm scrub_event trong app/logging_config.py bằng logic an toàn hơn dưới đây để duyệt qua mọi trường dạng string và dictionary:

def scrub_event(_: Any, __: str, event_dict: dict[str, Any]) -> dict[str, Any]:
    for key, val in event_dict.items():
        if isinstance(val, str):
            event_dict[key] = scrub_text(val)
        elif isinstance(val, dict):
            event_dict[key] = {
                k: scrub_text(v) if isinstance(v, str) else v for k, v in val.items()
            }
    return event_dict
Copy
Bước 4 — Thêm PII patterns
Mở file app/pii.py, thêm các regex pattern mới vào PII_PATTERNS để nhận diện Passport và Địa chỉ Việt Nam:

PII_PATTERNS: dict[str, str] = {
    "email": r"[\w\.-]+@[\w\.-]+\.\w+",
    "phone_vn": r"(?:\+84|0)[ \.-]?\d{3}[ \.-]?\d{3}[ \.-]?\d{3,4}",
    "cccd": r"\b\d{12}\b",
    "credit_card": r"\b\d{4}[- ]?\d{4}[- ]?\d{4}[- ]?\d{4}\b",
    # Thêm patterns mới:
    "passport": r"\b[A-Z]\d{7,8}\b",
    "address_vn": r"\b(?:số nhà|đường|phường|quận|huyện|tỉnh|thành phố)\b",
}
Copy
Bước 5 — Liên kết Correlation ID vào Langfuse Trace
Để sau này ta có thể đối chiếu giữa Trace trên Langfuse và Log thô trong hệ thống, ta cần đính kèm correlation_id vào thông tin metadata của Trace.

Mở file app/agent.py, tìm hàm run(). Trong phần cập nhật trace trên Langfuse, hãy import và đính kèm correlation_id lấy từ structlog.contextvars:

        # Thêm import ở đầu file hoặc bên trong hàm:
        from structlog.contextvars import get_contextvars

        langfuse_client = get_langfuse_client()
        langfuse_client.update_current_trace(
            user_id=hash_user_id(user_id),
            session_id=session_id,
            tags=["lab", feature, self.model],
            metadata={"correlation_id": get_contextvars().get("correlation_id", "MISSING")},
        )
Copy
Bước 6 — Bổ sung tỷ lệ lỗi (Error Rate) vào Metrics
Để phục vụ cho dashboard giám sát và alert rule cảnh báo lỗi hệ thống, ta cần tính toán được tỷ lệ lỗi (error_rate_pct) của API.

Mở file app/metrics.py, tìm hàm snapshot(). Hãy sửa đổi hàm này để tính toán tỷ lệ phần trăm lỗi từ tổng số request (request thành công TRAFFIC + request thất bại lưu trong ERRORS):

def snapshot() -> dict:
    total_errors = sum(ERRORS.values())
    total_requests = TRAFFIC + total_errors
    error_rate = (total_errors / total_requests * 100) if total_requests > 0 else 0.0

    return {
        "traffic": TRAFFIC,
        "latency_p50": percentile(REQUEST_LATENCIES, 50),
        "latency_p95": percentile(REQUEST_LATENCIES, 95),
        "latency_p99": percentile(REQUEST_LATENCIES, 99),
        "avg_cost_usd": round(mean(REQUEST_COSTS), 4) if REQUEST_COSTS else 0.0,
        "total_cost_usd": round(sum(REQUEST_COSTS), 4),
        "tokens_in_total": sum(REQUEST_TOKENS_IN),
        "tokens_out_total": sum(REQUEST_TOKENS_OUT),
        "error_rate_pct": round(error_rate, 2),  # ← Thêm trường này
        "error_breakdown": dict(ERRORS),
        "quality_avg": round(mean(QUALITY_SCORES), 4) if QUALITY_SCORES else 0.0,
    }
Copy
Kiểm tra kết quả
Xóa log cũ để tránh dữ liệu cũ làm sai kết quả chấm điểm của script validator:

macOS/Linux:

rm -f data/logs.jsonl
Copy
Windows PowerShell:

Remove-Item -Path data/logs.jsonl -ErrorAction SilentlyContinue
Copy
Khởi động lại Uvicorn:

# Bấm Ctrl+C ở Terminal 1 để tắt uvicorn cũ, rồi chạy lại:
uvicorn app.main:app --reload --env-file .env
Copy
Terminal 2:

python scripts/load_test.py
python scripts/validate_logs.py
Copy
Mục tiêu: score ≥ 80/100. Kiểm tra cụ thể:

correlation_id không còn "MISSING" → format req-<8hex>
Các trường user_id_hash, session_id, feature, model xuất hiện trong log request_received
Email và số điện thoại trong sample queries đã bị thay bằng [REDACTED_...]
Kiểm tra PII bằng tay:

grep -i "@" data/logs.jsonl    # Không nên có kết quả
grep "4111" data/logs.jsonl    # Không nên có kết quả
grep "REDACTED" data/logs.jsonl  # Phải có kết quả
Copy
Tự kiểm
·
✅ CHECKPOINT CP1 — Structured Logging, Correlation ID & PII
Tiêu chí nghiệm thu:

Kết quả kiểm tra: Chạy python scripts/validate_logs.py đạt tối thiểu 80/100 điểm và python -m pytest -q trả về kết quả pass hoàn toàn.
Bằng chứng (Evidence): Ảnh chụp màn hình điểm log validator và một đoạn log chứa correlation ID kèm chuỗi che thông tin [REDACTED_...] lưu trong thư mục submission/evidence/.
Câu hỏi phản biện: Mô tả sự khác biệt lớn nhất giữa log baseline (CP0) và log sau khi làm xong CP1. Tại sao bước gọi clear_contextvars() ở đầu middleware lại mang tính bắt buộc?

Nhóm 5 thành viên:
Thành viên A (API & Middleware): CP1 Middleware, gán Correlation ID, và bổ sung exception handler (phần mở rộng).
Thành viên B (Security Engineer): CP1 PII Scrubbing, regex patterns và kiểm chứng log không lộ PII.
Thành viên C (Metrics & Dashboard): CP1/CP2 đo đếm error_rate_pct và thiết kế spec Dashboard 6 nhóm chỉ số.
Thành viên D (SRE & Alerts Engineer): CP2 Thiết lập SLO, viết Alerts rules và Alert Runbook xử lý sự cố.
Thành viên E (QA & Chief Investigator): Chạy load test, bọc trace cho sub-component RAG/LLM (phần mở rộng), dẫn dắt điều tra Challenge (CP3) và hoàn thiện báo cáo nhóm.
Tự kiểm
·
✅ Hiểu bài trước khi bắt tay
Mục tiêu: Nắm được ba trụ cột Observability và flow điều tra.

Ba trụ cột là gì? (Metrics, Traces, Logs) — Vai trò của từng cái?
Tại sao điều tra incident luôn đi theo hướng Metrics → Traces → Logs, chứ không ngược lại?
Correlation ID giải quyết bài toán gì khi hệ thống có nhiều service?

Lab 13 — Observability cho hệ thống AI: Metrics, Traces & Logs

Tập trung

Ghi chú
0

Hỗ trợ

Tiếng Việt

2

Quay lại

Tiếp
2. Block 0 — Setup & Baseline (Checkpoint CP0)
⏱ Thời gian: 30 phút · Bắt đầu: 0:00

Tạo môi trường
Fork repo Day13-K4-Observability về tài khoản GitHub của nhóm. Một thành viên fork, rồi mời cả nhóm làm collaborator trong Settings → Collaborators. Clone bản fork về máy:

git clone https://github.com/<your-team>/Day13-K4-Observability.git
cd Day13-K4-Observability
Copy
Tạo virtual environment và cài dependencies:

macOS/Linux:

python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
cp .env.example .env
Copy
Windows PowerShell:

python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Copy-Item .env.example .env
Copy
Cấu hình Langfuse (Bắt buộc để thu thập Traces)
Nếu Lab Coach cung cấp key chung, điền vào .env. Nếu không, tự đăng ký Langfuse Cloud (miễn phí), tạo project rồi lấy key tại Settings -> API Credentials trong giao diện quản trị Langfuse:

LANGFUSE_PUBLIC_KEY=pk-lf-...
LANGFUSE_SECRET_KEY=sk-lf-...
LANGFUSE_HOST=https://cloud.langfuse.com
Copy
Lưu ý
·
Lưu ý về Langfuse Key
Nộp bằng chứng trace và waterfall từ Langfuse là yêu cầu bắt buộc trong hướng dẫn nộp bài của repository (SUBMISSION.md, docs/grading-evidence.md). Hãy đăng ký tài khoản Langfuse Cloud miễn phí và điền key để đảm bảo nhận được trọn vẹn điểm số phần Traces.

Chạy API và lấy baseline
Lưu ý
·
Tránh xung đột cổng 8000
Trước khi chạy API, đảm bảo bạn đã tắt tất cả các ứng dụng uvicorn hoặc container Docker chạy ở cổng 8000 từ bài lab Day 12 trước đó (dùng lệnh docker compose down ở repo Day 12 hoặc bấm Ctrl+C tắt uvicorn cũ).

Terminal 1 — khởi động server:

uvicorn app.main:app --reload --env-file .env
Copy
Terminal 2 — chạy load test để sinh log:

python scripts/load_test.py
Copy
Kiểm tra file data/logs.jsonl đã được tạo:

head -3 data/logs.jsonl
Copy
Chạy validator để lấy baseline score (ghi nhận vào báo cáo):

python scripts/validate_logs.py
Copy
Bạn sẽ thấy score thấp vì log chưa có correlation ID, chưa có enrichment và chưa bật bộ lọc che PII toàn diện (mặc dù hàm summarize_text() đã che PII sơ bộ trong trường message_preview, các dữ liệu nhạy cảm thô khác trong log như credit card hay email vẫn có thể bị rò rỉ khi chưa có processor). Đây là điểm xuất phát — checkpoint sau sẽ cải thiện từng phần.

Lưu ý
·
Lưu ý về Script Validator
Script validate_logs.py chỉ kiểm tra các mẫu PII cơ bản (như ký tự @ cho email và số thẻ test 4111 cho credit card). Đạt điểm 100/100 từ script validator chưa đồng nghĩa với việc log của bạn đã sạch hoàn toàn PII thực tế. Hãy tự tay kiểm tra file log sau khi chạy để đảm bảo không rò rỉ số điện thoại, CCCD hay địa chỉ Việt Nam.

Chạy public tests:

python -m pytest -q
Copy
Kiểm tra checkpoint
Tự kiểm
·
✅ CHECKPOINT CP0 — Setup & Baseline
Tiêu chí nghiệm thu:

Kết quả kiểm tra: File data/logs.jsonl chứa ít nhất 10 bản ghi JSON hợp lệ. Lệnh python -m pytest -q chạy thành công không báo lỗi môi trường.
Ghi nhận: Lưu lại điểm số baseline của lệnh validate_logs.py vào mục 2 trong file submission/REPORT.md để làm mốc đối chiếu sau này.

Lab 13 — Observability cho hệ thống AI: Metrics, Traces & Logs

Tập trung

Ghi chú
0

Hỗ trợ

Tiếng Việt

2

Quay lại

Tiếp
4. Block 2 — Metrics, Traces, Dashboard & Alerts (Checkpoint CP2)
⏱ Thời gian: 60 phút · Bắt đầu: 1:30

Bây giờ chúng ta sẽ nâng cấp hệ thống: từ chỗ chỉ ghi log đơn lẻ sang khả năng giám sát toàn diện thông qua Traces, Dashboard tập trung, đặt chỉ số cam kết chất lượng dịch vụ (SLO) và thiết lập Alert kèm Runbook xử lý sự cố.

Phần A — Traces trên Langfuse
Nếu đã cấu hình Langfuse keys trong .env, traces tự động được ghi khi gọi /chat (nhờ decorator @observe trong agent.py).

Tạo tối thiểu 10 traces:

# Chạy load test — 10 sample queries
python scripts/load_test.py
Copy
Mở Langfuse Dashboard → chọn project → xem danh sách traces. Mỗi trace hiển thị:

Trace ID duy nhất
User ID (hash)
Session ID
Tags (lab, feature, model)
Waterfall — timeline các span (mặc định chỉ có span cha tên run)
Evidence cần thu thập:

Screenshot danh sách ≥ 10 traces
Screenshot một trace waterfall đầy đủ (thấy span run và thời gian)
Lưu vào submission/evidence/
Lưu ý
·
Cấu hình Langfuse là bắt buộc
Vui lòng không bỏ qua bước cấu hình Langfuse. Hãy đảm bảo bạn đã lấy đúng API credentials từ project trên Langfuse Cloud và cấu hình trong file .env trước khi khởi động API server. Việc thiếu bằng chứng trace và waterfall trace sẽ làm ảnh hưởng trực tiếp đến điểm đánh giá kỹ thuật nhóm của bạn.

Lưu ý
·
Phần mở rộng (Không bắt buộc): Ghi nhận Trace cho các Sub-components
Mặc định, decorator @observe chỉ được gắn trên hàm run của LabAgent. Do đó waterfall trace chỉ hiển thị một span cha duy nhất là run. Để có thể nhìn rõ waterfall phân tách giữa RAG và LLM (rất hữu ích cho việc tìm root cause ở CP3), bạn có thể thực hiện phần mở rộng sau:

Mở app/mock_rag.py, import observe và gắn decorator lên hàm retrieve:
from .tracing import observe

@observe(as_type="span")
def retrieve(message: str) -> list[str]:
    # ...
Copy
Mở app/mock_llm.py, làm tương tự cho hàm generate của class FakeLLM:
from .tracing import observe

class FakeLLM:
    # ...
    @observe(as_type="span")
    def generate(self, prompt: str) -> FakeResponse:
        # ...
Copy
Sau khi gắn, khi chạy load test, trace waterfall của bạn trên Langfuse sẽ hiển thị đầy đủ các sub-spans retrieve và generate lồng bên dưới run.

Phần B — Thiết kế Dashboard
Mở file docs/dashboard-spec.md. Dashboard cần đủ 6 nhóm chỉ số (theo yêu cầu trong file):

#	Nhóm	Nguồn dữ liệu	Ví dụ panel
1	Latency	/metrics → latency_p50, latency_p95, latency_p99	Biểu đồ Line hoặc Single Value P50/P95/P99
2	Traffic	/metrics → traffic	Counter tổng số request hoặc QPS gauge
3	Error	/metrics → error_rate_pct, error_breakdown	Tỷ lệ lỗi (%) và bảng breakdown theo loại lỗi
4	Cost	/metrics → total_cost_usd, avg_cost_usd	Tổng chi phí hiện tại so với ngưỡng ngân sách
5	Tokens	/metrics → tokens_in_total, tokens_out_total	Tổng số token input/output đã tiêu thụ
6	Quality	/metrics → quality_avg	Điểm chất lượng trung bình của hệ thống
Gọi endpoint /metrics để xem dữ liệu hiện tại:

curl http://localhost:8000/metrics | python -m json.tool
Copy
Ghi rõ trong docs/dashboard-spec.md:

Tên panel, đơn vị, khoảng thời gian mặc định
Threshold hoặc SLO line
Công cụ sử dụng (Langfuse, Grafana, hoặc mô tả bằng spec)
Evidence: Chụp ảnh dashboard (nếu dùng Langfuse/Grafana) hoặc điền đầy đủ spec vào file.

Phần C — SLO & Alert Rules
SLO: Mở config/slo.yaml, điều chỉnh cho phù hợp với nhóm. Giá trị mặc định:

slis:
  latency_p95_ms:
    objective: 3000      # P95 dưới 3 giây
    target: 99.5         # 99.5% requests
  error_rate_pct:
    objective: 2         # Error rate dưới 2%
    target: 99.0
  daily_cost_usd:
    objective: 2.5       # Chi phí dưới $2.5/ngày
    target: 100.0
  quality_score_avg:
    objective: 0.75      # Quality score trung bình ≥ 0.75
    target: 95.0
Copy
Alert Rules: Mở config/alert_rules.yaml, điền 3 alert rules. Mỗi alert phải dựa trên triệu chứng (user-facing), không dựa trên tên implementation:

alerts:
  - name: high_latency_p95
    severity: warning
    condition: "latency_p95 > 3000ms for 5 minutes"
    type: symptom-based
    owner: on-call-engineer
    runbook: docs/alerts.md#alert-1
  - name: elevated_error_rate
    severity: critical
    condition: "error_rate_pct > 5 for 3 minutes"
    type: symptom-based
    owner: on-call-engineer
    runbook: docs/alerts.md#alert-2
  - name: cost_budget_exceeded
    severity: warning
    condition: "daily_cost_usd > 2.5"
    type: symptom-based
    owner: team-lead
    runbook: docs/alerts.md#alert-3
Copy
Alert Runbook: Mở docs/alerts.md, điền đầy đủ cho 3 alerts. Mỗi alert cần:

Tên, severity, SLI/SLO liên quan
Điều kiện kích hoạt
Ảnh hưởng tới người dùng
Ba bước kiểm tra đầu tiên (đây là phần quan trọng nhất)
Mitigation tạm thời
Owner
Tự kiểm
·
✅ CHECKPOINT CP2 — Metrics, Traces, Dashboard & Alerts
Tiêu chí nghiệm thu:

Kết quả kiểm tra: Có tối thiểu 10 traces hiển thị trên giao diện Langfuse. Thiết kế dashboard ghi đủ 6 nhóm chỉ số kỹ thuật. Hoàn thiện file cấu hình cảnh báo config/alert_rules.yaml và tài liệu hướng dẫn xử lý docs/alerts.md.
Bằng chứng (Evidence): Ảnh chụp danh sách trace, waterfall span và cấu hình dashboard lưu trong thư mục submission/evidence/.
Câu hỏi phản biện: Tại sao các cảnh báo kỹ thuật (Alert rules) nên được thiết kế dựa trên triệu chứng người dùng thấy (Symptom-based) thay vì dựa trên lỗi hệ thống hay tên hàm cụ thể?






Lab 13 — Observability cho hệ thống AI: Metrics, Traces & Logs

Tập trung

Ghi chú
0

Hỗ trợ

Tiếng Việt

2

Quay lại

Tiếp
5. Block 3 — Challenge: Điều tra Incident (Checkpoint CP3)
⏱ Thời gian: 60 phút · Bắt đầu: 2:30

Đã đến lúc thực chiến. Lab Coach sẽ gửi file config/challenge.json vào thư mục dự án của nhóm. File này chứa đề bài incident, seed ngẫu nhiên và bộ query test chính thức cho nhóm của bạn.

Lưu ý
·
Không tự tạo hoặc sửa challenge.json
File config/challenge.json do Lab Coach cung cấp. Tự tạo, sửa hoặc thay thế file này sẽ bị coi là vi phạm quy định của môn học. Nếu chưa nhận được file, hãy tiếp tục luyện tập với practice scenario.

Quy trình phát hành Challenge (Protocol Release)
Để đảm bảo buổi kiểm tra diễn ra đúng kế hoạch, nhóm của bạn cần tuân thủ quy trình sau:

Thời điểm nhận file: Đúng 2:30 (khi bắt đầu Block 3), Lab Coach sẽ công bố và cung cấp file config/challenge.json cho cả lớp thông qua kênh thông báo chính thức (Slack/Teams/Discord hoặc đẩy trực tiếp lên nhánh dev của repo lớp).
Kiểm tra tính hợp lệ: Sau khi tải và chép file vào đúng thư mục config/, chạy lệnh kiểm tra nhanh để đảm bảo cấu trúc file JSON không lỗi:
python -c "from app.challenge import load_challenge; print('Hợp lệ:', load_challenge().challenge_id)"
Copy
Xử lý Timeout (Chờ đợi): Nếu sau 5 phút (đến 2:35) vẫn chưa nhận được file hoặc file bị lỗi định dạng, hãy tiếp tục sử dụng các practice scenario (rag_slow hoặc tool_fail) để thực hành quy trình điều tra và báo ngay cho Lab Coach.
Phương án chấm điểm thay thế (Fallback Grading): Trong trường hợp bất khả kháng khiến file challenge không thể phát hành, điểm phần CP3 của nhóm sẽ được tính dựa trên kết quả điều tra và evidence của một trong các practice scenario tự chọn. Nhóm chỉ cần ghi rõ "Practice scenario: <tên_scenario>" vào mục Challenge ID trong báo cáo REPORT.md.
Luyện tập trước (nếu chưa có challenge)
Trong lúc chờ, luyện với practice scenario:

# Bật incident practice (ví dụ: RAG chậm)
python scripts/inject_incident.py --scenario rag_slow

# Gửi requests và quan sát
python scripts/load_test.py

# Xem metrics
curl http://localhost:8000/metrics | python -m json.tool

# Tắt incident
python scripts/inject_incident.py --scenario rag_slow --disable
Copy
Ba scenario practice:

rag_slow: RAG retrieval thêm 2.5 giây delay → latency tăng vọt
tool_fail: Vector store timeout → error rate tăng, 500 errors
cost_spike: Output tokens × 4 → chi phí tăng gấp 4
Chạy challenge chính thức
Lưu ý
·
⚠️ Lưu ý quan trọng khi chạy Challenge
Chỉ khi nào nhận được thông báo từ Lab Coach rằng file config/challenge.json đã được release thành công, bạn mới bắt đầu thực hiện các lệnh bên dưới. Nếu chưa có file này mà chạy với tham số --challenge, script sẽ báo lỗi FileNotFoundError.

Sau khi Lab Coach release config/challenge.json:

# Bước 1: Bật incident chính thức
python scripts/inject_incident.py

# Bước 2: Chạy input chính thức
python scripts/load_test.py --challenge --concurrency 5
Copy
Quy trình điều tra: Metrics → Traces → Logs
Bước 1 — Metrics: Phát hiện triệu chứng

curl http://localhost:8000/metrics | python -m json.tool
Copy
Hỏi: chỉ số nào bất thường so với baseline? Latency tăng? Error rate cao? Cost spike?

Bước 2 — Traces: Khoanh vùng vị trí

Mở Langfuse → lọc traces trong khoảng thời gian bất thường → mở một trace → xem waterfall.

Mặc định (chỉ có agent-level trace): Bạn sẽ thấy span cha run bị kéo dài thời gian xử lý (latency spike) hoặc hiển thị trạng thái Error. Nhờ đính kèm metadata, bạn có thể xem các trường feature, model, session_id để phát hiện sự bất thường (ví dụ: tất cả request bị chậm đều có feature=search).
Nếu đã làm phần mở rộng (sub-component trace): Bạn sẽ nhìn thấy chi tiết span con nào bên dưới (retrieve hay generate) là nguyên nhân chính gây chậm hoặc phát sinh lỗi.
Bước 3 — Logs: Chứng minh root cause

Lưu ý
·
Cách tìm Correlation ID nếu không sử dụng Langfuse
Nếu nhóm bạn không cấu hình Langfuse, bạn sẽ không thể lấy correlation ID từ trace dashboard. Đồng thời, do mặc định FastAPI không trả về correlation ID trong body của response lỗi 500, load_test.py sẽ hiển thị ID là None đối với các request lỗi.

Trong trường hợp này, hãy tìm correlation ID bằng cách:

Mở trực tiếp file log data/logs.jsonl.
Tìm các dòng log có trường "level":"error" hoặc "request_failed".
Đọc giá trị "correlation_id" của dòng log lỗi đó (ví dụ: req-a1b2c3d4).
Sử dụng ID tìm được đó để chạy lệnh lọc logs ở Bước 3 bên dưới để xem toàn bộ hành trình của request lỗi (bao gồm cả log request_received lúc bắt đầu).
Tìm log có cùng correlation ID với trace/log lỗi để chỉ ra chính xác dòng code/dữ liệu đầu vào gây lỗi. Do log dạng JSONL chứa nhiều dòng log độc lập, lệnh json.tool mặc định sẽ báo lỗi Extra data. Hãy lọc và định dạng log bằng một trong hai cách sau:

# Cách 1: Dùng jq (nếu máy bạn đã cài)
grep "req-<8hex-từ-trace>" data/logs.jsonl | jq

# Cách 2: Dùng Python (luôn hoạt động, không cần cài đặt thêm)
python -c "
import json
for line in open('data/logs.jsonl'):
    rec = json.loads(line)
    if rec.get('correlation_id') == 'req-<8hex-từ-trace>':
        print(json.dumps(rec, indent=2))
"
Copy
Kết hợp evidence từ cả ba lớp để kết luận:

Triệu chứng: (từ metrics, ví dụ: latency p95 > 2.5s hoặc error rate tăng vọt)
Vị trí: (từ trace span, ví dụ: span run bị chậm hoặc span retrieve báo lỗi)
Nguyên nhân gốc: (từ log line cụ thể, ví dụ: log ghi nhận sự kiện request_failed kèm chi tiết lỗi RAG timeout)
Fix action: (bạn sẽ làm gì nếu đây là hệ thống production thực tế)
Preventive measure: (làm gì để tránh lỗi lặp lại trong tương lai)
Ghi kết quả vào báo cáo
Điền vào submission/REPORT.md mục 5 (Điều tra challenge):

Challenge ID
Triệu chứng từ metrics
Trace ID liên quan
Log line/correlation ID liên quan
Root cause
Fix action
Preventive measure
Tự kiểm
·
✅ CHECKPOINT CP3 — Challenge Investigation
Tiêu chí nghiệm thu:

Kết quả kiểm tra: Hoàn thành đầy đủ nội dung mục 5 (Điều tra challenge) trong báo cáo submission/REPORT.md.
Bằng chứng (Evidence): Ảnh chụp biểu đồ metrics ghi nhận lỗi, dòng log thô chỉ ra nguyên nhân gốc và sơ đồ trace waterfall khoanh vùng span lỗi.
Câu hỏi phản biện: Bằng chứng kỹ thuật nào giúp nhóm bạn khẳng định chắc chắn đó là nguyên nhân gốc (Root Cause)? Nếu hệ thống chỉ ghi metrics mà không có log chi tiết, việc điều tra sẽ gặp khó khăn gì?

Lab 13 — Observability cho hệ thống AI: Metrics, Traces & Logs

Tập trung

Ghi chú
0

Hỗ trợ

Tiếng Việt

2

Quay lại

Tiếp
6. Hoàn tất — Báo cáo & Nộp bài
⏱ Thời gian: 30 phút · Bắt đầu: 3:30

Hoàn thiện báo cáo
Mở submission/REPORT.md và điền đầy đủ tất cả các mục:

Thông tin nhóm: tên, repo URL, commit SHA, thành viên và vai trò
Kết quả kỹ thuật: điểm validate_logs.py cuối cùng, tổng traces, PII leak
Logging và tracing: evidence correlation ID, PII redaction, trace waterfall
Dashboard, SLO và alerts: evidence dashboard, lý do chọn SLO, alert rules
Điều tra challenge: challenge ID (hoặc tên practice scenario), triệu chứng, trace ID, log, root cause
Đóng góp cá nhân: mỗi thành viên ghi phần việc + link commit/PR
Kiểm tra evidence
Đảm bảo thư mục submission/evidence/ có đủ:

 Kết quả validate_logs.py cuối cùng
 Danh sách ≥ 10 traces
 Một trace waterfall đầy đủ
 Log JSON có correlation ID và metadata
 Log chứng minh PII đã được redact
 Dashboard đủ 6 nhóm chỉ số
 Alert rules và runbook đã hoàn thiện
 Evidence điều tra challenge (screenshot metrics/log, và trace waterfall)
Kiểm tra trước khi nộp
python -m pytest -q
python scripts/validate_logs.py
git status --short
Copy
Đảm bảo:

Không commit .env, API key hoặc secret
Không commit .venv/, cache
Không có PII chưa được che trong log đã commit
File config/challenge.json không bị sửa
Quy trình nộp bài (Tuân thủ theo đúng README & SUBMISSION)
Để bài nộp của nhóm được tính là hợp lệ, hãy thực hiện đúng theo các quy định sau:

Chuẩn bị file nộp:

Hoàn thiện toàn bộ các file code trong thư mục app/, config/, scripts/ và tests/.
Điền đầy đủ tất cả các mục báo cáo trong tệp submission/REPORT.md.
Lưu tất cả ảnh chụp bằng chứng (evidence) vào thư mục submission/evidence/ và dẫn liên kết tương đối từ tệp REPORT.md.
Quy định nghiêm ngặt (Những thứ KHÔNG ĐƯỢC nộp):

Tuyệt đối không commit tệp cấu hình .env, các API key hoặc Langfuse secret key.
Không commit thư mục ảo .venv/, các tệp cache và các thư viện dependencies đã cài.
Không commit tệp log có chứa PII chưa được che giấu (redacted).
Không copy mã nguồn hoặc hình ảnh bằng chứng của nhóm khác.
Không tự ý chỉnh sửa hoặc tạo giả tệp config/challenge.json.
Chạy kiểm tra kỹ thuật lần cuối:

# Đảm bảo 11 tests public đều xanh
python -m pytest -q

# Đảm bảo điểm validator đạt 100/100
python scripts/validate_logs.py

# Kiểm tra danh sách file sắp commit (đảm bảo không thừa file rác)
git status --short
Copy
Đẩy code lên GitHub & Nộp bài:

Hãy chắc chắn bạn đã đặt tên repository của nhóm theo chuẩn: KX-DAY13-[Mã_SV_Nhóm_Trưởng] (Ví dụ: K4-DAY13-2A2026....).
git add -A
git commit -m "Hoàn thành lab Day 13 - [Mã_SV_Nhóm_Trưởng]"
git push origin main
Copy
Sau đó, nộp URL repository và commit SHA cuối cùng lên hệ thống Codelabs của lớp.

Lưu ý
·
Lưu ý quan trọng
Bài nộp sẽ bị tính là không hợp lệ (yêu cầu nộp lại) nếu: thiếu tệp báo cáo submission/REPORT.md, Lab Coach không thể clone được repo của nhóm, để lộ API key/secret, hoặc nộp sai định dạng URL.

Tự kiểm
·
✅ HOÀN TẤT — Báo cáo & Nộp bài
Tiêu chí nghiệm thu:

Kết quả kiểm tra: Chạy python -m pytest -q trả về kết quả pass. Lệnh validate_logs.py đạt điểm số tối đa theo yêu cầu. Báo cáo REPORT.md đã điền đầy đủ thông tin đóng góp của từng thành viên.
Chuẩn bị Demo: Sẵn sàng trình diễn nhanh luồng debug thực chiến trước Lab Coach: Từ việc phát hiện lỗi ở Dashboard (Metrics) -> Khoanh vùng vị trí (Traces) -> Chỉ ra dòng log lỗi cụ thể (Logs) -> Đưa ra giải pháp khắc phục.
Lab 13 — Observability cho hệ thống AI: Metrics, Traces & Logs

Tập trung

Ghi chú
0

Hỗ trợ

Tiếng Việt

2

Quay lại

Nộp bài để hoàn thành
8. Bonus — Tối ưu chi phí & Audit Log (+10 điểm)
Nếu hoàn thành sớm, nhóm có thể lấy thêm điểm bonus (tối đa +10, tổng không vượt 100):

Cost Optimization
Bật cost_spike incident: python scripts/inject_incident.py --scenario cost_spike
Chạy load test, ghi nhận total_cost_usd (before)
Đề xuất và triển khai giải pháp (ví dụ: giới hạn output tokens, cache response)
Chạy lại, ghi nhận total_cost_usd (after)
Evidence: screenshot before/after vào submission/evidence/
Audit Log
Tạo file log riêng data/audit.jsonl chỉ ghi các sự kiện quan trọng (incident enable/disable, config change). Đường dẫn đã có sẵn trong .env.example (AUDIT_LOG_PATH).

Custom Automation
Viết script tự động phát hiện anomaly từ data/logs.jsonl (ví dụ: phát hiện PII leak tự động, alert khi latency vượt SLO).

Góp ý cho buổi Lab
Không bắt buộc và không ảnh hưởng việc nộp bài. Giảng viên chỉ xem phản hồi ẩn danh.

Góp ý bài Lab
Nộp bài và đánh giá Lab
Dán link GitHub, Drive hoặc LMS của bài đã nộp. Điểm và nhận xét sẽ không hiển thị tại đây.

Đang tải trạng thái bài nộp…

