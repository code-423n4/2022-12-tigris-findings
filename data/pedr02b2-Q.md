### require and revert statements

require and revert should have more of a descriptive reason so that the user is aware of exactly why the revert has occured for them

 https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L101 

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L238
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L428
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L364
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L392

### typo's

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L1
PairsContract.sol:1://SPDX-License-Identifier: Unlicense
should be
PairsContract.sol:1://SPDX-License-Identifier: Unlicensed

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L1
Referrals.sol:1://SPDX-License-Identifier: Unlicense
should be 
Referrals.sol:1://SPDX-License-Identifier: Unlicensed

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L1
Referrals.sol:1://SPDX-License-Identifier: Unlicense
should be 
Referrals.sol:1://SPDX-License-Identifier: Unlicensed

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L1
Trading.sol:1://SPDX-License-Identifier: Unlicense
should be 
Trading.sol:1://SPDX-License-Identifier: Unlicensed

### Const variable naming convention
All capital naming convention should be used for immutable constant values

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L15
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L16
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L33

### Event missing index

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each 
index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event 
(threefields). Each event should use three indexed fields if there are three or more fields, and gas usage is not 
particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L24
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L25
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L26
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L196
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L85
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L86
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L377 - L3882
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L983 - 1053


### Solidity styling conventions

The following Files does not follow standard solidity style conventions with regards to the placement of the events and errors, consider following the solidity standard styleing conventions by placing such things as events, custom errors, constants functions in the correct order when writing contracts such as :-


1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions 

https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

### Public functions
public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L171
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L199
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L255
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L250
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L262
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L266
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L274
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L339
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L368

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L183
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L206
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L269
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/GovNFT.sol#L315

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L110

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/PairsContract.sol#L22

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L40
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L41
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L66
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L67
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L70
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Position.sol#L74

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L44

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L122


### No 0 address checks on contract constructors
Consider adding an address(0) check for these contructors to ensure that a 0 address cannot be input upon contract launch
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Lock.sol#L21
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L35
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Trading.sol#L143
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L28

### Create Lock function

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L57

The createLock function allows anyone to create a new bond and specify the owner of the bond, which could potentially be exploited to create bonds on behalf of other users without their consent. To mitigate this risk, the createLock function should only be called by the manager address, and the owner of the bond should be verified before the bond is created.

function createLock(
    address _asset,
    uint _amount,
    uint _period,
    address _owner
) external onlyManager() returns(uint id) {
    require(msg.sender == _owner, "Invalid owner");

###  Extend lock function
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/BondNFT.sol#L97

The extendLock function allows the owner of a bond to extend the lock period and/or amount of their bond. This could potentially be exploited to indefinitely extend the lock period, preventing the asset from being released. To mitigate this risk, the extendLock function could include a maximum limit on the number of times a bond can be extended, or a maximum total lock period.

possible example of mitigation

struct Bond {
    uint id;
    address owner;
    address asset;
    uint amount;
    uint mintEpoch;
    uint mintTime;
    uint expireEpoch;
    uint pending;
    uint shares;
    uint period;
    bool expired;
    uint extensions;  // new field to track number of extensions
}

function extendLock(
    uint _id,
    address _asset,
    uint _amount,
    uint _period,
    address _sender
) external onlyManager() {
    Bond memory bond = idToBond(_id);
    Bond storage _bond = _idToBond[_id];
    require(bond.owner == _sender, "!owner");
    require(!bond.expired, "Expired");
    require(bond.asset == _asset, "!asset");
    require(_amount > 0, "!amount");
    require(_period > 0, "!period");

    // Add check for maximum number of extensions
    require(bond.extensions < MAX_EXTENSIONS, "Exceeded maximum number of extensions");
    // Increment extension counter
    _bond.extensions++;
    // ... rest of function remains unchanged

### Function commented out as deprecated 
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

The initRefs function is marked as deprecated by the devs @ comments but is still able to be called. It is possible that this function could still be used by mistake, which could lead to unexpected behavior or errors. remove this function if it isnt to be used or cause it to throw an error if called so that it cannot be used accidently, if it still to be used explain further in the comments why it is deprecated but still able to be called
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/Referrals.sol#L58

### function can be marked as private or be removed 

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableToken.sol#L57
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableToken.sol#L62

The _msgSender and _msgData functions override the corresponding functions in the MetaContext contract. However, these functions are marked as internal and are not intended to be called externally. It is possible that these functions could be called by mistake, which could lead to unexpected behavior or errors.
To mitigate this issue, it would be advisable to mark these functions as private to prevent them from being called externally. Alternatively, these functions could be removed entirely if they are not used by any other functions in the contract.

### No checks for ERC20 
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L44
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L65

There is no check to ensure that the token being deposited or withdrawn is actually an ERC20 token. This could lead to issues if a user tries to deposit or withdraw a non-ERC20 token, as it will fail to implement the necessary methods.Add a check to ensure that the token being deposited or withdrawn is an ERC20 token by checking that it implements the IERC20 interface. consider updating the code to the following to carry out these checks

function deposit(address _token, uint256 _amount) public {
require(IERC20(_token).totalSupply() > 0, "Token must be ERC20");
require(allowed[_token], "Token not listed");
IERC20(_token).transferFrom(_msgSender(), address(this), _amount);
IERC20Mintable(stable).mintFor(
_msgSender(),
_amount*(10**(18-IERC20Mintable(_token).decimals()))
);
}

function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
require(IERC20(_token).totalSupply() > 0, "Token must be ERC20");
IERC20Mintable(stable).burnFrom(_msgSender(), _amount);
_output = _amount/10**(18-IERC20Mintable(_token).decimals());
IERC20(_token).transfer(
_msgSender(),
_output
);
}

### No checks on users balance 
https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol

There is no check to ensure that the user has enough balance of the token they are trying to deposit or withdraw. If a user tries to deposit or withdraw more tokens than they have, the transfer or burn methods will throw an error and the state of the contract will be left in an inconsistent state./Add a check to ensure that the user has enough balance of the token they are trying to deposit or withdraw before calling the transfer or burn methods. This can be done by calling the balanceOf method on the token contract and comparing it to the amount being deposited or withdrawn

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L44
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/StableVault.sol#L65

Alice has a balance of 10 tokens of type A and wants to deposit 20 tokens into the contract. She calls the deposit function with the arguments _token set to the address of the token contract for type A and _amount set to 20.

The contract checks the total supply of type A tokens to ensure that they are an ERC20 token, as well as checking that type A is listed as an allowed token.
The contract then calls the transferFrom method on the token contract for type A to transfer 20 tokens from Alice's balance to the contract's balance.
At this point, an error is thrown because Alice does not have enough tokens to cover the deposit. The transferFrom method fails and the state of the contract is not updated to reflect the failed transaction. However, Alice's balance of type A tokens is now reduced by 20, leaving her with a balance of -10 tokens.

Now suppose Alice tries to withdraw 10 stable coins from the contract. She calls the withdraw function with the arguments _token set to the address of the token contract for the stable coin and _amount set to 10.

The contract checks the total supply of the stable coin to ensure that it is an ERC20 token.
The contract then calls the burnFrom method on the stable coin contract to burn 10 stable coins from Alice's balance.
At this point, an error is thrown because Alice does not have enough stable coins to cover the withdrawal. The burnFrom method fails and the state of the contract is not updated to reflect the failed transaction. However, Alice's balance of stable coins is now reduced by 10, leaving her with a balance of -10 stable coins.

possible code update example :- 

function deposit(address _token, uint256 _amount) public {
  require(IERC20(_token).totalSupply() > 0, "Token must be ERC20");
  require(allowed[_token], "Token not listed");
  require(IERC20(_token).balanceOf(_msgSender()) >= _amount, "Insufficient balance");
  IERC20(_token).transferFrom(_msgSender(), address(this), _amount);
  IERC20Mintable(stable).mintFor(
    _msgSender(),
    _amount*(10**(18-IERC20Mintable(_token).decimals()))
  );
}

function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
  require(IERC20(_token).totalSupply() > 0, "Token must be ERC20");
  require(IERC20Mintable(stable).balanceOf(_msgSender()) >= _amount, "Insufficient balance");
  IERC20Mintable(stable).burnFrom(_msgSender(), _amount);
  _output = _amount/10**(18-IERC20Mintable(_token).decimals());
  IERC20(_token).transfer(
    _msgSender(),
    _output
  );
}

### Consider increasing or using a higher gas value

https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L26

The maxGasPrice variable is set to 1000 gwei, which may not be sufficient to cover gas costs during times of high network usage. Consider increasing this value or implementing a dynamic gas price strategy to ensure that transactions can be successfully mined.

### Unchecked interger underflow/overflow
https://github.com/code-423n4/2022-12-tigris/blob/588c84b7bb354d20cbca6034544c4faa46e6a80e/contracts/TradingExtension.sol#L61

The _closePosition function does not check for overflows or underflows when performing arithmetic operations, which can lead to security vulnerabilities. For example, the division operator is used to divide two unsigned integers, but it does not check for division by zero or if the result of the division would cause an overflow. To mitigate this issue, consider using the SafeMath library or manually checking for overflows and underflows.

possible mitigation example :- 
import "@openzeppelin/contracts/math/SafeMath.sol";

contract TradingExtension is Ownable{
    using SafeMath for uint256;

    // ...

    function _closePosition(
        uint _id,
        uint _price,
        uint _percent
    ) external onlyProtocol returns (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) {
        _trade = position.trades(_id);

        // Use SafeMath to automatically handle overflows and underflows
        uint positionSize = (_trade.margin*_trade.leverage/1e18).mul(_percent).div(DIVISION_CONSTANT);
        (_positionSize, _payout) = TradingLibrary.pnl(_trade.direction, _price, _trade.price, _trade.margin, _trade.leverage, _trade.accInterest);

        if (_trade.direction) {
            modifyLongOi(_trade.asset, _trade.tigAsset, false, positionSize);
        } else {
            modifyShortOi(_trade.asset, _trade.tigAsset, false, positionSize);     
        }
    }
}









