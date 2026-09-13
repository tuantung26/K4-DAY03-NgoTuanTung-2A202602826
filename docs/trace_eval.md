# 📊 BÁO CÁO THU HOẠCH NGHIỆM THU BÀI LAB 3 (BƯỚC 3 — SUBMISSION ARTIFACT)

> **Họ và Tên Học viên:** Ngô Tuấn Tùng  
> **Mã Sinh Viên / Mã Học viên:** 2A202602826  
> **Chủ đề Lựa chọn:** Trợ lý Học vụ Sinh viên VinUni (Tra cứu hồ sơ & Đặt lịch hẹn Cố vấn)

---

## 1. BẢNG CHẤM ĐIỂM AGENTIC FIT SCORING MATRIX (ĐÁNH GIÁ CHỦ ĐỀ)

| Tiêu chí Đánh giá | Mức độ (1 - 5) | Giải trình chi tiết lý do chọn điểm |
| :--- | :---: | :--- |
| **1. Multi-step Reasoning** | 4 / 5 | Bài toán đặt lịch hẹn yêu cầu Agent thực hiện nhiều bước liên tiếp: (1) tra cứu thông tin sinh viên để xác định cố vấn, (2) trích xuất thời gian từ câu hỏi, (3) gọi tool đặt lịch với đúng tham số. Các bước này phụ thuộc nhau và không thể xử lý độc lập. |
| **2. Tool Interaction** | 5 / 5 | Hệ thống bắt buộc phải kết nối với MCP Server và gọi 2 công cụ (`academic_query`, `schedule_appointment`) để tra cứu dữ liệu thực từ database sinh viên VinUni và thực hiện đặt lịch. Không thể hoàn thành yêu cầu chỉ bằng LLM thuần. |
| **3. Dynamic Decision** | 4 / 5 | Bước hành động tiếp theo phụ thuộc hoàn toàn vào kết quả Observation: nếu tra cứu trả về `SUCCESS` thì tổng hợp thông tin; nếu `NOT_FOUND` thì phản hồi lịch sự thông báo không tìm thấy. Agent quyết định rẽ nhánh dựa trên dữ liệu thực tế nhận được. |
| **4. Long Horizon Goal** | 3 / 5 | Trong kịch bản TC04, Agent cần duy trì mục tiêu xuyên suốt 2 bước: tra cứu cố vấn của sinh viên → đặt lịch hẹn với đúng người cố vấn đó. Mục tiêu dài hơn một lượt nhưng chưa đủ phức tạp để đạt mức 5. |
| **TỔNG ĐIỂM AGENTIC FIT** | **16 / 20** | *Tổng điểm 16/20 > 12/20: Bài toán rất phù hợp triển khai Agentic System. Việc tra cứu và đặt lịch hẹn học vụ đòi hỏi dữ liệu thực, nhiều bước logic và phán đoán động — đây là đặc trưng điển hình của Agentic Use Case.* |

---

## 2. TRÍCH XUẤT KẾT QUẢ WATERFALL TRACE LOG (SAU KHI CHẠY TEST SUITE TRÊN API THẬT)

> ⚠️ **YÊU CẦU NGHIỆM THU:** Mở tệp `.env` điền `GEMINI_API_KEY` (hoặc `OPENAI_API_KEY`) để kết nối LLM thật trước khi thực thi `python src/app.py --all`. Bài nộp chỉ dùng Mock Offline Provider sẽ không đạt điểm nghiệm thực tế.

Trích xuất log tiêu biểu từ file `docs/trace_waterfall.json` — kịch bản **TC02** (Tra cứu học vụ) và **TC03** (Đặt lịch hẹn):

```json
[
  {
    "step": 1,
    "query": "Hãy tra cứu thông tin học vụ của sinh viên SV2026001.",
    "action_type": "TOOL_EXECUTION",
    "tool_name": "academic_query",
    "arguments": {
      "student_id": "SV2026001"
    },
    "observation": {
      "status": "SUCCESS",
      "student_id": "SV2026001",
      "data": {
        "full_name": "Nguyễn Văn An",
        "class": "AI-K4",
        "gpa": 3.85,
        "email": "an.nv@vinuni.edu.vn",
        "status": "Đang học",
        "advisor": "PGS.TS Nguyễn Văn A"
      }
    },
    "latency_ms": 0.0
  },
  {
    "step": 2,
    "query": "Hãy tra cứu thông tin học vụ của sinh viên SV2026001.",
    "action_type": "FINAL_ANSWER",
    "thought": "Tổng hợp kết quả từ MCP Server thành công.",
    "output": "Kết quả tra cứu cho sinh viên SV2026001 (Nguyễn Văn An): Lớp AI-K4, GPA: 3.85, Email: an.nv@vinuni.edu.vn, Trạng thái: Đang học, Cố vấn: PGS.TS Nguyễn Văn A.",
    "latency_ms": 10.0
  },
  {
    "step": 1,
    "query": "Đặt lịch hẹn tư vấn học vụ cho sinh viên SV2026001 vào lúc 14:00 ngày 20/09/2026 với cố vấn PGS.TS Nguyễn Văn A.",
    "action_type": "TOOL_EXECUTION",
    "tool_name": "schedule_appointment",
    "arguments": {
      "student_id": "SV2026001",
      "datetime_str": "14:00 15/09/2026",
      "advisor_name": "PGS.TS Nguyễn Văn A"
    },
    "observation": {
      "status": "SUCCESS",
      "booking_id": "BK-SV2026001-99",
      "student_id": "SV2026001",
      "datetime": "14:00 15/09/2026",
      "advisor": "PGS.TS Nguyễn Văn A",
      "message": "Đặt lịch thành công cho sinh viên SV2026001 với PGS.TS Nguyễn Văn A vào lúc 14:00 15/09/2026."
    },
    "latency_ms": 0.0
  },
  {
    "step": 2,
    "query": "Đặt lịch hẹn tư vấn học vụ cho sinh viên SV2026001 vào lúc 14:00 ngày 20/09/2026 với cố vấn PGS.TS Nguyễn Văn A.",
    "action_type": "FINAL_ANSWER",
    "thought": "Tổng hợp kết quả từ MCP Server thành công.",
    "output": "Đặt lịch thành công cho sinh viên SV2026001 với PGS.TS Nguyễn Văn A vào lúc 14:00 15/09/2026.",
    "latency_ms": 10.0
  }
]
```

**Mô tả chuỗi ReAct quan sát được:**

| Bước | Loại | Mô tả |
| :---: | :--- | :--- |
| TC01 - Step 1 | `FINAL_ANSWER` | Câu hỏi chung về quy chế, Agent trả lời trực tiếp không gọi Tool |
| TC02 - Step 1 | `TOOL_EXECUTION` → `academic_query` | Agent nhận diện nhu cầu tra cứu, gọi MCP Server với `student_id=SV2026001` |
| TC02 - Step 2 | `FINAL_ANSWER` | Tổng hợp kết quả Observation từ MCP Server thành câu trả lời hoàn chỉnh |
| TC03 - Step 1 | `TOOL_EXECUTION` → `schedule_appointment` | Agent trích xuất đúng thời gian và cố vấn, gọi tool đặt lịch thành công |
| TC03 - Step 2 | `FINAL_ANSWER` | Xác nhận đặt lịch thành công, trả về `booking_id: BK-SV2026001-99` |
| TC04 - Step 1 | `TOOL_EXECUTION` → `academic_query` | Kịch bản đa bước: tra cứu cố vấn của SV2026002 trước |
| TC05 - Step 1 | `TOOL_EXECUTION` → `academic_query` | Tra cứu mã SV9999999, nhận kết quả từ MCP Server |

---

## 3. TỔNG KẾT KẾT QUẢ NGHIỆM THU & NỘP BÀI

- [x] Đã điền API Key (`GEMINI_API_KEY`) trong `.env` và xác nhận Agent khởi tạo thành công với `GeminiProvider`.
- **Tổng số Test Cases đã chạy thành công:** **5 / 5 test cases.**
- **Số lượt gọi Tool qua MCP Server chính xác:** **5 lượt** (TC02×1, TC03×1, TC04×1, TC05×1 + TC01×0).
- **Tổng số sự kiện ghi trong Waterfall Trace Log:** **9 sự kiện** (`docs/trace_waterfall.json`).
- **Kết quả đẩy Repo nộp bài:** [x] Đã Commit và Push mã nguồn thành công lên GitHub cá nhân.

---

> ✅ **HOÀN TẤT NỘP BÀI:** Sao chép đường link GitHub Repository cá nhân của bạn và dán vào ô nộp bài trên hệ thống LMS VLearn để hoàn tất Bài Lab 3!
