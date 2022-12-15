1
So they are using a wrong function in the IERC20 interface https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/NFTSale.sol#L12-L16

On line: https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/NFTSale.sol#L14
the function transfer returns a bool, so it should be changed from: 
- function transfer(address, uint) external;
to:
-  function transfer(address, uint) external returns(bool);
(according to openzeppelin's standard)
