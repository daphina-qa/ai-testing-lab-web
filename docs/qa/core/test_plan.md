# TEST PLAN: WEB A - TO-DO LIST APPLICATION (Release/Sprint hiện tại)

**Dự án:** Web A - To-Do List Application (Repo: `WEBLABSAI`)  
**Loại tài liệu:** Test Plan (kế hoạch thi công cho đợt test này)  
**Tài liệu liên quan:**
- Chiến lược & Automation tổng thể: `docs/qa/core/test_strategy.md`
- Quy trình kiểm thử (STLC): `docs/qa/core/test_processes-stlc.md`
- Định nghĩa hoàn thành (DoD): `docs/qa/core/test_processes-dod.md`
- Chi tiết Test Case: `docs/qa/test-cases/`
- SRS: `docs/srs.md`

> Tài liệu này **không lặp lại** phần Scope/Automation/Entry-Exit Criteria chung — các phần đó tham chiếu ngược về `test_strategy.md`. Test Plan chỉ mô tả **kế hoạch triển khai cụ thể của đợt test này**: timeline, nhân sự, deliverable, rủi ro theo tiến độ.

---

## 1. MỤC TIÊU CỦA ĐỢT TEST NÀY
- Viết & thực thi 100% test case cho FR-01 → FR-04, NFR-01, NFR-02 (theo scope đã định nghĩa tại `test_strategy.md` mục 2).
- Pass rate ≥ 98% trước khi bàn giao.
- Có báo cáo kết quả kiểm thử (Execution Report) và bộ automation script cho các case ổn định, bàn giao cuối đợt.

---

## 2. LỊCH TRÌNH (SCHEDULE) — Tổng: 4 ngày

| Ngày | Hoạt động | Deliverable | Phụ trách |
|---|---|---|---|
| **Ngày 1** | Viết Test Case chi tiết (từ Test Scenario trong `test_strategy.md`) + bắt đầu Execution | File test case tại `docs/qa/test-cases/`, kết quả execution phần đã test | Tester A + Tester B (song song, theo phân công mục 4) |
| **Ngày 2** | Hoàn tất Execution toàn bộ test case | Execution log đầy đủ (Pass/Fail/Blocked), danh sách bug | Tester A + Tester B |
| **Ngày 3 (sáng)** | Fix bug (dev) + Retest các case Fail (QA) | Bug đã fix + retest note | Dev + QA phối hợp |
| **Ngày 3 (chiều)** | Viết báo cáo kiểm thử (Execution Report) | `docs/qa/reports/execution/` | 1 người tổng hợp, người còn lại review |
| **Ngày 4** | Viết/bổ sung Automation Script cho các case đã Pass ổn định (theo `test_strategy.md` mục 3-4) | Script tại `tests/automation/` | Tester A + Tester B chia theo module |

**Lưu ý tiến độ:**
- Automation **chỉ áp dụng cho case đã Pass ổn định** ở Ngày 2 — không automate case còn đang Fail/chưa chốt, tránh viết lại script.
- Nếu Ngày 2 phát sinh nhiều bug hơn dự kiến, ưu tiên cắt giảm phạm vi automation (Ngày 4) trước, giữ nguyên thời gian fix bug/report.

---

## 3. PHẠM VI TEST CASE CỦA ĐỢT NÀY

*Danh sách đầy đủ Test Scenario ID xem tại `test_strategy.md`. Bảng dưới đây chỉ map nhanh nhóm chức năng → độ ưu tiên thực thi trong 2 ngày đầu.*

| Nhóm | Mã Test Scenario | Ưu tiên |
|---|---|---|
| FR-01: Add Task | TC_FR01_01 → 07 | Cao |
| FR-02: Toggle Task | TC_FR02_01 → 03 | Cao |
| FR-03: Delete Task | TC_FR03_01 → 03 | Cao |
| FR-04: Filter Tasks | TC_FR04_01 → 04 | Trung bình |
| NFR-01: Performance | TC_NFR01_01 | Trung bình |
| NFR-02: Data Persistence | TC_NFR02_01 → 03 | Cao |

Thứ tự thực thi ưu tiên **Cao** trước trong Ngày 1, nhóm **Trung bình** làm cuối Ngày 1 hoặc đầu Ngày 2, để đảm bảo case rủi ro cao (mất dữ liệu, CRUD) được test sớm nhất.

---

## 4. PHÂN CÔNG NHÂN SỰ (2 QA)

| Tester | Phụ trách | Ghi chú |
|---|---|---|
| **Tester A** | FR-01 (Add), FR-02 (Toggle), FR-03 (Delete) — 13 TC | Viết case + execute + automate phần này |
| **Tester B** | FR-04 (Filter), NFR-01 (Performance), NFR-02 (Data Persistence) — 8 TC | Viết case + execute + automate phần này, kiêm chạy regression cross-browser sau khi cả 2 pass Chrome |

**Check chéo (risk-based, không review 100%):**
- `TC_FR01_06` (XSS) và toàn bộ nhóm `TC_NFR02_*` (data persistence) được review chéo giữa 2 người trước khi đánh dấu Pass, vì đây là case rủi ro cao, dễ bỏ sót do phụ thuộc cách implement (`textContent` vs `innerHTML`, xử lý `localStorage` null).
- Áp dụng ngay trong Ngày 1-2 (execution), không để dồn cuối đợt.

---

## 5. RỦI RO THEO TIẾN ĐỘ ĐỢT TEST NÀY

*(Bổ sung cho rủi ro kỹ thuật đã nêu ở `test_strategy.md` mục 5 — ở đây là rủi ro về mặt lịch trình/vận hành của đợt test 4 ngày)*

| Rủi ro | Tác động | Biện pháp giảm thiểu |
|---|---|---|
| Viết TC + execute dồn trong 2 ngày, dễ thiếu case edge (do vội) | Cao | Ưu tiên case rủi ro cao trước (mục 3), case ít rủi ro có thể lược bớt nếu thiếu thời gian |
| Chỉ có 0.5 ngày fix bug — nếu bug nhiều/nặng sẽ không kịp | Cao | Có thể đẩy fix bug sang buổi chiều Ngày 3 (đổi chỗ với viết báo cáo), báo cáo làm rút gọn |
| Chỉ có 1 ngày automation — không đủ automate hết | Trung bình | Chỉ automate case Pass ổn định, ưu tiên nhóm CRUD (FR-01/02/03) vì tần suất regression cao nhất |

---

## 6. ENTRY / EXIT CRITERIA CỦA ĐỢT TEST NÀY

- **Entry:** Build ứng dụng (`index.html` + code liên quan trên `main`) đã sẵn sàng đầu Ngày 1; Test Scenario tại `test_strategy.md` và quy trình tại `test_processes-stlc.md` đã được duyệt.
- **Exit:** Đạt tiêu chí chung tại `test_strategy.md` mục 6 và `test_processes-dod.md` (Definition of Done) — 100% case core pass, không còn lỗi Critical/Major — **và** đã bàn giao Execution Report + Automation Script đúng hạn Ngày 4.