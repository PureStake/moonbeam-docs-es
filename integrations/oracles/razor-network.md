---
title: Razor Network
description: Cómo utilizar los datos de solicitud de un Razor Network Oracle en su DApp Moonbeam Ethereum utilizando contratos inteligentes
---
# Oraculo de Razor Network

![Razor Network Moonbeam Diagram](/images/razor/razor-banner.png)

## Introducción {: #introduction } 

Los desarrolladores ahora pueden obtener precios del oráculo de Razor Network utilizando un contrato Bridge implementado en Moonbase Alpha TestNet. Este Bridge actúa como middleware y los eventos que emite son recuperados por la infraestructura de Oracle de Razor Network, enviando precios al contrato de Bridge.

Para acceder a estos feeds de precios, debemos interactuar con la dirección del contrato de Bridge, que se puede encontrar en la siguiente tabla:

|     La red    | |         Dirección del contrato        |
|:--------------:|-|:------------------------------------------:|
| Moonbase Alpha | | 0x53f7660Ea48289B5DA42f1d79Eb9d4F5eB83D3BE |

## Trabajos {: #jobs } 

Cada fuente de datos tiene un ID de trabajo adjunto. Por ejemplo:

|    Identificación del trabajo	    | |    Precio subyacente [USD]  |
|:------------:|-|:--------------------------:|
|       1      | |            ETH             |
|       2      | |            BTC             |
|       3      | |      Acciones de Microsoft      |

Puede verificar las ID de trabajo para cada fuente de datos en el siguiente enlace . Los feeds de precios se actualizan cada 5 minutos. Puede encontrar más información en el [sitio web de documentación de Razor](https://docs.razor.network/).

## Obtener datos del contrato puente {: #get-data-from-bridge-contract } 

Los contratos pueden consultar datos en cadena, como los precios de los tokens, desde el oráculo de Razor Network mediante la implementación de la interfaz del contrato Bridge, que expone las funciones  `getResult` y `getJob` functions.

```solidity
pragma solidity 0.6.11;

interface Razor {
    
    function getResult(uint256 id) external view returns (uint256);
    
    function getJob(uint256 id) external view returns(string memory url, string memory selector, string memory name, bool repeat, uint256 result);
}
```

La primera función, `getResult`, toma el ID de trabajo asociado con la fuente de datos y obtiene el precio. Por ejemplo, si pasamos `1`, recibiremos el precio de la fuente de datos relacionada con el ID del trabajo.

La segunda función, `getJob`, toma el ID de trabajo asociado con la fuente de datos y obtiene la información general sobre la fuente de datos, como el nombre de la fuente de datos, el precio y la URL que se utiliza para obtener los precios.

### Contrato de ejemplo {: #example-contract } 

Hemos implementado el contrato puente en Moonbase Alpha TestNet (en la dirección `{{ networks.moonbase.razor.bridge_address }}`)  para que pueda verificar rápidamente la información proporcionada por el oráculo de Razor Network.

El único requisito es la interfaz Bridge, que define la `getResult` estructura y pone las funciones a disposición del contrato para consultas.


Podemos usar el siguiente `Demo` script. Proporciona varias funciones:

 - fetchPrice: una función de _visualización_ que consulta un solo ID de trabajo. Por ejemplo, para obtener el precio de `ETH` in `USD`,necesitaremos enviar la ID del trabajo. `1`
 - fetchMultiPrices: una función de _visualización_ que consulta múltiples ID de trabajo. Por ejemplo, para obtener el precio de `ETH` and `BTC` y `USD`, necesitaremos enviar las ID de trabajo `[1,2]`
 - savePrice: una función _pública_ que consulta un solo ID de trabajo. Esto envía una transacción y modifica la `price` variable almacenada en el contrato.
 - saveMultiPrices: una función _pública_ ue consulta múltiples ID de trabajo. Por ejemplo, para obtener el precio de `ETH` y `BTC` adentro `USD`, necesitaremos enviar las ID de trabajo `[1,2]`. Esto envía una transacción y modifica la `pricesArr` matriz almacenada en el contrato, que mantendrá el precio de cada par en el mismo orden que se especifica en la entrada.

```solidity
pragma solidity 0.6.11;

interface Razor {
    function getResult(uint256 id) external view returns (uint256);
    function getJob(uint256 id) external view returns(string memory url, string memory selector, string memory name, bool repeat, uint256 result);
}

contract Demo {
    // Interface
    Razor internal razor;
    
    // Variables
    uint256 public price;
    uint256[] public pricesArr;

    constructor(address _bridgeAddress) public {
        razor = Razor(_bridgeAddress); // Bridge Contract Address
                                       // Moonbase Alpha {{ networks.moonbase.razor.bridge_address }}
    }

    function fetchPrice(uint256 _jobID) public view returns (uint256){
        return razor.getResult(_jobID);
    }
    
    function fetchMultiPrices(uint256[] memory jobs) external view returns(uint256[] memory){
        uint256[] memory prices = new uint256[](jobs.length);
        for(uint256 i=0;i<jobs.length;i++){
            prices[i] = razor.getResult(jobs[i]);
        }
        return prices;
    }
    
    function savePrice(uint _jobID) public {
        price = razor.getResult(_jobID);
    }

    function saveMultiPrices(uint[] calldata _jobIDs) public {
        delete pricesArr;
        
        for (uint256 i = 0; i < _jobIDs.length; i++) {
            pricesArr.push(razor.getResult(_jobIDs[i]));
        }

    }
}
```

### Pruébalo en Moonbase Alpha {: #try-it-on-moonbase-alpha } 

La forma más fácil de probar su implementación de Oracle es apuntando la interfaz al contrato Bridge implementado en la dirección `{{ networks.moonbase.razor.bridge_address }}`:

```sol
pragma solidity 0.6.11;

interface Razor {
    function getResult(uint256 id) external view returns (uint256);
    function getJob(uint256 id) external view returns(string memory url, string memory selector, string memory name, bool repeat, uint256 result);
}
```

Con él, tendrás dos funciones de visualización disponibles, muy similares a nuestros ejemplos anteriores:

 - getPrice: proporciona el feed de precios para un único ID de trabajo dado como entrada a la función. Por ejemplo, para obtener el precio de `ETH` en `USD`, necesitaremos enviar la ID del trabajo. `1`
 - getMultiPrices: proporciona la alimentación de precios para varios ID de trabajo proporcionados como una entrada de matriz para la función. Por ejemplo, para obtener el precio de `ETH` y `BTC` en `USD`, necesitaremos enviar las ID de trabajo `[1,2]`

Usemos [Remix](/integrations/remix/) para recuperar el `BTC` precio `USD`.

Después de crear el archivo y compilar el contrato, diríjase a la pestaña "Implementar y ejecutar transacciones", ingrese la dirección del contrato (`{{ networks.moonbase.razor.bridge_address }}`), y haga clic en "En la dirección". Asegúrese de haber configurado el "Entorno" en "Injected Web3" para que esté conectado a Moonbase Alpha (a través del proveedor Web3 de la billetera).

![Razor Remix deploy](/images/razor/razor-demo1.png)

Esto creará una instancia del contrato de demostración con la que puede interactuar. Utilice las funciones `getPrice()` y `getMultiPrices()` para consultar los datos del par correspondiente.

![Razor check price](/images/razor/razor-demo2.png)

## Contáctenos {: #contact-us } 

Si tiene algún comentario sobre la implementación de Razor Network Oracle en su proyecto o cualquier otro tema relacionado con Moonbeam, no dude en comunicarse a través de nuestro [servidor de desarrollo oficial de Discord](https://discord.com/invite/PfpUATX).
