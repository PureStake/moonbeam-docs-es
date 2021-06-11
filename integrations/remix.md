---
title: Remix IDE
description: Aprenda a utilizar una de las herramientas de desarrollo de Ethereum más populares, Remix IDE, para interactuar con Moonbeam.
---

# Interactuar con Moonbeam usando Remix

![Intro diagram](/images/integrations/integrations-remix-banner.png)

## Introducción

Otra herramienta que los desarrolladores pueden usar para interactuar con Moonbeam es [Remix IDE](https://remix.ethereum.org/), uno de los entornos de desarrollo más utilizados para contratos inteligentes en Ethereum. Proporciona una solución basada en web para compilar e implementar rápidamente código basado en Solidity y Vyper en una máquina virtual local o, lo que es más interesante, en un proveedor Web3 externo, como MetaMask. Combinando ambas herramientas, uno puede comenzar muy rápidamente con Moonbeam.

## Implementar un contrato en Moonbeam

Para demostrar cómo puede aprovechar [Remix](https://remix.ethereum.org/) para implementar contratos inteligentes en Moonbeam, usaremos el siguiente contrato básico:

```solidity
pragma solidity ^0.7.5;

contract SimpleContract{
    string public text;
    
    constructor(string memory _input) {
        text = _input;
    }
}
```

Una vez compilado, podemos navegar a la pestaña "Implementar y ejecutar transacciones". Primero tenemos que configurar nuestro entorno en "Injected Web3". Esto utiliza el proveedor inyectado por MetaMask, que nos permite implementar contratos en la red a la que está conectado, en este caso, Moonbase Alpha TestNet.

Para este ejemplo, implementaremos el contrato desde una cuenta MetaMask financiada. Puede usar nuestro [grifo TestNet](/getting-started/testnet/faucet/) para financiar su cuenta para implementaciones en Moonbase Alpha. Luego, pase `Test Contract` como entrada a nuestra función constructora y presione implementar. Una ventana emergente de MetaMask mostrará la información relacionada con la transacción, que tendremos que firmar haciendo clic en "confirmar".

![Deploying Contract](/images/remix/integrations-remix-1.png)

Una vez que se incluye la transacción, el contrato aparece en la sección "Contratos implementados" en Remix. Allí, podemos interactuar con las funciones disponibles en nuestro contrato.

![Interact with Contract](/images/remix/integrations-remix-2.png)

## Tutoriales paso a paso

Si está interesado en una guía paso a paso más detallada, vaya a nuestros tutoriales específicos sobre el uso de [Remix en un nodo de desarrollo Moonbeam](/getting-started/local-node/using-remix/).  Los pasos también se pueden adaptar para implementar en Moonbase Alpha TestNet [conectando MetaMask a él.](/getting-started/testnet/metamask/).

