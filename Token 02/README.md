Chainlink Bootcamp por Solange Gueiros


Aula 03
https://pad.riseup.net/p/cl-pt-03

https://www.youtube.com/watch?v=fi1Hkc3sS5U


Slides
https://docs.google.com/presentation/d/e/2PACX-1vQDouGb19sN3qGO2zDU5VZ-JXhgnJoqVCvykLT6tbOOqX7zXqwqrJxPciBohc66nsoGs_L_UB9QZUQs/pub?start=false&loop=false&delayms=3000

Tarefas
https://forms.gle/k36cuefTTwchdPHw7

https://workshop-faucet.vercel.app/faucets
cllworkshop0101

tem essa também -> https://sepoliafaucet.com/

****************************

Nome - Cidade, Pais


Christie Cardoso - BH 

****************************************

https://data.chain.link/

https://docs.chain.link/

https://docs.chain.link/data-feeds

https://docs.chain.link/data-feeds/price-feeds/addresses?network=ethereum&page=1#sepolia-testnet


Token ERC20
https://ethereum.org/en/developers/docs/standards/tokens/

https://eips.ethereum.org/EIPS/eip-20

https://wizard.openzeppelin.com/

https://remix.ethereum.org/

Icon 5 - DEPLOY & RUN TRANSACTIONS
ENVIRONMENT
Injected provider - Metamask

Check the Sepolia (11155111) network

Criar
// Inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.21;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/AccessControl.sol";

contract Token is ERC20, AccessControl {
    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

    constructor() ERC20("Chainlink Bootcamp 2024 Token PT", "CBT24PT") {
        _grantRole(DEFAULT_ADMIN_ROLE, msg.sender);
        _grantRole(MINTER_ROLE, msg.sender);
    }

    function mint(address to, uint256 amount) public onlyRole(MINTER_ROLE) {
        _mint(to, amount);
    }

    function decimals() public pure override returns (uint8) {
        return 2;
    }    
}


// Fim

Altera o nome e simbolo do token na linha 10

Deploy

Nome - Token Address

Felipe Bolzan - 0xE58bb9C7b3A4de54951D05c9ee9830d881bc4204
Andre Melo - 0xdf3530805598C1021c090CD63369b03DDDa31E3c
alison de amorim - 0xc959F6d3DEa3e0CC6CEffd7e810Df2035b503993
Stefano Filippone - 0x0038eC46D7292190bc7fBcD453B6b623f8948694
Cairo Vinícius - 0xD43Cf59705f3a55A96d12ba111Ced9b3d82474e5
Antonio Miguel Mendes - 0xbE422B27657f972276902b79A8D0EaBc444394Be
Renan Pereira - 0x127fDF070f9835ae6F6C5CaBa130304a353e498d
Manoel Silva - 0xBc42052CCB6E7A6961A412Fd00C4bCADDa0ae10a
Aguinaldo Bernardes - 0xB1B9443Eb802918AB785cD8bCA76F41F985D81CF
Anderson Dantas - 0x75c04BBC97a39bc3B26179c665c4B751cB41Ae2f
Edson Junior - 0xB997Ef72dE5aa4b3745aaa00EB8995ccafb3031e
Tiago Moreno - 0x3A086A90573Ba223F476dAe9F994b75723C2F76
Bruna Sebold - 0x9bE8E6C4D5984351Bf1fAcf09468782b8337C478
Duarte - 0xDB63480197B0167E3414bDA58Adf5cFb26eAcb03
Thais Gouvea - 0xa9BCB9Cd826dCef6518B77f20e5ba41C1a01F131
Éder Werneck - 0xf64D414D0cBA967721647E563008e368Da7C332A
Rodrigo Galvan - 0xAcADF5c3E2168E204d18A13F09b05c5B1459BC8c
Márcia Silva - 0xC13b9465Dc7D676FaF4E9F479BeA7c93bE7186f8
Ronaldo Samadello - 0x88aA489F0C1d8D9B06E1e2133600158399a37575
Anton - 0x8859aD21C884e0209DFCB49967d3BA1Ffc3357a9
Alain Marques - 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4
Pedro Freitas Rodrigues - 0x5F5fc3D25A7b22B51FF1B46939fB007C34154cC2
Anny Cintia  - 0x56fFFEeB11f4F60aA461adE75bAeE2d5C44dE08C
Breno Cardoso - 0xa2EFC5838267638d20f2De9fF9Df80661e80f972
Luis Isidro -0x7F06e65b9E4Ad53beb33eAB385Ed1cbaF20CA9B0
Felipe Macedo - 0x33a68fA71EAEe19c5438Ff7201d2CaBc1e7AF378
Joao Batista - 0xcD833375f9b274f658E9B2CB932998Ad248C5B2f
Pedro Rodrigues - 0x4f7179f5C79e7f23d1516d75686aDCF77b0656c4
Pedro isaac - 0x7b96aF9Bd211cBf6BA5b0dd53aa61Dc5806b6AcE
Leonardo - 0x2B2ff616ACb367fEf82962a357D0a7AD35f88FfC
Marina Luciana  -0xbC1fE7897cb3c4cdb102e1639D6340dEC2Ada83e
Talles Eduardo - 0x8537aFEDE5A790B692d6879272223A0eAa80c862
Romulo Fernandes - 0x357D6513603e78a359B9b25bF135BFa8BBE2A608
Giaele Costa - 0x5ED12c83dB1b60702D012F62EF64c1BE74bA54e2
Ricardo Hildebrand - 0x3a31B067FB593d03a8bB7E28c02DA1dE6Fe230f1
Tiago Chaves de Oliveira - 0x1ebd7a839777d32b6bf486ceed5ca247bad12165
Renan Castro - 0x5379c7a822cF25216fbb3D8C8E6b6422F9Af7869
Gabriel de Carvalho - 0x67d1759B59E4C7c0403f00aebF4105F35Cf6895B
Paulo Lazzari - 0xE7898c4bE004Bb2ec9999924Fc33b338A0624D89
Pedro Almeida - 0x604aA3AdEd64A5751ab9B198306bd6fa5B67e527
gustavo lopes - 0x24eEa303a40313C07e211e232D538A4d4241C2Ce
Joao - 0x160F3B96668A28473C8aC51B14e785C6f2eB3Fa5
Renato Cadecaro - 0xFF8590E9699205f5733feEa535BfB7Add0cbbc30
Tiago Chaves de Oliveira - 0x1ebd7a839777d32b6bf486ceed5ca247bad12165
Sol - 0xA6b703F47bA59F68F612239A63153179aa0a4cb8
Rodrigo Zancanella 0x11bf29F7bB18e2820BABc751333976B77d2Cc29f
Samuel Paixão - 0x6b8053c648915445Eb5f94D5530Fda57CF326433
Jefferson Ferreira - 0x292646f552C12704cF5864b6331993Eb30a9aB1C
Dicas de Cripto - 0x234EdAFe747a1C496FB9a452DB956Ca86D14B16F
Manuela Borges - 0x0913712E1de5E02DB2A296b5B32C9763ECe35064
Abel Ramos - 0x384da3d2d6c4cd09eB449a9A52BB8D8dD84f1745
Marcel Brunetto - 0x0f404166010C0A639C92Ba56B39D50Eb89245DF8
Pablo Souza Grigoletti - 0x9549CbFf51fB64DB3C2Be8dEB7b982FB513ab564
Mauricio Schiesari - 0x2AF87C7e0df1DFFd5871C06DF6Ee62A2341924e6
Diogo Silva - 0x6F6200Aa859223e80eAA919764ECdFbc2A772E1F
Matheus - 0x4E11ee2Aeef90472ff3D60BC1a27B567870291FD
Rafael Leite dos Santos Duarte - 0x54936f859B3Ff30f09Ea94b59260dBBc80C995F3
Marcos - 0xCffC8528a94Ac124aF38aFE69498b6d9b8915A35
Ricardo Gorki - 0xaC5325EF027B1B586c34c0c5dd03F052383351C4
Rodrigo Vrindavan - 0x82DBb0E54c1c6D582C23BFa31fdEe4B744522BF8 (address do token)
Kevin - 0xB496aDa7A2110E3D912973F924aF796df7a9476C
Victor Mendes - 0x34D6646849282A1Df20B6e1E329f200EbBf4c030
Cristina Chacón - 0xB39636eB8e74c19554E3f26366075732715bE13F
Lisa Dafny - 0xFcA70Ab638ab88bF610766297A9c2012b3927bB8
Renato Ferrari - 0xEf14d8b929A48d466c4436Fb0F817f8501f4559C
Alessandra Cruz - 0xb869CA6C9Ae3B79fce4b28A604f1E212312D1C21
Vinícius de França - 0x253F300e18dc8f84f561457111dcAa06A625759F
Guilherme Gonçalves - Token - 0x764356f910653f4976E2c3Bc993d7069a809709B
Alexandre Gomes - 0x0d33d1051d00474d937b37d0995926cbfd45296e
André Luiz Rodrigues - 0xC1d1cAaAd8c9601A2d8f78dF474AD826B48A143e
Antonio JMS Token - 0x3969569A412631c2733Ff9Cc81f0E4Ed4123D77e
Otavio - 0x4E4D1534410F7074b6B3E2eEB289CAca32A73CB9
Boby - 0x310a639a9a2463f8D72590992eFB30cB78c24B96
Gabriel Pekim - 0x61Cd45Bb7a92D40470B67d606F5314383f61585E
Gleiton (Verix)- 0x5cb5431972981a42e3d0589610c5d53671eb1c4c08cc20e8615826f7255c3d44
Christina Bianchi 0xB00007Aa2eBdd8Dc27B487358426eE4438695ada
Joao Lola - 0x4Dd98375244CE5977AC1C336d9cAD39082150682
Geisy - 0x170549a9C54393c5676CEB2954860A6C329786e0
Sandro 0x5977A86a5A863078Af3AfEB50679DCb7c0b6B1E0
Jean Paul Drlik - 0x3AaC31E7582C1Bd3Ed2FcF993aD3923D40a6bE9d 
Paullo Anaya _ 0xEd0f7D5a109324dB3F16bE9D64aF1Ffb6bAbaC6F
Felipe Moura - 0x3Fd2F4707CE02e9021c69d44a434E7A26C98C8CC
Marco Gomes - 0x9286C4BCB9DEA50B4ae16F18442b757be2b116D1
Maria Eduarda - 0xfAF8c475968472535E0D4FB19b867c931A604093
Leomar 0x5c4277743A3C3a4240e545c4d809a6Ca3021db92
Felipe Gonçalves - 0x0154b4144cC34bBD843d3B1b2aB250168Cc09D83
Ricardo Gomes - 0x776761764189994A5c39e5e214E2EE7AFe20fc24
Barba - 0xBdec8c1E986b62e73e234543628524B39A0E48F4
Ênio Vinicius - 0xBfedb298C351217b725f57F27120671dB8289441
Rodrigo Gabriel - 0x81D3536Ba5Bf1376E20e69CA08F0A334C6068146
Guilherme Castro - 0x3cB418e077BE60BF09270C89a9c1b5F1c6DbB9C9
Antonio Gioia - 0x4E25e15F88F8293fA42a5f2c5702d7a8092449D2
Cleiton Fraga - 0x3BB7F22aF3BFEf340Bf6d0d1b7398aB9F894f513
Johnson Chen - 0x60EEF730D815D277eE40Cbbd032E412398cea672
Faury - 0xE3Afaa2433AB14134519b15523e1127F46F0BC24
Wellington 0x537b37042901D1B85440392cFA03e2B046d833D3
RDGSANGFRIO 0x59D72aBf391aF568BDaD934916b65F27F2d8b0f2
Belchior Souza 0x90C3155F3FEFa45AaED8E841b3914C942aa98dD7
Enzo-0xd8b934580fcE35a11B58C6D73aDeE468a2833fa8
Daniel Madureira - 0x8AaEC9b3Db16ab9Ce046B98fe6619A93d2304EF7
Edmar Borba - 0x7A55915d01aAc95920FD888bd80D9Cc131e6f4DB
Ramires Cardoso - 0xD09A5Cdd57fa3A729313FF670ba3Ae37FBe13305
Marcelo Santana - 0x225721A702303CB64140dD2276534FD3305dc53D
Cláudio M. Piska 0x6ecc6ded8429eea3bddc06c455f6fb71c55ea863
Rafael Said - 0x0EaacD8825a7eb3f5b2d41D7b857c4B12E81FFda
Rodiney - 0x48E619Ffa5671701b6b20c39e20473E122604D82
Gabriel Ferreira - 0xdf47c83c1FB0c901189404Be1713347205f5e4fB
Alexandre Nicolini - 0xcb25718b782B2A1578668966D342E3357cA2C1b9
Erik Alonso - 0xE3B1CA3bdeFC4C8021026eF86a45632f825d47F9
David Marono - 0x5d30f6890f698Cecba9e996F29Fe3e67c3205c8F
Márcio Santana - 0xEA253898a4FC10593A47e170a469809a7A89825E
Eric Bezerra - 0xdF8254852E8dF87343aE447891Fe646939Ee184A
André 0xc72a0AC23de933eBAC3a242297AA0DBeC45E50B0
João Pedro Moreira de Carvalho Siqueira - 0x8d5DEC376F26edd0e1c1AEe308A926A617aFBB79
Vinícius Felipe de Oliveira da Silva - 0x19a701a85758b5c11675B85b84Ef37b1B5C4aa88
Sylvia Sued - 0x830dB68b5C2C0238BF6A89Df5cb0Ce1106318681
Carlos Prata - 0xA55630BeBcF62b7FC49F67fBB4d597cdD383C0cA
Daniel de Castro - 0x4661AC723b0C6a854A5E684fcE7E697aD8cAD386
ricardo vaz - 0x001109A10aC935a06fECF794E1B9990302C9Bcf5
Antonio Quental token: 0x1Ce69022c67Af669f12316028FADcB861fbd5b04
Rodolfo Leal - 0xfA79e4b58Bdd88Aebf1C3CA0dbE585ed09981803
Cleverson Silva - 0x65BBe270aa746C117dFc4CAbD36239F52A56C8C7
Luiz Gustavo Nugnes. - 0x9aa873aFd33A19Cb8297D97681f97c5874495cCD
Christian K.S. - Token 1 - 0xfF527fE65Fe9F26F8c61fBc0940A375050Bcf965
Ronaldo Cassio - 0xf4584dfBC708a1b2FeE4EDA6256280cfD4A120f1
Bruno Castanheira - 0xBE8274C4db2b8c2Db5D573e2c4711A7C13521d37
Carina Cunha - 0xa52f120fa46d79b551f5160eE709267D598f3471
Tulio - 0xa80743c72125641463727c4c282df779680c710786e657d35a5874735966175c
Viviane - 0x6122D4EFF5421F952380Efdf41007f859293e796
André Meira - 0x8f4f39029c813a9e2dc9f099c4df9e7bf70cf330
Tiago Ferreira Cavazin - 0x8a273ed47a6469ff7ccfc5da0c3de1b9830d54df
Paulo Silva - 0x132c66d4734b12F5E9B8AC7fd0111aE1C3cEae63
Brandell Ferreira - 0xB0d8476D0357b1708386831137ef2747d63F1E98
Leonardo Carvalho - 0x8d862fb0eaed90895a35d19acad33156abf6fc2a
Thiago Madureira de Souza Machado - 0xaa9697306019563773e56983f58c551DEa2f181C
Alexander Cardoso - 0xfbc8f28ADF6323E1221510F0EA994Ae8De4fB676
Ronaldo Miranda da Cunha - 0x4C5De88a7512c21066752E8550a4fA31BAE08904
Moana Coelho - 0x5E114a1C24edb6Ab78E83315fd768ffF327425B3
Tiago Ferreira Cavazin - 0x5D280d72009CbAd1C2f63585Ce980bc6Ec5B7164
fernando -0x2a8b8649D0cAfDB1FD4d08b6F036B9C4B41Ddb76
Igor Mateus de Lima Nunes - 0x6B478A8c9e009F39a5640585271DF75F47Ca63E7
Fabiano Pereira - 0x9E62c3817DD7EdeB2729994d650352558BA718b2
Thiago Borges - 0x943b32C32D2290Fdc9Ea19d3143DBfc042A08D16
Rafael Vasconcelos - 0xF01F4B86399b000Bd69F1e0aE118BA9d3B35Ad1E
JONAS MORAIS QUEIROZ - 0x4b7b622d2825002cA8B905C6A50d96BFdfadb9C1
Adrian Felipe Gaspari - 0x8FbE989FB45ecE010928290687D82Fb05a094e8E
João Luiz Miotto - 0x8208eedbe397ea9cc7ea33172aa4e75a8e8669ec
Raffa Loffredo - 0xFcd64bd5c3678c015dd99976aEEA7afC7C00e426
Carlos Otavio Romano Pieper - 0x7636112F94eBDBD7ceA5aa88D479ACc05cfed0c1
José Aldo - 0xF0b8cfd58091A72662fA09BbE1FdA369a4383e36
Thauã Fernando Werner - 0x3274e7b4c183A2fE15a6700B5CD08C44dBA38Ff2
Einar César - 0x3e225c5EF3bB14993EB0abA111B094E95Fa98AeE
Guilherme Jorge Giorgi - 0x8f0Efda009F1739e5C9B8355fe707EA091a74b30
Daniel Basilio - 0x6C5a4FA0d776D09242F3DE624d01bd52507736cA
Sandra - 0xfc1a9a9765cea7a7DB8aD1f16e7022242803D9D0
Rafael - 0xCa37cc33fCc06C4e6767BE5e936249e6106669a9
Bruno Franco - 0x96Bd9c7c120A9d5B695c54A9f22E4A0E4c7051D9
Marco Monteiro - 0xC146Cf777F0A3AF6A0f96011a8d1305f1A4B4fC6
DIRCEU L GUERRA- 0x8974E421Dad70375d7e8dc16440276Dbe5ea27E2
Lucas Machado - 0xC5DFe29aEC8eFF1474c132955e447F92e39ac786
Emanuel Oliveira - 0x37297f7Bb775a222803A9b327710d9bd71403D87
Vagner da Silva - 0x4C1fe68492ab2cDf8F9e5b830387058C702FD8DB
Hiago Ferucci - 0xF8E330F02E392aC88350dDFF39d11E69A093E35F
Carlos Alberto Mero - 0x3046F4e004E54ed3F825BAC513Bb9180de59Cbc6
Thiago Lobo - 0x8AC8D3F55C3e675ac8d36bf53B0078507c4C4daa
Cristina Amazonas - 0xC7bc30c873C6FC2F2E0a75fA53940755Fe80f209
Sammyah - 0xd54DEAa60dd159E0907e2e78B95451861707ccFC
Airo Avila - 0x87cB7B956FD2AF7492A9ac0c18CbB2a597Be8bbA
Lucas Carapiá - 0x8579De6D12d1F7Be0391f3A4cCDc4F9214277a72
Carlos Diniz - 0x51055780f221b735d66b20427338f04ac83af204
Matheus Zimmermann - 0x7175bC248DB29dF0F68C6aed25Df054dd73D9F5a
Felipe Gama Santos - 0xA9f4F6F36a997d993fb8931b4a12B1E86808ff0D



*************************************************************

Metamask
Tokens
Import
Colar o endereco do token

No Remix, no token

Mint
To - Sua carteira
Amount - 10000

balanceOf
Sua carteira

********************************

Criar
TokenShop.sol

// Inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.21;

import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

interface TokenInterface {
    function mint(address account, uint256 amount) external;
}

contract TokenShop {
    
    AggregatorV3Interface internal priceFeed;
    TokenInterface public minter;
    uint256 public tokenPrice = 200; //1 token = 2.00 usd, with 2 decimal places
    address public owner;
    
    constructor(address tokenAddress) {
        minter = TokenInterface(tokenAddress);
        /**
        * Network: Sepolia
        * Aggregator: ETH/USD
        * Address: 0x694AA1769357215DE4FAC081bf1f309aDC325306
        */
        priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306);
        owner = msg.sender;
    }

    /**
    * Returns the latest answer
    */
    function getChainlinkDataFeedLatestAnswer() public view returns (int) {
        (
            /*uint80 roundID*/,
            int price,
            /*uint startedAt*/,
            /*uint timeStamp*/,
            /*uint80 answeredInRound*/
        ) = priceFeed.latestRoundData();
        return price;
    }

    function tokenAmount(uint256 amountETH) public view returns (uint256) {
        //Sent amountETH, how many usd I have
        uint256 ethUsd = uint256(getChainlinkDataFeedLatestAnswer());       //with 8 decimal places
        uint256 amountUSD = amountETH * ethUsd / 10**18; //ETH = 18 decimal places
        uint256 amountToken = amountUSD / tokenPrice / 10**(8/2);  //8 decimal places from ETHUSD / 2 decimal places from token 
        return amountToken;
    } 

    receive() external payable {
        uint256 amountToken = tokenAmount(msg.value);
        minter.mint(msg.sender, amountToken);
    }

    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }

    function withdraw() external onlyOwner {
        payable(owner).transfer(address(this).balance);
    }
}

//Fim

On CONTRACT
Selecionar TokenShop
(NAO TokenInterface)

Parameter: endereco do seu token
Deploy

Nome - TokenShop Address

Christie Cardoso - 0xABA033553Ddf58CEb70330e8ADF774152Fd16E9A
D0A04C75eD908440D83718


*******************************************
No Token

Precisa autorizar o TokenShop a emitir Tokens

MINTER_ROLE
0x9f2df0fed2c77648de5860a4cc508cd0818c85b8b8a1ab4ceeef8d981c8956a6

grantRole
role
0x9f2df0fed2c77648de5860a4cc508cd0818c85b8b8a1ab4ceeef8d981c8956a6

account
Endereco do seu TokenShop

Veja se está autorizado

hasRole
role
0x9f2df0fed2c77648de5860a4cc508cd0818c85b8b8a1ab4ceeef8d981c8956a6

account
Endereco do seu TokenShop

Resposta: true

Va para TokenShop

getChainlinkDataFeedLatestAnswer
252690577790
ETH/USD 2526.90577790

Ao meio dia
245307033207
ETH/USD 2453.07033207


https://eth-converter.com/
Amount 0.01 ETH = 10000000000000000 wei

para saber a quantidade de tokens que voce receber
tokenAmount
10000000000000000

Resposta
1263
= 12.63 tokens


Metamask
Enviar 0.01 ETH
Para o endereco do seu tokenShop

Comprando tokens do amigos



Christie Cardoso - Token: 0xABA033553Ddf58CEb70330e8ADF774152Fd16E9A
TokenShop: 0xB2c82b57F5dbDd0b364Bab39919a270377C809E7




Christie Cardoso - 0x985A7F5401312a0b7b4B4De164b9Fd607aB72DC7 TokenShop
       0x0E933F5d7139Ed531d5cC8588Ca5af9D5df235dc



