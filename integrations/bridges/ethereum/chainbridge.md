---
title: ChainBridge
description: Cómo usar ChainBridge para conectar activos entre Ethereum y Moonbeam usando contratos inteligentes
---
# Puente Ethereum Moonbeam de ChainBridge

![ChainBridge Moonbeam banner](/images/chainbridge/chainbridge-banner.png)

## Introducción {: #introduction } 

Un puente permite que dos cadenas económicamente soberanas y tecnológicamente diferentes se comuniquen entre sí. Pueden ir desde centralizados y confiables hasta descentralizados y con la confianza minimizada. Una de las soluciones disponibles actualmente es [ChainBridge](https://github.com/ChainSafe/ChainBridge#installation),  , un puente de blockchain multidireccional modular construido por [ChainSafe](https://chainsafe.io/). Una implementación ChainBridge ahora está disponible en Moonbeam, que conecta nuestra Moonbase Alpha TestNet y Ethereum's Kovan / Rinkeby TestNets.

Esta guía se divide en dos secciones principales. En la primera parte, explicaremos el flujo de trabajo general del puente. En la segunda parte, veremos un par de ejemplos usando el puente para transferir activos ERC-20 y ERC-721 entre Moonbase Alpha y Kovan / Rinkeby.

 - [Como funciona el puente](/integrations/bridges/ethereum/chainbridge/#how-the-bridge-works)
    - [Definiciones generales](/integrations/bridges/ethereum/chainbridge/#general-definitions)
 - [Usando el puente en Moonbase Alpha](/integrations/bridges/ethereum/chainbridge/#try-it-on-moonbase-alpha)
    - [Transferir tokens ERC-20](/integrations/bridges/ethereum/chainbridge/#erc-20-token-transfer)
    - [Transferir tokens ERC-721](/integrations/bridges/ethereum/chainbridge/#erc-721-token-transfer)
    - [Controlador genérico](/integrations/bridges/ethereum/chainbridge/#generic-handler)
    
## Cómo funciona el puente {: #how-the-bridge-works } 

ChainBridge es, en esencia, un protocolo de paso de mensajes. Los eventos en una cadena de origen se utilizan para enviar un mensaje que se enruta a la cadena de destino. Hay tres roles principales:

 - Oyente: extrae eventos de una cadena fuente y construye un mensaje
 - Enrutador: pasa el mensaje del Oyente al Escritor
 - Redactor: interpreta mensajes y envía transacciones a la cadena de destino.
 
ChainBridge actualmente confía en retransmisores confiables para realizar estos roles. Sin embargo, cuenta con un mecanismo que evita que cualquier retransmisor individual abuse de su poder y maneje mal los fondos. En un nivel alto, los retransmisores crean propuestas en la cadena de destino que se envían para su aprobación por otros retransmisores. La votación de aprobación también ocurre en la cadena de destino, y cada propuesta solo se ejecuta después de que alcanza un cierto umbral de votación.

A ambos lados del puente, hay un conjunto de contratos inteligentes, donde cada uno tiene una función específica:

 - **Contrato puente** — los usuarios y los retransmisores interactúan con este contrato. Delega las llamadas al gestor de contratos para depósitos, inicia una transacción en la cadena de origen y para la ejecución de las propuestas en la cadena de destino.
 - **Contratos de controlador** — valida los parámetros proporcionados por el usuario, creando un registro de depósito / ejecución
 - **Contrato objetivo** — como sugiere el nombre, este es el contrato con el que vamos a interactuar en cada lado del puente
 
### Flujo de trabajo general {: #general-workflow } 

El flujo de trabajo general es el siguiente (de la cadena A a la cadena B):
 
  - Un usuario inicia una transacción con la función _deposit()_ n el contrato puente de la Cadena A. Aquí, el usuario debe ingresar la cadena de destino, el ID del recurso y los datos de _llamada_ (definiciones después del diagrama). Después de algunas comprobaciones, se llama a la función _deposit()_ del contrato de administrador, que ejecuta la llamada correspondiente del contrato de destino.
  - Después de que se ejecuta la función del contrato de destino en la Cadena A, el contrato puente emite un evento de _Deposito_ que contiene los datos necesarios para ejecutarse en la Cadena B. Esto se denomina propuesta. Cada propuesta puede tener cinco estados (inactivo, activo, aprobado, ejecutado y cancelado).
  - Los retransmisores siempre están escuchando a ambos lados de la cadena. Una vez que un relevista recoge el evento, inicia una votación sobre la propuesta, lo que ocurre en el contrato puente en la Cadena B. Esto establece el estado de la propuesta de inactivo a activo.
  - Los retransmisores deben votar sobre la propuesta. Cada vez que un repetidor vota, el contrato puente emite un evento que actualiza su estado. Una vez que se alcanza un umbral, el estado cambia de activo a pasado. Luego, un relevador ejecuta la propuesta en la Cadena B a través del contrato puente.
  - Después de algunas comprobaciones, el puente ejecuta la propuesta en el contrato de destino a través del contrato de administrador en la Cadena B. Se emite otro evento, que actualiza el estado de la propuesta de pasada a ejecutada.

Este flujo de trabajo se resume en el siguiente diagrama:

![ChainBridge Moonbeam diagram](/images/chainbridge/chainbridge-diagram.png)

Los dos contratos de destino en cada lado del puente están vinculados mediante una serie de registros en el contrato de manipulador correspondiente a través del contrato del puente. Actualmente, estos registros solo pueden ser realizados por el administrador del contrato puente.

### Definiciones generales {: #general-definitions } 

Aquí hemos reunido una lista de conceptos aplicables a la implementación de ChainBridge (de la Cadena A a la Cadena B):

 - **ChainBridge Chain ID** — no debe confundirse con el ID de cadena de la red. Es un identificador de red único que utiliza el protocolo para cada cadena. Puede ser diferente del ID de cadena real de la propia red. Por ejemplo, para Moonbase Alpha y Rinkeby, hemos establecido el ID de la cadena ChainBridge en 43 y 4 respectivamente (Kovan se estableció en 42)
 - **ID de recurso** — es una palabra de 32 bytes destinada a identificar de forma única un activo en un entorno de cadena cruzada. Tenga en cuenta que el byte menos significativo está reservado para el chainId, por lo que tendríamos 31 bytes en total para representar un activo de una cadena en nuestro puente. Por ejemplo, esto puede expresar que tokenX en la Cadena A es equivalente a tokenY en la Cadena B
 - **Calldata** — es el parámetro requerido para el controlador que incluye la información necesaria para ejecutar la propuesta en la Cadena B. La serialización exacta se define para cada controlador. Puedes encontrar más información [aquí.](https://chainbridge.chainsafe.io/chains/ethereum/#erc20-erc721-handlers)

## Pruébalo en Moonbase Alpha {: #try-it-on-moonbase-alpha } 

Hemos configurado un relé con la implementación de ChainBridge, que está conectado a nuestra Moonbase Alpha TestNet y las redes de prueba Rinkeby y Kovan de Ethereum.

ChainSafe tiene [controladores preprogramados](https://chainbridge.chainsafe.io/chains/ethereum/#handler-contracts) específicos para las interfaces ERC-20 y ERC-721, y estos controladores se utilizan para transferir tokens ERC-20 y ERC-721 entre cadenas. En términos generales, esto es solo reducir el diagrama de propósito general que hemos descrito antes, por lo que el controlador funciona solo con las funciones específicas del token, como _bloquear / quemar_, y _menta / desbloquear_. 

Esta guía repasará dos ejemplos diferentes del uso del puente para transferir tokens entre cadenas. Acumulará fichas lunares ERC-20S y ERC-721 y las transferirá a través del puente de Moonbase Alpha a Kovan. Los mismos pasos se pueden seguir y aplicar a Rinkeby. Para interactuar con Moonbase Alpha y Kovan / Rinkeby, necesitará la siguiente información:

```
# Kovan/Rinkeby - Moonbase Alpha bridge contract address:
    {{ networks.moonbase.chainbridge.bridge_address }}

 # Kovan/Rinkeby - Moonbase Alpha ERC-20 handler contract:
    {{ networks.moonbase.chainbridge.ERC20_handler }}
   
# Kovan/Rinkeby - Moonbase Alpha ERC-721 handler contract:
    {{ networks.moonbase.chainbridge.ERC721_handler }}
```

!!! nota
    El contrato puente, el contrato de manipulador ERC-20 y las direcciones del contrato de manipulador ERC-721 enumeradas anteriormente son aplicables tanto para Kovan como para Rinkeby.

### Transferencia de token ERC-20 {: #erc20-token-transfer } 

Los tokens ERC-20 que quieran moverse a través del puente deben ser registrados por los retransmisores en el contrato de manipulador. Por lo tanto, para probar el puente, implementamos un token ERC-20 (ERC20S) donde cualquier usuario puede acuñar 5 tokens:

```
# Kovan/Rinkeby - Moonbase Alpha custom ERC-20 sample token:
    {{ networks.moonbase.chainbridge.ERC20S }}
```

De manera similar, interactuar directamente con el contrato de Bridge y llamar a la función `deposit()` on los parámetros correctos puede resultar intimidante. Para facilitar el proceso de uso del puente, hemos creado un contrato de puente modificado, que genera las entradas necesarias para la `deposit()` función:

```
# Kovan/Rinkeby - Moonbase Alpha custom bridge contract:
    {{ networks.moonbase.chainbridge.bridge_address }}
```


En términos simples, el contrato modificado utilizado para iniciar la transferencia tiene el _ID de cadena_ y _ID de recurso_ predefinidos para este ejemplo. Por lo tanto, construye el objeto _calldata_ a partir de la entrada del usuario, que es solo la dirección del destinatario y el valor que se enviará.

El flujo de trabajo general para este ejemplo se puede ver en este diagrama:

![ChainBridge ERC20 workflow](/images/chainbridge/chainbridge-erc20.png)

Para probar el puente con este token ERC-20 de muestra, debemos realizar los siguientes pasos (independientemente de la dirección de la transferencia):
 
 - Fichas de menta en la cadena de origen (esto aprueba el contrato del manejador de origen como gastador por la cantidad acuñada)
 - Utilice el contrato puente modificado para enviar tokens desde la Cadena de origen a la Cadena de destino
 - Espere hasta que se complete el proceso
 - Aprobar el contrato del manejador como gastador para devolver los tokens
 - Utilice el contrato puente modificado para enviar tokens desde la Cadena de destino a la Cadena de origen

!!! nota
    Recuerde que los tokens se transferirán solo si el contrato del controlador tiene suficiente margen para gastar tokens en nombre del propietario. Si el proceso falla, verifique la asignación.

Enviemos algunos tokens ERC20S de **Moonbase Alpha** a **Kovan**. Si desea probarlo con Rinkeby, los pasos y direcciones son los mismos. Para ello, usaremos [Remix](/integrations/remix/). Primero, podemos usar la siguiente interfaz para interactuar con este contrato y acuñar los tokens:

```solidity
pragma solidity ^0.8.1;

/**
    Interface for the Custom ERC20 Token contract for ChainBridge implementation
    Kovan/Rinkeby - Moonbase Alpha ERC-20 Address : 
        {{ networks.moonbase.chainbridge.ERC20S }}
*/

interface ICustomERC20 {

    // Mint 5 ERC20S Tokens
    function mintTokens() external;

    // Get Token Name
    function name() external view returns (string memory);
    
    /** 
        Increase allowance to Handler
        Kovan/Rinkeby - Moonbase Alpha ERC-20 Handler:
           {{ networks.moonbase.chainbridge.ERC20_handler}}
    */
    function increaseAllowance(address spender, uint256 addedValue) external returns (bool);
    
    // Get allowance
    function allowance(address owner, address spender) external view returns (uint256);
}
```

Tenga en cuenta que la función de acuñación del contrato de token ERC-20 también se modificó para aprobar el contrato de administrador correspondiente como gastador al acuñar tokens.

Después de agregar el contrato Custom ERC20 a Remix y compilarlo, los siguientes pasos son acuñar tokens ERC20S:

1. Vaya a la página ** Implementar y ejecutar transacciones** en Remix
2. Seleccione Web3 inyectado en el menú desplegable **Entorno**
3. Cargue la dirección del contrato del token ERC-20 personalizado y haga clic en **En la dirección**
4. Llame a la `mintTokens()` función y firme la transacción. 
5. Una vez confirmada la transacción, debería haber recibido 5 tokens ERC20S. Puede verificar su saldo agregando el token a [MetaMask](/integrations/wallets/metamask/).

![ChainBridge ERC20 mint Tokens](/images/chainbridge/chainbridge-image1.png)

Una vez que tengamos los tokens, podemos proceder a enviarlos por el puente a la cadena objetivo. En este caso, recuerda que lo hacemos desde **Moonbase Alpha** hasta **Kovan**. Hay una única interfaz que le permitirá transferir tokens ERC20S y ERC721M. Para este ejemplo, utilizará la `sendERC20SToken()`función para iniciar la transferencia de sus tokens ERC20S acuñados:

```solidity
pragma solidity 0.8.1;

/**
    Simple Interface to interact with bridge to transfer the ERC20S and ERC721M tokens
    Kovan/Rinkeby - Moonbase Alpha Bridge Address: 
        {{ networks.moonbase.chainbridge.bridge_address }}
 */

interface IBridge {

    /**
     * Calls the `deposit` function of the Chainbridge Bridge contract for the custom ERC20 (ERC20Sample) 
     * by building the requested bytes object from: the recipient, the specified amount and the destination
     * chainId.
     * @notice Use the destination `eth_chainId`.
     */
    function sendERC20SToken(uint256 destinationChainID, address recipient, uint256 amount) external;
    
    /**
     * Calls the `deposit` function for the custom ERC721 (ERC721Moon) that is only mintable in the
     * MOON side of the bridge. It builds the bytes object requested by the method from: the recipient,
     * the specified token ID and the destination chainId.
     * @notice Use the destination `eth_chainId`.
     */
    function sendERC721MoonToken(uint256 destinationChainID, address recipient, uint256 tokenId) external;
}
```

Después de agregar el contrato Bridge a Remix y compilarlo, para enviar tokens ERC20s a través del puente, deberá:

1. Cargue la dirección del contrato puente y haga clic en **En la dirección**
2. Para llamar a la `sendERC20SToken()` función, introduzca el ID de cadena de destino (Para este ejemplo estamos utilizando Kovan: `42`)
3. Ingrese la dirección del destinatario en el otro lado del puente
4. Suma el monto a transferir en WEI
5. Haga clic en **tramite** y luego MetaMask debe pop-up que le pedirá que firmar la transacción.

Una vez que se confirma la transacción, el proceso puede tardar alrededor de 3 minutos en completarse, después de lo cual debería haber recibido los tokens en Kovan.


![ChainBridge ERC20 send Tokens](/images/chainbridge/chainbridge-image2.png)

Puede verificar su saldo agregando el token a [MetaMask](/integrations/wallets/metamask/) y conectándolo a la red de destino, en nuestro caso Kovan.van.

![ChainBridge ERC20 balance](/images/chainbridge/chainbridge-image3.png)

Recuerda que también puedes acuñar tokens ERC20S en Kovan y enviarlos a Moonbase Alpha. Para aprobar a un gastador o aumentar su asignación, puede utilizar la `increaseAllowance()` función de la interfaz proporcionada. Para verificar la asignación del contrato de administrador en el contrato de token ERC20, puede usar la `allowance()` función de la interfaz.

!!! nota
    Los tokens se transferirán solo si el contrato del controlador tiene suficiente asignación para gastar tokens en nombre del propietario. Si el proceso falla, verifique la asignación.

### Transferencia de token ERC-721 {: #erc721-token-transfer } 

Al igual que en nuestro ejemplo anterior, los contratos de tokens ERC-721 deben ser registrados por los retransmisores para permitir la transferencia a través del puente. Por lo tanto, hemos personalizado un contrato de token ERC-721 para que cualquier usuario pueda acuñar un token para probar el puente. Sin embargo, como cada token no es fungible y, en consecuencia, es único, la función de menta solo está disponible en el contrato de token de la cadena de origen y no en el contrato de destino. En otras palabras, los tokens ERC-721M solo se pueden acuñar en Moonbase Alpha y luego transferirse a Rinkeby o Kovan. El siguiente diagrama explica el flujo de trabajo de este ejemplo, donde es importante resaltar que se mantienen el ID del token y los metadatos.

![ChainBridge ERC721 workflow](/images/chainbridge/chainbridge-erc721.png)

Para acuñar tokens en Moonbase Alpha (llamado ERC721Moon con el símbolo ERC721M) y enviarlos de ida y vuelta a Kovan / Rinkeby, necesita la siguiente dirección:

```
# Kovan/Rinkeby - Moonbase Alpha ERC-721 Moon tokens (ERC721M),
# Mint function in Moonbase Alpha: 
    {{ networks.moonbase.chainbridge.ERC721M }}
```

En lugar de interactuar con el contrato del puente y llamar a la función `deposit()`, hemos modificado el contrato del puente para facilitar el proceso de uso del puente (la misma dirección que en el ejemplo anterior):

```
# Kovan/Rinkeby - Moonbase Alpha custom bridge contract:
    {{ networks.moonbase.chainbridge.bridge_address }}
```

En términos simples, el contrato modificado puente utilizado para iniciar la transferencia va a crear el _chainID_ y de _resourceID_ para este ejemplo basado en el ID de cadena de destino que usted proporciona. Por lo tanto, crea el objeto _calldata_ a partir de la entrada del usuario, que es solo la dirección del destinatario y el ID del token que se enviará.

Enviemos un token ERC721M de **Moonbase Alpha** a **Kovan**. Para eso usaremos [Remix](/integrations/remix/). TLa siguiente interfaz se puede utilizar para interactuar con el contrato ERC721M de origen y acuñar los tokens. La `tokenOfOwnerByIndex()`unción también se puede utilizar para verificar los ID de token que pertenecen a una dirección específica, pasando la dirección y el índice a consultar (cada ID de token se almacena como un elemento de matriz asociado a la dirección):

```solidity
pragma solidity ^0.8.1;

/**
    Interface for the Custom ERC721 Token contract for ChainBridge implementation:
    Kovan/Rinkeby - Moonbase Alpha:
        ERC721Moon: {{ networks.moonbase.chainbridge.ERC721M }}

    ERC721Moon tokens are only mintable on Moonbase Alpha
*/

interface ICustomERC721 {
    
    // Mint 1 ERC-721 Token
    function mintToken() external returns (uint256);
    
    // Query tokens owned by Owner
    function tokenOfOwnerByIndex(address _owner, uint256 _index) external view returns (uint256);

    // Get Token Name
    function name() external view returns (string memory);
    
    // Get Token URI
    function tokenURI(uint256 tokenId) external view returns (string memory);
    
    /**
        Set Approval for Handler 
        Kovan/Rinkeby - Moonbase Alpha ERC-721 Handler:
           {{ networks.moonbase.chainbridge.ERC721_handler }}
    */
    function approve(address to, uint256 tokenId) external;
    
    // Check the address approved for a specific token ID
    function getApproved(uint256 tokenId) external view returns (address);
}
```

Tenga en cuenta que la función de acuñación del contrato de tokens ERC-721 también se modificó para aprobar el contrato de administrador correspondiente como gastador al acuñar tokens.

Después de agregar el contrato a Remix y compilarlo, a continuación, queremos acuñar un token ERC721M:

1. Vaya a la página **Implementar y ejecutar transacciones** en Remix
2. Seleccione Web3 inyectado en el menú desplegable **Entorno**
3. Cargue la dirección del contrato del token ERC721M personalizado y haga clic en **En la dirección**
4. Llame a la `mintTokens()` función y firme la transacción. 
5. Una vez confirmada la transacción, debería haber recibido un token ERC721M. Puede verificar su saldo agregando el token a [MetaMask](/integrations/wallets/metamask/).

![ChainBridge ERC721 mint Tokens](/images/chainbridge/chainbridge-image4.png) 

La siguiente interfaz le permite utilizar la `sendERC721MoonToken()` función para iniciar la transferencia de tokens acuñados originalmente en Moonbase Alpha (ERC721M).

```solidity
pragma solidity 0.8.1;

/**
    Simple Interface to interact with bridge to transfer the ERC20S and ERC721M tokens
    Kovan/Rinkeby - Moonbase Alpha Bridge Address: 
        {{ networks.moonbase.chainbridge.bridge_address }}
 */

interface IBridge {

    /**
     * Calls the `deposit` function of the Chainbridge Bridge contract for the custom ERC20 (ERC20Sample) 
     * by building the requested bytes object from: the recipient, the specified amount and the destination
     * chainId.
     * @notice Use the destination `eth_chainId`.
     */
    function sendERC20SToken(uint256 destinationChainID, address recipient, uint256 amount) external;
    
    /**
     * Calls the `deposit` function for the custom ERC721 (ERC721Moon) that is only mintable in the
     * MOON side of the bridge. It builds the bytes object requested by the method from: the recipient,
     * the specified token ID and the destination chainId.
     * @notice Use the destination `eth_chainId`.
     */
    function sendERC721MoonToken(uint256 destinationChainID, address recipient, uint256 tokenId) external;
}
```

Ahora puede proceder a enviar el token ERC721M a través del puente a la cadena de destino. En este caso, recuerda que lo haremos desde Moonbase Alpha hasta Kovan. Para transferir el token ERC721M a través del puente:

1. Cargue la dirección del contrato puente y haga clic en **En la dirección**
2. Llame a la `sendERC721MoonToken()` función para iniciar la transferencia de tokens ERC721M acuñada originalmente en la Base Lunar Alfa proporcionando el ID de cadena de destino (Para este ejemplo estamos utilizando Kovan: `42`)
3. Ingrese la dirección del destinatario en el otro lado del puente
4. Agregue el ID del token para transferir
5. Haga clic en **tramite** y luego MetaMask debe pop-up que le pedirá que firmar la transacción.

Una vez que se confirma la transacción, el proceso puede tardar alrededor de 3 minutos en completarse, después de lo cual debería haber recibido el mismo ID de token en Kovan.

![ChainBridge ERC721 send Token](/images/chainbridge/chainbridge-image5.png)

Puede verificar su saldo agregando el token a [MetaMask](/integrations/wallets/metamask/) y conectándolo a la red de destino, en nuestro caso Kovan.

![ChainBridge ERC721 balance](/images/chainbridge/chainbridge-image6.png)

Recuerde que los tokens ERC721M solo se pueden minar en Moonbase Alpha y luego estarán disponibles para enviar y recibir a Kovan o Rinkeby. Es importante verificar siempre la asignación proporcionada al contrato de manipulador en el contrato de token ERC721 correspondiente. Puede aprobar el contrato de administrador para enviar tokens en su nombre utilizando la `approve()`función proporcionada en la interfaz. Puede verificar la aprobación de cada uno de sus ID de token con la `getApproved()` función.

!!! nota
    Los tokens se transferirán solo si se aprueba el contrato de administrador para transferir tokens en nombre del propietario. Si el proceso falla, verifique la aprobación.

### Manejador genérico {: #generic-handler } 

El Generic Handler ofrece la posibilidad de ejecutar una función en la cadena A y crear una propuesta para ejecutar otra función en la cadena B (similar al diagrama de flujo de trabajo general). Esto proporciona una forma convincente de conectar dos cadenas de bloques independientes.

La dirección del controlador genérico es:
```
{{ networks.moonbase.chainbridge.generic_handler }}
```

Si está interesado en implementar esta funcionalidad, puede comunicarse con nosotros directamente a través de nuestro [servidor Discord](https://discord.com/invite/PfpUATX). Estaremos encantados de discutir esta implementación.

### Interfaz de usuario de puente de cadena alfa de Moonbase {: #moonbase-alpha-chainbridge-ui } 

Si quieres jugar con la transferencia de tokens ERC20S de Moonbase Alpha a Kovan o Rinkeby sin tener que configurar los contratos en Remix, puedes consultar nuestra [ interfaz de usuario Moonbase Alpha ChainBridge.](https://moonbase-chainbridge.netlify.app/transfer).

