# QUY TẮC KINH TẾ VÀ VẬN HÀNH HỆ THỐNG — KTS CLASS FUND

> **Dự án:** KTS Class Fund — Hệ thống Quản lý Quỹ lớp Kinh tế Số trên Blockchain  
> **Học phần:** ECO2432 — Tiền điện tử và Hợp đồng thông minh (Trường Đại học Kinh tế, Đại học Huế)  
> **Repository:** `https://github.com/thpdun/KTS-Class-Fund`  
> **Ngôn ngữ & Nền tảng:** Solidity `^0.8.20`, OpenZeppelin Contracts `5.x`, Ethereum Sepolia Testnet  
> **Tài liệu căn cứ:** [AGENTS.md](file:///d:/document/TDT/KTS-Class-Fund/AGENTS.md), [README.md](file:///d:/document/TDT/KTS-Class-Fund/README.md), [PROJECT_PLAN.md](file:///d:/document/TDT/KTS-Class-Fund/docs/PROJECT_PLAN.md), [SPEC.md](file:///d:/document/TDT/KTS-Class-Fund/docs/SPEC.md), [ARCHITECTURE.md](file:///d:/document/TDT/KTS-Class-Fund/docs/ARCHITECTURE.md)  
> **Trạng thái tài liệu:** Tài liệu hoàn chỉnh phục vụ nghiệm thu Lab 8 và triển khai Lab 9–11  

---

## LỜI NÓI ĐẦU VÀ NGUYÊN TẮC THẨM ĐỊNH

Tài liệu này xác định toàn bộ các **quy tắc kinh tế, giới hạn tài chính, phân định thẩm quyền và cơ chế kiểm soát rủi ro** của hệ thống KTS Class Fund. Mục tiêu cốt lõi là chuyển hóa các yêu cầu quản lý quỹ lớp thực tế thành các ràng buộc logic chặt chẽ, khách quan, có thể kiểm chứng toán học và lập trình trực tiếp trên Smart Contract.

Nhằm bảo đảm tính minh bạch và trung thực học thuật theo quy chuẩn của học phần ECO2432, tài liệu áp dụng **nguyên tắc phân định ba trạng thái** rõ ràng cho từng quy tắc:
1. **Trạng thái tài liệu:** Đã được quy định và chuẩn hóa trong văn bản đặc tả (`SPEC.md`, `ARCHITECTURE.md`, `ECONOMIC_RULES.md`).
2. **Trạng thái mã nguồn:** Chưa triển khai trong mã nguồn Smart Contract (chờ lập trình ở Lab 9).
3. **Trạng thái kiểm thử:** Chưa xác minh bằng bài kiểm thử thực tế (chờ chạy bộ test tự động ở Lab 11 & Lab 13).

---

## BẢNG DANH MỤC QUY TẮC KINH TẾ CHUẨN HÓA (ECONOMIC RULES MATRIX)

| Mã quy tắc | Tên quy tắc kinh tế | Vai trò áp dụng | Trạng thái tài liệu | Trạng thái mã nguồn | Trạng thái kiểm thử |
| :---: | :--- | :--- | :---: | :---: | :---: |
| **ER-01** | Cô lập tài chính và nạp tiền theo lớp (`classId`) | Bất kỳ ai | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-02** | Thẩm quyền và bắt buộc `phaseId` khi tạo đề xuất | Ban cán sự, Cố vấn, Giảng viên | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-03** | Ràng buộc nhân sự Ban cán sự tối thiểu khi tạo đề xuất | Mọi chủ thể tạo đề xuất | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-04** | Chốt danh sách cử tri (Snapshot) và ngưỡng 51% | Ban cán sự lớp | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-05** | Kiểm tra kép tư cách cử tri khi biểu quyết (Dual Check) | Ban cán sự lớp | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-06** | Cơ chế biểu quyết một chiều và chống bỏ phiếu trùng | Ban cán sự lớp | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-07** | Quyền tự biểu quyết của người tạo đề xuất là Ban cán sự | Ban cán sự lớp | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-08** | Thời hạn biểu quyết 7 ngày và chuyển trạng thái `Failed` | Mọi người dùng / Ban cán sự | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-09** | Độc quyền giải ngân của Ban cán sự và kiểm tra số dư quỹ | Ban cán sự đương nhiệm | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-10** | Thời hạn giải ngân 5 ngày sau khi đợt học đóng | Ban cán sự đương nhiệm | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-11** | Chuyển trạng thái lười sang `Expired` cho đề xuất quá hạn | Ban cán sự / Công khai | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-12** | Nguyên tắc đơn nhất giải ngân (Chống chi trùng lặp) | Ban cán sự đương nhiệm | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-13** | An toàn chuyển tiền ETH và xử lý khi giao dịch thất bại | Ban cán sự / Hợp đồng | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-14** | Cơ chế hủy khẩn cấp đề xuất của Giảng viên bị đình chỉ | Quản trị viên, Cố vấn lớp | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-15** | Phân quyền quản trị tối thiểu và chuyển giao quyền 2 bước | Quản trị viên hệ thống | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-16** | Quản lý danh mục Giảng viên và phân công theo lô | Quản trị viên hệ thống | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-17** | Phân quyền quản trị nhân sự Ban cán sự của Cố vấn lớp | Cố vấn học tập | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-18** | Quản lý khoản đóng góp hợp lệ (Không rút vốn/hoàn trả cá nhân) | Toàn hệ thống | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |
| **ER-19** | Quy trình thẩm định và xem xét hoàn tiền khi đóng nhầm | Người đóng tiền, Ban cán sự, Cố vấn | Đã đặc tả | Chưa triển khai | Chưa kiểm thử |

---

## PHẦN 1. DÒNG TIỀN VÀ QUYỀN LỢI

### 1.1. Cơ chế nạp tiền và nguyên tắc cô lập số dư theo từng lớp học
- **Cơ chế nạp quỹ:** Tiền được nạp vào quỹ lớp dưới dạng đồng tiền mã hóa Native Ether (ETH) trên mạng thử nghiệm Ethereum Sepolia Testnet thông qua hàm `depositToClass(uint256 classId) payable`. Bất kỳ ai (sinh viên, giảng viên, cựu sinh viên, nhà hảo tâm) đều có quyền nạp tiền vào quỹ lớp nếu sở hữu ví Ethereum kết nối hợp lệ.
- **Nguyên tắc cô lập tài chính (`classId` Isolation):** Hệ thống hỗ trợ quản lý độc lập nhiều lớp học trên cùng một hợp đồng thông minh. Toàn bộ dòng tiền nạp vào gắn với định danh `classId` nào sẽ được hạch toán riêng biệt vào biến trạng thái lưu trữ `mapping(uint256 => uint256) public classBalances`.
  - Tiền của lớp nào chỉ được sử dụng phục vụ cho các hoạt động của chính lớp đó.
  - Tuyệt đối không có cơ chế gộp quỹ, điều chuyển tiền liên lớp, hoặc dùng số dư của lớp A để bù đắp, bảo lãnh hay giải ngân cho yêu cầu chi tiêu của lớp B.

### 1.2. Quyền đề xuất sử dụng quỹ và quyền biểu quyết
- **Quyền đề xuất chi tiêu:** Quyền tạo đề xuất chi (`createProposal`) được phân định theo 3 căn cứ thẩm quyền và bắt buộc gắn với mã đợt học (`phaseId`) hợp lệ:
  1. *Cán sự lớp đương nhiệm (Class Officer):* Được tạo đề xuất phục vụ các hoạt động của lớp mình phụ trách.
  2. *Cố vấn học tập của lớp (Advisor):* Được tạo đề xuất cho lớp mình phụ trách.
  3. *Giảng viên có phân công giảng dạy (Lecturer):* Được tạo đề xuất phục vụ môn học được phân công, với điều kiện đợt học căn cứ đang mở (`OPEN`) và còn trong thời hạn (`block.timestamp <= endTime`).
  - *Sinh viên bình thường và Quản trị viên hệ thống (System Admin) tuyệt đối không có quyền tạo đề xuất chi tiêu.*
- **Quyền biểu quyết:** Thuộc quyền độc quyền của các thành viên Ban cán sự lớp (Class Officer). Giảng viên, Cố vấn học tập, Quản trị viên hệ thống và sinh viên không thuộc Ban cán sự tuyệt đối không có quyền biểu quyết duyệt chi.

### 1.3. Điều kiện để một đề xuất chi tiền được thông qua
Một đề xuất chi tiền chỉ được chuyển sang trạng thái sẵn sàng giải ngân (`Ready`) khi đáp ứng đầy đủ các điều kiện sau:
1. Đề xuất đang trong thời hạn biểu quyết 7 ngày (`block.timestamp <= votingDeadline`).
2. Số lượt biểu quyết chấp thuận hợp lệ tích lũy đạt hoặc vượt ngưỡng phê duyệt tối thiểu 51% cử tri Ban cán sự chốt lúc tạo đề xuất:
   $$\text{requiredVotes} = \max\left(1, \lceil 0.51 \times N \rceil\right)$$
   với $N$ là số cử tri Ban cán sự trong snapshot ($N \ge 1$).
3. Ngưỡng phê duyệt tối thiểu bắt buộc phải $\ge 1$ phiếu. Tuyệt đối không bao giờ có trường hợp đề xuất tự động thông qua mà không có phiếu tán thành.

### 1.4. Thẩm quyền thực hiện giao dịch chuyển tiền (Giải ngân)
- Sau khi đề xuất đã chuyển sang trạng thái `Ready`, duy nhất thành viên Ban cán sự (Class Officer) đương nhiệm của chính lớp đó mới có quyền kích hoạt giao dịch giải ngân (`executeProposal`).
- Giảng viên tạo đề xuất, Cố vấn học tập, Quản trị viên hệ thống hay địa chỉ người thụ hưởng (`recipient`) không thể tự ý kích hoạt lệnh chuyển tiền nếu không giữ vai trò Ban cán sự của lớp.

### 1.5. Giới hạn số dư quỹ khi thực hiện chi
- Số tiền giải ngân của một đề xuất bị giới hạn tuyệt đối bởi số dư khả dụng thực tế của lớp tại thời điểm gọi giao dịch:
   $$\text{classBalances}[classId] \ge \text{proposal.amount}$$
- Nếu tại thời điểm biểu quyết đề xuất đạt `Ready`, nhưng đến khi giải ngân số dư quỹ của lớp không đủ, giao dịch giải ngân lập tức bị từ chối (revert với lỗi `InsufficientBalance`). Tiền quỹ không bị trừ và đề xuất giữ nguyên trạng thái `Ready` chờ bổ sung quỹ.

### 1.6. Cơ chế xử lý khi giao dịch chuyển tiền thất bại
- Khi thực hiện giải ngân, hợp đồng thông minh chuyển ETH tới người nhận bằng lệnh gọi cấp thấp:
  ```solidity
  (bool success, ) = proposal.recipient.call{value: proposal.amount}("");
  ```
- **Hành vi bắt buộc khi thất bại (`!success`):**
  - Áp dụng nguyên tắc Checks-Effects-Interactions kết hợp kiểm soát hoàn nguyên toàn bộ (Atomic Revert): Nếu lệnh `call` trả về `false` (do địa chỉ người nhận là hợp đồng từ chối nhận ETH, hết gas nội bộ, hoặc cố tình revert), Smart Contract **bắt buộc phải revert toàn bộ giao dịch** với lỗi tùy biến `PaymentFailed()`.
  - **Hệ quả trạng thái:** Trạng thái đề xuất không được chuyển sang `Executed` mà được hoàn nguyên giữ nguyên là `Ready`; số dư quỹ của lớp không bị trừ; không có bất kỳ khoản phí ETH nào bị thất thoát khỏi quỹ lớp.
  - **Khả năng thực hiện lại:** Ban cán sự có quyền kích hoạt lại giao dịch giải ngân sau khi người thụ hưởng đã khắc phục sự cố ví/hợp đồng nhận tiền, miễn là đề xuất vẫn còn trong thời hạn giải ngân hợp lệ.

### 1.7. Quy định về quyền lợi tài chính, phân định đóng góp hợp lệ và xử lý hoàn tiền do đóng nhầm
- **Tính chất phi thương mại:** Quỹ lớp KTS Class Fund là quỹ nội bộ phục vụ đào tạo và sinh hoạt tập thể của sinh viên khoa Kinh tế Số.
- **Không sinh lời & Không lãi suất:** Hệ thống hoàn toàn không có cơ chế Staking, Yield Farming, cho vay lấy lãi, hay đầu tư sinh lời từ số dư quỹ.
- **Không chia cổ tức / Lợi tức:** Người nạp tiền không nhận được bất kỳ token vốn cổ phần, cổ tức hay quyền lợi tài chính phái sinh nào từ quỹ.
- **Phân định rõ ràng giữa hai trường hợp nạp tiền:**
  1. **Trường hợp 1 — Đóng góp hợp lệ:** Người dùng chủ động nạp tiền đúng mã lớp, đúng số tiền và đúng mục đích đóng góp cho quỹ lớp. Khoản đóng góp này trở thành tài sản chung của tập thể lớp và **không được hoàn trả theo yêu cầu rút tiền thông thường của cá nhân**. Tiền này chỉ được chi ra thông qua đề xuất chi hợp lệ được Ban cán sự biểu quyết thông qua.
  2. **Trường hợp 2 — Đóng nhầm (Mistaken Deposit):** Người dùng nạp tiền do nhầm lẫn (chọn sai mã lớp `classId`, nhập sai số tiền, hoặc lỗi giao dịch ngoại ý). Trường hợp này **được phép gửi yêu cầu xem xét hoàn tiền**, tuân thủ nghiêm ngặt 10 nguyên tắc thẩm định và an toàn tài chính.
- **10 nguyên tắc xử lý yêu cầu hoàn tiền do đóng nhầm:**
  1. *Cung cấp thông tin đối soát:* Người yêu cầu phải cung cấp hash giao dịch nạp gốc (`txHash`), mã lớp nạp nhầm (`classId`), số tiền thực nạp, địa chỉ ví người nạp và lý do chi tiết.
  2. *Thẩm định độc lập:* Yêu cầu phải được kiểm tra đối soát on-chain (kiểm tra sự kiện `DepositReceived` thực tế trên blockchain) và off-chain (xác minh người nạp không có nghĩa vụ đóng khoản tiền đó cho lớp).
  3. *Tuyệt đối không tự động hoàn tiền:* Hệ thống không tự động chuyển trả ETH chỉ vì nhận được yêu cầu của người dùng, ngăn ngừa hành vi gửi yêu cầu khống.
  4. *Xác định rõ thẩm quyền:* Phải xác định minh bạch người có thẩm quyền tiếp nhận, thẩm định, phê duyệt và thực hiện hoàn tiền.
  5. *Quản trị viên không được lạm quyền:* Quản trị viên hệ thống (System Admin) tuyệt đối không có quyền tự ý rút tiền quỹ hoặc bỏ qua quy trình kiểm soát với lý do "hoàn tiền thủ công".
  6. *Có căn cứ xác nhận:* Chỉ được hoàn tiền khi có căn cứ rõ ràng chứng minh giao dịch nạp là do nhầm lẫn và số tiền hoàn trả hợp lệ đã được phê duyệt.
  7. *Giới hạn số tiền hoàn trả:* Số tiền hoàn trả không được vượt quá số tiền thực tế đã nạp nhầm trong giao dịch gốc: $\text{refundAmount} \le \text{originalDepositAmount}$.
  8. *Lưu trữ lịch sử và kiểm tra audit:* Hệ thống phải lưu trữ đầy đủ thông tin giao dịch gốc, số tiền, lý do, người duyệt và kết quả xử lý thông qua các sự kiện on-chain (`RefundRequested`, `RefundProcessed`).
  9. *Chống hoàn tiền trùng lặp (Anti-Double Refund):* Một giao dịch nạp nhầm chỉ được hoàn trả duy nhất một lần. Hệ thống phải lưu vết trạng thái `isRefunded[txHash] = true` để từ chối mọi yêu cầu tiếp theo.
  10. *Bảo toàn tài sản hợp lệ của lớp:* Tiền hoàn trả chỉ được trích từ số dư khả dụng của đúng mã lớp đã nhận tiền nhầm; việc hoàn tiền tuyệt đối không làm ảnh hưởng đến các khoản đóng góp hợp lệ khác và không được gây thâm hụt số dư khả dụng của lớp.
- **Quyền hạn và Kiểm soát trong quy trình hoàn tiền:**
  - *Người gửi yêu cầu:* Chính địa chỉ ví đã thực hiện giao dịch nạp nhầm ban đầu (`msg.sender == originalSender`).
  - *Người kiểm tra thông tin:* Ban cán sự lớp phối hợp với Cố vấn học tập của lớp đối soát danh sách sinh viên và lịch sử nạp.
  - *Người phê duyệt / Từ chối:* `[CẦN NHÓM XÁC NHẬN]` — Nhóm đang xem xét hai phương án kỹ thuật:
    - *Phương án A (Chức năng hoàn tiền chuyên biệt - Dedicated Refund Module):* Xây dựng hàm hoàn tiền riêng biệt với cơ chế xác thực đa chữ ký hoặc có sự đồng thuận của Cố vấn học tập và đại diện Ban cán sự.
    - *Phương án B (Quy trình đề xuất hoàn tiền riêng biệt - Dedicated Refund Proposal):* Ban cán sự hoặc người nạp tạo một đề xuất hoàn tiền đặc thù (`RefundProposal`), được đưa vào biểu quyết tập thể của Ban cán sự với ngưỡng phê duyệt 51% trong hạn 7 ngày.
  - *Người thực hiện chuyển ETH:* Ban cán sự đương nhiệm kích hoạt lệnh giải ngân hoàn tiền an toàn, hoặc hợp đồng tự động giải ngân sau khi đề xuất hoàn tiền đạt trạng thái phê duyệt hợp lệ.
  - *Kiểm tra lịch sử xử lý:* Mọi người dùng đều có thể tra cứu lịch sử qua các sự kiện on-chain.
- **Lưu ý quan trọng về trạng thái mã nguồn:** Cơ chế hoàn tiền hiện tại là **yêu cầu nghiệp vụ đã được nhóm xác nhận đặc tả**, nhưng **chưa được lập trình trong Smart Contract v0.1**. Chức năng này sẽ được thiết kế kiến trúc chi tiết, lập trình ở Lab 9/10 và kiểm thử tự động ở Lab 11/13.

---

### BẢNG CHI TIẾT CÁC QUY TẮC KINH TẾ PHẦN 1 (ER-01 ĐẾN ER-04)

#### Quy tắc ER-01: Cô lập tài chính và nạp tiền theo lớp học
- **Nội dung quy tắc:** Hệ thống quản lý số dư độc lập cho từng mã lớp (`classId`). Mọi giao dịch nạp tiền phải chỉ định rõ `classId` hợp lệ và gửi kèm số lượng ETH lớn hơn 0. Tiền của lớp nào chỉ phục vụ chi tiêu cho lớp đó, không được hòa trộn hay bù trừ liên lớp.
- **Điều kiện áp dụng:** Mã lớp `classId` đã được Quản trị viên hệ thống khởi tạo hợp lệ (`classExists[classId] == true`) và giá trị nạp `msg.value > 0`.
- **Hành vi được phép:** Bất kỳ ai gửi giao dịch nạp ETH với `msg.value > 0` vào mã lớp `classId` hợp lệ.
- **Hành vi bị từ chối:** Nạp tiền với `msg.value == 0` (revert `InvalidAmount()`); nạp tiền vào `classId` chưa khởi tạo (revert `InvalidClassId()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Người dùng A nạp 0.5 ETH vào Lớp 1 -> Số dư Lớp 1 tăng 0.5 ETH, số dư Lớp 2 không đổi; phát sự kiện `DepositReceived(1, A, 0.5 ETH, timestamp)`.
  - *Kiểm thử vi phạm:* Nạp tiền với `msg.value == 0` -> Giao dịch bị revert với lỗi `InvalidAmount()`. Nạp tiền vào `classId = 999` chưa khởi tạo -> Giao dịch bị revert với lỗi `InvalidClassId()`.
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-02: Thẩm quyền và bắt buộc `phaseId` khi tạo đề xuất chi
- **Nội dung quy tắc:** Đề xuất chi quỹ lớp chỉ được khởi tạo bởi: (1) Cán sự đương nhiệm của lớp; (2) Cố vấn học tập của lớp; hoặc (3) Giảng viên được phân công giảng dạy lớp đó trong một đợt học đang mở (`OPEN`) và còn thời hạn hiệu lực (`block.timestamp <= endTime`). **Mọi đề xuất chi, bất kể người tạo là ai, đều bắt buộc phải gắn với một mã đợt học (`phaseId`) hợp lệ** thuộc đúng lớp.
- **Điều kiện áp dụng:** `classExists[classId] == true`, người gọi thỏa mãn 1 trong 3 vai trò trên, `phaseId` hợp lệ thuộc lớp, lớp có $\ge 1$ cán sự đủ điều kiện, `amount > 0`, `recipient != address(0)`, `recipient != address(this)`, `bytes(description).length > 0`.
- **Hành vi được phép:** Người có thẩm quyền tạo đề xuất chi kèm `phaseId` hợp lệ, số tiền hợp lệ và mục đích rõ ràng.
- **Hành vi bị từ chối:** Sinh viên bình thường hoặc Quản trị viên tạo đề xuất (revert `Unauthorized()`); để trống `phaseId` (revert `EmptyPhaseId()`); dùng `phaseId` không thuộc lớp hoặc không tồn tại (revert `InvalidPhase()`); Giảng viên dùng `phaseId` của đợt học đã `CLOSED` hoặc quá `endTime` (revert `PhaseNotEnded()` / `Unauthorized()`); địa chỉ người nhận rỗng (revert `InvalidAddress()`); số tiền $\le 0$ (revert `InvalidAmount()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Giảng viên dạy đợt `2026-S1-P1` đang `OPEN` tạo đề xuất chi 0.1 ETH kèm `phaseId = 2026-S1-P1` -> Thành công; Cố vấn hoặc Cán sự tạo đề xuất kèm `phaseId = 2026-S1-P1` -> Thành công.
  - *Kiểm thử vi phạm:* Tạo đề xuất nhưng để chuỗi `phaseId` rỗng -> Bị revert `EmptyPhaseId()`. Giảng viên tạo đề xuất gắn với đợt học đã `CLOSED` -> Bị revert.
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-03: Ràng buộc nhân sự Ban cán sự tối thiểu khi tạo đề xuất
- **Nội dung quy tắc:** Bắt buộc lớp phải có ít nhất 1 thành viên Ban cán sự đủ điều kiện biểu quyết (`eligibleVoterCount >= 1`) tại thời điểm tạo đề xuất. Tuyệt đối không cho phép tạo đề xuất khi lớp chưa có cán sự nào (`eligibleVoterCount == 0`).
- **Điều kiện áp dụng:** Áp dụng bình đẳng cho mọi chủ thể tạo đề xuất (Ban cán sự, Cố vấn học tập, Giảng viên).
- **Hành vi được phép:** Khởi tạo đề xuất chi khi lớp đã được bổ nhiệm ít nhất một Cán sự lớp hợp lệ (`_classOfficers[classId].length() >= 1`).
- **Hành vi bị từ chối:** Tạo đề xuất khi `_classOfficers[classId].length() == 0` (revert `NoEligibleVoters()`), ngăn chặn đề xuất rỗng cử tri.
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Cố vấn học tập bổ sung 1 cán sự vào lớp, sau đó tạo đề xuất chi -> Tạo thành công với `eligibleVoterCount = 1`, `requiredVotes = 1`.
  - *Kiểm thử vi phạm:* Lớp vừa khởi tạo chưa có cán sự nào, Cố vấn hoặc Giảng viên gọi `createProposal` -> Giao dịch bị revert với lỗi `NoEligibleVoters()`.
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-04: Chốt danh sách cử tri (Snapshot) và ngưỡng phê duyệt 51%
- **Nội dung quy tắc:** Tại thời điểm đề xuất được tạo, hệ thống chụp lại (snapshot) chính xác danh sách các thành viên Ban cán sự hợp lệ hiện thời và số lượng cử tri $N = \text{eligibleVoterCount}$. Ngưỡng phê duyệt tối thiểu được tính toán cố định tại thời điểm này theo công thức:
  $$\text{requiredVotes} = \max\left(1, \lceil 0.51 \times N \rceil\right)$$
  Ngưỡng này không bao giờ bị thay đổi bởi việc bổ nhiệm/bãi nhiệm cán sự sau đó.
- **Điều kiện áp dụng:** Khi hàm `createProposal` thực thi thành công với $N \ge 1$.
- **Hành vi được phép:** Ghi nhận danh sách cử tri snapshot `isSnapshotVoter[classId][proposalId][officer] = true` và tính `minimumApprovals = requiredVotes`.
- **Hành vi bị từ chối:** Tự ý thay đổi mẫu số hoặc ngưỡng biểu quyết của đề xuất cũ khi danh sách cán sự lớp có biến động; cho phép đề xuất chuyển sang `Ready` khi chưa nhận đủ `requiredVotes`.
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Lớp có 5 cán sự lúc tạo đề xuất ($N=5$, ngưỡng = 3). Sau khi tạo, Cố vấn xóa 1 cán sự. Đề xuất vẫn yêu cầu đủ 3 phiếu mới chuyển sang `Ready`.
  - *Kiểm thử vi phạm:* Đề xuất nhận được 2/5 phiếu trong hạn 7 ngày, cố tình gọi giải ngân -> Revert `ProposalNotReady()`.
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

---

## PHẦN 2. GIỚI HẠN CHỐNG LẠM DỤNG VÀ AN TOÀN TÀI CHÍNH

### 2.1. Phân định ranh giới quyền hạn giữa các vai trò
Hệ thống thiết lập nguyên tắc kiểm soát phân quyền chéo, không cho phép quyền lực tập trung vào một cá nhân:
- **Người tạo đề xuất không có quyền tự duyệt chi:** Giảng viên hoặc Cố vấn tạo đề xuất không có quyền biểu quyết duyệt đề xuất của chính mình và không có quyền kích hoạt chuyển tiền.
- **Cán sự tạo đề xuất chỉ có tối đa 1 lá phiếu:** Cán sự tạo đề xuất được quyền bỏ phiếu cho đề xuất của mình với tư cách là một thành viên tập thể (1 phiếu duy nhất), nhưng không thể tự ý giải ngân nếu chưa đạt đủ số phiếu của các thành viên khác theo quy định ngưỡng 51%.
- **Quản trị viên không có quyền can thiệp tài chính:** Quản trị viên hệ thống tuyệt đối không thể duyệt đề xuất, không thể biểu quyết và không thể rút tiền từ bất kỳ quỹ lớp nào.

### 2.2. Cơ chế xác định tư cách cử tri và Kiểm tra kép (Dual Check - QA-04 Phương án A)
Nhằm ngăn chặn hành vi lạm dụng quyền biểu quyết khi có sự biến động nhân sự cán sự lớp trong lúc đề xuất đang nhận phiếu, hệ thống áp dụng cơ chế **Kiểm tra kép (Phương án A)**:
- **Quy tắc kiểm tra kép khi gọi `voteProposal`:** Một tài khoản chỉ được công nhận là bỏ phiếu hợp lệ khi và chỉ khi thỏa mãn đồng thời hai điều kiện tại thời điểm giao dịch được đào:
  1. *Điều kiện Snapshot:* `isSnapshotVoter[classId][proposalId][msg.sender] == true` (nằm trong danh sách cán sự chốt tại thời điểm tạo đề xuất).
  2. *Điều kiện Hiện hành:* `_classOfficers[classId].contains(msg.sender) == true` (hiện tại vẫn đang là Ban cán sự hợp lệ của lớp, chưa bị bãi nhiệm).
- **Hệ quả đối với thành viên bị bãi nhiệm:** Nếu cán sự X có tên trong snapshot nhưng sau đó bị Cố vấn bãi nhiệm (`removeOfficer`) trước khi bỏ phiếu:
  - X lập tức bị tước quyền biểu quyết đối với đề xuất cũ. Lệnh gọi `voteProposal` của X sẽ bị revert ngay lập tức do vi phạm điều kiện hiện hành (TC-28).
  - Các lá phiếu mà X đã bỏ hợp lệ trước thời điểm bị bãi nhiệm vẫn được giữ nguyên hiệu lực (bảo toàn lịch sử).
- **Tình huống cán sự được phục chức (TC-44):** Nếu cán sự X bị xóa rồi được thêm lại vào Ban cán sự trước khi hết hạn 7 ngày, X lại thỏa mãn cả hai điều kiện trên và được phép bỏ phiếu bình thường.

### 2.3. Ranh giới thời hạn biểu quyết 7 ngày
- **Thời hạn biểu quyết cố định:** Đúng **7 ngày** kể từ thời điểm tạo đề xuất (tính theo giây Unix: $\text{votingDeadline} = \text{creationTime} + 7\text{ days} = \text{creationTime} + 604,800\text{ seconds}$).
- **Xử lý tại mốc biên thời gian:**
  - Tại $\text{block.timestamp} \le \text{votingDeadline}$ (kể cả tại đúng giây cuối cùng $\text{block.timestamp} == \text{votingDeadline}$): Giao dịch bỏ phiếu hợp lệ được chấp nhận. Hàm `failProposal` bị revert nếu gọi tại mốc này.
  - Tại $\text{block.timestamp} > \text{votingDeadline}$ (quá 1 giây trở đi): Mọi nỗ lực bỏ phiếu đều bị từ chối (revert với lỗi `VotingDeadlineExceeded`). Đề xuất nếu chưa đạt `Ready` sẽ được coi là thất bại về mặt hiệu lực thời gian thực.

### 2.4. Ranh giới thời hạn giải ngân 5 ngày sau khi đợt học đóng
- **Đề xuất khi đợt học đang mở (`OPEN`):** Không áp dụng thời hạn 5 ngày giải ngân (`closedAt` chưa phát sinh, = 0). Đề xuất đã đạt `Ready` được quyền giải ngân bất kỳ lúc nào nếu quỹ lớp có đủ tiền và đáp ứng các điều kiện khác. Tuyệt đối không tự áp dụng thời hạn 5 ngày tính từ một `closedAt` chưa tồn tại.
- **Đề xuất sau khi đợt học đã đóng (`CLOSED`):**
  - Thời hạn giải ngân tối đa là **5 ngày** kể từ thời điểm Quản trị viên thực hiện đóng đợt học thực tế:
    $$\text{executionDeadline} = \text{closedAt} + 5\text{ days} = \text{closedAt} + 432,000\text{ seconds}$$
  - Tại $\text{block.timestamp} \le \text{closedAt} + 5\text{ days}$ (kể cả tại mốc biên $\text{block.timestamp} == \text{closedAt} + 5\text{ days}$): Ban cán sự được giải ngân bình thường.
  - Tại $\text{block.timestamp} > \text{closedAt} + 5\text{ days}$: Tuyệt đối cấm giải ngân chuyển ETH; đề xuất chuyển sang trạng thái `Expired`.

### 2.5. Cơ chế chuyển trạng thái lười (Lazy State Transition) đối với đề xuất quá hạn
Do máy ảo Ethereum (EVM) là mô hình tính toán bị động, hệ thống áp dụng cơ chế **Chuyển trạng thái lười**:
1. **Đối với đề xuất `Pending` quá hạn 7 ngày biểu quyết:**
   - Hàm xem trạng thái `getProposalState` trả về trực tiếp giá trị `ProposalState.Failed`.
   - Bất kỳ ai cũng có thể gọi hàm công khai `failProposal(classId, proposalId)` để cập nhật trạng thái lưu trữ trên blockchain từ `Pending` sang `Failed` và phát sự kiện `ProposalFailed`.
2. **Đối với đề xuất `Ready` quá hạn 5 ngày sau đóng đợt học:**
   - Hàm xem trạng thái `getProposalState` trả về trực tiếp giá trị `ProposalState.Expired`.
   - Bất kỳ ai cũng có thể gọi hàm công khai `expireProposal(classId, proposalId)` để cập nhật trạng thái lưu trữ từ `Ready` sang `Expired` và phát sự kiện `ProposalExpired`.
   - Nếu Ban cán sự gọi `executeProposal` sau hạn chót 5 ngày: Smart contract không chuyển ETH, không revert mà tự động cập nhật trạng thái đề xuất sang `Expired`, phát sự kiện `ProposalExpired` và kết thúc giao dịch thành công. Nếu gọi lại lần thứ hai trở đi, giao dịch sẽ bị revert do trạng thái đã là `Expired`.

### 2.6. Xử lý các trạng thái kết thúc (Terminal States)
- Các trạng thái: `Executed` (đã chi tiền), `Failed` (không đủ phiếu), `Cancelled` (bị hủy do GV đình chỉ), `Expired` (quá hạn giải ngân) là các **trạng thái kết thúc bất biến**.
- Một khi đề xuất đã rơi vào một trong bốn trạng thái kết thúc này:
  - Khóa vĩnh viễn trên blockchain, không thể chuyển ngược trạng thái về `Pending` hay `Ready`.
  - Không thể bỏ phiếu, không thể giải ngân và không thể hủy lại.
  - Số tiền dự kiến chi của các đề xuất `Failed`, `Cancelled`, `Expired` được bảo toàn nguyên vẹn trong số dư của lớp.

### 2.7. Biện pháp kỹ thuật ngăn chặn các vector tấn công
- **Ngăn chặn biểu quyết hai lần (Anti-Double Voting):** Lưu vết bằng `mapping(uint256 => mapping(uint256 => mapping(address => bool))) public hasVoted`. Nếu `hasVoted == true`, revert `ProposalAlreadyVoted()`.
- **Ngăn chặn giải ngân hai lần (Anti-Double Execution):** Kiểm tra `proposal.state == ProposalState.Ready`. Ngay trước khi chuyển tiền, gán `proposal.state = ProposalState.Executed` và trừ số dư `classBalances[classId] -= amount`.
- **Ngăn chặn tấn công tái nhập (Anti-Reentrancy Attack):** Áp dụng mô hình Checks-Effects-Interactions (CEI) kết hợp modifier `nonReentrant` của OpenZeppelin Contracts 5.x.
- **Ngăn chặn giả mạo ủy quyền qua `tx.origin`:** Tuyệt đối không dùng `tx.origin` để xác thực quyền hạn; bắt buộc dùng `msg.sender`.

---

### BẢNG CHI TIẾT CÁC QUY TẮC KINH TẾ PHẦN 2 (ER-05 ĐẾN ER-13)

#### Quy tắc ER-05: Kiểm tra kép tư cách cử tri khi biểu quyết (Dual Check)
- **Nội dung quy tắc:** Cử tri bỏ phiếu cho đề xuất bắt buộc phải đồng thời thỏa mãn: (1) có tên trong danh sách cử tri chốt lúc tạo đề xuất (`isSnapshotVoter == true`); VÀ (2) hiện tại vẫn đang là Ban cán sự hợp lệ của lớp (`_classOfficers.contains(msg.sender) == true`).
- **Điều kiện áp dụng:** Khi gọi hàm `voteProposal(classId, proposalId)`.
- **Hành vi được phép:** Cán sự lớp thỏa mãn cả hai điều kiện thực hiện bỏ phiếu chấp thuận trong hạn 7 ngày.
- **Hành vi bị từ chối:** Người không có tên trong snapshot lúc tạo đề xuất hoặc cán sự đã bị Cố vấn bãi nhiệm cố tình bỏ phiếu (revert `Unauthorized()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Cán sự A có tên trong snapshot và vẫn giữ chức -> Bỏ phiếu thành công.
  - *Kiểm thử vi phạm:* Cán sự A bị Cố vấn xóa khỏi lớp sau khi tạo đề xuất, cố tình gọi bỏ phiếu cho đề xuất cũ -> Revert `Unauthorized()` (TC-28).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-06: Cơ chế biểu quyết một chiều và chống bỏ phiếu trùng lặp
- **Nội dung quy tắc:** Hệ thống áp dụng cơ chế phê duyệt đơn (Approval-only voting). Mỗi cử tri hợp lệ chỉ được bỏ phiếu chấp thuận duy nhất một lần cho một đề xuất chi (`hasVoted == false`).
- **Điều kiện áp dụng:** Đề xuất đang ở trạng thái `Pending` và chưa hết hạn 7 ngày.
- **Hành vi được phép:** Cử tri hợp lệ bỏ phiếu lần đầu tiên cho đề xuất.
- **Hành vi bị từ chối:** Bỏ phiếu lần thứ hai trở đi cho cùng một đề xuất (revert `ProposalAlreadyVoted()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Cán sự B bỏ phiếu lần 1 -> Thành công, số phiếu tăng 1, phát `VoteCast`.
  - *Kiểm thử vi phạm:* Cán sự B tiếp tục gọi bỏ phiếu lần 2 cho cùng đề xuất đó -> Revert `ProposalAlreadyVoted()` (TC-04).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-07: Quyền tự biểu quyết của người tạo đề xuất là Ban cán sự
- **Nội dung quy tắc:** Nếu người tạo đề xuất là thành viên Ban cán sự của lớp, người đó được phép tự bỏ phiếu cho đề xuất do mình tạo ra với tư cách một cử tri bình thường (được tính tối đa 1 phiếu chấp thuận).
- **Điều kiện áp dụng:** Người tạo thuộc Ban cán sự lớp tại thời điểm tạo đề xuất và vẫn đang là cán sự khi bỏ phiếu.
- **Hành vi được phép:** Tự bỏ 1 phiếu tán thành cho đề xuất của mình trong hạn 7 ngày.
- **Hành vi bị từ chối:** Tự bỏ phiếu nhiều hơn 1 lần (revert `ProposalAlreadyVoted()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Cán sự A tạo đề xuất, sau đó gọi bỏ phiếu cho đề xuất này -> Thành công, số phiếu tăng từ 0 lên 1 (TC-14).
  - *Kiểm thử vi phạm:* Cán sự A cố bỏ phiếu thêm lần nữa -> Revert `ProposalAlreadyVoted()`.
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-08: Thời hạn biểu quyết 7 ngày và chuyển trạng thái `Failed`
- **Nội dung quy tắc:** Đề xuất chỉ nhận phiếu trong thời hạn đúng 7 ngày ($\le 604,800$ giây). Sau 7 ngày, nếu chưa đạt ngưỡng phê duyệt 51%, đề xuất chuyển sang trạng thái kết thúc `Failed` và không thể nhận thêm phiếu.
- **Điều kiện áp dụng:** `block.timestamp > creationTime + 7 days` và đề xuất chưa đạt `Ready`.
- **Hành vi được phép:** Bất kỳ ai gọi `failProposal` khi đề xuất đã quá hạn 7 ngày; bỏ phiếu trong khoảng thời gian $\le \text{votingDeadline}$.
- **Hành vi bị từ chối:** Bỏ phiếu khi đã quá 7 ngày (revert `VotingDeadlineExceeded()`); gọi `failProposal` khi chưa hết 7 ngày (revert `VotingDeadlineNotPassed()`); gọi `failProposal` cho đề xuất đã `Ready` (revert `ProposalNotPending()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Tại $T_0 + 7\text{ days}$ gọi bỏ phiếu thành công (TC-23). Tại $T_0 + 7\text{ days} + 1\text{s}$, bất kỳ ai gọi `failProposal` chuyển sang `Failed` thành công (TC-07).
  - *Kiểm thử vi phạm:* Gọi `failProposal` ở ngày thứ 3 -> Revert `VotingDeadlineNotPassed()`.
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-09: Độc quyền giải ngân của Ban cán sự và kiểm tra số dư quỹ
- **Nội dung quy tắc:** Duy nhất thành viên Ban cán sự đương nhiệm của lớp mới có quyền gọi giải ngân đề xuất `Ready`. Quỹ lớp phải có số dư khả dụng $\ge$ số tiền yêu cầu.
- **Điều kiện áp dụng:** `_classOfficers[classId].contains(msg.sender) == true`, `proposal.state == Ready`, `classBalances[classId] >= proposal.amount`.
- **Hành vi được phép:** Cán sự đương nhiệm gọi giải ngân khi đề xuất đã `Ready` và quỹ lớp đủ tiền.
- **Hành vi bị từ chối:** Giảng viên, Cố vấn hoặc người ngoài gọi giải ngân (revert `Unauthorized()`); gọi giải ngân khi số dư quỹ nhỏ hơn số tiền đề xuất (revert `InsufficientBalance()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Cán sự lớp gọi giải ngân đề xuất `Ready` khi quỹ đủ tiền -> Thành công, chuyển ETH cho người nhận.
  - *Kiểm thử vi phạm:* Giảng viên tạo đề xuất cố gọi giải ngân -> Revert `Unauthorized()` (TC-12). Cán sự gọi giải ngân khi quỹ không đủ tiền -> Revert `InsufficientBalance()` (TC-08).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-10: Thời hạn giải ngân 5 ngày sau khi đợt học đóng
- **Nội dung quy tắc:** Khi đợt học gắn với đề xuất đã chuyển sang `CLOSED`, thời hạn tối đa để giải ngân là 5 ngày kể từ thời điểm đóng thực tế (`closedAt + 5 days`). Trong hạn (kể cả tại mốc biên `==`), đề xuất được giải ngân bình thường. Nếu đợt học chưa đóng (`OPEN`), không áp dụng thời hạn 5 ngày này.
- **Điều kiện áp dụng:** Đề xuất có `phaseId`, đợt học đã `CLOSED`, `block.timestamp <= closedAt + 5 days`.
- **Hành vi được phép:** Giải ngân đề xuất `Ready` trong vòng 5 ngày sau khi đợt học đóng, hoặc bất kỳ lúc nào khi đợt học còn `OPEN`.
- **Hành vi bị từ chối:** Giải ngân chuyển tiền khi đợt học đã `CLOSED` và đã quá 5 ngày (`block.timestamp > closedAt + 5 days`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Đợt học đóng lúc `closedAt`, tại `closedAt + 2 days` cán sự gọi giải ngân thành công (TC-10). Tại đúng `closedAt + 5 days` vẫn giải ngân thành công.
  - *Kiểm thử vi phạm:* Đợt học chưa đóng (`OPEN`) mà gọi kiểm tra quá hạn 5 ngày -> Không áp dụng hạn 5 ngày, giải ngân bình thường (TC-34).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-11: Chuyển trạng thái lười sang `Expired` cho đề xuất quá hạn giải ngân
- **Nội dung quy tắc:** Đề xuất `Ready` gắn với đợt học đã `CLOSED` nhưng quá hạn 5 ngày (`block.timestamp > closedAt + 5 days`) sẽ bị chuyển sang trạng thái kết thúc `Expired`. Không chuyển ETH, tiền quỹ giữ nguyên an toàn.
- **Điều kiện áp dụng:** `phase.status == CLOSED`, `block.timestamp > closedAt + 5 days`, đề xuất đang ở `Ready`.
- **Hành vi được phép:** Bất kỳ ai gọi `expireProposal` để cập nhật trạng thái `Expired`; cán sự gọi `executeProposal` lần đầu quá hạn tự động chuyển trạng thái `Expired` mà không revert.
- **Hành vi bị từ chối:** Gọi giải ngân lần 2 khi đề xuất đã `Expired` (revert `ProposalNotReady()`); gọi `expireProposal` khi đợt học còn `OPEN` hoặc chưa quá 5 ngày (revert `ExecutionDeadlineNotPassed()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Quá hạn 5 ngày, bất kỳ ai gọi `expireProposal` -> Thành công chuyển sang `Expired` (TC-33). Cán sự gọi `executeProposal` lần đầu quá hạn -> Không chuyển tiền, chuyển sang `Expired`, kết thúc thành công (TC-26).
  - *Kiểm thử vi phạm:* Gọi `expireProposal` khi đợt học còn `OPEN` -> Revert `ExecutionDeadlineNotPassed()` (TC-33).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-12: Nguyên tắc đơn nhất giải ngân (Chống chi trùng lặp)
- **Nội dung quy tắc:** Mỗi đề xuất chi hợp lệ chỉ được giải ngân thành công duy nhất một lần. Sau khi giải ngân, đề xuất chuyển vĩnh viễn sang trạng thái `Executed` và bị khóa.
- **Điều kiện áp dụng:** Khi đề xuất đã chuyển sang `Executed`.
- **Hành vi được phép:** Giải ngân thành công 1 lần duy nhất khi ở trạng thái `Ready`.
- **Hành vi bị từ chối:** Mọi giao dịch gọi giải ngân tiếp theo cho đề xuất đã `Executed` (revert `ProposalNotReady()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Giải ngân lần 1 thành công chuyển 0.2 ETH, trạng thái thành `Executed`.
  - *Kiểm thử vi phạm:* Cố gọi giải ngân lần 2 cho đề xuất đã `Executed` -> Bị revert, số dư quỹ lớp không bị trừ thêm (TC-09).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-13: An toàn chuyển tiền ETH và xử lý khi giao dịch thất bại
- **Nội dung quy tắc:** Quá trình chuyển ETH phải tuân thủ Checks-Effects-Interactions, sử dụng `call{value: ...}("")`, và có chốt chặn `nonReentrant`. Nếu lệnh `call` thất bại, toàn bộ giao dịch bắt buộc phải revert, bảo toàn nguyên vẹn số dư quỹ và giữ nguyên trạng thái `Ready`.
- **Điều kiện áp dụng:** Khi thực hiện giải ngân trong hàm `executeProposal`.
- **Hành vi được phép:** Chuyển ETH thành công cho địa chỉ người thụ hưởng hợp lệ; chuyển trạng thái sang `Executed`.
- **Hành vi bị từ chối:** Cho phép chuyển trạng thái `Executed` hoặc trừ số dư khi lệnh chuyển tiền thất bại; cho phép tấn công tái nhập.
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Người nhận là ví cá nhân thông thường -> Nhận ETH thành công, số dư quỹ giảm tương ứng.
  - *Kiểm thử vi phạm:* Người nhận là hợp đồng cố tình từ chối nhận ETH -> Toàn bộ giao dịch revert, đề xuất giữ nguyên `Ready`, số dư quỹ lớp không đổi (TC-22, TC-37).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

---

## PHẦN 3. QUYỀN QUẢN TRỊ VÀ PHÂN CÔNG TRÁCH NHIỆM

### 3.1. Nguyên tắc đặc quyền tối thiểu (Least Privilege)
Mỗi vai trò chỉ được cấp phát đúng và đủ các quyền hạn cần thiết để thực hiện nhiệm vụ nghiệp vụ được giao. Không tồn tại bất kỳ "Siêu tài khoản" nào có quyền lực can thiệp tài chính tùy tiện trên toàn bộ hệ thống.

### 3.2. Quyền hạn và giới hạn của từng vai trò

#### 1. Quản trị viên hệ thống (System Admin)
- **Quyền hạn được phép:** Quản lý danh mục Giảng viên (`registerLecturer`, `updateLecturerAddress`, `setLecturerStatus`); quản lý chu kỳ đợt học 3 tháng (`createPhase`, `closePhase` khi `block.timestamp > endTime`); quản lý phân công giảng dạy theo lô (`batchUpsertAssignments`); khởi tạo lớp và phân công Cố vấn (`initializeClass`); chuyển giao quyền sở hữu 2 bước (`Ownable2Step`); hủy khẩn cấp đề xuất của Giảng viên bị đình chỉ (`cancelSuspendedLecturerProposal`).
- **Giới hạn nghiêm ngặt:** **Tuyệt đối cấm rút tiền quỹ lớp**; tuyệt đối cấm biểu quyết duyệt đề xuất chi; không được trực tiếp thêm/xóa Ban cán sự của lớp; không thể đóng đợt học khi chưa hết thời gian theo lịch (`block.timestamp <= endTime`).

#### 2. Giảng viên (Lecturer)
- **Quyền hạn được phép:** Tạo đề xuất chi (`createProposal`) cho lớp học mà mình được phân công giảng dạy, với điều kiện đợt học căn cứ đang mở (`OPEN`) và còn trong thời hạn (`block.timestamp <= endTime`), và lớp có $\ge 1$ cán sự đủ điều kiện; tra cứu số dư và lịch sử đề xuất của lớp mình giảng dạy.
- **Giới hạn nghiêm ngặt:** Không có quyền biểu quyết duyệt đề xuất chi; không có quyền thực hiện giải ngân chuyển ETH; không được tự sửa đổi phân công; đề xuất chịu cơ chế hủy khẩn cấp nếu Giảng viên bị đình chỉ.

#### 3. Cố vấn học tập của lớp (Advisor)
- **Quyền hạn được phép:** Quản lý độc quyền nhân sự Ban cán sự của lớp mình phụ trách (`addOfficer`, `removeOfficer`); tạo đề xuất chi cho lớp mình phụ trách kèm mã đợt học `phaseId` hợp lệ; hủy khẩn cấp đề xuất của Giảng viên bị đình chỉ trong phạm vi lớp mình phụ trách.
- **Giới hạn nghiêm ngặt:** Không có quyền biểu quyết duyệt đề xuất chi; không có quyền thực hiện giải ngân chuyển tiền quỹ; không có quyền can thiệp vào nhân sự Ban cán sự hoặc quỹ của lớp khác.

#### 4. Cán bộ lớp (Class Officer)
- **Quyền hạn được phép:** Tạo đề xuất chi cho lớp mình phụ trách kèm mã đợt học `phaseId` hợp lệ; **độc quyền biểu quyết tập thể** (ngưỡng 51%); **độc quyền thực hiện giải ngân** khi đề xuất đã `Ready` và trong hạn; tự bỏ 1 phiếu cho đề xuất do chính mình tạo ra.
- **Giới hạn nghiêm ngặt:** Chỉ có quyền hạn trong phạm vi đúng mã lớp (`classId`) của mình; bị tước quyền biểu quyết ngay lập tức nếu bị Cố vấn bãi nhiệm khỏi Ban cán sự trước khi bỏ phiếu.

### 3.3. Quy tắc xử lý khi một người dùng mang nhiều vai trò (Multi-Role Resolution)
Hệ thống áp dụng **Quy tắc thứ tự ưu tiên vai trò** khi gọi hàm `createProposal`:
1. **Ưu tiên 1 (Cố vấn học tập - Advisor):** Nếu `classAdvisors[classId] == msg.sender`: Gán `proposerRole = ProposerRole.Advisor`. Đề xuất không bị hủy bởi cơ chế đình chỉ giảng viên.
2. **Ưu tiên 2 (Ban cán sự - Class Officer):** Nếu `_classOfficers[classId].contains(msg.sender) == true`: Gán `proposerRole = ProposerRole.ClassOfficer`. Đề xuất không bị hủy bởi cơ chế đình chỉ giảng viên.
3. **Ưu tiên 3 (Giảng viên bộ môn - Lecturer):** Nếu có phân công giảng dạy hợp lệ trong đợt học đang mở: Gán `proposerRole = ProposerRole.Lecturer`. Đề xuất chịu sự quản lý của cơ chế hủy khẩn cấp nếu Giảng viên sau đó bị Quản trị viên đình chỉ (`isActive == false`).

### 3.4. Dữ liệu trạng thái hiện hành và Giới hạn lưu trữ lịch sử trên Blockchain
- **Trạng thái on-chain trong Storage:** Smart Contract `KTSAcademicRegistry` chỉ lưu trữ trạng thái hiệu lực hiện hành (`isValid == true/false` trong mapping theo `assignmentKey`). Hợp đồng không lưu mảng toàn bộ lịch sử trong biến trạng thái để tối ưu gas.
- **Bảo toàn lịch sử qua Sự kiện (Blockchain Events):** Chuỗi lịch sử đầy đủ được phát qua các sự kiện on-chain: `TeachingAssignmentsBatchUpdated` và `TeachingAssignmentRecordUpdated`. Việc truy vấn chuỗi lịch sử đầy đủ phụ thuộc vào các dịch vụ chỉ mục ngoài chuỗi (The Graph / Off-chain Indexer).

---

### BẢNG CHI TIẾT CÁC QUY TẮC KINH TẾ PHẦN 3 (ER-14 ĐẾN ER-18)

#### Quy tắc ER-14: Cơ chế hủy khẩn cấp đề xuất của Giảng viên bị đình chỉ
- **Nội dung quy tắc:** Khi một Giảng viên bị Quản trị viên đình chỉ (`isActive == false`), Quản trị viên hệ thống (trên mọi lớp) hoặc Cố vấn học tập (trong lớp mình phụ trách) có quyền gọi hàm `cancelSuspendedLecturerProposal` để hủy đề xuất đang ở trạng thái `Pending` hoặc `Ready` do Giảng viên đó tạo ra.
- **Điều kiện áp dụng:** `proposal.proposerRole == ProposerRole.Lecturer`, `registry.isLecturerActive(proposal.proposer) == false`, đề xuất đang có trạng thái hiệu lực thực tế là `Pending` (trong hạn 7 ngày) hoặc `Ready` (trong hạn 5 ngày giải ngân).
- **Hành vi được phép:** Admin hoặc Cố vấn lớp gọi hủy đề xuất của Giảng viên bị đình chỉ khi đề xuất chưa kết thúc.
- **Hành vi bị từ chối:** Hủy đề xuất của Giảng viên đang hoạt động bình thường (revert `LecturerNotSuspended()`); hủy đề xuất đã giải ngân `Executed` (revert `ProposalNotCancellable()`); hủy đề xuất do Cán sự lớp tạo ra (revert `CannotCancelStudentProposal()`); hủy đề xuất đã quá hạn 7 ngày thực tế đã `Failed` (revert `ProposalNotCancellable()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Giảng viên bị đình chỉ có đề xuất `Pending`, Cố vấn lớp gọi hủy thành công (TC-29).
  - *Kiểm thử vi phạm:* Cố hủy đề xuất của Giảng viên đang hoạt động bình thường -> Revert `LecturerNotSuspended()` (TC-31). Cố hủy đề xuất đã giải ngân -> Revert `ProposalNotCancellable()` (TC-32).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-15: Phân quyền quản trị tối thiểu và chuyển giao quyền 2 bước (`Ownable2Step`)
- **Nội dung quy tắc:** Quản trị viên hệ thống kế thừa `Ownable2Step` của OpenZeppelin. Chuyển giao quyền quản trị bắt buộc trải qua 2 bước: Bước 1 gọi `transferOwnership(newOwner)`, Bước 2 địa chỉ mới gọi `acceptOwnership()`. Quản trị viên không có quyền rút tiền hay biểu quyết chi.
- **Điều kiện áp dụng:** Khi thực hiện thay đổi chủ quản trị contract.
- **Hành vi được phép:** Admin hiện tại đề xuất địa chỉ mới; địa chỉ mới gọi chấp nhận quyền.
- **Hành vi bị từ chối:** Địa chỉ chưa được đề xuất cố nhận quyền; Admin gọi hàm rút tiền quỹ lớp (revert do contract không có hàm này).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Admin chuyển cho ví B, ví B gọi chấp nhận -> B trở thành Admin mới (TC-27).
  - *Kiểm thử vi phạm:* Ví B chưa gọi chấp nhận mà cố thực hiện quyền Admin -> Revert `Unauthorized()`. Admin gọi hàm rút tiền quỹ -> Bị từ chối (TC-11).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-16: Quản lý danh mục Giảng viên và phân công theo lô
- **Nội dung quy tắc:** Quản trị viên quản lý danh mục giảng viên độc lập (gắn với `lecturerId` ổn định) và tải phân công giảng dạy theo lô cho từng đợt học (`phaseId`). Bắt buộc phát sự kiện tổng chứa `batchId` và `recordCount`.
- **Điều kiện áp dụng:** Chỉ Quản trị viên hệ thống được phép gọi.
- **Hành vi được phép:** Admin tải phân công theo lô hợp lệ; Giảng viên đổi ví qua `updateLecturerAddress` vẫn giữ nguyên phân công theo `lecturerId`.
- **Hành vi bị từ chối:** Người không phải Admin gọi cập nhật (revert `Unauthorized()`); gán ví mới trùng với ví của giảng viên khác (revert `AddressAlreadyAssigned()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Tải lô 10 bản ghi phân công -> Thành công, phát event tổng với `recordCount = 10` (TC-16). Giảng viên đổi ví mới vẫn tạo được đề xuất bình thường (TC-41).
  - *Kiểm thử vi phạm:* Đổi ví sang địa chỉ đã được gán cho giảng viên khác -> Revert `AddressAlreadyAssigned()` (TC-42).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-17: Phân quyền quản trị nhân sự Ban cán sự của Cố vấn lớp
- **Nội dung quy tắc:** Cố vấn học tập của lớp có quyền thêm hoặc xóa thành viên Ban cán sự (`_classOfficers`) của riêng lớp mình. Sử dụng cấu trúc dữ liệu tập hợp duy nhất `EnumerableSet` để đảm bảo không trùng lặp và xóa an toàn.
- **Điều kiện áp dụng:** Người gọi là Cố vấn hợp lệ của lớp (`classAdvisors[classId] == msg.sender`), địa chỉ cán sự khác `address(0)` và khác địa chỉ Cố vấn.
- **Hành vi được phép:** Cố vấn lớp thêm/xóa cán sự trong phạm vi lớp mình phụ trách.
- **Hành vi bị từ chối:** Thêm cán sự cho lớp khác mà mình không làm cố vấn (revert `Unauthorized()`); thêm địa chỉ `address(0)` (revert `InvalidAddress()`).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Cố vấn Lớp 1 thêm ví X làm cán sự Lớp 1 -> X trở thành cán sự hợp lệ.
  - *Kiểm thử vi phạm:* Cố vấn Lớp 1 cố thêm cán sự cho Lớp 2 -> Revert `Unauthorized()` (TC-02).
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-18: Quản lý khoản đóng góp hợp lệ (Không rút vốn/hoàn trả cá nhân)
- **Nội dung quy tắc:** Quỹ vận hành theo nguyên tắc phi lợi nhuận, không lãi suất, không chia lợi tức. Các khoản đóng góp hợp lệ của thành viên vào quỹ lớp trở thành tài sản chung không thể hoàn trả theo yêu cầu rút tiền cá nhân thông thường; mọi khoản chi trả đều phải thông qua đề xuất chi hợp lệ và biểu quyết tập thể của Ban cán sự.
- **Điều kiện áp dụng:** Toàn bộ vòng đời dòng tiền nạp quỹ hợp lệ trong hệ thống.
- **Hành vi được phép:** Nạp tiền vào quỹ lớp và giải ngân theo đề xuất chi đã được Ban cán sự biểu quyết thông qua.
- **Hành vi bị từ chối:** Người nạp tiền gọi hàm rút lại khoản tiền đóng góp hợp lệ (revert do contract không hỗ trợ hàm rút tiền cá nhân tùy ý).
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Sinh viên nạp tiền và tra cứu số dư minh bạch trên hàm view `classBalances`.
  - *Kiểm thử vi phạm:* Sinh viên cố tình gọi hàm rút lại tiền cá nhân -> Giao dịch bị revert do không tồn tại hàm rút tiền cá nhân.
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

#### Quy tắc ER-19: Quy trình thẩm định và xem xét hoàn tiền khi đóng nhầm (Mistaken Deposit Refund Protocol)
- **Nội dung quy tắc:** Người dùng chuyển nhầm tiền vào quỹ lớp (sai `classId`, sai số tiền, lỗi giao dịch ngoại ý) được quyền gửi yêu cầu xem xét hoàn tiền. Yêu cầu chỉ được chấp thuận khi đã qua thẩm định độc lập xác nhận giao dịch nạp nhầm thực tế, được phê duyệt theo thẩm quyền, số tiền hoàn trả $\le$ số tiền nạp gốc, và ghi nhận trạng thái đã hoàn tiền để chống hoàn trùng lặp. Tuyệt đối không cho phép Quản trị viên tự ý rút tiền hoàn và không hoàn tiền tự động khi chưa qua phê duyệt.
- **Mục đích:** Thiết lập cơ chế công bằng, minh bạch giải quyết các sai sót chuyển nhầm tiền của người dùng mà vẫn bảo toàn 100% tính toàn vẹn của quỹ lớp và ngăn chặn nguy cơ rút trộm tiền.
- **Điều kiện áp dụng:** Giao dịch nạp tiền gốc có hash giao dịch (`txHash`) tồn tại trên blockchain gắn với sự kiện `DepositReceived`, `msg.sender` của yêu cầu hoàn tiền khớp với người nạp gốc, giao dịch chưa từng được hoàn tiền trước đó (`isRefunded[txHash] == false`), và quỹ lớp hiện hành có đủ số dư khả dụng (`classBalances[classId] >= refundAmount`).
- **Hành vi được phép:** Người nạp gốc gửi yêu cầu hoàn tiền kèm bằng chứng; Ban cán sự và Cố vấn thẩm định; thực hiện hoàn trả ETH theo đúng quy trình phê duyệt đã thống nhất; ghi nhận trạng thái `isRefunded[txHash] = true` và phát sự kiện `RefundExecuted`.
- **Hành vi bị từ chối:** 
  - Yêu cầu hoàn tiền cho khoản đóng góp hợp lệ (revert `ValidContributionNotRefundable()`);
  - Yêu cầu hoàn tiền tự động chuyển ETH khi chưa qua phê duyệt (revert `RefundNotApproved()`);
  - Hoàn tiền lần thứ hai trở đi cho cùng một giao dịch gốc (revert `AlreadyRefunded()`);
  - Số tiền yêu cầu hoàn lớn hơn số tiền thực nạp trong giao dịch gốc (revert `RefundAmountExceedsDeposit()`);
  - Quản trị viên hệ thống (Admin) tự ý gọi lệnh rút tiền hoàn trực tiếp không qua quy trình (revert `Unauthorized()`);
  - Hoàn tiền khi số dư khả dụng của lớp không đủ (revert `InsufficientBalance()`).
- **Biện pháp ngăn hoàn tiền trùng (Anti-Double Refund / Replay Prevention):** Lưu vết biến trạng thái mapping `mapping(bytes32 => bool) public isRefunded`. Kiểm tra `require(!isRefunded[txHash])` và áp dụng Checks-Effects-Interactions: gán `isRefunded[txHash] = true` và trừ `classBalances[classId]` trước khi thực hiện lệnh gọi cấp thấp `call{value: refundAmount}("")`.
- **Rủi ro còn tồn tại:** Tranh chấp ngoài chuỗi về việc khoản đóng là "hợp lệ" hay "đóng nhầm"; số dư lớp bị tạm thiếu nếu tiền nhầm đã bị giải ngân cho một đề xuất hợp lệ trước đó.
- **Cách kiểm chứng / Tình huống kiểm thử dự kiến:**
  - *Kiểm thử thành công:* Người dùng A nạp nhầm 0.2 ETH vào Lớp 1, gửi yêu cầu hợp lệ; sau khi được phê duyệt, nhận lại đúng 0.2 ETH; số dư Lớp 1 giảm 0.2 ETH; phát sự kiện `RefundExecuted(classId, txHash, A, 0.2 ETH)`.
  - *Kiểm thử vi phạm:* Người dùng A yêu cầu hoàn tiền lần 2 cho cùng `txHash` -> Giao dịch bị revert với lỗi `AlreadyRefunded()`. Người dùng yêu cầu hoàn 0.5 ETH cho giao dịch nạp 0.2 ETH -> Revert với lỗi `RefundAmountExceedsDeposit()`. Admin cố rút tiền hoàn trực tiếp -> Revert `Unauthorized()`.
- **Trạng thái quy tắc:** Đã đặc tả / Chưa triển khai / Chưa kiểm thử.

---

## PHẦN 4. CÁC TÌNH HUỐNG NGƯỜI DÙNG BỊ THIỆT VÀ PHẢN HỒI CỦA NHÓM

Dưới đây là 6 tình huống lạm dụng, sai sót kỹ thuật và rủi ro thực tế trong quản lý tài chính quỹ lớp, được phân tích chi tiết theo 6 tiêu mục tiêu chuẩn kèm phản hồi xử lý của nhóm:

### Tình huống 1: Người ngoài hoặc Quản trị viên lạm quyền cố tình rút trộm tiền trực tiếp từ quỹ lớp
1. **Tên tình huống:** Rút tiền quỹ trực tiếp không qua quy trình biểu quyết tập thể (Unauthorized Fund Drain).
2. **Người dùng hoặc vai trò có thể gây ra:** Quản trị viên hệ thống (Deployer/Admin) hoặc kẻ tấn công chiếm được khóa riêng (private key) của ví Admin.
3. **Cách tình huống xảy ra:** Người nắm ví Admin cố tình gọi một hàm rút tiền đặc quyền (như `withdraw`, `emergencyWithdraw`) để chuyển toàn bộ số dư ETH của các lớp về ví cá nhân.
4. **Tác hại có thể phát sinh:** Toàn thể sinh viên bị mất toàn bộ tiền quỹ đã đóng góp; quỹ lớp bị rút cạn; hệ thống mất uy tín hoàn toàn.
5. **Quy tắc dùng để ngăn chặn:** Quy tắc **ER-01, ER-09, ER-15, ER-19**.
6. **Phản hồi của nhóm (Group Response):** Bổ sung điều kiện kiểm soát nghiêm ngặt trong kiến trúc Smart Contract: **tuyệt đối không lập trình bất kỳ hàm rút tiền trực tiếp nào** dành cho Admin/Owner (kể cả hàm rút tiền khẩn cấp hay hoàn tiền tùy ý). Cách duy nhất để chuyển ETH ra khỏi hợp đồng là thông qua hàm giải ngân đề xuất chi hợp lệ hoặc quy trình giải quyết hoàn tiền đóng nhầm đã được thẩm định độc lập và phê duyệt hợp lệ. Chấp nhận rủi ro rằng không có cơ chế "giải cứu khẩn cấp" từ Admin để đổi lấy tính an toàn phi tập trung tuyệt đối cho tiền quỹ của sinh viên.

### Tình huống 2: Cán sự lớp bị bãi nhiệm vẫn cố tình biểu quyết đề xuất chi bất minh
1. **Tên tình huống:** Cán sự bị bãi nhiệm lạm dụng quyền biểu quyết cho đề xuất cũ (Stale Snapshot Privilege Abuse).
2. **Người dùng hoặc vai trò có thể gây ra:** Thành viên Ban cán sự vừa bị Cố vấn học tập bãi nhiệm do có dấu hiệu vi phạm hoặc gian lận.
3. **Cách tình huống xảy ra:** Cán sự X có tên trong snapshot cử tri của một đề xuất đang `Pending`. Sau khi bị Cố vấn xóa khỏi Ban cán sự (`removeOfficer`), X vẫn dùng ví cũ gửi giao dịch `voteProposal` để giúp đề xuất bất minh đạt ngưỡng duyệt chi.
4. **Tác hại có thể phát sinh:** Quỹ lớp bị thất thoát tiền cho khoản chi không chính đáng; quyết định kỷ luật của Cố vấn lớp bị vô hiệu hóa trên blockchain.
5. **Quy tắc dùng để ngăn chặn:** Quy tắc **ER-05 (Kiểm tra kép - Dual Check theo Phương án A)**.
6. **Phản hồi của nhóm (Group Response):** Sửa quy tắc biểu quyết trong đặc tả và hợp đồng: không chỉ kiểm tra danh sách snapshot lúc tạo đề xuất, mà hàm `voteProposal` bắt buộc kiểm tra đồng thời cả 2 điều kiện: `isSnapshotVoter == true` VÀ `_classOfficers.contains(msg.sender) == true`. Nếu cán sự đã bị xóa khỏi lớp, giao dịch bỏ phiếu lập tức bị revert với lỗi `Unauthorized()`, tước quyền biểu quyết ngay tức khắc.

### Tình huống 3: Đề xuất đã quá hạn giải ngân sau khi đợt học đóng nhưng vẫn bị rút tiền quỹ
1. **Tên tình huống:** Giải ngân đề xuất sau khi đợt học đã đóng quá hạn 5 ngày (Stale Proposal Post-Phase Execution).
2. **Người dùng hoặc vai trò có thể gây ra:** Thành viên Ban cán sự thông đồng hoặc sơ suất thực thi đề xuất cũ.
3. **Cách tình huống xảy ra:** Một đề xuất đạt `Ready` trong đợt học $P1$. Đợt học $P1$ đã kết thúc và được Admin đóng (`CLOSED`). Sau 10 ngày kể từ khi đóng đợt, Ban cán sự vẫn gọi `executeProposal` để giải ngân số tiền này, gây thâm hụt ngân sách của đợt học mới $P2$.
4. **Tác hại có thể phát sinh:** Quỹ lớp bị chi sai niên độ tài chính, gây thiếu hụt tiền cho các hoạt động của đợt học mới; vi phạm kế hoạch ngân sách lớp.
5. **Quy tắc dùng để ngăn chặn:** Quy tắc **ER-02, ER-10, ER-11 (Bắt buộc `phaseId` và Chuyển trạng thái lười sang `Expired`)**.
6. **Phản hồi của nhóm (Group Response):** Sửa quy tắc tạo đề xuất: bắt buộc mọi đề xuất đều phải gắn với `phaseId` hợp lệ để quản lý vòng đời theo đợt học. Đồng thời bổ sung logic chuyển trạng thái lười trong hàm `executeProposal`: nếu đợt học đã `CLOSED` và $\text{block.timestamp} > \text{closedAt} + 5\text{ days}$, hợp đồng không chuyển ETH, tự động đổi trạng thái đề xuất sang `Expired`, phát sự kiện `ProposalExpired` và kết thúc thành công.

### Tình huống 4: Giảng viên bị kỷ luật đình chỉ nhưng đề xuất chi cũ vẫn bị giải ngân
1. **Tên tình huống:** Giải ngân đề xuất của Giảng viên đã bị đình chỉ công tác (Suspended Lecturer Proposal Execution).
2. **Người dùng hoặc vai trò có thể gây ra:** Giảng viên bị đình chỉ thông đồng với Ban cán sự, hoặc Ban cán sự thiếu thông tin tiếp tục giải ngân đề xuất cũ.
3. **Cách tình huống xảy ra:** Giảng viên Y tạo đề xuất mua tài liệu học tập, sau đó Giảng viên Y bị Nhà trường kỷ luật đình chỉ công tác (`isActive == false`). Tuy nhiên đề xuất cũ của Giảng viên Y vẫn đang ở trạng thái `Pending` hoặc `Ready`.
4. **Tác hại có thể phát sinh:** Tiền quỹ lớp bị giải ngân cho một cá nhân không còn tư cách giảng dạy; mục đích chi không còn người nghiệm thu chịu trách nhiệm.
5. **Quy tắc dùng để ngăn chặn:** Quy tắc **ER-14 (Cơ chế hủy khẩn cấp đề xuất của Giảng viên bị đình chỉ)**.
6. **Phản hồi của nhóm (Group Response):** Bổ sung hàm kiểm soát khẩn cấp `cancelSuspendedLecturerProposal(classId, proposalId)` trong Smart Contract. Cho phép Quản trị viên hệ thống hoặc Cố vấn học tập của lớp chủ động hủy đề xuất của giảng viên bị đình chỉ, chuyển trạng thái sang `Cancelled`, bảo toàn 100% tiền quỹ.

### Tình huống 5: Tấn công tái nhập (Reentrancy) hoặc Hợp đồng người nhận từ chối ETH làm treo quỹ
1. **Tên tình huống:** Tấn công rút cạn quỹ qua Reentrancy hoặc Chuyển tiền thất bại làm sai lệch trạng thái (Reentrancy / Failed Transfer Lock).
2. **Người dùng hoặc vai trò có thể gây ra:** Địa chỉ người thụ hưởng trong đề xuất là một Smart Contract độc hại hoặc hợp đồng không lập trình hàm nhận ETH.
3. **Cách tình huống xảy ra:** Khi Ban cán sự gọi giải ngân, hợp đồng người nhận trong hàm `fallback()` cố tình gọi lại `executeProposal` trước khi số dư bị trừ; hoặc hợp đồng người nhận revert khiến giao dịch lỗi.
4. **Tác hại có thể phát sinh:** Quỹ lớp có thể bị rút cạn toàn bộ số dư qua vòng lặp tái nhập, hoặc đề xuất bị kẹt cứng làm mất tiền quỹ.
5. **Quy tắc dùng để ngăn chặn:** Quy tắc **ER-13 (Checks-Effects-Interactions, NonReentrant và Atomic Revert)**.
6. **Phản hồi của nhóm (Group Response):** Áp dụng nghiêm ngặt mẫu thiết kế Checks-Effects-Interactions: cập nhật `proposal.state = Executed` và trừ `classBalances` trước khi gọi lệnh chuyển tiền ngoài chuỗi. Trang bị thêm modifier `nonReentrant` của OpenZeppelin. Nếu lệnh `call` chuyển ETH thất bại, kích hoạt `revert PaymentFailed()`, hoàn nguyên toàn bộ trạng thái về `Ready` để bảo toàn quỹ và cho phép giải ngân lại.

### Tình huống 6: Lớp chưa có Ban cán sự nhưng vẫn tạo được đề xuất dẫn đến kẹt tiền hoặc duyệt sai
1. **Tên tình huống:** Tạo đề xuất khi lớp chưa có cử tri Ban cán sự dẫn đến chia cho 0 hoặc tự động duyệt (Zero Voter Proposal Anomaly).
2. **Người dùng hoặc vai trò có thể gây ra:** Cố vấn học tập hoặc Giảng viên vội vàng tạo đề xuất chi khi lớp mới khởi tạo chưa kịp bổ nhiệm cán sự.
3. **Cách tình huống xảy ra:** Lớp có $N = 0$ cán sự. Nếu hệ thống cho phép tạo đề xuất, công thức $\lceil 0.51 \times 0 \rceil$ có thể cho ra ngưỡng bằng 0, khiến đề xuất tự động nhảy sang `Ready` mà không qua biểu quyết; hoặc đề xuất bị treo vĩnh viễn không ai duyệt được.
4. **Tác hại có thể phát sinh:** Quỹ lớp có thể bị giải ngân sai quy định mà không có sự giám sát của tập thể sinh viên.
5. **Quy tắc dùng để ngăn chặn:** Quy tắc **ER-03 (Ràng buộc nhân sự Ban cán sự tối thiểu khi tạo đề xuất)**.
6. **Phản hồi của nhóm (Group Response):** Bổ sung điều kiện kiểm soát tiên quyết trong hàm `createProposal`: bắt buộc kiểm tra `_classOfficers[classId].length() >= 1`. Nếu bằng 0, giao dịch lập tức bị revert với lỗi tùy biến `NoEligibleVoters()`, ngăn chặn hoàn toàn việc tạo đề xuất khi lớp chưa có cán sự.

---

## PHỤ LỤC: CÁC ĐIỂM ĐÃ THỐNG NHẤT VÀ NỘI DUNG CẦN TIẾP TỤC XÁC NHẬN

### 1. Các quyết định kinh tế nhóm đã chính thức thống nhất
1. **Ngưỡng thông qua đề xuất:** Thống nhất áp dụng tỷ lệ **đa số 51% làm tròn trần**:
   $$\text{requiredVotes} = \max\left(1, \lceil 0.51 \times N \rceil\right)$$
   với $N$ là số Class Officer đủ điều kiện trong snapshot lúc tạo đề xuất ($N \ge 1$). Nếu $N=0$, cấm tạo đề xuất. Đã loại bỏ hoàn toàn ngưỡng cố định 4/5 trong toàn bộ tài liệu dự án.
2. **Bắt buộc `phaseId` cho mọi đề xuất:** Tất cả đề xuất chi, bất kể người tạo là Giảng viên, Cố vấn hay Ban cán sự, **đều bắt buộc phải gắn với một mã đợt học (`phaseId`) hợp lệ**. Khi đợt học đóng, đề xuất `Ready` chỉ được thực thi trong thời hạn 5 ngày (`closedAt + 5 days`). Tại đúng thời điểm `closedAt + 5 days`, đề xuất vẫn còn hạn; sau đó chuyển sang `Expired`. Nếu đợt học chưa đóng, không áp dụng hạn 5 ngày tính từ một `closedAt` chưa tồn tại.
3. **Cơ chế hoàn tiền khi đóng nhầm:** Thống nhất nguyên tắc **cho phép xem xét hoàn tiền đối với các trường hợp người dùng đóng nhầm vào quỹ lớp** (chọn sai lớp, nhập sai số tiền, lỗi giao dịch). Phân định rõ với các khoản đóng góp hợp lệ (không hoàn trả theo yêu cầu cá nhân thông thường). Hoàn tiền bắt buộc tuân thủ 10 nguyên tắc thẩm định, chống hoàn trùng lặp và không cho phép Admin tự ý rút tiền.

### 2. Các nội dung cần nhóm tiếp tục thảo luận và xác nhận
1. **Quy định chọn đợt học khi tạo đề xuất của Cố vấn và Ban cán sự:**
   - Đối với Giảng viên: Đợt học bắt buộc phải đang ở trạng thái `OPEN` và còn hạn (`block.timestamp <= endTime`).
   - Đối với Cố vấn và Ban cán sự: Có bắt buộc đợt học phải đang `OPEN` tại thời điểm tạo đề xuất hay được phép chọn bất kỳ đợt học nào trong năm học của lớp? (`[CẦN NHÓM XÁC NHẬN]`).
2. **Lựa chọn phương án kỹ thuật triển khai chức năng hoàn tiền đóng nhầm:**
   - *Phương án A (Chức năng hoàn tiền chuyên biệt):* Thiết kế hàm `requestRefund` và `approveRefund` riêng biệt, yêu cầu chữ ký phê duyệt đồng thuận giữa Cố vấn học tập và Ban cán sự.
   - *Phương án B (Quy trình đề xuất hoàn tiền riêng biệt):* Lập một dạng đề xuất đặc thù `ProposalType.Refund` đưa vào quy trình biểu quyết tập thể của Ban cán sự với ngưỡng phê duyệt 51%.
   - Nhóm cần biểu quyết lựa chọn phương án kiến trúc phù hợp trước khi lập trình ở Lab 9 (`[CẦN NHÓM XÁC NHẬN]`).
3. **Quyền biểu quyết của cán sự được phục chức (TC-44):**
   - Thống nhất duy trì Phương án A hiện tại (cho phép cán sự được phục chức bỏ phiếu cho đề xuất cũ nếu thỏa mãn đồng thời cả 2 điều kiện) hay cần áp dụng cơ chế đánh dấu hủy vĩnh viễn quyền biểu quyết một khi đã từng bị xóa khỏi lớp? (`[CẦN NHÓM XÁC NHẬN]`).
