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
## Lines too long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible.

Here are some of the instances entailed:

[File: Trading.sol#L496](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L496)
[File: Trading.sol#L543](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L543)
[File: Trading.sol#L643](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L643)

## Unspecific compiler version pragma
For most source-units the compiler version pragma is very unspecific ^0.8.0. While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different EVM compilation that is ultimately deployed on the blockchain.

Avoid floating pragmas where possible. It is highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.

## Lack of events for critical operations
Critical operations not triggering events will make it difficult to review the correct behavior of the deployed contracts. Users and blockchain monitoring systems will not be able to detect suspicious behaviors at ease without events. Consider adding events where appropriate for all critical operations for better support of off-chain logging API.

Here are the instances entailed:

[File: TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol)

```
144:    function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {

190:    function _setReferral(

222:    function setValidSignatureTimer(

231:    function setChainlinkEnabled(bool _bool) external onlyOwner {

240:    function setNode(address _node, bool _bool) external onlyOwner {

249:    function setAllowedMargin(

264:    function setMinPositionSize(

274:    function setPaused(bool _paused) external onlyOwner {
```
## Large numerical number
For better code readability, consider using scientific notation or incorporating `_` to represent big numbers instead of adopting their numerical literals.

For instance, the constant value below may be rewritten as follows:

[File: TradingExtension.sol#L26](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L26)

```diff
-    uint public maxGasPrice = 1000000000000; // 1000 gwei
+    uint public maxGasPrice = 1000_000_000_000; // 1000 gwei
```
Alternatively, it may also be refactored as follows:

```diff
-    uint public maxGasPrice = 1000000000000; // 1000 gwei
+    uint public maxGasPrice = 1e12; // 1000 gwei
```
