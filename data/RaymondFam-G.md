## Split require statements using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`.

Here are some of the instances entailed:

[File: Trading.sol#L887](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L887)

```
            require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");
```
## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the instance below may be refactored as follows:

[File: Trading.sol#L497](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L497)

```diff
-            if (trade.direction && trade.orderType == 1) {
+            if (!(!trade.direction || trade.orderType != 1)) {
```
## Use storage instead of memory for structs/arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct.

Here are some of the instances entailed:

[File: Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol)

```
183:         IPosition.MintTrade memory _mintTrade = IPosition.MintTrade(

237:         IPosition.Trade memory _trade = position.trades(_id);

270:         IPosition.Trade memory _trade = position.trades(_id);
...
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, consider replacing `>=` with the strict counterpart `>` in the following inequality instance:

[File: Trading.sol#L491](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L491)

```diff
-            require(block.timestamp >= limitDelay[_id]);
+            require(block.timestamp > limitDelay[_id] - 1);
```
Similarly, consider replacing `<=` with the strict counterpart `<` in the following inequality instance, as an example:

[File: Trading.sol#L966](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L966)

```diff
-             require(_percent <= DIVISION_CONSTANT);
+            require(_percent < DIVISION_CONSTANT + 1);
```
## += and -= cost more gas
`+=` and `-=` generally cost 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

For instance, the `+=` and `-=` instances below may be refactored as follows:

[File: Trading.sol#L509-L511](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L509-L511)

```diff
-                trade.price += trade.price * _spread / DIVISION_CONSTANT;
+                trade.price = trade.price + trade.price * _spread / DIVISION_CONSTANT;

-                trade.price -= trade.price * _spread / DIVISION_CONSTANT;
+                trade.price = trade.price - trade.price * _spread / DIVISION_CONSTANT;
```
## Unused imports
Importing source units that are not referenced in the contract increases the size of deployment. Consider removing them to save some gas. If there was a plan to use them in the future, a comment explaining why they were imported would be helpful.

Here are some of the instances entailed:

[File: Trading.sol#L8](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L8)

```
import "./interfaces/IReferrals.sol";
```
[File: Lock.sol#L4](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L4)

```
import "hardhat/console.sol";
```
[File: StableVault.sol#L5](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol#L5)

```
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";
```
## Ternary over `if ... else`
Using ternary operator instead of the if else statement saves gas.

For instance, the code block below may be refactored as follows:

[File: Trading.sol#L197-L201](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L197-L201)

```
 _tradeInfo.direction
     ? {
         tradingExtension.modifyLongOi(_tradeInfo.asset, _tigAsset, true, _positionSize);
     }
     : {
         tradingExtension.modifyShortOi(_tradeInfo.asset, _tigAsset, true, _positionSize);
     }
```
## Payable access control functions costs less gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`.

For instance, the following code line may be refactored as follows:

[File: TradingExtension.sol#L240](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L240)

```diff
-    function setNode(address _node, bool _bool) external onlyOwner {
+    function setNode(address _node, bool _bool) external payable onlyOwner {
```
## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A private visibility that saves more gas on function calls than the internal visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

[File: TradingExtension.sol#L278-L281](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol#L278-L281)

```diff
+    function _onlyProtocol() private view {
+        require(msg.sender == trading, "!protocol");
+    }

    modifier onlyProtocol() {
-      require(msg.sender == trading, "!protocol");
+        _onlyProtocol();
        _;
    }
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
  version: "0.8.0",
  settings: {
    optimizer: {
      enabled: true,
      runs: 1000,
    },
  },
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

[File: Referrals.sol#L70-L72]https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol#L70-L72)

```diff
-        for (uint i=0; i<_codeOwnersL; i++) {
+        for (uint i=0; i<_codeOwnersL;) {
              _referral[_ownedCodes[i]] = _codeOwners[i];

+            unchecked {
+                i++;
+            }
          }
```
## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block instance below may be refactored as follows:

[File: MetaContext.sol#L29-L35](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol#L29-L35)

```diff
-    function _msgData() internal view virtual override returns (bytes calldata) {
+    function _msgData() internal view virtual override returns (bytes calldata _data) {
        if (_isTrustedForwarder[msg.sender]) {
-            return msg.data[:msg.data.length - 20];
+            _data = msg.data[:msg.data.length - 20];
        } else {
-            return super._msgData();
+            _data = super._msgData();
        }
    }
```