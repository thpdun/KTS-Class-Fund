# NHẬT KÝ SỬ DỤNG AI — KTS CLASS FUND

> **Dự án:** KTS Class Fund — Hệ thống Quản lý Quỹ lớp Kinh tế Số trên Blockchain  
> **Học phần:** ECO2432 — Tiền điện tử và Hợp đồng thông minh  
> **Repository:** `https://github.com/thpdun/KTS-Class-Fund`  
> **Quy ước:** Tuân thủ quy tắc sử dụng AI theo [AGENTS.md](file:///d:/document/TDT/KTS-Class-Fund/AGENTS.md) (không bịa kết quả kiểm thử, đối soát kỹ lưỡng mọi đầu ra của AI trước khi đưa vào codebase).

---

## 1. Nhật ký các phiên làm việc trong Lab 8

### Phiên làm việc 8.1: Xây dựng dự thảo Quy tắc kinh tế và Vận hành hệ thống
- **Thời điểm thực hiện:** Giai đoạn chuẩn bị Lab 8 (Tháng 10/2026).
- **Mục tiêu:** Tạo mới tài liệu `docs/ECONOMIC_RULES.md` mô tả toàn diện các quy tắc tài chính, dòng tiền và kiểm soát rủi ro cho KTS Class Fund theo yêu cầu của Giảng viên.
- **Prompt sử dụng (Tóm tắt nội dung chính):**  
  > *Yêu cầu đọc kỹ các file hiện có (`AGENTS.md`, `README.md`, `PROJECT_PLAN.md`, `SPEC.md`, `ARCHITECTURE.md`), tạo mới file `docs/ECONOMIC_RULES.md` bằng tiếng Việt với đủ 4 phần bắt buộc: (1) Dòng tiền và quyền lợi; (2) Giới hạn chống lạm dụng; (3) Quyền quản trị; (4) Các tình huống người dùng bị thiệt hại. Xây dựng mã định danh `ER-xx`, phân biệt 3 trạng thái và đối chiếu các mâu thuẫn trong tài liệu.*
- **Tóm tắt đầu ra của AI:**
  - Sinh file `docs/ECONOMIC_RULES.md` gồm 18 quy tắc kinh tế (`ER-01` đến `ER-18`).
  - Phân tích 6 tình huống lạm dụng và thiệt hại thực tế.
  - Phát hiện mâu thuẫn quan trọng giữa `README.md` (ngưỡng xác nhận 4/5, tức 80%) và `SPEC.md` / `ARCHITECTURE.md` (ngưỡng đa số 51% làm tròn trần).
  - Nêu rõ vấn đề đề xuất của Advisor/Cán sự chưa bắt buộc truyền `phaseId`.
- **Lỗi, thiếu sót và rủi ro phát hiện trong đầu ra AI:**
  - Đầu ra ban đầu vẫn để ngỏ mâu thuẫn ngưỡng 4/5 vs 51% mà chưa có kết luận dứt khoát của nhóm.
  - Trong các tình huống thiệt hại ở Phần 4, mới chỉ có phân tích rủi ro và cách kiểm chứng, chưa có trường thông tin tường minh về "Phản hồi của nhóm (Group Response)".
  - Chưa phân định thật rạch ròi giữa việc "cấm hoàn trả tiền đóng góp quỹ" với việc "chi tiền hợp lệ từ quỹ theo đề xuất".
- **Cách nhóm kiểm tra và xử lý:**
  - Nhóm tiến hành thảo luận nội bộ: chính thức chốt chọn phương án ngưỡng 51% động theo `SPEC.md` và loại bỏ hoàn toàn ngưỡng 4/5.
  - Thống nhất nguyên tắc: mọi đề xuất bắt buộc phải gắn với `phaseId` hợp lệ, không có ngoại lệ.
  - Nhóm kiểm tra lại từng điều kiện nghiệp vụ để chuẩn bị cho prompt điều chỉnh tiếp theo.
- **Người thực hiện kiểm tra:** `[CẦN NHÓM XÁC NHẬN - Điền tên: Trần Hoàng Phương Dung / Võ Ngọc Tâm Phúc]`.

---

### Phiên làm việc 8.2: Hoàn thiện hồ sơ bàn giao Lab 8 và đồng bộ hóa tài liệu
- **Thời điểm thực hiện:** Giai đoạn nghiệm thu Lab 8.
- **Mục tiêu:** Rà soát toàn bộ repository, giải quyết triệt để các mâu thuẫn theo quyết định đã chốt, hoàn thiện `README.md`, `docs/PROJECT_PLAN.md`, `docs/SPEC.md`, `docs/ECONOMIC_RULES.md`, lập `docs/LAB_08_CHECKLIST.md`.
- **Prompt sử dụng (Tóm tắt nội dung chính):**  
  > *Rà soát và hoàn thiện Lab 8 theo tài liệu hướng dẫn môn học ECO2432. Áp dụng 3 quyết định kinh tế nhóm đã thống nhất: (1) Ngưỡng 51% `requiredVotes = max(1, ceil(0.51 × N))`; (2) Mọi đề xuất bắt buộc phải có `phaseId` hợp lệ; (3) Nguyên tắc không hoàn tiền đóng góp. Cập nhật `ECONOMIC_RULES.md`, kiểm tra `README.md`, `PROJECT_PLAN.md`, `SPEC.md`, `AI_JOURNAL.md`, lập `LAB_08_CHECKLIST.md`, không tự ý viết smart contract và không tự commit/push.*
- **Tóm tắt đầu ra của AI:**
  - Cập nhật `README.md`: sửa quy trình hoạt động sang ngưỡng 51%, bắt buộc `phaseId`, phân biệt `Failed` vs `Expired`, bổ sung danh mục tài liệu `docs/`.
  - Cập nhật `docs/PROJECT_PLAN.md`: bổ sung chi tiết phân công trách nhiệm Lab 8–15 và các điểm `[CẦN NHÓM XÁC NHẬN]`.
  - Cập nhật `docs/SPEC.md`: sửa R1 (nguyên tắc không hoàn tiền đóng góp), R2 (bắt buộc `phaseId` cho mọi vai trò).
  - Cập nhật `docs/ECONOMIC_RULES.md`: bổ sung đầy đủ hành vi được phép / hành vi bị từ chối cho 18 quy tắc, bổ sung trường phản hồi của nhóm cho cả 6 tình huống lạm dụng.
  - Lập bảng kiểm tra chi tiết `docs/LAB_08_CHECKLIST.md` với ba trạng thái `[x]`, `[ ]`, và `[CẦN NHÓM XÁC NHẬN]`.
- **Lỗi, thiếu sót và rủi ro phát hiện trong đầu ra AI:**
  - Cần đảm bảo các câu hỏi chưa ngã ngũ (như việc Advisor có bắt buộc phải chọn đợt học đang `OPEN` hay không, xử lý nạp nhầm thế nào) phải được giữ nguyên ở dạng `[CẦN NHÓM XÁC NHẬN]`, không để AI tự quyết định thay nhóm.
  - Phải tách bạch giữa những việc AI đã làm trong file tài liệu với những việc thủ công sinh viên phải tự làm trên Git (commit, push, chụp ảnh bằng chứng).
- **Cách nhóm kiểm tra và xử lý:**
  - Đọc lại toàn bộ diff của các file bằng công cụ kiểm tra của IDE.
  - Xác nhận không có mã smart contract nào bị sinh sớm trong Lab 8.
  - Chuẩn bị thực hiện commit thủ công trên Git theo cú pháp quy định.
- **Người thực hiện kiểm tra:** `[CẦN NHÓM XÁC NHẬN - Điền tên: Trần Hoàng Phương Dung / Võ Ngọc Tâm Phúc]`.

---

### Phiên làm việc 8.3: Cập nhật đặc tả và quy tắc kinh tế cho cơ chế hoàn tiền khi đóng nhầm
- **Thời điểm thực hiện:** Giai đoạn hoàn thiện Lab 8 (Tháng 10/2026).
- **Mục tiêu:** Rà soát và cập nhật tài liệu dự án theo quyết định mới của nhóm: cho phép người dùng đóng nhầm (chọn sai lớp, nhập sai số tiền, lỗi giao dịch) gửi yêu cầu xem xét hoàn tiền; duy trì nguyên tắc không hoàn trả khoản đóng góp hợp lệ; đặc tả 10 nguyên tắc xử lý hoàn tiền và bổ sung quy tắc kinh tế `ER-19`.
- **Prompt sử dụng (Tóm tắt nội dung chính):**  
  > *Rà soát và chỉnh sửa tài liệu theo quyết định mới của nhóm: cho phép hoàn tiền khi người dùng đóng nhầm vào quỹ lớp. Phân biệt rõ hai trường hợp: (1) Đóng góp hợp lệ: không hoàn trả theo yêu cầu cá nhân thông thường; (2) Đóng nhầm: được phép yêu cầu xem xét hoàn tiền theo 10 nguyên tắc thẩm định. Xác định rõ quyền hạn và kiểm soát, không tự cấp quyền cho Admin rút tiền, bổ sung quy tắc `ER-19` vào `ECONOMIC_RULES.md`, cập nhật `SPEC.md`, `README.md`, `PROJECT_PLAN.md`, `LAB_08_CHECKLIST.md`. Không tự ý viết smart contract và không tự commit/push.*
- **Tóm tắt đầu ra của AI:**
  - Cập nhật `docs/ECONOMIC_RULES.md`: bổ sung `ER-19` (Quy trình thẩm định và xem xét hoàn tiền khi đóng nhầm) vào ma trận và chi tiết quy tắc; cập nhật mục 1.7 với đầy đủ 10 nguyên tắc hoàn tiền và 2 phương án kỹ thuật đề xuất (`[CẦN NHÓM XÁC NHẬN]`); cập nhật phụ lục.
  - Cập nhật `docs/SPEC.md`: cập nhật quy tắc R1 phân định rõ đóng góp hợp lệ vs đóng nhầm, bổ sung quyết định vào mục 7.3.
  - Cập nhật `README.md`: sửa bước 1 mục 6 về chính sách dòng tiền mới.
  - Cập nhật `docs/PROJECT_PLAN.md`: bổ sung nhiệm vụ thiết kế và kiểm thử cơ chế hoàn tiền trong các mốc Lab 9–11; cập nhật câu hỏi thảo luận nội bộ.
  - Cập nhật `docs/LAB_08_CHECKLIST.md`: rà soát toàn bộ checklist theo 19 quy tắc kinh tế.
- **Lỗi, thiếu sót và rủi ro phát hiện trong đầu ra AI:**
  - AI có thể bị cám dỗ tự động cấp quyền rút tiền cho System Admin hoặc tự ý sinh hàm rút tiền `refund()` trong Smart Contract. Nhóm đã kịp thời kiểm soát, nghiêm cấm sinh code contract sớm ở Lab 8 và nhấn mạnh nguyên tắc Least Privilege (Admin không có quyền rút tiền).
  - Cần bảo đảm chống hoàn tiền trùng lặp (Replay/Double Refund Attack) bằng mapping trạng thái `isRefunded[txHash]` trên blockchain theo Checks-Effects-Interactions.
  - Nhắc nhở rõ ràng: cơ chế hoàn tiền mới ở mức **đặc tả yêu cầu nghiệp vụ**, chưa triển khai trong Smart Contract v0.1 và chưa được kiểm thử tự động.
- **Cách nhóm kiểm tra và xử lý:**
  - Kiểm tra diff từng file để bảo đảm không còn câu khẳng định "quỹ không bao giờ hoàn tiền trong mọi trường hợp" mà thay bằng sự phân định rạch ròi giữa đóng góp hợp lệ và đóng nhầm.
  - Giữ nguyên trạng thái `[CẦN NHÓM XÁC NHẬN]` cho việc lựa chọn giữa Phương án A (module chuyên biệt) và Phương án B (quy trình đề xuất riêng biệt).
- **Người thực hiện kiểm tra:** `[CẦN NHÓM XÁC NHẬN - Điền tên: Trần Hoàng Phương Dung / Võ Ngọc Tâm Phúc]`.

---

## 2. Mẫu bảng ghi nhật ký AI chuẩn cho các Lab tiếp theo (Lab 9 – Lab 15)

Mỗi khi sử dụng công cụ AI (Antigravity / Gemini / ChatGPT / Claude) trong các bài thực hành tiếp theo, thành viên nhóm copy mẫu dưới đây để ghi lại minh chứng trung thực:

```markdown
### Phiên làm việc [Tên Lab . Số thứ tự phiên] (Ví dụ: 9.1, 10.1, ...)
- **Ngày thực hiện:** YYYY-MM-DD
- **Mục tiêu:** (Mô tả mục tiêu cụ thể, ví dụ: viết contract lõi, viết test case, ...)
- **Prompt đã sử dụng:** 
  > (Dán nguyên văn câu lệnh prompt đã đưa cho AI)
- **Tóm tắt đầu ra của AI:**
  - (Tóm tắt ngắn gọn những gì AI trả về: cấu trúc code, đoạn test, phân tích, ...)
- **Đánh giá ban đầu:** [Dùng được ngay / Phải sửa chữa nhiều / Sai hoàn toàn, phải bỏ]
- **Lỗi, thiếu sót hoặc rủi ro phát hiện trong đầu ra AI:**
  - (Ghi rõ điểm sai logic, vi phạm AGENTS.md, thiếu kiểm tra quyền, thừa/thiếu modifier, ...)
- **Cách nhóm kiểm tra và sửa chữa:**
  - (Mô tả sinh viên đã chỉnh sửa mã nguồn hoặc prompt lại như thế nào, kèm bằng chứng kiểm tra)
- **Người thực hiện kiểm tra & đối soát:** (Họ tên thành viên nhóm thực hiện)
```
