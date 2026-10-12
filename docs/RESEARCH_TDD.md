# Nghiên cứu: Test-Driven Development (TDD) & CLI Testing

## 1. TDD Core Principles & Red-Green-Refactor Cycle
- Khái niệm TDD: Test-Driven Development (TDD) là phương pháp phát triển phần mềm mà  trong đó các ca kiểm thử (tests) được viết trước khi viết mã nguồn thực thi, xác định rõ kì vọng của hệ thống trước thông qua test.
-  việc viết test trước khi viết code logic nhằm những mục đích:
* Làm rõ yêu cầu nghiệp vụ, buộc ta phải vào vai người sử dụng hàm / chương trình để thiết kế API: hàm tên gì, nhận tham số nào, trả về dữ liệu gì, ném ra lỗi nào khi gặp biên. 
* Tránh viết thừa các logic không cần thiết khi viết code tối thiểu để vượt qua bài test.
* Dễ dàng phát hiện các thay đổi làm sai lệch logic.
- Chu trình Red - Green - Refactor: quy trình TDD vận hành theo một vòng lặp ngắn gồm 3 pha: 
* Red: Viết một bài unit test nhỏ mô tả chính xác một hành vi mong muốn mà hiện tại hệ thống chưa có. Sau đó, chạy test suite và chứng kiến bài test thất bại (thường do hàm chưa tồn tại hoặc trả về sai giá trị)
* Green: Viết lượng mã nguồn tối thiểu nhất có thể chỉ để bài test vừa viết chuyển sang màu xanh (pass). Không quan tâm code có tối ưu hay không, mục tiêu duy nhất là đưa hệ thống về trạng thái an toàn (green).
* Refactor: Khi tất cả bài test đã pass, tiến hành dọn dẹp mã nguồn: xóa bỏ code trùng lặp , đặt lại tên biến cho rõ nghĩa, tách hàm, tối ưu hiệu năng, chạy lại test liên tục sau mỗi thay đổi nhỏ.

## 2. Testing Levels: Unit, Integration, and End-to-End
- Định nghĩa và phạm vi: Unit Test, Integration Test, E2E Test
* Unit Test (Kiểm thử đơn vị): Kiểm tra các đơn vị mã nguồn nhỏ nhất có thể cô lập (hàm, phương thức, class). Mọi phụ thuộc bên ngoài (file system, network, database) đều được tạo dữ liệu giả lập.
VÍ DỤ trong Ticket CLI: Test hàm validateTicketInput(title, desc) kiểm tra tiêu đề có rỗng hay không, hoặc test hàm định dạng ngày tháng hiển thị ticket.
* Integration Test (Kiểm thử tích hợp): Kiểm tra sự phối hợp tương tác giữa hai hoặc nhiều module, hoặc giữa mã nguồn ứng dụng với các hệ thống ngoại vi thực tế.
VÍ DỤ trong Ticket CLI: Test service lưu trữ TicketRepository tương tác trực tiếp với hệ thống tệp tin thật (đọc, ghi file tickets.json, kiểm tra xử lý khi file bị lỗi cấu trúc dữ liệu JSON hỏng).
* End-to-End Test (Kiểm thử đầu cuối): Mô phỏng toàn bộ luồng người dùng thực tế chạy từ đầu đến cuối trên môi trường giống thật nhất.
VÍ DỤ trong Ticket CLI: Chạy trực tiếp câu lệnh từ terminal (ví dụ: thực thi node dist/index.js create --title "Lỗi hệ thống") và kiểm tra mã thoát, chuỗi in ra stdout/stderr, cùng trạng thái file dữ liệu sau khi lệnh kết thúc.
- Bảng so sánh ưu / nhược điểm giữa các cấp độ:
| Tiêu chí so sánh     | Unit Test                           | Integration Test                          | End-to-End (E2E) Test                                    |   |
|----------------------|-------------------------------------|-------------------------------------------|----------------------------------------------------------|---|
| Phạm vi (Scope)      | Đơn vị nhỏ nhất, cô lập logic       | Sự kết nối giữa các module/I-O            | Toàn bộ ứng dụng từ đầu vào đến đầu ra                   |   |
| Tốc độ thực thi      | Cực nhanh (vài mili-giây)           | Trung bình                                | Chậm nhất (vài giây đến vài phút)                        |   |
| Chi phí thiết lập    | Thấp, dễ bảo trì                    | Trung bình (cần tạo mock dữ liệu/tệp tạm) | Cao, dễ gãy (flaky) khi môi trường đổi                   |   |
| Mức độ phụ thuộc     | Hoàn toàn cô lập (Mock I/O)         | Phụ thuộc file, cache, DB hoặc network    | Phụ thuộc toàn bộ runtime, môi trường OS                 |   |
| Độ tin cậy nghiệp vụ | Đảm bảo hàm xử lý đúng logic nội bộ | Đảm bảo các mảnh ghép hoạt động cùng nhau | Đảm bảo trải nghiệm và tính đúng đắn cho người dùng cuối |   |
| Tỷ trọng đề xuất     | 70% (chiếm đa số)                   | 20%                                       |10%                                                      |   |

## 3. AI Code Validation & Prompting Strategies
- Cơ chế dùng test làm "hàng rào bảo vệ" chống hallucinations
* Rủi ro từ AI: Các mô hình AI thường có xu hướng tự đưa ra giải phóng trông có vẻ hợp lý nhưng có thể thường xuyên mắc những lỗi như gọi thư viện không tồn tại, bỏ quên các ca biên hoặc vi phạm logic bảo mật.
* Test suite là " hàng rào bảo vệ " chống AI Halluciations: Khi tự tay thiết kế và viết bộ test trước, code do AI buộc phải chạy qua test runner và lỗi sai khi test báo đỏ sẽ được phát hiện ngay lập tức. Miễn là toàn bộ test suite giữ nguyên được màu xanh (Green), việc refactor được giao cho AI mà không sợ tính năng bị ảnh hưởng. 
- Chiến thuật tương tác và kiểm soát code do AI sinh ra (Layered Questioning, Iterative Refinement)
* Chiến thuật 1: Layered Questioning (hỏi phân tầng): thay vì đưa ra một yêu cầu chung chung thì chia nhỏ thành 4 tầng giao tiếp gồm: Research (Nghiên cứu khái niệm), Context Framing (Đưa ra bài toán cụ thể), Mininal Code Example (Ví dụ tối thiểu), Validation & Edge Cases (Kiểm chứng biên).
* Chiến thuật 2: Iterative Refinement (lặp và tinh chỉnh): không bao giờ chấp nhận đoạn code đầu tiên một cách thụ động mà phải làm theo các bước: Nhận bản nháp mã nguồn từ AI; Tự review bằng các câu hỏi phản biện; Tự tay sửa các lỗ hổng; Đưa đoạn code đã sửa kèm lập luận ngược lại cho AI để AI tiếp tục tối ưu hoặc phát hiện lỗ hổng mới. 
* Chiến thuật 3: Kĩ thuật " Teach AI " ( Chỉnh nắn ngữ cảnh ): Khi phát hiện AI dùng hàm lỗi thời hoặc tự chế API không có thật, chỉ ro lỗi và cung cấp tài liệu chính xác ngay trong prompt tiếp theo, giúp chuẩn hóa ngữ cảnh của phiên trò chuyện, ngăn chặn AI tiếp tục lặp lại ảo giác ở các câu lệnh sau.
## 4. CLI Testing Best Practices & Code Examples
- Các vùng cần test trong ứng dụng CLI (Arguments, Validation, File Storage, Errors)
Khi viết công cụ dòng lệnh CLI, Có 4 khu vực chính cần bao phủ bài test:
* CLI Arguments & Flag Parsing (Phân tích cú pháp cờ và tham số): Kiểm tra xem cờ bắt buộc có nhận đúng không; Kiểm tra giá trị mặc định khi người dùng không truyền cờ.
* Input Valiation & Sanitization (Kiểm tra và chuẩn hóa dữ liệu đầu vào): Chặn chuỗi rỗng hoặc toàn khoảng trắng; Kiểm tra giá trị enumm hợp lệ.
* File I/O  & State Persistence (Lưu trữ trạng thái tệp tin): Kiểm tra việc đọc/ghi file dữ liệu tickets.json; Xử lý trường hợp file chưa tồn tại; Xử lý trường hợp file bị hỏng dữ liệu mà không làm sập toàn bộ chương trình.
- Ví dụ code kiểm thử minh họa (Red - Green - Refactor)
* Pha 1: Red: Viết ca kiểm thử trước mô tả yêu cầu validate tiêu đề và gán trạng thái mặc định (Test thất bại do chưa có logic).
* Pha 2: Green: Viết mã nguồn tối thiểu để test case chuyển sang trạng thái thành công.
* Pha 3: Refactor: Chuẩn hóa kiểu dữ liệu TypeScript, tách hàm validation độc lập và thay thế ID bằng UUID mà không làm phá vỡ bộ test.
## 5. Appendix: AI Interaction Evidence
- Link cuộc trò chuyện với AI
** Mục tiêu phiên làm việc: Nghiên cứu lý thuyết TDD, phân tầng kiểm thử, thiết lập kịch bản mẫu cho Ticket CLI và xây dựng quy trình kiểm soát mã nguồn AI sinh ra.
** Chiến thuật tương tác đã áp dụng: 
  - *Layered Questioning*: Đi từ tìm hiểu bản chất TDD -> Phân tích cấp độ Unit/Integration/E2E -> Xác lập quy trình AI Guardrail -> Viết code thực tế kèm phân tích ca biên.
  - *Critical Refinement*: Chủ động đặt các câu hỏi phản biện về ranh giới áp dụng, rủi ro của việc mock quá mức và các ca biên (edge cases) trong xử lý CLI.
** Link phiên bản trao đổi với AI:  https://gemini.google.com/app/ce7fb56f0e691d1e