// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/utils/math/SafeMath.sol";

contract MyToken {
    using SafeMath for uint256;

    // Events
    event TokensReleased(address indexed beneficiary, uint256 amount);

    // Token details
    uint256 public constant totalSupply = 10000000;
    uint256 public constant issuanceDate = 1646064000; // Example issuance date (2022-02-28)
    uint256 public constant vestingDuration = 6 * 30 days; // 6 months in seconds

    // Vesting schedule
    mapping(address => uint256) public balances;
    mapping(address => uint256) public vestingStart;
    mapping(address => uint256) public vestedAmount;

    // Modifier to check if tokens are vested
    modifier vested(address beneficiary) {
        require(block.timestamp >= vestingStart[beneficiary], "Vesting has not started yet");
        _;
    }

    // Constructor
    constructor() {
        balances[msg.sender] = totalSupply;
        vestingStart[msg.sender] = issuanceDate;
    }

    // Function to release vested tokens
    function releaseTokens() external vested(msg.sender) {
        uint256 unreleased = releasableAmount(msg.sender);
        require(unreleased > 0, "No tokens to release");

        vestedAmount[msg.sender] = vestedAmount[msg.sender].add(unreleased);
        balances[msg.sender] = balances[msg.sender].sub(unreleased);

        emit TokensReleased(msg.sender, unreleased);
    }

    // Function to calculate releasable amount
    function releasableAmount(address beneficiary) public view returns (uint256) {
        if (block.timestamp < vestingStart[beneficiary]) {
            return 0;
        }
        uint256 timeElapsed = block.timestamp.sub(vestingStart[beneficiary]);
        if (timeElapsed >= vestingDuration) {
            return balances[beneficiary];
        }
        return balances[beneficiary].mul(timeElapsed).div(vestingDuration).sub(vestedAmount[beneficiary]);
    }
}
