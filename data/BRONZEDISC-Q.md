## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/NoTransferToken.sol

```solidity
// modifiers coming after constructor & functions
72:     modifier onlyOwner() {
639:    modifier onlyMinter() {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

```solidity
// modifier coming after constructor and functions
278:    modifier onlyProtocol { 
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol

```solidity
// modifier coming after constructor and functions
51:    modifier onlyMinter() {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

```solidity
// modifier and events are coming after constructor and functions
80:    modifier onlyProtocol() {
85:    event ReferralCreated(address _referrer, bytes32 _hash);
86:    event Referred(address _referredTrader, bytes32 _hash);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```solidity
// state variables, constructor and modifier coming after functions
73:    Counters.Counter private _tokenIds;
74:    string public baseURI;
76:    constructor(string memory _setBaseURI, string memory _name, string memory _symbol) ERC721(_name, _symbol) {
314:    modifier onlyMinter() {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```solidity

// these two declarations are not following the right pattern as they are at the end of the code
189:    modifier onlyProtocol() {
196:    event AssetAdded(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFTBridged.sol

```solidity
//  state variables being declared after functions and constructor
224:    address[] public assets;
225:    mapping(address => bool) private _allowedAsset;
226:    mapping(address => uint) private assetsIndex;
227:    mapping(address => mapping(address => uint256)) private userPaid;
228:    mapping(address => mapping(address => uint256)) private userDebt;
229:    mapping(address => uint256) private accRewardsPerNFT;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```solidity
//  state variables being declared after functions and constructor
264:    address[] public assets;
265:    mapping(address => bool) private _allowedAsset;
266:    mapping(address => uint) private assetsIndex;
267:    mapping(address => mapping(address => uint256)) private userPaid;
268:    mapping(address => mapping(address => uint256)) private userDebt;
269:    mapping(address => uint256) private accRewardsPerNFT;
```


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```solidity
// struct coming before state variables
12:    struct Bond {


// modifier coming after constructor and functions
372:      modifier onlyManager() {

// events should come right after state variables
377:    event Distribution(address _tigAsset, uint256 _amount);
378:    event Lock(address _tigAsset, uint256 _amount, uint256 _period, address _owner, uint256 _id);
379:    event ExtendLock(uint256 _period, uint256 _amount, address _owner, uint256 _id);
380:    event Release(address _tigAsset, uint256 _amount, address _owner, uint256 _id);
381:    event ClaimFees(address _tigAsset, uint256 _amount, address _claimer, uint256 _id);
382:    event ClaimDebt(address _tigAsset, uint256 _amount, address _owner);
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Within a grouping, place the view and pure functions last.


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```solidity
// internal functions should come last
81:    function _baseURI() internal override view returns (string memory) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/NFTSale.sol

```solidity
// view functions should come after all non-view ones
36:    function available() external view returns (uint) {
```


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFTBridged.sol

```solidity 
    Different visibility functions are mixing throughout the code, they should be ordered as listed above
```


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```solidity
    Different visibility functions are mixing throughout the code, they should be ordered as listed above
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```solidity
    Different visibility functions are mixing throughout the code, they should be ordered as listed above
```

---

### natSpec missing [3]

- It is recommended that Solidity contracts are fully annotated using NatSpec.

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

```solidity
7:    interface IPrice {

8:    function latestAnswer() external view returns (int256);

9:    function decimals() external view returns (uint256);

12:   struct PriceData {

21:   library TradingLibrary {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol

```solidity
6:    contract MetaContext is Ownable {

9:    function setTrustedForwarder(address _forwarder, bool _bool) external onlyOwner {

13:    function isTrustedForwarder(address _forwarder) external view returns (bool) {

17:    function _msgSender() internal view virtual override returns (address sender) {

29:    function _msgData() internal view virtual override returns (bytes calldata) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/WETH.sol

```solidity
9:    event  Approval(address indexed src, address indexed guy, uint wad);

10:    event  Transfer(address indexed src, address indexed dst, uint wad);

11:    event  Deposit(address indexed dst, uint wad);

12:    event  Withdrawal(address indexed src, uint wad);

17:    function deposit() public payable {

21:    function withdraw(uint wad) public {

28:    function totalSupply() public view returns (uint) {

32:    function approve(address guy, uint wad) public returns (bool) {

38:    function transfer(address dst, uint wad) public returns (bool) {

42:    function transferFrom(address src, address dst, uint wad)
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/NoTransferToken.sol

```solidity
21:    function _msgSender() internal view virtual returns (address) {

25:    function _msgData() internal view virtual returns (bytes calldata) {

53:    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

599:    constructor(string memory name_, string memory symbol_) ERC20(name_, symbol_) {}

601:    function burnFrom(

612:    function mintFor(
``

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MockEndpoint.sol

```solidity
30:    struct StoredPayload {

36:    struct QueuedPayload {

51:    event UaForceResumeReceive(uint16 chainId, bytes srcAddress);

52:    event PayloadCleared(uint16 srcChainId, bytes srcAddress, uint64 nonce, address dstAddress);

53:    event PayloadStored(uint16 srcChainId, bytes srcAddress, address dstAddress, uint64 nonce, bytes payload, bytes reason);

55:    constructor(uint16 _chainId) {

// incomplete
62:    function setEstimatedFees(uint _nativeFee, uint _zroFee) public {

67:    function getChainId() external view override returns (uint16) {

71:    function setDestLzEndpoint(address destAddr, address lzEndpointAddr) external {

75:    function send(

116:    function receivePayload(

167:    function getLengthOfQueue(uint16 _srcChainId, bytes calldata _srcAddress) external view returns (uint) {

199:    function setConfig(

206:    function getConfig(

215:    function setSendVersion(

219:    function setReceiveVersion(

223:    function getSendVersion(

229:    function getReceiveVersion(

235:    function getInboundNonce(uint16 _chainID, bytes calldata _srcAddress) external view override returns (uint64) {

239:    function getOutboundNonce(uint16 _chainID, address _srcAddress) external view override returns (uint64) {

255:    function forceResumeReceive(uint16 _srcChainId, bytes calldata _srcAddress) external override {

272:    function retryPayload(uint16 _srcChainId, bytes calldata _srcAddress, bytes calldata _payload) external override {

289:    function hasStoredPayload(uint16 _srcChainId, bytes calldata _srcAddress) external view override returns (bool) {

294:    function isSendingPayload() external pure override returns (bool) {

298:    function isReceivingPayload() external pure override returns (bool) {

302:    function getSendLibraryAddress(address) external view override returns (address) {

306:    function getReceiveLibraryAddress(address) external view override returns (address) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MockERC20.sol

```solidity
6:  contract MockERC20 is ERC20Permit {

11:    constructor(string memory _name, string memory _symbol, uint8 decimals_, address _initialAccount, uint256 _initialBalance) ERC20(_name, _symbol) ERC20Permit(_name) {

17:    function decimals() public view override returns (uint8) {

21:    function mint(address _account, uint _amount) external {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MockChainlinkFeed.sol

```solidity
5:    interface IPrice {

6:    function latestAnswer() external view returns (int256);

7:    function decimals() external view returns (uint256);

10:   contract MockChainlinkFeed is IPrice {

14:    function latestAnswer() external view returns (int256) {

18:    function decimals() external pure returns (uint256) {

22:    function setPrice(int256 _price) external {

```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MetaContextTest.sol

```solidity
7:  contract MetaContextTest is MetaContext {

14:    function getMsgSender(uint256 _value) external returns (address) {

20:    function getMsgData() external returns (bytes memory) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/BadStableVault.sol

```solidity
9:    interface IERC20Mintable is IERC20 {

10:    function mintFor(address, uint256) external;

11:    function burnFrom(address, uint256) external;

12:    function decimals() external view returns (uint);

15:    interface ERC20Permit is IERC20 {

16:    function permit(

27:     contract BadStableVault is MetaContext, IStableVault {

35:    constructor(address _stable) {

39:    function deposit(address _token, uint256 _amount) public {

42:    function depositWithPermit(address _token, uint256 _amount, uint256 _deadline, bool _permitMax, uint8 v, bytes32 r, bytes32 s) external {

45:    function withdraw(address _token, uint256 _amount) external returns (uint256 _output) {

49:    function listToken(address _token) external onlyOwner {

53:    function delistToken(address _token) external onlyOwner {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol

```solidity
7:    interface ITrading {

9:    struct TradeInfo {

21:    struct ERC20PermitData {

30:    function initiateMarketOrder(

38:    function initiateCloseOrder(

48:    function addMargin(

57:    function removeMargin(

67:    function addToPosition(

78:    function initiateLimitOrder(

86:    function cancelLimitOrder(

91:    function updateTpSl(

100:    function executeLimitOrder(

106:    function liquidatePosition(

112:    function limitClose(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IStableVault.sol

```solidity
5:    interface IStableVault {

6:    function deposit(address, uint) external;

7:    function withdraw(address, uint) external returns (uint256);

8:    function allowed(address) external view returns (bool);

9:    function stable() external view returns (address);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IReferrals.sol

```solidity
5:    interface IReferrals {

7:    function createReferralCode(bytes32 _hash) external;

8:    function setReferred(address _referredTrader, bytes32 _hash) external;

9:    function getReferred(address _trader) external view returns (bytes32);

10:    function getReferral(bytes32 _hash) external view returns (address);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol

```solidity
5:    interface IPairsContract {

7:    struct Asset {

16:    struct OpenInterest {

22:    function allowedAsset(uint) external view returns (bool);

23:    function idToAsset(uint256 _asset) external view returns (Asset memory);

24:    function idToOi(uint256 _asset, address _tigAsset) external view returns (OpenInterest memory);

25:    function setAssetBaseFundingRate(uint256 _asset, uint256 _baseFundingRate) external;

26:    function modifyLongOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external;

27:    function modifyShortOi(uint256 _asset, address _tigAsset, bool _onOpen, uint256 _amount) external;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IGovNFT.sol

```solidity
5:    interface IGovNFT {

6:    function distribute(address _tigAsset, uint _amount) external;

7:    function safeTransferMany(address _to, uint[] calldata _ids) external;

8:    function claim(address _tigAsset) external;

9:    function pending(address user, address _tigAsset) external view returns (uint256);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol

```solidity
4:    interface IBondNFT {

5:    function createLock(

12:    function extendLock(

20:    function claim(

25:    function claimDebt(

30:    function release(

35:    function distribute(

40:    function ownerOf(uint _id) external view returns (uint256);

42:    function totalAssets() external view returns (uint256);

43:    function getAssets() external view returns (address[] memory);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

```solidity
10:  contract TradingExtension is Ownable{

28:    constructor(

122:    function _checkGas() public view {

126:    function modifyShortOi(

135:    function modifyLongOi(

144:    function setMaxGasPrice(uint _maxGasPrice) external onlyOwner {

148:    function getRef(

222:    function setValidSignatureTimer(

231:    function setChainlinkEnabled(bool _bool) external onlyOwner {

274:    function setPaused(bool _paused) external onlyOwner {

278:    modifier onlyProtocol { 
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```solidity
14:    interface ITradingExtension {

15:    function getVerifiedPrice(

21:    function getRef(

24:    function _setReferral(

28:    function validateTrade(uint _asset, address _tigAsset, uint _margin, uint _leverage) external view;

29:    function isPaused() external view returns(bool);

30:    function minPos(address) external view returns(uint);

31:    function modifyLongOi(

37:    function modifyShortOi(

43:    function paused() external returns(bool);

44:    function _limitClose(

50:    function _checkGas() external view;

51:    function _closePosition(

58:    interface IStable is IERC20 {

59:    function burnFrom(address account, uint amount) external;

60:    function mintFor(address account, uint amount) external;

63:    interface ExtendedIERC20 is IERC20 {

64:    function decimals() external view returns (uint);

67:    interface ERC20Permit is IERC20 {

68:    function permit(

79:     contract Trading is MetaContext, ITrading {

98:    struct Fees {

136:    struct Proxy {

143:    constructor(

983:    event PositionOpened(

992:    event PositionClosed(

1001:    event PositionLiquidated(

1007:    event LimitOrderExecuted(

1018:    event UpdateTPSL(

1025:    event LimitCancelled(

1030:    event MarginModified(

1038:    event AddToPosition(

1045:    event FeesDistributed(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Timelock.sol

```solidity
6:   contract Timelock is TimelockController {

7:    constructor(address[] memory _proposers, address[] memory _executors, uint256 _time) TimelockController(_time, _proposers, _executors, address(0)) {}
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol

```solidity
7:  contract StableToken is ERC20Permit, MetaContext {

11:    constructor(string memory name_, string memory symbol_) ERC20Permit(name_) ERC20(name_, symbol_) {}

13:    function burnFrom(

24:    function mintFor(

// incomplete
38:    function setMinter(

57:    function _msgSender() internal view override(Context, MetaContext) returns (address sender) {

62:    function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol

```solidity
9:   interface IERC20Mintable is IERC20 {

10:    function mintFor(address, uint256) external;

11:    function burnFrom(address, uint256) external;

12:    function decimals() external view returns (uint);

15:    interface ERC20Permit is IERC20 {

16:    function permit(

27:    contract StableVault is MetaContext, IStableVault {

35:    constructor(address _stable) {

53:    function depositWithPermit(address _token, uint256 _amount, uint256 _deadline, bool _permitMax, uint8 v, bytes32 r, bytes32 s) external {


```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

```solidity
43:    function getReferred(address _trader) external view returns (bytes32) {

47:    function getReferral(bytes32 _hash) external view returns (address) {

53:    function setProtocol(address _protocol) external onlyOwner {

// if is deprecated then maybe remove it from the codebase?
   /**
    * @notice deprecated
    */
60:    function initRefs(

80:    modifier onlyProtocol() {

85:    event ReferralCreated(address _referrer, bytes32 _hash);

86:    event Referred(address _referredTrader, bytes32 _hash);
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```solidity
9:   contract Position is ERC721Enumerable, MetaContext, IPosition {

11:    function ownerOf(uint _id) public view override(ERC721, IERC721, IPosition) returns (address) {

40:    function isMinter(address _address) public view returns (bool) { return _isMinter[_address]; }

41:    function trades(uint _id) public view returns (Trade memory) {

66:    function openPositions() public view returns (uint256[] memory) { return _openPositions; }

67:    function openPositionsIndexes(uint _id) public view returns (uint256) { return _openPositionsIndexes[_id]; }

68:    function assetOpenPositions(uint _asset) public view returns (uint256[] memory) { return _assetOpenPositions[_asset]; }

69:    function assetOpenPositionsIndexes(uint _asset, uint _id) public view returns (uint256) { return _assetOpenPositionsIndexes[_asset][_id]; }

70:    function limitOrders(uint _asset) public view returns (uint256[] memory) { return _limitOrders[_asset]; }

71:    function limitOrderIndexes(uint _asset, uint _id) public view returns (uint256) { return _limitOrderIndexes[_asset][_id]; }

76:    constructor(string memory _setBaseURI, string memory _name, string memory _symbol) ERC721(_name, _symbol) {

81:    function _baseURI() internal override view returns (string memory) {

85:    function setBaseURI(string memory _newBaseURI) external onlyOwner {

282:    function assetOpenPositionsLength(uint _asset) external view returns (uint256) {

286:    function limitOrdersLength(uint _asset) external view returns (uint256) {

290:    function getCount() external view returns (uint) {

294:    function userTrades(address _user) external view returns (uint[] memory) {

302:    function openPositionsSelection(uint _from, uint _to) external view returns (uint[] memory) {

310:    function setMinter(address _minter, bool _bool) external onlyOwner {

314:    modifier onlyMinter() {

320:    function _msgSender() internal view override(Context, MetaContext) returns (address sender) {

323:    function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {


```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol

```solidity
10:    contract Lock is Ownable{

21:    constructor(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```solidity
189:    modifier onlyProtocol() {

196:    event AssetAdded(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/NFTSale.sol

```solidity
6:    interface IERC721 {

7:    function balanceOf(address) external view returns (uint256);

8:    function safeTransferMany(address, uint[] memory) external;

9:    function claim(address) external;

12:   interface IERC20 {

13:    function balanceOf(address) external view returns (uint256);

14:    function transfer(address, uint) external;

15:    function transferFrom(address, address, uint) external;

18:     contract NFTSale is Ownable {

26:    constructor (IERC721 _nft, IERC20 _token) {

32:    function setPrice(uint _price) external onlyOwner {

36:    function available() external view returns (uint) {

40:    function buy(uint _amount) external {

54:    function recovertoken() external {

58:    function recoverNft() external onlyOwner {

63:    function setIds(uint[] calldata _ids) external onlyOwner {

67:    function claimPendingRev(address _tigAsset) external {
```


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFTBridged.sol

```solidity
12:  contract GovNFTBridged is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {

22:    event MessageFailed(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes _payload, bytes _reason);

23:    event RetryMessageSuccess(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes32 _payloadHash);

24:    event ReceiveNFT(

30:    constructor(

40:    function _baseURI() internal override view returns (string memory) {

44:    function setBaseURI(string calldata _newBaseURI) external onlyOwner {

48:    function _bridgeMint(address to, uint256 tokenId) public {

57:    function _burn(uint256 tokenId) internal override {

67:    function _transfer(

82:    function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {

86:    function crossChain(

128:    function lzReceive(

143:    function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {

149:    function _nonblockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64, bytes memory _payload) internal {

166:    function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {

//incomplete
179:    function estimateFees(

196:    function setGas(uint _gas) external onlyOwner {

200:    function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {

205:    function safeTransferMany(address _to, uint[] calldata _ids) external {

211:    function safeTransferFromMany(address _from, address _to, uint[] calldata _ids) external {

217:    function approveMany(address _to, uint[] calldata _ids) external {

231:    function claim(address _tigAsset) external {

238:    function distribute(address _tigAsset, uint _amount) external {

247:    function pending(address user, address _tigAsset) public view returns (uint256) {

251:    function addAsset(address _asset) external onlyOwner {

258:    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

262:    function setMaxBridge(uint256 _max) external onlyOwner {

266:    function assetsLength() public view returns (uint256) {

270:    function allowedAsset(address _asset) external view returns (bool) {

274:    function balanceIds(address _user) external view returns (uint[] memory) {

283:    function _msgSender() internal view override(Context, MetaContext) returns (address sender) {

286:    function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
```


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```solidity
12:   contract GovNFT is ERC721Enumerable, ILayerZeroReceiver, MetaContext, IGovNFT {

24:    event MessageFailed(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes _payload, bytes _reason);

25:    event RetryMessageSuccess(uint16 _srcChainId, bytes _srcAddress, uint64 _nonce, bytes32 _payloadHash);

26:    event ReceiveNFT(

32:    constructor(

42:    function _baseURI() internal override view returns (string memory) {

46:    function setBaseURI(string calldata _newBaseURI) external onlyOwner {

50:    function _mint(address to, uint256 tokenId) internal override {

//incomplete
76:    function _burn(uint256 tokenId) internal override {

//incomplete
89:    function _transfer(

104:    function mintMany(uint _amount) external onlyOwner {

110:    function mint() external onlyOwner {

114:    function setTrustedAddress(uint16 _chainId, address _contract, bool _bool) external onlyOwner {

168:    function lzReceive(

183:    function nonblockingLzReceive(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {

189:    function _nonblockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64, bytes memory _payload) internal {

206:    function retryMessage(uint16 _srcChainId, bytes calldata _srcAddress, uint64 _nonce, bytes calldata _payload) public {

219:    function estimateFees(

236:    function setGas(uint _gas) external onlyOwner {

240:    function setEndpoint(ILayerZeroEndpoint _endpoint) external onlyOwner {

245:    function safeTransferMany(address _to, uint[] calldata _ids) external {

251:    function safeTransferFromMany(address _from, address _to, uint[] calldata _ids) external {

257:    function approveMany(address _to, uint[] calldata _ids) external {

296:    function pending(address user, address _tigAsset) public view returns (uint256) {

300:    function addAsset(address _asset) external onlyOwner {

307:    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

311:    function setMaxBridge(uint256 _max) external onlyOwner {

315:    function assetsLength() public view returns (uint256) {

319:    function allowedAsset(address _asset) external view returns (bool) {

323:    function balanceIds(address _user) external view returns (uint[] memory) {

332:    function _msgSender() internal view override(Context, MetaContext) returns (address sender) {

335:    function _msgData() internal view override(Context, MetaContext) returns (bytes calldata) {
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Forwarder.sol

```solidity
6:  contract Forwarder is MinimalForwarder {}
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Faucet.sol

```solidity
4:    interface IERC20 {

5:    function mintFor(address, uint) external;

13:    constructor(address _usd) {

17:    function faucet() external {
```


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```solidity

8:  contract BondNFT is ERC721Enumerable, Ownable {

12:    struct Bond {

40:    constructor(

278:    function _baseURI() internal override view returns (string memory) {

282:    function safeTransferMany(address _to, uint[] calldata _ids) external {

290:    function safeTransferFromMany(address _from, address _to, uint[] calldata _ids) external {

298:    function approveMany(address _to, uint[] calldata _ids) external {

306:    function _mint(

316:    function _burn(

323:    function _transfer(

339:    function balanceIds(address _user) public view returns (uint[] memory) {

349:    function addAsset(address _asset) external onlyOwner {

357:    function setAllowedAsset(address _asset, bool _bool) external onlyOwner {

362:    function setBaseURI(string calldata _newBaseURI) external onlyOwner {

366:    function setManager(

372:    modifier onlyManager() {

377:    event Distribution(address _tigAsset, uint256 _amount);

378:    event Lock(address _tigAsset, uint256 _amount, uint256 _period, address _owner, uint256 _id);

379:    event ExtendLock(uint256 _period, uint256 _amount, address _owner, uint256 _id);

380:    event Release(address _tigAsset, uint256 _amount, address _owner, uint256 _id);

381:    event ClaimFees(address _tigAsset, uint256 _amount, address _claimer, uint256 _id);

382:    event ClaimDebt(address _tigAsset, uint256 _amount, address _owner);

```

---

### State variable and function names [4]

- Variables should be named according to their specifications
  - private and internal `variables` should preppend with `underline`
  - private and internal `functions` should preppend with `underline`
  - constant state variables should be UPPER_CASE


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MockERC20.sol

```solidity
9:    address private deployer;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MockChainlinkFeed.sol

```solidity
12:    int256 private price;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/BadStableVault.sol

```solidity
30:    mapping(address => uint) private tokenIndex;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

```solidity
17:    mapping(address => bool) private isNode;

22:    IPairsContract private pairsContract;

23:    IReferrals private referrals;

24:    IPosition private position;

// these external & public functions are prefixed with `_`
61:     function _closePosition(
88:     function _limitClose(
122:    function _checkGas() public view {
190:    function _setReferral(

```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```solidity
121:    IPairsContract private pairsContract;

122:    IPosition private position;

123:    IGovNFT private gov;

124:    ITradingExtension private tradingExtension;

// these external functions are prefixed with `_`
24:    function _setReferral(
44:    function _limitClose(
50:    function _checkGas() external view;
51:    function _closePosition(
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableVault.sol

```solidity
30:    mapping(address => uint) private tokenIndex;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

```solidity
9:    bool private isInit;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```solidity
34:    mapping(uint256 => mapping(address => mapping(bool => int256))) private accInterestPerOi;

35:    mapping(uint256 => mapping(address => uint256)) private lastUpdate;

36:    mapping(uint256 => int256) private initId;

37:    mapping(uint256 => mapping(address => uint256)) private longOi;

38:    mapping(uint256 => mapping(address => uint256)) private shortOi;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```solidity
14:    uint256 private maxBaseFundingRate = 1e10;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol

```solidity
12:    uint public constant minPeriod = 7;

13:    uint public constant maxPeriod = 365;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFTBridged.sol

```solidity
226:    mapping(address => uint) private assetsIndex;

227:    mapping(address => mapping(address => uint256)) private userPaid;

228:    mapping(address => mapping(address => uint256)) private userDebt;

229:    mapping(address => uint256) private accRewardsPerNFT;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFT.sol

```solidity
15:    uint256 private counter = 1;

266:    mapping(address => uint) private assetsIndex;

267:    mapping(address => mapping(address => uint256)) private userPaid;

268:    mapping(address => mapping(address => uint256)) private userDebt;

269:    mapping(address => uint256) private accRewardsPerNFT;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```solidity
27:    uint private totalBonds;

33:    mapping(address => uint) private assetsIndex;

34:    mapping(uint256 => mapping(address => uint256)) private bondPaid;

35:    mapping(address => mapping(uint256 => uint256)) private accRewardsPerShare; // tigAsset => epoch => accRewardsPerShare
```

---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/ExcessivelySafeCall.sol

```solidity
// this contract is bellow 0.8.0 and does not implement `SafeMath` 
2:  pragma solidity >=0.7.6;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/TradingLibrary.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/utils/MetaContext.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/WETH.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/NoTransferToken.sol

```solidity
8:  pragma solidity ^0.8.0;

35: pragma solidity ^0.8.0;

113:  pragma solidity ^0.8.0;

198:  pragma solidity ^0.8.0;

228:  pragma solidity ^0.8.0;

586:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MockEndpoint.sol

```solidity
3:  pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MockERC20.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MockChainlinkFeed.sol

```solidity
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/MetaContextTest.sol

```solidity
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/mock/BadStableVault.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ITrading.sol

```solidity
5:  pragma solidity ^0.8.0;
```


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IStableVault.sol

```solidity
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IReferrals.sol

```solidity
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IPairsContract.sol

```solidity
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroUserApplicationConfig.sol

```solidity
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroReceiver.sol

```solidity
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/ILayerZeroEndpoint.sol

```solidity
3:   pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IGovNFT.sol

```solidity
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/interfaces/IBondNFT.sol

```solidity
2:  pragma solidity ^0.8.9;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/TradingExtension.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Trading.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Timelock.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/StableToken.sol

```solidity
2:   pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Referrals.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Position.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/PairsContract.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/NFTSale.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Lock.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/GovNFTBridged.sol

```solidity
2:  pragma solidity ^0.8.0;
```


https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Forwarder.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/Faucet.sol

```solidity
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-12-tigris/blob/main/contracts/BondNFT.sol

```solidity
2:  pragma solidity ^0.8.0;
```