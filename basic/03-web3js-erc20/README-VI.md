[English](./README.md) / VI

# Web3js ERC20

Bài viết này khái quát cho Dev về cách tương tác cũng như tìm hiểu về giao diện cơ bản của ERC20 contract (Tạm dịch là hợp đồng ERC20).

## Bắt đầu
### Mô tả hàm Simple Token Contract

- IERC20:

 Total Supply: Lấy tổng số lượng token ERC20 trong contract.

 Balance Of:  Lấy số token ERC20 của một account cụ thể trong contract.

 Transfer: Chuyển token ERC20 đến một account cụ thể.

 Allowance: Lấy số token ERC20 của tài khoản nguồn (source account) mà tài khoản đích (target      account) có thể sử dụng.

 Approve: Cho phép tài khoản đích chuyển một số lượng token ERC20 nhất định.

 Tranfe rFrom: (gọi bởi bên thứ 3) Chuyển một lượng token ERC20 nhất định từ tài khoản nguồn đến tài khoản đích.
 
- IERC20 Meta data:

 Name: lấy tên của token
  
 Symbol: Lấy biểu tượng của token
  
 Decimals: Lấy số thập phân (decimals) của token
  
 ### Khởi động
  
 1. Các cài đặt phụ thuộc: `npm install`
 2. Sao chép cấu hình file: `cp .env.example .env`
 3. Chỉnh sử cấu hình file thành `vim .env`, sao chép ID dự án và khóa riêng tư vào file `.env`
    ```text
    PRIVATE_KEY=YOUR_PRIVATE_KEY
    INFURA_ID=YOUR_PROJECT_ID
    ```  
  4. Khởi động `index.js` file: `node index.js`
 
 # Hiểu về thư viện (source code)
  ## `compile.js`
  Bạn không thể sử dụng trực tiếp file `.sol` do đó cần đưa nó về hệ nhị phân
   ### Load tệp smart contract `Incrementer.sol` vào biến `source`.
 
  ```js
 // Load contract
 const source = fs.readFileSync("Incrementer.sol", "utf8");
 ```

  

  
 

