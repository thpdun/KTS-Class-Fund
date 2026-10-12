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

1. Tiền được nạp vào quỹ lớp.
2. Giảng viên hoặc thành viên ban cán sự tạo yêu cầu chi.
3. Các thành viên ban cán sự kiểm tra thông tin và xác nhận yêu cầu.
4. Yêu cầu cần đạt tối thiểu **4/5 lượt xác nhận** trong thời hạn **7 ngày**.
5. Khi đủ số lượt xác nhận, yêu cầu chuyển sang trạng thái `Ready`.
6. Một thành viên ban cán sự gọi `Execute`. Smart Contract kiểm tra các điều kiện như thời hạn, số dư và trạng thái yêu cầu trước khi chuyển tiền.
7. Sau khi chuyển tiền thành công, yêu cầu chuyển sang trạng thái `Executed` và giao dịch được ghi nhận.

Mỗi yêu cầu chỉ được thực hiện một lần. Nếu hết thời hạn mà chưa đủ số lượt xác nhận, yêu cầu chuyển sang trạng thái `Expired`.

## 7. Công nghệ sử dụng dự kiến

- **Solidity:** Xây dựng Smart Contract.
- **Remix IDE:** Biên dịch, triển khai và kiểm thử hợp đồng thông minh.
- **Ethereum Sepolia Testnet:** Môi trường thử nghiệm giao dịch.
- **MetaMask:** Kết nối ví và tương tác với Blockchain.
- **Web DApp:** Giao diện theo dõi và quản lý quỹ lớp.
- **Git và GitHub:** Quản lý mã nguồn và phối hợp làm việc nhóm.

*Lưu ý: Phiên bản thử nghiệm sử dụng ETH trên mạng thử nghiệm, không sử dụng tiền thật.*

## 8. Cấu trúc dự án

```text
kts-class-fund/
├── README.md
├── AGENTS.md
├── docs/
│   ├── PROJECT_PLAN.md
│   ├── SPEC.md
│   ├── ECONOMIC_RULES.md
│   └── AI_JOURNAL.md
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

## 9. Kế hoạch phát triển

Nhóm sẽ phát triển KTS Class Fund xuyên suốt các Lab, theo các giai đoạn:

- **Giai đoạn 1:** Xác định vấn đề, yêu cầu và quy tắc nghiệp vụ của quỹ lớp.
- **Giai đoạn 2:** Xây dựng, biên dịch và kiểm thử Smart Contract.
- **Giai đoạn 3:** Rà soát các quy tắc, quyền truy cập và các trường hợp ngoại lệ.
- **Giai đoạn 4:** Phát triển Web DApp, kết nối ví và tích hợp Smart Contract.
- **Giai đoạn 5:** Kiểm thử tổng thể, lưu bằng chứng và hoàn thiện sản phẩm.

## 10. Kết quả mong đợi

Hoàn thiện một nguyên mẫu KTS Class Fund có thể chạy thử trên môi trường Blockchain, hỗ trợ quản lý quỹ lớp Kinh tế số, theo dõi thu – chi, xác nhận yêu cầu chi theo quy tắc đã thống nhất và kiểm tra lịch sử giao dịch.


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
