---
title: Band Protocol
description: Cómo utilizar los datos de solicitud de un protocolo de banda Oracle en su DApp Moonbeam Ethereum usando contratos inteligentes o javascript
---
# Protocolo de banda Oracle


![Band Protocol Moonbeam Diagram](/images/band/band-banner.png)

## Introducción

Los desarrolladores tienen dos formas de obtener precios de la infraestructura Oracle de Band. Por un lado, pueden usar los contratos inteligentes de Band en Moonbeam. Al hacerlo, acceden a los datos que están en la cadena y se actualizan a intervalos regulares o cuando el deslizamiento del precio es mayor que una cantidad objetivo (diferente para cada token). Por otro lado, los desarrolladores pueden usar la biblioteca auxiliar de Javascript, que usa un punto final de API para obtener los datos usando funciones similares a las de los contratos inteligentes, pero esta implementación omite la blockchain por completo. Esto puede ser útil si su interfaz DApp necesita acceso directo a los datos.

La dirección del contrato del agregador se puede encontrar en la siguiente tabla:

|     La red    | |         Dirección del contrato del agregador       |
|:--------------:|-|:------------------------------------------:|
| Moonbase Alpha | | 0xDA7a001b254CD22e46d3eAB04d937489c93174C3 |

## Token admitido
Las consultas de precios con cualquier denominación están disponibles siempre que se admitan los símbolos base y de cotización (_base_/_quote_). Por ejemplo:

 - `BTC/USD`
 - `BTC/ETH`
 - `ETH/EUR`

En el momento de escribir este artículo, la lista de símbolos admitidos se puede encontrar siguiendo [este enlace](https://data.bandprotocol.com). Hay más de 146 pares de precios disponibles para consultar.

## Consultar precios

Como se indicó anteriormente, los desarrolladores pueden aprovechar dos métodos para consultar precios desde el oráculo de Band:

 - Contrato inteligente de Band en Moonbeam (implementado en Moonbase Alpha TestNet por ahora)
 - Biblioteca auxiliar de JavaScript

## Obtener datos mediante contratos inteligentes

Los contratos pueden consultar datos en cadena, como precios de tokens, desde el oráculo de Band mediante la implementación de la interfaz del `StdReference` contrato, que expone las funciones `getReferenceData` y `getReferenceDataBulk`.

La primera función, `getReferenceData`, toma dos cadenas (la base y el símbolo de cotización) como entradas. La función consulta el `StdReference` contrato para conocer las últimas tarifas disponibles para esos dos tokens. Devuelve una `ReferenceData` estructura.

La `ReferenceData` estructura tiene los siguientes elementos:

 - Tasa: la tasa de cambio en términos de _base/cotización_. El valor devuelto se multiplica por 10<sup>18</sup>
 - Última base actualizada: la última vez que se actualizó el precio base (desde la época de UNIX)
 - Última cotización actualizada: la última vez que se actualizó el precio cotizado (desde la época de UNIX)
 
```
struct ReferenceData {
   uint256 rate; 
   uint256 lastUpdatedBase; 
   uint256 lastUpdatedQuote;
}
```

La segunda función, `getReferenceDataBulk`, toma información como matrices de datos. Por ejemplo, si pasamos `['BTC','BTC','ETH']`como base y `['USD','ETH','EUR']` como cita, la `ReferenceData` matriz devuelta contiene la información sobre los siguientes pares:

 - `BTC/USD`
 - `BTC/ETH`
 - `ETH/EUR`

### Contrato de ejemplo

El siguiente código de contrato inteligente proporciona algunos ejemplos simples del `StdReference` y la `getReferenceData` función; estos no están destinados a la producción. La `IStdReference.sol` interfaz define la estructura de ReferenceData y las funciones disponibles para realizar las consultas.

```sol
pragma solidity 0.6.11;
pragma experimental ABIEncoderV2;

interface IStdReference {
    /// A structure returned whenever someone requests for standard reference data.
    struct ReferenceData {
        uint256 rate; // base/quote exchange rate, multiplied by 1e18.
        uint256 lastUpdatedBase; // UNIX epoch of the last time when base price gets updated.
        uint256 lastUpdatedQuote; // UNIX epoch of the last time when quote price gets updated.
    }

    /// Returns the price data for the given base/quote pair. Revert if not available.
    function getReferenceData(string memory _base, string memory _quote)
        external
        view
        returns (ReferenceData memory);

    /// Similar to getReferenceData, but with multiple base/quote pairs at once.
    function getReferenceDataBulk(string[] memory _bases, string[] memory _quotes)
        external
        view
        returns (ReferenceData[] memory);
}
```
A continuación, podemos usar el siguiente `DemoOracle` script. Proporciona cuatro funciones: 

 - getPrice: una función de _vista_ ue consulta una sola base. En este ejemplo, el precio de `BTC` cotizado en `USD`
 - getMultiPrices: una función de _vista_ que consulta múltiples bases. En este ejemplo, el precio de `BTC` y `ETH`, ambos cotizados en `USD`
 - savePrice: una función _pública_ que consulta el par _base/cotización_ pair. Cada elemento se proporciona como cadenas independientes, por ejemplo `_base = "BTC", _quotes = "USD"`.  Esto envía una transacción y modifica la `price` variable almacenada en el contrato.
 - saveMultiPrices: una función _pública_ que consulta cada par _base/cotización_ pair. Cada elemento se proporciona como una matriz de cadenas. Por ejemplo `_bases = ["BTC","ETH"], _quotes = ["USD","USD"]`. Esto envía una transacción y modifica la `prices` matriz almacenada en el contrato, que mantendrá el precio de cada par en el mismo orden que se especifica en la entrada.

Cuando se implementa, la función de constructor necesita la dirección del contrato de agregador para la red de destino.

```sol
pragma solidity 0.6.11;
pragma experimental ABIEncoderV2;

import "./IStdReference.sol";

contract DemoOracle {
    IStdReference ref;
    
    uint256 public price;
    uint256[] public pricesArr;

    constructor(IStdReference _ref) public {
        ref = _ref; // Aggregator Contract Address
                    // Moonbase Alpha 0xDA7a001b254CD22e46d3eAB04d937489c93174C3

    }

    function getPrice(string memory _base, string memory _quote) external view returns (uint256){
        IStdReference.ReferenceData memory data = ref.getReferenceData(_base,_quote);
        return data.rate;
    }

    function getMultiPrices(string[] memory _bases, string[] memory _quotes) external view returns (uint256[] memory){
        IStdReference.ReferenceData[] memory data = ref.getReferenceDataBulk(_bases,_quotes);

        uint256 len = _bases.length;
        uint256[] memory prices = new uint256[](len);
        for (uint256 i = 0; i < len; i++) {
            prices[i] = data[i].rate;
        }

        return prices;
    }
    
    function savePrice(string memory _base, string memory _quote) external {
        IStdReference.ReferenceData memory data = ref.getReferenceData(_base,_quote);
        price = data.rate;
    }
    
    function saveMultiPrices(
        string[] memory _bases,
        string[] memory _quotes
    ) public {
        require(_bases.length == _quotes.length, "BAD_INPUT_LENGTH");
        uint256 len = _bases.length;
        IStdReference.ReferenceData[] memory data = ref.getReferenceDataBulk(_bases,_quotes);
        delete pricesArr;
        for (uint256 i = 0; i < len; i++) {
            pricesArr.push(data[i].rate);
        }
        
    }
}
```

### Pruébelo en Moonbase Alpha

Hemos implementado un contrato disponible en Moonbase Alpha TestNet (en la dirección `0xf15c870344c1c02f5939a5C4926b7cDb90dEc655`) para que pueda verificar fácilmente la información proporcionada por el oráculo de Band Protocol. Para hacerlo, necesita el siguiente contrato de interfaz:

```sol
pragma solidity 0.6.11;
pragma experimental ABIEncoderV2;

interface TestInterface {
    function getPrice(string memory _base, string memory _quote) external view returns (uint256);

    function getMultiPrices(string[] memory _bases, string[] memory _quotes) external view returns (uint256[] memory);
}
```

Con él, tendrá dos funciones de visualización disponibles, muy similares a nuestros ejemplos anteriores:

 - getPrice: getPrice: proporciona el feed de precios para un solo par base / cotización que se proporciona como entrada a la función, es decir, "BTC", "USD"
 - getMultiPrices: proporciona el feed de precios para varios pares de base / cotización que se proporcionan como entrada a la función, es decir, ["BTC", "ETH", "ETH"], ["USD", "USD", "EUR "]

Por ejemplo, usando [Remix](/integrations/remix/),podemos consultar fácilmente el `BTC/USD` par de precios usando esta interfaz.

Después de crear el archivo y compilar el contrato, diríjase a la pestaña "Implementar y ejecutar transacciones", ingrese la dirección del contrato (`0xf15c870344c1c02f5939a5C4926b7cDb90dEc655`) y haga clic en "En la dirección". Asegúrate de haber configurado el "Entorno" en "Injected Web3" para estar conectado a Moonbase Alpha.

![Band Protocol Remix deploy](/images/band/band-demo1.png)

Esto creará una instancia del contrato de demostración con la que puede interactuar. Utilice las funciones `getPrice()` y `getMultiPrices()` para consultar los datos del par correspondiente.

![Band Protocol Remix check price](/images/band/band-demo2.png)

## Biblioteca auxiliar de Javascript BandChain.js

La biblioteca auxiliar también admite una `getReferenceData` función similar . Para comenzar, es necesario instalar la biblioteca:

```
npm install @bandprotocol/bandchain.js

```

La biblioteca proporciona una función de constructor que requiere un punto final al que apuntar. Esto devuelve una instancia que luego habilita todos los métodos necesarios, como la `getReferenceData` función. Al solicitar información, la función acepta una matriz en la que cada elemento es el par _base/cotización_ necesario. Por ejemplo:

```
getReferenceData(['BTC/USD', 'BTC/ETH', 'ETH/EUR'])

```

Luego, devuelve un objeto de matriz con la siguiente estructura:

```
[
  {
    pair: 'BTC/USD',
    rate: rate,
    updated: { base: lastUpdatedBase, quote: lastUpdatedQuote}
  },
  {
    pair: 'BTC/ETH',
    rate: rate,
    updated: { base: lastUpdatedBase, quote: lastUpdatedQuote}
  },
  {
    pair: 'ETH/EUR',
    rate: rate,
    updated: { base: lastUpdatedBase, quote: lastUpdatedQuote}
  }
]
```
Donde `lastUpdatedBase` y `lastUpdatedQuote` son la última vez que se actualizaron los precios base y de cotización respectivamente (desde la época de UNIX).

### Ejemplo de uso

El siguiente script de Javascript proporciona un ejemplo simple de la `getReferenceData` función.

```js
const BandChain = require('@bandprotocol/bandchain.js');

const queryData = async () => {
  const endpoint = 'https://poa-api.bandchain.org';

  const bandchain = new BandChain(endpoint);
  const dataQuery = await bandchain.getReferenceData(['BTC/USD', 'BTC/ETH', 'ETH/EUR']);
  console.log(dataQuery);
};

queryData();
```

Podemos ejecutar este código con un nodo, y el siguiente `dataQuery` resultado debería verse así:

![Band Protocol JavaScript Library](/images/band/band-console.png)

Tenga en cuenta que, en comparación con la solicitud realizada a través de contratos inteligentes, el resultado se proporciona directamente en las unidades correctas.
