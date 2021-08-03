---
title: Exploradores de Bloques
description: Una descripción general de los exploradores de bloques disponibles actualmente que pueden usarse para navegar por las capas de Substrato y Ethereum de Moonbeam TestNet.
---
# Exploradores de bloques

![Explorer Banner](/images/explorers/explorers-banner.png)

## Introducción {: #introduction } 

Los exploradores de bloques pueden considerarse motores de búsqueda para la blockchain. Permiten a los usuarios buscar información como saldos, contratos y transacciones. Los exploradores de bloques más avanzados incluso ofrecen capacidades de indexación, que les permiten proporcionar un conjunto completo de información, como tokens ERC20 en la red. Incluso podrían ofrecer servicios de API para acceder a él a través de servicios externos.

Moonbeam proporciona dos conjuntos diferentes de exploradores: uno para consultar la API de Ethereum y otro para la API de sustrato.

!!! nota
    Si está utilizando Brave Browser y su explorador no se conecta a la instancia de Moonbeam a la que lo está apuntando, intente deshabilitar Brave Shield.

## API de Ethereum {: #ethereum-api } 

### Expedición (nodo de desarrollo - TestNet) {: #expedition-dev-node-testnet } 

En [este enlace](https://moonbeam-explorer.netlify.app/) se puede encontrar una versión del Explorador de [expediciones](https://github.com/etclabscore/expedition) con el tema Moonbeam. 

De forma predeterminada, el explorador está conectado a Moonbase Alpha TestNet. Sin embargo, puede conectarlo siguiendo los siguientes pasos:

 1. Haga clic en el ícono de ajustes en la esquina superior derecha
 2. Seleccione "Desarrollo" si tiene un nodo en ejecución `http://localhost:9933` (ubicación RPC predeterminada de un nodo Moonbeam que se ejecuta con `--dev` bandera). También puedes volver a "Moonbase Alpha"
 3. En el caso de que desee conectarse a una URL de RPC específica, seleccione "RPC personalizado" e ingrese la URL. Por ejemplo, `http://localhost:9937`

![Expedition Explorer](/images/explorers/explorers-images-1.png)

### Blockscout (TestNet) {: #blockscout-testnet } 

Blockscout proporciona una interfaz fácil de usar para que los usuarios inspeccionen y confirmen transacciones en cadenas de bloques EVM, incluido Moonbeam. Le permite buscar transacciones, ver cuentas y saldos, y verificar contratos inteligentes. Puede encontrar más información en su [sitio de documentación](https://docs.blockscout.com/).

Como características principales, Blockscout ofrece:

 - Desarrollo de código abierto, lo que significa que todo el código está abierto a la comunidad para explorarlo y mejorarlo. Puedes encontrar el código [aquí](https://github.com/blockscout/blockscout)
 - Seguimiento de transacciones en tiempo real
 - Interacción de contrato inteligente
 - Compatibilidad con token ERC20 y ERC721, que enumera todos los contratos de token disponibles en una interfaz amigable
 - API con todas las funciones con GraphQL, donde los usuarios pueden probar las llamadas a la API directamente desde una interfaz web
 
En [este enlace](https://moonbase-blockscout.testnet.moonbeam.network/) se puede encontrar una instancia de Blockscout que se ejecuta contra Moonbase Alpha TestNet.

![Blockscout Explorer](/images/explorers/explorers-images-2.png)

## API de sustrato {: #substrate-api } 

### PolkadotJS (Nodo de desarrollo - TestNet) {: #polkadotjs-dev-node-testnet } 

Polkadot JS Apps utiliza el punto final de WebSocket para interactuar con la red. Para conectarlo a un nodo de desarrollo Moonbeam, puede seguir los pasos de [este tutorial](/getting-started/local-node/setting-up-a-node/#connecting-polkadot-js-apps-to-a-local-moonbeam-node). El puerto predeterminado para esto es `9944`.

![Polkadot JS Local Node](/images/explorers/explorers-images-3.png)

Para ver e interactuar con la capa de sustrato de Moonbase Alpha, vaya a [esta URL](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/explorer). Estas son las aplicaciones Polkadot JS que apuntan a TestNet. Puede encontrar más información en [esta página](/integrations/wallets/polkadotjs/).

![Polkadot JS Moonbase Alpha](/images/explorers/explorers-images-4.png)

### Subscan {: #subscan } 

Subscan proporciona capacidades de explorador de blockchain para cadenas basadas en sustratos. Es capaz de analizar módulos estándar o personalizados. Por ejemplo, esto es útil para mostrar información sobre los pallets (o módulos) de Staking, Governance y EVM. El código es de código abierto y se puede encontrar [aquí](https://github.com/itering/subscan-essentials).

En [este enlace](https://moonbase.subscan.io/) se puede encontrar una instancia de Subscan que se ejecuta contra Moonbase Alpha TestNet.

![Subscan Moonbase Alpha](/images/explorers/explorers-images-5.png)
