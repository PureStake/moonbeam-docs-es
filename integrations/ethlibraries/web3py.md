---
title: Web3.py
description: Siga este tutorial para aprender a usar la biblioteca Python de Ethereum Web3 para implementar contratos inteligentes de Solidity en Moonbeam.
---
# Biblioteca de Python Web3.py

![Intro diagram](/images/builders/tools/eth-libraries/web3py-banner.png)

## Introducción {: #introduction } 

[Web3.py](https://web3py.readthedocs.io/) es un conjunto de bibliotecas que permiten a los desarrolladores interactuar con los nodos de Ethereum utilizando los protocolos HTTP, IPC o WebSocket con Python. Moonbeam tiene una API similar a Ethereum disponible que es totalmente compatible con las invocaciones JSON RPC de estilo Ethereum. Por lo tanto, los desarrolladores pueden aprovechar esta compatibilidad y usar la biblioteca web3.py para interactuar con un nodo Moonbeam como si lo estuvieran haciendo en Ethereum.

## Configurar Web3.py con Moonbeam {: #setup-web3py-with-moonbeam } 

Para comenzar con la biblioteca web3.py, instálela usando el siguiente comando:

```
pip3 install web3
```

Una vez hecho esto, la configuración más simple para comenzar a usar la biblioteca y sus métodos es la siguiente:

```py
from web3 import Web3

web3 = Web3(Web3.HTTPProvider('RPC_URL'))
```

Según la red a la que desee conectarse, puede establecer RPC_URLlos siguientes valores:

 - Nodo de desarrollo Moonbeam: `http://127.0.0.1:9933`
 - Moonbase Alpha TestNet: `https://rpc.testnet.moonbeam.network`

## Tutoriales paso a paso {: #step-by-step-tutorials } 

Si está interesado en una guía paso a paso más detallada, consulte nuestros tutoriales específicos sobre el uso de web3.py en Moonbeam para [enviar una transacción](/getting-started/local-node/send-transaction/) o [implementar un contrato](/getting-started/local-node/deploy-contract/).

