---
title: Web3.js
description: Siga este tutorial para aprender a usar la biblioteca de JavaScript Ethereum Web3 para implementar contratos inteligentes de Solidity en Moonbeam.
---
# Biblioteca JavaScript de Web3.js

![Intro diagram](/images/integrations/integrations-web3js-banner.png)

## Introducción {: #introduction } 

[Web3.js](https://web3js.readthedocs.io/) es un conjunto de bibliotecas que permiten a los desarrolladores interactuar con los nodos de Ethereum utilizando los protocolos HTTP, IPC o WebSocket con JavaScript. Moonbeam tiene una API similar a Ethereum disponible que es totalmente compatible con las invocaciones JSON RPC de estilo Ethereum. Por lo tanto, los desarrolladores pueden aprovechar esta compatibilidad y usar la biblioteca web3.js para interactuar con un nodo Moonbeam como si lo estuvieran haciendo en Ethereum.

## Configurar Web3.js con Moonbeam {: #setup-web3js-with-moonbeam } 

Para comenzar con la biblioteca web3.js, primero debemos instalarla usando el siguiente comando:

```
npm install web3
```

Una vez hecho esto, la configuración más simple para comenzar a usar la biblioteca y sus métodos es la siguiente:

```js
const Web3 = require('web3');

//Create web3 instance
const web3 = new Web3('RPC_URL');
```

Según la red a la que desee conectarse, puede establecer `RPC_URL` los siguientes valores:

 - Nodo de desarrollo Moonbeam:  `http://127.0.0.1:9933`
 - Moonbase Alpha TestNet: `https://rpc.testnet.moonbeam.network`

## Tutoriales paso a paso {: #step-by-step-tutorials } 

Si está interesado en una guía paso a paso más detallada, consulte nuestros tutoriales específicos sobre el uso de web3.js en Moonbeam para [enviar una transacción](/getting-started/local-node/send-transaction/) o [implementar un contrato](/getting-started/local-node/deploy-contract/).

