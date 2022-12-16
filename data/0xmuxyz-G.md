## Report title
- Using `++i` for incrementing `i` inside of loop should be replaced with `unchecked { ++ }`


## Code Snippet that has not been optimized yet
- Below are lines that has not been optimized yet:
   https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol#L113-L119
```solidity
for (uint i=0; i < assets.length; i++) {
    uint balanceBefore = IERC20(assets[i]).balanceOf(address(this));
    IGovNFT(govNFT).claim(assets[i]);
    uint balanceAfter = IERC20(assets[i]).balanceOf(address(this));
    IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
    bondNFT.distribute(assets[i], balanceAfter - balanceBefore);
}
```

## Description
- The `unchecked` block is able to use from solidity version 0.8.0
- The variable `i` inside of loop is not able to overflow because of the condition `i < length`, where length is defined as uint256. The maximum value `i` that can reach is `max(uint)-1` . Therefore, incrementing `i` inside `unchecked` block is safe. Also consuming gas of incrementing `i` inside `unchecked` block is less than using `++i`.


## Recommendation
- Incrementing `i` inside `unchecked` block should be used like below: 
```solidity
for (uint i=0; i < assets.length;) {
    uint balanceBefore = IERC20(assets[i]).balanceOf(address(this));
    IGovNFT(govNFT).claim(assets[i]);
    uint balanceAfter = IERC20(assets[i]).balanceOf(address(this));
    IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
    bondNFT.distribute(assets[i], balanceAfter - balanceBefore);

    // Incrementing i inside unchecked block
    unchecked {
        i++
    }
}
```
