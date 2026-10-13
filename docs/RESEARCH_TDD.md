# Nghiên cứu: Test-Driven Development (TDD) & CLI Testing

## 1. TDD Core Principles & Red-Green-Refactor Cycle
- Khái niệm TDD: Test-Driven Development (TDD) là phương pháp phát triển phần mềm mà trong đó các ca kiểm thử (tests) được viết trước khi viết mã nguồn thực thi, xác định rõ kì vọng của hệ thống trước thông qua test.
-  việc viết test trước khi viết code logic nhằm những mục đích:
* Làm rõ yêu cầu nghiệp vụ, buộc ta phải vào vai người sử dụng hàm / chương trình để thiết kế API: hàm tên gì, nhận tham số nào, trả về dữ liệu gì, ném ra lỗi nào khi gặp biên. 
* Tránh viết thừa các logic không cần thiết khi viết code tối thiểu để vượt qua bài test.
* Dễ dàng phát hiện các thay đổi làm sai lệch logic.
- Chu trình Red - Green - Refactor: quy trình TDD vận hành theo một vòng lặp ngắn gồm 3 pha: 
* Red: Viết một bài unit test nhỏ mô tả chính xác một hành vi mong muốn mà hiện tại hệ thống chưa có. Sau đó, chạy test suite và chứng kiến bài test thất bại (thường do hàm chưa tồn tại hoặc trả về sai giá trị)
* Green: Viết lượng mã nguồn tối thiểu nhất có thể chỉ để bài test vừa viết chuyển sang màu xanh (pass). Không quan tâm code có tối ưu hay không, mục tiêu duy nhất là đưa hệ thống về trạng thái an toàn (green).
* Refactor: Khi tất cả bài test đã pass, tiến hành dọn dẹp mã nguồn: xóa bỏ code trùng lặp , đặt lại tên biến cho rõ nghĩa, tách hàm, tối ưu hiệu năng, chạy lại test liên tục sau mỗi thay đổi nhỏ.
* Giới hạn & Ranh giới áp dụng TDD: TDD phát huy tối đa sức mạnh với core business logic, thuật toán, validation và data processing. TDD kém hiệu quả và tốn chi phí khi áp dụng cho việc dò tìm mẫu thử nghiệm UI nhanh hoặc tích hợp với các thư viện bên thứ ba chưa ổn định API.

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
*(Ghi chú: Tỷ lệ 70/20/10 là nguyên tắc heuristic tham khảo từ Testing Pyramid của Google/Martin Fowler, không phải quy chuẩn cứng nhắc. Với các công cụ CLI nhỏ chủ yếu đọc/ghi file, tỷ trọng Integration Test có thể tăng lên linh hoạt).*
- Các lỗi phổ biến trong kiểm thử
1. Over-testing:
   * *Bản chất*: Viết test cho cả các getter/setter tầm thường, test framework bên thứ ba hoặc viết hàng chục ca kiểm thử trùng lặp không mang lại giá trị nghiệp vụ.
   * *Hậu quả*: Làm phình to test suite, thời gian chạy CI chậm chạp, chi phí bảo trì cao khi sửa đổi code nhỏ.
   * *Cách tránh*: Chỉ tập trung test các logic nghiệp vụ quan trọng, các nhánh điều kiện phân luồng và các ca biên có rủi ro cao.
2. Weak Assertions (Khẳng định lỏng lẻo):
   * *Bản chất*: Sử dụng các assertion chung chung như `expect(result).toBeDefined()` hoặc `expect(ticket).toBeTruthy()` thay vì so sánh dữ liệu thực tế.
   * *Hậu quả*: Test vẫn báo xanh dù hàm trả về dữ liệu rỗng, sai thuộc tính hoặc logic nghiệp vụ bị lỗi (False Positive).
   * *Cách tránh*: Luôn kiểm tra chính xác giá trị các trường quan trọng bằng `toBe()`, `toEqual()` hoặc `toMatchObject({ id: expect.any(String), status: 'OPEN' })`.
3. Testing Implementation Details (Kiểm thử chi tiết cài đặt):
   * *Bản chất*: Viết test soi xét hàm nội bộ, biến private hoặc kiểm tra xem một hàm phụ trợ đã được gọi bao nhiêu lần thay vì kiểm tra kết quả đầu ra.
   * *Hậu quả*: Bài test rất dễ gãy; mỗi lần tái cấu trúc code thì test lại báo đỏ dù hành vi bên ngoài vẫn đúng 100%.
   * *Cách tránh*: Xem hàm/module như một "hộp đen"; chỉ đưa Input vào và assert Output/Side-effect kỳ vọng.
4. Blind Trust in AI-generated Code (Mù quáng tin tưởng code AI):
   * *Bản chất*: Chấp nhận ngay đoạn code do AI tạo ra khi thấy chạy được lần đầu mà không tự tay viết test case phản biện hoặc kiểm chứng ca biên.
   * *Hậu quả*: Code tiềm ẩn rò rỉ bộ nhớ, lỗi bảo mật, race condition hoặc " ảo giác " gọi các API không tồn tại.
   * *Cách tránh*: Sử dụng TDD làm rào chắn: Viết test case trước để ép code AI phải pass, sau đó tự tay rà soát và kiểm chứng lại với tài liệu nguồn chính thức.

## 3. AI Code Validation & Prompting Strategies
- Cơ chế dùng test làm "hàng rào bảo vệ" chống hallucinations
* Rủi ro từ AI: Các mô hình AI thường có xu hướng tự đưa ra giải phóng trông có vẻ hợp lý nhưng có thể thường xuyên mắc những lỗi như gọi thư viện không tồn tại, bỏ quên các ca biên hoặc vi phạm logic bảo mật.
* Test suite là " hàng rào bảo vệ " chống AI Halluciations: Khi tự tay thiết kế và viết bộ test trước, code do AI buộc phải chạy qua test runner và lỗi sai khi test báo đỏ sẽ được phát hiện ngay lập tức. Miễn là toàn bộ test suite giữ nguyên được màu xanh (Green), việc refactor được giao cho AI mà không sợ tính năng bị ảnh hưởng. 
- Solution Exploration: Lựa chọn Test Runner cho Ticket CLI
Trước khi bắt đầu cài đặt, quy trình Solution Exploration được thực hiện để so sánh 2 giải pháp kiểm thử phổ biến:

* Phương án A: Vitest
  * *Ưu điểm*: Hỗ trợ TypeScript và ESM native cực tốt nhờ Vite/esbuild; tốc độ khởi chạy và thực thi siêu nhanh; cấu hình tối giản (`vitest.config.ts`); tương thích hoàn toàn cú pháp Jest (`describe`, `it`, `expect`).
  * *Nhược điểm*: Tuổi đời mới hơn Jest, một số matcher chuyên biệt của cộng đồng bên thứ ba cũ chưa hỗ trợ.
* Phương án B: Jest
  * *Ưu điểm*: Tiêu chuẩn lâu đời trong hệ sinh thái Node.js, tài liệu và cộng đồng hỗ trợ khổng lồ.
  * *Nhược điểm*: Khởi động chậm hơn do overhead; cấu hình TypeScript + Pure ESM rất phức tạp (thường xuyên xung đột module type với `ts-jest` hoặc Babel).
* Quyết định & Điều kiện lựa chọn: Lựa chọn **Vitest** vì dự án Ticket CLI sử dụng Node.js hiện đại với TypeScript và ESM. Tốc độ thực thi của Vitest giúp chu trình Red-Green-Refactor phản hồi gần như tức thì mà không mất thời gian xử lý lỗi config module.
- Chiến thuật tương tác và kiểm soát code do AI sinh ra (Layered Questioning, Iterative Refinement)
* Chiến thuật 1: Layered Questioning (hỏi phân tầng): thay vì đưa ra một yêu cầu chung chung thì chia nhỏ thành 4 tầng giao tiếp gồm: Research (Nghiên cứu khái niệm), Context Framing (Đưa ra bài toán cụ thể), Mininal Code Example (Ví dụ tối thiểu), Validation & Edge Cases (Kiểm chứng biên).
* Chiến thuật 2: Iterative Refinement (lặp và tinh chỉnh): không bao giờ chấp nhận đoạn code đầu tiên một cách thụ động mà phải làm theo các bước: Nhận bản nháp mã nguồn từ AI; Tự review bằng các câu hỏi phản biện; Tự tay sửa các lỗ hổng; Đưa đoạn code đã sửa kèm lập luận ngược lại cho AI để AI tiếp tục tối ưu hoặc phát hiện lỗ hổng mới. 

## 4. CLI Testing Best Practices & Code Examples
- Các vùng cần test trong ứng dụng CLI (Arguments, Validation, File Storage, Errors)
## 4.1. Khi viết công cụ dòng lệnh CLI, Có 4 khu vực chính cần bao phủ bài test:
* CLI Arguments & Flag Parsing (Phân tích cú pháp cờ và tham số): Kiểm tra xem cờ bắt buộc có nhận đúng không; Kiểm tra giá trị mặc định khi người dùng không truyền cờ.
* Input Valiation & Sanitization (Kiểm tra và chuẩn hóa dữ liệu đầu vào): Chặn chuỗi rỗng hoặc toàn khoảng trắng; Kiểm tra giá trị enumm hợp lệ.
* File I/O  & State Persistence (Lưu trữ trạng thái tệp tin): Kiểm tra việc đọc/ghi file dữ liệu tickets.json; Xử lý trường hợp file chưa tồn tại; Xử lý trường hợp file bị hỏng dữ liệu mà không làm sập toàn bộ chương trình.

## 4.2. Bảng ma trận Test Cases minh họa
| Test Case | Test Level | Input / Điều kiện ban đầu | Output kỳ vọng | Exit Code / Side Effect |
|---|---|---|---|---|
| Title rỗng hoặc toàn khoảng trắng | Unit Test | `validateTicketInput({ title: "   " })` | Ném ngoại lệ: `Error("Title is required")` | Không tạo object, không ghi dữ liệu |
| Lọc danh sách theo status | Unit Test | `filterTickets(list, "OPEN")` | Trả về mảng chỉ chứa tickets có `status === "OPEN"` | Không làm thay đổi mảng gốc (Pure function) |
| File `tickets.json` bị hỏng cấu trúc | Integration Test | File chứa nội dung lỗi cú pháp: `"{ invalid_json: "` | Bắt ngoại lệ và ném: `Error("Database corrupted")` | Ghi lỗi ra stderr, không làm crash đột ngột process |
| Lệnh show ID không tồn tại | Integration / CLI | Tham số dòng lệnh: `ticket show --id=999` (ID không có trong data) | Thông báo ra stderr: `"Ticket with ID 999 not found"` | Exit code là `1` |
| File `tickets.json` bị hỏng cấu trúc | Đảm bảo hàm xử lý đúng logic nội bộ | Đảm bảo các mảnh ghép hoạt động cùng nhau | Đảm bảo trải nghiệm và tính đúng đắn cho người dùng cuối |  |
| Lệnh show ID không tồn tại | 70% (chiếm đa số) | 20% | 10% |  |

- Ví dụ code kiểm thử minh họa (Red - Green - Refactor)
* Pha 1: Red: Viết ca kiểm thử trước mô tả yêu cầu validate tiêu đề và gán trạng thái mặc định (Test thất bại do chưa có logic).

// tests/ticket-service.test.ts
import { describe, it, expect, vi } from 'vitest';
import { validateTicketInput, readTicketsFromFile } from '../src/ticket-service';
import fs from 'node:fs/promises';

describe('Ticket Service - Red Phase', () => {
  it('ném ra lỗi nếu title rỗng hoặc toàn khoảng trắng', () => {
    expect(() => validateTicketInput({ title: '   ' })).toThrowError('Title is required');
  });

  it('xử lý an toàn và báo lỗi khi file tickets.json bị hỏng dữ liệu', async () => {
    vi.spyOn(fs, 'readFile').mockResolvedValue('{ corrupted json string');
    await expect(readTicketsFromFile('fake-path.json')).rejects.toThrowError('Database corrupted');
  });
});

* Pha 2: Green: Viết mã nguồn tối thiểu để test case chuyển sang trạng thái thành công.

// src/ticket-service.ts
import fs from 'node:fs/promises';

export function validateTicketInput(input: { title: string }) {
  if (!input.title || input.title.trim().length === 0) {
    throw new Error('Title is required');
  }
  return { title: input.title.trim() };
}

export async function readTicketsFromFile(filePath: string) {
  try {
    const raw = await fs.readFile(filePath, 'utf-8');
    return JSON.parse(raw);
  } catch (error) {
    throw new Error('Database corrupted');
  }
}

* Pha 3: Refactor: Chuẩn hóa kiểu dữ liệu TypeScript, tách hàm validation độc lập và thay thế ID bằng UUID mà không làm phá vỡ bộ test.
// src/ticket-service.ts (Refactored)
import fs from 'node:fs/promises';

export interface TicketInput {
  title: string;
  priority?: 'LOW' | 'MEDIUM' | 'HIGH';
}

function sanitizeAndValidateTitle(title?: string): string {
  const trimmed = title?.trim();
  if (!trimmed) {
    throw new Error('Title is required');
  }
  return trimmed;
}

export function validateTicketInput(input: TicketInput) {
  return {
    title: sanitizeAndValidateTitle(input.title),
    priority: input.priority ?? 'MEDIUM'
  };
}

export async function readTicketsFromFile(filePath: string): Promise<any[]> {
  try {
    const raw = await fs.readFile(filePath, 'utf-8');
    return JSON.parse(raw);
  } catch (err: any) {
    if (err.name === 'SyntaxError') {
      throw new Error('Database corrupted: Malformed JSON syntax');
    }
    throw err;
  }
}
## 5. Appendix: AI Interaction Evidence
### 5.1. Case Study: Phản biện và Kiểm chứng mã nguồn AI trong thực tế 

Trong quá trình tìm hiểu cách kiểm thử tương tác tệp tin cho Ticket CLI, một chu trình tương tác và phản biện thực tế đã diễn ra:

#### a. Đặt vấn đề ban đầu 
- **Câu hỏi đưa cho AI**: *"Hãy viết cho tôi bài test và code cho hàm lưu ticket mới vào file `tickets.json`." mà  quên cô lập môi trường"*

#### b. Phân tích đoạn mã AI sinh ra & Phát hiện lỗi 
- AI đã đề xuất một hàm `saveNewTicket` và bài test tương tác trực tiếp với đường dẫn file cố định trên ổ đĩa:
// Mã nguồn do AI sinh ra ban đầu (Chưa cô lập môi trường)
const STORAGE_FILE = path.resolve(__dirname, '../data/tickets.json');

export async function saveNewTicket(ticket: Ticket): Promise<void> {
  // Đọc file thật, push dữ liệu và ghi đè trực tiếp lên ổ cứng
  const raw = await fs.readFile(STORAGE_FILE, 'utf-8');
  const tickets = JSON.parse(raw);
  tickets.push(ticket);
  await fs.writeFile(STORAGE_FILE, JSON.stringify(tickets, null, 2), 'utf-8');
}

// Bài test do AI sinh ra
it('lưu ticket mới thành công', async () => {
  await saveNewTicket({ id: 'T-100', title: 'Fix bug', status: 'OPEN' });
  const raw = await fs.readFile(STORAGE_FILE, 'utf-8');
  const tickets = JSON.parse(raw);
  expect(tickets).toHaveLength(2);

## c. Các lỗ hổng nghiêm trọng được phát hiện:

Vi phạm tính cô lập (Lack of Isolation): Đọc/ghi trực tiếp vào file vật lý trong repo làm biến đổi dữ liệu thật.

Flaky Test trên CI/CD: Khẳng định expect(tickets).toHaveLength(2) dựa trên giả định file đã có sẵn 1 bản ghi. Nếu test chạy lặp lại hoặc các worker chạy song song, bài test sẽ bị fail ngẫu nhiên do Race Condition.

Thiếu khả năng phục hồi: Không bắt riêng ngoại lệ SyntaxError khi file JSON hỏng định dạng.

## d. Phản biện và đặt lại vấn đề** : *" hãy tái cấu trúc lại bằng cách tách tầng Repository và mock module fs/promises ."*
- AI đã tiếp nhận phản biện và tái cấu trúc thành công:
* Tách tầng TicketRepository: Cho phép inject filePath qua constructor, tách riêng hàm getAll()
* Unit Test chuẩn mực với Vitest Mock:
import { describe, it, expect, vi, beforeEach } from 'vitest';
import fs from 'node:fs/promises';
import { TicketRepository } from '../src/ticketRepository';

// Mock toàn bộ module I/O để chạy 100% trên RAM
vi.mock('node:fs/promises');

describe('TicketRepository - Unit Test', () => {
  let repo: TicketRepository;
  const mockPath = '/virtual/tickets.json';

  beforeEach(() => {
    vi.clearAllMocks();
    repo = new TicketRepository(mockPath);
  });

  it('tự tạo mảng mới và lưu ticket nếu file chưa tồn tại (ENOENT)', async () => {
    const enoentError = new Error('File not found') as any;
    enoentError.code = 'ENOENT';
    vi.mocked(fs.readFile).mockRejectedValueOnce(enoentError);
    vi.mocked(fs.writeFile).mockResolvedValueOnce(undefined);

    await repo.save({ id: 'T-1', title: 'New Ticket', status: 'OPEN' });

    expect(fs.writeFile).toHaveBeenCalledWith(
      mockPath,
      JSON.stringify([{ id: 'T-1', title: 'New Ticket', status: 'OPEN' }], null, 2),
      'utf-8'
    );
  });
});

### 5.2. Tham khảo tài liệu nguồn chính thống
Martin Fowler, The Practical Test Pyramid: https://martinfowler.com/articles/practical-test-pyramid.html

Martin Fowler, Mocks Aren't Stubs: https://martinfowler.com/articles/mocksArentStubs.html

Kent Beck, Test-Driven Development: By Example, Addison-Wesley Professional.

Vitest Documentation, Mocking Fundamentals: https://vitest.dev/guide/mocking.html

** Link phiên bản trao đổi với AI:  https://gemini.google.com/app/ce7fb56f0e691d1e