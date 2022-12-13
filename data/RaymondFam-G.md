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