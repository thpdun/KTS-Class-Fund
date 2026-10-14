# PROJECT PLAN — KTS Class Fund

> **Dự án:** KTS Class Fund — Hệ thống Quản lý Quỹ lớp Kinh tế Số trên Blockchain  
> **Học phần:** ECO2432 — Tiền điện tử và Hợp đồng thông minh  
> **Repository:** `https://github.com/thpdun/KTS-Class-Fund`

---

## 1. Thành viên nhóm và phân công vai trò

| STT | Họ và tên | Mã sinh viên | Vai chính Lab 8–11 (Dự kiến) | Vai chính Lab 12–15 (Dự kiến) | Trạng thái xác nhận |
|---|---|---|---|---|---|
| 1 | **Trần Hoàng Phương Dung** | 23K4300004 | Đặc tả nghiệp vụ, Kiến trúc & Hợp đồng thông minh | Phát triển Giao diện DApp, Kiểm thử tích hợp | `[CẦN NHÓM XÁC NHẬN]` |
| 2 | **Võ Ngọc Tâm Phúc** | 23K4300015 | Thiết kế kiểm thử, Quy tắc kinh tế, Nhật ký AI | Tối ưu hóa Hợp đồng, Audit bảo mật & Deploy | `[CẦN NHÓM XÁC NHẬN]` |

*Ghi chú:* Phân công vai trò trên là phương án phối hợp làm việc dự kiến của nhóm, có sự hỗ trợ chéo và xoay vai giữa hai giai đoạn để cả hai thành viên đều nắm vững toàn bộ quy trình Web3 từ Contract đến Frontend; cần họp nhóm để biểu quyết chốt chính thức (`[CẦN NHÓM XÁC NHẬN]`).

---

## 2. Người dùng, vấn đề và sản phẩm bàn giao

- **Người dùng chính:**
  1. *Sinh viên lớp Kinh tế số:* Người nạp quỹ, giám sát số dư và theo dõi tính minh bạch của các khoản chi.
  2. *Ban cán sự lớp (Class Officer):* Người tạo đề xuất, độc quyền biểu quyết tập thể (ngưỡng 51%) và độc quyền kích hoạt giải ngân.
  3. *Cố vấn học tập (Advisor):* Quản lý nhân sự Ban cán sự của lớp và tạo đề xuất chi trong phạm vi lớp phụ trách.
  4. *Giảng viên bộ môn (Lecturer):* Tạo đề xuất chi phục vụ hoạt động môn học trong đợt học đang mở.
  5. *Quản trị viên hệ thống (System Admin):* Quản lý danh mục Giảng viên, cấu trúc đợt học và phân công giảng dạy.
- **Vấn đề cần giải quyết:** Quản lý quỹ lớp thủ công bằng sổ sách/chuyển khoản cá nhân thiếu minh bạch, khó đối soát lịch sử thu - chi, tiềm ẩn rủi ro lạm quyền hoặc chi tiêu không có sự đồng thuận của tập thể.
- **Sản phẩm cuối nhìn thấy được (Dự kiến hoàn thiện tại Lab 15):** 
  - Hợp đồng thông minh hoàn chỉnh triển khai trên mạng thử nghiệm Ethereum Sepolia Testnet.
  - Giao diện Web DApp công khai cho phép kết nối ví MetaMask, xem số dư quỹ cô lập theo lớp, khởi tạo đề xuất chi, biểu quyết on-chain và tra cứu lịch sử giao dịch.

---

## 3. Các mốc bắt buộc, sản phẩm bàn giao và trách nhiệm chi tiết

| Mốc thực hiện | Mục tiêu & Sản phẩm bàn giao chính | Người phụ trách chính | Người phối hợp / Rà soát | Hạn chót & Minh chứng cần lưu |
|---|---|---|---|---|
| **Lab 8** | - Khởi tạo codebase nhóm, tài liệu `README.md`, `SPEC.md`, `ECONOMIC_RULES.md`, `PROJECT_PLAN.md`, `AI_JOURNAL.md`, `LAB_08_CHECKLIST.md`.<br>- Thống nhất ngưỡng 51%, bắt buộc `phaseId`, quy tắc phân định đóng góp hợp lệ và hoàn tiền đóng nhầm.<br>- Commit đúng cú pháp: `lab-08: khoi tao codebase nhom va dac ta v0.1`. | Trần Hoàng Phương Dung | Võ Ngọc Tâm Phúc | Ảnh chụp commit history trên GitHub có sự tham gia của các thành viên. |
| **Lab 9** | - Viết và biên dịch thành công Smart Contract lõi: `KTSAcademicRegistry.sol` và `KTSClassFund.sol`.<br>- Áp dụng OpenZeppelin 5.x (`Ownable2Step`, `ReentrancyGuard`, `EnumerableSet`).<br>- Thiết kế cấu trúc dữ liệu và logic xử lý hoàn tiền đóng nhầm theo phương án nhóm thống nhất. | Trần Hoàng Phương Dung | Võ Ngọc Tâm Phúc | Bằng chứng biên dịch không lỗi (Remix/Hardhat compile log). |
| **Lab 10** | - Rà soát lỗ hổng, audit mã nguồn nội bộ (kiểm tra CEI, reentrancy, overflow, access control, bảo mật cơ chế hoàn tiền đóng nhầm chống hoàn trùng).<br>- Sửa lỗi và lưu lại bảng đối chiếu trước/sau audit. | Võ Ngọc Tâm Phúc | Trần Hoàng Phương Dung | Báo cáo audit nội bộ và diff sửa lỗi có bằng chứng. |
| **Lab 11** | - Xây dựng bộ kiểm thử tự động xác minh toàn bộ 19 quy tắc kinh tế (`ER-01` đến `ER-19`, bao gồm luồng hoàn tiền đóng nhầm).<br>- Chạy kiểm thử luồng đúng, sai quyền và ranh giới thời gian. | Võ Ngọc Tâm Phúc | Trần Hoàng Phương Dung | Kết quả chạy test pass 100% kèm log kiểm thử. |
| **Lab 12** | - Tham gia đánh giá Gate Review 1 với Giảng viên.<br>- Báo cáo tiến độ, giải trình quy tắc kinh tế và kiến trúc hệ thống. | Trần Hoàng Phương Dung | Võ Ngọc Tâm Phúc | Biên bản đánh giá Gate Review 1 đạt yêu cầu. |
| **Lab 13** | - Viết và thực thi các ca kiểm thử bảo mật tấn công/gian lận: biểu quyết trùng, rút trộm tiền, giải ngân quá hạn, hủy đề xuất GV đình chỉ. | Võ Ngọc Tâm Phúc | Trần Hoàng Phương Dung | Bằng chứng test ca gian lận thất bại đúng như kỳ vọng (revert). |
| **Lab 14** | - Thực hiện audit chéo với nhóm bạn trong lớp theo phân công của Giảng viên.<br>- Lập báo cáo phát hiện lỗi và tiếp nhận phản hồi audit từ nhóm bạn. | Trần Hoàng Phương Dung | Võ Ngọc Tâm Phúc | Báo cáo audit chéo có xác nhận giữa hai nhóm. |
| **Lab 15** | - Hoàn thiện giao diện Web DApp kết nối MetaMask và Contract Sepolia.<br>- Đưa DApp lên hosting công khai (Vercel/GitHub Pages), hoàn thiện video demo và báo cáo tổng kết. | Võ Ngọc Tâm Phúc | Trần Hoàng Phương Dung | URL DApp công khai hoạt động ổn định + Video demo. |

---

## 4. Các nội dung cần nhóm tiếp tục xác nhận

1. Biểu quyết chính thức về phương án phân công vai trò chi tiết giữa hai thành viên (`[CẦN NHÓM XÁC NHẬN]`).
2. Thống nhất phương án kỹ thuật triển khai chức năng hoàn tiền do đóng nhầm: Lựa chọn giữa Phương án A (chức năng hoàn tiền chuyên biệt với sự đồng thuận của Cố vấn và Ban cán sự) hay Phương án B (quy trình tạo và biểu quyết đề xuất hoàn tiền riêng biệt do Ban cán sự duyệt 51%), cùng cơ chế lưu trữ bằng chứng on-chain (`[CẦN NHÓM XÁC NHẬN]`).
3. Xác nhận quy định chọn đợt học khi tạo đề xuất của Cố vấn và Ban cán sự: bắt buộc phải chọn đợt đang `OPEN` hay được phép chọn bất kỳ đợt nào trong năm học (`[CẦN NHÓM XÁC NHẬN]`).
