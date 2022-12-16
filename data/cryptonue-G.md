# [G-01] Merge function with Same logic

Instead of creating a separate function, it's better to just pack two function with same logic into a new general function (and distinguish between the two logic with a parameter)

The following functions are quite identical, only change a bit, which can be merge into one function

```solidity
File: PairsContract.sol
154:     function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
155:         if (_onOpen) {
156:             _idToOi[_asset][_tigAsset].longOi += _amount;
157:             require(_idToOi[_asset][_tigAsset].longOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxLongOi");
158:         }
159:         else {
160:             _idToOi[_asset][_tigAsset].longOi -= _amount;
161:             if (_idToOi[_asset][_tigAsset].longOi < 1e9) {
162:                 _idToOi[_asset][_tigAsset].longOi = 0;
163:             }
164:         }
165:     }
...
174:     function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external onlyProtocol {
175:         if (_onOpen) {
176:             _idToOi[_asset][_tigAsset].shortOi += _amount;
177:             require(_idToOi[_asset][_tigAsset].shortOi <= _idToOi[_asset][_tigAsset].maxOi || _idToOi[_asset][_tigAsset].maxOi == 0, "MaxShortOi");
178:             }
179:         else {
180:             _idToOi[_asset][_tigAsset].shortOi -= _amount;
181:             if (_idToOi[_asset][_tigAsset].shortOi < 1e9) {
182:                 _idToOi[_asset][_tigAsset].shortOi = 0;
183:             }
184:         }
185:     }
```

#

# [G-02] USING `PRIVATE` RATHER THAN `PUBLIC` FOR CONSTANTS, SAVES GAS

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

```solidity
File: Lock.sol
12:     uint public constant minPeriod = 7;
13:     uint public constant maxPeriod = 365;
```