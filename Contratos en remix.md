 // SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract MoonbaseFaucet {
    address public owner;
    uint256 public dripAmount = 0.1 ether;
    mapping(address => uint256) public lastDripTime;
    uint256 public cooldown = 1 hours;

    event Dripped(address indexed recipient, uint256 amount);

    modifier onlyOwner() {
        require(msg.sender == owner, "No eres el propietario");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    receive() external payable {}

    function drip() external {
        require(address(this).balance >= dripAmount, "Faucet sin fondos");
        require(block.timestamp >= lastDripTime[msg.sender] + cooldown, "Espera antes de volver");

        lastDripTime[msg.sender] = block.timestamp;
        payable(msg.sender).transfer(dripAmount);

        emit Dripped(msg.sender, dripAmount);
    }

    function changeDripAmount(uint256 _amount) external onlyOwner {
        dripAmount = _amount;
    }

    function changeCooldown(uint256 _cooldown) external onlyOwner {
        cooldown = _cooldown;
    }

    function withdraw() external o
