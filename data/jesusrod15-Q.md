1-)Tranding.sol shloud be marked as abstract




2-)

TimelockController.sol  expected  3 parameters in constructor  but  Timelock provide 4 

contract Timelock is TimelockController {
    constructor(address[] memory _proposers, address[] memory _executors, uint256 _time) TimelockController(_time, _proposers, _executors, address(0)) {}
}



