
Chainlink Bootcamp por Solange Gueiros Jan/2024

Aula 09 - Chainlink Functions para acessar dados off-chain
https://pad.riseup.net/p/cl-pt-09

inicio https://www.youtube.com/watch?v=tRDVuKskfv4&ab_channel=Chainlink

continuação https://www.youtube.com/watch?v=XZW00WXaJQU&t=8s&ab_channel=Chainlink

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
cllworkshop0101
(sol) se puder coloca mais avax no faucets por favor, nao consegui finalizar o segundo passo por falta de faucets.
https://faucets.chain.link/fuji
You have reached your rate limit. Please try again after 2024-01-28T01:45:04.685Z

https://chain.link/community/advocates
https://chain.link/developers/experts

Link do grupo @Telegram:
https://t.me/+sw2bRlynKwExNzMx

Data final para exercícios:
  ->  Jan 31

Música do dia: O sol - Vitor Kley
https://www.youtube.com/watch?v=YVJijQIualA

"Ô, sol
Vê se não esquece
E me ilumina
Preciso de você aqui" ;D ☀️🌞

****************************************************

Nome - Cidade, País - Rede social se quiser
👑 Sol - solangegueiros 👑
Christie Cardoso - BH - BR


****************************************************

https://blog.chain.link/ways-to-use-chainlink-functions/

https://usechainlinkfunctions.com/
https://usechainlinkfunctions.com/posts/aviation-flight-status
https://www.run-functions.app/

https://dev.chain.link/products/functions
https://docs.chain.link/chainlink-functions

https://functions.chain.link/playground
Fill with example code
Star Wars Characters

Argument
1

Run Code

Resultado
Luke Skywalker

Exemplos
https://swapi.dev/api/people/1
https://swapi.dev/api/people/4


Para os Devs mais experientes
https://www.npmjs.com/package/@chainlink/functions-toolkit

Metamask
Fuji


https://functions.chain.link/fuji/
Connect Wallet

Create Subscription

Your email address

Subscription name
Chainlink Bootcamp 2024 PT

Add funds
5 LINK

ou 
Actions
Fund subscription

Add consumer
I'll do it later


Nome - link functions subscription id
Sol - https://functions.chain.link/fuji/3119
Christie Cardoso - https://functions.chain.link/fuji/3909


***************************************

https://docs.chain.link/chainlink-functions/getting-started

https://docs.chain.link/chainlink-functions/getting-started#deploy-a-functions-consumer-contract-on-polygon-mumbai
Abrir no Remix
Exemplo na Polygon Mumbai

Metamask
Fuji

https://remix.ethereum.org/
Icon 5 - DEPLOY & RUN TRANSACTIONS
ENVIRONMENT
Injected provider - Metamask
Custom (43113) network = Fuji

Criar
GettingStartedFunctionsConsumer.sol

// início

// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

// Deploy on Fuji

import {FunctionsClient} from "@chainlink/contracts/src/v0.8/functions/dev/v1_0_0/FunctionsClient.sol";
import {ConfirmedOwner} from "@chainlink/contracts/src/v0.8/shared/access/ConfirmedOwner.sol";
import {FunctionsRequest} from "@chainlink/contracts/src/v0.8/functions/dev/v1_0_0/libraries/FunctionsRequest.sol";

/**
 * Request testnet LINK and ETH here: https://faucets.chain.link/
 * Find information on LINK Token Contracts and get the latest ETH and LINK faucets here: https://docs.chain.link/resources/link-token-contracts/
 */

/**
 * @title GettingStartedFunctionsConsumer
 * @notice This is an example contract to show how to make HTTP requests using Chainlink
 * @dev This contract uses hardcoded values and should not be used in production.
 */
contract GettingStartedFunctionsConsumer is FunctionsClient, ConfirmedOwner {
    using FunctionsRequest for FunctionsRequest.Request;

    // State variables to store the last request ID, response, and error
    bytes32 public s_lastRequestId;
    bytes public s_lastResponse;
    bytes public s_lastError;

    // Custom error type
    error UnexpectedRequestID(bytes32 requestId);

    // Event to log responses
    event Response(
        bytes32 indexed requestId,
        string character,
        bytes response,
        bytes err
    );

    // Hardcoded for Fuji
    // Supported networks https://docs.chain.link/chainlink-functions/supported-networks
    address router = 0xA9d587a00A31A52Ed70D6026794a8FC5E2F5dCb0;
    bytes32 donID =
        0x66756e2d6176616c616e6368652d66756a692d31000000000000000000000000;

    //Callback gas limit
    uint32 gasLimit = 300000;

    // JavaScript source code
    // Fetch character name from the Star Wars API.
    // Documentation: https://swapi.dev/documentation#people
    string source =
        "const characterId = args[0];"
        "const apiResponse = await Functions.makeHttpRequest({"
        "url: `https://swapi.dev/api/people/${characterId}/`"
        "});"
        "if (apiResponse.error) {"
        "throw Error('Request failed');"
        "}"
        "const { data } = apiResponse;"
        "return Functions.encodeString(data.name);";

    // State variable to store the returned character information
    string public character;

    /**
     * @notice Initializes the contract with the Chainlink router address and sets the contract owner
     */
    constructor() FunctionsClient(router) ConfirmedOwner(msg.sender) {}

    /**
     * @notice Sends an HTTP request for character information
     * @param subscriptionId The ID for the Chainlink subscription
     * @param args The arguments to pass to the HTTP request
     * @return requestId The ID of the request
     */
    function sendRequest(
        uint64 subscriptionId,
        string[] calldata args
    ) external onlyOwner returns (bytes32 requestId) {
        FunctionsRequest.Request memory req;
        req.initializeRequestForInlineJavaScript(source); // Initialize the request with JS code
        if (args.length > 0) req.setArgs(args); // Set the arguments for the request

        // Send the request and store the request ID
        s_lastRequestId = _sendRequest(
            req.encodeCBOR(),
            subscriptionId,
            gasLimit,
            donID
        );

        return s_lastRequestId;
    }

    /**
     * @notice Callback function for fulfilling a request
     * @param requestId The ID of the request to fulfill
     * @param response The HTTP response data
     * @param err Any errors from the Functions request
     */
    function fulfillRequest(
        bytes32 requestId,
        bytes memory response,
        bytes memory err
    ) internal override {
        if (s_lastRequestId != requestId) {
            revert UnexpectedRequestID(requestId); // Check if request IDs match
        }
        // Update the contract's state variables with the response and any errors
        s_lastResponse = response;
        character = string(response);
        s_lastError = err;

        // Emit an event to log the response
        emit Response(requestId, character, s_lastResponse, s_lastError);
    }
}

// fim

Deploy

Nome - GettingStartedFunctionsConsumer address

Sol - 0x9519C4cac95c033C14D9E8FD0500Dad09a105287
Christie Cardoso - 0x7B7b7f8b86F3c65951d05dbd82f07c1E48dbc22B


***************************************************************************************

Va para sua conta em 
https://functions.chain.link/fuji/

Add consumer
Endereço do smart contract GettingStartedFunctionsConsumer 

No Remix
GettingStartedFunctionsConsumer 

sendRequest
expandir

subscriptionId
args
[1]

Transact


Converter Bytes para String
https://web3-type-converter.onbrn.com/

**************************************************************************************

Exercicio 2
Temperatura de uma cidade

Api
https://wttr.in/

Exemplo
https://wttr.in/Sao-Paulo?format=3
https://wttr.in/Rio-De-Janeiro?format=3

Testar no Playground
https://functions.chain.link/playground/7dce0d62-c132-40cb-ac72-e30532e599f2

Sao-Paulo
Rio-De-Janeiro


No Remix

Criar
TemperatureFunctions.sol

// Inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

// Deploy on Fuji

import {FunctionsClient} from "@chainlink/contracts/src/v0.8/functions/dev/v1_0_0/FunctionsClient.sol";
import {FunctionsRequest} from "@chainlink/contracts/src/v0.8/functions/dev/v1_0_0/libraries/FunctionsRequest.sol";

contract TemperatureFunctions is FunctionsClient {
    using FunctionsRequest for FunctionsRequest.Request;

    // State variables to store the last request ID, response, and error
    bytes32 public s_lastRequestId;
    bytes public s_lastResponse;
    bytes public s_lastError;

    // Custom error type
    error UnexpectedRequestID(bytes32 requestId);

    // Event to log responses
    event Response(
        bytes32 indexed requestId,
        string temperature,
        bytes response,
        bytes err
    );

    // Hardcoded for Fuji
    // Supported networks https://docs.chain.link/chainlink-functions/supported-networks
    address router = 0xA9d587a00A31A52Ed70D6026794a8FC5E2F5dCb0;
    bytes32 donID =
        0x66756e2d6176616c616e6368652d66756a692d31000000000000000000000000;

    //Callback gas limit
    uint32 gasLimit = 300000;

    // Your subscription ID.
    uint64 public s_subscriptionId;

    // JavaScript source code    
    string public source =
        "const city = args[0];"
        "const apiResponse = await Functions.makeHttpRequest({"
        "url: `https://wttr.in/${city}?format=3`,"
        "responseType: 'text'"
        "});"
        "if (apiResponse.error) {"
        "throw Error('Request failed');"
        "}"
        "const { data } = apiResponse;"
        "return Functions.encodeString(data);";
    string public lastCity;    
    string public lastTemperature;

    constructor(uint64 subscriptionId) FunctionsClient(router) {
        s_subscriptionId = subscriptionId;
    }

    function getTemperature(
        string memory _city
    ) external returns (bytes32 requestId) {

        string[] memory args = new string[](1);
        args[0] = _city;

        FunctionsRequest.Request memory req;
        req.initializeRequestForInlineJavaScript(source); // Initialize the request with JS code
        if (args.length > 0) req.setArgs(args); // Set the arguments for the request

        // Send the request and store the request ID
        s_lastRequestId = _sendRequest(
            req.encodeCBOR(),
            s_subscriptionId,
            gasLimit,
            donID
        );
        lastCity = _city;

        return s_lastRequestId;
    }

    /**
     * @notice Callback function for fulfilling a request
     * @param requestId The ID of the request to fulfill
     * @param response The HTTP response data
     * @param err Any errors from the Functions request
     */
    function fulfillRequest(
        bytes32 requestId,
        bytes memory response,
        bytes memory err
    ) internal override {
        if (s_lastRequestId != requestId) {
            revert UnexpectedRequestID(requestId); // Check if request IDs match
        }        
        s_lastError = err;

        // Update the contract's state variables with the response and any errors
        s_lastResponse = response;
        lastTemperature = string(response);

        // Emit an event to log the response
        emit Response(requestId, lastTemperature, s_lastResponse, s_lastError);
    }
}


//Fim

Parametro
SubscriptionId => Id da sua conta no Chainlink Functions


Nome - TemperatureFunctions

Sol - 0x2BabC242fA1CcD3f8c287C18844e31E7893aae5b
Christie Cardoso - 0xe8188f82b6be0517B0b40eecCf8755CDde20EF08

********************************************
Sol eu inclui aqui embaixo o que tinham apagado (1h28min) pra ajudar quem assiste depois (Cristina)

Va para sua conta 
https://functions.chain.link/fuji

Add consumer
Endereco do TemperatureFunctions

getTemperature
cidades - exemplos
Sao-Paulo
Rio-De-Janeiro


https://www.metric-conversions.org/temperature/fahrenheit-to-celsius.htm

*******************************************************************


No Remix

Criar
WeatherFunctions.sol

Parametro
SubscriptionId => Id da sua conta no Chainlink Functions

https://raw.githubusercontent.com/solangegueiros/chainlink-bootcamp-2024/main/WeatherFunctions.sol

// Inicio

// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

// Deploy on Fuji

import {FunctionsClient} from "@chainlink/contracts/src/v0.8/functions/dev/v1_0_0/FunctionsClient.sol";
import {FunctionsRequest} from "@chainlink/contracts/src/v0.8/functions/dev/v1_0_0/libraries/FunctionsRequest.sol";

contract WeatherFunctions is FunctionsClient {
    using FunctionsRequest for FunctionsRequest.Request;

    // State variables to store the last request ID, response, and error
    bytes32 public lastRequestId;
    bytes public lastResponse;
    bytes public lastError;

    struct RequestStatus {
        bool fulfilled; // whether the request has been successfully fulfilled
        bool exists; // whether a requestId exists
        bytes response;
        bytes err;
    }
    mapping(bytes32 => RequestStatus) public requests; /* requestId --> requestStatus */          
    bytes32[] public requestIds;

    // Event to log responses
    event Response(
        bytes32 indexed requestId,
        string temperature,
        bytes response,
        bytes err
    );

    // Hardcoded for Fuji
    // Supported networks https://docs.chain.link/chainlink-functions/supported-networks
    address router = 0xA9d587a00A31A52Ed70D6026794a8FC5E2F5dCb0;
    bytes32 donID =
        0x66756e2d6176616c616e6368652d66756a692d31000000000000000000000000;

    //Callback gas limit
    uint32 gasLimit = 300000;

    // Your subscription ID.
    uint64 public subscriptionId;

    // JavaScript source code    
    string public source =
        "const city = args[0];"
        "const apiResponse = await Functions.makeHttpRequest({"
        "url: `https://wttr.in/${city}?format=3`,"
        "responseType: 'text'"
        "});"
        "if (apiResponse.error) {"
        "throw Error('Request failed');"
        "}"
        "const { data } = apiResponse;"
        "return Functions.encodeString(data);";
    string public lastCity;    
    string public lastTemperature;
    address public lastSender;

    struct CityStruct {
        address sender;
        bool exists;
        string name;
        string temperature;
    }
    CityStruct[] public cities;
    mapping(string => uint256) public cityIndex;
    mapping(bytes32 => string) public request_city; /* requestId --> city*/

    constructor(uint64 functionsSubscriptionId) FunctionsClient(router) {
        subscriptionId = functionsSubscriptionId;
    }

    function getTemperature(
        string memory _city
    ) external returns (bytes32 requestId) {

        string[] memory args = new string[](1);
        args[0] = _city;

        FunctionsRequest.Request memory req;
        req.initializeRequestForInlineJavaScript(source); // Initialize the request with JS code
        if (args.length > 0) req.setArgs(args); // Set the arguments for the request

        // Send the request and store the request ID
        lastRequestId = _sendRequest(
            req.encodeCBOR(),
            subscriptionId,
            gasLimit,
            donID
        );
        lastCity = _city;
        request_city[lastRequestId] = _city;

        CityStruct memory auxCityStruct = CityStruct({
            sender: msg.sender,
            exists: true,
            name: _city,
            temperature: ""            
        });
        cities.push(auxCityStruct);
        cityIndex[_city] = cities.length-1;

        requests[lastRequestId] = RequestStatus({
            exists: true,
            fulfilled: false,
            response: "",
            err: ""
        });
        requestIds.push(lastRequestId);

        return lastRequestId;
    }

    /**
     * @notice Callback function for fulfilling a request
     * @param requestId The ID of the request to fulfill
     * @param response The HTTP response data
     * @param err Any errors from the Functions request
     */
    function fulfillRequest(
        bytes32 requestId,
        bytes memory response,
        bytes memory err
    ) internal override {
        require(requests[requestId].exists, "request not found");

        lastError = err;
        lastResponse = response;

        requests[requestId].fulfilled = true;
        requests[requestId].response = response;
        requests[requestId].err = err;

        string memory auxCity = request_city[requestId];

        lastTemperature = string(response);

        require(cities[cityIndex[auxCity]].exists, "city not found");
        cities[cityIndex[auxCity]].temperature = lastTemperature;

        // Emit an event to log the response
        emit Response(requestId, lastTemperature, lastResponse, lastError);
    }

    function listCities() public view returns (CityStruct[] memory) {
        return cities;
    }

    function getCity(string memory city) public view returns (CityStruct memory) {
        return cities[cityIndex[city]];
    }

}


// Fim

Deploy

Parametro
SubscriptionId => Id da sua conta no Chainlink Functions


Nome - WeatherFunctions

Sol - 0xE34e7d3c132371865709052f738de184182a8D3
Christie Cardoso - 0xdD35F4611409ba0Ca129351cF530DE591086c490


********************************************



Chainlink Bootcamp por Solange Gueiros Jan/2024

Aula 10 - Conectando o mundo utilizando a plataforma Chainlink
https://pad.riseup.net/p/cl-pt-10

Slides
https://docs.google.com/presentation/d/e/2PACX-1vQDouGb19sN3qGO2zDU5VZ-JXhgnJoqVCvykLT6tbOOqX7zXqwqrJxPciBohc66nsoGs_L_UB9QZUQs/pub?start=false&loop=false&delayms=3000

Tarefas
https://forms.gle/k36cuefTTwchdPHw7
Data limite para fazer as tarefas e receber o certificado em Fevereiro: 31/Jan/2024 

Todas as aulas estao aqui
https://lu.ma/ig70bk68

Exercicios do Bootcamp
https://github.com/solangegueiros/chainlink-bootcamp-2024

Faucet
https://workshop-faucet.vercel.app/faucets
cllworkshop0101

Suporte Bootcamp
https://chain.link/discord
bootcamp channel

Link do grupo @Telegram:
https://t.me/+sw2bRlynKwExNzMx

Data final para exercícios:
  ->  Jan 31

https://chain.link/community/advocates
https://chain.link/developers/experts

https://chainlinklabs.com/careers
https://cryptojobslist.com/ 
https://cryptocurrencyjobs.co/ 
https://crypto.jobs/ https://web3.career/

Jeff Linkedin:
    https://www.linkedin.com/in/jeffbeltrao/

Javier Linkedin:
    https://www.linkedin.com/in/javier-salomon/


********************************************

Nome - Profissão

Christie Cardoso - Desenvolvedor Web 

*************************************

Metamask
Fuji

Remix
Icon 5 - DEPLOY & RUN TRANSACTIONS
ENVIRONMENT
Injected provider - Metamask
Custom (43113) network = Fuji

Create
WeatherFunctions.sol

https://raw.githubusercontent.com/solangegueiros/chainlink-bootcamp-2024/main/WeatherFunctions.sol

ou

// Begin


// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

// Deploy on Fuji

import {FunctionsClient} from "@chainlink/contracts/src/v0.8/functions/dev/v1_0_0/FunctionsClient.sol";
import {FunctionsRequest} from "@chainlink/contracts/src/v0.8/functions/dev/v1_0_0/libraries/FunctionsRequest.sol";

contract WeatherFunctions is FunctionsClient {
    using FunctionsRequest for FunctionsRequest.Request;

    // State variables to store the last request ID, response, and error
    bytes32 public lastRequestId;
    bytes public lastResponse;
    bytes public lastError;

    struct RequestStatus {
        bool fulfilled; // whether the request has been successfully fulfilled
        bool exists; // whether a requestId exists
        bytes response;
        bytes err;
    }
    mapping(bytes32 => RequestStatus) public requests; /* requestId --> requestStatus */          
    bytes32[] public requestIds;

    // Event to log responses
    event Response(
        bytes32 indexed requestId,
        string temperature,
        bytes response,
        bytes err
    );

    // Hardcoded for Fuji
    // Supported networks https://docs.chain.link/chainlink-functions/supported-networks
    address router = 0xA9d587a00A31A52Ed70D6026794a8FC5E2F5dCb0;
    bytes32 donID =
        0x66756e2d6176616c616e6368652d66756a692d31000000000000000000000000;

    //Callback gas limit
    uint32 gasLimit = 300000;

    // Your subscription ID.
    uint64 public subscriptionId;

    // JavaScript source code
    string public source =
        "const city = args[0];"
        "const apiResponse = await Functions.makeHttpRequest({"
        "url: `https://wttr.in/${city}?format=3&m`,"
        "responseType: 'text'"
        "});"
        "if (apiResponse.error) {"
        "throw Error('Request failed');"
        "}"
        "const { data } = apiResponse;"
        "return Functions.encodeString(data);";
    string public lastCity;    
    string public lastTemperature;
    address public lastSender;

    struct CityStruct {
        address sender;
        uint timestamp;
        string name;
        string temperature;
    }
    CityStruct[] public cities;
    mapping(string => uint256) public cityIndex;
    mapping(bytes32 => string) public request_city; /* requestId --> city*/

    constructor(uint64 functionsSubscriptionId) FunctionsClient(router) {
        subscriptionId = functionsSubscriptionId;      
    }

    function getTemperature(
        string memory _city
    ) external returns (bytes32 requestId) {

        string[] memory args = new string[](1);
        args[0] = _city;

        FunctionsRequest.Request memory req;
        req.initializeRequestForInlineJavaScript(source); // Initialize the request with JS code
        if (args.length > 0) req.setArgs(args); // Set the arguments for the request

        // Send the request and store the request ID
        lastRequestId = _sendRequest(
            req.encodeCBOR(),
            subscriptionId,
            gasLimit,
            donID
        );
        lastCity = _city;
        request_city[lastRequestId] = _city;

        CityStruct memory auxCityStruct = CityStruct({
            sender: msg.sender,
            timestamp: 0,
            name: _city,
            temperature: ""            
        });
        cities.push(auxCityStruct);
        cityIndex[_city] = cities.length-1;

        requests[lastRequestId] = RequestStatus({
            exists: true,
            fulfilled: false,
            response: "",
            err: ""
        });
        requestIds.push(lastRequestId);

        return lastRequestId;
    }

    // Receive the weather in the city requested
    function fulfillRequest(
        bytes32 requestId,
        bytes memory response,
        bytes memory err
    ) internal override {
        require(requests[requestId].exists, "request not found");

        lastError = err;
        lastResponse = response;

        requests[requestId].fulfilled = true;
        requests[requestId].response = response;
        requests[requestId].err = err;

        string memory auxCity = request_city[requestId];
        lastTemperature = string(response);
        cities[cityIndex[auxCity]].temperature = lastTemperature;
        cities[cityIndex[auxCity]].timestamp = block.timestamp;

        // Emit an event to log the response
        emit Response(requestId, lastTemperature, lastResponse, lastError);
    }

    function getCity(string memory city) public view returns (CityStruct memory) {
        return cities[cityIndex[city]];
    }

    function listAllCities() public view returns (CityStruct[] memory) {
        return cities;
    }

    function listCities(uint start, uint end) public view returns (CityStruct[] memory) {
        if (end > cities.length)
            end = cities.length-1;
        require (start <= end, "start must <= end");
        uint cityCount = end - start + 1;
        CityStruct[] memory citiesAux = new CityStruct[](cityCount);

        for (uint i = start; i < (end + 1); i++) {
            citiesAux[i-start] = cities[i];
        }
        return citiesAux;
    }

}


// End

Deploy

Parameter
Function SubscriptionID do functions.chain.link/fuji


Test it on Playground
https://functions.chain.link/playground/17e3c09f-0730-420b-9325-f49f357911a0


No Functions functions.chain.link/fuji
Add a consumer 
SOL- 0xd8110865f9714EEFAF4b96dd71C7f45ACa0158D6
Ricardo Hildebrand - 0x7DB60a0Aead2b63642


Name - WeatherFunctions address
Sol - 9b902fbd16Bb66A22cc03b
Christie - 0xdD35F4611409ba0Ca129351cF530DE591086c490

************************************************************

https://functions.chain.link/fuji/
If you has less than 3 LINK
Actions => Add funds

getTemperature

Sao-Paulo
Rio-De-Janeiro
Salvador


Ultima tarefa!
executar getTemperature no smart contract da Sol
At Address (abaixo do deploy)
Sol contract address
0xd8110865f9714EEFAF4b96dd71C7f45ACa0158D6


Objetivo
Ter todos os estudantes no meu contrato
:)



Resultado

Christie Cardoso - 0x063A07958dFaDc2c140A6Ec2f4380B280254aeE3,1706721983,Contagem,Contagem: ⛅️ +26°C


