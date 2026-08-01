# Software Testing Life Cycle (STLC) — AI-Augmented

> **Dự án:** `ai-testing-lab-web`
> **Mô hình:** Testing as Code (TaC) tích hợp AI
> **Phạm vi áp dụng:** Toàn bộ vòng đời kiểm thử từ phân tích yêu cầu đến đóng chu trình, cho cả kiểm thử thủ công và tự động.
> **Đối tượng:** QA Engineer, SDET, Dev, Product Owner, Release Manager.

---

## 1. Tổng quan mô hình

STLC truyền thống gồm 6 giai đoạn tuần tự. Trong mô hình **Testing as Code (TaC)**, các artefact kiểm thử (test case, test data, test script, báo cáo) được quản lý **như mã nguồn** — versioned trong Git, review qua Pull Request, và **AI được nhúng vào từng giai đoạn** như một trợ lý tăng tốc (accelerator), không thay thế vai trò ra quyết định của QA.

```

> **Nguyên tắc cốt lõi:** AI đề xuất — Con người xác nhận (**AI Suggests, Human Confirms**). Mọi output do AI sinh ra (test case, test data, phân tích impact...) đều phải được QA review trước khi đưa vào baseline.

---

## 2. Giai đoạn 1 — Phân tích Yêu cầu (Requirement Analysis)

### 2.1. Hoạt động chính
- Nghiên cứu tài liệu yêu cầu (User Story, PRD, Figma design, API spec).
- Xác định phạm vi kiểm thử (in-scope / out-of-scope).
- Nhận diện yêu cầu mơ hồ, thiếu logic nghiệp vụ, xung đột giữa các tài liệu.
- Xác định testability của yêu cầu (Acceptance Criteria có đo lường được không).
- Đặt câu hỏi làm rõ (clarification) với BA/PO.

### 2.2. Kết quả đầu ra (Deliverables)

| Artefact | Mô tả | Nơi lưu trữ |

- RTM khởi tạo (Requirement Traceability Matrix) > Ánh xạ yêu cầu → tiêu chí chấp nhận > `tests/01-planning/rtm/`
- Danh sách câu hỏi làm rõ (Clarification Log) > Các điểm mơ hồ cần BA/PO xác nhận > `tests/01-planning/clarifications/` 

### 2.3. Ứng dụng AI / TaC Enablement
>  **AI Requirement Reviewer**: LLM được prompt để đọc User Story và tự động phát hiện:
> - Acceptance Criteria không rõ ràng, thiếu điều kiện biên (edge case).
> - Các thuật ngữ nghiệp vụ mâu thuẫn giữa các story liên quan (cross-check bằng semantic search trong Git history).
> - Đề xuất bổ sung AC theo mẫu **Given-When-Then** nếu story viết dạng tự do.

- **Công cụ:** AI Chat tích hợp trong Jira/Confluence hoặc script CLI đọc `.md` story và sinh checklist review.
- **Lợi ích đo lường:** Giảm ~40% thời gian review yêu cầu thủ công; phát hiện sớm ~25% lỗi thiếu AC trước khi vào Sprint.
- **Ràng buộc:** AI **không tự quyết định** phạm vi kiểm thử — chỉ đề xuất, QA Lead phê duyệt cuối cùng.

---

## 3. Giai đoạn 2 — Lập kế hoạch Kiểm thử (Test Planning)

### 3.1. Hoạt động chính
- Xác định chiến lược kiểm thử (Test Strategy): loại test (Functional, API, UI, Performance, Security, Accessibility).
- Ước lượng effort, phân bổ nguồn lực, xác định rủi ro (Risk-based Testing).
- Xác định tiêu chí Entry/Exit cho từng giai đoạn.
- Lựa chọn công cụ, framework tự động hóa (Playwright/Cypress cho Web, k6 cho Performance...).
- Xây dựng Test Schedule gắn với Sprint/Release timeline.

### 3.2. Kết quả đầu ra (Deliverables)
| Artefact | Mô tả | Nơi lưu trữ |
|---|---|---|
| Test Plan | Chiến lược, phạm vi, rủi ro, lịch trình | `tests/01-planning/test-plan.md` |
| Risk Matrix | Ma trận rủi ro theo mức độ ảnh hưởng x xác suất | `tests/01-planning/risk-matrix.md` |
| Resource & Tooling Plan | Công cụ, nhân sự, môi trường dự kiến | `tests/01-planning/resources.md` |

### 3.3. Ứng dụng AI / TaC Enablement
>  **AI Risk Predictor**: Phân tích lịch sử bug (Jira/GitHub Issues) và mã nguồn thay đổi gần nhất để **dự đoán module có rủi ro cao**, từ đó đề xuất mức độ ưu tiên kiểm thử (Risk-based Test Prioritization).

- **Công cụ:** Script phân tích `git log` + bug density theo module, kết hợp LLM tóm tắt xu hướng lỗi lịch sử.
- **Testing as Code:** Test Plan được viết dạng Markdown, versioned trong repo, review qua PR như code — mọi thay đổi chiến lược kiểm thử đều có lịch sử audit.
- **Lợi ích đo lường:** Tối ưu phân bổ effort kiểm thử vào 20% module gây ra 80% lỗi (nguyên lý Pareto), dựa trên dữ liệu thực tế thay vì cảm tính.

---

## 4. Giai đoạn 3 — Thiết kế Case / Kịch bản (Test Design)

### 4.1. Hoạt động chính
- Thiết kế Test Case chi tiết từ Acceptance Criteria (Positive, Negative, Edge case, Boundary).
- Thiết kế Test Data (bao gồm dữ liệu giả lập, dữ liệu biên).
- Viết kịch bản tự động hóa (Automation Script) theo chuẩn Page Object Model / BDD.
- Review Test Case theo cặp (Peer Review) trước khi baseline.

### 4.2. Kết quả đầu ra (Deliverables)
| Artefact | Mô tả | Nơi lưu trữ |
|---|---|---|
| Test Case Suite | Bộ test case chi tiết (manual + automation mapping) | `tests/02-design/test-cases/` |
| Automation Scripts | Script Playwright/Cypress theo POM | `tests/03-automation/` |
| Test Data Set | Dữ liệu kiểm thử, fixtures, mocks | `tests/02-design/test-data/` |

### 4.3. Ứng dụng AI / TaC Enablement
>  **AI Test Case Generator**: Từ Acceptance Criteria (Gherkin) hoặc từ diff của PR, AI tự động sinh:
> - Bộ test case nháp (draft) bao phủ happy path + edge case + negative case.
> - Skeleton script tự động hóa (Playwright) từ mô tả ngôn ngữ tự nhiên.
> - Test data biên (boundary value) dựa trên schema validation của form/API.

- **Quy trình TaC:**
  1. QA viết prompt mô tả tính năng → AI sinh draft test case (`.feature` / `.md`).
  2. QA review, chỉnh sửa, bổ sung case AI bỏ sót.
  3. Commit vào repo, review qua Pull Request (bắt buộc ≥1 approver).
  4. CI pipeline lint test case (kiểm tra format, trùng lặp ID).
- **Công cụ:** Claude/GPT tích hợp CLI hoặc plugin IDE, kết hợp `Gherkin linter`, `Playwright Codegen`.
- **Lợi ích đo lường:** Giảm ~50-60% thời gian viết test case thủ công; tăng độ phủ edge case nhờ AI liệt kê boundary value có hệ thống.

>  **Lưu ý bắt buộc:** Test case do AI sinh **luôn ở trạng thái "Draft - Needs Review"** cho đến khi QA xác nhận. Không merge trực tiếp vào baseline.

---

## 5. Giai đoạn 4 — Thiết lập Môi trường (Test Environment Setup)

### 5.1. Hoạt động chính
- Chuẩn bị môi trường kiểm thử (Dev/Staging/UAT) đồng bộ cấu hình với Production.
- Cấu hình CI/CD pipeline (GitHub Actions/Jenkins) để chạy test tự động.
- Seed dữ liệu test, cấu hình mock server/service virtualization.
- Smoke check môi trường trước khi bắt đầu Execution.

### 5.2. Kết quả đầu ra (Deliverables)
| Artefact | Mô tả | Nơi lưu trữ |
|---|---|---|
| Environment Config as Code | File cấu hình môi trường (Docker Compose, `.env.test`) | `tests/03-environment/config/` |
| CI/CD Pipeline Definition | Workflow YAML chạy test tự động | `.github/workflows/` |
| Environment Readiness Checklist | Checklist xác nhận môi trường sẵn sàng | `tests/03-environment/checklist.md` |

### 5.3. Ứng dụng AI / TaC Enablement
>  **AI Environment Diagnostics**: AI phân tích log lỗi khi pipeline setup thất bại (dependency conflict, config sai) và **đề xuất fix tự động** (auto-suggest patch cho Dockerfile/YAML).

- **Infrastructure as Code (IaC):** Toàn bộ môi trường test được định nghĩa bằng code (Docker Compose/Terraform), đảm bảo tính tái lập (reproducibility) 100%.
- **Công cụ:** AI-assisted log analysis tích hợp trong CI (phân tích log GitHub Actions khi job fail), tự động comment gợi ý fix trên PR.
- **Lợi ích đo lường:** Giảm thời gian debug "môi trường lỗi do config" từ hàng giờ xuống còn vài phút nhờ AI chẩn đoán nhanh nguyên nhân gốc.

---

## 6. Giai đoạn 5 — Thực thi & Báo cáo Lỗi (Test Execution & Bug Reporting)

### 6.1. Hoạt động chính
- Thực thi Test Case (manual) và chạy Automation Suite (CI/CD).
- Ghi nhận kết quả (Pass/Fail/Blocked), log evidence (screenshot, video, trace).
- Báo cáo lỗi (Bug Report) chi tiết: mô tả, bước tái hiện, mức độ ưu tiên/nghiêm trọng.
- Retest sau khi Dev fix, thực hiện Regression Testing.

### 6.2. Kết quả đầu ra (Deliverables)
| Artefact | Mô tả | Nơi lưu trữ |
|---|---|---|
| Execution Report | Kết quả chạy test (Pass/Fail, log, trace) | `tests/04-reports/execution/` |
| Bug Reports | Báo cáo lỗi chi tiết, liên kết Jira/GitHub Issues | Jira / `tests/04-reports/bugs/` |
| Regression Report | Kết quả bộ regression tự động | `tests/04-reports/regression/` |

### 6.3. Ứng dụng AI / TaC Enablement
> ⚙️ **AI PR Impact Analysis**: Khi có Pull Request mới, AI phân tích diff code để:
> - Xác định module/tính năng bị ảnh hưởng (Impact Analysis).
> - Đề xuất tập con test case liên quan cần chạy lại (Smart Test Selection) thay vì chạy toàn bộ Regression Suite.
> - Sinh báo cáo lưu tại `tests/05-ai-impact-reports/`.

> ⚙️ **AI Bug Triage & Root Cause Assist**: Khi test fail, AI đọc log/stack trace/screenshot diff để:
> - Tự động phân loại lỗi (UI regression, data issue, flaky test, environment issue).
> - Đề xuất mức độ Severity/Priority ban đầu.
> - Tự động điền template Bug Report (Steps to Reproduce từ trace của Playwright).

- **Công cụ:** Playwright Trace Viewer + AI phân tích trace, GitHub Bot tự động comment kết quả Impact Analysis trên PR.
- **Lợi ích đo lường:** Giảm ~70% thời gian chạy Regression không cần thiết nhờ Smart Test Selection; giảm thời gian viết Bug Report thủ công nhờ auto-draft từ AI.

---

## 7. Giai đoạn 6 — Đóng chu trình Kiểm thử (Test Closure)

### 7.1. Hoạt động chính
- Đánh giá mức độ hoàn thành theo Exit Criteria đã định nghĩa.
- Tổng hợp báo cáo kiểm thử tổng thể (Test Summary Report).
- Rà soát bug tồn đọng (Open Defects), quyết định release hay defer.
- Lưu trữ artefact kiểm thử, rút kinh nghiệm (Retrospective/Lessons Learned).
- Cập nhật lại Test Case Suite/Automation Suite cho chu kỳ tiếp theo.

### 7.2. Kết quả đầu ra (Deliverables)
| Artefact | Mô tả | Nơi lưu trữ |
|---|---|---|
| Test Summary Report | Báo cáo tổng kết: coverage, pass rate, bug density | `tests/04-reports/summary/` |
| Lessons Learned | Ghi nhận cải tiến quy trình cho chu kỳ sau | `tests/02-processes/retrospectives/` |
| Test Closure Sign-off | Xác nhận đóng chu trình bởi QA Lead | Đính kèm trong Release ticket |

### 7.3. Ứng dụng AI / TaC Enablement
> ⚙️ **AI Test Summary Generator**: AI tổng hợp toàn bộ dữ liệu execution (từ nhiều run CI/CD) thành báo cáo tường thuật (narrative report) bằng ngôn ngữ tự nhiên — dùng để trình bày cho stakeholder không chuyên kỹ thuật.

- **Metric Dashboard as Code:** Các chỉ số (pass rate, defect density, automation coverage) được sinh tự động từ pipeline, không nhập tay.
- **AI Retrospective Assist:** AI phân tích các Bug Report trong chu kỳ để tìm **pattern lỗi lặp lại** (recurring root cause), đề xuất action item cải tiến cho Sprint sau.
- **Lợi ích đo lường:** Rút ngắn thời gian tổng hợp báo cáo cuối kỳ từ vài ngày xuống còn vài giờ; nâng cao chất lượng insight nhờ AI phát hiện pattern mà con người dễ bỏ sót.

---
