1:
initiateLimitOrder() no check _orderType range
only check _orderType!=0,not check only in [1,2]
it can pass orderType>2 now
suggest:

```solidity
    function initiateLimitOrder(
        TradeInfo calldata _tradeInfo,
        uint256 _orderType, // 1 limit, 2 stop
        uint256 _price,
        ERC20PermitData calldata _permitData,
        address _trader
    )
        external
    {
..    
-       if (_orderType == 0) revert("5"); 
+       if (_orderType == 0 || _orderType > 2) revert("5"); 
```