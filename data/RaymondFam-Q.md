## Interfaces should be imported
In `Trading.sol`, interfaces are showing up in their entirety at the top of the contract which facilitates an ease of references on the same file page. This has, on the other hand, made the already large contract size to house over a thousand code lines. Consider saving the four interfaces entailed into [`ITradingExtension.sol`, `IStable.sol`, `ExtendedIERC20.sol`, and `ERC20Permit.sol`](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L14-L77) respectively, and have them imported like all other files.

## Inconsistency in interface naming
Some interfaces in the code bases are named without the prefix `I` that could cause confusion to developers and readers referencing or interacting with the protocol. Consider conforming to Solidity's naming conventions by having the two instances below refactored as follow:

[File: Trading.sol#L63-L67](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L63-L67)

``` diff
- interface ExtendedIERC20 is IERC20 {
+ interface IExtendedIERC20 is IERC20 {

- interface ERC20Permit is IERC20 {
+ interface IERC20Permit is IERC20 {
```
## Use `uint256` instead of `uint`
Throughout the code bases, there are instances of `uint` as opposed to `uint256`. In favor of explicitness, consider replacing all instances of `uint` with `uint256`.

For instance, the following code lines may be refactored as follows:

[File: Position.sol#L16-L18](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L16-L18)

```diff
-    uint constant public DIVISION_CONSTANT = 1e10; // 100%
+    uint256 constant public DIVISION_CONSTANT = 1e10; // 100%

-    mapping(uint => mapping(address => uint)) public vaultFundingPercent;
+    mapping(uint256 => mapping(address => uint256)) public vaultFundingPercent;
```

