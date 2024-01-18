Chainlink Bootcamp por Solange Gueiros Jan/2024

Aula 04
https://pad.riseup.net/p/cl-pt-04

Slides
https://docs.google.com/presentation/d/e/2PACX-1vQDouGb19sN3qGO2zDU5VZ-JXhgnJoqVCvykLT6tbOOqX7zXqwqrJxPciBohc66nsoGs_L_UB9QZUQs/pub?start=false&loop=false&delayms=3000

Tarefas
https://forms.gle/k36cuefTTwchdPHw7

Todas as aulas estao aqui
https://lu.ma/ig70bk68

Faucet
https://workshop-faucet.vercel.app/faucets/fuji
     

Exercicios do Bootcamp
https://github.com/solangegueiros/chainlink-bootcamp-2024

https://chain.link/community/advocates
https://chain.link/developers/experts


Portal do dev
https://dev.chain.link/

Boa noite Pessoal!

Nome - Cidade, Pais


Christie Cardoso - BH

***************************************************************************************

https://dev.chain.link/products/ccip

https://docs.chain.link/ccip/tutorials/cross-chain-tokens

Blockchain de Origem
Avalanche Fuji

Blockchain de Destino
Ethereum Sepolia

Adicionar Fuji no Metamask
https://chainlist.org/chain/43113

Conectar Avalanche na carteira
Avalanche Fuji Testnet - Add to Metamask

Pegar Faucet
Avax Fuji e LINK na Fuji
https://workshop-faucet.vercel.app/faucets/fuji
https://faucets.chain.link/

Add LINK on Fuji no Metamask
https://docs.chain.link/resources/link-token-contracts#fuji-testnet

CCIP-BnM
https://docs.chain.link/ccip/test-tokens#mint-tokens-in-the-documentation
Add CCIP-BnM to wallet
Mint 1 CCIP-BnM Token


https://remix.ethereum.org/

Icon 5 - DEPLOY & RUN TRANSACTIONS
ENVIRONMENT
Injected provider - Metamask
Verifica se a rede é Custom (43113) network = Fuji

Crie um arquivo:
CCIPTokenSenderFujiSepolia.sol 


// Inicio


// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

import {IRouterClient} from "@chainlink/contracts-ccip/src/v0.8/ccip/interfaces/IRouterClient.sol";
import {Client} from "@chainlink/contracts-ccip/src/v0.8/ccip/libraries/Client.sol";
import {IERC20} from "@chainlink/contracts-ccip/src/v0.8/vendor/openzeppelin-solidity/v4.8.0/token/ERC20/IERC20.sol";
import {LinkTokenInterface} from "@chainlink/contracts/src/v0.8/interfaces/LinkTokenInterface.sol";

contract CCIPTokenSenderFujiSepolia {
    IRouterClient router;
    LinkTokenInterface linkToken;
    address public owner;

    // https://docs.chain.link/resources/link-token-contracts#fuji-testnet
    address link= 0x0b9d5D9136855f6FEc3c0993feE6E9CE8a297846;

    // https://docs.chain.link/ccip/supported-networks/v1_2_0/testnet#avalanche-fuji
    address routerAddress = 0xF694E193200268f9a4868e4Aa017A0118C9a8177;
    address bnmToken = 0xD21341536c5cF5EB1bcb58f6723cE26e8D8E90e4;
    
    // https://docs.chain.link/ccip/supported-networks/v1_2_0/testnet#ethereum-sepolia
    uint64 destinationChainSelector = 16015286601757825753;
   
    error NotEnoughBalance(uint256 currentBalance, uint256 calculatedFees);
    error NothingToWithdraw();
   
    event TokensTransferred(
        bytes32 indexed messageId, // The unique ID of the message.
        uint64 indexed destinationChainSelector, // The chain selector of the destination chain.
        address receiver, // The address of the receiver on the destination chain.
        address token, // The token address that was transferred.
        uint256 tokenAmount, // The token amount that was transferred.
        address feeToken, // the token address used to pay CCIP fees.
        uint256 fees // The fees paid for sending the message.
    );

    constructor() {
        owner = msg.sender;
        router = IRouterClient(routerAddress);
        linkToken = LinkTokenInterface(link);
        linkToken.approve(routerAddress, type(uint256).max);
    }
   
    function transferToSepolia(
        address _receiver,
        uint256 _amount
    )
        external
        returns (bytes32 messageId)
    {
        Client.EVMTokenAmount[]
            memory tokenAmounts = new Client.EVMTokenAmount[](1);
        Client.EVMTokenAmount memory tokenAmount = Client.EVMTokenAmount({
            token: bnmToken,
            amount: _amount
        });
        tokenAmounts[0] = tokenAmount;
       
        // Build the CCIP Message
        Client.EVM2AnyMessage memory message = Client.EVM2AnyMessage({
            receiver: abi.encode(_receiver),
            data: "",
            tokenAmounts: tokenAmounts,
            extraArgs: Client._argsToBytes(
                Client.EVMExtraArgsV1({gasLimit: 0})
            ),
            feeToken: address(linkToken)
        });
       
        // CCIP Fees Management
        uint256 fees = router.getFee(destinationChainSelector, message);

        if (fees > linkToken.balanceOf(address(this)))
            revert NotEnoughBalance(linkToken.balanceOf(address(this)), fees);

        linkToken.approve(address(router), fees);
       
        // Approve Router to spend CCIP-BnM tokens we send
        IERC20(bnmToken).approve(address(router), _amount);
       
        // Send CCIP Message
        messageId = router.ccipSend(destinationChainSelector, message);
       
        emit TokensTransferred(
            messageId,
            destinationChainSelector,
            _receiver,
            bnmToken,
            _amount,
            link,
            fees
        );  
    }
    
    function balancesOf(address account) public view returns (uint256 linkBalance, uint256 bnmTokenBalance) {
        linkBalance =  linkToken.balanceOf(account);
        bnmTokenBalance = IERC20(bnmToken).balanceOf(account);
    }

    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }

    function withdrawToken(
        address _beneficiary,
        address _token
    ) public onlyOwner {
        uint256 amount = IERC20(_token).balanceOf(address(this));
       
        if (amount == 0) revert NothingToWithdraw();
       
        IERC20(_token).transfer(_beneficiary, amount);
    }
}

// Fim



https://docs.chain.link/ccip/tutorials/cross-chain-tokens

https://docs.chain.link/resources/link-token-contracts#fuji-testnet

https://docs.chain.link/ccip/supported-networks/v1_2_0/testnet#avalanche-fuji

Tenha CERTEZA que voce está na Avax Fuji no Metamask ((4313) network)

Deploy

Nome - CCIPTokenSenderFujiSepolia Contract Address

Christie Cardoso - 0x1786BAf729b438C18842A6bE945d80A04ff59b56


*************************************************************

Avax Fuji Block explorer
https://testnet.snowtrace.io/

Fund the CCIPTokenSenderFujiSepolia contract
No Metamask
Envie para o endereco do seu CCIPTokenSenderFujiSepolia
1 LINK
0.1 CCIP BnM token

No smart contract
balancesOf
Endereco do seu smart contract

Enviar 0.01 BnM da Fuji para Sepolia
https://eth-converter.com/
0.01 BnM = 10000000000000000 (wei)

transferToSepolia
_receiver
endereco da sua carteira (do Metamask)

_amount
0x5d2FDfF134636515Cb4CDdce3A174b16f4ec80780x5d2FDfF134636515Cb4CDdce3A174b16f4ec80780x5d2FDfF134636515Cb4CDdce3A174b16f4ec8078


https://ccip.chain.link/


*********************************************

No Metamask
Selecionar Sepolia

Adicionar CCIP-BnM (Sepolia) no Metamask
https://docs.chain.link/ccip/test-tokens#mint-tokens-in-the-documentation
