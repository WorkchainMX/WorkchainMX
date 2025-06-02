contratos:

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

prestamo:

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

 para la imagen: 
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ImageUploader {
    address public owner;
    uint256 public uploadFee; // en wei
    uint256 public totalImages;

    struct Image {
        address uploader;
        string imageURI;
        uint256 timestamp;
    }

    mapping(uint256 => Image) public images;

    event ImageUploaded(address indexed uploader, uint256 indexed imageId, string imageURI);
    event FeeUpdated(uint256 newFee);
    event FundsWithdrawn(uint256 amount);

    modifier onlyOwner() {
        require(msg.sender == owner, "Solo el owner puede hacer esto");
        _;
    }

    constructor(uint256 _uploadFee) {
        owner = msg.sender;
        uploadFee = _uploadFee; // Por ejemplo: 0.01 DEV = 10**16
    }

    function uploadImage(string calldata _imageURI) external payable {
        require(msg.value >= uploadFee, "Pago insuficiente para subir imagen");

        images[totalImages] = Image({
            uploader: msg.sender,
            imageURI: _imageURI,
            timestamp: block.timestamp
        });

        emit ImageUploaded(msg.sender, totalImages, _imageURI);
        totalImages++;
    }

    function updateFee(uint256 _newFee) external onlyOwner {
        uploadFee = _newFee;
        emit FeeUpdated(_newFee);
    }

    function withdrawFunds() external onlyOwner {
        uint256 balance = address(this).balance;
        require(balance > 0, "No hay fondos para retirar");
        payable(owner).transfer(balance);
        emit FundsWithdrawn(balance);
    }

    function getImage(uint256 imageId) public view returns (Image memory) {
        return images[imageId];
    }

    function contractBalance() public view returns (uint256) {
        return address(this).balance;
    }
}
