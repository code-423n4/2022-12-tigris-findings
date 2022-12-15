## Consider Deprecated  ReentrancyGuard dependency in contract StableVault.sol 
import "@openzeppelin/contracts/security/ReentrancyGuard.sol"; L5


## Check Effects Interactions pattern not respected
To avoid unexpected behavior in the future (be it for the solution or for a fork), it’s recommended to always follow the CEI pattern.

Consider always moving the state-changes before the external calls.

While here, there’s no real impact, it’s still a bad practice ( has a callback, and a state change is occurring just after the call, albeit under certain conditions and without any thinkable impact here):

 _safeMint(_mintTrade.account, newTokenID);  position.sol 148

function release BondNFT.sol

(_claimAmount) = claim(_id,bond.owner) (contracts/BondNFT.sol#151)
IERC20(tigAsset).transfer(manager,amount) (contracts/BondNFT.sol#185)
_burn(_id) (contracts/BondNFT.sol#157)
delete _idToBond[_id] (contracts/BondNFT.sol#319)


## Unused named returns
While not consuming more gas with the Optimizer enabled: using both named returns and a return statement isn't necessary. Removing one of those can improve code clarity:

BondNFT.sol
returns(uint id) L62
returns(uint amount, uint lockAmount, address asset, address _owner) L140
returns(uint amount, address tigAsset) L171
returns(uint amount) L199

Trading.sol
returns(uint256 _price, uint256 _spread) L29
returns(uint _limitPrice, address _tigAsset) L49

TradingExtension.sol
_limitPreturns(uintrice, address _tigAsset) L93
 returns(uint256 _price, uint256 _spread) L170

/interfaces/IBONDnft.sol
returns(uint id) L10
returns(uint amount, address tigAsset) L23
returns(uint amount) L28
returns(uint amount, uint lockAmount, address asset, address _owner); L33



## Consider Warning & Error Compilled

 TypeError: Invalid contract specified in override list: "IERC721".
  --> contracts/Position.sol:11:44:
   |
11 |     function ownerOf(uint _id) public view override(ERC721, IERC721, IPosition) returns (address) {
   |                                            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Note: This contract:
  --> @openzeppelin/contracts/token/ERC721/IERC721.sol:11:1:
   |
11 | interface IERC721 is IERC165 {
   | ^ (Relevant source part starts here and spans across multiple lines).

Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> contracts/mock/BadStableVault.sol:45:23:
   |
45 |     function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
   |                       ^^^^^^^^^^^^^^


Warning: Function state mutability can be restricted to pure
  --> contracts/mock/BadStableVault.sol:45:5:
   |
45 |     function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning: Contract code size exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
 --> contracts/BondNFT.sol:8:1:
  |
8 | contract BondNFT is ERC721Enumerable, Ownable {
  | ^ (Relevant source part starts here and spans across multiple lines).


Warning: Contract code size exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
  --> contracts/GovNFT.sol:12:1:
   |
12 | contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {
   | ^ (Relevant source part starts here and spans across multiple lines).


Warning: Contract code size exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
  --> contracts/GovNFTBridged.sol:12:1:
   |
12 | contract GovNFTBridged is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {
   | ^ (Relevant source part starts here and spans across multiple lines).

Warning: Contract code size exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
 --> contracts/Position.sol:9:1:
  |
9 | contract Position is ERC721Enumerable, MetaContext, IPosition {
  | ^ (Relevant source part starts here and spans across multiple lines).


Warning: Contract code size exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
  --> contracts/Trading.sol:79:1:
   |
79 | contract Trading is MetaContext, ITrading {
   | ^ (Relevant source part starts here and spans across multiple lines).