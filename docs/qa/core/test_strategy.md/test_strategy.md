# Chiến lược kiểm thử & Khung tự động hóa - Web A (To-Do List)

## 1. Mục tiêu kiểm thử
- **Đảm bảo chất lượng chức năng:** Xác thực toàn bộ các luồng BDD Given-When-Then đã định nghĩa trong SRS (Thêm, Chuyển đổi trạng thái/Hoàn thành, Xóa, Lọc) hoạt động chính xác tuyệt đối.
- **Xác thực tính bền vững dữ liệu:** Đảm bảo cơ chế `localStorage` của trình duyệt duy trì trạng thái chính xác qua các lần tải lại trang (refresh).
- **Sẵn sàng và phát triển tự động hóa:** Thiết lập một quy trình tích hợp liền mạch, nơi chiến lược kiểm thử chuyển đổi mượt mà thành các script tự động hóa.

## 2. Phạm vi kiểm thử
- **Trong phạm vi (In-Scope):**
  - **Kiểm thử chức năng:** Tương tác CRUD và chuyển đổi trạng thái trên giao diện To-Do List.
  - **Kiểm thử UI/UX:** Các thành phần giao diện trực quan, hiệu ứng gạch ngang khi hoàn thành task, và các nút điều khiển phản hồi.
  - **Kiểm thử lưu trữ:** Tính toàn vẹn khi đọc/ghi của bộ nhớ cục bộ (Local Storage).
- **Ngoài phạm vi (Out-of-Scope):**
  - Kiểm thử hiệu năng nâng cao / tải trọng cao (vì đây là ứng dụng SPA chạy ở phía client nhẹ nhàng).
  - Kiểm thử bảo mật xâm nhập chuyên sâu.

## 3. Cấp độ kiểm thử & Phương pháp luận
- **Kiểm thử khám phá thủ công:** Thực hiện trực tiếp trên trình duyệt để kiểm tra hành vi giao diện ban đầu và độ mượt mà của trải nghiệm người dùng.
- **Kiểm thử chấp nhận:** Ánh xạ trực tiếp các kịch bản Gherkin (`Given-When-Then`) từ file `SRS/00-srs.md` thành các bước thực thi kiểm thử.
- **Chuyển đổi tự động hóa liên tục:** Ngay khi một tác vụ kiểm thử thủ công hoàn tất và được xác minh cho một tính năng, nhân sự kiểm thử sẽ lập tức viết script automation tương ứng để mở rộng bộ kiểm thử hồi quy (regression suite).

## 4. Phân tích mã nguồn do AI điều khiển & Tác động hồi quy
- **Kích hoạt & Phân tích:** Sau mỗi lần build hoặc tạo Pull Request, các workflow tự động sẽ tận dụng AI để phân tích sự thay đổi mã nguồn (code diffs) đối chiếu với các yêu cầu trong SRS.
- **Kết quả đầu ra tự động:**
  - **Phạm vi ảnh hưởng:** Xác định các module chịu tác động trực tiếp và gián tiếp.
  - **Kịch bản kiểm thử mục tiêu:** Lọc ra các trường hợp phù hợp từ `tests/03-test-cases/` để thực thi.
  - **Gợi ý script hồi quy:** Đề xuất hoặc làm nổi bật các script tự động hóa phù hợp cần chạy.

## 5. Quản lý rủi ro
- **Rủi ro 1: Mất dữ liệu khi xóa bộ nhớ đệm (cache) trình duyệt.**
  * *Giải pháp giảm thiểu:* Tài liệu hóa rõ ràng các giới hạn lưu trữ trong test case và định hướng tích hợp cơ sở dữ liệu backend trong tương lai.
- **Rủi ro 2: Thay đổi bộ định danh (locator) giao diện làm gãy script tự động hóa.**
  * *Giải pháp giảm thiểu:* Sử dụng các định danh rõ ràng, ổn định (`id`, `data-testid`) ngay từ giai đoạn khởi đầu dự án.

## 6. Tiêu chí đầu vào & đầu ra
- **Tiêu chí đầu vào (Entry Criteria):** Tài liệu SRS (`00-srs.md`) đã được phê duyệt trên nhánh `docs/srs`; mã nguồn cơ sở được triển khai trên nhánh `main`.
- **Tiêu chí đầu ra (Exit Criteria):** 100% các test case chức năng cốt lõi được thực thi và đạt (pass); không còn lỗi nghiêm trọng hoặc lỗi lớn nào tồn đọng.

## 7. Cấu trúc thư mục Testing as Code
```text
ai-testing-lab-web/
└── tests/
    ├── 01-strategy/           # Định nghĩa chiến lược kiểm thử & phạm vi
    ├── 02-processes/          # Quy trình STLC & hướng dẫn Definition of Done (DoD)
    ├── 03-test-cases/         # Test case chức năng và ánh xạ BDD
    ├── 04-reports/            # Báo cáo kết quả thực thi
    ├── 05-ai-impact-reports/  # Nhật ký phân tích tác động PR do AI hỗ trợ
    └── 06-automation-scripts/ # Script được viết ngay sau khi hoàn thành task manual
        ├── add-task.spec.js
        └── toggle-task.spec.js