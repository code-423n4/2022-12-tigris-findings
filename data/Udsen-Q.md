## 1. EXPRESSIONS SHOULD USE IMMUTABLE INSTEAD OF CONSTANTS

There is a difference in the way constants and immutable are used. Each should be used in their appropriate contexts. Constants should be used where literal values are written into the code and immutable to used for expressions or values calculated in the constructor. 

    uint constant private DAY = 24 * 60 * 60;

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol#L10

The above should be declared either as a immutable variable and initialized inside the constructor or the final calculated value should be assigned to the constant as literal. 

## 2. DECLARED INTERNAL FUNCTION HAS NOT BEEN USED AND CAN BE REMOVED.

Even though below function is declared, it is not used. Hence it can be removed from the contract.

    function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
        return MetaContext._msgData();
    }

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol#62

## 3. USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

Can import the required specific contracts, functions or variables by using the named imports explicitly. Plain imports will import the entire context of the imported contract which could lead into variable name conflicts etc ...

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L4-L12

## 4. BE EXPLICIT WHEN DECLARING TYPES 

There are multiple occasions where `uint` has been used instead of `uint256`. It is best practice to be explicit when declaring types. All `uint` declarations can be changed to `uint256`

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L98-L103

## 5. USE UPPERCASE LETTERS WHEN DECLARING CONSTANTS

It is best practice and widely used convention to use uppercase letters when declaring `constants` in solidity.
There is one occasion constant is declared in lowercase letters.

    uint private constant liqPercent = 9e9; // 90%

This can be changed to:

    uint private constant LIQPERCENT = 9e9; // 90%

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L96

## 6. Missing `address(0)` checks for `_protocol`

    function setProtocol(address _protocol) external onlyOwner {
        protocol = _protocol;
    }

By mistake `_protocol` can be `address(0)`. Hence it is best practice to check for `address(0)` when assigning the value. 

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol#L129-131

## 7. EVENT IS MISSING `INDEXED` FIELDS

Each event can use upto three `indexed` fields if gas is not particularly of concern. Index event fields make the fields more quickly accessible to the off-chain tools that parse events.

    event PositionOpened(
        TradeInfo _tradeInfo,
        uint _orderType,
        uint _price,
        uint _id,
        address _trader,
        uint _marginAfterFees
    );

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L983-1053
