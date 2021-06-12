---
title: Usando Truffle
description: Moonbeam hace que sea increíblemente fácil implementar un contrato inteligente basado en Solidity en un nodo Moonbeam usando Truffle. Aprenda cómo en este tutorial.
---

# Interactuar con Moonbeam usando Truffle

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//RD5MefSPNeo' frameborder='0' allowfullscreen></iframe></div>
<style>.caption { font-family: Open Sans, sans-serif; font-size: 0.9em; color: rgba(170, 170, 170, 1); font-style: italic; letter-spacing: 0px; position: relative;}</style><div class='caption'>Puede encontrar todo el código relevante para este tutorial en la <a href="{{ config.site_url }}resources/code-snippets/">página de fragmentos de código</a></div>

## Introducción

Esta guía describe el proceso de implementación de un contrato inteligente basado en Solidity en un nodo Moonbeam utilizando [Truffle](https://www.trufflesuite.com/), una herramienta de desarrollo de uso común para contratos inteligentes en Ethereum. Dadas las características de compatibilidad con Ethereum de Moonbeam, Truffle se puede usar directamente con un nodo Moonbeam.

!!! nota
    Este tutorial se creó utilizando la etiqueta tutorial {{ networks.development.build_tag}} que se basa en la versión {{ networks.moonbase.version }} de [Moonbase Alpha](https://github.com/PureStake/moonbeam/releases/tag/{{ networks.moonbase.version }}).  La plataforma Moonbeam y los componentes de [Frontier](https://github.com/paritytech/frontier) en los que se basa para la compatibilidad con Ethereum basada en sustratos aún se encuentran en un desarrollo muy activo. Los ejemplos de esta guía asumen que tiene un entorno basado en MacOS o Ubuntu 18.04 y deberá adaptarse en consecuencia para Windows.
    
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

Para esta guía, necesitará tener un nodo de desarrollo Moonbeam ejecutándose en `--dev` modo. Esto se puede hacer siguiendo los pasos detallados [aquí](/getting-started/local-node/setting-up-a-node/) o usando el [complemento Moonbeam Truffle](/integrations/trufflebox/#the-moonbeam-truffle-plugin), que usaremos en los ejemplos de este tutorial.

## Comprobación de requisitos previos

--8<-- 'text/common/install-nodejs.md'


Además, puede instalar Truffle globalmente ejecutando:

```
npm install -g truffle
```

A la fecha de publicación de esta guía, las versiones utilizadas fueron 15.12.0, 7.6.3 y 5.2.4 respectivamente.

!!! nota
    Para los siguientes ejemplos, no es necesario tener Truffle instalado globalmente, ya que se incluye como una dependencia en la caja Moonbeam Truffle. Si lo prefiere, puede ejecutar `npx truffle` o en `./node_modules/.bin/truffle` lugar de `truffle`.

## Empezando con Truffle

Para facilitar el proceso de introducción a Truffle, hemos [lanzado la caja Moonbeam Truffle](https://moonbeam.network/announcements/moonbeam-truffle-box-available-solidity-developers/). Esto proporciona una configuración estándar para acelerar el proceso de aceleración para implementar contratos en Moonbeam. Para leer más sobre la caja, puede visitar [este enlace](/integrations/trufflebox/).

Para descargar la caja Moonbeam Truffle, siga [estas instrucciones](/integrations/trufflebox/#downloading-and-setting-up-the-truffle-box).  Una vez dentro del directorio, echemos un vistazo al `truffle-config.js` archivo (para el propósito de esta guía, se eliminó parte de la información):

```js
const HDWalletProvider = require('@truffle/hdwallet-provider');
// Moonbeam Development Node Private Key
const privateKeyDev =
   '99B3C12287537E38C90A9219D4CB074A89A16E9CDB20BF85728EBD97C343E342';
//...
module.exports = {
   networks: {
      dev: {
         provider: () => {
            ...
            return new HDWalletProvider(privateKeyDev, 'http://localhost:9933/')
         },
         network_id: 1281,
      },
      //...
   },
   plugins: ['moonbeam-truffle-plugin']
};
```

Tenga en cuenta que estamos usando `HD-Wallet-Provider` Truffle como la cartera determinista jerárquica. Además, hemos definido una `dev` red que apunta a la URL del proveedor del nodo de desarrollo y se incluye la clave privada de la cuenta de desarrollo, que contiene todos los fondos en el nodo de desarrollo.

## Ejecución de un nodo de desarrollo

Para configurar un nodo de desarrollo Moonbeam, puede seguir [este tutorial](/getting-started/local-node/setting-up-a-node/). El proceso toma alrededor de 40 minutos en total y necesita instalar Substrate y todas sus dependencias. El complemento Moonbeam Truffle proporciona una forma de comenzar con un nodo de desarrollo mucho más rápido, y el único requisito es tener Docker instalado (en el momento de redactar este artículo, la versión de Docker utilizada era la 19.03.6).

Para iniciar un nodo de desarrollo Moonbeam en su entorno local, primero debemos descargar la imagen de Docker correspondiente:

```
truffle run moonbeam install
```

![Docker image download](/images/truffle/using-truffle-1.png)

Una vez descargado, podemos proceder a iniciar el nodo local con el siguiente comando:

```
truffle run moonbeam start
```

Verá un mensaje que indica que el nodo se ha iniciado, seguido de los dos puntos finales disponibles.

![Moonbeam local node started](/images/truffle/using-truffle-2.png)

Una vez que haya terminado de usar su nodo de desarrollo Moonbeam, puede ejecutar las siguientes líneas para detenerlo y eliminar la imagen de Docker si ese es el caso:

```
truffle run moonbeam stop && \
truffle run moonbeam remove
```

![Moonbeam local node stoped and image removed](/images/truffle/using-truffle-3.png)

## El archivo del contrato

También hay un contrato de token ERC-20 incluido con la caja Truffle:

```solidity
pragma solidity ^0.7.5;

// Import OpenZeppelin Contract
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

// This ERC-20 contract mints the specified amount of tokens to the contract creator.
contract MyToken is ERC20 {
    constructor(uint256 initialSupply) ERC20("MyToken", "MYTOK")
    {
        _mint(msg.sender, initialSupply);
    }
}
```

Este es un simple contrato ERC-20 basado en el contrato OpenZepplin ERC-20. Crea "MyToken" con el símbolo "MYTOK" y los 18 decimales estándar. Además, asigna el suministro de token inicial creado al creador del contrato.

Si echamos un vistazo al script de migración de contratos de Truffle a continuación `migrations/2_deploy_contracts.js`, contiene lo siguiente:

```javascript
var MyToken = artifacts.require('MyToken');

module.exports = function (deployer) {
   deployer.deploy(MyToken, '8000000000000000000000000');
};
```

"8000000000000000000000000" es el número de tokens que se acuñarán inicialmente con el contrato, es decir, 8 millones con 18 decimales.

## Implementar un contrato en Moonbeam usando Truffle

Antes de que podamos implementar nuestros contratos, debemos compilarlos. (Decimos "contratos" porque las implementaciones normales de Truffle incluyen el `Migrations.sol` contrato). Puede hacer esto con el siguiente comando:

```
truffle compile
```

Si tiene éxito, debería ver un resultado como el siguiente:

![Truffle compile success message](/images/truffle/using-truffle-4.png)

Ahora estamos listos para implementar los contratos compilados. Puede hacer esto con el siguiente comando:

```
truffle migrate --network dev
```

Si tiene éxito, verá acciones de implementación, incluida la dirección del contrato implementado:

![Successful contract deployment actions](/images/truffle/using-truffle-5.png)

Una vez que haya seguido la [guía MetaMask](/getting-started/local-node/using-metamask/) y la [guía Remix](/getting-started/local-node/using-remix/), podrá tomar la dirección de contrato implementada que se devuelve y cargarla en MetaMask o Remix.

