# Emit Events When Changing Critical Aspects of Protocol

It is best practice to emit events for functions that change critical aspects of a protocol in order to allow off-chain monitoring to trigger. For example, a hacker could gain access to the private keys of a deployer or find a vulnerability to approve their own proposals for a multisig/DAO. Off-chain monitoring would allow the team to respond quickly and let users know what is happening as soon as possible.

### Affected Functions
`Trading.sol`
```
setBlockDelay (Line 902)
setAllowedVault (Line 916)
setMaxWinPercent (Line 930)
setLimitOrderPriceRange (Line 943)
setFees (Line 956)
setTradingExtension (Line 979)
```