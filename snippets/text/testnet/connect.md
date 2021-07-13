### HTTPS DNS

Para conectarse a Moonbase Alpha a través de HTTPS, simplemente apunte a su proveedor al siguiente DNS de RPC:

```
https://rpc.testnet.moonbeam.network
```

Para la biblioteca web3.js, puede crear una instancia local de Web3 y configurar el proveedor para que se conecte a Moonbase Alpha (se admiten tanto HTTP como WS):

```js
const Web3 = require('web3'); //Load Web3 library
.
.
.
//Create local Web3 instance - set Moonbase Alpha as provider
const web3 = new Web3('https://rpc.testnet.moonbeam.network'); 
```
Para la biblioteca ethers.js, defina el proveedor mediante el uso `ethers.providers.StaticJsonRpcProvider(providerURL, {object})` y la configuración de la URL del proveedor en Moonbase Alpha:

```js
const ethers = require('ethers');


const providerURL = 'https://rpc.testnet.moonbeam.network';
// Define Provider
const provider = new ethers.providers.StaticJsonRpcProvider(providerURL, {
    chainId: 1287,
    name: 'moonbase-alphanet'
});
```

Cualquier billetera Ethereum debería poder generar una dirección válida para Moonbeam (por ejemplo, [MetaMask](https://metamask.io/)).

### WSS DNS

Para las conexiones WebSocket, puede utilizar el siguiente DNS:

```
wss://wss.testnet.moonbeam.network
```

### ID de cadena

Para la base lunar Alfa TestNet el ID de cadena es: `1287`.

### Relay Chain

Para conectarse a la relay chain de Moonbase Alpha, manejada por PureStake, puedes utilizar el siguiente punto de acceso WS:

```
wss://wss-relay.testnet.moonbeam.network
```