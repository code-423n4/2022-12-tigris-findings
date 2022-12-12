
##

## [GAS-1]  NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

[File: 2022-12-tigris/contracts/Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol)

     20:     external returns(uint256 _price, uint256 _spread);

     55:     external returns (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout);

[File: 2022-12-tigris/contracts/TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol)

       65:    external onlyProtocol returns (IPosition.Trade memory _trade, uint256 _positionSize, int256 _payout) {

       170:  returns(uint256 _price, uint256 _spread) 

##

##  [GAS-2]  For || operator checks we don't want to check all conditions. As per || results any one condition true over all condition checks become true. If any one condition is true then other condition checks are waste of computing power and gas . So its better we can avoid condition checks after true

[File: 2022-12-tigris/contracts/Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol)

      241:   if (_percent > DIVISION_CONSTANT || _percent == 0) revert BadClosePercent();

      496:   if (_price > trade.price+trade.price*limitOrderPriceRange/DIVISION_CONSTANT || _price < trade.price- 
                trade.price*limitOrderPriceRange/DIVISION_CONSTANT) revert("6"); 

     877:    require(_token == IStableVault(_stableVault).stable() || IStableVault(_stableVault).allowed(_token), "Token not approved in vault");

[File: 2022-12-tigris/contracts/TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol)

         217:   if (_leverage < asset.minLeverage || _leverage > asset.maxLeverage) revert("!lev");

##

## [GAS-3]  USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

[File: 2022-12-tigris/contracts/Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol)

     2:  pragma solidity ^0.8.0;

##

## [GAS-4]     Instead of using operator && on single require or if checks . Using double REQUIRE OR IF checks can save more gas. 

        
[File: 2022-12-tigris/contracts/Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol)

       497:  if (trade.direction && trade.orderType == 1) {

       499:  } else if (!trade.direction && trade.orderType == 1) {

       501:   } else if (!trade.direction && trade.orderType == 2) {

       625:   if (maxWinPercent > 0 && _toMint > _trade.margin*maxWinPercent/DIVISION_CONSTANT) {

       838:  if (_sl < _price && _sl != 0) revert("3");

      887:   require(_proxy.proxy == _msgSender() && _proxy.time >= block.timestamp, "Proxy not approved");

##

## [GAS-5] <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES . FOR EVERY CALL CAN SAVE 13 GAS 

   [File: 2022-12-tigris/contracts/Trading.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol)

         509:   trade.price += trade.price * _spread / DIVISION_CONSTANT;

        511:    trade.price -= trade.price * _spread / DIVISION_CONSTANT;

##

## [GAS-6]  MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE

[File: 2022-12-tigris/contracts/TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol)

         mapping(address => bool) private isNode;
         mapping(address => uint) public minPositionSize;
         mapping(address => bool) public allowedMargin;

##

## [GAS-7]  FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

[File: 2022-12-tigris/contracts/TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol)

       function setValidSignatureTimer(
        uint _validSignatureTimer
       )
        external
        onlyOwner

      231:  function setChainlinkEnabled(bool _bool) external onlyOwner {

      240:  function setNode(address _node, bool _bool) external onlyOwner {

        function setAllowedMargin(
        address _tigAsset,
        bool _bool
        ) 
        external
        onlyOwner

        function setMinPositionSize(
        address _tigAsset,
        uint _min
       ) 
        external
        onlyOwner

      274:    function setPaused(bool _paused) external onlyOwner {

##

## [GAS-8]  USE FUNCTION INSTEAD OF MODIFIERS


[File: 2022-12-tigris/contracts/TradingExtension.sol](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol)  

       modifier onlyProtocol { 
        require(msg.sender == trading, "!protocol");
        _;
        }













GAS-1	Use assembly to check for address(0)	12
GAS-2	Using bools for storage incurs overhead	17
GAS-3	Cache array length outside of loop	13
GAS-4	State variables should be cached in stack variables rather than re-reading them from storage	6
GAS-5	Use calldata instead of memory for function arguments that do not get mutated	25
GAS-6	Use Custom Errors	95
GAS-7	Don't initialize variables with default value	20
GAS-8	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	21
GAS-9	Using private rather than public for constants, saves gas	3
GAS-10	Use != 0 instead of > 0 for unsigned integer comparison	22