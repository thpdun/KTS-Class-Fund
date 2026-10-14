# KTS Class Fund
### Smart Contract for Digital Economics Class Fund Management

## 1. Giới thiệu dự án

**KTS Class Fund** là dự án xây dựng hệ thống quản lý quỹ lớp Kinh tế số bằng công nghệ Blockchain và Smart Contract, nhằm hỗ trợ quản lý các khoản thu – chi minh bạch, dễ theo dõi và có cơ chế nhiều người xác nhận trước khi thực hiện khoản chi.

Dự án được thực hiện trong khuôn khổ học phần **ECO2432 – Tiền điện tử và Hợp đồng thông minh** tại Trường Đại học Kinh tế, Đại học Huế.

## 2. Thành viên nhóm

| STT | Họ và tên | Mã sinh viên |
|---|---|---|
| 1 | Trần Hoàng Phương Dung | 23K4300004 |
| 2 | Võ Ngọc Tâm Phúc | 23K4300015 |

## 3. Vấn đề cần giải quyết

Việc quản lý quỹ lớp Kinh tế số theo phương pháp thủ công có thể gây khó khăn trong việc theo dõi số dư, kiểm tra các khoản thu – chi và xác định quá trình xác nhận một khoản chi.

KTS Class Fund hướng đến giải quyết vấn đề này bằng cách sử dụng Smart Contract để thực thi các quy tắc quản lý quỹ nhất quán, đồng thời ghi nhận giao dịch trên Blockchain để thuận tiện cho việc kiểm tra lịch sử.

## 4. Mục tiêu dự án

- Quản lý số dư và các khoản thu của quỹ lớp Kinh tế số.
- Cho phép tạo yêu cầu chi với thông tin về mục đích, số tiền và người nhận.
- Thiết lập cơ chế nhiều thành viên ban cán sự xác nhận trước khi thực hiện khoản chi.
- Kiểm tra các điều kiện trước khi chuyển tiền, hạn chế chi sai hoặc chi trùng.
- Cho phép sinh viên theo dõi tình hình thu – chi và lịch sử giao dịch.
- Xây dựng giao diện Web DApp để người dùng tương tác với Smart Contract.

## 5. Đối tượng sử dụng

- **Sinh viên lớp Kinh tế số:** Theo dõi số dư quỹ, các khoản thu – chi, yêu cầu chi và lịch sử giao dịch.
- **Giảng viên:** Tạo yêu cầu chi phục vụ các hoạt động của lớp và theo dõi trạng thái xử lý.
- **Ban cán sự lớp:** Tạo yêu cầu chi, xác nhận yêu cầu và thực hiện khoản chi theo các quy tắc của hệ thống.

## 6. Quy trình hoạt động dự kiến

1. Tiền được nạp vào quỹ lớp theo từng mã lớp (`classId`). Đối với các khoản đóng góp hợp lệ, tiền trở thành tài sản chung của lớp và không có cơ chế hoàn trả theo yêu cầu cá nhân thông thường. Đối với các giao dịch đóng nhầm (chọn sai lớp, sai số tiền, lỗi giao dịch), hệ thống cho phép người dùng gửi yêu cầu xem xét hoàn tiền theo quy trình thẩm định độc lập, chặt chẽ, chống hoàn trùng và bảo đảm an toàn quỹ. Tiền quỹ chỉ được giải ngân thông qua đề xuất chi hợp lệ đã được Ban cán sự biểu quyết thông qua, hoặc qua quy trình giải quyết hoàn tiền đóng nhầm được phê duyệt hợp lệ.
2. Giảng viên, Cố vấn học tập hoặc Ban cán sự tạo đề xuất chi gắn với mã đợt học (`phaseId`) hợp lệ của lớp.
3. Các thành viên ban cán sự kiểm tra thông tin và thực hiện biểu quyết chấp thuận đề xuất.
4. Đề xuất cần đạt tối thiểu **51% số phiếu chấp thuận của Ban cán sự đủ điều kiện tại thời điểm tạo đề xuất** theo công thức:
   $$\text{requiredVotes} = \max(1, \lceil 0.51 \times N \rceil)$$
   với $N$ là số lượng cử tri Ban cán sự chốt trong snapshot lúc tạo đề xuất ($N \ge 1$), trong thời hạn biểu quyết **7 ngày**.
5. Khi nhận đủ số lượt chấp thuận trong hạn, đề xuất chuyển sang trạng thái `Ready`.
6. Một thành viên ban cán sự đương nhiệm gọi `executeProposal`. Smart Contract kiểm tra các điều kiện như thời hạn (trong vòng 5 ngày kể từ khi đợt học đóng nếu đợt học đã `CLOSED`), số dư khả dụng và trạng thái đề xuất trước khi chuyển tiền.
7. Sau khi chuyển tiền thành công, đề xuất chuyển sang trạng thái `Executed` và giao dịch được ghi nhận trên Blockchain.

Mỗi đề xuất chỉ được giải ngân duy nhất một lần. Nếu hết thời hạn 7 ngày mà chưa đạt đủ số phiếu chấp thuận tối thiểu, đề xuất chuyển sang trạng thái `Failed`. Nếu đề xuất đã `Ready` nhưng quá thời hạn 5 ngày sau khi đợt học đóng (`block.timestamp > closedAt + 5 days`), đề xuất chuyển sang trạng thái `Expired` và không được phép chuyển tiền.

## 7. Tài liệu dự án

Các tài liệu chính của dự án được lưu trữ trong thư mục `docs/`:

- [Kế hoạch dự án (PROJECT_PLAN.md)](docs/PROJECT_PLAN.md): Phân công vai trò thành viên, mốc hoàn thành và sản phẩm bàn giao từ Lab 8 đến Lab 15.
- [Đặc tả kỹ thuật (SPEC.md)](docs/SPEC.md): Đặc tả chi tiết yêu cầu, đầu vào/đầu ra, quy tắc R0–R10 và 44 ca kiểm thử chuẩn hóa.
- [Quy tắc kinh tế và vận hành (ECONOMIC_RULES.md)](docs/ECONOMIC_RULES.md): 19 quy tắc kinh tế chuẩn hóa (ER-01 đến ER-19), 4 phần bắt buộc và 6 kịch bản phân tích rủi ro.
- [Nhật ký làm việc với AI (AI_JOURNAL.md)](docs/AI_JOURNAL.md): Ghi nhận toàn bộ quá trình tương tác, kiểm tra và hiệu chỉnh đầu ra từ công cụ AI.
- [Checklist Lab 8 (LAB_08_CHECKLIST.md)](docs/LAB_08_CHECKLIST.md): Danh mục kiểm tra sự sẵn sàng của hồ sơ bàn giao Lab 8.
- [Tài liệu thiết kế kiến trúc (ARCHITECTURE.md)](docs/ARCHITECTURE.md): Thiết kế kỹ thuật Smart Contract, phân chia contract, mô hình dữ liệu và sơ đồ luồng.

## 8. Công nghệ sử dụng dự kiến

- **Solidity (`^0.8.20`):** Xây dựng Smart Contract tuân thủ tiêu chuẩn OpenZeppelin 5.x.
- **Remix IDE & Hardhat:** Biên dịch, triển khai và kiểm thử hợp đồng thông minh.
- **Ethereum Sepolia Testnet:** Môi trường thử nghiệm giao dịch.
- **MetaMask:** Kết nối ví và tương tác với Blockchain.
- **Web DApp:** Giao diện theo dõi và quản lý quỹ lớp.
- **Git và GitHub:** Quản lý mã nguồn và phối hợp làm việc nhóm.

*Lưu ý: Phiên bản thử nghiệm sử dụng ETH trên mạng thử nghiệm Sepolia, không sử dụng tiền thật.*

## 9. Cấu trúc dự án

```text
kts-class-fund/
├── README.md
├── AGENTS.md
├── docs/
│   ├── PROJECT_PLAN.md
│   ├── SPEC.md
│   ├── ECONOMIC_RULES.md
│   ├── AI_JOURNAL.md
│   ├── LAB_08_CHECKLIST.md
│   └── ARCHITECTURE.md
├── contracts/
│   ├── training/
│   └── project/
│       └── ProjectCore.sol
├── test/
├── web/
└── evidence/
    ├── lab-08/
    ├── lab-09/
    └── ...
```

## 10. Kế hoạch phát triển

Nhóm sẽ phát triển KTS Class Fund xuyên suốt các Lab, theo các giai đoạn:

- **Giai đoạn 1 (Lab 8):** Xác định vấn đề, yêu cầu và quy tắc nghiệp vụ, hoàn thiện đặc tả và quy tắc kinh tế v0.1.
- **Giai đoạn 2 (Lab 9–11):** Xây dựng, biên dịch và kiểm thử Smart Contract, đảm bảo quy tắc kinh tế chạy đúng.
- **Giai đoạn 3 (Lab 12–13):** Gate Review 1, rà soát kiểm thử ca tấn công/gian lận và bảo mật.
- **Giai đoạn 4 (Lab 14):** Audit chéo giữa các nhóm.
- **Giai đoạn 5 (Lab 15):** Phát triển Web DApp, kết nối ví và công khai URL sản phẩm.

## 11. Kết quả mong đợi

Hoàn thiện một nguyên mẫu KTS Class Fund có thể chạy thử trên môi trường Blockchain Sepolia Testnet, hỗ trợ quản lý quỹ lớp Kinh tế số, theo dõi thu – chi, xác nhận yêu cầu chi theo ngưỡng 51% đã thống nhất và kiểm tra lịch sử giao dịch minh bạch.


# ECO2432 Web3 Starter

Kho khởi đầu dùng xuyên suốt 15 bài thực hành.

## Bắt đầu (thay cho bước "Fork kho" trong sổ tay)

Sổ tay ghi "Fork kho `hce-web3-starter`". Học kỳ này kho được phát dạng tệp nén, nên làm như sau:

1. Giải nén thư mục này vào máy, mở bằng Antigravity.
2. Đọc `AGENTS.md` trước khi yêu cầu công cụ AI sinh mã.
3. Sao chép `SPEC.md` và `AI_JOURNAL.md` cho từng bài.
4. Chỉ dùng ví thử nghiệm và mạng Sepolia; không dùng khóa ví có tiền thật.

Đưa lên GitHub (làm khi đã có tài khoản; cần trước khi nộp Lab 1):

```bash
git init -b main
git add .
git commit -m "chore: thiet lap moi truong lam viec"
git remote add origin https://github.com/<tai-khoan>/<ten-repo>.git   # repo tạo TRỐNG trên GitHub
git push -u origin main
```

Lab 8 (repo nhóm): một thành viên tạo repo trống mới, đưa nội dung thư mục này lên theo đúng các
lệnh trên, rồi mời các thành viên khác làm collaborator.

## Cấu trúc

- `contracts/training/`: hợp đồng mẫu dùng ở Lab 9, 10, 11 và 13
  (`TimeLockVault`, `VaultBuggy`, `ClassPoint`, `VulnerableBank`).
- `contracts/lab04/ClubTokens.sol`: ba token dùng cho Lab 4.
- `web/index.html`: giao diện mẫu dùng ở Lab 15.
- `prompt_templates.md`: mẫu câu lệnh có yêu cầu và tiêu chí kiểm chứng rõ ràng.

Các hợp đồng có chữ `Buggy`, `Vulnerable` hoặc cảnh báo trong mã đều chứa lỗi có chủ đích.

## Chạy hợp đồng

- Cách chính: mở Remix IDE (`https://remix.ethereum.org`), tạo tệp, dán mã. Remix tự tải thư viện
  `@openzeppelin/...`, không cần cài gì.
- Nếu Antigravity gạch đỏ dòng `import "@openzeppelin/..."`: đó là do máy chưa có thư viện, mã
  không sai. Muốn hết gạch đỏ thì cài Node.js rồi chạy `npm install` trong thư mục này (không bắt buộc).
