[English](./README.md) / VI

# Web3js ERC20

Bài viết này khái quát cho Dev về cách tương tác cũng như tìm hiểu về giao diện cơ bản của ERC20 contract (Tạm dịch là hợp đồng ERC20).

## Bắt đầu
### Mô tả hàm Simple Token Contract

- IERC20:

TotalSupply: Lấy tổng số lượng token ERC20 trong contract
balanceOf:  Lấy số token ERC20 của một account cụ thể trong contract
transfer: Chuyển token ERC20 đến một account cụ thể
allowance: Lấy số token ERC20 của tài khoản nguồn (source account) mà tài khoản đích (target      account) có thể sử dụng
approve: Cho phép tài khoản đích chuyển một số lượng token ERC20 nhất định.
tranferFrom: (gọi bởi bên thứ 3) Chuyển một lượng token ERC20 nhất định từ tài khoản nguồn đến   tài khoản đích.
 
- IERC20 Meta data
  name: lấy tên của token
  symbol: Lấy biểu tượng của token
  decimals
  
 

