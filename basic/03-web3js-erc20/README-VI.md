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
```
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

4. Xuất `contractFile` Object


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

6. Lấy `abi` và `bin`

Khi deploy smart contract, chúng ta cần 2 đối số quan trọng của smart contract đó là `bytecode` và `abi`. Ở bước 1, chúng ta đã tải `SimpleToken` object đã được biên dịch, do đó có thể nhận `bytecode` và `abi` từ `SimpleToken` object.


```
const bytecode = contractFile.evm.bytecode.object;
const abi = contractFile.abi;
```
7. Nhận instance của contract

Có thể tạo instance bằng `abi` đã nhận ở bước trước

```
const deployContract = new web3.eth.Contract(abi);
```

8. Tạo giao dịch `deployContract`

```
const deployTx = deployContract.deploy({
    data: bytecode,
    arguments: ['DAPPLEARNING', 'DAPP', 0, 10000000],
});
```

| Cho đến hiện tại, giao dịch chưa được deploy vào blockchain

9. Kí giao dịch

Sử dụng Private key để kí giao dịch.

```
const deployTransaction = await web3.eth.accounts.signTransaction(
    {
    data: deployTx.encodeABI(),
    gas: '8000000',
    },
    account_from.privateKey
);
```
10. Deploy contract

Gửi giao dịch `deployTransaction` đã kí đến Blockchain, bạn sẽ nhận được biên lai giao dịch. Địa chỉ hợp đồng (contract address) cũng được gửi kèm trong biên lai.

```
const deployReceipt = await web3.eth.sendSignedTransaction(deployTransaction.rawTransaction);
console.log(`Contract deployed at address: ${deployReceipt.contractAddress}`);
```
11. Tạo giao dịch chuyển khoản (a tranfer transaction)

Tác vụ dưới đây là ví dụ cho một cuộc giao dịch chuyển khoản ERC20, người nhận là `receiver account`, và số lượng giao dịch là 100000 token.

```
const transferTx = erc20Contract.methods.transfer(receiver, 100000).encodeABI();
```

12. Kí và gửi giao dịch

```
const transferReceipt = await web3.eth.sendSignedTransaction(transferTransaction.rawTransaction);
```

13. Kiểm tra số dư `receiver account`

Sau khi giao dịch đã được gửi đi, bạn có thể log và kiểm tra số dư để đảm bảo số dư là chính xác.

```
erc20Contract.methods
    .balanceOf(receiver)
    .call()
    .then((result) => {
    console.log(`The balance of receiver is ${result}`);
    });
```

## Lưu Ý

- `infura` không hỗ trợ `sendTransaction`, chỉ hỗ trợ `sendRawtransaction`
- `infura` không gọi `eth_sendTransaction`, vì vậy bạn cần mở khóa account trên node `ethereum`. Biết thêm thông tin chi tiết vui lòng truy cập [this](https://ethereum.stackexchange.com/questions/70853/the-method-eth-sendtransaction-does-not-exist-is-not-available-on-infura)

## Tài Liệu Tham Khảo

- Mocha tutorial: http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html
- Mocha blog: https://pcaaron.github.io/pages/fe/block/improve4.html#%E8%B7%91%E6%B5%8B%E8%AF%95
- ERC20 doc: https://docs.openzeppelin.com/contracts/2.x/api/token/erc20





