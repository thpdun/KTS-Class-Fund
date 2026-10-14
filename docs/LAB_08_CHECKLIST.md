# CHECKLIST NGHIỆM THU LAB 8 — KTS CLASS FUND

> **Học phần:** ECO2432 — Tiền điện tử và Hợp đồng thông minh  
> **Repository:** `https://github.com/thpdun/KTS-Class-Fund`  
> **Nhiệm vụ:** Khởi tạo codebase nhóm và đặc tả v0.1  
> **Nguyên tắc đánh dấu:**  
> - `[x]`: Đã hoàn thành và có căn cứ kiểm tra thực tế trong repository.  
> - `[ ]`: Chưa thực hiện hoặc cần thao tác thủ công từ phía thành viên nhóm.  
> - `[CẦN NHÓM XÁC NHẬN]`: Phụ thuộc vào quyết định hoặc thông tin nội bộ của nhóm chưa chốt chính thức.

---

## 1. Năm file tài liệu bắt buộc của Lab 8

- [x] **`README.md`**: Đã cập nhật mô tả một câu về sản phẩm, mục tiêu phiên bản đầu, quy trình hoạt động theo ngưỡng 51%, bắt buộc `phaseId`, chính sách phân định đóng góp hợp lệ và xem xét hoàn tiền khi đóng nhầm, và liên kết đầy đủ tới các tài liệu trong thư mục `docs/`.
- [x] **`docs/PROJECT_PLAN.md`**: Đã cập nhật thông tin thành viên (Trần Hoàng Phương Dung, Võ Ngọc Tâm Phúc), phân công vai trò, kế hoạch các mốc từ Lab 8 đến Lab 15 (bao gồm hạng mục thiết kế/audit/test cơ chế hoàn tiền ở Lab 9–11), sản phẩm bàn giao, trách nhiệm và đánh dấu rõ nội dung cần nhóm biểu quyết.
- [x] **`docs/SPEC.md`**: Đã đồng bộ đặc tả kỹ thuật: bắt buộc `phaseId` cho mọi vai trò tạo đề xuất, áp dụng công thức ngưỡng 51%, quy tắc nạp tiền và cơ chế 10 nguyên tắc xử lý hoàn tiền đóng nhầm (R1), quy trình kiểm tra quyền (R2) và ma trận 44 test cases.
- [x] **`docs/ECONOMIC_RULES.md`**: Đã hoàn thiện tài liệu quy tắc kinh tế với đúng 4 phần bắt buộc, 19 quy tắc chuẩn hóa (`ER-01` đến `ER-19`), phân định 3 trạng thái và 6 tình huống lạm dụng có phản hồi của nhóm.
- [x] **`docs/AI_JOURNAL.md`**: Đã tạo tài liệu nhật ký làm việc với AI ghi chép 3 phiên làm việc thực tế của Lab 8 (bao gồm phiên cập nhật chính sách hoàn tiền 8.3) và cung cấp mẫu bảng ghi nhật ký chuẩn cho các bài Lab tiếp theo.

---

## 2. Bốn phần chính bắt buộc của `ECONOMIC_RULES.md`

- [x] **Phần 1. Dòng tiền và quyền lợi:** Phân định rõ cơ chế nạp ETH; nguyên tắc cô lập số dư tuyệt đối theo lớp (`classId`); thẩm quyền đề xuất chi; thẩm quyền biểu quyết độc quyền của Ban cán sự; thẩm quyền giải ngân; giới hạn số dư quỹ; xử lý giao dịch `call` chuyển ETH thất bại (revert toàn bộ); tính chất phi lợi nhuận, không lãi suất, không chia cổ tức; phân định rạch ròi giữa đóng góp hợp lệ (không hoàn trả theo yêu cầu cá nhân) và đóng nhầm (được yêu cầu xem xét hoàn tiền theo 10 nguyên tắc).
- [x] **Phần 2. Giới hạn chống lạm dụng:** Phân định ranh giới quyền hạn; cơ chế chốt cử tri (Snapshot) kết hợp Kiểm tra kép (**Phương án A** - QA-04); ranh giới thời hạn biểu quyết 7 ngày; ranh giới thời hạn giải ngân 5 ngày sau đóng đợt học; cơ chế Chuyển trạng thái lười (**Lazy State Transition**) cho đề xuất `Failed` và `Expired`; tính bất biến của các trạng thái kết thúc; biện pháp kỹ thuật chống chi hai lần, bỏ phiếu hai lần, chống giả mạo `tx.origin` và chống tấn công tái nhập (`ReentrancyGuard` + CEI).
- [x] **Phần 3. Quyền quản trị:** Nguyên tắc đặc quyền tối thiểu (**Least Privilege**); phân định rõ 4 vai trò (**Quản trị viên hệ thống**, **Giảng viên**, **Cố vấn học tập**, **Ban cán sự**); quy trình chuyển quyền 2 bước `Ownable2Step`; cấm Quản trị viên rút quỹ hoặc biểu quyết; quy tắc ưu tiên phân xử người dùng mang nhiều vai trò (**Multi-Role Resolution**: Advisor > Class Officer > Lecturer); phân biệt dữ liệu trạng thái hiện hành trong storage và chuỗi lịch sử qua sự kiện.
- [x] **Phần 4. Các tình huống người dùng bị thiệt hại:** Phân tích chi tiết 6 tình huống lạm dụng, sai sót và rủi ro thực tế với đầy đủ 6 tiêu mục bắt buộc.

---

## 3. Ít nhất 4 quy tắc kinh tế có thể kiểm chứng được

Đã xây dựng và chuẩn hóa **19 quy tắc kinh tế** (vượt xa yêu cầu tối thiểu 4 quy tắc), mỗi quy tắc đều có điều kiện áp dụng, hành vi được phép, hành vi bị từ chối, kịch bản kiểm thử dự kiến và trạng thái thực tế:
- [x] `ER-01`: Cô lập tài chính và nạp tiền theo lớp học (`classId`).
- [x] `ER-02`: Thẩm quyền và bắt buộc `phaseId` khi tạo đề xuất chi.
- [x] `ER-03`: Ràng buộc nhân sự Ban cán sự tối thiểu khi tạo đề xuất (`eligibleVoterCount >= 1`).
- [x] `ER-04`: Chốt danh sách cử tri (Snapshot) và ngưỡng phê duyệt 51%.
- [x] `ER-05`: Kiểm tra kép tư cách cử tri khi biểu quyết (Dual Check - QA-04 Phương án A).
- [x] `ER-06`: Cơ chế biểu quyết một chiều và chống bỏ phiếu trùng lặp.
- [x] `ER-07`: Quyền tự biểu quyết của người tạo đề xuất là Ban cán sự.
- [x] `ER-08`: Thời hạn biểu quyết 7 ngày và chuyển trạng thái `Failed`.
- [x] `ER-09`: Độc quyền giải ngân của Ban cán sự và kiểm tra số dư quỹ.
- [x] `ER-10`: Thời hạn giải ngân 5 ngày sau khi đợt học đóng.
- [x] `ER-11`: Chuyển trạng thái lười sang `Expired` cho đề xuất quá hạn giải ngân.
- [x] `ER-12`: Nguyên tắc đơn nhất giải ngân (Chống chi trùng lặp).
- [x] `ER-13`: An toàn chuyển tiền ETH và xử lý khi giao dịch thất bại.
- [x] `ER-14`: Cơ chế hủy khẩn cấp đề xuất của Giảng viên bị đình chỉ.
- [x] `ER-15`: Phân quyền quản trị tối thiểu và chuyển giao quyền 2 bước (`Ownable2Step`).
- [x] `ER-16`: Quản lý danh mục Giảng viên và phân công theo lô.
- [x] `ER-17`: Phân quyền quản trị nhân sự Ban cán sự của Cố vấn lớp.
- [x] `ER-18`: Quản lý khoản đóng góp hợp lệ (Không rút vốn/hoàn trả cá nhân).
- [x] `ER-19`: Quy trình thẩm định và xem xét hoàn tiền khi đóng nhầm.

---

## 4. Ít nhất 5 tình huống lạm dụng và phản hồi của nhóm

Đã phân tích chi tiết **6 tình huống lạm dụng thực tế** trong Phần 4 của `docs/ECONOMIC_RULES.md`, mỗi tình huống đều có đủ 6 tiêu mục tiêu chuẩn:
- [x] **Tình huống 1:** Người ngoài hoặc Quản trị viên lạm quyền cố tình rút trộm tiền trực tiếp từ quỹ lớp. (Có phản hồi của nhóm: cấm mọi hàm rút tiền trực tiếp trong contract).
- [x] **Tình huống 2:** Cán sự lớp bị bãi nhiệm vẫn cố tình biểu quyết đề xuất chi bất minh. (Có phản hồi của nhóm: áp dụng kiểm tra kép Dual Check).
- [x] **Tình huống 3:** Đề xuất đã quá hạn giải ngân sau khi đợt học đóng nhưng vẫn bị rút tiền quỹ. (Có phản hồi của nhóm: bắt buộc `phaseId` và cơ chế Lazy State sang `Expired`).
- [x] **Tình huống 4:** Giảng viên bị kỷ luật đình chỉ nhưng đề xuất chi cũ vẫn bị giải ngân. (Có phản hồi của nhóm: bổ sung hàm `cancelSuspendedLecturerProposal`).
- [x] **Tình huống 5:** Tấn công tái nhập (Reentrancy) hoặc Hợp đồng người nhận từ chối ETH làm treo quỹ. (Có phản hồi của nhóm: áp dụng CEI, `nonReentrant` và atomic revert).
- [x] **Tình huống 6:** Lớp chưa có Ban cán sự nhưng vẫn tạo được đề xuất dẫn đến kẹt tiền hoặc duyệt sai. (Có phản hồi của nhóm: chặn `createProposal` khi cán sự = 0).

---

## 5. Sự nhất quán giữa README, SPEC và ECONOMIC_RULES

- [x] **Ngưỡng thông qua đề xuất:** Thống nhất tuyệt đối áp dụng tỷ lệ đa số 51% làm tròn trần $\text{requiredVotes} = \max(1, \lceil 0.51 \times N \rceil)$ trên cả `README.md`, `SPEC.md` và `ECONOMIC_RULES.md` (đã xóa bỏ hoàn toàn ngưỡng 4/5).
- [x] **Bắt buộc `phaseId` cho mọi đề xuất:** Đã cập nhật trên cả `README.md`, `SPEC.md` và `ECONOMIC_RULES.md`: mọi đề xuất (Lecturer, Advisor, Class Officer) đều phải có `phaseId` hợp lệ.
- [x] **Chính sách dòng tiền và hoàn tiền đóng nhầm:** Thống nhất trên cả 3 tài liệu (`README.md`, `SPEC.md`, `ECONOMIC_RULES.md`): các khoản đóng góp hợp lệ không hoàn trả theo yêu cầu cá nhân; các giao dịch đóng nhầm được phép gửi yêu cầu xem xét hoàn tiền theo 10 nguyên tắc thẩm định độc lập, có chốt chặn chống hoàn tiền trùng và bảo toàn quỹ lớp.
- [x] **Ranh giới thời gian:** Nhất quán thời hạn biểu quyết 7 ngày và thời hạn giải ngân 5 ngày sau khi đợt học đóng.
- [x] **Trạng thái đề xuất:** Nhất quán vòng đời 6 trạng thái (`Pending`, `Ready`, `Executed`, `Failed`, `Cancelled`, `Expired`).

---

## 6. Repository GitHub của nhóm

- [x] **Đường dẫn repository:** `https://github.com/thpdun/KTS-Class-Fund` (đã được cấu hình remote origin).
- [x] **Cấu trúc thư mục:** Đúng chuẩn quy định của học phần ECO2432 (chứa `AGENTS.md`, `README.md`, thư mục `docs/`, `contracts/`, `web/`, `docs/evidence/`).

---

## 7. Các hạng mục thủ công nhóm phải tự thực hiện (Manual Actions Required)

Do tuân thủ nguyên tắc an toàn (không tự ý chạy lệnh commit/push mà không có sự kiểm tra của người dùng), các bước sau đây cần do thành viên nhóm tự thực hiện:

- [ ] **Kiểm tra diff thay đổi:** Dùng lệnh `git diff` hoặc giao diện Git của Antigravity/VS Code để kiểm tra lại toàn bộ các thay đổi trên các file tài liệu.
- [ ] **Thực hiện lệnh commit với thông điệp chính xác:**
  ```bash
  git add README.md docs/ AI_JOURNAL.md
  git commit -m "lab-08: khoi tao codebase nhom va dac ta v0.1"
  ```
- [ ] **Thực hiện đẩy mã nguồn lên GitHub:**
  ```bash
  git push origin main
  ```
- [ ] **Bảo đảm sự tham gia của các thành viên trên Git:**
  - Thành viên thứ hai (Võ Ngọc Tâm Phúc) thực hiện clone/pull, tạo một commit chỉnh sửa nhỏ (ví dụ cập nhật xác nhận vai trò trong `docs/PROJECT_PLAN.md`) và push lên để lịch sử Git ghi nhận sự đóng góp của cả hai thành viên.
- [ ] **Chụp ảnh màn hình minh chứng:**
  - Truy cập giao diện web của repository: `https://github.com/thpdun/KTS-Class-Fund/commits/main`.
  - Chụp ảnh màn hình danh sách commit thể hiện commit `lab-08: khoi tao codebase nhom va dac ta v0.1` và sự tham gia của cả 2 thành viên.
  - Lưu file ảnh vào: `docs/evidence/lab-08/commit_history.png`.
- [ ] **Thực hiện commit và push ảnh minh chứng:**
  ```bash
  git add docs/evidence/lab-08/commit_history.png
  git commit -m "docs: bo sung anh minh chung commit history lab-08"
  git push origin main
  ```

---

## 8. Các vấn đề nội bộ cần nhóm họp và xác nhận chính thức

- [CẦN NHÓM XÁC NHẬN] Biểu quyết chính thức về phương án phân công vai trò tại `docs/PROJECT_PLAN.md` (Trần Hoàng Phương Dung: Đặc tả/Contract Lab 8–11; Võ Ngọc Tâm Phúc: Test/Quy tắc kinh tế Lab 8–11).
- [CẦN NHÓM XÁC NHẬN] Quy định về việc chọn đợt học khi tạo đề xuất của Cố vấn và Ban cán sự: có bắt buộc đợt học phải đang ở trạng thái `OPEN` tại thời điểm tạo hay không.
- [CẦN NHÓM XÁC NHẬN] Thống nhất phương án kỹ thuật triển khai hoàn tiền đóng nhầm: Phương án A (Module hoàn tiền chuyên biệt có xác thực đa chữ ký Cố vấn + Cán sự) hay Phương án B (Quy trình đề xuất hoàn tiền riêng biệt do Ban cán sự duyệt 51%).
- [CẦN NHÓM XÁC NHẬN] Thống nhất duy trì Phương án A cho phép cán sự phục chức được bỏ phiếu cho đề xuất cũ nếu thỏa mãn cả hai điều kiện tại thời điểm vote.
