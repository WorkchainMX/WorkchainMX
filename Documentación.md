 PROBLEMÁTICA 

¿Sabías que aproximadamente el 23.5% de los adultos en México no tienen acceso a productos financieros formales? Esto afecta principalmente a trabajadores, agricultores y personas sin historial crediticio, quienes enfrentan muchas dificultades para obtener servicios básicos como préstamos o créditos. La falta de documentación oficial y antecedentes financieros limita sus oportunidades para crecer y mejorar su calidad de vida. 

SOLUCIÓN 

WorkChainMX es una plataforma digital pensada para apoyar a trabajadores, agricultores y personas sin historial crediticio que no pueden acceder a servicios financieros tradicionales. La plataforma permite crear un perfil digital basado en el historial de transacciones y pagos dentro del sistema, funcionando como una referencia confiable de reputación financiera. 

LOS USUARIOS PUEDEN: 

Mostrar que trabajan y cumplen con sus compromisos de pago. 

Solicitar préstamos en criptomonedas directamente a otras personas, sin necesidad de intermediarios financieros tradicionales. 

Pagar esos préstamos poco a poco, con reglas claras que protegen a ambas partes. 

IMPORTANCIA 

Muchas personas que trabajan en el sector informal o viven en comunidades rurales y urbanas necesitan financiamiento para comprar insumos básicos como semillas, herramientas o transporte. Pero al no tener historial crediticio ni documentos oficiales, no pueden acceder a servicios financieros formales. 

Esto los obliga a recurrir a prestamistas informales que cobran intereses muy altos y ofrecen condiciones poco confiables, lo que dificulta su progreso económico. WorkChainMX quiere cambiar esta situación, ofreciendo un sistema justo, seguro y transparente para pedir préstamos, y ayudando a construir un historial crediticio digital que facilite el acceso a más apoyo financiero en el futuro. 

FUNCIONAMIENTO 

Registro: Los usuarios entran a la plataforma usando aplicaciones para manejar activos digitales como MetaMask, WalletConnect, Coinbase Wallet o Trust Wallet. 

Historial de cumplimiento: Cada vez que alguien paga un préstamo o cumple con un acuerdo, su historial mejora, mostrando que es responsable y digno de confianza. 

Préstamos: Los usuarios pueden pedir préstamos pequeños en criptomonedas (como Astar y Tereum) a otras personas que quieran apoyarlos, sin necesidad de intermediarios. 

Reglas claras: La plataforma establece reglas para los pagos y se asegura de que se respeten, evitando abusos o fraudes. Esta parte está en constante mejora para que funcione cada vez mejor. 

TECNOLOGÍAS UTILIZADAS 

En WorkChainMX usamos Polkadot para crear una plataforma segura, escalable y compatible con las herramientas más usadas por los desarrolladores. 

Un componente clave es Moonbase Alpha, una red de prueba (testnet) que permite a los desarrolladores construir y probar aplicaciones descentralizadas (dApps) , que es una parachain de Polkadot compatible con la Máquina Virtual de Ethereum (EVM). Moonbase Alpha simula el entorno real, ofreciendo un espacio seguro para probar antes de lanzar en la red principal. 

Componentes principales y sus funciones: 

Componente	                                  Función
EVM (Ethereum Virtual Machine)	      Ejecuta contratos inteligentes escritos en Solidity, igual que en Ethereum.
Substrate	                           Framework de Polkadot que se usa para construir Moonbeam y Moonbase Alpha.
Polkadot Relay Chain	                Proporciona seguridad y consenso a las parachains como Moonbeam.
Parachain	                           Moonbeam es una parachain conectada a Polkadot; Moonbase Alpha simula esta conexión en un entorno de prueba.


Permitiendo construir una plataforma descentralizada en la validación y registro de transacciones, para ofrecer transparencia, seguridad y facilidad de uso. 

BENEFICIOS 

♣Plataforma sencilla e intuitiva, diseñada para que cualquier persona pueda usarla sin complicaciones. 

♣Permite crear una identidad digital que genera confianza dentro del sistema. 

♣Registro seguro a través de aplicaciones para gestionar activos digitales, sin necesidad de datos personales tradicionales. 

♣Abre la puerta a la inclusión financiera para quienes no tienen historial crediticio. 

♣Fomenta préstamos justos que impulsan el desarrollo económico local. 

♣Ayuda a construir un historial crediticio digital sólido y confiable. 

♣Reduce la dependencia de prestamistas informales con condiciones desfavorables. 

♣Compatible con diversas aplicaciones para manejar activos digitales, facilitando el acceso. 

♣Ofrece transparencia y protección gracias a la tecnología blockchain de Polkadot. 

♣Promueve la confianza entre usuarios mediante un sistema claro para evaluar el comportamiento. 

♣Se apoya en la seguridad robusta que proporciona el consenso de la Relay Chain de Polkadot. 

♣Facilita la interoperabilidad con otras parachains y redes compatibles. 

♣Compatible con contratos inteligentes de Ethereum, lo que simplifica el desarrollo y la adopción. 

♣Cuenta con entornos de prueba avanzados que garantizan estabilidad y confiabilidad en las aplicaciones. 

PROGRAMAS Y LENGUAJES USADOS 

Solidity: Para crear contratos inteligentes en blockchains compatibles. 

Hardhat: Herramienta para desarrollar, probar y desplegar contratos inteligentes. 

JavaScript (JS): Para interfaces web interactivas. 

JSON: Para intercambiar información entre sistemas. 

HTML: Para la estructura de las páginas web. 

React: Para construir aplicaciones web modernas y dinámicas. 

¿QUÉ ES UNA BLOCKCHAIN? 

Blockchain, o cadena de bloques, es una tecnología que funciona como un libro digital compartido y seguro donde se registran transacciones o información en bloques conectados entre sí. Cada bloque está vinculado al anterior mediante un código criptográfico, formando una cadena que no se puede modificar. 

Esta base de datos está distribuida entre muchas computadoras (nodos) que mantienen copias idénticas y sincronizadas, haciendo que la información sea casi imposible de falsificar o hackear, y garantizando transparencia y confianza. 

Polkadot, la tecnología que usamos en WorkChainMX, permite que diferentes blockchains se conecten e interactúen, facilitando la creación de aplicaciones financieras completas, seguras y escalables. 

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
