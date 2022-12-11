Many contracts such as The Trading and BondNFT contracts uses the transfer() methods to send ethers, within these methods a fixed amount of 2300 gas **at least** is always passed ,and also keeping in mind that the gas amount of EVM transactions can be changed anytime especially during hard forks, if this deployed already and that change happens the contract will then be using a very high amount of gas, also there is a high chance that the gas for this transaction may not be sufficient and the transfer can revert

so, it's better to use the builtin call() function  payable(_proxy).call{value:msg.value}("") instead.

for more see : https://eips.ethereum.org/EIPS/eip-1884#backwards-compatibility, https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/