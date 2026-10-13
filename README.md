# engineer-onboarding-tuan-1
# Ticket Manager CLI - Onboarding Week 1

Repository phục vụ chương trình Onboarding (Tuần 1): Nghiên cứu phương pháp luận **Test-Driven Development (TDD)**, phân tầng kiểm thử phần mềm và xây dựng quy trình kiểm soát chất lượng mã nguồn do AI tạo ra.

---

##  Tài liệu nghiên cứu (Deliverable)

Báo cáo nghiên cứu chi tiết đã được cập nhật và hoàn thiện theo góp ý từ mentor:
 **[Đọc tài liệu nghiên cứu chi tiết: docs/RESEARCH_TDD.md](./docs/RESEARCH_TDD.md)**

---

##  Nội dung chính trong tài liệu nghiên cứu

1. **TDD Core Principles & Red-Green-Refactor Cycle**:
   - Bản chất của việc viết test trước logic (Design tool, chống over-engineering).
   - Chi tiết chu trình 3 pha Red - Green - Refactor và ranh giới áp dụng TDD.
2. **Testing Levels: Unit, Integration, and End-to-End**:
   - Khái niệm, phạm vi và nguyên tắc mock cô lập I/O trong Unit Test.
   - Bảng so sánh 3 cấp độ kiểm thử kèm lưu ý về heuristic tỷ lệ 70/20/10.
   - Phân tích 4 sai lầm phổ biến trong kiểm thử (*Over-testing, Weak Assertions, Testing Implementation Details, Blind Trust in AI*).
3. **AI Code Validation & Workflows**:
   - Cơ chế dùng test suite làm hàng rào an toàn chống AI Hallucinations.
   - **Solution Exploration**: So sánh ưu/nhược điểm giữa *Vitest* và *Jest*, giải thích lý do lựa chọn Vitest cho dự án CLI.
   - Chiến thuật giao tiếp: *Layered Questioning* và *Iterative Refinement*.
4. **CLI Testing Best Practices & Code Examples**:
   - 4 vùng kiểm thử trọng yếu của ứng dụng CLI (Arguments, Validation, File I/O, Exit Codes).
   - Ma trận Test Cases minh họa (*Input -> Expected Output -> Test Level*).
   - Code snippet hoàn chỉnh mô phỏng chu trình Red - Green - Refactor (Vitest / TypeScript).
5. **Appendix: AI Interaction Evidence & Verification**:
   - **Case Study thực tế**: Minh chứng phản biện và chỉnh nắn code AI (phát hiện lỗi vi phạm tính cô lập khi ghi file thật -> yêu cầu tách Repository & Mocking -> kiểm chứng độc lập qua tài liệu Vitest & Martin Fowler).
   - Danh mục tài liệu tham khảo chính thống và link phiên trao đổi với AI.

---

##  Công nghệ & Công cụ đề xuất
- **Ngôn ngữ**: TypeScript / Node.js
- **Test Runner**: Vitest
- **Version Control**: Git / GitHub