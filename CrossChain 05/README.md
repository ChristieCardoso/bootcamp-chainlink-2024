
Chainlink Bootcamp por Solange Gueiros Jan/2024

Aula 07
https://pad.riseup.net/p/cl-pt-07


Oi, Sol!

Espero que esteja bem e estamos prontos para a aula!
Beba seu Monster e bora lá!
Ass. Melhor turma de Bootcamp Ever!


*****************

Slides
https://docs.google.com/presentation/d/e/2PACX-1vQDouGb19sN3qGO2zDU5VZ-JXhgnJoqVCvykLT6tbOOqX7zXqwqrJxPciBohc66nsoGs_L_UB9QZUQs/pub?start=false&loop=false&delayms=3000

Tarefas
https://forms.gle/k36cuefTTwchdPHw7

Todas as aulas estao aqui
https://lu.ma/ig70bk68

Faucet
https://workshop-faucet.vercel.app/faucets
cllworkshop0101

Exercicios do Bootcamp
https://github.com/solangegueiros/chainlink-bootcamp-2024

https://chain.link/community/advocates
https://chain.link/developers/experts

Portal do dev
https://dev.chain.link/

Link do grupo @Telegram:
https://t.me/+sw2bRlynKwExNzMx


Nome - Cidade, Pais
-------------------

Christie Cardoso - BH - BR

***************************************************************

Metamask
Sepolia

https://remix.ethereum.org/

Icon 5 - DEPLOY & RUN TRANSACTIONS
ENVIRONMENT
Injected provider - Metamask
Sepolia

Criar
CrossChainPriceNFT.sol

// Inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

// Deploy this contract on Sepolia

// Importing OpenZeppelin contracts
import "@openzeppelin/contracts@4.6.0/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts@4.6.0/utils/Counters.sol";
import "@openzeppelin/contracts@4.6.0/utils/Base64.sol";

// Importing Chainlink contracts
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract CrossChainPriceNFT is ERC721, ERC721URIStorage {
    using Counters for Counters.Counter;
    using Strings for uint256;

    Counters.Counter public tokenIdCounter;

    // Create price feed
    AggregatorV3Interface internal priceFeed;
    uint256 public lastPrice = 0;

    string priceIndicatorUp = unicode"😀";
    string priceIndicatorDown = unicode"😔";
    string priceIndicatorFlat = unicode"😑";
    string public priceIndicator;

    struct ChainStruct {
        uint64 code;
        string name;
        string color;
    }
    mapping (uint256 => ChainStruct) chain;

    //https://docs.chain.link/ccip/supported-networks/testnet
    constructor() ERC721("CrossChain Price", "CCPrice") {
        chain[0] = ChainStruct ({
            code: 16015286601757825753,
            name: "Sepolia",
            color: "#0000ff" //blue
        });
        chain[1] = ChainStruct ({
            code: 14767482510784806043,
            name: "Fuji",
            color: "#ff0000" //red
        });
        chain[2] = ChainStruct ({
            code: 12532609583862916517,
            name: "Mumbai",
            color: "#4b006e" //purple
        });

        // https://docs.chain.link/data-feeds/price-feeds/addresses        
        priceFeed = AggregatorV3Interface(
            // Sepolia BTC/USD
            0x1b44F3514812d835EB1BDB0acB33d3fA3351Ee43            
        );

        // Mint an NFT
        mint(msg.sender);
    }

    function mint(address to) public {
        // Mint from Sepolia network = chain[0]
        mintFrom(to, 0);
    }

    function mintFrom(address to, uint256 sourceId) public {
        // sourceId 0 Sepolia, 1 Fuji, 2 Mumbai
        uint256 tokenId = tokenIdCounter.current();
        _safeMint(to, tokenId);
        updateMetaData(tokenId, sourceId);    
        tokenIdCounter.increment();
    }

    // Update MetaData
    function updateMetaData(uint256 tokenId, uint256 sourceId) public {
        // Create the SVG string
        string memory finalSVG = buildSVG(sourceId);
           
        // Base64 encode the SVG
        string memory json = Base64.encode(
            bytes(
                string(
                    abi.encodePacked(
                        '{"name": "Cross-chain Price SVG",',
                        '"description": "SVG NFTs in different chains",',
                        '"image": "data:image/svg+xml;base64,',
                        Base64.encode(bytes(finalSVG)), '",',
                        '"attributes": [',
                            '{"trait_type": "source",',
                            '"value": "', chain[sourceId].name ,'"},',
  

                          '{"trait_type": "price",',
                            '"value": "', lastPrice.toString() ,'"}',
                        ']}'
                    )
                )
            )
        );
        // Create token URI
        string memory finalTokenURI = string(
            abi.encodePacked("data:application/json;base64,", json)
        );
        // Set token URI
        _setTokenURI(tokenId, finalTokenURI);
    }

    // Build the SVG string
    function buildSVG(uint256 sourceId) internal returns (string memory) {

        // Create SVG rectangle with random color
        string memory headSVG = string(
            abi.encodePacked(
                "<svg xmlns='http://www.w3.org/2000/svg' version='1.1' xmlns:xlink='http://www.w3.org/1999/xlink' xmlns:svgjs='http://svgjs.com/svgjs' width='500' height='500' preserveAspectRatio='none' viewBox='0 0 500 500'> <rect width='100%' height='100%' fill='",
                chain[sourceId].color,
                "' />"
            )
        );
        // Update emoji based on price
        string memory bodySVG = string(
            abi.encodePacked(
                "<text x='50%' y='50%' font-size='128' dominant-baseline='middle' text-anchor='middle'>",
                comparePrice(),
                "</text>"
            )
        );
        // Close SVG
        string memory tailSVG = "</svg>";

        // Concatenate SVG strings
        string memory _finalSVG = string(
            abi.encodePacked(headSVG, bodySVG, tailSVG)
        );
        return _finalSVG;
    }

    // Compare new price to previous price
    function comparePrice() public returns (string memory) {
        uint256 currentPrice = getChainlinkDataFeedLatestAnswer();
        if (currentPrice > lastPrice) {
            priceIndicator = priceIndicatorUp;
        } else if (currentPrice < lastPrice) {
            priceIndicator = priceIndicatorDown;
        } else {
            priceIndicator = priceIndicatorFlat;
        }
        lastPrice = currentPrice;
        return priceIndicator;
    }

    function getChainlinkDataFeedLatestAnswer() public view returns (uint256) {
        (, int256 price, , , ) = priceFeed.latestRoundData();
        return uint256(price);
    }

    function tokenURI(uint256 tokenId)
        public view override(ERC721, ERC721URIStorage) returns (string memory)
    {
        return super.tokenURI(tokenId);
    }

    // The following function is an override required by Solidity.
    function _burn(uint256 tokenId) internal override(ERC721, ERC721URIStorage)
    {
        super._burn(tokenId);
    }
}

// Fim

Deploy

Nome - CrossChainPriceNFT Address
Sol - 0x19a602627615616193088bC199Ede96B94bb5Ab4
Christie Cardoso - 0x49FD34AEa0f5C416a79f91c0e61293d5De1285b8

**************************************

https://testnets.opensea.io/

Nome - NFT Collection
Sol - https://testnets.opensea.io/collection/crosschain-price-93
Christie Cardoso - https://testnets.opensea.io/collection/crosschain-price-9

*************************************************************************************

On Remix
CrossChainPriceNFT

Mint
Para sua carteira Metamask

Criar 
CrossDestinationMinter.sol

// Inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

// Deploy this contract on Sepolia

import {CCIPReceiver} from "@chainlink/contracts-ccip/src/v0.8/ccip/applications/CCIPReceiver.sol";
import {Client} from "@chainlink/contracts-ccip/src/v0.8/ccip/libraries/Client.sol";

interface InftMinter {
    function mintFrom(address account, uint256 sourceId) external;
}

/**
 * THIS IS AN EXAMPLE CONTRACT THAT USES HARDCODED VALUES FOR CLARITY.
 * THIS IS AN EXAMPLE CONTRACT THAT USES UN-AUDITED CODE.
 * DO NOT USE THIS CODE IN PRODUCTION.
 */
contract CrossDestinationMinter is CCIPReceiver {
    InftMinter public nft;

    event MintCallSuccessfull();
    // https://docs.chain.link/ccip/supported-networks/testnet
    address routerSepolia = 0x0BF3dE8c5D3e8A2B34D2BEeB17ABfCeBaf363A59;

    constructor(address nftAddress) CCIPReceiver(routerSepolia) {
        nft = InftMinter(nftAddress);
    }

    function _ccipReceive(
        Client.Any2EVMMessage memory message
    ) internal override {
        (bool success, ) = address(nft).call(message.data);
        require(success);
        emit MintCallSuccessfull();
    }

    function testMint() external {
        // Mint from Sepolia
        nft.mintFrom(msg.sender, 0);
    }

    function testMessage() external {
        // Mint from Sepolia
        bytes memory message;
        message = abi.encodeWithSignature("mintFrom(address,uint256)", msg.sender, 0);

        (bool success, ) = address(nft).call(message);
        require(success);
        emit MintCallSuccessfull();
    }

    function updateNFT(address nftAddress) external {
        nft = InftMinter(nftAddress);
    }
}

// Fim

Deploy
Parametro: the NFT collection address



Nome - CrossDestinationMinter address

Christie Cardoso - 0x9074384045cb34D66FCF692cb9906D488E7794F8


**********************************************************

No contrato CrossDestinationMinter, 
executar as funções

***********testMint**************

Thiago Borges - 0x22fa89eaf5043234c194a140006df7c49de6218e562a1df5835bada55259d4ff
Vinícius de França - 0xcd7ba4de760e2e0bad00640aa6140a15107a2b42614e35c9612c962a4a263f68
Johnson Chen - 0xe513771c9f02ad790a2dade17c1504279bbeec4ba1f31e69bf9b3344b6495575
Edson Junior - 0xbec867d386e8854e7831fdddf4721b65c5e7d014c7bc2cf3d10e32cac494ca91
Mauricio Schiesari - 0x3b67fc4641fd75e942719a788fa43ca94c6e7e745a364e499060f47b4664458e
Brandell Ferreira - 0x46c7ed7abb47012bfef4c075851a496a9f4b1a98533079ead293f7350682dd01
Cairo Vinícius - 0x65b05eb7c8b126eb6fb72e9662de9ef5c749eb45d0f363f71566d6a35e32a0f2
Faury - 0xa134503929bab4ad780099c4ef370dfe665c68a7a58bc0c0a7c55f51decc67f5
Cristina Chacón - 0x52376e736c64eb0677a0359c2a1a44279a0a37027446f6f45725ffe6dcdee2e6
David Marono - 0xA2Fea34790a87aA0E513523b2FE1bfB5Af9c5C58
Andre Kort Kamp - 0x9ffb039eaa0302e4ac296cbecc969bd65a9a096cc6c05e6aa975ea66a1388412
Boby - 0xcbc60d25e29203d38ff1e2f5d4f0c1e12c87178326334e11afa34445c3fab13a
Thiago Madureira de Souza Machado - 0x8319f93c71765bfbd3b5193dec38ddbbfefe6954c2691071203040929b380d6d
Diogo Silva - 0xda77c544bfa34dd42273840b2894c765aba339abcd0b187c8415ce333d2fbd14
Erik Alonso - https://sepolia.etherscan.io/tx/0x2418cfc69bd0a0c3a32324baa81d46738baa6c997ee5acb79845cdda4208086a
André Luiz Rodrigues - 0x6f3109b079e9d4f34d5b256871beb28693b704990bbf4cd0ead8bdd1bf9dc245
Marcelo Santana - 0x33aa352f4d21fc7ca723beb69badd00f5e8f2041dc2e6a43d746e97d128ef779
Vinícius Felipe de Oliveira da Silva - 0xa8a3048fbf7f419febe56d5872c4193044ef68ecf9f0fe3c5750bd643dac6316
Ramires Cardoso - 0x56b820af0c3b854a1ee08c7a336f8a427b62134ee854a89fe90669df0602d12b
Lisa Dafny - 0x16d4977ceaf1cdbf7a78ca932cc0df64d6684d9225e61e91a5a76803aaf74230
Tiago Chaves de Oliveira - https://sepolia.etherscan.io/tx/0x1a42295492e1c0709a32f64c8e458d787c1fca64994c7df0061d94e1d068bee1
Rodrigo Vrindavan: 0x2381748eba6f00b75b1fa35d674c8fdf1538d2893017eefd78c6ca7da47852f2
Rodiney - 0xcb96056ee0ede11f7f63ade151e11563532fd5a297169dd3f6ffc0f7850ab95c
Christian K.S. - Testmint - 0x9859c62ff769406d578bca7df8a0e26eac218582d6f001b69375c58c7f0a2f7c
Raffaela Loffredo - https://sepolia.etherscan.io/tx/0xaded9b2d6da65315e2d29285bf93092d09fa27fbe50cf03f2f992381ffcc5f33
Otávio Neto - 0x322cea16b12d791588cc9188fbcb7dbe5e680433e004f3748aea5e9b43096634
Felipe Macedo - 0xd13e5929e464710678157336f2e4dbc3880ccd20832d92c388373c7caa2ebb74
Pedro Rodrigues - 0x3e60c82d438311ffc008270a2c4f43d71b10342e227fb4b25ac9d36d426e262

**********testMessage************

Tiago Chaves de Oliveira - https://sepolia.etherscan.io/tx/0xf132be9102dd1862ed744b3c97006af8c6f4d90503d6c5846dc9736e57a9e1da
Cairo Vinícius - 0xe1abcb730a11fa5a479ea12b5bb9e4d5b833690451bd99a6ff5fb0ccc83feb31
Thiago Borges - 0x38a4b82c08e9a0006605fb4cfb3ea1d66de3fe33e15291e37f00870c076b65c5
Andre Kort Kamp - 0x460a115b2cc681da7ac2f99cfea379d99de95a6d0e2c0540dc0afdcc6b6426d2
Faury Nunes de Lima - 
0x72183d80a32e906cbd8cad11ed24847c532f4ad2034391c06c0fc72779295073
Edson Junior - 0xc2e11544b0fbe788cb93f1f5117afd468f13b00b32281634b780e575521a7292
Mauricio Schiesari - 0x4d34f253aa0b9b4bf1a14cbf0993c7a5c8b89f75e8391411cfbef32925bef0d9
Diogo Silva - 0xda77c544bfa34dd42273840b2894c765aba339abcd0b187c8415ce333d2fbd14
Boby - 0x6f583236f306db52dcd2fb718e328dc5a96074405bd5ac07b2d09ce6268fceb7
Vinicius de França - 0xc37fb16b502024e7407d46c2bb52c5c8cce57b1010f7941b942b597259df1678
Cristina Chacón - 0x8bcbf2c0c9c406dae22ebef191717b486f9e43901e5dd792929601083827aa2b
Thiago Madureira de Souza Machado - 0x56bdfd5d3f01322c78aa52ccdd87c53a5d29dfee339ff83ec181e064fb8b8644
Barba - 0xef3569e9439901d34bc32fa65ef3f25118223f1300d3653580ce9b01f94ba63c - Mint
Barba - 0x3adebc280834e04a816fc915a57d41abd13e5fe78e423efdbdb8648f3503f029 - Message
André Luiz Rodrigues - 0xbc8d068837c459f8a45799bd44cb01d01d93427cfa91fa527f76bcfb789278e9
Marco Monteiro - 0xc576afe8c8f125a776faaedb113ca4856ac2a5d905ffa8367bc6d85cfcd09837
Rodrigo Galvan - 0x2a56bb1390feffb118da59fd24fb4c5aee869b6588bb5d51dffe97843d1248a3
Federico Zighue - 0x23b55cdf752e0fcf6347a983b710de19e65b736e392b46122ea70e35099e6d3e
Marcelo Santana - 0xdae039e5b6e127ccfc5a0c2b73ccc25e3f2265580125a5ddec4409471ba36fb9
Rodrigo Vrindavan: 0x3165abcd7230cd7fe14c721005843fa496cac7cb938e0fb25b22337d42f4bc82 
Renan Castro - https://sepolia.etherscan.io/tx/0x978c13ba8c141a95cdb8379fde91836e2e97b7e331dad98e5bd07757bb7b0ea8
Renato Cadecaro - CrossDestinationMinter -  testMint - https://sepolia.etherscan.io/tx/0x8822a676f7d1a77c04cdc69ed7779ba878eb87dc13e9565c6dff34ec2f3e7537
Renato Cadecaro - CrossDestinationMinter -  testMessage - https://sepolia.etherscan.io/tx/0x8ea69db8d78730e09432db91713f147e2532b2a9b1336d7c8bf219a99cb3a9fd
Johnson Chen - 0x090db7cf7b795492de336931d1dd79aa379456604c7f895832b0f525e002a38f
Alexandre Gomes - CrossDestinationMinter - testMint -tx-id 0x793be2ed277389eca5933987a7eb5f3dec26e92acc4463cf212d218d77c7a627
Alexandre Gomes - CrossDestinationMinter -  testMessage -tx-id 0x8be36532ca3d3c0e82f3e3a262c425fcb10d2a126ea437200af1dce245da98a0 
Marco Monteiro - 0x72ab4ead0192e49ac3ebc6a894b28e76f7a7ede128b71bdf37475f0cb9215749
David Marono - 0xA2Fea34790a87aA0E513523b2FE1bfB5Af9c5C58
Renato Ferrari - testMint - 0x7f3fce72fbbf65a46dabc7a448b9d74a478bbe61798ef7c491e535d7fe337fef
Gabriel Thome -> 0x4afeeB2A1c0403f207060c3c46645291f3bA9455
Renato Ferrari - testMessage - 0xce1d23c3d38a7abd398e2dbab3d3f01fc6ec4e2006c92925a4729a076b21e97b
André Meira - TestMint -0x2b655803dea488dd0e1e4153ce88c5e67cc60a280f37ec412ce814c7709272cb
TestMessage - 0x96c0531f952f933fb58fbd4ce70d7e1d05d68b8efcc49cf757b4bb80f7feb938
Cleiton Fraga - TestMint - https://sepolia.etherscan.io/tx/0x94a204741ba4f4337fc4a03bdee2964807dbe18f2f8bba4d1f654a7922d0804f
Cleiton Fraga - TestMessage - https://sepolia.etherscan.io/tx/0xa14e750641fd3d326f47132a2c022a0248a6c05448683f77c44e1e4824e6fad3
Paulo Silva - CrossDestinationMinter - TestMint
0x79d81de29bfbfa465b3692b765df48c30897cd6dcd5fc6fbc5c1102264c8ab86
TestMessage - 0xa4c80358f3f02362da9bdc90264025f5588e9c3ef2abe307d084e97704cc704f
Rodrigo Galvan - 0xbe9653d1cbeffb4dbe311b84dc612dc2573df72fd33683f84d7c30e2c0fa6a25
Ronaldo Cassio - https://sepolia.etherscan.io/tx/0x33916e58b72acd6853e3e2031b04f5c13ffe36ea0be2944500c8ad15c0b29277
https://sepolia.etherscan.io/tx/0x796f74a106d4430ed5a316a6115f903543d6461ba788487c828e604f2a2f3f20
Erik Alonso - https://sepolia.etherscan.io/tx/0xa42fca8022a8efe64ebb3ef031d6a7e140abbd314ca534ff77b5df302d0e0ff4
Brandell Ferreira - 0xa14f684bbe40bbb4cea9334ed81d73f2ec5f0f8852bbdddf7ae24b2485f95d84
Renan Castro - https://sepolia.etherscan.io/tx/0xf92e0905ed0400195b244d2238726c2d171af7a2ecc26124eb4a1a0f9ee8bf15
Maria Eduarda - testMint 0xf468241dcdfbf759d2f0ba9393ec801945a541266fb61ca4066ff3386e61e1bc - testMessage 0x420c12cbc70e614fc3ff54e2a690c9c239108e27d6ae7546a26d2822cb59dcc8
Diego Aguiar - Test mint/Test messg. 0x5f728a8a0423685689f4d5b285634d638a56a47fcc42076cf67a7c9cd2987590/ 0xb27ecfa249dc290566dc4d64cceeb5e9d7a8ff2b03c5c0cbe7edb432561b851c
Lisa Dafny - 0x1104f07fff4cef2df0482694fda85cab32fb5f0423f224dcf45197411a0cfba0
Nuno Edgar Nunes Fernandes TestMint on EtherScan: https://sepolia.etherscan.io/tx/0x6dfdc94b2e6208fb63b175ea1f34fdbe5827caa26e1bfe52c64a2902984a3f97
Nuno Edgar Nunes Fernandes TestMessage o EtherScan: https://sepolia.etherscan.io/tx/0xb9dbca91413825c65af36ca39b659b646c23e54791e12793387032dd1c0d2a22
Ricardo Gomes
        testMint 0x9435d33161826c16d19b1719484130dca981b3bcb8cbc814453933144e0ca4a9
        testMessage 0xbcafda22c4296786ca9a87db2e9a111d7d85306f210560c522edabd5433cc17b
Jether Rodrigues: 
    - Test Mint: https://sepolia.etherscan.io/tx/0x124e06bd4b405556dc784085fb8a1e425fc21fa714fca680ba2739940a8d482c
    - Test Message: https://sepolia.etherscan.io/tx/0xabd60ef174d9b1044bde273d96c8875443a27c15bdc98f66183136bd7f82ae01
    David Marono - 
Rafael Said - https://sepolia.etherscan.io/tx/0x97b991317746fec09b2f41a22b51d218f0a219f234f7f21e5c3043b989fc3989
Federico Zighue - 0x3bed1f9087bc0d95b94687a5cc5ced2ddfcd9b782537bb07e084498ee2eeb670
Leoma Leomar https://testnets.opensea.io/assets/sepolia/0x1761c2933d2b7262f0fce6381244e20a1af6a19e/5
Ramires Cardoso - 0x233b6826072a51a4670367869af6484d265fdbd386ea0a1f66e19ac576e2f27e
Joao 0x5a825292c1d61605303540850ee3b29030e25b586b241e1d76a657fc9456b3de
Vinícius Felipe de Oliveira da Silva - 0x10cec5da1a0d5e55e354d83a869d7c4edd768c8c844e3bef4204803bc50bcdd3
Rodiney - 0x4ee685ccfa88249462f6058a14221cd4718848afdf6f5f90767d9aab60acb8bf
Christian K.S. - Testmessage - 0x08e71b2002d1ed602a5df08025da4fe05b07c0b81056437620c9de878940c4f1
Raffaela Loffredo - https://sepolia.etherscan.io/tx/0xcafbaf174349f8d96e2b54116511f981a7e93c5fca56910aa1473188c84314f8
Otávio Neto - 0x69d441581789b89f32e10fbf7f396e72dce3335a2c517121cd0e2f9bb1c9526f
Marcio - 0x3Dbe10d974766dae43475768812ceeD798227D58
Felipe Macedo - 0x9703c49ca4da8ebe710f7fce08cb8f0cf00c039cea464a690e82e3395f217d74
Pedro Rodrigues - 0x741b2d6f265a37dd86d9cbc93d99b9c5205cfc4fa0bd00a6ac58c875f02e2673
71. Rodrigo Gabriel - https://sepolia.etherscan.io/tx/0xa6769485633e9d3acd087c890b53e27281e27a05b9266c30256af2ba69063e3b

***********************************************************************

https://chainlist.org/chain/43113

Metamask
SELECIONE Avalanche FUJI
https://workshop-faucet.vercel.app/faucets/fuji
https://faucets.chain.link/fuji

No Remix
Criar
CrossSourceMinter.sol

// Inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

// Deploy this contract on Fuji

import {IRouterClient} from "@chainlink/contracts-ccip/src/v0.8/ccip/interfaces/IRouterClient.sol";
import {Client} from "@chainlink/contracts-ccip/src/v0.8/ccip/libraries/Client.sol";
import {LinkTokenInterface} from "@chainlink/contracts/src/v0.8/interfaces/LinkTokenInterface.sol";

/**
 * THIS IS AN EXAMPLE CONTRACT THAT USES HARDCODED VALUES FOR CLARITY.
 * THIS IS AN EXAMPLE CONTRACT THAT USES UN-AUDITED CODE.
 * DO NOT USE THIS CODE IN PRODUCTION.
 */
contract CrossSourceMinter {

    // Custom errors to provide more descriptive revert messages.
    error NotEnoughBalance(uint256 currentBalance, uint256 calculatedFees); // Used to make sure contract has enough balance to cover the fees.
    error NothingToWithdraw(); // Used when trying to withdraw but there's nothing to withdraw.

    IRouterClient public router;
    LinkTokenInterface public linkToken;
    uint64 public destinationChainSelector;
    address public owner;
    address public destinationMinter;

    event MessageSent(bytes32 messageId);

    constructor(address destMinterAddress) {
        owner = msg.sender;

        // https://docs.chain.link/ccip/supported-networks/testnet

        // from Fuji
        address routerAddressFuji = 0xF694E193200268f9a4868e4Aa017A0118C9a8177;
        router = IRouterClient(routerAddressFuji);
        linkToken = LinkTokenInterface(0x0b9d5D9136855f6FEc3c0993feE6E9CE8a297846);
        linkToken.approve(routerAddressFuji, type(uint256).max);

        // to Sepolia
        destinationChainSelector = 16015286601757825753;
        destinationMinter = destMinterAddress;
    }

    function mintOnSepolia() external {
        // Mint from Fuji network = chain[1]
        Client.EVM2AnyMessage memory message = Client.EVM2AnyMessage({
            receiver: abi.encode(destinationMinter),
            data: abi.encodeWithSignature("mintFrom(address,uint256)", msg.sender, 1),
            tokenAmounts: new Client.EVMTokenAmount[](0),
            extraArgs: Client._argsToBytes(
                Client.EVMExtraArgsV1({gasLimit: 980_000})
            ),
            feeToken: address(linkToken)
        });        

        // Get the fee required to send the message
        uint256 fees = router.getFee(destinationChainSelector, message);

        if (fees > linkToken.balanceOf(address(this)))
            revert NotEnoughBalance(linkToken.balanceOf(address(this)), fees);

        bytes32 messageId;
        // Send the message through the router and store the returned message ID
        messageId = router.ccipSend(destinationChainSelector, message);
        emit MessageSent(messageId);
    }

    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }

    function linkBalance (address account) public view returns (uint256) {
        return linkToken.balanceOf(account);
    }

    function withdrawLINK(
        address beneficiary
    ) public onlyOwner {
        uint256 amount = linkToken.balanceOf(address(this));
        if (amount == 0) revert NothingToWithdraw();
        linkToken.transfer(beneficiary, amount);
    }
}

// Fim

Deploy
Parameter é o endereço do CrossDestinationMinter (publicado na Sepolia)

Nome - CrossSourceMinter

Christie Cardoso - 0xe999056e5DbB2266e05bd108B443698aFbf1FBd6


***************

No Metamask
Rede Avalanche Fuji

Envie 6 LINK para o endereço do CrossSourceMinter

transaction ID

Christie Cardoso - 0x5beb65991f79bdf019ebcf69378bd6cc53b4ea47f33d0964eb58a9ac1ab2b202

**************************

Veja o saldo de LINK do CrossSourceMinter

No Remix, CrossSourceMinter

linkBalance
Parametro: endereço CrossSourceMinter

O resultado será
6000000000000000000
= 6 LINK


Vamos criar um NFT a partir da rede Fuji que vai aparecer na rede Sepolia!!!

mintOnSepolia


Veja a transacao em 
https://ccip.chain.link/


nome - MintOnSepolia transaction id

Christie Cardoso - 0x1f7ddaa28bd66d92591979711a8eb0efbdac47cdda3185638ef4225b65f37dce


******************************

https://ccip.chain.link/



Christie Cardoso - https://ccip.chain.link/msg/0xb0e3f53058d96f69a610f84bb9df945307911f00073232ce250a34dd13961835

*************************************************


Adicionar a rede Polygon Mumbai no Metamask

https://chainlist.org/chain/80001 - MUMBAI

No Metamask
Selecionar Mumbai

No Remix
Criar
CrossSourceMinterMumbai.sol

// Inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

// Deploy this contract on Mumbai

import {IRouterClient} from "@chainlink/contracts-ccip/src/v0.8/ccip/interfaces/IRouterClient.sol";
import {Client} from "@chainlink/contracts-ccip/src/v0.8/ccip/libraries/Client.sol";
import {LinkTokenInterface} from "@chainlink/contracts/src/v0.8/interfaces/LinkTokenInterface.sol";

/**
 * THIS IS AN EXAMPLE CONTRACT THAT USES HARDCODED VALUES FOR CLARITY.
 * THIS IS AN EXAMPLE CONTRACT THAT USES UN-AUDITED CODE.
 * DO NOT USE THIS CODE IN PRODUCTION.
 */
contract CrossSourceMinterMumbai {

    // Custom errors to provide more descriptive revert messages.
    error NotEnoughBalance(uint256 currentBalance, uint256 calculatedFees); // Used to make sure contract has enough balance to cover the fees.
    error NothingToWithdraw(); // Used when trying to withdraw but there's nothing to withdraw.

    IRouterClient public router;
    LinkTokenInterface public linkToken;
    uint64 public destinationChainSelector;
    address public owner;
    address public destinationMinter;

    event MessageSent(bytes32 messageId);

    constructor(address destMinterAddress) {
        owner = msg.sender;

        // https://docs.chain.link/ccip/supported-networks

        // from Fuji
        address routerAddressMumbai = 0x1035CabC275068e0F4b745A29CEDf38E13aF41b1;
        router = IRouterClient(routerAddressMumbai);
        linkToken = LinkTokenInterface(0x326C977E6efc84E512bB9C30f76E30c160eD06FB);
        linkToken.approve(routerAddressMumbai, type(uint256).max);

        // to Sepolia
        destinationChainSelector = 16015286601757825753;
        destinationMinter = destMinterAddress;
    }

    function mintOnSepolia() external {
        // Mint from Mumbai network = chain[2]
        Client.EVM2AnyMessage memory message = Client.EVM2AnyMessage({
            receiver: abi.encode(destinationMinter),
            data: abi.encodeWithSignature("mintFrom(address,uint256)", msg.sender, 2),
            tokenAmounts: new Client.EVMTokenAmount[](0),
            extraArgs: Client._argsToBytes(
                Client.EVMExtraArgsV1({gasLimit: 980_000})
            ),
            feeToken: address(linkToken)
        });        

        // Get the fee required to send the message
        uint256 fees = router.getFee(destinationChainSelector, message);

        if (fees > linkToken.balanceOf(address(this)))
            revert NotEnoughBalance(linkToken.balanceOf(address(this)), fees);

        bytes32 messageId;
        // Send the message through the router and store the returned message ID
        messageId = router.ccipSend(destinationChainSelector, message);
        emit MessageSent(messageId);
    }

    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }

    function linkBalace (address account) public view returns (uint256) {
        return linkToken.balanceOf(account);
    }

    function withdrawLINK(
        address beneficiary
    ) public onlyOwner {
        uint256 amount = linkToken.balanceOf(address(this));
        if (amount == 0) revert NothingToWithdraw();
        linkToken.transfer(beneficiary, amount);
    }
}

// Fim


Nome - CrossSourceMinterMumbai address

Christie Cardoso - 0xBa5C8f9ED969e9e37642f5F2209Fa5a6E50657D8
