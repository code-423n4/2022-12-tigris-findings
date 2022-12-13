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
[File: Position.sol#L99-L109](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol#L99-L109)

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
## Minimization of truncation
The number of divisions in an equation should be reduced to minimize truncation frequency, serving to achieve higher precision. And, where deemed fit, comment the code line with the original multiple division arithmetic operation for clarity reason.

For instance, the equation below with three division may be refactored as follows:

[File: TradingLibrary.sol#L64](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L64)

```diff
-    _liqPrice = _tradePrice - ((_tradePrice*1e18/_leverage) * uint(int(_margin)+_accInterest) / _margin) * _liqPercent / 1e10;
+    _liqPrice = _tradePrice - (_tradePrice*1e18 * uint(int(_margin)+_accInterest) * _liqPercent) / (_leverage * _margin * 1e10);
```
## Typo mistakes
[File: PairsContract.sol#L151](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L151)

```diff
-     * @param _onOpen true if adding to open interesr
+     * @param _onOpen true if adding to open interest
```
## Use `delete` to clear variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address or a boolean to false. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic.

For instance, the `a = 0` instance below may be refactored as follows:

[File: PairsContract.sol#L162](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L162)

```diff
-                _idToOi[_asset][_tigAsset].longOi = 0;
+                delete _idToOi[_asset][_tigAsset].longOi;
```
Similarly, the `a = false` instance below may be rewritten as follows:

[File: StableVault.sol#L95](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L95)

```diff
-        allowed[_token] = false;
+        delete allowed[_token];
```
## OpenZeppelin draft abstract contract
The protocol imports Openzeppelin's draft abstract contracts that are not ready for mainnet use. Contracts of this nature have not been time tested due to inadequate security auditing and are liable to modification and changes in future development. As such, the use of draft EIP712 in production contracts could result in associated permit functions failing to work as expected and impact both the protocol and the users.

Here is an instance entailed:

[File: StableToken.sol#L4](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L4)

```
import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
```
## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach.

For instance, the import instances below could be refactored as follows:

[File: StableVault.sol#L4-L7](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L4-L7)

```
import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import {ReentrancyGuard} from "@openzeppelin/contracts/security/ReentrancyGuard.sol";
import {MetaContext} from "./utils/MetaContext.sol";
import {IStableVault.sol} from "./interfaces/IStableVault.sol";
```
## Unneeded `return` keyword on named returns
Functions adopting named returns need not use the `return` keyword when returning their corresponding values.

For instance, the return instance below may be refactored as follows:

[File: StableToken.sol#L57-L59](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#L57-L59)

```diff
    function _msgSender() internal view override(Context, MetaContext) returns (address sender) {
-        return MetaContext._msgSender();
+        sender = MetaContext._msgSender();
    }
```