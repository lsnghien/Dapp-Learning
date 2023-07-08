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

 Tranfe rFrom: (Gọi bởi bên thứ 3) Chuyển một lượng token ERC20 nhất định từ tài khoản nguồn đến tài khoản đích.
 
- IERC20 dữ liệu Meta:

 Name: Lấy tên của token
  
 Symbol: Lấy biểu tượng của token
  
 Decimals: Lấy số thập phân (decimals) của token
  
 ### Khởi động
  
 1. Cài đặt các thành phần phụ thuộc (Install Dependencies): `npm install`
 2. Sao chép cấu hình file: `cp .env.example .env`
 3. Chỉnh sử cấu hình file thành `vim .env`và sao chép ID dự án và khóa riêng tư vào file `.env`
    ```text
    PRIVATE_KEY=YOUR_PRIVATE_KEY
    INFURA_ID=YOUR_PROJECT_ID
    ```  
  4. Khởi động `index.js` file: `node index.js`
 
 # Tìm Hiểu Về Thư Viện (source code)
  ## `compile.js`
  Bạn không thể sử dụng trực tiếp file `.sol` do đó cần đưa nó về hệ nhị phân.
  1. Tải tệp smart contract `Incrementer.sol` vào biến `source`.
  ```js
 // Load contract
 const source = fs.readFileSync("Incrementer.sol", "utf8");
 ```
2. Biên dịch mã nguồn tệp smart contract
// Ngôn ngữ biên dịch solidity
const input = {
    language: 'Solidity',
    sources: {
    'SimpleToken.sol': {
        content: source,
    },
    },
    settings: {
    outputSelection: {
        '*': {
        '*': ['*'],
        },
    },
    },
};

const tempFile = JSON.parse(solc.compile(JSON.stringify(input)));
```
| Lưu ý: Phiên bản solidity sử dụng trong tác vụ này là version `0.8.0`, ở mỗi phiên bản khác nhau sẽ có cách biên dịch khác nhau

3. Lấy Contract Object hệ nhị phân
Các solidity object sau khi biên dịch thành công thì chứa rất rất nhiều thuộc tính (properties) và giá trị (values). Tuy nhiên chúng ta chỉ cần lấy contract object, vì vậy có thể lấy `SimpleToken` contract object bằng cách truy cập các thuộc tính đối tượng (object properties).

  ```js
const contractFile = tempFile.contracts['SimpleToken.sol']['SimpleToken'];
```

4. Xuất `contractFile` Objec


```js
module.exports = contractFile;
```

---

### `index.js`

1. Tải `SimpleToken` smart contract từ file `biên dịch` (`compile` file)

```js
const contractFile = require('./compile');
```

2. Tải khóa riêng tư (private key)
   
Vì mục đích bảo mật, các private key không được mã hóa cứng, nhưng có thể đọc chúng dưới dạng các biến môi trường. Khi khởi động task, các `dotenv` plugin sẽ tự động đọc cấu hình trong tệp `.env` và tải chúng về dưới dạng biến môi trường. Bạn có thể sử dụng private key và các biến môi trường khác thông qua `process.env`.

```js
require('dotenv').config();
const privatekey = process.env.PRIVATE_KEY;
 ```
3. Tạo một tài khoản `người nhận` (`receiver` account) để kiểm tra
 
```js
const receiver = '0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266';
```

4. Xây dựng đối tượng `web3` (`web3` object)

```js
const web3 = new Web3(new Web3.providers.HttpProvider('https://goerli.infura.io/v3/' + process.env.INFURA_ID));
```
| Note: `INFURA_ID` chính là `PROJECT ID` của dự án `Infura` mà bạn đã tạo trước đó. Xem lại:[task](../01-web3js-deploy/README.md)

5. Nhận địa chỉ `account`

Trên blockchain, địa chỉ người dùng là riêng biệt cho từng cá nhân. Bạn có thể lấy `địa chỉ` bằng private key.
Trong tác vụ này, bạn có thể sử dụng API  `we3.eth.accounts.privateKeyToAccount` để nhận địa chỉ account bằng cách truyền private key như một đối số (parameter)

```js
const account = web 3.eth.accounts.privateKeyToAccount(privatekey);
const account_from = {
    privateKey: account.privateKey,
    accountaddress: account.address,
};
```




