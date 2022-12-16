## Index
|    | Issue                                                                            |
|----|----------------------------------------------------------------------------------|
| 1  | use SafeERC20 library instead of IERC20                                          |
| 2  | funds should directly send to `bond.owner `                                      |
| 3  | Missing checks for address(0x0) when assigning values to address state variables |
| 4  | Missing checks for uint(0) input                                                 |
| 5  | require()/revert() statements should have descriptive reason strings             |
| 6  | Contracts are not using their OZ Upgradeable counterparts                        |
| 7  | Critical Changes Should Use Two-step Procedure                                   |
| 8  | USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’                            |
| 9  | contract should use constant solidity version                                    |
| 10 | some critical function should have events                                        |

## 1.use SafeERC20 library instead of IERC20 
rather than using `transfer()/transferfrom()` use `safetransfer()/safetransferfrom()` by using SafeERC20 because `transfer()/transferfrom()` does not revert incase of failure and may loss funds
[BondNFT.sol#L185](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L185)
[BondNFT.sol#L216](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L216)
[Lock.sol#L52](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L52)

## 2.funds should directly send to `bond.owner `
instead of sending funds to manager contract then sending to user funds should directly be sent to owner by replacing manager to `bond.owner`
and maybe user will not get desired amount due to gas fees during transfer
[BondNFT.sol#L185](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L185)

## 3.Missing checks for address(0x0) when assigning values to address state variables
[Lock.sol#L127-L131](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L127-L131)
[BondNFT.sol#L366-L370](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L366-L370)

## 4.Missing checks for uint(0) input
[BondNFT.sol#L57-L86](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L57-L86)

## 5.require()/revert() statements should have descriptive reason strings
instead of reverting directly user should know the reason for function to revert
[Trading.sol#L491](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L491)
[Trading.sol#L954](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L954)
[Trading.sol#L966](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L966)

## 6.Contracts are not using their OZ Upgradeable counterparts
The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.
[Trading.sol#L6](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L6)
[BondNFT.sol#L4-L6](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L4-L6)
[GovNFT.sol#L4-L5](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L4-L5)
[Lock.sol#L5-L6](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L5-L6)

## 7.Critical Changes Should Use Two-step Procedure
[Lock.sol#L127-L131](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L127-L131)
[BondNFT.sol#L366-L370](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L366-L370)

## 8.USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’
for example use it like import `{Ownable} from "@openzeppelin/contracts/access/Ownable.sol";`
[Trading.sol#L6](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L6)
[BondNFT.sol#L4-L6](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L4-L6)

## 9.contract should use constant solidity version 
[Position.sol#L2](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L2)

## 10.some critical function should have events 
[BondNFT.sol#L366-L370](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L366-L370)