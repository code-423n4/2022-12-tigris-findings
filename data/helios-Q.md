# 1st report for : Trading.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

## 1. Insufficient gas limit checking in `_closePosition` function

Summary:

The `_closePosition` function of the `Trading` contract uses a `_checkGas` function to ensure that there is enough gas to complete the transaction. However, this check is performed after the transaction has already begun, meaning that the function may run out of gas before the check is performed. If this happens, the transaction will fail and any changes made by the function will be rolled back.

Impact:

If the `_closePosition` function runs out of gas before the `_checkGas` function is called, the transaction will fail and any changes made by the function will be rolled back. This could potentially cause problems for users of the contract.

Recommendation:

To prevent this vulnerability, the `_checkGas` function should be called before any other actions are performed in the `_closePosition` function. This will ensure that there is enough gas to complete the transaction before any changes are made.

For example:
```
function _closePosition(
    uint _id,
    uint _price,
    uint _percent
) external returns (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) {
    _checkGas();

    // Rest of function...
}
```

## 2. Denial of Service through `_checkGas`

Summary:

The `_checkGas` function in the `Trading` contract is not protected by an access control, allowing any contract or external actor to call it. This function calls `assert` with a gas value of 2300, which is less than the gas cost of calling the function. As a result, any contract or external actor can call `_checkGas` repeatedly to exhaust all of the contract's gas and prevent it from processing any further transactions. This could potentially be used as a denial of service attack against the contract.

Impact:

An attacker could repeatedly call the `_checkGas` function to exhaust the contract's gas and prevent it from processing any further transactions. This could potentially disrupt the normal operation of the contract and cause losses for its users.

Recommendation:

To prevent this vulnerability, the `_checkGas` function should be protected by an access control so that only authorized contracts and actors can call it. This can be done by adding a `onlyOwner` modifier to the function, which checks that the caller is the contract owner.

For example: 
```
modifier onlyOwner {
  require(msg.sender == owner, "Only contract owner can call this function");
  _;
}

function _checkGas() external view onlyOwner {
  assert(gasleft() > 2300, "Gas limit reached");
}
```
Alternatively, the `_checkGas` function could be removed entirely, as it does not appear to serve any useful purpose.

## 3. Incorrect gas limit calculation in the `withdraw` function

Summary:

In the `withdraw` function of the `Trading` contract, the gas limit for calling the `_withdraw` function of the `ITradingExtension` contract is calculated incorrectly. The gas limit is calculated by calling the `_checkGas` function of the `ITradingExtension` contract, which returns the gas limit for calling the `withdraw` function of the `Trading` contract itself. This means that the gas limit used to call the `_withdraw` function will always be the same, regardless of the actual gas usage of the `_withdraw` function.

Impact:

If the `_withdraw` function of the `ITradingExtension` contract has a higher gas usage than the `withdraw` function of the `Trading` contract, it is possible that the `_withdraw` function will run out of gas and fail. This could result in the user's funds being locked in the contract and not being able to be withdrawn.

Recommendation:

To fix this vulnerability, the gas limit for calling the `_withdraw` function should be calculated by calling the `_checkGas` function of the `ITradingExtension` contract before calling the `_withdraw` function, and then passing the returned gas limit to the `_withdraw` function as the `gas` parameter. This will ensure that the correct gas limit is used for the `_withdraw` function.

For example:
```
uint gas = tradingExtension._checkGas();
tradingExtension._withdraw(trader, amount, gas);
```

## 4. Incorrect calculation of payout in `_closePosition` function

Summary:

In the `_closePosition` function of the `Trading` contract, the `payout` variable is calculated by multiplying the `positionSize` by the `price` and subtracting the `positionSize` from the result. However, this calculation is incorrect and will not accurately determine the payout for a trade.

Impact:

This error could result in the incorrect calculation of payouts for trades, leading to inaccurate balances for users of the contract. This could potentially result in financial loss for users if they are not properly compensated for their trades.

Recommendation:

To fix this issue, the `payout` variable should be calculated by multiplying the `positionSize` by the `price` and dividing the result by the constant `DIVISION_CONSTANT`. This will ensure that the correct payout is calculated for each trade.

For example:
```
int256 payout = (int256(positionSize) * int256(price)) / DIVISION_CONSTANT;
```
Alternatively, the `payout` variable could be calculated by multiplying the `positionSize` by the difference between the `price` and `DIVISION_CONSTANT`, which would also give the correct result.

For example:
```
int256 payout = int256(positionSize) * (int256(price) - DIVISION_CONSTANT);
```

## 5. Insufficient `require` statements in `processDelegatedTrade` function

Summary:

In the `processDelegatedTrade` function of the `Trading` contract, several calls to external contract functions are made without checking the return value of the call. If one of these external contract functions fails, the call to `processDelegatedTrade` will continue without throwing an error and the trade will be executed without the intended conditions being met.

Impact:

This vulnerability allows an attacker to manipulate the execution of the `processDelegatedTrade` function by causing external contract calls to fail, potentially allowing the attacker to execute trades without the intended conditions being met.

Recommendation:

To prevent this vulnerability, the `processDelegatedTrade` function should check the return value of each external contract call and throw an error if the call fails. This can be done by using the `require` keyword and checking the return value of each external contract call. For example:
```
function processDelegatedTrade(
    bytes32 _tradeId,
    address _trader,
    uint _asset,
    uint _size,
    bool _isLong,
    uint _limitPrice,
    uint _price,
    bytes _signature
) external {
    // Check the return value of each external contract call and throw an error if the call fails
    require(pairsContract.isAllowed(_asset), NotAllowedPair());
    require(tradingExtension.isPaused() == false, TradingPaused());
    require(_isLong ? tradingExtension.validateTrade(_asset, _trader, _size, _price, _size * _price / DIVISION_CONSTANT) : tradingExtension.validateTrade(_asset, _trader, _size, _price, _size * _price / DIVISION_CONSTANT), BadLeverage());
    // ...
}
```

## 6. Untrusted contract implementation in `tradingExtension`

Summary:

The `Trading` contract accepts an implementation of the `ITradingExtension` interface as the `tradingExtension` contract. This contract is then called on line 128 of the `_open` function, which can execute arbitrary code. This allows an attacker to supply an implementation of `ITradingExtension` that could potentially be used to disrupt the normal operation of the `Trading` contract or steal funds from the contract.

Impact:

An attacker could supply an implementation of the `ITradingExtension` interface that contains malicious code and use it to execute arbitrary code within the `Trading` contract. This could potentially be used to steal funds from the contract or disrupt its normal operation.

Recommendation:

To prevent this vulnerability, the `Trading` contract should only accept implementations of the `ITradingExtension` interface from trusted sources. This could be done by requiring that the `tradingExtension` contract be initialized with a specific address, or by requiring that the `tradingExtension` contract be signed by a trusted party. This will ensure that only trusted implementations of the `ITradingExtension` interface can be used by the `Trading` contract.

## 7. Token trading contract allows unauthorized users to mint tokens

Summary:

In the `mintStableToken` function of the `Trading` contract, the `_mint` parameter is passed to the `mintFor` function of the `IStable` interface without being checked for authorization. This means that any user can mint tokens for any other user, allowing unauthorized users to mint tokens for themselves or for others.

Impact:

An attacker could exploit this vulnerability to mint tokens for themselves or for others without permission, potentially leading to a loss of funds or a breach of contract terms.

Recommendation:

To prevent this vulnerability, the `mintStableToken` function should check that the caller has permission to mint tokens for the `_for` user before calling the `mintFor` function. This can be done by calling the `canMintFor` function of the `IStable` interface, which checks if the caller has permission to mint tokens for the `_for` user. If the `canMintFor` function returns false, the `mintStableToken` function should immediately revert.

For example:
```
function mintStableToken(
    IStable _stableToken,
    address _for,
    uint _mint
) external onlyStableTokens(_stableToken) {
    require(_stableToken.canMintFor(msg.sender, _for), "Unauthorized minting");

    _stableToken.mintFor(_for, _mint);
}
```

## 8. ModifyOi function can be called by anyone

Summary:

In the `Trading` contract, the `modifyOi` function is marked as `external`, which means that it can be called by anyone. This function is used to modify the outstanding interest (OI) of a position, which can potentially be used to manipulate the contract's state.

Impact:

An attacker could call the `modifyOi` function to manipulate the outstanding interest of a position, potentially allowing them to disrupt the normal operation of the contract.

Recommendation:

To prevent this vulnerability, the `modifyOi` function should be marked as `internal` instead of `external`. This will prevent anyone other than the contract itself from calling this function.

Alternatively, access controls can be implemented to ensure that only authorized users are able to call the `modifyOi` function. This can be done by adding a `require` statement at the beginning of the function that checks that the caller has the appropriate permission. For example:
```
modifier onlyAuthorized() {
  require(msg.sender == authorizedUser, "msg.sender is not authorized to call this function");
  _;
}

function modifyOi(...) external onlyAuthorized {
  ...
}
```
## 9. `isPaused` function in `Trading` contract does not check for pause state

Summary:

In the `isPaused` function of the `Trading` contract, the `paused` state variable is never checked. Instead, the function always returns `false`, even if the contract is paused. This can result in the contract continuing to process trades even when it is supposed to be paused.

Impact:

If the `Trading` contract is paused, users may be able to continue trading on the contract, potentially leading to incorrect balances and/or loss of funds.

Recommendation:

To prevent this vulnerability, the `isPaused` function should check the value of the `paused` state variable and return its value. This can be done by replacing the current implementation of the `isPaused` function with the following code:
```
function isPaused() external view returns(bool) {
    return paused;
}
```
## 10. Unbounded array access in `init` function

Summary:

In the `init` function of the `Trading` contract, the `delayStops` array is accessed at index `_delays[i]`, where `_delays` is an array of user-supplied values. However, there is no bounds checking on the `_delays[i]` index, so an attacker can supply a large index value to access elements outside the bounds of the `delayStops` array, potentially leading to memory corruption and other undefined behavior.

Impact:

An attacker could exploit this vulnerability by supplying a large index value for the `_delays` array in the `init` function, potentially causing memory corruption and other undefined behavior. This could potentially be used to disrupt the normal operation of the contract, or to cause it to execute arbitrary code under the attacker's control.

Recommendation:

To prevent this vulnerability, the `init` function should include bounds checking on the `_delays[i]` index to ensure that it does not access elements outside the bounds of the `delayStops` array. This can be done by adding a check that the `_delays[i]` index is less than the length of the `delayStops` array before accessing the `delayStops` array at that index.

For example:
```
require(_delays[i] < delayStops.length, "Index out of bounds");
delayStops[_delays[i]] = _stop;
```

## 11. Access control vulnerability in `setFee` function

Summary:

The `setFee` function of the `Trading` contract has a vulnerability because it does not properly check the caller's access level before allowing them to modify the contract's fee values. This allows anyone to call the function and change the fees, potentially allowing an attacker to disrupt the normal operation of the contract.

Impact:

An attacker could call the `setFee` function and change the fees to arbitrary values, potentially disrupting the normal operation of the contract.

Recommendation:

To prevent this vulnerability, the `setFee` function should check the caller's access level before allowing them to modify the contract's fee values. This can be done by adding a call to the `require` function that checks that the caller has the appropriate access level before modifying the fees. For example:
```
function setFee(bytes32 _feeType, uint _value) external {
    require(isAdmin(), "Only admins can set fees");
    ...
}
```
Alternatively, the `setFee` function could be made `internal` so that it can only be called by other functions within the contract, and access controls can be implemented in those functions. This would prevent external callers from modifying the fees directly.

## 12.  Incorrect use of `require` in the `getVerifiedPrice` function.

Summary: 
The `getVerifiedPrice` function in contract `Trading` uses `require` instead of `revert` in the condition where the `_priceData` provided is invalid. This can cause the function to return incorrect results instead of failing as intended.

Impact: 
This bug can cause the `getVerifiedPrice` function to return incorrect results when provided with invalid `_priceData`. This can cause the contract to behave unexpectedly and potentially result in losses for users of the contract.

Recommendation: 
Replace `require` with `revert` in the condition where `_priceData` is invalid. This will cause the function to revert and fail, instead of potentially returning incorrect results.

## 13. Lack of input validation in the `validateTrade` function

Summary: 
The `validateTrade` function in the `Trading` contract does not adequately validate its inputs. Specifically, the `_margin` and `_leverage` arguments are not checked for validity. This can allow an attacker to exploit the contract by passing malicious values to this function.

Impact: 
An attacker could exploit this vulnerability to execute arbitrary contract calls, potentially allowing them to steal funds or manipulate the contract's state in other harmful ways.

Recommendation: 
Input validation should be added to the `validateTrade` function to ensure that the `_margin` and `_leverage` arguments are within the expected range. This will prevent attackers from passing malicious values to this function and help protect the contract from potential exploitation.

## 14. Unchecked call to `tradingExtension.isPaused()` in `modifyShortOi()` function

Summary: 
The `modifyShortOi()` function of the `Trading` contract calls the `isPaused()` function of the `tradingExtension` contract without checking its return value. If the `tradingExtension` contract is paused, this can lead to the `modifyShortOi()` function modifying the contract state, even though it should not be allowed to do so.

Impact: 
If the `tradingExtension` contract is paused, this vulnerability could allow unauthorized modifications to the contract state. This could potentially result in financial losses for users of the contract.

Recommendation: 
The `modifyShortOi()` function should check the return value of the `isPaused()` function before modifying the contract state. If the `tradingExtension` contract is paused, the `modifyShortOi()` function should not modify the contract state and should instead throw an error.

## 15. Unsafe implementation of `mintFor()` and `burnFrom()` functions in Trading contract

Summary: 
The implementation of `mintFor()` and `burnFrom()` functions in the `Trading` contract do not check if the caller is the contract owner, which can allow any contract user to mint and burn tokens.

Impact: 
This vulnerability can be exploited by a malicious contract user to mint an unlimited amount of tokens and send them to their own address, potentially leading to inflation and loss of value for legitimate token holders.

Recommendation: 
The `mintFor()` and `burnFrom()` functions should be modified to check if the caller is the contract owner before allowing them to mint or burn tokens. This can be done by adding an `if` statement to check the caller's address against the contract owner's address before executing the function's logic.

## 16. Improper implementation of `limitOrderPriceRange`

Summary: 
In the Trading contract, the `limitOrderPriceRange` variable is incorrectly implemented as a public variable with a default value of 1e8. This default value allows anyone to read and modify the value of this variable, which could potentially be used to manipulate the behavior of the contract.

Impact: 
This implementation allows anyone to read and modify the value of the `limitOrderPriceRange` variable, which could potentially be used to manipulate the behavior of the contract. This could lead to unexpected or undesirable behavior, and may result in financial losses for users of the contract.

Recommendation: 
The `limitOrderPriceRange` variable should be made private and given a default value that cannot be easily manipulated. Alternatively, the variable could be removed entirely if it is not needed for the correct functioning of the contract.

# 2nd report for : TradingExtension.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

## 1. Incorrect handling of integer overflow in `_closePosition` function

Summary:

The `_closePosition` function in the `TradingExtension` contract is vulnerable to an integer overflow attack. Specifically, the `_percent` parameter passed to the function is not properly checked for validity, and the contract may attempt to perform arithmetic operations on it that could result in an overflow.

Impact:

An attacker could exploit this vulnerability to cause the contract to perform arithmetic operations on an improperly validated `_percent` value, potentially resulting in an integer overflow and allowing the attacker to manipulate the contract's internal state. This could be used to cause the contract to transfer funds to the attacker, or to execute other malicious actions.

Recommendation:

It is recommended that the `_closePosition` function be modified to properly validate the `_percent` parameter before performing any arithmetic operations on it. This could be done by checking that the value is within a valid range (e.g. 0 to 100) before performing any operations on it. Additionally, it may be necessary to use SafeMath or other library functions to ensure that integer overflows do not occur during the calculation of the `_payout` value.

## 2. Unchecked Revert when closing position

Summary: 
The `_closePosition` function can revert without checking the return value. This can cause issues for users who expect their positions to be closed when calling this function.

Impact: 
Users who expect their positions to be closed when calling `_closePosition` may be disappointed if the function reverts unexpectedly. This could cause confusion and may lead to users losing funds if they continue to hold positions that they expect to be closed.

Recommendation: 
The `_closePosition` function should check the return value of the `TradingLibrary.pnl` call and revert if necessary. This will ensure that the function behaves as expected and will prevent confusion and potential loss of funds for users.

## 3. Revert error not handled in the code.

Summary: 
The `_closePosition()` and `_limitClose()` functions in the `TradingExtension` contract fail to handle a revert error when checking the value of a trade's `orderType` variable.

Impact: 
If a trade's `orderType` value is not 0, calling `_closePosition()` or `_limitClose()` will cause the contract to revert, which will consume all of the gas used in the transaction and will prevent the function from executing any further. This can result in the loss of funds for the caller.

Recommendation: 
The `_closePosition()` and `_limitClose()` functions should handle the revert error by checking the value of `orderType` and only executing their remaining code if the value is 0. This can be done by adding an `if` statement that checks the value of `orderType` before executing the remaining code in the function.

## 4. TradingExtension contract has an uninitialized storage pointer

Summary:

The `_closePosition` and `_limitClose` functions of the `TradingExtension` contract have a potential uninitialized storage pointer in their `IPosition.Trade memory _trade` local variable.

Impact:

If an attacker can trigger the `revert` statement on the `_closePosition` or `_limitClose` function before the `_trade` variable has been initialized, they can read an uninitialized storage pointer. This can allow the attacker to read data from the storage of the contract that they should not be able to access.

Recommendation:

The `_closePosition` and `_limitClose` functions should initialize the `_trade` variable before the first use. This can be done by replacing the current declaration with `IPosition.Trade memory _trade = position.trades(_id);`

## 5. Unchecked division operation

Summary: 
In the `_closePosition` function, the division operation in the following line of code is not checked for division by zero:
```
(_positionSize, _payout) = TradingLibrary.pnl(_trade.direction, _price, _trade.price, _trade.margin, _trade.leverage, _trade.accInterest);
```
Impact: 
This division by zero could cause the contract to revert, or even cause the EVM to crash.

Recommendation: 
The division operation should be checked to ensure that it does not divide by zero. This can be done by adding a check for division by zero before performing the division. For example:
```
if (_percent == 0) {
    revert("Division by zero");
}
```
# 3rd report for : TradingLibrary.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

## 1. Integer overflow vulnerability in TradingLibrary.pnl() function

Summary: 
The `TradingLibrary.pnl` function is vulnerable to integer overflow attacks.

Impact: 
An attacker could potentially exploit the integer overflow vulnerability by passing large values to the `_margin`, `_leverage`, or `accInterest` parameters of the pnl function. This could result in incorrect calculations and the loss of funds.

Recommendation: 
One possible solution is to modify the `pnl` function to use the `SafeMath` library to prevent integer overflows. For example, instead of using the `+` and `-` operators to add or subtract values, the `add` and `sub` functions from `SafeMath` could be used. This would ensure that the calculations performed by the `pnl` function always produce a valid result, even if the inputs are large.

## 2. Unchecked external call in `getVerifiedPrice` function

Summary: 
The `getVerifiedPrice` function in the `TradingLibrary` contract makes an unchecked external call to the `decimals` function in the `IPrice` contract. This could potentially result in the contract executing malicious code or entering into an infinite loop.

Impact: 
An attacker could potentially exploit this vulnerability by calling the `getVerifiedPrice` function with a malicious `IPrice` contract that has a `decimals` function that never returns or that executes arbitrary code. This could potentially result in the attacker gaining unauthorized access to the contract's funds or internal state.

Recommendation: 
The `getVerifiedPrice` function should be modified to check the return value of the `decimals` function before continuing with its execution. This can be achieved by using the `require` or `assert` functions to check whether the return value is valid. Alternatively, the `decimals` function could be marked `view` and called using the `.call` function to prevent it from modifying the contract's state.

## 3. Reentrancy vulnerability in TradingLibrary contract

Summary: 
The `TradingLibrary` contract is vulnerable to reentrancy attacks.

Impact: 
An attacker could potentially call the `getLiqPrice` function repeatedly, causing the contract to execute the `liqPrice` function multiple times. This could result in the attacker gaining unauthorized access to the contract's funds or internal state.

Recommendation: 
One possible solution is to modify the `getLiqPrice` function to include a mutex or to use the `send` or `transfer` functions to call external functions in a way that is safe from reentrancy attacks.

# 4th report for : Position.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

## 1. Divide by zero vulnerability in `Position` contract

Summary:

In the `Position` contract, the `_pendingFunding` variable is calculated by dividing `(int256(block.timestamp-lastUpdate[_trade.asset][_trade.tigAsset])*fundingDeltaPerSec[_trade.asset][_trade.tigAsset])*1e18` by `int256(longOi[_trade.asset][_trade.tigAsset])` or `int256(shortOi[_trade.asset][_trade.tigAsset])`.

However, if `longOi[_trade.asset][_trade.tigAsset]` or `shortOi[_trade.asset][_trade.tigAsset]` is zero, this will cause a divide by zero error.

Impact:

If an attacker can set `longOi[_trade.asset][_trade.tigAsset]` or `shortOi[_trade.asset][_trade.tigAsset]` to zero, they can cause a divide by zero error in the contract, potentially leading to instability in the contract or denial of service attacks.

Recommendation:

To avoid the divide by zero vulnerability, the `_pendingFunding` calculation should check if `longOi[_trade.asset][_trade.tigAsset]` or `shortOi[_trade.asset][_trade.tigAsset]` is zero before dividing by it. If either value is zero, `_pendingFunding` should be set to zero instead of performing the division.

## 2. Funding calculation overflow

Summary:

The code on line 57 calculates the pending funding for a trade, but the calculation can overflow an `int256` type, leading to undefined behavior.

Impact:

The overflow can result in incorrect funding calculations, potentially leading to funds being incorrectly transferred or lost.

Recommendation:

To fix this issue, the calculation on line 57 should be rewritten to use a type that can hold larger values, such as `uint256`. Additionally, the `1e18` and `1e10` constants should be replaced with more readable named constants. For example, `ONE_ETHER_IN_WEI = 1e18` and `ONE_HUNDRED_PERCENT = 1e10`.

## 3. Minting Protection Vulnerability in Position Contract

Summary:

The `_isMinter` mapping in the `Position` contract is not protected against unauthorized minting, allowing anyone to mint NFTs on the contract.

Impact:

This vulnerability allows any user to mint NFTs on the `Position` contract, potentially allowing them to create and sell fake NFTs, or otherwise manipulate the contract's state. This could result in financial losses for users of the contract.

Recommendation:

To fix this issue, the `_isMinter` mapping should be protected with a modifier that checks that the caller is authorized to mint NFTs before allowing them to mint. This could be accomplished by adding a `minter` role to the contract, and using a `require` statement with the `isMinter` modifier to check that the caller is a minter before allowing them to mint. For example:
```
modifier isMinter() {
    require(minter[msg.sender], "Only minters are allowed to mint NFTs");
    _;
}
```
This modifier could then be applied to the minting function in the contract, like so:

```
function mint(...) public isMinter {
    // Mint the NFT here
}
```
This would prevent anyone who is not a minter from minting NFTs on the contract.

# 5th report for : PairsContact.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

## 1. Check for minimum leverage in `updateAssetLeveragev

Summary:
The `updateAssetLeverage` function allows the contract owner to update the leverage values of an asset. However, the minimum leverage is only checked if it is greater than 0, which means it is possible to set the minimum leverage to 0 and cause the require condition to be bypassed.

Impact:
Allowing the minimum leverage to be set to 0 can cause the contract to be exploited by a malicious actor. For example, they can set a very high maximum leverage value, which could allow them to take out very large positions without having to provide enough collateral.

Recommendation:
The minimum leverage check should be performed regardless of the value of `_minLeverage`. This can be done by changing the `if` statement to a `require` statement, as shown below:
```
require(_maxLeverage > 0, "Invalid max leverage");
require(_minLeverage > 0, "Invalid min leverage");
```
## 2. Incorrect use of `bytes`

Summary: In the `addAsset()` function, the `bytes` data type is incorrectly used in the following lines of code:
```
bytes memory _name  = bytes(_idToAsset[_asset].name);
require(_name.length > 0, "No name");
```
This will cause the `require()` statement to always be true, since `_name` will always be a non-empty `bytes` object regardless of the value of `_idToAsset[_asset].name`.

Impact: This bug will allow any user to add an asset to the contract without providing a valid name, potentially allowing the contract to be used for malicious purposes.

Recommendation: Replace the line `bytes memory _name = bytes(_idToAsset[_asset].name);` with `bytes memory _name = _idToAsset[_asset].name;`, so that `_name` is assigned the value of `_idToAsset[_asset].name` instead of a `bytes` object. Then, the `require()` statement can be used to ensure that `_name` is a non-empty string.

## 3. Open Interest Mapping Vulnerability

Summary: 
The contract has a vulnerability in the `_idToOi` mapping. This mapping is used to store the open interest for an asset for a given address. However, there is no check to ensure that the given address is a valid address, which means that an attacker can pass in a malformed address to access the open interest of another user.

Impact: 
An attacker can access the open interest of another user by passing in a malformed address to the `idToOi` function. This could allow the attacker to view the open interest of another user, and potentially manipulate or steal their assets.

Recommendation: 
Add a check to ensure that the given address is a valid address before accessing the open interest in the `idToOi` function. This can be done by using the `require` function to check that the address is not the zero address and that it is a contract. For example:
```
function idToOi(uint256 _asset, address _tigAsset) public view returns (OpenInterest memory) {
    require(_tigAsset != address(0) && isContract(_tigAsset), "Invalid address");
    return _idToOi[_asset][_tigAsset];
}
```
# 6th report for : Referrals.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

## 1. Incorrect use of `require()` in `initRefs()` function

Summary: 
The `initRefs()` function uses `require()` to check if the contract has been initialized (using the `isInit` boolean), but does not actually initialize the contract if it hasn't been done already. This can lead to confusion and unintended behavior.

Impact: 
If the contract has not been initialized and `initRefs()` is called, the `require()` check will fail and the function will return without initializing the contract. This can lead to unpredictable behavior and could potentially cause loss of funds.

Recommendation: 
The `initRefs()` function should initialize the contract if it has not been initialized already, rather than just checking if it has been initialized using a `require()` statement. This can be done by setting the `isInit` boolean to `true` at the beginning of the function.

## 2. Potential vulnerability in Referrals contract

Summary: 
The `initRefs()` function in the Referrals contract allows an attacker to set the referral code and referred address mappings to arbitrary values, potentially allowing the attacker to gain unauthorized access to the contract's functions.

Impact: 
If an attacker is able to call the `initRefs()` function, they may be able to gain unauthorized access to the contract's functions, such as `createReferralCode()` and `setReferred()`. This could potentially allow the attacker to manipulate the contract's state in unintended ways.

Recommendation: 
The `initRefs()` function should be removed or modified to prevent unauthorized access. Additionally, the contract should enforce more strict access controls to prevent unauthorized users from calling its functions.

## 3. Title: Unchecked return value in `setReferred`

Summary: 
The `setReferred` function in the `Referrals` contract does not check the return value of `_referredTrader == _referral[_hash]`. As a result, if `_referredTrader` is equal to `_referral[_hash]`, then `_referred[_referredTrader]` will not be set and the `Referred` event will not be emitted.

Impact: 
If `_referredTrader` is equal to `_referral[_hash]`, then the `Referred` event will not be emitted, and the contract's state will not be updated to reflect the referral. This could potentially cause confusion or incorrect behavior in any contracts or applications that rely on the `Referrals` contract to track referrals.

Recommendation: 
The `setReferred` function should check the return value of `_referredTrader == _referral[_hash]` and only update the contract's state and emit the `Referred` event if the return value is `false`.

Example fix:
```
    function setReferred(address _referredTrader, bytes32 _hash) external onlyProtocol {
        if (_referred[_referredTrader] != bytes32(0) || _referredTrader == _referral[_hash]) {
            return;
        }
        _referred[_referredTrader] = _hash;
        emit Referred(_referredTrader, _hash);
    }
```
## 4. Uninitialized `isInit` variable

Summary: The `isInit` variable in the `Referrals` contract is not properly initialized, which could cause unexpected behavior in the contract.

Impact: If the `isInit` variable is not properly initialized, the `initRefs` function may be called multiple times, potentially leading to incorrect data being stored in the `_referra` and `_referred` mappings. This could lead to incorrect referral information being returned by the contract, potentially leading to incorrect payments or other unintended consequences.

Recommendation: Initialize the `isInit` variable in the contract constructor or in the `initRefs` function to ensure that it is properly initialized. Alternatively, remove the isInit variable and the check for it in the `initRefs` function, as it does not appear to serve any useful purpose.

## 5. Incorrect use of `bytes32` in `setReferred` function

Summary: 
In the `setReferred` function, the `bytes32` type is used to represent a hash of a referral code. However, `bytes32` is not a suitable type for storing hashes, as it is not fixed-length and can store any 32 bytes of data. This could cause the contract to store incorrect data, leading to incorrect referral information being returned.

Impact: 
The use of the `bytes32` type in the `setReferred` function could cause the contract to store incorrect referral information. This could result in incorrect referral payments or other unintended consequences.

Recommendation: 
Use a fixed-length hash type, such as `bytes32` or `bytes20`, to represent referral code hashes in the `setReferred` function. Alternatively, consider using a library that provides a fixed-length hash type, such as the `bytes32` type from the `bytes` library.

# 7th report for : GovNFT.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

## 1. Misuse of `require` function

Summary:
The `require` function is used to check whether `_bridgeMint` is called by the contract itself or the contract owner. However, `_msgSender()` returns the caller of the `_bridgeMint` function, which could be any user or contract. Therefore, this `require` statement does not achieve its intended purpose.

Impact:
As a result of this issue, any user or contract can call the `_bridgeMint` function, regardless of whether they are the contract or the contract owner. This could potentially result in unauthorized minting of tokens.

Recommendation:
To fix this issue, the `_bridgeMint` function should check that the caller is the contract itself or the contract owner using the `msg.sender` address instead of the `_msgSender()` function. This will ensure that only the contract or its owner can call the function.

## 2. Excess gas usage in ERC721 mint function

Summary:

The `_mint` function in the `GovNFT` contract uses an excessive amount of gas to mint a new token. The `for` loop that iterates over the `assets` array uses a variable `i` which is not defined or initialized, leading to the contract entering an infinite loop.

Impact:

The contract can be exploited to cause a denial of service attack on the Ethereum network. Users calling the `_mint` function will be forced to pay the gas cost for the contract to enter the infinite loop, which could result in a significant amount of gas being used.

Recommendation:

Define and initialize the variable `i` before using it in the `for` loop in the `_mint` function. This will prevent the contract from entering an infinite loop and reduce the amount of gas used.

## 3. Incorrect MAX value in minting and bridging NFTs

Summary: 
In the `GovNFT` contract, the `MAX` constant is defined as `10000`, but it is only used in the `_mint` and `_bridgeMint` functions to check that `counter <= MAX` before minting a new NFT. However, `counter` is only incremented by `1` in each function, so an attacker can easily mint more than `MAX` NFTs by calling either function multiple times.

Impact: 
If an attacker is able to call the `_mint` or `_bridgeMint` functions multiple times, they can mint more NFTs than the `MAX` limit allows. This can cause the contract to run out of gas, or otherwise be unable to function properly.

Recommendation: 
The `MAX` constant should be used to limit the total number of NFTs that can be minted, rather than just the number of NFTs that can be minted in a single call to `_mint` or `_bridgeMint`. To do this, the `counter` variable should be incremented by the number of NFTs being minted in each function, rather than just `1`. Additionally, the `MAX` constant should be set to a reasonable value that reflects the intended maximum number of NFTs that should be minted.

## 4. Incorrect use of `owner` in `_bridgeMint` method

Summary: The `_bridgeMint` method is intended to be called only by the contract itself and the contract's owner, but it currently checks only if the caller is `msg.sender` or the contract's owner. This is incorrect because `_msgSender()` returns the context of the caller, which is not necessarily the contract's owner.

Impact: An attacker can call the `_bridgeMint` method and mint NFTs even if they are not the contract's owner.

Recommendation: Replace the use of `_msgSender()` with `msg.sender` in the `_bridgeMint` method. Alternatively, check if the caller is `msg.sender` or the contract's owner using `require(msg.sender == address(this) || msg.sender == owner(), "NotBridge")` instead of `require(msg.sender == address(this) || _msgSender() == owner(), "NotBridge")`.

## 5. Uncontrolled Access to isTrustedAddress mapping

Summary:

The `isTrustedAddress` mapping in the `GovNFT` contract is accessible to any caller without restriction. This allows an attacker to set any address as a trusted address, potentially allowing them to bypass security checks and perform unauthorized actions.

Impact:

An attacker can set any address as a trusted address by calling the `addTrustedAddress` function. This could allow the attacker to bypass security checks and perform unauthorized actions.

Recommendation:

The access to the `isTrustedAddress` mapping should be restricted to only allow the contract owner or other authorized parties to modify it. This can be done by adding a require statement to the `addTrustedAddress` function to check that the caller is the contract owner or other authorized party.

## 6. Re-entrancy attack in GovNFT contract

Summary:
The `_transfer` function in the GovNFT contract contains a re-entrancy vulnerability that allows an attacker to repeatedly call this function and transfer NFTs belonging to other users. This can be exploited by an attacker to transfer arbitrary amounts of NFTs belonging to other users, potentially leading to loss of funds for those users.

Impact:
If exploited, this vulnerability could allow an attacker to steal NFTs belonging to other users and transfer them to their own account. This could potentially result in significant financial losses for the affected users.

Recommendation:
To fix this vulnerability, the `_transfer` function should be modified to check for re-entrancy before performing the NFT transfer. This can be done by adding a mutex or by using the `require` statement to check that the contract is not in the middle of executing a call to itself. For example, the `_transfer` function could be modified as follows:
```
function _transfer(
    address from,
    address to,
    uint256 tokenId
) internal override {
    require(ownerOf(tokenId) == from, "!Owner");
    require(!isReentering, "Re-entrancy attack detected");
    isReentering = true;
    for (uint i=0; i<assetsLength(); i++) {
        userDebt[from][assets[i]] += accRewardsPerNFT[assets[i]];
        userDebt[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);
        userDebt[to][assets[i]] -= userPaid[to][assets[i]]/balanceOf(to);
        userPaid[to][assets[i]] += accRewardsPerNFT[assets[i]];
        userPaid[from][assets[i]] -= userPaid[from][assets[i]]/balanceOf(from);            
    }
    super._transfer(from, to, tokenId);
    isReentering = false;
}
```
# 8th report for : StableToken.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol

## 1. Incorrect access control for `setMinter()` function

Summary: 
The `setMinter()` function in the contract is only restricted to the contract owner, but it should only be accessible to minters.

Impact: 
This allows any contract owner to set the minter status of any address, including themselves. This can be exploited to mint an unlimited amount of tokens.

Recommendation: 
Restrict the `setMinter()` function to only be accessible by minters by adding the `onlyMinter()` modifier to the function declaration.

## 2. Unrestricted minting and burning in StableToken contract

Summary:

The `StableToken` contract allows any account to mint and burn tokens, as long as the `_msgSender()` function returns an address that is marked as a minter in the `isMinter` mapping. The `onlyMinter()` modifier used in the `mintFor()` and `burnFrom()` functions only checks that `isMinter[_msgSender()]` is `true`, without further restrictions.

Impact:

This vulnerability allows any account to mint and burn an arbitrary amount of tokens, potentially leading to inflation of the token supply and loss of funds for the contract owner and other token holders.

Recommendation:

The `onlyMinter()` modifier should be updated to enforce additional checks, such as checking that the caller has the `MINTER_ROLE` defined in the `ERC20Permit` contract, or that the caller is the contract owner. This will prevent unauthorized accounts from minting and burning tokens.

# 9th report for : StableVault.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol

## 1. Reentrancy Vulnerability in StableVault contract

Summary:
The `deposit` function in the StableVault contract does not use the ReentrancyGuard provided by OpenZeppelin to protect against reentrancy attacks. This allows an attacker to call the `deposit` function again after it has already transferred the tokens from the user but before it has finished updating the contract's internal state, allowing the attacker to potentially transfer the tokens multiple times without the user's consent.

Impact:
An attacker can exploit this vulnerability to transfer tokens from the user's account multiple times without the user's consent, potentially resulting in significant loss of funds for the user.

Recommendation:
To protect against reentrancy attacks, the `deposit` function should use the ReentrancyGuard provided by OpenZeppelin. This can be done by adding the following code at the beginning of the `deposit` function:
```
ReentrancyGuard guard = new ReentrancyGuard();
guard.protect(this);
```
# 10th report for : Lock.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol

## 1. Missing require() in the extendLock() function

Summary: 
The `extendLock()` function does not check if the user has approved the contract to transfer the specified amount of assets from the user's account. This could allow a user to extend the lock period and increase the amount of assets locked without actually having the specified assets in their account.

Impact: 
A user could abuse this bug to extend the lock period and increase the amount of assets locked without having the required amount of assets in their account. This could lead to incorrect calculations of the total locked assets and potentially result in lost assets.

Recommendation: 
Add a `require()` statement to check that the user has approved the contract to transfer the specified amount of assets from their account before calling the `transferFrom()` function. This will ensure that the user actually has the required assets in their account before extending the lock period and increasing the amount of assets locked.

## 2. `Lock` contract does not protect against reentrancy attack

Summary: 
The `claimGovFees` function in the `Lock` contract is called before any token transfers in the `lock`, `extendLock`, and `release` functions, which leaves the contract vulnerable to reentrancy attack.

Impact: 
An attacker can call the `claimGovFees` function in a reentrant manner to repeatedly call the `lock`, `extendLock`, and `release` functions, effectively stealing tokens from the contract.

Recommendation: 
The `claimGovFees` function should be called after the token transfers in the `lock`, `extendLock`, and `release` functions, or the `Lock` contract should be modified to use a circuit breaker pattern to prevent reentrancy attacks. Alternatively, the `lock`, `extendLock`, and `release` functions could use a mutex pattern to prevent multiple calls from executing simultaneously.

## 3. Lock contract is vulnerable to Integer Overflow

Summary: 
The `totalLocked` mapping in the `Lock` contract stores the total amount of tokens that have been locked. However, this mapping uses an unsigned integer (`uint`) to store the value, which is vulnerable to an integer overflow attack.

Impact: 
An attacker can exploit this vulnerability to lock a large number of tokens, causing the value stored in `totalLocked` to overflow and become a very small number. This can cause the contract to malfunction and potentially lead to loss of funds.

Recommendation: 
The type of the `totalLocked` mapping should be changed from `uint` to `uint256` to prevent integer overflow. Additionally, the contract should check for overflow and handle it gracefully to prevent any potential vulnerabilities.

## 4. extendLock function doesn't check the period parameter

Summary:
The `extendLock` function allows the caller to extend the lock time of a bond by providing a `_period` parameter. However, this function does not check that the value of `_period` is between the `minPeriod` and `maxPeriod` values. This means that a malicious caller could potentially set `_period` to an arbitrary value, potentially allowing them to lock funds indefinitely.

Impact:
If exploited, this vulnerability could allow an attacker to lock funds in a bond indefinitely, potentially denying the owner access to their funds.

Recommendation:
To fix this issue, the `extendLock` function should include a check to ensure that the value of `_period` is between the `minPeriod` and `maxPeriod` values. This can be done by adding a `require` statement that checks the value of `_period` against these values, similar to the checks performed in the `lock` function.

## 5. Lock contract has a security vulnerability

Summary: The `Lock` contract has a security vulnerability that allows an attacker to steal locked tokens by calling the `release()` function with a non-existent bond id.

Impact: The attacker can steal locked tokens from the contract by calling the `release()` function with a non-existent bond id. This can result in significant loss of funds for the contract owner and other users of the contract.

Recommendation: To fix this vulnerability, the `release()` function should check if the bond id passed to it exists before attempting to release the bond. This can be done by calling the `exists()` function on the `bondNFT` contract and ensuring that it returns `true` before calling the `release()` function on the bondNFT contract. Additionally, the `release()` function should also check that the caller is the owner of the bond before releasing it.

## 6. Allow calling claimGovFees from arbitrary addresses

Summary: The `claimGovFees` function is marked as `public`, which allows it to be called from arbitrary addresses. This can allow a malicious actor to make false claims and potentially steal funds from the contract.

Impact: A malicious actor can call `claimGovFees` from an arbitrary address and potentially steal funds from the contract.

Recommendation: The `claimGovFees` function should be marked as `internal` or `private` to prevent it from being called from arbitrary addresses. Alternatively, the function can be modified to check that the caller has the appropriate privileges before executing.

# 11th report for : BondNFT.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

## 1. Error in extending lock period and/or amount of a bond

Summary: 
In the function `extendLock()`, the `bond` struct is not updated after increasing the `_amount` and `_period` of the bond. As a result, the function does not correctly reflect the extended lock period and/or amount of the bond.

Impact: 
This error can lead to incorrect calculations of the bond's expiration epoch and share value, potentially resulting in incorrect payments to the bond's owner.

Recommendation: 
Update the `bond` struct to reflect the extended lock period and/or amount of the bond. This can be done by updating the `_bond` storage variable in line 70 with the new values of `_amount` and `_period`.

## 2. Call to `_mint()` function without checking return value

Summary: 
The `createLock()` function calls the `_mint()` function without checking its return value. This means that if `_mint()` fails, the contract will continue to execute without any indication that the mint operation failed.

Impact: 
If the `_mint()` function fails, the bond will not be created, but the `totalBonds` variable will still be incremented and the `_idToBond` mapping will be updated with a reference to a bond that does not exist. This could lead to further issues and potentially result in lost funds.

Recommendation: 
The `createLock()` function should check the return value of the `_mint()` function and properly handle any failures by reverting the transaction and/or returning an error to the caller. This will ensure that the contract behaves as expected and that any failures are properly handled.

## 3. Incorrect permission check in extendLock()

Summary: 
The `extendLock()` function allows any address to extend the lock period and/or amount of a bond, despite the function being marked as `onlyManager()`. This means that any address can use this function to extend the lock period and/or amount of a bond, regardless of whether or not the address is the manager.

Impact: 
This vulnerability allows any address to extend the lock period and/or amount of a bond, potentially allowing the attacker to lock funds for longer periods of time than intended.

Recommendation: 
The `extendLock()` function should be modified to correctly check that the caller is the manager before allowing the lock period and/or amount of a bond to be extended. This can be done by replacing the `onlyManager()` modifier with a custom modifier that checks that `msg.sender` is the contract's manager.

## 4. Timestamp misuse in BondNFT contract

Summary:

The `BondNFT` contract uses `block.timestamp` to track the timestamp of a bond's creation. This can be easily manipulated by a miner, as they have control over the `block.timestamp` value. This can lead to the `Bond` struct being created with an arbitrary timestamp, potentially resulting in security vulnerabilities.

Impact:

Using `block.timestamp` to track the creation time of a `Bond` struct can be easily manipulated by a miner, potentially leading to security vulnerabilities in the contract. This could allow an attacker to create bonds with arbitrary timestamps, potentially allowing them to bypass security checks in the contract.

Recommendation:

Instead of using `block.timestamp` to track the creation time of a `Bond` struct, consider using `now` or `block.timestamp` in combination with a check against the previous block's timestamp to ensure that the timestamp has not been manipulated. This will provide a more accurate and secure way to track the creation time of a `Bond` struct.

## 5. Infinite Loop in `extendLock()` Function

Summary: 
The `extendLock()` function in the BondNFT contract contains an infinite loop that can be triggered by a malicious user. The loop is triggered when the `_amount` parameter is greater than the `bond.amount` value.

Impact: 
A malicious user could trigger this infinite loop and potentially cause a denial of service attack on the contract. This could cause the contract to become unresponsive and potentially result in lost funds.

Recommendation: 
The loop in the `extendLock()` function should be rewritten to prevent the potential for an infinite loop. One possible solution would be to add a check to ensure that the `_amount` parameter is not greater than the `bond.amount` value.

# 12th report for : MetaContext.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol

## 1. Incorrect implementation of `_msgSender` and `_msgData` in MetaContext contract

Summary: 
The `_msgSender` and `_msgData` functions in the MetaContext contract have been implemented in such a way that they can be manipulated by malicious actors to execute arbitrary contract calls with the `msg.sender` and `msg.data` of their choosing. This can be exploited to steal funds or perform other unauthorized actions in contracts that inherit from MetaContext.

Impact: 
This vulnerability allows attackers to execute arbitrary contract calls with any `msg.sender` and `msg.data` of their choosing. This can be used to steal funds or perform other unauthorized actions in contracts that inherit from MetaContext.

Recommendation: 
The implementation of the `_msgSender` and `_msgData` functions in the MetaContext contract should be revised to prevent attackers from manipulating the `msg.sender` and `msg.data` values. One possible solution would be to use the `msg.sig` value to verify the authenticity of the `msg.sender` and `msg.data` values.

## 2. Unrestricted _msgSender() and _msgData() Modification

Summary: 
The `_msgSender()` and `_msgData()` functions in the contract `MetaContext` can be modified by any address that is mapped as a trusted forwarder in the `_isTrustedForwarder` mapping. This allows any address that is mapped as a trusted forwarder to potentially modify the caller and calldata of a message.

Impact: 
This vulnerability allows any address that is mapped as a trusted forwarder to potentially modify the caller and calldata of a message. This can be used to bypass access controls and potentially cause other issues if the caller and calldata are relied on for security.

Recommendation: 
The `_msgSender()` and `_msgData()` functions should only be modifiable by the contract owner. This can be done by adding an `onlyOwner` modifier to these functions. Alternatively, the `_isTrustedForwarder` mapping can be removed entirely and a different mechanism can be used to implement trusted forwarders.

# 13th report for : IBondNFT.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol

## 1. Incorrect implementation of `ownerOf()` function

Summary: 
The `ownerOf()` function in the `IBondNFT` interface is implemented incorrectly. It always returns the same value, regardless of the input.

Impact: 
This bug makes it impossible to determine the owner of a given NFT asset. This could result in incorrect or fraudulent transfers of assets.

Recommendation: 
The `ownerOf()` function should be implemented to return the correct owner of the NFT asset with the given ID.

## 2. Incorrect check for contract ownership in `claimDebt` function

Summary: 
The `claimDebt` function does not check if the caller is the contract owner before allowing them to claim debt. This can allow any caller to claim debt on behalf of the contract owner.

Impact: 
This can allow an attacker to steal funds from the contract by claiming debt on behalf of the contract owner.

Recommendation: 
Add a check to verify that the caller is the contract owner before allowing them to claim debt. This can be done by adding a simple if statement that checks the caller's address against the contract owner's address before allowing them to claim debt.

```
function claimDebt(
    address _owner,
    address _tigAsset
) external returns(uint amount) {
    // Add check to verify that caller is contract owner
    require(msg.sender == owner(), "Only contract owner can claim debt");

    // Rest of function code goes here
}
```
## 3. Missing function visibility

Summary: 
The `createLock` function of the `IBondNFT` interface does not have a visibility specified, which is required for Solidity functions. This can cause confusion and prevent the function from being called correctly.

Impact: 
If this function is called without specifying a visibility, it will not be executed and the contract's intended behavior will not be carried out. This can cause unexpected results and potentially lead to financial losses.

Recommendation: 
Add a visibility modifier (such as `public`) to the `createLock` function to specify how it can be accessed. This will ensure that the function can be called correctly and the contract will behave as intended.

# 14th report for : ILayerZeroEndpoint.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol

## 1. Missing the payable modifier on the send function

Summary: 
The send function is missing the payable modifier on the `_refundAddress` parameter, which may cause the function to not receive any ether sent to it.

Impact: 
This may cause the contract to not receive any ether sent to it via the `_refundAddress` parameter, which could cause issues with the contract's functionality.

Recommendation: 
Add the payable modifier to the `_refundAddress` parameter in the send function. For example:
```
function send(uint16 _dstChainId, bytes calldata _destination, bytes calldata _payload, address payable _refundAddress, address _zroPaymentAddress, bytes calldata _adapterParams) external payable;
```
## 2. Incorrectly typed parameter in `retryPayload()` function

Summary: 
The `retryPayload()` function in the provided contract has a wrongly typed parameter. The `_srcAddress` parameter is typed as `bytes calldata` but should be typed as `bytes`. This may cause the function to behave unexpectedly and potentially lead to vulnerabilities.

Impact: 
The incorrect typing of the `_srcAddress` parameter in the `retryPayload()` function can potentially lead to vulnerabilities in the contract. For example, if the `_srcAddress` parameter is not correctly interpreted by the function, it may cause the function to operate on the wrong contract and potentially lead to the loss of funds or other assets.

Recommendation: 
The `_srcAddress` parameter in the `retryPayload()` function should be correctly typed as `bytes` to ensure that the function operates as expected and does not introduce vulnerabilities. The function definition should be updated to the following:
```
function retryPayload(uint16 _srcChainId, bytes _srcAddress, bytes calldata _payload) external;
```
# 15th report for : lLayerZeroReceiver.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroReceiver.sol

## 1. Use of `bytes calldata` in `lzReceive` function

Summary: 
The `lzReceive` function in the provided code contains the use of the `bytes calldata` keyword, which is an experimental Solidity feature that is not recommended for use in production code.

Impact: 
Using the `bytes calldata` keyword in the `lzReceive` function may cause problems or unexpected behavior, especially if the contract is used in a live production environment.

Recommendation: 
It is recommended to replace the `bytes calldata` keyword with the regular `bytes` keyword in the `lzReceive` function. This will avoid any potential issues with the experimental keyword and improve the reliability and safety of the contract.

# 16th report for : ILayerZeroUserApplicationConfig.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol

## 1. Lack of input validation in `setSendVersion` and `setReceiveVersion` functions

Summary: 
The `setSendVersion` and `setReceiveVersion` functions in the code above do not perform any input validation on the `_version` parameter. This can potentially allow an attacker to specify a malicious value for `_version`, leading to security vulnerabilities in the contract.

Impact: 
If an attacker is able to specify a malicious value for the `_version` parameter in the `setSendVersion` or `setReceiveVersion` functions, they could potentially exploit vulnerabilities in the contract's messaging library to gain unauthorized access to contract functions or data, or execute arbitrary code. This could result in loss of funds or sensitive information.

Recommendation: 
It is recommended to add input validation to the `setSendVersion` and `setReceiveVersion` functions to ensure that only valid values for `_version` can be passed to these functions. This can help prevent potential security vulnerabilities in the contract.

## 2. Lack of Input Validation in `setConfig()` Function

Summary: 
The `setConfig()` function in the given Solidity code does not properly validate the input parameters `_chainId` and `_configType`, allowing an attacker to pass arbitrary values to these parameters.

Impact: 
An attacker could potentially use this vulnerability to modify the configuration of the LayerZero messaging library in unexpected ways, potentially leading to incorrect behavior or security vulnerabilities.

Recommendation: 
Input validation should be implemented for the `_chainId` and `_configType` parameters in the `setConfig()` function to ensure that only valid values are accepted. This will prevent attackers from passing arbitrary values to these parameters and potentially causing issues with the functionality of the contract.

# 17th report for : IPairsContract.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol

## 1. Lack of Proper Input Sanitization in `modifyLongOi` and `modifyShortOi` Functions

Summary:
The `modifyLongOi` and `modifyShortOi` functions in the contract do not properly sanitize the `_amount` input parameter, which could allow an attacker to manipulate the values of `longOi` and `shortOi` in the `OpenInterest` struct.

Impact:
This vulnerability could allow an attacker to manipulate the `longOi` and `shortOi` values in the `OpenInterest` struct, potentially allowing the attacker to gain an unfair advantage in the contract's functionality.

Recommendation:
To fix this vulnerability, the `modifyLongOi` and `modifyShortOi` functions should properly sanitize the `_amount` input parameter to ensure that it falls within the expected range of values. Additionally, the contract should enforce any additional constraints on the values of `longOi` and `shortOi` to prevent them from being set to arbitrary values.

# 18th report for : lPosition.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPosition.sol

## 1. Incorrect asset ownership when calling mint()

Summary: 
The `mint()` function accepts a `MintTrade` struct as input. This struct includes an `account` field, which is meant to be the address that owns the asset being minted. However, the function does not verify that the caller of the function actually owns the asset specified in the `MintTrade` struct. As a result, any address can mint any asset, even if they do not own it.

Impact: 
This vulnerability allows attackers to mint arbitrary assets without owning them. This can potentially be used to create fake assets, or to manipulate the market by minting large amounts of an asset and then selling it.

Recommendation: 
The `mint()` function should verify that the caller of the function actually owns the asset specified in the `MintTrade` struct before allowing them to mint it. This can be done by calling the `ownerOf()` function and checking that it returns the caller's address.

## 2. Access Control Vulnerability in `IPosition` Interface

Summary: 
The `IPosition` interface in the provided code contains a vulnerability in its `setAccInterest()` function that allows any Ethereum address to modify the `accInterest` field of any `Trade` struct. This field is intended to hold the accumulated interest for a given trade, but an attacker could exploit this vulnerability to set this value to an arbitrary number, potentially leading to the manipulation of trade balances and other unintended consequences.

Impact: 
This vulnerability could allow an attacker to manipulate trade balances and potentially gain an unfair advantage in the trading system implemented by the IPosition interface. This could lead to financial losses for legitimate traders and damage the overall integrity of the system.

Recommendation: 
The `setAccInterest()` function should include proper access controls to ensure that only authorized addresses can modify the `accInterest` field of a `Trade` struct. This could be achieved by requiring the caller to have a specific role or permission before allowing them to modify this field. Alternatively, the `accInterest` field could be made internal to the contract and only updated by other functions within the contract that have the necessary access controls in place.

# 19th report for : IReferrals.sol https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IReferrals.sol

## 1. Incorrect access control for referral code creation

Summary: 
The createReferralCode function of the IReferrals interface does not enforce any access control checks, allowing any contract or address to create referral codes.

Impact: 
This can allow unauthorized entities to create referral codes, potentially leading to security vulnerabilities or abuse of the referral code system.

Recommendation: 
Implement access control checks on the createReferralCode function to ensure that only authorized entities can create referral codes. This can be done by adding a require statement that checks the caller's address against a list of authorized addresses or contracts. For example:
```
function createReferralCode(bytes32 _hash) external {
    // Only allow authorized addresses to create referral codes
    require(msg.sender in authorizedAddresses, "Unauthorized address: cannot create referral codes.");
    
    // Create referral code
    // ...
}
```