 // SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract NativeLoanWithPenalty {
    address public lender;
    address public borrower;

    uint256 public loanAmount;
    uint256 public interestAmount;
    uint256 public totalDue;
    uint256 public amountRepaid;
    uint256 public dueDate;
    uint256 public penaltyPercent = 10; // Penalización por atraso (%)

    bool public loanTaken;
    bool public penaltyApplied;

    enum LoanState { Funded, Taken, Repaid, Defaulted }
    LoanState public state;

    event Funded(address indexed lender, uint256 amount);
    event LoanTaken(address indexed borrower);
    event PartialRepayment(address indexed borrower, uint256 amount);
    event FullyRepaid(address indexed borrower);
    event PenaltyApplied(uint256 penalty);
    event Withdrawn(address indexed lender, uint256 amount);

    modifier onlyLender() {
        require(msg.sender == lender, "No eres el prestamista");
        _;
    }

    modifier onlyBorrower() {
        require(msg.sender == borrower, "No eres el prestatario");
        _;
    }

    constructor(uint256 _interestAmount, uint256 _durationSeconds) payable {
        require(msg.value > 0);

        lender = msg.sender;
        loanAmount = msg.value;
        interestAmount = _interestAmount;
        totalDue = loanAmount + interestAmount;
        dueDate = block.timestamp + _durationSeconds;

        state = LoanState.Funded;
        emit Funded(lender, loanAmount);
    }

    function takeLoan() external {
        require(state == LoanState.Funded);
        require(!loanTaken);

        borrower = msg.sender;
        loanTaken = true;
        state = LoanState.Taken;

        (bool success, ) = payable(borrower).call{value: loanAmount}("");
        require(success);

        emit LoanTaken(borrower);
    }

    function repay() external payable onlyBorrower {
        require(state == LoanState.Taken || state == LoanState.Defaulted, "No puedes pagar ahora");
        require(msg.value > 0, "Debes enviar DEV");

        amountRepaid += msg.value;
        emit PartialRepayment(msg.sender, msg.value);

        if (amountRepaid >= totalDue) {
            state = LoanState.Repaid;
            emit FullyRepaid(msg.sender);
        }
    }

    function applyPenalty() external onlyLender {
        require(state == LoanState.Taken);
        require(block.timestamp > dueDate);
        require(!penaltyApplied);

        uint256 penalty = (loanAmount * penaltyPercent) / 100;
        totalDue += penalty;
        penaltyApplied = true;
        state = LoanState.Defaulted;

        emit PenaltyApplied(penalty);
    }

    function withdraw() external onlyLender {
        require(state == LoanState.Repaid || state == LoanState.Defaulted);
        uint256 amount = address(this).balance;
        require(amount > 0, "Nada para retirar");

        (bool success, ) = payable(lender).call{value: amount}("");
        require(success, "Fallo el retiro");

        emit Withdrawn(lender, amount);
    }

    function getOutstanding() external view returns (uint256) {
        if (amountRepaid >= totalDue) {
            return 0;
        }
        return totalDue - amountRepaid;
    }

    receive() external payable {}
}
