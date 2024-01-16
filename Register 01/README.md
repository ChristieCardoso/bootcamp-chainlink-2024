Chainlink Bootcamp by Solange Gueiros - Jan/2024
Aula 02

https://www.youtube.com/@chainlink

Aula 01
https://www.youtube.com/watch?v=mH2hkWG6OS8


Tarefas - formulario
https://forms.gle/k36cuefTTwchdPHw7

Slides
https://docs.google.com/presentation/d/e/2PACX-1vQDouGb19sN3qGO2zDU5VZ-JXhgnJoqVCvykLT6tbOOqX7zXqwqrJxPciBohc66nsoGs_L_UB9QZUQs/pub?start=false&loop=false&delayms=3000


NOME - Cidade, Pais


Christie Cardoso - BH - BR


******************************************

https://remix.ethereum.org/

Icone 4 - SOLIDITY COMPILER
Habilitar Auto compile

Icone 5 - DEPLOY & RUN TRANSACTIONS

ENVIRONMENT
Injected provider - Metamask
Verifique se a rede é 
Sepolia (11155111) network


https://sepolia-faucet.pk910.de/
https://sepoliafaucet.com/
https://faucets.chain.link/

Icone 2 - FILE EXPLORER

Criar o arquivo 
Register.sol

Copiar e colar o que estiver entre o inicio e fim

// Inicio
// SPDX-License-Identifier: MIT 
pragma solidity 0.8.21; 

contract Register { 
    string private info; 
    
    function getInfo() public view returns (string memory) { 
        return info;
    } 

    function setInfo(string memory _info) public { 
        info = _info; 
    } 
} 

// Fim

Para pegar ETH na Sepolia
https://sepolia.etherscan.io/address/0xA982C62E4c67aBEd0B4bC6f1d514DB5a48A8D372#writeContract

Icone 4 - SOLIDITY COMPILER
Sinal verde
Compilation successful

duarte - 0xDB63480197B0167E3414bDA58Adf5cFb26eAcb03

Icone 5 - DEPLOY & RUN TRANSACTIONS
Deploy


Deployed Contracts

Copiar o endereco do Register

Nome - Register address 

Christie Cardoso - 0x7ea6B5cc2aA063ef0079423CF7aD8e32Eca2A728

**********************************************


getInfo
vazio

SetInfo
a informacao que voce quiser

getInfo
Veja a informacao

https://sepolia.etherscan.io/
Procura o endereco do seu smart contract

Verificar o codigo fonte do contrato no Etherscan
No endereco do seu Register
Tab Contract

((OBS : Nao estou acompanhando ao vivo por conta do horario aqui na Grecia
Duvida !
Enter the Solidity Contract Code below *
Onde encontro o Solidity Contract Code ?
Como preencher toda a verificacao ?
ARMAROLI  grato))

2 icone - FILE EXPLORER
Criar
RegisterAccess.sol

// Inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.21;
 
contract RegisterAccess {
    string[] private info;
    address public owner;
    mapping (address => bool) public allowlist; 
 
    constructor() {
        owner = msg.sender;
        allowlist[msg.sender] = true;
    } 
 
    event InfoChange(string oldInfo, string newInfo); 
 
    modifier onlyOwner {
        require(msg.sender == owner,"Only owner");
        _;
    }
 
    modifier onlyAllowlist {
        require(allowlist[msg.sender] == true, "Only allowlist");
        _;
    } 
 
    function getInfo(uint index) public view returns (string memory) {
        return info[index];
    }
 
    function setInfo(uint index, string memory _info) public onlyAllowlist {
        emit InfoChange (info[index], _info);
        info[index] = _info;
    }
 
    function addInfo(string memory _info) public onlyAllowlist returns (uint index) {
        info.push (_info);
        index = info.length -1;
    }
   
    function listInfo() public view returns (string[] memory) {
        return info;
    }
 
    function addMember (address _member) public onlyOwner {        
        allowlist[_member] = true;
    }
 
    function delMember (address _member) public onlyOwner {
        allowlist[_member] = false;
    }    
}


// Fim

Icone 5 - DEPLOY & RUN TRANSACTIONS
Deploy

Deployed Contracts

Copiar o endereco do RegisterAccess

CHristie Cardoso - 0x295e299BF82361ACfbe6E672eFE67721abfb5Ec5

Para eu alterar o SEU contrato
Sol Address - 0xf213A0f44B0f1FD55Bbb5C6080541b47E49303fE


Nome - Seu endereco 

Christie Cardoso - 0x063A07958dFaDc2c140A6Ec2f4380B280254aeE3

**********************************

https://github.com/solangegueiros/register-learn-solidity


