[English](./README.md) / VI

## Giới thiệu về `Truffle`

`Truffle` là một môi trường phát triển, testing framework, asset pipeline cho blockchains sử dụng **Ethereum Virtual Machine (EVM)**. `Truffle` giúp cho việc viết dapp front-end dễ dàng hơn và dễ đoán hơn. Thường sử dụng ngôn ngữ lập trình JavaScript.

- Tích hợp hợp đồng thông minh (smart contract), liên kết (linking), deployment, và quản lý nhị phân (binary management).
- Thử nghiệm contract tự động để phát triển nhanh chóng.
- Cho phép viết kịch bản, mở rộng deployment và di dời framework.
- Quản lý network để triển khai cho bất cứ mạng công cộng và mạng riêng nào.
- Gói quản lý với  EthPM & NPM, sử dụng tiêu chuẩn [ERC190].(https://github.com/ethereum/EIPs/issues/190)
- Bảng điều khiển tương tác giúp giao tiếp trực tiếp với Contract.
- Xây dựng cấu hình pineline, hỗ trợ tích hợp chặt chẽ.
- Trình chạy script ngoại thực thi các script trong môi trường Truffle.

### [Khởi Động Nhanh Truffle](https://www.trufflesuite.com/docs/truffle/quickstart)

## Giới thiệu về Dự Án

### Cấu Trúc

- `Contracts/` : Danh mục các hợp đồng Solidity
- `Migratations/` : Thư mục cho các tập tin triển khai scriptable 
- `test/` : Thư mục các test file dùng để test các ứng dụng và hợp đồng
- `truffle-config.js`: Cấu hình file Truffe

### Files

1. `contracts/SimpleToken.sol`: Đây là Smart contract `erc20` được viết bằng `Solidity`.
2. `migrations/1_initial_migration.js`: Tệp này là tập lệnh di chuyển (deployment) cho hợp đồng `Migrations` được tìm thấy trong file `Migrations.sol`.
   
   > 1. Lưu ý rằng, filename bao gồm tiền tố là một số và hậu tố là một mô tả. Tiền tố được đánh số bắt buộc để ghi lại quá trình di chuyển có chạy thành công hay không. Phần miêu tả ở hậu tố phụ thuộc vào khả năng đọc hiểu.
   > 2. `Migrations.sol` là một file Solidity riêng biệt giúp quản lý và cập nhật trạng thái smart contract đã được bạn triển khai. File này đi kèm với mọi dự án Truffle, và thường **không thể chỉnh sửa**.

3. `truffle-config.js`: Đây là Tệp cấu hình Truffle, dùng để setting thông tin network và các cài đặt khác liên quan đến dự án. Đừng lo lắng nếu thấy tệp trống vì có thể sử dụng lệnh Truffle có sẵn một số tích hợp mặc định 

 
  



