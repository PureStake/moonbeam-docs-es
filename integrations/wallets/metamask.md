---
title: MetaMask
description: Esta guía le explica cómo conectar MetaMask, una billetera Ethereum basada en navegador, a Moonbeam.
---

# Interactuando con Moonbeam usando MetaMask

![Intro diagram](/images/tokens/connect/metamask/metamask-banner.png)

## Introducción {: #introduction } 

Los desarrolladores pueden aprovechar las funciones de compatibilidad con Ethereum de Moonbeam para integrar herramientas, como [MetaMask](https://metamask.io/), en sus DApps. Al hacerlo, pueden usar la biblioteca inyectada que proporciona MetaMask para interactuar con la blockchain.

Actualmente, MetaMask se puede configurar para conectarse a dos redes: un nodo de desarrollo Moonbeam o Moonbase Alpha TestNet.

Si ya tiene MetaMask instalado, puede conectar fácilmente MetaMask a Moonbase Alpha TestNet:

<div class="button-wrapper">
    <a href="#" class="md-button connectMetaMask" value="moonbase">Connect MetaMask</a>
</div>

!!! nota
    MetaMask aparecerá pidiendo permiso para agregar Moonbase Alpha como una red personalizada. Una vez que apruebe los permisos, MetaMask cambiará su red actual a Moonbase Alpha.

Aprenda [a integrar un botón Connect MetaMask](https://medium.com/moonbeam-network/integrate-metamask-into-a-dapp-ea7528c5a786) en su dapp, para que los usuarios puedan conectarse a Moonbase Alpha con un simple clic de un botón.

## Conecte MetaMask a Moonbeam {: #connect-metamask-to-moonbeam } 

Una vez que haya instalado [MetaMask](https://metamask.io/) puede conectarlo a Moonbeam haciendo clic en el logotipo superior derecho y abriendo la configuración.

![MetaMask3](/images/tokens/connect/metamask/metamask-6.png)

A continuación, vaya a la pestaña Redes y haga clic en el botón "Agregar red".

![MetaMask4](/images/tokens/connect/metamask/metamask-7.png)

Aquí puede configurar MetaMask para las siguientes redes:

Nodo de desarrollo Moonbeam:

--8<-- 'text/metamask-local/development-node-details.md'

Moonbase Alpha TestNet:

--8<-- 'text/testnet/testnet-details.md'

## Tutoriales paso a paso {: #step-by-step-tutorials } 

En el caso de que estés interesado en guías paso a paso más detalladas para configurar MetaMask to Moonbeam, puedes acudir a nuestros tutoriales específicos:

 - MetaMask en un [nodo de desarrollo Moonbeam](/getting-started/local-node/using-metamask/)
 - MetaMask en [Moonbase Alpha](/getting-started/testnet/metamask/)
