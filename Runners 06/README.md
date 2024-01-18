Chainlink Bootcamp por Solange Gueiros Jan/2024

Aula 08
https://pad.riseup.net/p/cl-pt-08

https://www.youtube.com/watch?v=vbVDNhGnDRI&ab_channel=Chainlink

Slides
https://docs.google.com/presentation/d/e/2PACX-1vQDouGb19sN3qGO2zDU5VZ-JXhgnJoqVCvykLT6tbOOqX7zXqwqrJxPciBohc66nsoGs_L_UB9QZUQs/pub?start=false&loop=false&delayms=3000

Tarefas
https://forms.gle/k36cuefTTwchdPHw7

Todas as aulas estao aqui
https://lu.ma/ig70bk68

Exercicios do Bootcamp
https://github.com/solangegueiros/chainlink-bootcamp-2024

Faucet
https://workshop-faucet.vercel.app/faucets

https://chain.link/community/advocates
https://chain.link/developers/experts

Link do grupo @Telegram:
https://t.me/+sw2bRlynKwExNzMx

Nome - rede social (se quiser compartilhar)


Christie Cardoso - BH - BR https://www.linkedin.com/in/christie-cardoso-164a28235/

*******************************************************


https://dev.chain.link/products/vrf

https://docs.chain.link/vrf/v2/security
https://docs.chain.link/vrf/v2/best-practices
https://docs.chain.link/vrf/v2/subscription/supported-networks#avalanche-fuji-testnet

Metamask
Fuji

https://remix.ethereum.org/

Icon 5 - DEPLOY & RUN TRANSACTIONS
ENVIRONMENT
Injected provider - Metamask
Custom (43113) network = Fuji

Criar
Runners.sol

// inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

// Deploy this contract on Fuji

import "@openzeppelin/contracts@4.6.0/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts@4.6.0/utils/Counters.sol";
import "@openzeppelin/contracts@4.6.0/utils/Base64.sol";

import "@chainlink/contracts/src/v0.8/interfaces/VRFCoordinatorV2Interface.sol";
import "@chainlink/contracts/src/v0.8/vrf/VRFConsumerBaseV2.sol";

contract Runners is ERC721, ERC721URIStorage, VRFConsumerBaseV2  {
    using Counters for Counters.Counter;
    using Strings for uint256;

    // VRF
    event RequestSent(uint256 requestId, uint32 numWords);
    event RequestFulfilled(uint256 requestId, uint256[] randomWords);

    struct RequestStatus {
        bool fulfilled; // whether the request has been successfully fulfilled
        bool exists; // whether a requestId exists
        uint256[] randomWords;
    }
    mapping(uint256 => RequestStatus) public s_requests; /* requestId --> requestStatus */          

    // Fuji coordinator
    // https://docs.chain.link/docs/vrf/v2/subscription/supported-networks/
    VRFCoordinatorV2Interface COORDINATOR;
    address vrfCoordinator = 0x2eD832Ba664535e5886b75D64C46EB9a228C2610;
    bytes32 keyHash = 0x354d2f95da55398f44b7cff77da56283d9c6c829a4bdf1bbcaf2ad6a4d081f61;
    uint32 callbackGasLimit = 2500000;
    uint16 requestConfirmations = 3;
    uint32 numWords =  1;

    // past requests Ids.
    uint256[] public requestIds;
    uint256 public lastRequestId;
    uint256[] public lastRandomWords;

    // Your subscription ID.
    uint64 public s_subscriptionId;

    //Runners NFT
    Counters.Counter public tokenIdCounter;
    string[] characters_image = [
        "https://ipfs.io/ipfs/QmTgqnhFBMkfT9s8PHKcdXBn1f5bG3Q5hmBaR4U6hoTvb1?filename=Chainlink_Elf.png",
        "https://ipfs.io/ipfs/QmZGQA92ri1jfzSu61JRaNQXYg1bLuM7p8YT83DzFA2KLH?filename=Chainlink_Knight.png",
        "https://ipfs.io/ipfs/QmW1toapYs7M29rzLXTENn3pbvwe8ioikX1PwzACzjfdHP?filename=Chainlink_Orc.png",
        "https://ipfs.io/ipfs/QmPMwQtFpEdKrUjpQJfoTeZS1aVSeuJT6Mof7uV29AcUpF?filename=Chainlink_Witch.png"
    ];
    string[] characters_name = [
        "Elf",
        "Knight",
        "Orc",
        "Witch"
    ];

    struct Runner {
        string name;
        string image;
        uint256 distance;
        uint256 round;
    }
    Runner[] public runners;
    mapping(uint256 => uint256) public request_runner; /* requestId --> tokenId*/


    constructor(uint64 subscriptionId) ERC721("Runners", "RUN")
        VRFConsumerBaseV2(vrfCoordinator)        
    {
        COORDINATOR = VRFCoordinatorV2Interface(vrfCoordinator);
        s_subscriptionId = subscriptionId;
        safeMint(msg.sender,0);
    }

    function safeMint(address to, uint256 charId) public {
        uint8 aux = uint8 (charId);
        require( (aux >= 0) && (aux <= 3), "invalid charId");
        string memory yourCharacterImage = characters_image[charId];

        runners.push(Runner(characters_name[charId], yourCharacterImage, 0, 0));

        uint256 tokenId = tokenIdCounter.current();
        string memory uri = Base64.encode(
            bytes(
                string(
                    abi.encodePacked(
                        '{"name": "', runners[tokenId].name, '",'
                        '"description": "Chainlink runner",',
                        '"image": "', runners[tokenId].image, '",'
                        '"attributes": [',
                            '{"trait_type": "distance",',
                            '"value": ', runners[tokenId].distance.toString(),'}',
                            ',{"trait_type": "round",',
                            '"value": ', runners[tokenId].round.toString(),'}',
                        ']}'
                    )
                )
            )
        );
        // Create token URI
        string memory finalTokenURI = string(
            abi.encodePacked("data:application/json;base64,", uri)
        );
        tokenIdCounter.increment();
        _safeMint(to, tokenId);
        _setTokenURI(tokenId, finalTokenURI);
    }


    function run(uint256 tokenId) external returns (uint256 requestId) {

        require (tokenId < tokenIdCounter.current(), "tokenId not exists");

        // Will revert if subscription is not set and funded.
        requestId = COORDINATOR.requestRandomWords(
            keyHash,
            s_subscriptionId,
            requestConfirmations,
            callbackGasLimit,
            numWords
        );
        s_requests[requestId] = RequestStatus({
            randomWords: new uint256[](0),
            exists: true,
            fulfilled: false
        });
        requestIds.push(requestId);
        lastRequestId = requestId;
        emit RequestSent(requestId, numWords);
        request_runner[requestId] = tokenId;
        return requestId;      
    }

    function fulfillRandomWords(
        uint256 _requestId, /* requestId */
        uint256[] memory _randomWords
    ) internal override {
        require (tokenIdCounter.current() >= 0, "You must mint a NFT");
        require(s_requests[_requestId].exists, "request not found");
        s_requests[_requestId].fulfilled = true;
        s_requests[_requestId].randomWords = _randomWords;
        lastRandomWords = _randomWords;

        uint aux = (lastRandomWords[0] % 10 + 1) * 10;
        uint256 tokenId = request_runner[_requestId];
        runners[tokenId].distance += aux;
        runners[tokenId].round ++;

        string memory uri = Base64.encode(
            bytes(
                string(
                    abi.encodePacked(
                        '{"name": "', runners[tokenId].name, '",'
                        '"description": "Chainlink runner",',
                        '"image": "', runners[tokenId].image, '",'
                        '"attributes": [',
                            '{"trait_type": "distance",',
                            '"value": ', runners[tokenId].distance.toString(),'}',
                            ',{"trait_type": "round",',
                            '"value": ', runners[tokenId].round.toString(),'}',
                        ']}'
                    )
                )
            )
        );
        // Create token URI
        string memory finalTokenURI = string(
            abi.encodePacked("data:application/json;base64,", uri)
        );
        _setTokenURI(tokenId, finalTokenURI);
    }

    function getRequestStatus(
        uint256 _requestId
    ) external view returns (bool fulfilled, uint256[] memory randomWords) {
        require(s_requests[_requestId].exists, "request not found");
        RequestStatus memory request = s_requests[_requestId];
        return (request.fulfilled, request.randomWords);
    }  
    // CONFERE SE ESTÁ CERTO, SOL

    // The following functions are overrides required by Solidity.

    function tokenURI(uint256 tokenId)
        public view override(ERC721, ERC721URIStorage) returns (string memory)
    {
        return super.tokenURI(tokenId);
    }

    function _burn(uint256 tokenId) internal override(ERC721, ERC721URIStorage)
    {
        super._burn(tokenId);
    }
}

// fim

NAO faça deploy ainda!


https://vrf.chain.link/

Connect wallet
Create a subscription on Fuji

Add funds
10 LINKs
OU
Actions => Fund Subscription
10 LINK

Add Consumer
I will do it later

Name - Subscription Id
Sol - https://vrf.chain.link/fuji/985
Christie Cardoso - https://vrf.chain.link/fuji/1001

******************************************************

No Remix
Deploy
Parametro: Subscription ID

********Preciso de avax na rede fuji para dar continuidade na tarefa, por favor
0x7345e617343DbfD47458b9F689863D9e9Dc5BC24

Name - Runners address
Sol - 0x62D0274048A270351a368eFE58d9867A59CE1A33
Christie Cardoso - 0x0c6eB000cAf0De890E60F0727Ea429e29eD9dEFc

**************************************

Va para 
https://vrf.chain.link/fuji/
Na sua subscription

Add consumer
Endereço do smart contract Runners


https://testnets.opensea.io/

Nome - Link da sua coleção
Sol - https://testnets.opensea.io/collection/runners-258
Christie - https://testnets.opensea.io/assets/avalanche-fuji/0x7a871B40354fe0873B61729b2290eABE83459267/0

***********************

No Remix
No Runners
Executa a função 
Run
Parametro 0
= tokenId do NFT criado


LastRequestId

Copia e usa s_requests
Result
0:bool: fulfilled true
1:bool: exists true


runners
Parameter tokenId: 0


Round 1

Nome - Distancia
Sol - 100 🏆

*********************************************

Veja no opensea

Abra o NFT
... Canto superior direito
Refresh Metamdata
Atualiza a pagina

Round 2
Run
Parametro 0

Nome - Distancia
Sol - 140

*****************

SafeMint
To - sua carteira
charId = outro personagem entre 0 e 3

Run
TokenId 1

