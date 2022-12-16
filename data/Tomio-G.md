1.
Title: Using multiple `require` instead `&&` can save gas

Proof of Concept:
[Trading.sol#L887](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol#L887)
[TradingLibrary.sol#L116-L119](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L116-L119)

Recommended Mitigation Steps:
Change to:

```
	require(_proxy.proxy == _msgSender(), "Proxy not approved");
	require(_proxy.time >= block.timestamp, "Proxy not approved");
```
________________________________________________________________________

2.
Title: Comparison operators

Proof of Concept:
[TradingLibrary.sol#L109-L110](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol#L109-L110)

Recommended Mitigation Steps:
Replace `<=` with `<`, and `>=` with `>` for gas opt
________________________________________________________________________

3.
Title: Gas savings for using solidity 0.8.10

impact:
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

Proof of Concept:
All contract in scope

Recommended Mitigation Steps:
Consider to upgrade pragma to at least 0.8.10.

Solidity 0.8.10 has a useful change which reduced gas costs of external calls
Reference: [here](https://blog.soliditylang.org/2021/11/09/solidity-0.8.10-release-announcement/)
______________________________________________________________________

4.
Title: Reduce the size of error messages (Long revert Strings)

Impact:
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Proof of Concept:
[GovNFT.sol#L155](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L155)
[GovNFT.sol#L185](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L185)
[GovNFT.sol#L209-L210](https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol#L209-L210)

Recommended Mitigation Steps:
Consider shortening the revert strings to fit in 32 bytes
________________________________________________________________________