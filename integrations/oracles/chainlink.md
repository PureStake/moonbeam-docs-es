---
title: Chainlink
description: Cómo utilizar los datos de solicitud de un Chainlink Oracle en su Moonbeam Ethereum Dapp utilizando contratos inteligentes o Javascript
---

# Oráculo de Chailink

![Chainlink Moonbeam Banner](/images/chainlink/chainlink-banner.png)

## Introducción

Los desarrolladores ahora pueden usar [la red Oracle descentralizada de Chainlink](https://chain.link/) para obtener datos en Moonbase Alpha TestNet. Este tutorial pasa por dos formas diferentes de usar Chainlink Oracles:

 - [Modelo de solicitud básica](https://docs.chain.link/docs/architecture-request-model), donde el usuario final envía una solicitud a un proveedor de Oracle, que obtiene los datos a través de una API y cumple la solicitud almacenando estos datos en cadena.
 - [Price Feeds](https://docs.chain.link/docs/architecture-decentralized-model), donde los operadores de Oracle actualizan continuamente los datos en un contrato inteligente para que otros contratos inteligentes puedan obtenerlos.

## Modelo de solicitud básico

Antes de comenzar a buscar los datos en sí, es importante comprender los conceptos básicos del "modelo de solicitud básico".

--8<-- 'text/chainlink/chainlink-brm.md'

### El contrato del cliente

El contrato del Cliente es el elemento que inicia la comunicación con Oracle mediante el envío de una solicitud. Como se muestra en el diagrama, llama al método _transferAndCall_ desde el contrato del token LINK, pero se necesita más procesamiento para rastrear la solicitud a Oracle. Para este ejemplo, puede usar el código de [este archivo](/snippets/code/chainlink/Client.sol), e implementarlo en [Remix](/integrations/remix/) para probarlo. Veamos las funciones básicas del contrato:

 - _constructor_: se ejecuta cuando se implementa el contrato. Establece la dirección del token LINK y el propietario del contrato.
 - _requestPrice_: necesita la dirección del contrato de Oracle, el ID del trabajo y los tokens de pago (en LINK) para el cumplidor de la solicitud. Construye la nueva solicitud que se envía utilizando el _sendChainlinkRequestTo_ función de la _ChainlinkClient.sol_ importación
 - _cumplir_: devolución de llamada utilizada por el nodo de Oracle para cumplir con la solicitud almacenando la información consultada en el contrato

```solidity
pragma solidity ^0.6.6;

import "https://github.com/smartcontractkit/chainlink/evm-contracts/src/v0.6/ChainlinkClient.sol";

contract Client is ChainlinkClient {
  //... there is mode code here

  constructor(address _link) public {
    // Set the address for the LINK token for the network
    setChainlinkToken(_link);
    owner = msg.sender;
  }

  function requestPrice(address _oracle, string memory _jobId, uint256 _payment)
    public
    onlyOwner
  {
    // newRequest takes a JobID, a callback address, and callback function as input
    Chainlink.Request memory req = buildChainlinkRequest(stringToBytes32(_jobId), address(this), this.fulfill.selector);
    // Sends the request with the amount of payment specified to the oracle
    sendChainlinkRequestTo(_oracle, req, _payment);
  }

  function fulfill(bytes32 _requestId, uint256 _price)
    public
    recordChainlinkFulfillment(_requestId)
  {
    currentPrice = _price;
  }

  //... there is more code here
}
```

Tenga en cuenta que el contrato del Cliente debe tener un saldo de tokens LINK para poder pagar esta solicitud. Sin embargo, si implementa su configuración, puede establecer el valor LINK en 0 en su `ChainlinkClient.sol` contract,contrato, pero aún necesita tener implementado el contrato del token LINK.

### Pruébalo en Moonbase Alpha

Si desea evitar los obstáculos de implementar todos los contratos, configurar su nodo de Oracle, crear ID de trabajo, etc., lo tenemos cubierto.

Está disponible un contrato de cliente personalizado en Moonbase Alpha que realiza todas las solicitudes a nuestro contrato de Oracle, con un pago de token 0 LINK. Estas solicitudes son cumplidas por un nodo de Oracle que estamos ejecutando. Puede probarlo con el siguiente contrato de interfaz y el contrato de cliente personalizado implementado en `{{ networks.moonbase.chainlink.client_contract }}`:

```solidity
pragma solidity ^0.6.6;

/**
 * @title Simple Interface to interact with Universal Client Contract
 * @notice Client Address {{ networks.moonbase.chainlink.client_contract }}
 */
interface ChainlinkInterface {

  /**
   * @notice Creates a Chainlink request with the job specification ID,
   * @notice and sends it to the Oracle.
   * @notice _oracle The address of the Oracle contract fixed top
   * @notice _payment For this example the PAYMENT is set to zero
   * @param _jobId The job spec ID that we want to call in string format
   */
    function requestPrice(string calldata _jobId) external;

    function currentPrice() external view returns (uint);

}
```

Esto proporciona dos funciones. `requestPrice()` solo necesita el ID de trabajo de los datos que desea consultar. Esta función inicia la cadena de eventos explicada anteriormente. `currentPrice()` es una función de vista que devuelve el último precio almacenado en el contrato.

Actualmente, el nodo de Oracle tiene un conjunto de ID de trabajo para diferentes datos de precios para los siguientes pares:

|  Base / Cotización |     |                 Referencia de ID de trabajo                  |
| :----------: | --- | :-----------------------------------------------: |
|  BTC to USD  |     |  82ceee2897824a0e8b014ed4ed2ab31e  |
|  ETH to USD  |     |  60160cdd0e10489681967e9d7ef4c927  |
|  DOT to USD  |     |  6f6371a780324b90aaf195a0d39c723c  |
|  KSM to USD  |     |  30a1686f657249f4b6ab01e384b2beaa  |
| AAVE to USD  |     |  541b8f7db7374d78b38285ef1b8bfacc  |
| ALGO to USD  |     |  cdb48696e2314133a1dc8ea27922dd24  |
| BAND to USD  |     |  6b0983e0cb6d4aca908b615302a9d672  |
| LINK to USD  |     |  aad8dbdb0c1840ab905728d85117b681  |
| SUSHI to USD |     |  b4b07d0fc218455caaff2223a05ec208  |
|  UNI to USD  |     |  22b567acabdb419abe8136a2bab6ade8  |

Sigamos adelante y usemos el contrato de interfaz con el `BTC to USD` ID de trabajo en [Remix](/integrations/remix/).

Después de crear el archivo y compilar el contrato, diríjase a la pestaña "Implementar y ejecutar transacciones", ingrese la dirección del contrato del Cliente y haga clic en "En la dirección". Asegúrate de haber configurado el "Entorno" en "Injected Web3" para estar conectado a Moonbase Alpha. Esto creará una instancia del contrato del Cliente con la que podrá interactuar. Utilice la función `requestPrice()` para consultar los datos del ID de trabajo correspondiente. Una vez confirmada la transacción, tenemos que esperar hasta que se produzca todo el proceso explicado anteriormente. Podemos consultar el precio utilizando la función de visualización `currentPrice()`.

![Chainlink Basic Request on Moonbase Alpha](/images/chainlink/chainlink-image1.png)

Si hay algún par específico que desea que incluyamos, no dude en comunicarse con nosotros a través de nuestro [servidor de Discord](https://discord.com/invite/PfpUATX).

### Ejecute su contrato con el cliente

Si desea ejecutar su contrato de Cliente pero utiliza nuestro nodo de Oracle, puede hacerlo con la siguiente información:

|  tipo de contrato  |     |                      Habla a                      |
| :-------------: | --- | :-----------------------------------------------: |
| Oracle Contract |     | {{ networks.moonbase.chainlink.oracle_contract }} |
|   LINK Token    |     |  {{ networks.moonbase.chainlink.link_contract }}  |

Recuerde que el pago del token LINK se establece en cero.

### Otras solicitudes

Los oráculos de Chainlink pueden proporcionar tentativamente muchos tipos diferentes de fuentes de datos con el uso de adaptadores externos. Sin embargo, para simplificar, nuestro nodo de Oracle está configurado para entregar solo precios.

Si está interesado en ejecutar su propio nodo Oracle en Moonbeam, visite [esta guía](/node-operators/oracles/node-chainlink/). Además, recomendamos [visitar el sitio de documentación de Chainlink](https://docs.chain.link/docs).

## Feeds de precios

Antes de comenzar a buscar los datos en sí, es importante comprender los conceptos básicos de las fuentes de precios.

En una configuración estándar, cada fuente de precios se actualiza mediante una red de Oracle descentralizada. Cada nodo de Oracle es recompensado por publicar los datos de precios en el contrato del agregador. Sin embargo, la información solo se actualiza si se recibe un número mínimo de respuestas de los nodos de Oracle (durante una ronda de agregación).

El usuario final puede recuperar precios con operaciones de solo lectura a través de un contrato de consumidor, haciendo referencia a la interfaz de agregación correcta (contrato de proxy). El proxy actúa como un software intermedio para proporcionar al consumidor el agregador más actualizado para un feed de precios en particular.

![Price Feed Diagram](/images/chainlink/chainlink-pricefeed.png)

### Pruébalo en Moonbase Alpha

Si desea evitar los obstáculos de implementar todos los contratos, configurar su nodo de Oracle, crear ID de trabajo, etc., lo tenemos cubierto.

Hemos implementado todos los contratos necesarios en Moonbase Alpha para simplificar el proceso de solicitud de precios. En nuestra configuración actual, estamos ejecutando solo un nodo de Oracle que obtiene el precio de una única fuente de API. Los datos de precios se verifican cada minuto y se actualizan en los contratos inteligentes cada hora, a menos que haya una desviación de precios del 1%.

Los datos se encuentran en una serie de contratos inteligentes (uno por cada feed de precios) y se pueden obtener con la siguiente interfaz:

```solidity
pragma solidity ^0.6.6;

interface ConsumerV3Interface {
    /**
     * Returns the latest price
     */
    function getLatestPrice() external view returns (int);

    /**
     * Returns the decimals to offset on the getLatestPrice call
     */
    function decimals() external view returns (uint8);

    /**
     * Returns the description of the underlying price feed aggregator
     */
    function description() external view returns (string memory);
}
```

Esto proporciona tres funciones. `getLatestPrice()` leerá los últimos datos de precios disponibles en el contrato del Agregador a través del Poder. Hemos agregado la `decimals()` función, que devuelve el número de decimales de los datos y la `description()` función, que devuelve una breve descripción del feed de precios disponible en el contrato de agregador que se consulta.

Actualmente, existe un contrato de consumo para los siguientes pares de precios:

|  Base / Cotización  |     |                     Contrato de consumo                    |
| :----------: | --- | :-------------------------------------------------------: |
|  BTC to USD  |     |  0x86f11CffCB1A86Ecb79643FCa1a3C666a61F84Fd  |
|  ETH to USD  |     |  0xE33691Ba3cF532D4025cF0f47679eFe430d4A618  |
|  DOT to USD  |     |  0x235A40b872e543b6238df7Ff55E2D8eCAE80a6bd  |
|  KSM to USD  |     |  0x9Df2E2179ddb4D9197451946104068e08eD3E49F  |
| AAVE to USD  |     |  0xD1e52C81FD72fFc6dA3bF083297E6C37852E93DA  |
| ALGO to USD  |     |  0x0183d1C98442510DD7aaB0E2d09863c47FFF4dF1  |
| BAND to USD  |     |  0x124A3EE742737BC30307d1F82c5406bf79aBa4d6  |
| LINK to USD  |     |  0xC58B34ea686Db2d567835de7AB58fC678b6f186A  |
| SUSHI to USD |     |  0x21B52fFF227cD9F425E817f9E1eaA0eB8cd647e1  |
|  UNI to USD  |     |  0x5Cd72748F94a8597f563D92687A8D2A5074b10E5  |

Sigamos adelante y usemos el contrato de interfaz para obtener el precio del `BTC to USD` uso de [Remix](/integrations/remix/).

Después de crear el archivo y compilar el contrato, diríjase a la pestaña "Implementar y ejecutar transacciones", ingrese la dirección del contrato del consumidor correspondiente BTC to USDy haga clic en "En la dirección". Asegúrate de haber configurado el "Entorno" en "Injected Web3" para estar conectado a Moonbase Alpha.

Esto creará una instancia del contrato del consumidor con la que puede interactuar. Utilice la función `getLatestPrice()` tpara consultar los datos del feed de precios correspondiente.


![Chainlink Price Feeds on Moonbase Alpha](/images/chainlink/chainlink-image2.png)

Tenga en cuenta que para obtener el precio real, debe tener en cuenta los decimales del feed de precios, disponible con el `decimals()` método.

Si hay algún par específico que desea que incluyamos, no dude en comunicarse con nosotros a través de nuestro [servidor de Discord](https://discord.com/invite/PfpUATX).
