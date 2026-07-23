# Software Requirements Specification (SRS) - Web A (To-Do List) - BDD Format

## 1. Introduction
- **Project Name**: Web A - To-Do List Application
- **Type**: Single-Page Web Application (SPA) / Vanilla JavaScript
- **Objective**: Define functional requirements using Given-When-Then scenarios for precise test case derivation.

---

## 2. Functional Scenarios (BDD Specifications)

### FR-01: Add Task (Thêm công việc)

#### Scenario: Add a valid task successfully
- **Given** người dùng đang ở giao diện chính của ứng dụng To-Do List
- **When** người dùng nhập nội dung "Học Testing as Code" vào ô nhập liệu và nhấn phím Enter (hoặc nút Add)
- **Then** công việc "Học Testing as Code" phải xuất hiện trong danh sách với trạng thái chưa hoàn thành (Pending)
- **And** ô nhập liệu phải được tự động làm trống

#### Scenario: Attempt to add an empty or whitespace-only task
- **Given** người dùng đang ở giao diện chính của ứng dụng
- **When** người dùng để trống ô nhập liệu (hoặc chỉ nhập khoảng trắng) và nhấn nút Add
- **Then** ứng dụng không được thêm công việc mới vào danh sách
- **And** có thể hiển thị thông báo lỗi hoặc giữ nguyên trạng thái ô nhập liệu

---

### FR-02: Complete/Toggle Task (Đánh dấu hoàn thành)

#### Scenario: Mark an active task as completed
- **Given** danh sách có sẵn một công việc chưa hoàn thành là "Mua sách Git"
- **When** người dùng click vào ô checkbox (hoặc tên) của công việc đó
- **Then** trạng thái của công việc chuyển thành hoàn thành (Completed)
- **And** tiêu đề công việc hiển thị hiệu ứng gạch ngang (strikethrough)

#### Scenario: Revert a completed task back to active
- **Given** danh sách có sẵn một công việc đã hoàn thành là "Mua sách Git"
- **When** người dùng click lại vào checkbox để bỏ chọn
- **Then** trạng thái công việc chuyển lại thành chưa hoàn thành (Active)
- **And** hiệu ứng gạch ngang trên tiêu đề bị gỡ bỏ

---

### FR-03: Delete Task (Xóa công việc)

#### Scenario: Delete a task from the list
- **Given** danh sách đang hiển thị công việc "Task cần xóa"
- **When** người dùng nhấn vào biểu tượng nút Xóa (Delete) tương ứng với công việc đó
- **Then** công việc "Task cần xóa" phải biến mất hoàn toàn khỏi giao diện người dùng
- **And** dữ liệu trong bộ nhớ trình duyệt (`localStorage`) cũng được cập nhật loại bỏ công việc này

---

### FR-04: Filter Tasks (Bộ lọc công việc)

#### Scenario: Filter to view only active tasks
- **Given** ứng dụng có cả công việc đang làm và đã hoàn thành
- **When** người dùng chọn bộ lọc "Active"
- **Then** giao diện chỉ hiển thị các công việc chưa hoàn thành

#### Scenario: Filter to view only completed tasks
- **Given** ứng dụng có cả công việc đang làm và đã hoàn thành
- **When** người dùng chọn bộ lọc "Completed"
- **Then** giao diện chỉ hiển thị các công việc đã được đánh dấu hoàn thành

---

## 3. Non-Functional Requirements
- **NFR-01: Performance**: 
  - **Given** người dùng mở trang web
  - **When** ứng dụng tải tài nguyên tĩnh
  - **Then** thời gian tải trang phải dưới 1 giây (do sử dụng Vanilla JS).
- **NFR-02: Data Persistence**: 
  - **Given** người dùng đã thêm một số task và tải lại trang (Refresh / F5)
  - **When** trình duyệt tải lại hoàn tất
  - **Then** toàn bộ dữ liệu task cũ vẫn phải được giữ nguyên nhờ `localStorage`.