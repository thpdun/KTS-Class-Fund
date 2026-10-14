# SPEC — KTS Class Fund

## 1. Mục tiêu

Hệ thống KTS Class Fund là nguyên mẫu ứng dụng phi tập trung (DApp) hỗ trợ quản lý quỹ lớp Kinh tế số (học phần ECO2432). Hệ thống giải quyết tình trạng thiếu minh bạch và khó khăn trong việc theo dõi thu - chi thủ công bằng cách:
- Cho phép sinh viên, ban cán sự lớp, giảng viên và cố vấn học tập tra cứu số dư quỹ và lịch sử giao dịch một cách công khai, minh bạch trên blockchain.
- Ghi nhận các khoản nạp tiền vào quỹ minh bạch trên blockchain.
- Quản lý quy trình tạo, phê duyệt tập thể và thực thi các yêu cầu chi tiêu theo các quy tắc kiểm soát chặt chẽ, hạn chế tối đa nguy cơ chi sai mục đích hoặc chi trùng lặp.
- **Kiến trúc nghiệp vụ phân quyền theo từng lớp học (`classId`):** Phiên bản đầu tiên phục vụ thử nghiệm cho một lớp Kinh tế số cụ thể, nhưng cấu trúc dữ liệu và logic nghiệp vụ được thiết kế có khả năng mở rộng hỗ trợ nhiều lớp độc lập trong tương lai mà không phải thay đổi toàn bộ mô hình phân quyền.
- **Cấu trúc thời gian năm học và thời lượng đợt học chuẩn hóa:**
  - Một năm học có đúng **2 kỳ học** (Semester 1 - `S1`, Semester 2 - `S2`).
  - Mỗi kỳ học có đúng **2 đợt học** (Phase 1 - `P1`, Phase 2 - `P2`).
  - Tổng cộng mỗi năm học có đúng **4 đợt học** độc lập.
  - Mỗi đợt học kéo dài **cố định 3 tháng theo lịch** (tính theo mốc ngày bắt đầu và ngày kết thúc trên lịch dương lịch; không mặc định 3 tháng là 90 ngày).
  - Định dạng mã đợt học chuẩn hóa: `<Năm>-<Kỳ>-<Đợt>` (ví dụ: `2026-S1-P1`).
- **Quản lý danh mục giảng viên độc lập và phân công giảng dạy theo lô:** Tách biệt danh mục giảng viên với lịch phân công giảng dạy theo từng kỳ/đợt học; hỗ trợ quy trình tải lên theo lô (batch upload) an toàn và tối ưu giao dịch.
- **Quản lý vòng đời đợt học (`OPEN` / `CLOSED`):** Phân định rõ ràng giữa trạng thái mở/đóng của đợt học và vòng đời của yêu cầu chi; bảo đảm các yêu cầu chi tạo hợp lệ trước khi đợt học đóng vẫn tiếp tục quy trình bỏ phiếu và giải ngân bình thường.
- **Tách bạch 4 vai trò rõ ràng:**
  1. *System Admin (Quản trị viên hệ thống):* Địa chỉ ví deployer ban đầu khi triển khai contract; quản lý danh mục Giảng viên, danh sách phân công giảng dạy theo kỳ/đợt học, phân công Cố vấn học tập theo từng lớp cụ thể và quản lý vòng đời đợt học; có quyền hủy các đề xuất do Giảng viên bị đình chỉ/ngừng hoạt động tạo ra trên toàn hệ thống; tuyệt đối không mặc nhiên có quyền biểu quyết hay thực thi thu - chi quỹ lớp nếu không có vai trò tương ứng.
  2. *Advisor (Cố vấn học tập):* Một giảng viên được phân công phụ trách một lớp cụ thể; có quyền quản lý danh sách Ban cán sự của riêng lớp đó; có quyền tạo yêu cầu chi cho lớp mình cố vấn (với điều kiện lớp có ít nhất 1 Class Officer đủ điều kiện, kể cả khi không giảng dạy lớp đó và quyền này không tự mất đi khi một đợt học đóng); có quyền hủy đề xuất do Giảng viên bị đình chỉ tạo ra trong phạm vi lớp mình cố vấn; không có quyền biểu quyết hay thực thi chi tiêu quỹ.
  3. *Lecturer (Giảng viên):* Được System Admin xác nhận tư cách trong danh mục giảng viên; được tạo yêu cầu chi cho lớp khi thỏa mãn điều kiện giảng dạy trong đợt học đang mở hoặc làm cố vấn học tập (với điều kiện lớp có ít nhất 1 Class Officer đủ điều kiện); theo dõi tiến độ; không có quyền biểu quyết hay thực thi giải ngân.
  4. *Class Officer (Ban cán sự):* Do Advisor của lớp quản lý; có quyền tạo yêu cầu chi cho lớp mình; độc quyền biểu quyết tập thể (ngưỡng 51%) và độc quyền thực thi giải ngân quỹ khi yêu cầu đủ điều kiện (`Ready`).

*Lưu ý: Đây là tài liệu đặc tả yêu cầu nghiệp vụ ban đầu (v0.1) cho nguyên mẫu thử nghiệm học tập trên Ethereum Sepolia Testnet, sử dụng ETH thử nghiệm, không xử lý tiền thật và không phải là hệ thống đã triển khai hoàn chỉnh.*

## 2. Đầu vào

Các dữ liệu đầu vào cần thiết cho hệ thống bao gồm:

1. **Địa chỉ ví người dùng (User Address):**
   - Định dạng: Địa chỉ ví Ethereum hợp lệ kết nối qua MetaMask (`0x...`).
   - Ai cung cấp: Người dùng tương tác với hệ thống (System Admin, Advisor, Lecturer, Class Officer, Sinh viên).
   - Điều kiện hợp lệ: Ví đã kết nối thành công, thuộc mạng thử nghiệm Sepolia.

2. **Mã định danh lớp học (Class ID):**
   - Định dạng: Số nguyên dương hoặc chuỗi định danh duy nhất của lớp học (`uint256` hoặc chuỗi chuẩn hóa, ví dụ `classId = 1` cho lớp Kinh tế số ban đầu).
   - Ai cung cấp: Người dùng khi thực hiện các thao tác gắn với lớp (nạp tiền, tạo đề xuất, biểu quyết, giải ngân, tra cứu).
   - Điều kiện hợp lệ: Lớp học đã được System Admin khởi tạo hợp lệ trên hệ thống.

3. **Danh mục Giảng viên (Lecturer Directory Data):**
   - **Mã định danh giảng viên (Lecturer ID / Code):** Chuỗi ký tự định danh duy nhất (ví dụ mã cán bộ/giảng viên).
   - **Địa chỉ ví giảng viên (`lecturerAddress`):** Địa chỉ ví Ethereum hợp lệ (`address`), khác `address(0)` và khác địa chỉ hợp đồng.
   - **Trạng thái hoạt động (`isActive`):** Trạng thái Boolean xác định giảng viên có đang hoạt động hay đã bị ngừng hoạt động.
   - Ai cung cấp: System Admin nhập đơn lẻ hoặc theo lô (batch).

4. **Dữ liệu Cấu trúc Năm học, Đợt học và Phân công giảng dạy (Academic Period & Assignment Data):**
   - **Mã định danh đợt học (`phaseId`):** Chuỗi chuẩn hóa theo mẫu `<Năm>-<Kỳ>-<Đợt>`, ví dụ `2026-S1-P1`, `2026-S1-P2`, `2026-S2-P1`, `2026-S2-P2`.
   - **Thời gian bắt đầu (`startTime`):** Dấu thời gian Unix (timestamp) của mốc 00:00:00 ngày bắt đầu đợt học theo lịch dương lịch.
   - **Thời gian kết thúc (`endTime`):** Dấu thời gian Unix (timestamp) của mốc 23:59:59 ngày kết thúc trọn vẹn đúng 3 tháng theo lịch dương lịch (không mặc định là 90 ngày).
   - **Trạng thái đợt học:** Giá trị trạng thái tối thiểu gồm `OPEN` (đang mở) và `CLOSED` (đã đóng).
   - **Bản ghi phân công giảng dạy (Teaching Assignment Record):**
     - Mã đợt học (`phaseId`).
     - Mã lớp (`classId`).
     - Mã định danh giảng viên (`lecturerId`): Định danh ổn định của giảng viên trong danh mục.
     - Địa chỉ ví giảng viên tại thời điểm phân công (`lecturerAddress`): Tra cứu tương ứng với `lecturerId` tại thời điểm thực hiện giao dịch.
     - Môn học hoặc mã môn học (`courseCode`).
     - Trạng thái hiệu lực của bản ghi phân công (`isValid`).
   - **Điều kiện hợp lệ của đợt học:** `phaseId` không được để trống; thời gian bắt đầu phải trước thời gian kết thúc (`startTime < endTime`); đợt học kéo dài đúng 3 tháng theo lịch; không trùng lặp mã đợt học đã tạo.
   - **Điều kiện hợp lệ của bản ghi phân công:** Giảng viên (`lecturerId`) phải thuộc danh mục giảng viên đang hoạt động (`isActive == true`); địa chỉ ví hợp lệ và khác `address(0)`; mã lớp `classId` phải tồn tại; môn học `courseCode` không để trống.
   - Ai cung cấp: System Admin nhập đơn lẻ hoặc theo lô.

5. **Dữ liệu phân công Cố vấn học tập (Advisor Data):**
   - **Mã lớp (`classId`):** Lớp học tương ứng.
   - **Địa chỉ ví Advisor:** Phải là một giảng viên đang có trạng thái hoạt động hợp lệ trong danh mục giảng viên.
   - Ai cung cấp: System Admin.

6. **Dữ liệu quản lý Ban cán sự của lớp (Class Officer Data):**
   - **Mã lớp (`classId`):** Lớp mà Advisor đang phụ trách.
   - **Địa chỉ Class Officer (`officerAddress`):** Địa chỉ ví thành viên Ban cán sự được thêm hoặc xóa; điều kiện hợp lệ: khác `address(0)`, khác địa chỉ Advisor và khác địa chỉ hợp đồng.
   - Ai cung cấp: Advisor của lớp đó.

7. **Số tiền nạp vào quỹ lớp (Deposit Amount):**
   - Định dạng: Giá trị số dương biểu diễn bằng đơn vị ETH/Wei gắn với một `classId`.
   - Ai cung cấp: Bất kỳ thành viên nào gửi tiền vào quỹ lớp tương ứng.
   - Điều kiện hợp lệ: Lớn hơn 0 (> 0 ETH) và người gửi có đủ số dư trong ví để chi trả kèm phí gas.

8. **Thông tin tạo yêu cầu chi của lớp (Proposal Details):**
   - **Mã lớp (`classId`):** Lớp phát sinh yêu cầu chi tiêu (bắt buộc đối với mọi yêu cầu).
   - **Căn cứ tạo yêu cầu (Basis of Authority):**
     - *Quy tắc chung:* Mọi đề xuất chi, bất kể người tạo thuộc vai trò Lecturer, Advisor hay Class Officer, **đều bắt buộc phải gắn với một mã đợt học (`phaseId`) hợp lệ** thuộc đúng lớp được liên kết. Không tạo ngoại lệ cho đề xuất do Advisor hoặc Class Officer tạo.
     - *Căn cứ 1 (Giảng viên - Lecturer):* Giảng viên có phân công giảng dạy trong đợt học `phaseId` đang `OPEN` của lớp đó (`block.timestamp <= endTime`).
     - *Căn cứ 2 (Cố vấn học tập - Advisor):* Giảng viên là Cố vấn học tập của lớp đó, gắn với mã đợt học `phaseId` hợp lệ thuộc lớp (`[CẦN NHÓM XÁC NHẬN]` về việc đợt học có bắt buộc phải đang `OPEN` tại thời điểm tạo đề xuất hay không).
     - *Căn cứ 3 (Ban cán sự - Class Officer):* Thành viên Ban cán sự của lớp đó, gắn với mã đợt học `phaseId` hợp lệ thuộc lớp (`[CẦN NHÓM XÁC NHẬN]` về việc đợt học có bắt buộc phải đang `OPEN` tại thời điểm tạo đề xuất hay không).
   - **Mục đích chi (Purpose/Description):** Chuỗi văn bản mô tả lý do chi tiêu; điều kiện hợp lệ: không được để trống.
   - **Số tiền yêu cầu chi (Amount):** Giá trị số dương biểu diễn bằng ETH/Wei; điều kiện hợp lệ: lớn hơn 0 (> 0 ETH).
   - **Địa chỉ người nhận (Recipient Address):** Địa chỉ ví Ethereum hợp lệ (`0x...`); điều kiện hợp lệ: không được là địa chỉ rỗng (`address(0)`) và không trùng với địa chỉ hợp đồng quỹ.
   - **Chốt danh sách cử tri và ngưỡng phê duyệt (Voter Snapshot & Threshold):**
     - Tại thời điểm tạo yêu cầu chi, hệ thống chốt số lượng Ban cán sự đủ điều kiện (`eligibleVoterCount`) và danh sách cử tri đủ điều kiện của lớp. Sự thay đổi số lượng Ban cán sự sau đó không làm thay đổi mẫu số đã chốt.
     - Số phiếu tối thiểu cần đạt để chuyển sang `Ready` được xác định theo công thức: `minimumApprovals = max(1, ceil(0.51 * eligibleVoterCount))`.
     - Bắt buộc lớp phải có ít nhất một thành viên Ban cán sự đủ điều kiện biểu quyết (`eligibleVoterCount >= 1`). Nếu `eligibleVoterCount == 0`, hệ thống từ chối mọi yêu cầu tạo chi (revert). Do `eligibleVoterCount >= 1`, ngưỡng này luôn ít nhất bằng 1 (`minimumApprovals >= 1`).

9. **Thông tin bỏ phiếu yêu cầu chi (Vote/Approval Confirmation):**
   - **Mã lớp (`classId`) & Mã yêu cầu chi (Proposal ID):** Định danh duy nhất của yêu cầu chi cần bỏ phiếu.
   - Ai cung cấp: Thành viên Ban cán sự thuộc danh sách đủ điều kiện đã được chốt tại thời điểm tạo yêu cầu chi đó (kể cả người tạo đề xuất nếu người đó là Ban cán sự).
   - Điều kiện hợp lệ: Yêu cầu chi tồn tại trên lớp; người gửi là cử tri đủ điều kiện theo danh sách chốt tại thời điểm tạo yêu cầu chi; thời hạn 7 ngày chưa trôi qua; mỗi địa chỉ ví chỉ được bỏ phiếu tối đa 1 lần cho cùng một yêu cầu chi.
   - Hiệu lực của lá phiếu & Kiểm tra kép (QA-04 Phương án A): Lá phiếu hợp lệ đã bỏ được giữ nguyên, kể cả khi thành viên sau đó bị xóa khỏi Ban cán sự. Tuy nhiên, nếu chưa bỏ phiếu mà bị xóa khỏi Ban cán sự, thành viên đó không còn quyền bỏ phiếu cho đề xuất cũ nữa (giao dịch bị revert do vi phạm điều kiện cán sự hiện hành).

10. **Lệnh cập nhật trạng thái đợt học và yêu cầu chi:**
    - **Lệnh đóng đợt học (`closePhase`):** Mã đợt học (`phaseId`) cần đóng.
      - Ai cung cấp: Duy nhất System Admin được phép gọi hàm đóng đợt học.
      - Điều kiện thời gian: Chỉ được đóng khi thời gian hiện tại đã vượt quá ngày kết thúc theo lịch (`block.timestamp > endTime`). Tại thời điểm `block.timestamp == endTime`, giao dịch bị từ chối (revert).
      - Hiệu lực: Chỉ chính thức chuyển sang trạng thái `CLOSED` khi giao dịch được đào thành công trên blockchain, đồng thời lưu mốc thời gian đóng thực tế (`closedAt = block.timestamp`).
    - **Lệnh cập nhật yêu cầu chi thất bại (`failProposal`):** Mã lớp (`classId`) & Mã yêu cầu chi (Proposal ID). Điều kiện thời gian: khi yêu cầu chi đã quá hạn 7 ngày kể từ khi tạo (`block.timestamp > creationTime + 7 days`) mà chưa đạt ngưỡng 51% (chưa chuyển sang `Ready`). Ai cung cấp: Bất kỳ người dùng nào (công khai).

11. **Lệnh thực hiện yêu cầu chi (Execution Trigger - `executeProposal`):**
    - **Mã lớp (`classId`) & Mã yêu cầu chi (Proposal ID):** Định danh của yêu cầu chi cần giải ngân.
    - Ai cung cấp: Class Officer đương nhiệm của chính lớp đó.
    - Điều kiện hợp lệ: Yêu cầu chi đang ở trạng thái đủ điều kiện thực hiện (`Ready`). Quỹ lớp `classId` có đủ số dư (`balance >= amount`).
    - Hạn chót thực hiện chi (Execution Deadline):
      - Khi đợt học căn cứ đang mở (`phase.status == OPEN`): Không áp dụng thời hạn 5 ngày (do `closedAt` chưa phát sinh, giá trị mặc định bằng 0); đề xuất `Ready` được phép thực hiện giải ngân bất kỳ lúc nào nếu đáp ứng đủ các điều kiện khác, tuyệt đối không bị đánh dấu thành `Expired`.
      - Khi đợt học căn cứ đã đóng (`phase.status == CLOSED`): Hạn chót thực hiện chi cố định là 5 ngày kể từ thời điểm đóng đợt học thực tế (`closedAt + 5 days`). Trong khoảng thời gian `block.timestamp <= closedAt + 5 days` (bao gồm đúng mốc biên `block.timestamp == closedAt + 5 days`), yêu cầu `Ready` được phép thực hiện giải ngân bình thường.
      - Khi đã quá hạn sau đóng đợt (`phase.status == CLOSED` VÀ `block.timestamp > closedAt + 5 days`): Lệnh gọi thực hiện chi không chuyển ETH mà kích hoạt cơ chế chuyển trạng thái lười (Lazy State Transition), cập nhật trạng thái đề xuất sang `Expired`, phát sự kiện `ProposalExpired` và kết thúc thành công. Nếu đề xuất đã ở trạng thái `Expired`, lệnh gọi thực hiện chi tiếp theo bị từ chối (revert).

12. **Lệnh hủy đề xuất của Giảng viên bị đình chỉ (`cancelSuspendedLecturerProposal`):**
    - **Mã lớp (`classId`) & Mã yêu cầu chi (Proposal ID):** Định danh của đề xuất cần hủy.
    - Ai cung cấp: System Admin (có quyền trên toàn bộ các lớp) hoặc Advisor của chính lớp `classId` đó.
    - Điều kiện hợp lệ: Chỉ áp dụng đối với đề xuất do Giảng viên (Lecturer) tạo ra VÀ Giảng viên đó hiện đang ở trạng thái bị đình chỉ/ngừng hoạt động (`isActive == false`); đề xuất phải đang ở trạng thái `Pending` hoặc `Ready`. Tuyệt đối không được hủy đề xuất do Class Officer tạo ra, không được hủy đề xuất của giảng viên đang hoạt động bình thường (`isActive == true`), và không thể hủy đề xuất thuộc các trạng thái kết thúc (`Executed`, `Failed`, `Cancelled`, `Expired`). Lệnh hủy không làm chuyển ETH.

13. **Lệnh công khai cập nhật đề xuất `Ready` quá hạn sang `Expired` (`expireProposal`):**
    - **Mã lớp (`classId`) & Mã yêu cầu chi (Proposal ID):** Định danh của đề xuất cần đánh dấu hết hạn trên storage.
    - Ai cung cấp: Bất kỳ người dùng nào (công khai - public transaction).
    - Điều kiện hợp lệ: Đợt học gắn với đề xuất đã đóng (`phase.status == CLOSED`), thời gian hiện tại đã vượt quá hạn giải ngân (`block.timestamp > closedAt + 5 days`), và đề xuất đang lưu trữ ở trạng thái `Ready`. Hàm cập nhật trạng thái sang `Expired`, phát sự kiện `ProposalExpired` và không thực hiện chuyển ETH. (Lưu ý: Nếu đợt học còn `OPEN`, hàm này bị revert do chưa đóng đợt học).

## 3. Quy tắc nghiệp vụ

Hệ thống áp dụng các quy tắc nghiệp vụ cốt lõi sau (được đánh mã để phục vụ kiểm thử và đối soát):

### A. Quản trị hệ thống, Danh mục giảng viên và Phân công theo lô
- **R0-A (System Admin ban đầu, Chuyển quyền quản trị 2 bước và Quản lý Danh mục Giảng viên):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (quy tắc nhóm đã chốt chính thức).
  - *Người thực hiện:* System Admin (địa chỉ ví deployer khi triển khai smart contract, hoặc địa chỉ quản trị kế nhiệm sau khi nhận quyền).
  - *Hành động:*
    - Khởi tạo và quản lý danh mục giảng viên độc lập (gồm mã giảng viên `lecturerId`, địa chỉ ví `lecturerAddress`, trạng thái hoạt động `isActive`).
    - Thêm giảng viên mới, sửa đổi thông tin ví, ngừng hoạt động (deactivate) giảng viên khi không còn sử dụng.
    - Nhập hoặc tải danh sách giảng viên theo lô (batch import).
    - **Chuyển giao quyền System Admin (Ownable2Step):** Sử dụng cơ chế chuyển giao quyền sở hữu an toàn hai bước (`Ownable2Step` của OpenZeppelin):
      1. Bước 1: System Admin hiện tại gọi hàm đề xuất chuyển quyền sở hữu cho một địa chỉ ví mới (`transferOwnership(newOwner)`). Tại thời điểm này, quyền quản trị **chưa** được chuyển sang địa chỉ mới; System Admin hiện tại vẫn giữ nguyên đầy đủ quyền hạn.
      2. Bước 2: Địa chỉ ví được đề xuất phải chủ động gọi hàm chấp nhận quyền sở hữu (`acceptOwnership()`). Chỉ sau khi giao dịch này thành công, quyền System Admin mới chính thức được chuyển giao sang địa chỉ mới.
      - *Mục đích:* Ngăn chặn tuyệt đối rủi ro chuyển nhầm quyền quản trị vào địa chỉ ví không tồn tại hoặc ví không có khả năng tương tác (mất quyền vĩnh viễn).
  - *Nguyên tắc dữ liệu và định danh giảng viên:*
    - Danh mục giảng viên độc lập với phân công giảng dạy; không cần nhập lại danh sách giảng viên khi bắt đầu đợt học mới.
    - Không xóa vĩnh viễn bản ghi giảng viên khỏi contract để tránh phá vỡ liên kết với lịch sử yêu cầu chi và giao dịch trước đây (sử dụng cờ trạng thái `isActive`).
    - **Phân định rõ định danh giảng viên ổn định và địa chỉ ví:**
      - Hệ thống lưu trữ `lecturerId` ổn định trong dữ liệu danh mục và dữ liệu phân công giảng dạy.
      - Khi giao diện người dùng cần hiển thị địa chỉ ví hiện hành của giảng viên, giao diện thực hiện truy vấn địa chỉ ví hiện tại tương ứng với `lecturerId`.
      - Phân biệt rõ: (1) Mã định danh giảng viên ổn định (`lecturerId`); (2) Địa chỉ ví hiện tại (`lecturerAddress`); (3) Lịch sử phân công theo từng đợt học; (4) Địa chỉ ví đã được ghi nhận trong các giao dịch hoặc sự kiện lịch sử (sự kiện quá khứ giữ nguyên địa chỉ ví tại thời điểm phát sinh, không bị làm sai lệch khi giảng viên đổi địa chỉ ví mới).
  - *Giới hạn quyền hạn:* System Admin quản lý phân quyền và dữ liệu đào tạo, nhưng **tuyệt đối không mặc nhiên có quyền bỏ phiếu hoặc tự thực hiện giao dịch chi quỹ** nếu không có vai trò tương ứng; không được tự ý rút tiền quỹ lớp; không được bỏ qua quy trình biểu quyết của Ban cán sự.
  - *Trường hợp bị từ chối:* Người gọi không phải System Admin -> Revert giao dịch; Địa chỉ chưa được đề xuất cố chấp nhận quyền quản trị -> Revert giao dịch.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Deployer thêm giảng viên hoặc ngừng hoạt động ví giảng viên -> Thành công. Deployer đề xuất ví B làm admin, ví B chấp nhận -> Ví B trở thành admin mới. Deployer gọi hàm rút tiền hoặc bỏ phiếu chi -> Bị từ chối.
    - Không đạt (Fail): Ví không phải deployer cố sửa danh mục giảng viên -> Bị từ chối (revert). Địa chỉ được đề xuất chưa gọi `acceptOwnership` mà cố thực hiện quyền admin -> Bị từ chối.

- **R0-B (Quản lý Phân công giảng dạy theo lô và Cố vấn học tập):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (quy tắc nhóm đã chốt chính thức).
  - *Người thực hiện:* System Admin.
  - *Hành động:*
    - Phân công một Giảng viên hoạt động làm Cố vấn học tập (Advisor) cho một `classId` cụ thể.
    - Nhập hoặc tải danh sách phân công giảng dạy theo lô (batch assignment) cho một đợt học cụ thể (`phaseId`).
    - Thêm, sửa, hoặc thu hồi bản ghi phân công giảng dạy.
  - *Nguyên tắc phân công và bảo toàn lịch sử dữ liệu:*
    - Danh sách giảng viên được phân công giảng dạy theo từng đợt học có thể được bổ sung và cập nhật (append/upsert).
    - **Không xóa mất lịch sử phân công cũ:** Dữ liệu cũ phải được lưu trữ để có thể tra cứu sau này (sử dụng cờ hiệu lực `isValid` hoặc cơ chế lập phiên bản thay vì xóa khỏi contract).
    - **Phân định rõ ràng 4 khía cạnh:**
      1. *Phân công đang có hiệu lực:* Bản ghi phân công giảng dạy gắn với `phaseId`, `classId`, `lecturerId` đang có `isValid == true`.
      2. *Lịch sử phân công đã thay đổi hoặc bị thu hồi:* Các bản ghi phân công cũ, đã bị sửa đổi hoặc đánh dấu `isValid == false`, được giữ nguyên để đối soát và tra cứu lịch sử.
      3. *Quyền tạo yêu cầu chi mới:* Bắt buộc kiểm tra dựa trên phân công đang có hiệu lực (`isValid == true`) tại thời điểm gọi giao dịch tạo yêu cầu chi.
      4. *Tình trạng của các yêu cầu chi đã được tạo trước đó:* Nếu một phân công giảng dạy bị xóa, thu hồi hoặc thay đổi, các yêu cầu chi đã được tạo trước đó vẫn tiếp tục hoạt động bình thường theo các quy tắc đã chốt tại thời điểm tạo yêu cầu. Việc thay đổi phân công **không được tự động hủy yêu cầu chi cũ, không xóa phiếu đã bỏ và không làm thay đổi ngưỡng biểu quyết đã chốt**.
  - *Sự kiện ghi nhận khi cập nhật phân công theo lô (Batch Event):*
    - Khi cập nhật một lô phân công giảng dạy, smart contract bắt buộc phát một **sự kiện tổng cho cả lô** (ví dụ: `TeachingAssignmentsBatchUpdated`), bao gồm tối thiểu:
      - Mã định danh lô (`batchId` hoặc tương đương).
      - Số lượng bản ghi trong lô (`recordCount`).
      - Mã đợt học (`phaseId`).
    - Yêu cầu bắt buộc là phải có event tổng này cho mỗi giao dịch lô; hệ thống không cấm việc phát thêm event chi tiết cho từng bản ghi nếu cần thiết cho đối soát ngoài chuỗi (off-chain indexers).
  - *Quy trình nhập dữ liệu theo lô qua Giao diện (Off-chain to On-chain):*
    1. System Admin chọn file phân công (Excel/CSV) trên Web DApp. File được xử lý ở giao diện, smart contract không đọc trực tiếp file.
    2. Giao diện kiểm tra cú pháp, loại bỏ dữ liệu trùng lặp và cảnh báo lỗi.
    3. Giao diện hiển thị bản xem trước (preview) để System Admin kiểm tra đối soát trước khi gửi.
    4. System Admin bấm xác nhận gửi giao dịch qua MetaMask.
    5. Giao diện chia thành một hoặc nhiều giao dịch theo kích thước lô phù hợp giới hạn gas của block trên Ethereum Sepolia.
    6. **Xử lý rủi ro giao dịch từng phần:** Nếu file được chia thành nhiều giao dịch, một số giao dịch có thể thành công trong khi giao dịch khác thất bại. Giao diện có trách nhiệm thông báo chi tiết kết quả từng lô, chỉ rõ các bản ghi đã ghi nhận và các bản ghi bị lỗi để System Admin đối soát và thử lại riêng phần lỗi, tuyệt đối không gửi lại toàn bộ tránh nhập trùng lặp.
    7. Dữ liệu chỉ được coi là đã ghi nhận thành công khi từng giao dịch blockchain tương ứng được đào và xác nhận.
  - *Trường hợp bị từ chối:* Người gọi không phải System Admin; phân công cho giảng viên đang bị ngừng hoạt động; mã lớp hoặc đợt học không tồn tại.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): System Admin nhập danh sách phân công cho đợt `2026-S1-P1` -> Thành công; phát event tổng chứa `batchId` và `recordCount`; bản ghi thể hiện đúng đợt học. Phân công bị thu hồi sau đó nhưng đề xuất chi đã tạo trước đó vẫn tiếp tục biểu quyết bình thường.
    - Không đạt (Fail): Gán phân công cho một địa chỉ ví chưa từng có trong danh mục giảng viên -> Bị từ chối (revert). Giảng viên bị thu hồi phân công cố tạo đề xuất mới -> Bị từ chối.

- **R0-C (Advisor quản lý danh sách Ban cán sự của lớp):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN**.
  - *Người thực hiện:* Advisor được phân công của đúng `classId` đó.
  - *Hành động:* Thêm hoặc xóa địa chỉ thành viên Ban cán sự (`Class Officer`) trong phạm vi lớp mình phụ trách.
  - *Điều kiện hợp lệ:* Người gọi là Advisor hợp lệ của đúng `classId`; địa chỉ officer hợp lệ (khác `address(0)`).
  - *Độc lập của quyền Advisor:* Quyền Cố vấn học tập được quản lý độc lập với phân công giảng dạy; khi một đợt học đóng (`CLOSED`), quyền Advisor của lớp **không tự mất đi**.
  - *Giới hạn quyền hạn Advisor:* Advisor chỉ quản lý danh sách Class Officer của đúng lớp mình; không có quyền can thiệp lớp khác; không có quyền biểu quyết hay thực thi thu - chi quỹ lớp. Nếu Advisor muốn tạo yêu cầu chi, quyền tạo được xét độc lập theo quy tắc R2.
  - *Trường hợp bị từ chối:* Người gọi không phải Advisor của lớp tương ứng; thêm địa chỉ `address(0)`.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Advisor của lớp 1 thêm ví X làm Class Officer lớp 1 -> X trở thành Class Officer lớp 1.
    - Không đạt (Fail): Advisor của lớp 1 cố thêm Class Officer cho lớp 2 -> Bị từ chối (revert).

### B. Cấu trúc Năm học, Đợt học và Vòng đời Đợt học (`OPEN` / `CLOSED`)
- **R0-D (Cấu trúc Năm học và Thời lượng Đợt học 3 tháng theo lịch):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (quy tắc nhóm đã chốt chính thức).
  - *Cấu trúc chuẩn hóa:*
    - 1 năm học = 2 kỳ học (`S1`, `S2`).
    - 1 kỳ học = 2 đợt học (`P1`, `P2`).
    - Tổng cộng = 4 đợt học/năm: `<Năm>-S1-P1`, `<Năm>-S1-P2`, `<Năm>-S2-P1`, `<Năm>-S2-P2`.
  - *Thời lượng cố định 3 tháng theo lịch:*
    - Mỗi đợt học kéo dài đúng **3 tháng theo lịch dương lịch**; không mặc định 3 tháng là 90 ngày cố định (vì các tháng có số ngày thực tế khác nhau: 28, 29, 30 hoặc 31 ngày).
    - Mốc thời gian được quy ước chặt chẽ: `startTime` là 00:00:00 ngày đầu tháng và `endTime` là 23:59:59 ngày cuối cùng của tháng thứ 3 theo lịch dương lịch, bảo đảm các đợt học kế tiếp nhau không bị chồng lấn và không có khoảng trống bỏ sót.
    - Ví dụ phân bổ chuẩn theo lịch:
      - Đợt 1 (`2026-S1-P1`): Từ 00:00:00 ngày 01/01/2026 đến 23:59:59 ngày 31/03/2026 (90 ngày).
      - Đợt 2 (`2026-S1-P2`): Từ 00:00:00 ngày 01/04/2026 đến 23:59:59 ngày 30/06/2026 (91 ngày).
      - Đợt 3 (`2026-S2-P1`): Từ 00:00:00 ngày 01/07/2026 đến 23:59:59 ngày 30/09/2026 (92 ngày).
      - Đợt 4 (`2026-S2-P2`): Từ 00:00:00 ngày 01/10/2026 đến 23:59:59 ngày 31/12/2026 (92 ngày).
  - *Quản lý trạng thái đợt học:* Mỗi đợt học có trạng thái `OPEN` hoặc `CLOSED`.
  - *Phân biệt mốc thời gian và trạng thái lưu trữ trên Blockchain:*
    - Do blockchain là máy trạng thái bị động (passive state machine), smart contract tuyệt đối **không tự động thực thi hay tự động đổi trạng thái** khi mốc thời gian `endTime` trôi qua mà bắt buộc phải có một giao dịch kích hoạt hợp lệ.
    - Cần phân biệt rõ 3 khái niệm độc lập:
      1. *Thời điểm kết thúc theo lịch (`endTime`):* Mốc thời gian Unix timestamp tương ứng với 23:59:59 của ngày cuối cùng của tháng thứ ba trong đợt học (được lưu cố định khi khởi tạo đợt học).
      2. *Điều kiện hiệu lực của phân công giảng dạy:*
         - Trong đợt học (`block.timestamp <= endTime`): Giảng viên có phân công hợp lệ được phép tạo yêu cầu chi (nếu đợt học đang `OPEN`).
         - Hết hiệu lực (`block.timestamp > endTime`): Ngay khi thời gian block vượt quá `endTime` (bắt đầu từ 00:00:00 ngày đầu tiên của đợt tiếp theo), mọi giao dịch tạo yêu cầu chi mới dựa trên phân công của đợt này đều bị từ chối ngay lập tức tại hàm gọi, kể cả khi trạng thái lưu trữ của đợt học trên hợp đồng vẫn đang là `OPEN`.
      3. *Trạng thái lưu trữ `CLOSED`:* Chỉ chính thức chuyển sang `CLOSED` khi có một giao dịch gọi hàm đóng đợt học `closePhase(phaseId)` được thực thi thành công trên blockchain.
  - *Cơ chế đóng đợt học và quyền hạn:*
    - Cung cấp hàm `closePhase(phaseId)`.
    - **Quyền gọi hàm:** Duy nhất System Admin được phép gọi hàm đóng đợt học. Giao diện và smart contract áp dụng modifier kiểm tra quyền của System Admin.
    - **Điều kiện thời gian bắt buộc:** Giao dịch chỉ hợp lệ khi `block.timestamp > endTime`.
      - Tại thời điểm `block.timestamp == endTime`, không được đóng đợt học; giao dịch bị revert ngay lập tức nhằm bảo đảm đợt học kết thúc trọn vẹn từng giây theo lịch dương.
      - Trạng thái chỉ chính thức chuyển sang `CLOSED` khi giao dịch được đào và thực thi thành công trên blockchain; đồng thời hợp đồng ghi nhận mốc thời gian đóng thực tế: `closedAt = block.timestamp`.
  - *Hạn thực hiện đề xuất chi đã được duyệt sau khi đóng đợt học (Execution Deadline):*
    - Đặt hạn chót thực hiện giải ngân cố định là **5 ngày sau khi đợt học thực sự được đóng**.
    - **Công thức xác định:**
      $$\text{executionDeadline} = \text{closedAt} + 5\text{ ngày (432,000 giây)}$$
    - **Điều kiện thời gian cho phép thực hiện chi:**
      - Khi đợt học căn cứ còn đang mở (`phase.status == OPEN`): Không áp dụng thời hạn 5 ngày (`closedAt` chưa phát sinh, mặc định bằng 0); các đề xuất đã đạt `Ready` được thực hiện chi bất kỳ lúc nào nếu đáp ứng đủ số dư quỹ và quyền của Class Officer; tuyệt đối không bị đánh dấu thành `Expired`.
      - Sau khi đợt học đã chuyển sang `CLOSED`: Đề xuất `Ready` chỉ được phép thực hiện chi nếu thời gian hiện tại chưa vượt quá hạn chót 5 ngày:
        $$\text{block.timestamp} \le \text{closedAt} + 5\text{ days}$$
        Tại đúng mốc thời gian biên $\text{block.timestamp} == \text{closedAt} + 5\text{ days}$, đề xuất `Ready` vẫn được phép giải ngân thành công.
    - **Hành vi khi đã quá hạn sau khi đóng đợt học (`phase.status == CLOSED` VÀ `block.timestamp > closedAt + 5 days`):**
      - Tuyệt đối không cho phép thực hiện chi (chuyển ETH) sau deadline.
      - **Cơ chế chuyển trạng thái lười (Lazy State Transition):** Do smart contract không tự thay đổi trạng thái theo thời gian nếu không có giao dịch gọi vào, trạng thái được cập nhật qua 2 kênh:
        1. *Kênh thực hiện chi (`executeProposal`):* Khi Class Officer gọi hàm thực hiện chi cho một đề xuất `Ready` mà đợt học đã đóng và thời gian đã quá hạn (`phase.status == CLOSED && block.timestamp > closedAt + 5 days`), smart contract xác định đề xuất đã quá hạn, không chuyển ETH, cập nhật trạng thái đề xuất sang `Expired`, và phát sự kiện `ProposalExpired` trong cùng giao dịch đó (giao dịch thành công, không revert, nhằm lưu trạng thái `Expired` on-chain). Nếu một giao dịch sau đó lại gọi `executeProposal` khi đề xuất đã ở trạng thái `Expired`, giao dịch đó bị revert do trạng thái không phải `Ready`.
        2. *Kênh công khai (`expireProposal`):* Cho phép bất kỳ ai (public) gọi hàm `expireProposal(classId, proposalId)` khi đợt học đã đóng và thời gian đã vượt quá `closedAt + 5 days` (`phase.status == CLOSED && block.timestamp > closedAt + 5 days`) và đề xuất đang lưu ở `Ready`. Hàm cập nhật trạng thái đề xuất sang `Expired` và phát sự kiện `ProposalExpired`, không chuyển ETH. Nếu đợt học còn `OPEN`, hàm bị revert do chưa đóng đợt học.
    - **Trạng thái đề xuất và số tiền chưa chi:**
      - Đề xuất `Ready` khi quá hạn giải ngân chuyển sang trạng thái lưu trữ chính thức là `Expired`.
      - Số tiền dự kiến chi không bị trừ khỏi quỹ lớp, vẫn nằm nguyên vẹn trong số dư quỹ của lớp.
      - Lịch sử đề xuất, danh sách phiếu và các sự kiện phê duyệt trước đó được bảo toàn đầy đủ để kiểm tra, đối soát.
      - Đề xuất đã `Executed` trước hạn tuyệt đối không bị chuyển ngược trạng thái.
      - Đề xuất `Pending` vẫn tuân theo hạn biểu quyết 7 ngày độc lập, không bị áp dụng nhầm hạn giải ngân 5 ngày.
  - *Hệ quả khi đợt học chuyển sang `CLOSED`:*
    1. **Chặn tạo mới:** Không cho phép tạo yêu cầu chi mới dựa trên căn cứ quyền giảng dạy thuộc đợt học đã đóng.
    2. **Bảo tồn dữ liệu:** Không xóa danh sách phân công giảng dạy cũ; giữ nguyên để phục vụ tra cứu, kiểm tra và truy vết lịch sử.
    3. **Không ảnh hưởng yêu cầu cũ:** Không tự động hủy bỏ, không xóa và không tự động chuyển trạng thái của các yêu cầu chi đã tạo hợp lệ trước khi đợt học đóng sang `Failed`.
    4. **Duy trì quyền Advisor:** Không làm mất quyền Cố vấn học tập của các lớp.
    5. **Độc lập đợt mới:** Đợt học mới được tạo với mã định danh riêng (`phaseId` mới); không bắt buộc xóa dữ liệu đợt cũ.
  - *Quy tắc vận hành yêu cầu chi đã tạo hợp lệ trước khi đóng đợt:*
    - Yêu cầu chi đã được tạo hợp lệ trước khi đợt học đóng vẫn tiếp tục chu trình biểu quyết và giải ngân bình thường theo quy tắc độc lập của yêu cầu chi.
    - Nếu yêu cầu đang `Pending`: Tiếp tục nhận phiếu trong hạn 7 ngày kể từ lúc tạo yêu cầu.
    - Nếu đạt >= 51% đúng hạn: Chuyển sang `Ready` bình thường.
    - Nếu hết 7 ngày không đạt: Chuyển sang `Failed`.
    - Nếu đã `Ready`: Được phép giải ngân trong hạn 5 ngày kể từ thời điểm đợt học đóng (`block.timestamp <= closedAt + 5 days`).
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Đợt `2026-S1-P1` có `endTime` là 23:59:59 ngày 31/03/2026. Sang ngày 01/04/2026 (`block.timestamp > endTime`), System Admin gọi `closePhase` thành công đổi trạng thái sang `CLOSED`, lưu `closedAt`.
    - Đạt (Pass): Đề xuất đạt `Ready` trước khi đợt đóng; sau khi đợt đóng 2 ngày (`block.timestamp <= closedAt + 5 days`), Class Officer gọi giải ngân thành công. Đề xuất `Ready` tại đúng thời điểm biên `block.timestamp == closedAt + 5 days` vẫn giải ngân thành công.
    - Đạt (Pass): Người dùng bất kỳ gọi `expireProposal` khi đề xuất `Ready` đã quá hạn 5 ngày (`block.timestamp > closedAt + 5 days`) -> Thành công chuyển sang `Expired`, không chuyển tiền, quỹ giữ nguyên số dư.
    - Không đạt (Fail): System Admin gọi `closePhase` tại đúng `block.timestamp == endTime` -> Bị từ chối (revert).
    - Không đạt (Fail): Người không phải System Admin gọi `closePhase` -> Bị từ chối (revert).
    - Không đạt (Fail): Gọi giải ngân đề xuất khi đề xuất đã ở trạng thái `Expired` -> Bị từ chối (revert do không ở trạng thái `Ready`). Khi gọi giải ngân lần đầu sau deadline (`block.timestamp > closedAt + 5 days`), smart contract không chuyển ETH mà cập nhật trạng thái sang `Expired` và phát sự kiện `ProposalExpired`.

### C. Nạp tiền vào quỹ lớp
- **R1 (Nạp tiền vào quỹ lớp hợp lệ và Cơ chế xử lý hoàn tiền đóng nhầm):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** về nguyên tắc phân định đóng góp hợp lệ và xem xét hoàn tiền đóng nhầm; **[CẦN NHÓM XÁC NHẬN]** về phương án kỹ thuật triển khai (Phương án A vs Phương án B) và vai trò phê duyệt cụ thể.
  - *Người thực hiện:* Bất kỳ ai sở hữu ví kết nối hợp lệ.
  - *Hành động:* Gửi ETH vào quỹ của một lớp cụ thể (`classId`).
  - *Điều kiện nạp tiền hợp lệ:* `classId` tồn tại hợp lệ; giá trị ETH gửi kèm > 0.
  - *Trạng thái trước/sau khi nạp:* Số dư quỹ của riêng lớp `classId` tăng đúng bằng số tiền nạp; phát sự kiện `DepositReceived(classId, msg.sender, msg.value, block.timestamp)`.
  - *Phân định hai trường hợp dòng tiền nạp vào:*
    1. **Trường hợp 1 — Đóng góp hợp lệ:** Người dùng chủ động nạp đúng lớp, đúng số tiền và đúng mục đích đóng góp cho quỹ lớp. Khoản đóng góp này trở thành tài sản chung của tập thể lớp và **không được hoàn trả theo yêu cầu rút tiền cá nhân thông thường**. Tiền quỹ này chỉ được chi ra thông qua đề xuất chi hợp lệ được Ban cán sự biểu quyết thông qua (R2–R6).
    2. **Trường hợp 2 — Đóng nhầm (Mistaken Deposit):** Người dùng nạp tiền do nhầm lẫn (chọn sai `classId`, nhập sai số tiền, hoặc lỗi giao dịch ngoại ý). Trường hợp này **được phép gửi yêu cầu xem xét hoàn tiền** theo quy trình thẩm định độc lập.
  - *10 nguyên tắc đặc tả quy trình xử lý hoàn tiền do đóng nhầm:*
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
  - *Phương án kỹ thuật đang đề xuất thảo luận:*
    - *Phương án A (Chức năng hoàn tiền chuyên biệt):* Thiết kế hàm `requestRefund(uint256 classId, bytes32 txHash, uint256 amount, string reason)` và hàm phê duyệt `approveAndExecuteRefund` có xác thực đa chữ ký giữa Cố vấn học tập và đại diện Ban cán sự.
    - *Phương án B (Quy trình đề xuất hoàn tiền riêng biệt):* Tạo đề xuất đặc thù `ProposalType.Refund` đưa vào quy trình biểu quyết tập thể của Ban cán sự với ngưỡng phê duyệt 51%.
    - `[CẦN NHÓM XÁC NHẬN]` phương án được lựa chọn trước khi triển khai code ở Lab 9.
  - *Trạng thái triển khai và kiểm thử thực tế:*
    - Yêu cầu nghiệp vụ: **Đã đặc tả**.
    - Triển khai Smart Contract: **Chưa triển khai** (sẽ lập trình tại Lab 9/10).
    - Bộ kiểm thử tự động: **Chưa kiểm thử** (sẽ bổ sung bài test tự động tại Lab 11/13).
  - *Trường hợp bị từ chối:* `classId` không tồn tại; giá trị gửi vào bằng 0 ETH -> Bị từ chối (revert). Yêu cầu hoàn tiền cho khoản đóng góp hợp lệ -> Bị từ chối (revert). Yêu cầu hoàn tiền lần 2 cho cùng giao dịch gốc -> Bị từ chối (revert `AlreadyRefunded`). Yêu cầu hoàn tiền vượt quá số tiền nạp gốc -> Bị từ chối (revert `RefundAmountExceedsDeposit`). Admin cố rút tiền hoàn trực tiếp -> Bị từ chối (revert `Unauthorized`).
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Gửi 0.05 ETH vào lớp 1 -> Số dư lớp 1 tăng 0.05 ETH, số dư các lớp khác không đổi.
    - Không đạt (Fail): Gửi 0 ETH vào lớp 1 -> Bị từ chối (revert). Yêu cầu hoàn tiền khi chưa được phê duyệt -> Bị từ chối (revert). Yêu cầu hoàn tiền lần 2 -> Bị từ chối (revert).

### D. Tạo yêu cầu chi của lớp
- **R2 (Điều kiện tạo yêu cầu chi hợp lệ):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (quy tắc nhóm đã chốt).
  - *Người thực hiện:* Thành viên Ban cán sự (Class Officer) của lớp HOẶC Giảng viên (Lecturer) thỏa mãn căn cứ hợp lệ.
  - *Quy trình kiểm tra quyền khi tạo yêu cầu chi:*
    1. Xác định mã lớp (`classId`) mà yêu cầu chi thuộc về.
    2. **Quy tắc bắt buộc về mã đợt học (`phaseId`):** Tất cả đề xuất, bất kể người tạo thuộc vai trò Lecturer, Advisor hay Class Officer, **đều bắt buộc phải gắn với một mã đợt học (`phaseId`) hợp lệ** thuộc đúng lớp liên kết với đề xuất. Không tạo ngoại lệ cho đề xuất do Advisor hoặc Class Officer tạo.
    3. **Nếu người tạo là Class Officer đương nhiệm của lớp:** Cho phép tạo yêu cầu chi trong phạm vi lớp mình phụ trách kèm mã đợt học `phaseId` hợp lệ thuộc lớp (`[CẦN NHÓM XÁC NHẬN]` về việc đợt học có bắt buộc phải đang ở trạng thái `OPEN` tại thời điểm tạo đề xuất hay không).
    4. **Nếu người tạo là Advisor của lớp:** Cho phép tạo yêu cầu chi trong phạm vi lớp mình cố vấn kèm mã đợt học `phaseId` hợp lệ thuộc lớp (`[CẦN NHÓM XÁC NHẬN]` về việc đợt học có bắt buộc phải đang ở trạng thái `OPEN` tại thời điểm tạo đề xuất hay không).
    5. **Nếu người tạo là Lecturer (không phải Advisor và không phải Class Officer):**
       - Bắt buộc cung cấp mã đợt học (`phaseId`) làm căn cứ.
       - Kiểm tra phân công giảng dạy: Người tạo phải có bản ghi phân công giảng dạy hợp lệ (`isValid == true`) cho đúng lớp `classId` trong đợt học `phaseId` đó.
       - Kiểm tra thời gian đợt học: Đợt học `phaseId` phải đang ở trạng thái `OPEN` VÀ thời gian hiện tại chưa vượt quá ngày kết thúc (`block.timestamp <= endTime`). Nếu đợt học đã `CLOSED` hoặc đã quá `endTime`, từ chối giao dịch ngay lập tức.
    6. Nếu không thỏa mãn bất kỳ căn cứ nào trong các trường hợp trên -> Từ chối giao dịch ngay lập tức.
  - *Điều kiện dữ liệu hợp lệ:*
    - Cung cấp đầy đủ mục đích chi (chuỗi ký tự không rỗng).
    - Số tiền yêu cầu chi > 0.
    - Địa chỉ người nhận hợp lệ (khác `address(0)` và khác địa chỉ hợp đồng quỹ).
    - Mã đợt học `phaseId` không được để trống và phải tồn tại hợp lệ trên hệ thống.
    - **Điều kiện cử tri biểu quyết của lớp (ĐÃ ĐƯỢC NHÓM XÁC NHẬN CHÍNH THỨC):**
      - Bắt buộc lớp phải có ít nhất 1 thành viên Ban cán sự đủ điều kiện biểu quyết (`eligibleVoterCount >= 1`).
      - Nếu lớp chưa có thành viên Ban cán sự nào (`eligibleVoterCount == 0`), mọi giao dịch tạo yêu cầu chi bị từ chối triệt để (revert) với lỗi không có cử tri đủ điều kiện (`NoEligibleVoters`). Quy tắc này áp dụng bình đẳng cho mọi đối tượng tạo đề xuất: Class Officer, Advisor và Lecturer.
      - Tại thời điểm tạo đề xuất thành công, hệ thống chốt số lượng cử tri đủ điều kiện (`eligibleVoterCount >= 1`) và danh sách Ban cán sự đủ điều kiện của lớp. Sự thay đổi nhân sự Ban cán sự sau đó không làm thay đổi mẫu số đã chốt.
      - Bắt buộc áp dụng ngưỡng phê duyệt tối thiểu `minimumApprovals >= 1` (xem chi tiết quy tắc R3).
  - *Lưu trữ thông tin truy vết:* Mỗi yêu cầu chi lưu trữ đầy đủ mã lớp (`classId`), địa chỉ người tạo, căn cứ tạo (Class Officer, Advisor, hoặc Lecturer), mã đợt học (`phaseId`), mục đích, số tiền, người nhận, thời điểm tạo, số lượng cử tri chốt (`eligibleVoterCount`), ngưỡng tối thiểu (`minimumApprovals`), và hạn chót bỏ phiếu.
  - *Phân biệt quyền hạn:* Giảng viên tạo yêu cầu chi **tuyệt đối không có quyền bỏ phiếu hay thực hiện chi**. Người tạo yêu cầu không mặc nhiên có quyền thực thi.
  - *Trạng thái trước/sau:* Hệ thống sinh Proposal ID mới cho lớp `classId`, lưu trữ thông tin yêu cầu, khởi tạo số lượt chấp thuận bằng 0, gán mốc thời gian tạo, đặt trạng thái ban đầu là `Pending`; phát sự kiện tạo yêu cầu chi.
  - *Trường hợp bị từ chối:* Người gọi không thuộc Class Officer và không thỏa mãn điều kiện Lecturer/Advisor của lớp; lớp chưa có Ban cán sự (`eligibleVoterCount == 0`); để trống `phaseId` hoặc `phaseId` không tồn tại; đợt học căn cứ đã `CLOSED` hoặc quá `endTime` (khi tạo theo tư cách giảng dạy); số tiền <= 0; mục đích rỗng; người nhận không hợp lệ.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Lecturer dạy đợt học `2026-S1-P1` đang `OPEN` tạo yêu cầu chi kèm `phaseId = 2026-S1-P1` cho lớp có ít nhất 1 Class Officer -> Thành công.
    - Đạt (Pass): Advisor của lớp tạo yêu cầu chi cho lớp mình kèm `phaseId = 2026-S1-P1` khi lớp có ít nhất 1 Class Officer -> Thành công.
    - Đạt (Pass): Class Officer tạo yêu cầu chi cho lớp mình kèm `phaseId = 2026-S1-P1` khi lớp có ít nhất 1 Class Officer -> Thành công.
    - Đạt (Pass): Sau khi Advisor bổ sung Ban cán sự và lớp có ít nhất 1 cử tri, việc tạo đề xuất mới được chấp nhận thành công.
    - Không đạt (Fail): Tạo đề xuất chi khi để trống `phaseId` -> Bị từ chối (revert với `EmptyPhaseId`).
    - Không đạt (Fail): Tạo đề xuất chi khi lớp chưa có Ban cán sự (`eligibleVoterCount == 0`), kể cả do Advisor hay Lecturer tạo -> Bị từ chối (revert).
    - Không đạt (Fail): Lecturer dùng phân công thuộc đợt học đã `CLOSED` làm căn cứ duy nhất để tạo yêu cầu chi -> Bị từ chối (revert).

### E. Biểu quyết và Ngưỡng chấp thuận 51%
- **R3 (Quyền biểu quyết, Chốt danh sách cử tri và Ngưỡng chấp thuận):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (quy tắc nhóm đã chốt chính thức).
  - *Người thực hiện:* Thành viên Ban cán sự (Class Officer) đủ điều kiện biểu quyết của đúng lớp đó theo danh sách chốt tại thời điểm tạo yêu cầu chi. Giảng viên, Advisor, System Admin và người ngoài không có quyền bỏ phiếu.
  - *Quy tắc chốt danh sách cử tri và tính ngưỡng chấp thuận:*
    - **Chốt số lượng Ban cán sự tại thời điểm tạo yêu cầu (Q1):** Khi một yêu cầu chi được tạo, hệ thống chốt số lượng Ban cán sự đủ điều kiện biểu quyết (`eligibleVoterCount`) và danh sách cử tri đủ điều kiện của lớp tại thời điểm tạo yêu cầu. Sự thay đổi số lượng Ban cán sự sau đó (Advisor thêm hoặc xóa thành viên) **không làm thay đổi mẫu số đã chốt** của yêu cầu đó.
    - **Công thức xác định số phiếu tối thiểu cần đạt (`minimumApprovals`):**
      $$\text{minimumApprovals} = \max\left(1, \lceil 0.51 \times \text{eligibleVoterCount} \rceil\right)$$
    - **Ràng buộc ngưỡng tối thiểu khi lớp có cử tri hợp lệ (Q10 & Quyết định 3):**
      - Bắt buộc `minimumApprovals >= 1`. Một đề xuất tuyệt đối không được chuyển sang `Ready` nếu chưa có ít nhất một phiếu tán thành hợp lệ và đạt ngưỡng yêu cầu.
      - Tuyệt đối không để ngưỡng biểu quyết bằng 0 hoặc để đề xuất tự động đạt `Ready` khi chưa có phiếu tán thành.
      - Do quy tắc R2 bắt buộc lớp phải có ít nhất 1 Class Officer đủ điều kiện mới được tạo đề xuất (`eligibleVoterCount >= 1`), mọi đề xuất tồn tại trên hệ thống đều có mẫu số chốt $\ge 1$ và ngưỡng phê duyệt tối thiểu luôn bảo đảm $\ge 1$. Không còn tình huống tạo đề xuất khi lớp có 0 cử tri (`eligibleVoterCount == 0`).
    - **Bảo toàn lá phiếu đã bỏ và Kiểm tra kép quyền biểu quyết (Q2 & Quyết định QA-04 Phương án A):**
      - Lá phiếu hợp lệ đã được bỏ vẫn được giữ nguyên tính hiệu lực, kể cả khi người bỏ phiếu sau đó bị xóa khỏi danh sách Ban cán sự của lớp. Việc xóa thành viên không được xóa hoặc vô hiệu hóa lá phiếu hợp lệ đã ghi nhận.
      - **Cơ chế Kiểm tra kép (Phương án A đã được nhóm xác nhận):** Để một thành viên bỏ phiếu hợp lệ cho một đề xuất, thành viên đó bắt buộc phải đồng thời thỏa mãn 2 điều kiện tại thời điểm gửi giao dịch bỏ phiếu:
        1. Có tên trong danh sách cử tri chốt (`isSnapshotVoter == true`) tại thời điểm tạo đề xuất đó.
        2. Hiện tại vẫn đang giữ vai trò Ban cán sự hợp lệ của lớp (`isClassOfficer == true`) tại thời điểm gọi giao dịch.
      - Nếu một thành viên Ban cán sự bị xóa khỏi lớp sau khi đề xuất được tạo, thành viên đó không còn được phép bỏ phiếu cho đề xuất cũ nữa (giao dịch bị revert). Mẫu số `eligibleVoterCount` và ngưỡng phê duyệt `minimumApprovals` đã chốt của đề xuất vẫn giữ nguyên không đổi.
      - Tuyệt đối không cho phép một địa chỉ bỏ phiếu nhiều lần cho cùng một yêu cầu chi.
    - **Cơ chế bỏ phiếu (Approval-only voting):** Hệ thống chỉ ghi nhận lượt bỏ phiếu đồng ý/chấp thuận (`voteApprove`). Mỗi phiếu hợp lệ được tính là 1 phiếu chấp thuận. Hệ thống phiên bản v0.1 **không hỗ trợ** các lựa chọn phiếu chống (Against/Reject) hay phiếu trắng (Abstain); mọi hành vi không đồng ý được thể hiện bằng việc không bỏ phiếu chấp thuận.
    - **Bảng ví dụ minh họa tính toán ngưỡng:**
      | Tổng số Class Officer chốt lúc tạo (`eligibleVoterCount`) | 0.51 × Tổng số | Ngưỡng làm tròn trần $\max(1, \lceil 0.51 \times N \rceil)$ |
      |---|---|---|
      | 1 thành viên | 0.51 | **1 phiếu** (100%) |
      | 2 thành viên | 1.02 | **2 phiếu** (100%) |
      | 3 thành viên | 1.53 | **2 phiếu** (66.7%) |
      | 4 thành viên | 2.04 | **3 phiếu** (75%) |
      | 5 thành viên | 2.55 | **3 phiếu** (60%) |
      | 7 thành viên | 3.57 | **4 phiếu** (57.1%) |
      | 10 thành viên | 5.10 | **6 phiếu** (60%) |
  - *Điều kiện hợp lệ khi bỏ phiếu:*
    - Người gửi là cử tri đủ điều kiện theo danh sách chốt tại thời điểm tạo yêu cầu chi của đúng `classId`.
    - Yêu cầu chi tồn tại, đang ở trạng thái lưu trữ `Pending`.
    - Người gửi chưa từng bỏ phiếu cho yêu cầu chi này (chưa có trong danh sách đã bỏ phiếu).
    - Thời gian hiện tại chưa vượt quá thời hạn 7 ngày kể từ khi tạo (`block.timestamp <= creationTime + 7 days`).
  - *Chuyển trạng thái khi đạt ngưỡng:* Khi số phiếu chấp thuận đạt đủ ngưỡng `minimumApprovals` trong thời hạn 7 ngày, trạng thái của yêu cầu chi chuyển ngay từ `Pending` sang `Ready`; phát sự kiện yêu cầu đã đủ điều kiện thực hiện.
  - *Trường hợp bị từ chối:* Người gọi không thuộc danh sách cử tri chốt của yêu cầu chi; yêu cầu không ở `Pending`; yêu cầu đã hết hạn 7 ngày; người gọi đã bỏ phiếu trước đó; yêu cầu đã ở `Ready`, `Executed`, `Failed`, `Cancelled` hoặc `Expired`.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Lớp có 5 Class Officer chốt lúc tạo đề xuất. Advisor xóa 1 Class Officer chưa bỏ phiếu (lớp còn 4). Đề xuất vẫn yêu cầu 3 phiếu (`minimumApprovals = 3`). Khi nhận đủ 3 phiếu từ các cử tri hợp lệ -> Chuyển sang `Ready`.
    - Không đạt (Fail): Class Officer bị xóa sau khi tạo đề xuất cố tình gọi bỏ phiếu cho đề xuất cũ đó -> Bị từ chối (revert do vi phạm điều kiện cán sự hiện hành theo Phương án A).
    - Đạt (Pass): Lớp có đúng 1 Class Officer (`eligibleVoterCount = 1`), ngưỡng yêu cầu 1 phiếu (`minimumApprovals = 1`). Class Officer bỏ phiếu tán thành -> Chuyển sang `Ready`.
    - Không đạt (Fail): Thành viên không thuộc danh sách cử tri chốt cố tình bỏ phiếu -> Bị từ chối (revert).

- **R4 (Ngăn chặn bỏ phiếu trùng lặp):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (nguyên tắc an toàn cốt lõi).
  - *Người thực hiện:* Class Officer của lớp.
  - *Hành động:* Bỏ phiếu cho một yêu cầu chi.
  - *Điều kiện hợp lệ:* Mỗi địa chỉ ví Class Officer chỉ được bỏ phiếu tối đa 1 lần cho mỗi yêu cầu chi.
  - *Trường hợp bị từ chối:* Cùng một địa chỉ ví gửi bỏ phiếu lần thứ hai trở đi cho cùng một Proposal ID -> Bị từ chối ngay lập tức, số phiếu không tăng.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Class Officer A bỏ phiếu lần 1 -> Thành công.
    - Không đạt (Fail): Class Officer A bỏ phiếu lần 2 cho cùng một Proposal -> Bị từ chối (revert).

- **R5 (Người tạo yêu cầu được tự bỏ phiếu nếu là Ban cán sự):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (quy tắc nhóm đã chốt).
  - *Quy tắc:* Nếu người tạo yêu cầu chi đồng thời là thành viên Ban cán sự (Class Officer) của lớp, người đó **hoàn toàn có quyền bỏ phiếu** cho yêu cầu do chính mình tạo.
  - *Tính toán phiếu:* Phiếu của người tạo được tính bình thường như phiếu của các thành viên khác vào tổng số phiếu chấp thuận (1 phiếu).
  - *Ràng buộc:* Vẫn phải tuân thủ nghiêm ngặt quy tắc R4 (chỉ được bỏ phiếu duy nhất 1 lần).
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Class Officer A tạo đề xuất, sau đó gọi hàm bỏ phiếu cho đề xuất này -> Phiếu được chấp nhận, số phiếu tăng 1.
    - Không đạt (Fail): Class Officer A cố bỏ phiếu thêm lần nữa -> Bị từ chối.

### F. Thực hiện yêu cầu chi
- **R6 (Thực hiện chi tiền và giải ngân theo lớp):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN**.
  - *Người thực hiện:* Class Officer đương nhiệm của đúng `classId` đó (Giảng viên, Advisor, System Admin không có quyền gọi hàm này).
  - *Hành động:* Kích hoạt chuyển ETH từ quỹ của lớp tới địa chỉ người nhận.
  - *Điều kiện hợp lệ:*
    - Người gọi là Class Officer hợp lệ của đúng `classId`.
    - Yêu cầu chi đang ở đúng trạng thái `Ready` (đã đạt ngưỡng tối thiểu trước hạn 7 ngày).
    - Số dư quỹ của riêng lớp `classId` tại thời điểm thực hiện phải lớn hơn hoặc bằng (>=) số tiền yêu cầu chi (không được chi vượt số dư quỹ của lớp).
    - **Điều kiện thời hạn thực hiện (Execution Deadline - Q7 & Quyết định 2):**
      - Nếu đợt học căn cứ đang mở (`phase.status == OPEN`): Không áp dụng thời hạn 5 ngày (do `closedAt` chưa phát sinh, mặc định bằng 0); đề xuất `Ready` được phép giải ngân bất kỳ lúc nào nếu đáp ứng đủ các điều kiện khác, tuyệt đối không bị coi là `Expired`.
      - Nếu đợt học căn cứ đã chuyển sang `CLOSED`: Được phép thực hiện giải ngân nếu thời gian hiện tại chưa vượt quá 5 ngày kể từ thời điểm đóng đợt học thực tế (`block.timestamp <= closedAt + 5 days`). Tại mốc thời gian biên đúng `block.timestamp == closedAt + 5 days`, đề xuất vẫn được phép giải ngân thành công.
      - Khi đợt học đã đóng và đã quá 5 ngày (`phase.status == CLOSED` VÀ `block.timestamp > closedAt + 5 days`):
        - Tuyệt đối không chuyển ETH sau hạn.
        - **Cơ chế Lazy State Transition trong `executeProposal`:** Khi Class Officer gọi giải ngân lần đầu sau hạn chót, smart contract không chuyển ETH mà cập nhật trạng thái đề xuất từ `Ready` sang `Expired` và phát sự kiện `ProposalExpired(classId, proposalId, block.timestamp)`. Giao dịch này kết thúc thành công để ghi nhận trạng thái on-chain, không revert.
        - Nếu đề xuất đã ở trạng thái `Expired`, mọi lệnh gọi `executeProposal` tiếp theo bị từ chối triệt để (revert).
        - Số tiền chưa chi không bị trừ khỏi quỹ lớp, vẫn nằm nguyên vẹn trong số dư quỹ của lớp; dữ liệu lịch sử và sự kiện được giữ nguyên để đối soát.
  - *Trạng thái trước/sau và an toàn giao dịch (Checks-Effects-Interactions & An toàn chuyển ETH):*
    - Hợp đồng áp dụng mẫu thiết kế Checks-Effects-Interactions để phòng chống tấn công tái nhập (Reentrancy).
    - Chuyển số tiền tương ứng từ hợp đồng quỹ tới người nhận bằng `call{value: ...}("")`. Nếu giao dịch chuyển ETH thất bại (ví dụ địa chỉ người nhận là hợp đồng từ chối nhận ETH hoặc lỗi thực thi):
      - Toàn bộ giao dịch `executeProposal` **bắt buộc phải revert** ngay lập tức.
      - Trạng thái lưu trữ của đề xuất **giữ nguyên là `Ready`** (không chuyển sang `Executed`, không chuyển sang `Failed`/`Expired`).
      - Số dư quỹ lớp không bị giảm, không bị thất thoát ETH.
      - Người quản lý có thể thử lại giao dịch giải ngân hoặc xử lý trước khi thời hạn kết thúc.
    - Chỉ sau khi việc chuyển ETH thành công, trạng thái yêu cầu mới chính thức chuyển sang `Executed`, số dư quỹ của lớp `classId` giảm tương ứng, và phát sự kiện `ProposalExecuted(classId, proposalId, msg.sender, recipient, amount, block.timestamp)`. Tuyệt đối không thể giải ngân 2 lần cho cùng một đề xuất.
  - *Trường hợp bị từ chối:* Người gọi không phải Class Officer của lớp; yêu cầu không ở trạng thái `Ready` (đang `Pending`, `Failed`, `Cancelled`, `Expired` hoặc đã `Executed`); số dư quỹ lớp không đủ; giao dịch chuyển ETH tới người nhận thất bại -> Revert toàn bộ.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Đề xuất `Ready` trong đợt học còn `OPEN` được Class Officer giải ngân thành công.
    - Đạt (Pass): Quỹ lớp 1 có 1.0 ETH, yêu cầu chi 0.2 ETH ở trạng thái `Ready` được Class Officer lớp 1 thực hiện trong hạn (đợt `CLOSED` chưa quá 5 ngày) -> Người nhận nhận được 0.2 ETH, quỹ lớp 1 còn 0.8 ETH, trạng thái thành `Executed`.
    - Đạt (Pass): Đề xuất `Ready` tại đúng mốc `block.timestamp == closedAt + 5 days` vẫn được giải ngân thành công.
    - Đạt (Pass): Gọi giải ngân lần đầu khi đợt học đã `CLOSED` và quá hạn 1 giây (`block.timestamp = closedAt + 5 days + 1 second`) -> Không chuyển ETH, trạng thái chuyển sang `Expired`, quỹ lớp giữ nguyên số dư, phát event `ProposalExpired`.
    - Không đạt (Fail): Giảng viên hoặc người ngoài gọi hàm thực hiện chi -> Bị từ chối (revert).
    - Không đạt (Fail): Gọi giải ngân khi đề xuất đã ở trạng thái `Expired` -> Bị từ chối (revert).
    - Không đạt (Fail): Địa chỉ người nhận từ chối nhận ETH -> Giao dịch revert, trạng thái giữ nguyên `Ready`, số dư quỹ giữ nguyên.

- **R7 (Tính đơn nhất của việc thực hiện chi - Chống chi trùng):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (nguyên tắc an toàn cốt lõi).
  - *Quy tắc:* Mỗi yêu cầu chi chỉ được thực hiện thành công duy nhất một lần.
  - *Trạng thái trước/sau:* Sau khi đã sang `Executed`, yêu cầu bị khóa vĩnh viễn.
  - *Trường hợp bị từ chối:* Bất kỳ nỗ lực gọi thực hiện lại nào đối với một yêu cầu đã ở trạng thái `Executed` đều bị từ chối triệt để.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Yêu cầu đã chuyển sang `Executed` từ chối mọi lệnh gọi giải ngân tiếp theo.
    - Không đạt (Fail): Hai lệnh thực thi gửi liên tiếp chỉ có lệnh đầu tiên thành công; lệnh thứ hai bị từ chối.

### G. Xử lý thời hạn 7 ngày và trạng thái `Failed`
- **R8 (Thời hạn bỏ phiếu 7 ngày, Chuyển trạng thái `Failed` và Hạn giải ngân):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (quy tắc nhóm đã chốt chính thức).
  - *Quy tắc thời hạn bỏ phiếu:* Thời hạn bỏ phiếu là đúng **7 ngày** (tương đương 604,800 giây kể từ thời điểm tạo yêu cầu chi). Việc đóng đợt học không làm thay đổi deadline bỏ phiếu của một yêu cầu đã tạo.
  - *Quy tắc xử lý quá hạn bỏ phiếu:* Nếu hết thời hạn 7 ngày mà yêu cầu **chưa đạt ngưỡng chấp thuận tối thiểu**, yêu cầu phải chuyển sang trạng thái `Failed`. Yêu cầu ở trạng thái `Failed` tuyệt đối không được tiếp tục bỏ phiếu hoặc thực hiện chi.
  - *Kiểm tra tự động trực tiếp:* Dù chưa có ai gọi hàm cập nhật trạng thái lưu trữ, các hàm bỏ phiếu (R3) và thực thi (R6) vẫn phải tự kiểm tra mốc thời gian deadline. Nếu đã qua 7 ngày mà chưa đạt `Ready`, giao dịch bỏ phiếu hoặc giải ngân bị từ chối ngay lập tức.
  - *Hàm cập nhật trạng thái công khai:* Cho phép bất kỳ người dùng nào gọi hàm công khai `failProposal(classId, proposalId)` (hoặc tương đương) để chuyển trạng thái lưu trữ của yêu cầu từ `Pending` sang `Failed` nếu thời gian hiện tại > thời điểm tạo + 7 ngày và chưa đạt `Ready`.
  - *Bảo toàn yêu cầu `Ready` và Ràng buộc hạn giải ngân (Q7 & Quyết định 2):*
    - Nếu yêu cầu đã đạt ngưỡng phê duyệt hợp lệ trước deadline 7 ngày và đã chuyển sang trạng thái `Ready`, yêu cầu **không tự động chuyển thành `Failed`** chỉ vì thời gian 7 ngày đã qua hoặc vì đợt học đã đóng.
    - Tuy nhiên, việc thực hiện giải ngân yêu cầu `Ready` sau khi đợt học đóng phải tuân thủ nghiêm ngặt **hạn chót thực thi (execution deadline = closedAt + 5 ngày)** theo quy tắc R0-D và R6.
    - Sau thời hạn 5 ngày này (`phase.status == CLOSED` VÀ `block.timestamp > closedAt + 5 days`), đề xuất `Ready` chuyển sang trạng thái `Expired` thông qua cơ chế Lazy State Transition:
      - Gọi hàm công khai `expireProposal(classId, proposalId)`: Bất kỳ người dùng nào cũng có thể gọi để chuyển trạng thái đề xuất `Ready` quá hạn sang `Expired`, phát sự kiện `ProposalExpired`, không chuyển ETH.
      - Gọi hàm `executeProposal`: Lần đầu gọi sau hạn chót khi đợt học đã đóng sẽ không chuyển ETH, cập nhật trạng thái sang `Expired`, phát sự kiện `ProposalExpired` mà không revert. Lệnh gọi tiếp theo khi đã ở `Expired` sẽ bị revert.
    - Số tiền chưa giải ngân nằm lại an toàn trong quỹ lớp và lịch sử yêu cầu được giữ nguyên để đối soát.
  - *Trường hợp bị từ chối:* Gọi hàm đánh dấu `Failed` khi chưa hết hạn 7 ngày, hoặc khi yêu cầu đã ở trạng thái `Ready` / `Executed` / `Cancelled` / `Expired`. Gọi hàm `expireProposal` khi đợt học còn `OPEN`, hoặc khi đợt học đã đóng nhưng chưa quá 5 ngày sau `closedAt`, hoặc khi đề xuất không ở trạng thái `Ready`.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Yêu cầu ở ngày thứ 8 mới nhận được 2/5 phiếu -> Gọi đánh dấu `Failed` thành công; các lệnh bỏ phiếu tiếp theo bị từ chối.
    - Đạt (Pass): Yêu cầu đã thành `Ready` ở ngày thứ 4 -> Sang ngày thứ 8 yêu cầu vẫn giữ nguyên `Ready` và giải ngân bình thường (nếu đợt học chưa đóng hoặc đợt học đóng chưa quá 5 ngày).
    - Đạt (Pass): Người dùng bất kỳ gọi `expireProposal` thành công cho đề xuất `Ready` đã quá hạn 5 ngày sau đóng đợt học.
    - Không đạt (Fail): Yêu cầu mới tạo được 3 ngày nhưng bị gọi chuyển sang `Failed` -> Bị từ chối do chưa hết hạn.
    - Không đạt (Fail): Gọi `expireProposal` khi đợt học chưa đóng hoặc chưa quá 5 ngày kể từ `closedAt` -> Bị từ chối (revert).

### H. Theo dõi số dư và lịch sử theo lớp
- **R9 (Minh bạch tra cứu thông tin theo từng lớp):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (nguyên tắc minh bạch).
  - *Người thực hiện:* Bất kỳ ai kết nối hệ thống.
  - *Hành động:* Đọc dữ liệu công khai từ smart contract gắn với từng `classId`.
  - *Kết quả khi hợp lệ:* Tra cứu chính xác số dư hiện tại của quỹ từng lớp, danh sách tất cả các yêu cầu chi theo lớp, thông tin Advisor, danh sách Class Officer đương nhiệm của lớp, danh sách phân công giảng dạy theo từng đợt học (`phaseId`), và lịch sử các giao dịch nạp/chi của lớp đó.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): Dữ liệu đọc ra khớp hoàn toàn với trạng thái lưu trữ và các sự kiện đã phát trên blockchain cho từng lớp riêng biệt.

### I. Hủy đề xuất của Giảng viên bị đình chỉ hoặc ngừng hoạt động
- **R10 (Quyền hủy đề xuất của Giảng viên bị đình chỉ - Quyết định 1):**
  - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN** (quy tắc nhóm đã chốt chính thức - Quyết định 1).
  - *Người thực hiện:* System Admin (trên toàn bộ các lớp) HOẶC Advisor của chính lớp đó (`classId`).
  - *Mục đích và bản chất:* Cơ chế khẩn cấp kiểm soát rủi ro bảo vệ ngân quỹ lớp khi một giảng viên tạo đề xuất bị System Admin ngừng hoạt động/đình chỉ (`isActive == false`) do kỷ luật hoặc thôi việc.
  - *Điều kiện kiểm tra chặt chẽ (Preconditions):*
    1. **Tư cách giảng viên:** Giảng viên tạo đề xuất (`proposal.proposer`) phải đang ở trạng thái ngừng hoạt động (`isActive == false`) trong danh mục giảng viên tại thời điểm gọi lệnh hủy. Nếu giảng viên vẫn đang hoạt động bình thường (`isActive == true`), giao dịch hủy bị từ chối triệt để (revert).
    2. **Đúng chủ thể tạo đề xuất:** Đề xuất phải do chính giảng viên bị đình chỉ đó tạo ra. Tuyệt đối không áp dụng hàm này để hủy tùy tiện đề xuất do Class Officer hoặc giảng viên khác tạo ra.
    3. **Phạm vi quyền hạn:**
       - System Admin có quyền hủy đề xuất của giảng viên bị đình chỉ trên bất kỳ lớp học nào trong hệ thống.
       - Advisor chỉ có quyền hủy đề xuất của giảng viên bị đình chỉ trong phạm vi lớp mà mình được phân công làm Cố vấn học tập. Mọi nỗ lực của Advisor nhằm hủy đề xuất ở lớp khác đều bị từ chối (revert).
    4. **Trạng thái đề xuất hợp lệ:** Đề xuất phải đang ở trạng thái `Pending` hoặc `Ready`.
    5. **Bất biến của đề xuất đã giải ngân:** Đề xuất đã ở trạng thái `Executed` **tuyệt đối không thể bị hủy** và không thể đảo ngược giao dịch chuyển ETH. Đề xuất đã kết thúc (`Failed`, `Cancelled`, `Expired`) cũng không thể hủy lại.
  - *Hành vi và Trạng thái sau giao dịch:*
    - Trạng thái đề xuất chuyển sang `Cancelled`.
    - Hợp đồng ghi nhận thông tin kiểm toán: địa chỉ người thực hiện hủy (`cancelledBy`), thời điểm hủy (`cancelledAt`), lý do hủy (`reason = LecturerSuspended`).
    - Hợp đồng phát sự kiện `ProposalCancelled(classId, proposalId, caller, lecturerAddress, reason)`.
    - Số dư quỹ lớp giữ nguyên vẹn 100%, không bị hao hụt.
    - Lịch sử đề xuất và danh sách các lá phiếu đã bỏ trước đó được bảo toàn nguyên vẹn phục vụ đối soát, kiểm toán.
  - *Điều kiện kiểm thử:*
    - Đạt (Pass): System Admin hủy thành công đề xuất `Pending`/`Ready` của Giảng viên đã bị đình chỉ (`isActive == false`).
    - Đạt (Pass): Advisor hủy thành công đề xuất của Giảng viên bị đình chỉ trong lớp mình cố vấn.
    - Không đạt (Fail): Advisor cố hủy đề xuất của Giảng viên bị đình chỉ ở lớp mình không làm cố vấn -> Bị từ chối (revert).
    - Không đạt (Fail): System Admin hoặc Advisor cố hủy đề xuất của Giảng viên vẫn đang hoạt động bình thường (`isActive == true`) -> Bị từ chối (revert).
    - Không đạt (Fail): Cố hủy đề xuất đã giải ngân (`Executed`) của Giảng viên bị đình chỉ -> Bị từ chối (revert).
    - Không đạt (Fail): Cố hủy đề xuất do Class Officer tạo ra -> Bị từ chối (revert).
    - Không đạt (Fail): Người không phải System Admin hoặc Advisor của lớp gọi lệnh hủy -> Bị từ chối (revert).

---

### Bảng tổng hợp phân quyền theo vai trò (Role Permission Matrix)

| Chức năng / Hành động | System Admin | Advisor (Cố vấn học tập) | Lecturer (Giảng viên) | Class Officer (Ban cán sự) | Sinh viên |
|---|:---:|:---:|:---:|:---:|:---:|
| Chuyển giao quyền quản trị (`Ownable2Step`) | **Có** (Đề xuất ví mới) | Không | Không | Không | Không |
| Chấp nhận quyền quản trị kế nhiệm | **Có** (Ví được đề xuất) | Không | Không | Không | Không |
| Quản lý danh mục Giảng viên (thêm/sửa/ngừng hoạt động) | **Có** | Không | Không | Không | Không |
| Quản lý cấu trúc đợt học & Phân công giảng dạy theo lô | **Có** | Không | Không | Không | Không |
| Khởi tạo / Mở đợt học mới (`OPEN`) | **Có** | Không | Không | Không | Không |
| Đóng đợt học (`closePhase` sang `CLOSED`) | **Có** (khi `block.timestamp > endTime`) | Không | Không | Không | Không |
| Phân công Cố vấn học tập cho lớp | **Có** | Không | Không | Không | Không |
| Quản lý Ban cán sự (thêm/xóa Class Officer) | Không | **Có** (trong lớp phụ trách) | Không | Không | Không |
| Tạo yêu cầu chi | Không | **Có** (trong lớp mình cố vấn; bắt buộc lớp có $\ge$ 1 Class Officer) | **Có** (trong lớp dạy khi đợt `OPEN` & còn hạn; bắt buộc lớp có $\ge$ 1 Class Officer) | **Có** (trong lớp của mình; bắt buộc lớp có $\ge$ 1 Class Officer) | Không |
| Bỏ phiếu chấp thuận yêu cầu chi | Không | Không | Không | **Có** (thuộc danh sách cử tri chốt của đề xuất) | Không |
| Tự bỏ phiếu nếu là người tạo đề xuất | Không | Không | Không | **Có** (nếu là Ban cán sự của lớp, tối đa 1 phiếu) | Không |
| Thực hiện chi tiền (giải ngân khi `Ready`) | Không | Không | Không | **Có** (trong lớp mình khi `Ready` & chưa quá hạn; quá hạn kích hoạt chuyển `Expired`) | Không |
| Hủy đề xuất của Giảng viên bị đình chỉ (`cancelSuspendedLecturerProposal`) | **Có** (mọi lớp; chỉ khi đề xuất do GV tạo đang `isActive == false` và ở `Pending/Ready`) | **Có** (trong lớp cố vấn; chỉ khi đề xuất do GV tạo đang `isActive == false` và ở `Pending/Ready`) | Không | Không | Không |
| Cập nhật đề xuất `Ready` quá hạn sang `Expired` (`expireProposal`) | **Có** (công khai khi `phase.status == CLOSED` & quá hạn `closedAt + 5 days`) | **Có** (công khai khi `phase.status == CLOSED` & quá hạn `closedAt + 5 days`) | **Có** (công khai khi `phase.status == CLOSED` & quá hạn `closedAt + 5 days`) | **Có** (công khai khi `phase.status == CLOSED` & quá hạn `closedAt + 5 days`) | **Có** (công khai khi `phase.status == CLOSED` & quá hạn `closedAt + 5 days`) |
| Rút tiền quỹ trực tiếp (không qua đề xuất `Ready`) | **Tuyệt đối Không** | **Tuyệt đối Không** | **Tuyệt đối Không** | **Tuyệt đối Không** | **Tuyệt đối Không** |
| Cập nhật yêu cầu quá hạn sang `Failed` (`failProposal`) | **Có** (công khai khi quá hạn 7 ngày & chưa `Ready`) | **Có** (công khai khi quá hạn 7 ngày & chưa `Ready`) | **Có** (công khai khi quá hạn 7 ngày & chưa `Ready`) | **Có** (công khai khi quá hạn 7 ngày & chưa `Ready`) | **Có** (công khai khi quá hạn 7 ngày & chưa `Ready`) |
| Nạp tiền vào quỹ lớp | **Có** | **Có** | **Có** | **Có** | **Có** |
| Tra cứu số dư và lịch sử giao dịch | **Có** | **Có** | **Có** | **Có** | **Có** |

*Ghi chú quan trọng về phân quyền:*
1. **System Admin:** Quản trị danh mục và cấu trúc dữ liệu đào tạo; chuyển giao quyền sở hữu qua quy trình 2 bước (`Ownable2Step`); có quyền hủy đề xuất của Giảng viên bị đình chỉ trên toàn hệ thống; **tuyệt đối không có quyền rút tiền quỹ lớp hoặc tự duyệt chi tiêu** chỉ vì đang giữ quyền quản trị.
2. **Advisor:** Có quyền tạo yêu cầu chi cho lớp mình cố vấn (bắt buộc lớp có ít nhất 1 Class Officer; quyền này độc lập với việc giảng dạy và không bị mất khi đợt học đóng); có quyền hủy đề xuất của Giảng viên bị đình chỉ trong phạm vi lớp mình cố vấn; **không mặc nhiên có quyền bỏ phiếu hay giải ngân**.
3. **Class Officer:** Chỉ có quyền thao tác tài chính, biểu quyết và giải ngân **trong phạm vi đúng lớp của mình** (`classId`), không có quyền thao tác trên lớp khác. Quyền biểu quyết được xác định theo danh sách chốt tại thời điểm tạo đề xuất.
4. **Sinh viên:** Có quyền nạp tiền vào quỹ và tra cứu số dư/lịch sử giao dịch minh bạch; **không có quyền tạo yêu cầu chi, bỏ phiếu hay giải ngân** theo quy định hiện tại.
5. **Quyền gọi `closePhase`:** Đã được nhóm xác nhận chính thức: Duy nhất System Admin có quyền gọi hàm đóng đợt học khi thời gian hiện tại đã vượt quá ngày kết thúc (`block.timestamp > endTime`). Tại đúng `block.timestamp == endTime`, giao dịch bị từ chối.
6. **Điều kiện tạo yêu cầu chi:** Mọi chủ thể tạo đề xuất (Class Officer, Advisor, Lecturer) đều bị ràng buộc bởi điều kiện tiên quyết: lớp phải có ít nhất một thành viên Ban cán sự đủ điều kiện (`eligibleVoterCount >= 1`). Nếu `eligibleVoterCount == 0`, giao dịch tạo đề xuất bị từ chối triệt để.

## 4. Đầu ra

Hệ thống cung cấp các kết quả đầu ra có thể kiểm tra và tra cứu được gắn với từng mã lớp (`classId`) bao gồm:

1. **Số dư quỹ theo lớp:**
   - Giá trị số dư ETH hiện có trong quỹ của từng lớp (`classId`), tự động cập nhật độc lập sau mỗi giao dịch nạp hoặc giải ngân. Số dư của lớp này hoàn toàn tách biệt với số dư của lớp khác.

2. **Kết quả giao dịch nạp tiền:**
   - Trạng thái thành công/thất bại của giao dịch.
   - Bản ghi sự kiện nạp tiền (mã `classId`, địa chỉ người nạp, số tiền, thời điểm). Mã băm giao dịch (Tx Hash) là thuộc tính tự nhiên của giao dịch blockchain mà giao diện người dùng đọc từ biên lai giao dịch (receipt); smart contract phát event chứa thông tin đối soát mà không bắt buộc phải lưu cố định Tx Hash trong biến trạng thái (storage).

3. **Thông tin và vòng đời trạng thái yêu cầu chi:**
   - Mã định danh lớp (`classId`) và mã yêu cầu chi (Proposal ID).
   - Nội dung chi tiết: người tạo, căn cứ tạo đề xuất (kèm `phaseId` nếu dựa trên quyền giảng dạy), mục đích, số tiền, người nhận, thời điểm tạo, thời hạn biểu quyết (7 ngày), số cử tri chốt (`eligibleVoterCount >= 1`) và ngưỡng phê duyệt tối thiểu `minimumApprovals >= 1`.
   - Số lượt bỏ phiếu chấp thuận hiện tại và danh sách các địa chỉ ví Class Officer đã bỏ phiếu.
   - **Vòng đời 6 trạng thái chuẩn hóa:**
     - `Pending`: Trạng thái ban đầu sau khi tạo, đang trong thời hạn 7 ngày chờ các cử tri thuộc danh sách chốt của lớp bỏ phiếu.
     - `Ready`: Đã nhận đủ số phiếu chấp thuận đạt ngưỡng `minimumApprovals >= 1` trong thời hạn 7 ngày; đủ điều kiện để Class Officer thực hiện chi tiền. Nếu đợt học đã đóng, yêu cầu được giải ngân trong vòng 5 ngày kể từ thời điểm đóng (`block.timestamp <= closedAt + 5 days`).
     - `Executed`: Đã được Class Officer của lớp thực hiện chuyển tiền thành công tới địa chỉ người nhận (hoàn thành chu trình giải ngân, trạng thái kết thúc, bị khóa vĩnh viễn).
     - `Failed`: Hết thời hạn 7 ngày nhưng không đạt ngưỡng chấp thuận tối thiểu; yêu cầu chính thức thất bại, bị khóa vĩnh viễn và không thể bỏ phiếu hay giải ngân (trạng thái kết thúc).
     - `Cancelled`: Đề xuất (`Pending` hoặc `Ready`) do Giảng viên đã bị đình chỉ/ngừng hoạt động (`isActive == false`) tạo ra, đã bị System Admin hoặc Advisor của lớp hủy theo cơ chế bảo vệ rủi ro khẩn cấp (R10); bị khóa vĩnh viễn, quỹ lớp không bị trừ tiền (trạng thái kết thúc).
     - `Expired`: Đề xuất `Ready` đã quá hạn giải ngân 5 ngày sau khi đợt học đóng (`block.timestamp > closedAt + 5 days`), bị vô hiệu hóa thông qua cơ chế Lazy State Transition (khi gọi `expireProposal` hoặc khi gọi `executeProposal` sau hạn chót); bị khóa vĩnh viễn, không thể giải ngân, quỹ lớp giữ nguyên số dư (trạng thái kết thúc).

4. **Kết quả thực hiện yêu cầu chi:**
   - Biên lai giao dịch giải ngân, ghi nhận số tiền chuyển ra từ quỹ lớp, địa chỉ người nhận và trạng thái `Executed`.
   - Thông báo lỗi/từ chối chi tiết nếu điều kiện thực hiện không thỏa mãn (không đủ số dư quỹ lớp, sai trạng thái, sai quyền).

5. **Kết quả quản trị hệ thống và đào tạo:**
   - Danh mục Giảng viên hợp lệ kèm mã định danh, địa chỉ ví và cờ trạng thái hoạt động.
   - Danh sách đợt học chuẩn hóa (mã `phaseId`, ngày bắt đầu, ngày kết thúc 3 tháng theo lịch, trạng thái `OPEN`/`CLOSED`).
   - Danh sách phân công giảng dạy theo từng đợt học (`phaseId`) và lớp học (`classId`).
   - Thông tin phân công Advisor gắn với từng lớp (`classId`).
   - Danh sách địa chỉ ví Class Officer đương nhiệm gắn với từng lớp (`classId`).

6. **Lịch sử giao dịch theo lớp:**
   - Danh sách phân định rõ ràng giữa giao dịch nạp (Deposit) và giao dịch chi (Withdrawal/Execution) theo từng lớp kèm mốc thời gian và địa chỉ liên quan để đối soát.

## 5. Trường hợp ngoại lệ

Hệ thống kiểm soát chặt chẽ các trường hợp ngoại lệ sau:

| Tình huống ngoại lệ | Hành vi của hệ thống | Trạng thái dữ liệu | Kết quả mong đợi |
|---|---|---|---|
| Người dùng chưa kết nối ví hoặc kết nối sai mạng | Giao diện từ chối gửi giao dịch lên blockchain | Không thay đổi | Yêu cầu kết nối MetaMask mạng Sepolia |
| Nạp tiền với số tiền bằng 0 ETH | Hợp đồng thông minh từ chối nhận | Số dư quỹ lớp giữ nguyên | Giao dịch bị revert kèm thông báo lỗi số tiền không hợp lệ |
| Nạp tiền hoặc thao tác trên mã lớp (`classId`) không tồn tại | Từ chối giao dịch | Không thay đổi dữ liệu | Revert giao dịch do mã lớp không hợp lệ |
| Địa chỉ người nhận không hợp lệ (ví dụ `address(0)`) | Từ chối tạo yêu cầu chi | Không tạo Proposal mới | Báo lỗi địa chỉ người nhận không hợp lệ |
| Người không phải System Admin cố quản lý giảng viên, phân công hoặc đóng đợt học | Giao dịch quản trị bị từ chối | Không thay đổi quyền | Revert giao dịch do vi phạm quyền System Admin |
| System Admin cố đóng đợt học khi `block.timestamp == endTime` (chưa vượt quá ngày kết thúc) | Giao dịch đóng đợt bị từ chối | Đợt học giữ nguyên `OPEN` | Revert giao dịch do chỉ được đóng khi `block.timestamp > endTime` |
| Giảng viên dùng phân công của đợt học đã `CLOSED` hoặc đã quá ngày kết thúc để tạo yêu cầu chi | Giao dịch tạo yêu cầu bị từ chối | Không ghi nhận yêu cầu | Revert giao dịch do đợt học căn cứ đã kết thúc |
| Giảng viên không dạy lớp và không phải Advisor cố tạo yêu cầu chi | Giao dịch tạo yêu cầu bị từ chối | Không ghi nhận yêu cầu | Revert giao dịch do không thỏa điều kiện phân công |
| Sinh viên hoặc System Admin cố tạo yêu cầu chi | Giao dịch tạo yêu cầu bị từ chối | Không ghi nhận yêu cầu | Revert giao dịch do không có quyền tạo |
| Lecturer hoặc Advisor cố bỏ phiếu hoặc thực thi chi quỹ | Giao dịch biểu quyết/thực thi bị từ chối | Trạng thái giữ nguyên | Revert giao dịch do chỉ Class Officer mới có quyền tài chính |
| Class Officer của lớp A cố biểu quyết hoặc thực thi yêu cầu của lớp B | Giao dịch bị từ chối | Dữ liệu lớp B giữ nguyên | Revert giao dịch do không có quyền trên lớp khác |
| Thành viên Class Officer cố biểu quyết 2 lần cho 1 yêu cầu | Từ chối lượt biểu quyết từ lần thứ 2 trở đi | Số lượt biểu quyết giữ nguyên | Revert giao dịch với thông báo đã biểu quyết trước đó |
| Yêu cầu chi chưa đạt ngưỡng tối thiểu nhưng bị gọi thực hiện | Từ chối thực hiện giải ngân | Trạng thái giữ nguyên `Pending` | Revert giao dịch do yêu cầu chưa sẵn sàng (`Not Ready`) |
| Thực hiện yêu cầu chi khi quỹ lớp không đủ số dư | Từ chối giải ngân chuyển tiền | Số dư và trạng thái giữ nguyên | Revert giao dịch do không đủ số dư quỹ lớp (`Insufficient Balance`) |
| Hai người cố thực hiện đồng thời cùng một yêu cầu | Giao dịch thứ nhất thành công, giao dịch thứ hai bị từ chối | Trạng thái là `Executed`, chỉ chuyển tiền 1 lần | Giao dịch thứ hai bị revert do yêu cầu đã thực hiện (`Already Executed`) |
| Thực hiện giải ngân lần đầu sau khi đợt học đã đóng quá 5 ngày (`phase.status == CLOSED` & `block.timestamp > closedAt + 5 days`) | Không chuyển ETH; hợp đồng cập nhật trạng thái đề xuất sang `Expired` và phát sự kiện `ProposalExpired` | Trạng thái chuyển từ `Ready` sang `Expired`, số dư quỹ lớp giữ nguyên | Giao dịch thành công ghi nhận trạng thái `Expired`; tiền quỹ giữ nguyên an toàn |
| Thực hiện giải ngân khi đợt học còn `OPEN` | Cho phép giải ngân bình thường nếu đủ điều kiện | Trạng thái chuyển từ `Ready` sang `Executed`, trừ số dư quỹ lớp | Giao dịch thành công chuyển ETH cho người nhận, không bị coi là `Expired` |
| Chuyển ETH thất bại khi giải ngân (ví dụ recipient contract revert) | Toàn bộ giao dịch giải ngân bị revert ngay lập tức | Trạng thái giữ nguyên `Ready`, số dư quỹ lớp không đổi | Giao dịch revert kèm lỗi chuyển ETH thất bại; không chuyển sang `Executed`, không mất tiền quỹ |
| Gọi thực hiện giải ngân khi đề xuất đã ở trạng thái kết thúc (`Executed`, `Failed`, `Cancelled`, `Expired`) | Từ chối giải ngân | Trạng thái và số dư giữ nguyên | Revert giao dịch do đề xuất không ở trạng thái `Ready` |
| Bất kỳ ai gọi `expireProposal` khi đề xuất `Ready` đã quá hạn 5 ngày sau đóng đợt (`phase.status == CLOSED` & `block.timestamp > closedAt + 5 days`) | Hợp đồng cập nhật trạng thái đề xuất sang `Expired` và phát event | Trạng thái chuyển từ `Ready` sang `Expired` | Giao dịch thành công ghi nhận trạng thái `Expired` công khai |
| Cố gọi `expireProposal` khi đợt học chưa đóng (`OPEN`) hoặc chưa quá 5 ngày sau `closedAt` | Từ chối cập nhật trạng thái | Trạng thái giữ nguyên `Ready` | Revert giao dịch do đề xuất chưa hết hạn thực thi |
| Cố biểu quyết yêu cầu đã quá thời hạn 7 ngày | Từ chối giao dịch ngay tại hàm gọi | Trạng thái không tăng phiếu | Revert giao dịch do yêu cầu đã quá thời hạn 7 ngày |
| Cố đánh dấu `Failed` cho yêu cầu đã ở trạng thái `Ready` | Từ chối cập nhật trạng thái | Trạng thái giữ nguyên `Ready` | Revert giao dịch do yêu cầu đã được phê duyệt hợp lệ trước hạn |
| Cố đánh dấu `Failed` khi yêu cầu chưa hết hạn 7 ngày | Từ chối cập nhật trạng thái | Trạng thái giữ nguyên `Pending` | Revert giao dịch do yêu cầu vẫn còn trong thời hạn bỏ phiếu |
| Tạo đề xuất khi lớp chưa có Class Officer (`eligibleVoterCount == 0`) | Từ chối giao dịch tạo đề xuất ngay lập tức | Không phát sinh Proposal mới, dữ liệu lớp giữ nguyên | Revert giao dịch do lớp bắt buộc phải có ít nhất 1 Ban cán sự đủ điều kiện (`NoEligibleVoters`) |
| Giảng viên bị đình chỉ (`isActive == false`) có đề xuất `Pending`/`Ready` bị hủy bởi Admin hoặc Advisor của lớp | Cập nhật trạng thái đề xuất sang `Cancelled`, lưu vết kiểm toán và phát sự kiện `ProposalCancelled` | Trạng thái chuyển sang `Cancelled`, quỹ lớp không đổi | Giao dịch thành công; đề xuất bị hủy và khóa vĩnh viễn, tiền quỹ được bảo toàn |
| Cố hủy đề xuất của Giảng viên vẫn đang hoạt động (`isActive == true`) | Từ chối giao dịch hủy | Trạng thái đề xuất giữ nguyên | Revert giao dịch do giảng viên chưa bị đình chỉ (`LecturerNotSuspended`) |
| Advisor của lớp A cố hủy đề xuất của Giảng viên bị đình chỉ tại lớp B | Từ chối giao dịch hủy | Trạng thái đề xuất lớp B giữ nguyên | Revert giao dịch do vi phạm phạm vi quyền hạn của Advisor |
| Cố hủy đề xuất đã giải ngân (`Executed`) hoặc đã kết thúc (`Failed`, `Cancelled`, `Expired`) | Từ chối giao dịch hủy | Trạng thái giữ nguyên | Revert giao dịch do đề xuất không ở trạng thái có thể hủy (`Pending` hoặc `Ready`) |
| Cố hủy đề xuất do Class Officer tạo ra bằng hàm hủy của giảng viên | Từ chối giao dịch hủy | Trạng thái đề xuất giữ nguyên | Revert giao dịch do đề xuất không phải do giảng viên tạo |
| Địa chỉ chưa được đề xuất cố gọi chấp nhận quyền System Admin (`acceptOwnership`) | Từ chối giao dịch chuyển quyền | Quyền System Admin giữ nguyên | Revert giao dịch do người gọi không phải pending owner |
| Người dùng từ chối ký giao dịch trên MetaMask | Hủy phiên giao dịch trên máy người dùng | Không phát sinh giao dịch blockchain | Giao diện hiển thị người dùng đã hủy giao dịch |

### Bảng kịch bản kiểm thử chuẩn hóa (Test Specification Matrix)

Bảng dưới đây tổng hợp các trường hợp kiểm thử quan trọng và rủi ro cao nhằm bảo đảm tính nhất quán trước khi triển khai smart contract:

| Mã kiểm thử | Mã quy tắc | Tình huống kiểm thử & Điều kiện đầu vào | Hành động thực hiện | Trạng thái trước giao dịch | Trạng thái sau giao dịch | Kết quả mong đợi |
|---|:---:|---|---|---|---|---|
| **TC-01** | R0-C, R3 | **Phân quyền sai lớp:** Ví `CO_A` là Class Officer lớp 1, cố bỏ phiếu cho đề xuất của lớp 2 | `CO_A` gọi hàm bỏ phiếu cho `proposalId = 1` của `classId = 2` | Yêu cầu lớp 2 ở trạng thái `Pending` | Giữ nguyên trạng thái `Pending`, số phiếu không đổi | Revert giao dịch do người gọi không thuộc Ban cán sự lớp 2 |
| **TC-02** | R0-C | **Phân quyền sai lớp:** Ví `ADV_A` là Advisor lớp 1, cố thêm Ban cán sự cho lớp 2 | `ADV_A` gọi hàm thêm Class Officer cho `classId = 2` | Danh sách Class Officer lớp 2 không đổi | Giữ nguyên danh sách Class Officer lớp 2 | Revert giao dịch do người gọi không phải Advisor của lớp 2 |
| **TC-03** | R0-D, R2 | **Tạo yêu cầu sau `endTime`:** Đợt `2026-S1-P1` có `endTime` là 23:59:59 ngày 31/03/2026. Thời gian hiện tại là 00:00:01 ngày 01/04/2026 (`block.timestamp > endTime`), trạng thái lưu trữ đợt vẫn là `OPEN` | Giảng viên có phân công đợt `2026-S1-P1` gọi tạo yêu cầu chi kèm mã đợt này | Chưa có đề xuất mới nào được tạo | Không có đề xuất mới nào được lưu | Revert giao dịch do đợt học căn cứ đã kết thúc thời gian hiệu lực |
| **TC-04** | R4 | **Biểu quyết trùng lặp:** Class Officer `CO_1` của lớp 1 đã bỏ phiếu thành công cho đề xuất 1 | `CO_1` gọi tiếp hàm bỏ phiếu lần thứ 2 cho đề xuất 1 | Đề xuất 1 ở trạng thái `Pending`, số phiếu = 1 | Đề xuất 1 ở `Pending`, số phiếu = 1 | Revert giao dịch do địa chỉ ví đã biểu quyết cho đề xuất này |
| **TC-05** | R8, R3 | **Biểu quyết sau deadline 7 ngày:** Đề xuất 1 tạo lúc `T0`. Thời gian hiện tại `block.timestamp > T0 + 7 days`. Đề xuất mới có 1 phiếu (chưa đạt ngưỡng) | Class Officer `CO_2` gọi bỏ phiếu cho đề xuất 1 | Đề xuất 1 ở `Pending`, số phiếu = 1 | Đề xuất 1 ở `Pending`, số phiếu = 1 | Revert giao dịch do thời hạn biểu quyết 7 ngày đã kết thúc |
| **TC-06** | R3 | **Đạt ngưỡng 51% đúng hạn:** Lớp 1 có 5 Class Officer chốt lúc tạo (`eligibleVoterCount = 5`, ngưỡng $\lceil 0.51 \times 5 \rceil = 3$ phiếu). Đề xuất 1 đang có 2 phiếu trong hạn 7 ngày | Class Officer `CO_3` gọi bỏ phiếu cho đề xuất 1 | Đề xuất 1 ở trạng thái `Pending`, số phiếu = 2 | Đề xuất 1 chuyển sang trạng thái `Ready`, số phiếu = 3 | Giao dịch thành công; phát sự kiện đề xuất đã đạt ngưỡng (`Ready`) |
| **TC-07** | R8 | **Hết hạn 7 ngày chưa đạt ngưỡng và chuyển `Failed`:** Đề xuất 1 có 2/5 phiếu, đã quá 7 ngày kể từ khi tạo | Bất kỳ ai gọi `failProposal(classId = 1, proposalId = 1)` | Đề xuất 1 ở trạng thái `Pending` | Đề xuất 1 chuyển sang trạng thái `Failed` | Giao dịch thành công; đề xuất bị khóa vĩnh viễn, phát sự kiện chuyển `Failed` |
| **TC-08** | R6 | **Không đủ số dư quỹ lớp:** Quỹ lớp 1 hiện có 0.5 ETH. Đề xuất 1 yêu cầu 1.0 ETH đang ở trạng thái `Ready` | Class Officer gọi thực hiện giải ngân đề xuất 1 | Quỹ lớp 1: 0.5 ETH; Đề xuất 1: `Ready` | Quỹ lớp 1: 0.5 ETH; Đề xuất 1: `Ready` | Revert giao dịch do số dư quỹ của lớp không đủ chi trả |
| **TC-09** | R7 | **Thực thi 2 lần (Chống chi trùng):** Đề xuất 1 đã được giải ngân thành công và chuyển sang `Executed` | Class Officer gọi thực thi giải ngân lại đề xuất 1 | Đề xuất 1 ở trạng thái `Executed` | Đề xuất 1 giữ nguyên `Executed`, quỹ lớp không trừ thêm | Revert giao dịch do đề xuất đã thực thi trước đó |
| **TC-10** | R0-D, R6 | **Giải ngân yêu cầu `Ready` trong hạn 5 ngày sau khi đợt học đóng (Q7):** Đề xuất 1 đạt `Ready` trước khi đợt học đóng. Đợt học chuyển sang `CLOSED` lúc `closedAt`. Sau 2 ngày (`block.timestamp <= closedAt + 5 days`), Quỹ lớp 1 đủ số dư | Class Officer gọi thực hiện giải ngân đề xuất 1 | Đợt học: `CLOSED`; Đề xuất 1: `Ready`; Quỹ lớp 1: 1.0 ETH | Đợt học: `CLOSED`; Đề xuất 1: `Executed`; Quỹ lớp 1: 0.8 ETH | Giao dịch thành công; chuyển 0.2 ETH cho người nhận; phát sự kiện giải ngân thành công |
| **TC-11** | R0-A | **System Admin cố can thiệp quỹ:** Deployer gọi hàm rút tiền trực tiếp từ quỹ lớp 1 hoặc cố biểu quyết đề xuất | Deployer gửi giao dịch rút tiền hoặc bỏ phiếu | Quỹ lớp và trạng thái đề xuất không đổi | Quỹ lớp và trạng thái đề xuất giữ nguyên | Revert giao dịch do System Admin không có quyền rút quỹ hoặc biểu quyết |
| **TC-12** | R0-C, R3, R6 | **Advisor hoặc Giảng viên cố biểu quyết/giải ngân:** Giảng viên/Advisor không mang vai trò Class Officer cố gọi hàm bỏ phiếu hoặc giải ngân | Giảng viên/Advisor gửi giao dịch bỏ phiếu hoặc giải ngân | Trạng thái đề xuất và số dư không đổi | Trạng thái đề xuất và số dư giữ nguyên | Revert giao dịch do chỉ Class Officer của lớp mới có quyền biểu quyết/giải ngân |
| **TC-13** | R2 | **Sinh viên cố tạo yêu cầu chi:** Địa chỉ sinh viên (chỉ là người nạp/xem quỹ, không là Class Officer/Advisor/Lecturer) gửi giao dịch tạo yêu cầu chi | Sinh viên gọi hàm tạo yêu cầu chi | Không phát sinh đề xuất mới | Danh sách đề xuất giữ nguyên | Revert giao dịch do không có quyền tạo đề xuất |
| **TC-14** | R5 | **Người tạo tự bỏ phiếu khi là Class Officer:** Class Officer `CO_1` tạo đề xuất 1, sau đó tự bỏ phiếu cho đề xuất 1 trong hạn 7 ngày | `CO_1` gọi hàm bỏ phiếu cho đề xuất 1 | Đề xuất 1: `Pending`, số phiếu = 0 | Đề xuất 1: `Pending`, số phiếu = 1 (hoặc `Ready` nếu đủ ngưỡng) | Giao dịch thành công ghi nhận 1 phiếu chấp thuận hợp lệ |
| **TC-15** | R0-D | **Khởi tạo đợt học sai mốc thời gian:** System Admin gửi giao dịch tạo đợt học với `startTime >= endTime` hoặc mã đợt rỗng | Deployer gọi hàm tạo đợt học với dữ liệu thời gian không hợp lệ | Đợt học chưa tồn tại | Đợt học không được tạo | Revert giao dịch do dữ liệu thời gian đợt học không hợp lệ (`startTime < endTime`) |
| **TC-16** | R0-B | **Tải phân công theo lô phát sinh event tổng (Q6) và xử lý giao dịch từng phần:** File có 20 bản ghi chia thành 2 lô (lô 1: 10 bản ghi hợp lệ; lô 2: 10 bản ghi chứa 1 địa chỉ không phải giảng viên) | Giao diện gửi 2 giao dịch blockchain | Chưa có bản ghi nào | Lô 1 thành công (ghi nhận 10 bản ghi, phát event tổng kèm `batchId` và `recordCount = 10`); Lô 2 revert toàn bộ 10 bản ghi | Giao diện đối soát, giữ nguyên 10 bản ghi lô 1 và chỉ cho phép Admin gửi lại lô 2 sau khi sửa lỗi |
| **TC-17** | R8 | **Cố gọi `failProposal` khi đề xuất đã `Ready`:** Đề xuất 1 đạt ngưỡng `Ready` ở ngày thứ 3. Sang ngày thứ 8 (`block.timestamp > creationTime + 7 days`), người dùng gọi chuyển `Failed` | Người dùng gọi `failProposal(classId = 1, proposalId = 1)` | Đề xuất 1 ở trạng thái `Ready` | Đề xuất 1 giữ nguyên trạng thái `Ready` | Revert giao dịch do đề xuất đã đạt điều kiện phê duyệt trước hạn |
| **TC-18** | R3, R0-C | **Biến động Class Officer khi đề xuất đang `Pending` (Q1, Q2):** Lớp có 5 Class Officer chốt lúc tạo đề xuất (`eligibleVoterCount = 5`, ngưỡng 3). Đề xuất 1 nhận được 2 phiếu. Advisor xóa 1 Class Officer chưa bỏ phiếu (số thành viên lớp còn 4). | Class Officer thứ 3 (trong danh sách chốt) gọi bỏ phiếu cho đề xuất 1 | Đề xuất 1: `Pending`, 2 phiếu | Đề xuất 1: `Ready`, 3 phiếu | Giao dịch thành công; mẫu số giữ nguyên là 5 (ngưỡng 3 phiếu), đạt đủ 3 phiếu chuyển sang `Ready` |
| **TC-19** | R2 | **Tạo đề xuất với địa chỉ người nhận không hợp lệ:** Class Officer hoặc Giảng viên tạo đề xuất với `recipient = address(0)` hoặc địa chỉ hợp đồng quỹ | Người tạo gọi hàm tạo đề xuất | Chưa có đề xuất mới | Không có đề xuất mới nào được lưu | Revert giao dịch do địa chỉ người nhận không hợp lệ |
| **TC-20** | R2 | **Tạo đề xuất với số tiền bằng 0:** Class Officer tạo đề xuất với `amount = 0 ETH` | Class Officer gọi hàm tạo đề xuất với `amount = 0` | Chưa có đề xuất mới | Không có đề xuất mới nào được lưu | Revert giao dịch do số tiền yêu cầu chi phải lớn hơn 0 |
| **TC-21** | R1, R6 | **Độc lập số dư giữa các lớp (`classId` isolation):** Lớp 1 nạp 2.0 ETH, Lớp 2 nạp 0.5 ETH. Lớp 1 giải ngân 1.0 ETH | Class Officer lớp 1 giải ngân đề xuất 1.0 ETH của lớp 1 | Quỹ Lớp 1: 2.0 ETH; Quỹ Lớp 2: 0.5 ETH | Quỹ Lớp 1: 1.0 ETH; Quỹ Lớp 2: 0.5 ETH | Giao dịch thành công; số dư Quỹ Lớp 2 hoàn toàn không bị ảnh hưởng |
| **TC-22** | R6 | **Chuyển ETH thất bại khi giải ngân:** Đề xuất 1 ở trạng thái `Ready`, quỹ lớp đủ ETH. Địa chỉ người nhận là contract từ chối nhận ETH (không có `receive`/`fallback`) | Class Officer gọi thực hiện giải ngân đề xuất 1 | Quỹ lớp: 1.0 ETH; Đề xuất 1: `Ready` | Quỹ lớp: 1.0 ETH; Đề xuất 1: `Ready` | Revert toàn bộ giao dịch; trạng thái không đổi sang `Executed`, quỹ lớp không bị trừ |
| **TC-23** | R8, R3 | **Biểu quyết tại đúng mốc biên deadline 7 ngày:** Đề xuất 1 tạo lúc `T0`. Thời gian hiện tại chính xác `block.timestamp == T0 + 7 days` (giây cuối cùng của thời hạn 7 ngày) | Class Officer gọi bỏ phiếu chấp thuận; đồng thời kiểm tra gọi `failProposal` tại mốc này | Đề xuất 1: `Pending`, thiếu 1 phiếu | Đề xuất 1 chuyển sang `Ready` (hoặc tăng phiếu thành công); lệnh gọi `failProposal` bị revert | Biểu quyết thành công do thỏa mãn `block.timestamp <= creationTime + 7 days`; gọi `failProposal` bị từ chối vì chỉ cho phép khi thời gian lớn hơn hẳn deadline (`block.timestamp > creationTime + 7 days`) |
| **TC-24** | R2, R3 | **Tạo đề xuất khi lớp chưa có Ban cán sự (Quyết định 3):** Lớp mới khởi tạo, Advisor chưa thêm Class Officer nào (`eligibleVoterCount = 0`) | Advisor hoặc Giảng viên gọi tạo đề xuất chi cho lớp | Lớp chưa có Class Officer (`eligibleVoterCount = 0`) | Không có đề xuất mới nào được lưu | Revert giao dịch do lớp bắt buộc phải có ít nhất 1 Ban cán sự đủ điều kiện (`NoEligibleVoters`). Sau khi Advisor thêm ít nhất 1 Class Officer, việc tạo đề xuất mới được thực hiện thành công. |
| **TC-25** | R0-D | **Đóng đợt học tại đúng mốc biên `block.timestamp == endTime` (Q8):** Đợt học có `endTime = 23:59:59`. Thời gian khối hiện tại chính xác là `block.timestamp == endTime` | System Admin gọi `closePhase(phaseId)` | Đợt học đang ở `OPEN` | Đợt học giữ nguyên `OPEN` | Revert giao dịch do chỉ được phép đóng khi thời gian lớn hơn hẳn (`block.timestamp > endTime`) |
| **TC-26** | R0-D, R6 | **Giải ngân đề xuất sau khi đợt học đã đóng quá 5 ngày (Lazy Transition sang `Expired` - Quyết định 2):** Đề xuất 1 ở trạng thái `Ready`. Đợt học đóng lúc `closedAt`. Thời gian hiện tại `block.timestamp = closedAt + 5 days + 1 second` | Class Officer gọi giải ngân đề xuất 1 lần đầu; sau đó gọi tiếp lần 2 | Đợt học: `CLOSED`; Đề xuất 1: `Ready`; Quỹ lớp đủ tiền | Lần 1: Không chuyển ETH, Đề xuất 1 chuyển sang `Expired`, phát event `ProposalExpired`, quỹ lớp giữ nguyên số dư; Lần 2: Giữ nguyên `Expired` | Lần gọi đầu thành công ghi nhận trạng thái `Expired`; Lần gọi thứ 2 bị revert do đề xuất đã ở trạng thái `Expired` |
| **TC-27** | R0-A | **Chuyển giao quyền System Admin 2 bước (`Ownable2Step` - Q5):** System Admin gọi `transferOwnership(newAdmin)`. Trước khi `newAdmin` gọi chấp nhận, `newAdmin` cố thao tác quản trị; sau đó `newAdmin` gọi `acceptOwnership()` | `newAdmin` gọi thêm giảng viên trước và sau khi gọi `acceptOwnership` | Admin hiện tại là deployer | Sau khi chấp nhận, `newAdmin` trở thành System Admin chính thức | Lệnh gọi trước `acceptOwnership` bị revert; sau `acceptOwnership` thành công |
| **TC-28** | R3 | **Kiểm tra kép quyền biểu quyết của Ban cán sự bị xóa (Quyết định QA-04 Phương án A):** Ban cán sự `CO_A` có tên trong snapshot đề xuất 1. Advisor xóa `CO_A` khỏi lớp (`isClassOfficer == false`). Sau đó `CO_A` cố bỏ phiếu cho đề xuất 1 và đề xuất 2. | `CO_A` gọi bỏ phiếu cho đề xuất 1; sau đó gọi bỏ phiếu cho đề xuất 2 | Đề xuất 1: `Pending`; Đề xuất 2: `Pending` | Đề xuất 1: giữ nguyên số phiếu; Đề xuất 2: giữ nguyên | Bỏ phiếu đề xuất 1 bị revert (do vi phạm điều kiện hiện hành: không còn là Class Officer tại thời điểm bỏ phiếu); Bỏ phiếu đề xuất 2 bị revert (không thuộc snapshot đề xuất mới) |
| **TC-29** | R10 | **Hủy đề xuất của Giảng viên bị đình chỉ (Quyết định 1):** Giảng viên `LEC_1` bị đình chỉ (`isActive == false`) có đề xuất đang ở `Pending` hoặc `Ready`. | System Admin hoặc Advisor của đúng lớp đó gọi `cancelSuspendedLecturerProposal` | Đề xuất ở trạng thái `Pending` hoặc `Ready` | Đề xuất chuyển sang trạng thái `Cancelled`, lưu thông tin kiểm toán `cancelledBy`, `cancelledAt`, phát `ProposalCancelled` | Giao dịch thành công; đề xuất bị hủy và khóa vĩnh viễn, quỹ lớp không bị trừ tiền, lịch sử phiếu bầu được bảo toàn |
| **TC-30** | R10 | **Advisor cố hủy đề xuất của Giảng viên bị đình chỉ ở lớp khác (Quyết định 1):** Giảng viên bị đình chỉ có đề xuất tại Lớp 2. `ADV_1` là Advisor của Lớp 1 cố hủy đề xuất này | `ADV_1` gọi `cancelSuspendedLecturerProposal` cho Lớp 2 | Đề xuất Lớp 2 ở trạng thái `Pending` | Đề xuất Lớp 2 giữ nguyên trạng thái | Revert giao dịch do Advisor không có thẩm quyền trên lớp khác |
| **TC-31** | R10 | **Cố hủy đề xuất của Giảng viên đang hoạt động bình thường (Quyết định 1):** Giảng viên vẫn có `isActive == true`. Đề xuất đang ở `Pending` hoặc `Ready` | System Admin hoặc Advisor gọi hủy đề xuất của giảng viên này | Đề xuất ở trạng thái `Pending` hoặc `Ready` | Đề xuất giữ nguyên trạng thái ban đầu | Revert giao dịch do giảng viên chưa bị đình chỉ (`LecturerNotSuspended`) |
| **TC-32** | R10 | **Cố hủy đề xuất đã giải ngân của Giảng viên bị đình chỉ (Quyết định 1):** Đề xuất đã chuyển sang `Executed` trước khi giảng viên bị đình chỉ | System Admin hoặc Advisor gọi hủy đề xuất này | Đề xuất ở trạng thái `Executed` | Đề xuất giữ nguyên trạng thái `Executed` | Revert giao dịch do đề xuất đã giải ngân thành công là bất biến, không thể bị hủy (`ProposalNotCancellable`) |
| **TC-33** | R0-D, R8 | **Cập nhật công khai đề xuất `Ready` quá hạn sang `Expired` (`expireProposal` - Quyết định 2):** Đề xuất 1 ở `Ready`. Đợt học đóng lúc `closedAt`. Thời gian hiện tại `block.timestamp > closedAt + 5 days` | Bất kỳ người dùng nào gọi `expireProposal(classId = 1, proposalId = 1)` | Đề xuất 1 ở trạng thái `Ready` | Đề xuất 1 chuyển sang trạng thái `Expired`, phát event `ProposalExpired` | Giao dịch thành công; đề xuất chính thức sang `Expired` mà không chuyển ETH, không thể giải ngân tiếp. Nếu gọi khi chưa quá 5 ngày thì revert. |
| **TC-34** | R0-D, R6 | **Giải ngân thành công khi đợt học còn `OPEN` (QA-01):** Đề xuất 1 đạt `Ready`, đợt học căn cứ vẫn đang ở `OPEN` (`phase.status == OPEN`, `closedAt == 0`). Quỹ lớp đủ số dư | Class Officer gọi `executeProposal(classId = 1, proposalId = 1)` | Đợt học: `OPEN`; Đề xuất 1: `Ready`; Quỹ lớp: 1.0 ETH | Đợt học: `OPEN`; Đề xuất 1: `Executed`; Quỹ lớp: 0.8 ETH | Giao dịch thành công, chuyển ETH cho người nhận; KHÔNG bị đánh dấu nhầm thành `Expired` do `closedAt == 0` |
| **TC-35** | R10 | **Cố hủy đề xuất do Class Officer tạo bằng hàm hủy của Giảng viên (QA-02):** Đề xuất do Class Officer tạo đang ở `Pending` hoặc `Ready`. Giảng viên nào đó bị đình chỉ | System Admin hoặc Advisor gọi `cancelSuspendedLecturerProposal` cho đề xuất của Class Officer | Đề xuất ở trạng thái `Pending` hoặc `Ready` | Đề xuất giữ nguyên trạng thái | Revert giao dịch do đề xuất không phải do Giảng viên tạo (`ProposalNotCancellable`) |
| **TC-36** | R10 | **Cố hủy đề xuất đã ở trạng thái kết thúc (`Failed`, `Cancelled`, `Expired`):** Đề xuất do Giảng viên bị đình chỉ tạo nhưng đã ở `Failed` (quá 7 ngày) hoặc `Expired` (quá 5 ngày sau đóng đợt) | System Admin hoặc Advisor gọi `cancelSuspendedLecturerProposal` | Đề xuất ở trạng thái `Failed` hoặc `Expired` | Đề xuất giữ nguyên trạng thái | Revert giao dịch do chỉ được hủy đề xuất đang `Pending` hoặc `Ready` (`ProposalNotCancellable`) |
| **TC-37** | R6 | **Chuyển ETH thất bại tại đúng ranh giới thời hạn 5 ngày (QA-06):** Đề xuất 1 ở `Ready`. Đợt học `CLOSED`. Thời gian đúng `block.timestamp == closedAt + 5 days`. Recipient contract từ chối nhận ETH | Class Officer gọi `executeProposal` | Quỹ lớp: 1.0 ETH; Đề xuất 1: `Ready` | Quỹ lớp: 1.0 ETH; Đề xuất 1: `Ready` | Toàn bộ giao dịch revert; đề xuất giữ nguyên `Ready`, số dư quỹ giữ nguyên; không bị trừ tiền và không chuyển sang `Executed` |
| **TC-38** | R8, R3 | **Bỏ phiếu sau deadline 7 ngày (`block.timestamp > creationTime + 7 days`):** Đề xuất 1 tạo lúc `T0`. Hiện tại `block.timestamp = T0 + 7 days + 1 second` | Class Officer gọi `voteProposal(classId = 1, proposalId = 1)` | Đề xuất 1: `Pending` (trong storage chưa đổi) | Đề xuất 1: `Pending`, số phiếu không đổi | Revert giao dịch do quá hạn thời gian biểu quyết (`VotingDeadlineExceeded`). Hàm `getProposalState` trả về `Failed`. |
| **TC-39** | R10, R8 | **Cố hủy đề xuất của Giảng viên bị đình chỉ khi đề xuất đã quá 7 ngày nhưng storage chưa cập nhật:** Giảng viên `LEC_1` bị đình chỉ. Đề xuất của `LEC_1` có storage là `Pending` nhưng `block.timestamp > votingDeadline` (trạng thái hiệu lực là `Failed`). | Admin hoặc Advisor gọi `cancelSuspendedLecturerProposal` | Storage đề xuất: `Pending`, quá deadline 7 ngày | Storage giữ nguyên hoặc chuyển sang `Failed` | Revert giao dịch (`ProposalNotCancellable`) do trạng thái hiệu lực thực tế đã là `Failed`, không được phép ghi đè thành `Cancelled`. |
| **TC-40** | R10, R0-D | **Cố hủy đề xuất của Giảng viên bị đình chỉ khi đề xuất đã quá hạn giải ngân 5 ngày sau đóng đợt nhưng storage chưa cập nhật:** Giảng viên `LEC_1` bị đình chỉ. Đề xuất của `LEC_1` có storage là `Ready`, đợt học đã `CLOSED`, `block.timestamp > closedAt + 5 days` (trạng thái hiệu lực là `Expired`). | Admin hoặc Advisor gọi `cancelSuspendedLecturerProposal` | Storage đề xuất: `Ready`, quá hạn giải ngân 5 ngày | Storage giữ nguyên hoặc chuyển sang `Expired` | Revert giao dịch (`ProposalNotCancellable`) do trạng thái hiệu lực thực tế đã là `Expired`, không được phép ghi đè thành `Cancelled`. |
| **TC-41** | R0-A, R2 | **Giảng viên cập nhật địa chỉ ví mới và kiểm tra quyền:** Giảng viên `GV01` đổi từ `Ví A` sang `Ví B` qua `updateLecturerAddress`. Phân công giảng dạy của `GV01` cho Lớp 1 vẫn `isValid == true`. | `Ví A` gọi `createProposal`; sau đó `Ví B` gọi `createProposal` | `Ví A` đã bị gỡ liên kết; `Ví B` được liên kết với `GV01` | Đề xuất tạo bởi `Ví B` được lưu trữ thành công | Lệnh gọi của `Ví A` bị revert (không còn là giảng viên hợp lệ); Lệnh gọi của `Ví B` thành công vì hệ thống tra cứu phân công theo `lecturerId` gắn với ví hiện hành. Đề xuất cũ do `Ví A` tạo trước đó vẫn giữ nguyên tác giả là `Ví A`. |
| **TC-42** | R0-A | **Cập nhật địa chỉ ví giảng viên sang chính địa chỉ hiện tại hoặc địa chỉ đã thuộc về giảng viên khác:** Admin gọi cập nhật ví cho `GV01`. | Lần 1: Gọi `updateLecturerAddress("GV01", ví_hiện_tại)`; Lần 2: Gọi `updateLecturerAddress("GV01", ví_của_GV02)` | `GV01` có ví hiện tại; `GV02` có ví riêng | Dữ liệu giảng viên giữ nguyên vẹn | Lần 1: Revert `SameAddress()` hoặc no-op an toàn; Lần 2: Revert `AddressAlreadyAssigned()` do địa chỉ ví đã thuộc giảng viên khác (bảo toàn ánh xạ hai chiều song ánh). |
| **TC-43** | R0-B, R2 | **Thu hồi phân công giảng dạy và kiểm tra đề xuất cũ vs đề xuất mới:** Giảng viên `GV01` có phân công tại Lớp 1, đã tạo Đề xuất 1 (`Pending`). Admin cập nhật phân công với `isValid = false` (thu hồi). | Giảng viên `GV01` gọi `createProposal` tạo Đề xuất 2; Ban cán sự tiếp tục gọi `voteProposal` cho Đề xuất 1 | Phân công `GV01` tại Lớp 1 bị thu hồi (`isValid == false`) | Không có đề xuất mới; Đề xuất 1 tăng phiếu bình thường | Lệnh tạo Đề xuất 2 bị revert (`AssignmentNotFound` / `InvalidAssignment`); Đề xuất 1 tiếp tục biểu quyết bình thường (đề xuất cũ không bị ảnh hưởng). |
| **TC-44** | R3, R0-C | **Cán sự bị xóa rồi được thêm lại trước deadline biểu quyết (Edge-case):** Cán sự `CO_1` nằm trong snapshot cử tri Đề xuất 1. Advisor xóa `CO_1` khỏi lớp, sau đó thêm lại `CO_1` vào lớp khi đề xuất vẫn đang `Pending`. | `CO_1` gọi `voteProposal` cho Đề xuất 1 | `CO_1` hiện đang có tên trong `_classOfficers` của lớp | Đề xuất 1 tăng 1 phiếu chấp thuận | Giao dịch thành công theo Phương án A do thỏa mãn đồng thời cả 2 điều kiện tại thời điểm vote: (1) có tên trong snapshot cử tri; (2) hiện đang là Class Officer hợp lệ. |

## 6. Ngoài phạm vi

Các chức năng và đặc điểm sau **nằm ngoài phạm vi** của phiên bản nguyên mẫu ban đầu:
- Quản lý và lưu chuyển tiền thật hoặc tiền pháp định (chỉ sử dụng ETH thử nghiệm trên mạng Sepolia).
- Tích hợp trực tiếp API thời gian thực với phần mềm quản lý đào tạo trường đại học (thực hiện qua quy trình xuất/nhập file theo lô của System Admin).
- Smart contract tự động đọc trực tiếp định dạng file Excel/CSV (việc đọc file và chia nhỏ lô do giao diện frontend DApp xử lý).
- Tích hợp tài khoản ngân hàng hoặc cổng thanh toán truyền thống.
- Gửi thông báo tự động qua Email, SMS hoặc tin nhắn viễn thông.
- Hỗ trợ triển khai đa chuỗi (Multi-chain) ngoài Ethereum Sepolia Testnet.
- Hệ thống báo cáo phân tích tài chính phức tạp, biểu đồ dự báo ngân sách dài hạn.
- Cơ chế bỏ phiếu phân tầng phức tạp với quyền biểu quyết theo tỷ lệ đóng góp tiền quỹ.
- Cơ chế bỏ phiếu chống (Against/Reject) và phiếu trắng (Abstain) (phiên bản v0.1 chỉ triển khai cơ chế phê duyệt đơn - Approval-only voting).

## 7. Các quyết định nghiệp vụ và câu hỏi mở

### 7.1. Các quyết định đã được nhóm xác nhận chính thức

Dựa trên các quyết định chính thức của nhóm (Q1 – Q10), các vấn đề nghiệp vụ và kỹ thuật sau đây đã được chốt và đưa vào đặc tả:

1. **Xác định số phiếu cần thiết và snapshot cử tri (Giải quyết Câu hỏi 1 & Q1):**
   - Khi tạo yêu cầu chi, hệ thống chốt số lượng Ban cán sự đủ điều kiện (`eligibleVoterCount`) và danh sách cử tri tại thời điểm tạo yêu cầu.
   - Sự thay đổi số lượng Ban cán sự sau đó không làm thay đổi mẫu số đã chốt.
   - Công thức xác định số phiếu tối thiểu: $\text{minimumApprovals} = \max(1, \lceil 0.51 \times \text{eligibleVoterCount} \rceil)$.

2. **Bảo toàn phiếu đã bỏ và Kiểm tra kép quyền biểu quyết của thành viên bị xóa (Giải quyết Câu hỏi 2, Q2 & Quyết định QA-04 Phương án A):**
   - Lá phiếu hợp lệ đã bỏ được giữ nguyên hiệu lực, kể cả khi thành viên sau đó bị xóa khỏi danh sách Ban cán sự.
   - Theo quyết định QA-04 Phương án A (Kiểm tra kép): Thành viên bị xóa khỏi danh sách Ban cán sự sau khi tạo đề xuất sẽ không còn quyền bỏ phiếu cho đề xuất cũ đó (giao dịch bị revert do vi phạm điều kiện hiện hành).
   - Không cho phép một địa chỉ bỏ phiếu nhiều lần cho cùng một đề xuất.

3. **Cập nhật phân công theo đợt học và bảo tồn lịch sử (Giải quyết Câu hỏi 3 & Q3):**
   - Phân công giảng dạy theo từng đợt học được bổ sung/cập nhật theo cơ chế append/upsert mà không xóa mất lịch sử cũ.
   - Dữ liệu cũ được lưu trữ (sử dụng cờ hiệu lực `isValid`) để phục vụ tra cứu và kiểm toán.

4. **Độc lập của đề xuất chi khi phân công thay đổi hoặc bị thu hồi (Giải quyết Câu hỏi 4 & Q4):**
   - Phân công giảng dạy bị sửa đổi hoặc thu hồi không làm tự động hủy bỏ các đề xuất chi đã tạo trước đó, không xóa phiếu đã bỏ và không thay đổi ngưỡng biểu quyết đã chốt.
   - Quyền tạo đề xuất mới bắt buộc kiểm tra theo phân công đang có hiệu lực.

5. **Chuyển giao quyền System Admin an toàn bằng `Ownable2Step` (Giải quyết Câu hỏi 6 & Q5):**
   - Áp dụng quy trình 2 bước của OpenZeppelin: (1) System Admin hiện tại đề xuất địa chỉ mới; (2) Địa chỉ mới chủ động chấp nhận quyền.
   - Quyền quản trị không chuyển ngay lập tức khi mới đề xuất. System Admin không có quyền rút quỹ hoặc biểu quyết duyệt chi.

6. **Sự kiện tổng khi cập nhật phân công theo lô (Giải quyết Câu hỏi 7 & Q6):**
   - Bắt buộc phát một sự kiện tổng cho cả lô phân công (chứa tối thiểu `batchId` và `recordCount`). Không cấm phát thêm sự kiện chi tiết nếu cần.

7. **Hạn thực hiện đề xuất chi đã được duyệt và trạng thái `Expired` (Giải quyết Câu hỏi 8, Q7 & Quyết định 2):**
   - Đặt hạn chót thực hiện giải ngân cố định là **5 ngày sau khi đợt học thực tế được đóng**: $\text{executionDeadline} = \text{closedAt} + 5\text{ days}$.
   - Smart contract không tự động thay đổi trạng thái chỉ vì thời gian trôi qua. Thay vào đó, áp dụng cơ chế **Lazy State Transition**:
     - Lần đầu tiên Class Officer gọi `executeProposal` sau hạn chót 5 ngày: hợp đồng không chuyển ETH, cập nhật trạng thái đề xuất từ `Ready` sang `Expired`, phát sự kiện `ProposalExpired(classId, proposalId, block.timestamp)`, kết thúc giao dịch thành công. Các lần gọi tiếp theo sẽ bị revert do đề xuất đã `Expired`.
     - Cho phép bất kỳ ai gọi hàm công khai `expireProposal(classId, proposalId)` để chuyển trạng thái đề xuất quá hạn sang `Expired` và phát sự kiện `ProposalExpired`.
   - Tiền chưa giải ngân nằm lại an toàn 100% trong quỹ của lớp; dữ liệu lịch sử được giữ nguyên để đối soát.

8. **Quyền đóng đợt học và điều kiện thời gian (Giải quyết Câu hỏi 9 & Q8):**
   - Duy nhất System Admin có quyền gọi hàm đóng đợt học `closePhase`.
   - Điều kiện thời gian bắt buộc: $\text{block.timestamp} > \text{endTime}$. Tại thời điểm $\text{block.timestamp} == \text{endTime}$, giao dịch bị từ chối. Trạng thái chỉ chuyển sang `CLOSED` khi giao dịch được đào thành công trên blockchain, lưu `closedAt`.

9. **Lưu định danh giảng viên và địa chỉ ví (Giải quyết Câu hỏi 10 & Q9):**
   - Lưu trữ `lecturerId` ổn định trong dữ liệu phân công.
   - Giao diện truy vấn địa chỉ ví hiện tại theo `lecturerId`.
   - Lịch sử giao dịch và sự kiện lưu cố định địa chỉ ví tại thời điểm giao dịch phát sinh, không làm sai lệch lịch sử khi đổi ví.

10. **Điều kiện cử tri Ban cán sự khi tạo đề xuất chi (Giải quyết Câu hỏi 11, Q10 & Quyết định 3):**
    - Bắt buộc lớp phải có ít nhất 1 thành viên Ban cán sự đủ điều kiện biểu quyết (`eligibleVoterCount >= 1`).
    - Nếu lớp chưa có thành viên Ban cán sự nào (`eligibleVoterCount == 0`), mọi giao dịch tạo yêu cầu chi bị từ chối triệt để (revert) với lỗi `NoEligibleVoters`.
    - Quy tắc này áp dụng bình đẳng cho mọi đối tượng tạo đề xuất: Class Officer, Advisor và Lecturer.
    - Ngưỡng phê duyệt tối thiểu `minimumApprovals = max(1, ceil(0.51 * eligibleVoterCount))` luôn bảo đảm $\ge 1$. Đề xuất không bao giờ có thể tự động đạt `Ready` khi chưa có phiếu tán thành.

11. **Cơ chế hủy đề xuất của Giảng viên bị đình chỉ hoặc ngừng hoạt động (Quyết định 1):**
    - Khi một Giảng viên bị System Admin đình chỉ hoặc ngừng hoạt động (`isActive == false`), System Admin (trên mọi lớp) hoặc Advisor của chính lớp đó có quyền gọi hàm `cancelSuspendedLecturerProposal` để hủy các đề xuất đang ở trạng thái `Pending` hoặc `Ready` do giảng viên đó tạo ra.
    - Đề xuất chuyển sang trạng thái `Cancelled`, lưu thông tin kiểm toán (`cancelledBy`, `cancelledAt`, `reason = LecturerSuspended`), phát sự kiện `ProposalCancelled`.
    - Tuyệt đối không cho phép hủy đề xuất do Class Officer tạo ra, không cho phép hủy đề xuất của giảng viên đang hoạt động bình thường (`isActive == true`), và không thể hủy đề xuất đã giải ngân (`Executed`).
    - Tiền quỹ của lớp được bảo toàn nguyên vẹn; lịch sử phiếu đã bỏ được lưu trữ đầy đủ để đối soát.

---

### 7.2. Các điểm kỹ thuật đã được giải quyết dứt điểm và lưu ý triển khai

Ba câu hỏi kỹ thuật mở trước đây đã được nhóm thống nhất và giải quyết dứt điểm theo 3 quyết định mới:

1. **Xử lý yêu cầu chi khi Giảng viên bị đình chỉ (Đã giải quyết theo Quyết định 1):**
   - *Kết luận:* Thống nhất áp dụng cơ chế hủy có kiểm soát khẩn cấp. System Admin (toàn hệ thống) và Advisor (trong phạm vi lớp phụ trách) có quyền gọi hàm `cancelSuspendedLecturerProposal` để hủy đề xuất `Pending` hoặc `Ready` của giảng viên có `isActive == false`, chuyển trạng thái sang `Cancelled`. Đề xuất do Ban cán sự tạo ra hoặc đề xuất của giảng viên đang hoạt động bình thường không bị ảnh hưởng.

2. **Trạng thái lưu trữ và cơ chế chuyển đổi cho đề xuất `Ready` quá hạn giải ngân (Đã giải quyết theo Quyết định 2):**
   - *Kết luận:* Thống nhất bổ sung trạng thái lưu trữ chính thức `Expired` vào `enum ProposalState` (chuẩn hóa gồm 6 trạng thái: `Pending`, `Ready`, `Executed`, `Failed`, `Cancelled`, `Expired`). Áp dụng cơ chế **Lazy State Transition** (không giả định blockchain tự chạy ngầm):
     - Hàm `executeProposal`: Lần đầu gọi khi quá hạn `closedAt + 5 days` sẽ không chuyển ETH, cập nhật trạng thái sang `Expired`, phát event `ProposalExpired` và kết thúc thành công. Lệnh gọi tiếp theo khi đã ở `Expired` sẽ bị revert.
     - Hàm công khai `expireProposal(classId, proposalId)`: Bất kỳ ai cũng có thể gọi để chuyển trạng thái on-chain sang `Expired` khi đã quá hạn 5 ngày sau khi đóng đợt học.

3. **Cơ chế biểu quyết khi tạo đề xuất lúc chưa có Ban cán sự (Đã giải quyết theo Quyết định 3):**
   - *Kết luận:* Loại bỏ hoàn toàn tình huống tạo đề xuất khi lớp chưa có Ban cán sự. Quy tắc chốt: Bắt buộc lớp phải có ít nhất 1 Class Officer đủ điều kiện (`eligibleVoterCount >= 1`) mới được tạo đề xuất chi. Nếu `eligibleVoterCount == 0`, giao dịch tạo đề xuất lập tức bị revert với lỗi `NoEligibleVoters`. Lớp cần được Advisor bổ sung ít nhất 1 Ban cán sự trước khi có thể khởi tạo bất kỳ yêu cầu chi nào.

*Lưu ý kỹ thuật cho giai đoạn cài đặt Smart Contract:*
- Khai báo đầy đủ `enum ProposalState { Pending, Ready, Executed, Failed, Cancelled, Expired }`.
- Sử dụng Custom Errors thay vì chuỗi `require` dài để tối ưu gas: `NoEligibleVoters()`, `LecturerNotSuspended()`, `ProposalNotCancellable()`, `ProposalAlreadyExpired()`, `ExecutionDeadlineExceeded()`, `Unauthorized()`, `PhaseNotClosed()`, `PaymentFailed()`.
- Tuân thủ mẫu Checks-Effects-Interactions trong mọi hàm thay đổi trạng thái, đặc biệt là `executeProposal` và `cancelSuspendedLecturerProposal`.
- **Đặc tả chi tiết các sự kiện (Events) on-chain theo quy ước AGENTS.md:**
  - `event ProposalCreated(uint256 indexed classId, uint256 indexed proposalId, address indexed proposer, uint256 amount, address recipient, uint256 eligibleVoterCount, uint256 minimumApprovals, uint256 votingDeadline);`
  - `event VoteCast(uint256 indexed classId, uint256 indexed proposalId, address indexed voter, uint256 currentApprovals);`
  - `event ProposalReady(uint256 indexed classId, uint256 indexed proposalId, uint256 approvalsCount);`
  - `event ProposalExecuted(uint256 indexed classId, uint256 indexed proposalId, address indexed executor, address recipient, uint256 amount, uint256 timestamp);`
  - `event ProposalFailed(uint256 indexed classId, uint256 indexed proposalId, address indexed caller, uint256 timestamp);`
  - `event ProposalCancelled(uint256 indexed classId, uint256 indexed proposalId, address indexed caller, address lecturer, bytes32 reason, uint256 timestamp);`
  - `event ProposalExpired(uint256 indexed classId, uint256 indexed proposalId, address indexed caller, uint256 timestamp);`
  - `event DepositReceived(uint256 indexed classId, address indexed sender, uint256 amount, uint256 timestamp);`
  - `event TeachingAssignmentsBatchUpdated(bytes32 indexed batchId, string indexed phaseId, uint256 recordCount, uint256 timestamp);`
  - `event TeachingAssignmentRecordUpdated(bytes32 indexed batchId, string indexed phaseId, uint256 indexed classId, string lecturerId, address lecturerAddress, string courseCode, bool isValid, uint256 timestamp);`
  - `event PhaseClosed(string indexed phaseId, uint256 closedAt, address indexed admin);`

4. **Hàm tra cứu trạng thái hiệu lực theo thời gian thực (`getProposalState` - View Function):**
   - Do hàm `view` trên Solidity không thể ghi hay thay đổi dữ liệu trên storage blockchain, SPEC định nghĩa rõ ràng cơ chế tính toán trạng thái hiệu lực (`effective state`) để phục vụ frontend/dApp hiển thị chính xác theo thời gian thực:
     - Nếu storage `state == Executed`, `Failed`, `Cancelled`, `Expired`: Trả về trực tiếp trạng thái đó (không bao giờ bị tính lại thành trạng thái khác).
     - Nếu storage `state == Pending`:
       - Nếu `block.timestamp > creationTime + 7 days` (quá hạn 7 ngày mà chưa đạt `Ready`): Hàm view trả về `ProposalState.Failed`.
       - Ngược lại: Trả về `ProposalState.Pending`.
     - Nếu storage `state == Ready`:
       - Nếu đợt học căn cứ đã đóng (`phase.status == CLOSED`) VÀ `block.timestamp > closedAt + 5 days`: Hàm view trả về `ProposalState.Expired`.
       - Ngược lại (bao gồm cả trường hợp đợt học còn `OPEN` hoặc `block.timestamp <= closedAt + 5 days`): Trả về `ProposalState.Ready`.
   - Để đồng bộ trạng thái thực tế lên storage nhằm phục vụ kiểm toán hoặc logic on-chain khác, bất kỳ ai cũng có thể gửi giao dịch công khai (`failProposal` hoặc `expireProposal`) như đã định nghĩa.

### 7.3. Các quyết định mới được nhóm xác nhận (Confirmed Group Decisions)

1. **Hiệu lực biểu quyết của Class Officer bị xóa hoặc thu hồi quyền giữa quá trình biểu quyết (QA-04):**
   - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN CHÍNH THỨC: PHƯƠNG ÁN A (KIỂM TRA KÉP)**.
   - *Nội dung quyết định:* Khi một Class Officer bị Advisor xóa khỏi danh sách Ban cán sự của lớp sau khi đề xuất được tạo:
     - Tại thời điểm gọi hàm `voteProposal`, hợp đồng thực hiện kiểm tra kép: người gọi bắt buộc phải có tên trong snapshot cử tri chốt (`isSnapshotVoter == true`) VÀ hiện tại vẫn đang là Ban cán sự hợp lệ (`isClassOfficer == true`).
     - Người đã bị xóa khỏi Ban cán sự không còn quyền bỏ phiếu cho đề xuất cũ (giao dịch bị revert).
     - Mẫu số `eligibleVoterCount` và ngưỡng phê duyệt tối thiểu `minimumApprovals` đã chốt lúc tạo đề xuất được bảo toàn nguyên vẹn, không đổi.
     - Lá phiếu đã bỏ hợp lệ trước thời điểm bị xóa vẫn được giữ nguyên hiệu lực.

2. **Cơ chế xử lý yêu cầu hoàn tiền do đóng nhầm:**
   - *Trạng thái:* **ĐÃ ĐƯỢC NHÓM XÁC NHẬN VỀ MẶT NGUYÊN TẮC NGHIỆP VỤ; ĐANG CHỜ CHỐT PHƯƠNG ÁN THIẾT KẾ KỸ THUẬT**.
   - *Nội dung quyết định:*
     - Phân định rõ: các khoản đóng góp hợp lệ không thể hoàn trả theo yêu cầu cá nhân thông thường; các giao dịch nạp nhầm (sai lớp, sai số tiền, lỗi thao tác) được phép gửi yêu cầu xem xét hoàn tiền theo 10 nguyên tắc thẩm định độc lập.
     - Quản trị viên hệ thống (System Admin) tuyệt đối không có quyền tự ý rút tiền quỹ hay can thiệp chuyển ETH trực tiếp.
     - Bắt buộc kiểm soát chống hoàn tiền trùng lặp on-chain bằng biến mapping trạng thái `isRefunded[txHash] = true`.
     - Nhóm sẽ biểu quyết chọn phương án kỹ thuật triển khai: Phương án A (Hàm hoàn tiền chuyên biệt có sự đồng thuận của Cố vấn và Ban cán sự) hay Phương án B (Quy trình đề xuất hoàn tiền riêng biệt do Ban cán sự biểu quyết 51%) trước khi viết Smart Contract ở Lab 9.

