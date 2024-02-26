pragma solidity ^0.8.0;

contract AptosBalanceChecker { mapping(address => uint256) public balances; address[] public wealthyAccounts; address[] public poorAccounts;
