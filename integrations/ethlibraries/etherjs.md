---
title: Ethers.js
description: Siga este tutorial para aprender a usar la biblioteca Ethereum EtherJS para desplegar contratos inteligentes Solidity en Moonbeam.
---
# Biblioteca de JavaScript de Ethers.js

![Intro diagram](/images/integrations/integrations-ethersjs-banner.png)

## Introducción

La biblioteca [ethers.js](https://docs.ethers.io/) lproporciona un conjunto de herramientas para interactuar con los nodos Ethereum con JavaScript, similar a web3.js. Moonbeam tiene una API similar a Ethereum disponible que es totalmente compatible con las invocaciones JSON RPC de estilo Ethereum. Por lo tanto, los desarrolladores pueden aprovechar esta compatibilidad y usar la biblioteca ethers.js para interactuar con un nodo Moonbeam como si lo estuvieran haciendo en Ethereum. Puede leer más sobre ethers.js en esta [publicación de blog](https://medium.com/l4-media/announcing-ethers-js-a-web3-alternative-6f134fdd06f3).

## Configurar Ethers.js con Moonbeam

Para comenzar con la biblioteca ethers.js, instálela con el siguiente comando:

```
npm install ethers
```

Una vez hecho esto, la configuración más simple para comenzar a usar la biblioteca y sus métodos es la siguiente:

```js
const ethers = require('ethers');

// Variables definition
const privKey = '0xPRIVKEY';

// Define Provider
const provider = new ethers.providers.StaticJsonRpcProvider('RPC_URL', {
    chainId: ChainId,
    name: 'NETWORK_NAME'
});

// Create Wallet
let wallet = new ethers.Wallet(privKey, provider);
```

Hay diferentes métodos disponibles en el interior `provider` y `wallet`. Según la red a la que desee conectarse, puede establecer `RPC_URL` los siguientes valores:

Nodo de desarrollo Moonbeam: 
 - RPC_URL: `http://127.0.0.1:9933`"
 - ChainId: `1281`
 - NETWORK_NAME: `moonbeam-development`
 
Moonbase Alpha TestNet: 
 - RPC_URL: `https://rpc.testnet.moonbeam.network`
 - ChainId: `1287`
 - NETWORK_NAME: `moonbase-alpha`

## Tutoriales paso a paso

Si está interesado en una guía paso a paso más detallada, puede ir a nuestros tutoriales específicos sobre el uso de ethers.js en Moonbeam para [enviar una transacción](/getting-started/local-node/send-transaction/) o [implementar un contrato](/getting-started/local-node/deploy-contract/).
