[English](./README.md) / Vietnamese
# Mô Tả
Demo code dưới đây cung cấp cho Dev góc nhìn khái quát về cách kí gửi và nhận biên lai giao dịch cũng như xác minh các kết quả thực thi. Các ví dụ cũng đồng thời cung cấp các mã giám sát sự kiện (the event monitoring code) để giúp Dev tiện theo dõi các hoạt động sự kiện một hoặc nhiều lần.

 # Bắt Đầu
 
 ## Hàm (functions) trong [Smart Contract](Incrementer.sol)
 - `Constructor`: Hàm khởi tạo (Constructor Function) được gọi khi smart contract được triển khai, đồng thời sẽ khởi tạo `number` thành `_initialNumber`;
 - `increment`: hàm gia tăng `number` từ `value` đã cho
 - `rest` : Hàm đặt giá trị của `number` về 0 
 - `getNumber` : Hàm lấy `number`

## Khởi động
 
 1. Các cài đặt phụ thuộc: `npm install`
 2. Sao chép cấu hình file: `cp .env.example .env`
 3. Chỉnh sử cấu hình file thành `vim .env`, sao chép ID dự án và khóa riêng tư vào file `.env`
    ```text
    PRIVATE_KEY=YOUR_PRIVATE_KEY
    INFURA_ID=YOUR_PROJECT_ID
    ```  
  4. Khởi động `index.js` file: `node index.js`
 
 # Trình phiên dịch Source Code
 ## `compile.js`
 Bạn không thể sử dụng trực tiếp file `.sol` do đó cần đưa nó về hệ nhị phân
 ### Load tệp smart contract `Incrementer.sol` thành biến `source`.
 
 ```js
// Load contract
const source = fs.readFileSync("Incrementer.sol", "utf8");
```
### Biên dịch tệp smart contract
```js
const input = {
  language: "Solidity",
  sources: {
    "Incrementer.sol": {
      content: source,
    },
  },
  settings: {
    outputSelection: {
      "*": {
        "*": ["*"],
      },
    },
  },
};

const tempFile = JSON.parse(solc.compile(JSON.stringify(input)));
```

| Note: Bản cập nhật Solidity `0.8.0`, trình biên dịch ở mỗi bản cập nhật là khác nhau.

### Lấy Contract Object

```js
const contractFile = tempFile.contracts["Incrementer.sol"]["Incrementer"];
```  

## Xuất `ContractFile` Object

Để sử dụng `ContractFile` Object trong các file `js` khác bạn cần export chúng
```js
module.exports = contractFile;
```  

## `index.js`

### 1. Tải `Incrementer` smart contract từ file `compile`
```js
const contractOfIncrementer = require("./compile");
```

### 2. Đọc private key từ các biến môi trường (environment variables)
Bởi vì mục đích bảo mật nên private key không được xem như là một hard coded, tuy nhiên nó vẫn có thể được đọc như những biến môi trường. 
Khi khởi động task, plugin `dotenv` tự động đọc các cấu hình trên file `.env` và load chúng dưới dạng các biến môi trường, sau đó có thể sử dụng các `private key` và các biến môi trường khác thông qua `process.env` .
Code: 
```js
require("dotenv").config();
const privatekey = process.env.PRIVATE_KEY;
```
### 3. Tạo instance `web3`

`web3` là API chính của thư viện `web3js`, được dùng để tương tác với blockchain.
```js
// Create web3 with goerli provider，you can change goerli to other testnet
const web3 = new Web3(
  "https://goerli.infura.io/v3/" + process.env.INFURA_ID
);
```
| Note: `INFURA_ID` chính là `PROJECT ID` của dự án `Infura` mà bạn đã tạo trong phần [Chuẩn bị](../01-web3js-deploy/README-vn.md)

### 4. Lấy địa chỉ `Account`
Mỗi blockchain sẽ cung cấp cho người dùng một `address` duy nhất thông qua private key. Bạn có thể sử dụng API `we3.eth.accounts.privateKeyToAccount` để lấy địa chỉ `account` bằng cách chuyển private key thành một parameter.
```js
// Tạo account từ privatekey
const account = web3.eth.accounts.privateKeyToAccount(privatekey);
const account_from = {
  privateKey: privatekey,
  accountAddress: account.address,
};
```

### 5. Nhận `bytecode` và `abi`
Khi deploy smart contract, bạn cần chỉ rõ `bytecode` và `abi` của smart contract đó. `Bytecode` là mã nhị phân đã được biên dịch của smart contract, còn `abi` (Application Binary Interface) chính là giao diện của smart contract.
```js
const bytecode = contractOfIncrementer.evm.bytecode.object;
const abi = contractOfIncrementer.abi;
```  

### 6. Nhận Contract instance
Ở bước cuối cùng bạn đã nhận `bytecode` và `abi`, do đó bạn có thể tạo contract instance thông qua `abi`.
```js
// Create contract instance
  const deployContract = new web3.eth.Contract(abi);
```
### 7. Tạo giao dịch (transaction) của `deployContract`

```js
// Create Tx
const deployTx = deployContract.deploy({
    data: bytecode,
    arguments: [5],
});
```  

### 8. Kí giao dịch (Sign the transaction)
Sử dụng khóa riêng tư (private key) để kí giao dịch.
```js
// Sign Tx
const deployTransaction = await web3.eth.accounts.signTransaction(
    {
        data: deployTx.encodeABI(),
        gas: 8000000,
    },
    account_from.privateKey
);
```

### 9. Gửi giao dịch (send the transaction) / Triển khai smart contract (Deploy smart contract)
Gửi `deploy` giao dịch (deploy transaction) của bạn đến blockchain, nhận biên lai và lấy địa chỉ hợp đồng (contract address) từ biên lai này.
```js
const deployReceipt = await web3.eth.sendSignedTransaction(
    deployTransaction.rawTransaction
);
console.log(`Contract deployed at address: ${deployReceipt.contractAddress}`);
```

### 10. Tải contract instance từ blockchain thông qua địa chỉ hợp đồng (contract address)










