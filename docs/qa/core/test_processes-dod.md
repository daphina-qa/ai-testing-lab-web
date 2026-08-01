# Definition of Done (DoD) & Definition of Ready (DoR) cho Quality Assurance

> **Dự án:** `ai-testing-lab-web`
> **Mô hình:** Testing as Code (TaC) tích hợp AI
> **Mục tiêu:** Thiết lập rào chắn chất lượng (Quality Gate) rõ ràng, có thể đo lường, áp dụng nhất quán tại 3 cấp độ: **Feature (DoR)** → **Pull Request (DoD)** → **Release Candidate (DoD)**.

```
DoR (Feature sẵn sàng vào Dev/QA)
        │
        ▼
DoD - Pull Request (Code + Test sẵn sàng merge)
        │
        ▼
DoD - Release Candidate (Sản phẩm sẵn sàng release)
```

---

## 1. Definition of Ready (DoR) — Cấp độ User Story / Feature

> **Mục đích:** Đảm bảo một User Story/Feature **đủ điều kiện** để chuyển từ Backlog sang giai đoạn Dev/QA thực thi (In Progress), tránh lãng phí công sức vào yêu cầu chưa rõ ràng.

### 1.1. Checklist DoR

- [ ] Story có **Acceptance Criteria** viết theo định dạng **Given-When-Then** (hoặc tương đương), rõ ràng và đo lường được.
- [ ] Story đã qua **AI Requirement Reviewer** để rà soát yêu cầu mơ hồ/thiếu edge case (xem `docs/qa/core/test_processes-stlc.md`); các câu hỏi làm rõ đã được BA/PO trả lời.
- [ ] Story đã được **estimate** (story point/effort) bởi cả Dev và QA.
- [ ] Đã xác định rõ **phạm vi kiểm thử** (in-scope/out-of-scope), bao gồm cả các trường hợp Negative/Edge case chính.
- [ ] Thiết kế UI/UX (Figma/Wireframe) đã sẵn sàng và không còn thay đổi lớn (nếu có UI liên quan).
- [ ] Các dependency kỹ thuật (API, service khác, feature flag) đã được xác định và không bị block.
- [ ] Story không vi phạm giới hạn kích thước hợp lý (đủ nhỏ để hoàn thành trong 1 Sprint).
- [ ] Đã có sự đồng thuận (Sign-off) giữa PO, Dev Lead và QA Lead trong buổi Refinement.

> 📌 **Quality Gate:** Nếu Story **không đạt DoR**, QA có quyền **từ chối nhận** vào Sprint hoặc yêu cầu quay lại Refinement. Đây là điểm kiểm soát để tránh "garbage in, garbage out" trong toàn bộ STLC.

---

## 2. DoD cho Pull Request (PR) / Code Level

> **Mục đích:** Đảm bảo mọi thay đổi code trước khi merge vào nhánh chính (`main`/`develop`) đạt chuẩn chất lượng tối thiểu, có bằng chứng kiểm thử tự động và phân tích tác động bởi AI.

### 2.1. Yêu cầu Unit Test / Integration Test Coverage

| Tiêu chí | Ngưỡng tối thiểu | Ghi chú |
|---|---|---|
| Unit Test Coverage (dòng code mới/thay đổi) | **≥ 80%** | Đo bằng công cụ coverage tích hợp CI (ví dụ Istanbul/Jest coverage) |
| Integration Test Coverage cho API endpoint mới | **100% happy path + ≥1 negative case** | Bắt buộc với mọi endpoint mới hoặc thay đổi contract |
| Unit Test trạng thái | **100% PASS**, không skip/disable test không có lý do được ghi chú | CI block merge nếu có test fail |
| Test cho bug fix | Bắt buộc có **regression test** tái hiện bug trước khi fix | Đính kèm link Bug ID trong PR |

- [ ] Toàn bộ Unit Test và Integration Test liên quan **PASS 100%** trên CI.
- [ ] Coverage report được đính kèm tự động trong PR (comment bot).
- [ ] Không có coverage giảm so với baseline (`coverage diff ≥ 0%`).

### 2.2. Yêu cầu về AI Impact Analysis

> Tham chiếu: `docs/qa/report/ai-impact/.md/`

- [ ] PR đã được **AI PR Impact Analysis** tự động phân tích diff code (theo `docs/qa/core/test_processes-stlc.md §6.3`).
- [ ] Báo cáo Impact Analysis đã được **sinh và lưu** tại `docs/qa/report/ai-impact/.md/PR-<số PR>.md`, bao gồm:
  - Danh sách module/component bị ảnh hưởng.
  - Danh sách Test Case/Automation Script liên quan cần chạy lại (Smart Test Selection).
  - Mức độ rủi ro dự đoán (Low/Medium/High).
- [ ] Nếu AI đánh giá rủi ro **High**, bắt buộc có **thêm 1 QA Reviewer** xác nhận thủ công (không chỉ dựa vào automation).
- [ ] Tác giả PR đã đọc và phản hồi (resolve/comment) toàn bộ cảnh báo (warning) do AI Impact Analysis nêu ra.

### 2.3. Yêu cầu PASS toàn bộ kịch bản Sanity/Smoke tự động

- [ ] Bộ **Smoke Test tự động** (chạy trên môi trường CI/Preview) **PASS 100%** trước khi cho phép merge.
- [ ] Thời gian chạy Smoke Suite ≤ **10 phút** (đảm bảo fast feedback loop).
- [ ] Không có test bị đánh dấu **Flaky** (flaky test phải được cách ly/quarantine và ticket theo dõi riêng, không được che giấu bằng retry vô hạn).

### 2.4. Checklist tổng hợp PR (Code Review Gate)

- [ ] Unit/Integration Test coverage đạt ngưỡng và 100% PASS.
- [ ] Báo cáo AI Impact Analysis đã sinh và được review.
- [ ] Smoke/Sanity Suite tự động PASS 100%.
- [ ] Không có lỗi Linter/Static Analysis (ESLint, TypeScript check) ở mức Error.
- [ ] Có ít nhất **1 Code Reviewer** (Dev) và **1 QA Reviewer** (nếu PR ảnh hưởng logic nghiệp vụ) approve.
- [ ] Test case mới (nếu có) đã được cập nhật vào `docs/qa/test-cases/.md` theo chuẩn TaC (versioned, không phải file rời trên máy cá nhân).
- [ ] Không còn `TODO`/`console.log`/code debug thừa trong diff.

> ⚠️ **Quality Gate:** PR **không được merge** nếu bất kỳ mục nào trong checklist trên chưa đạt, trừ trường hợp có **ngoại lệ được QA Lead/Tech Lead phê duyệt bằng văn bản** (comment trong PR) kèm ticket theo dõi nợ kỹ thuật (Tech Debt).

---

## 3. DoD cho Release Candidate (RC) / Milestone

> **Mục đích:** Đảm bảo một phiên bản (Release Candidate) đủ điều kiện phát hành ra Production hoặc bàn giao cho giai đoạn UAT/Staging cuối cùng.

### 3.1. Pass Rate tối thiểu cho Regression Automation Suite

| Loại kiểm thử | Pass rate tối thiểu | Ghi chú |
|---|---|---|
| Regression Automation Suite (toàn bộ) | **≥ 98%** | Chạy trên môi trường Staging đồng bộ Production |
| Critical Path / Core Flow (thanh toán, đăng nhập, đăng ký...) | **100%** | Không chấp nhận bất kỳ fail nào ở luồng lõi |
| Cross-browser/Cross-device Suite | **≥ 95%** | Theo ma trận trình duyệt đã định nghĩa trong Test Plan |
| API Contract Test | **100%** | Đảm bảo không breaking change ngoài ý muốn |

- [ ] Regression Suite đã chạy **tối thiểu 2 lần liên tiếp ổn định** (stable run) trước khi chốt RC, không tính các lần fail do flaky.
- [ ] Mọi test fail trong Regression đã được triage: xác định là **Bug thật** hay **Flaky/Environment issue**.

### 3.2. Yêu cầu về Báo cáo Kiểm thử

> Tham chiếu: `docs/qa/report/execution/.md/`

- [ ] **Test Summary Report** đã được sinh (thủ công hoặc qua AI Test Summary Generator — `docs/qa/core/test_processes-stlc.md §7.3`) và lưu tại `docs/qa/report/execution/summary/RC-<version>.md`.
- [ ] Báo cáo bao gồm tối thiểu các nội dung:
  - [ ] Tổng số Test Case thực thi / Pass / Fail / Blocked / Skipped.
  - [ ] Coverage tổng thể (Requirement Coverage, Automation Coverage).
  - [ ] Danh sách Known Issues còn tồn đọng kèm mức độ Severity.
  - [ ] Kết quả Regression, Smoke, Performance (nếu áp dụng), Security scan cơ bản.
  - [ ] Khuyến nghị Go/No-Go từ QA Lead.
- [ ] Báo cáo đã được review và **ký xác nhận (sign-off)** bởi QA Lead, Tech Lead và Product Owner.

### 3.3. Mức độ lỗi còn đọng (Critical/High Bug Policy)

| Mức độ Severity | Chính sách Release |
|---|---|
| **Critical** (Blocker: sập hệ thống, mất dữ liệu, lỗ hổng bảo mật nghiêm trọng, chặn luồng lõi) | **0 lỗi** — Tuyệt đối không release nếu còn tồn đọng Critical chưa fix hoặc chưa có workaround được duyệt |
| **High** (Ảnh hưởng nghiêm trọng chức năng chính, không có workaround) | **0 lỗi mở** — hoặc tối đa có Waiver được PO + QA Lead phê duyệt kèm kế hoạch hotfix cụ thể trong vòng ≤ 48h sau release |
| **Medium** (Ảnh hưởng chức năng phụ, có workaround) | Tối đa **≤ 5 lỗi** tồn đọng, phải được ghi nhận rõ trong Release Notes |
| **Low** (Cosmetic, UI nhỏ, không ảnh hưởng nghiệp vụ) | Không chặn release, backlog cho Sprint sau |

- [ ] Không còn bug **Critical** ở trạng thái Open/Reopened.
- [ ] Không còn bug **High** chưa có Waiver phê duyệt.
- [ ] Tất cả Waiver (ngoại lệ release có lỗi tồn đọng) được ghi nhận có chữ ký số/comment phê duyệt của **QA Lead** và **Product Owner**, kèm ETA fix.
- [ ] Bug density (số lỗi/1000 dòng code thay đổi) không vượt ngưỡng cảnh báo đã định nghĩa trong Risk Matrix

### 3.4. Checklist tổng hợp Release Candidate

- [ ] Regression Suite đạt Pass rate theo ngưỡng quy định (§3.1).
- [ ] Test Summary Report đầy đủ, đã sign-off (§3.2).
- [ ] Không còn Critical bug; High bug (nếu có) đã có Waiver hợp lệ (§3.3).
- [ ] Performance baseline không suy giảm so với release trước (nếu có kiểm thử hiệu năng).
- [ ] Rollback Plan đã được chuẩn bị và verify khả thi.
- [ ] Release Notes đã liệt kê đầy đủ known issues còn tồn đọng.

> ✅ **Quality Gate cuối cùng:** Chỉ khi **toàn bộ checklist §3.4** đạt yêu cầu, QA Lead mới đưa ra khuyến nghị **"GO"** cho Release. Quyết định release cuối cùng thuộc về Product Owner/Release Manager dựa trên khuyến nghị này, nhưng **không được override** chính sách 0 lỗi Critical.

---

## 4. Bảng tổng hợp 3 cấp độ DoD/DoR

| Cấp độ | Áp dụng khi nào | Chủ sở hữu Quality Gate | Điều kiện chặn cứng (Hard Block) |
|---|---|---|---|
| **DoR** | Trước khi Story vào Sprint | QA Lead / Scrum Master | AC không rõ ràng, chưa qua AI Requirement Review |
| **DoD - PR** | Trước khi merge code | Code Reviewer + QA Reviewer | Unit/Integration test fail, Smoke fail, thiếu AI Impact Report |
| **DoD - RC** | Trước khi release Production | QA Lead + PO (sign-off) | Còn bug Critical, Regression pass rate dưới ngưỡng |

---

## 5. Nguyên tắc vận hành chung

> **Testing as Code (TaC):** Mọi tiêu chí DoD/DoR trong tài liệu này phải được **thực thi hóa (enforce)** thông qua pipeline CI/CD (branch protection rules, required status checks), **không phụ thuộc vào việc con người tự giác kiểm tra thủ công** wherever có thể tự động hóa.

- [ ] Các quality gate ở §2 và §3 được cấu hình là **Required Status Checks** trên GitHub branch protection.
- [ ] Mọi ngoại lệ (Waiver/Exception) đều phải có **audit trail** bằng văn bản (comment PR, ticket Jira), không chấp nhận thỏa thuận miệng.
- [ ] Tài liệu DoD/DoR này được review và cập nhật định kỳ **mỗi Quý** hoặc sau mỗi Retrospective phát hiện gap trong quy trình.