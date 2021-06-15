---
title: Usando Remix
description: Aprenda a utilizar una de las herramientas de desarrollo de Ethereum más populares, Remix IDE, para interactuar con un nodo Moonbeam local.
---

# Interactuar con Moonbeam usando Remix

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//RT_f1-ga_n4' frameborder='0' allowfullscreen></iframe></div>
<style>.caption { font-family: Open Sans, sans-serif; font-size: 0.9em; color: rgba(170, 170, 170, 1); font-style: italic; letter-spacing: 0px; position: relative;}</style><div class='caption'>Puede encontrar todo el código relevante para este tutorial en la <a href="{{ config.site_url }}resources/code-snippets/">página de fragmentos de código</a></div>

## Introducción

Remix es uno de los entornos de desarrollo más utilizados para contratos inteligentes en Ethereum. Dadas las características de compatibilidad con Ethereum de Moonbeam, Remix se puede usar directamente con un nodo de desarrollo Moonbeam o Moonbase Alpha TestNet.

Esta guía describe el proceso de creación e implementación de un contrato inteligente basado en Solidity en un nodo de desarrollo Moonbeam utilizando [Remix IDE](https://remix.ethereum.org/). 

!!! nota
    Este tutorial se creó utilizando la etiqueta tutorial-v7 que se basa en la versión v0.7.0 de [Moonbase Alpha](https://github.com/PureStake/moonbeam/releases/tag/{{ networks.moonbase.version }}) . La plataforma Moonbeam y los componentes de [Frontier](https://github.com/paritytech/frontier) en los que se basa para la compatibilidad con Ethereum basada en sustratos aún se encuentran en un desarrollo muy activo.

Esta guía asume que tiene un nodo Moonbeam local ejecutándose en `--dev` modo y que tiene una instalación de [MetaMask](https://metamask.io/) configurada para usar este nodo local. Puede encontrar instrucciones para ejecutar un nodo Moonbeam local [aquí](/getting-started/local-node/setting-up-a-node/) instrucciones para conectar MetaMask a él [aquí](/getting-started/local-node/using-metamask/).

## Comprobación de requisitos previos

Si siguió las guías anteriores, debería tener un nodo Moonbeam local que comenzará a crear bloques a medida que lleguen las transacciones:

![Local Moonbeam node producing blocks](/images/remix/using-remix-1.png)

Y debe tener una instalación de MetaMask conectada a su nodo de desarrollo Moonbeam local con al menos una cuenta que tenga saldo. Debería verse algo como esto (vista ampliada):

![MetaMask installation with a balance](/images/remix/using-remix-2.png)

!!! nota
   ¡Asegúrese de estar conectado a su nodo Moonbeam y no a otra red!

## Introducción a Remix

Ahora, activemos Remix para ejercitar funcionalidades más avanzadas en Moonbeam.

Inicie Remix navegando a [https://remix.ethereum.org/](https://remix.ethereum.org/). En la pantalla principal, en Entornos, seleccione Solidity para configurar Remix para el desarrollo de Solidity, luego navegue a la vista Exploradores de archivos:

![File explorer](/images/remix/using-remix-3.png)

Crearemos un nuevo archivo para guardar el contrato inteligente de Solidity. Presione el botón + debajo de Exploradores de archivos e ingrese el nombre "MyToken.sol" en el cuadro de diálogo emergente:

![Create a new file for your Solidity contract](/images/remix/using-remix-4.png)

A continuación, peguemos el siguiente contrato inteligente en la pestaña del editor que aparece:

```solidity
--8<-- 'code/remix-local/contract.md'
```

Este es un simple contrato ERC-20 basado en la plantilla actual de Open Zeppelin ERC-20. Crea MyToken con el símbolo MYTOK y acuña la totalidad del suministro inicial al creador del contrato.

Una vez que haya pegado el contrato en el editor, debería verse así:

![Paste the contract into the editor](/images/remix/using-remix-5.png)

Ahora, navegue a la opción de la barra lateral de compilación para presionar el botón "Compilar MyToken.sol":

![Compile MyToken.sol](/images/remix/using-remix-6.png)

Verá que Remix descarga todas las dependencias de Open Zeppelin y compila el contrato.

## Implementar un contrato en Moonbeam usando Remix

Ahora podemos implementar el contrato navegando a la opción de la barra lateral Implementación. Debe cambiar el menú desplegable "Entorno" superior de "VM JavaScript" a "Web3 inyectado". Esto le dice a Remix que use el proveedor inyectado MetaMask, que lo apuntará a su nodo de desarrollo Moonbeam. Si quisiera probar esto usando Moonbase Alpha TestNet, tendría que conectar MetaMask a TestNet en lugar de su nodo de desarrollo local.

Tan pronto como seleccione "Injected Web3", se le pedirá que permita que Remix se conecte a su cuenta de MetaMask.

![Replace](/images/remix/using-remix-7.png)

Presione "Siguiente" en Metamask para permitir que Remix acceda a la cuenta seleccionada.

De vuelta en Remix, debería ver que la cuenta que desea usar para la implementación ahora está administrada por MetaMask. Junto al botón Implementar, especifiquemos un suministro inicial de tokens de 8M. Dado que este contrato utiliza el valor predeterminado de 18 decimales, el valor para poner en la casilla es `8000000000000000000000000`.

Una vez que haya ingresado este valor, seleccione "Implementar".

![Enter an account balance and deploy](/images/remix/using-remix-8.png)

Se le pedirá en MetaMask que confirme la transacción de implementación del contrato.

![Confirm the transaction message](/images/remix/using-remix-9.png)

!!! nota
    Si tiene problemas para implementar algún contrato específico, puede intentar aumentar manualmente el límite de gas. Puede hacer esto en Configuración -> Avanzado -> Controles de gas avanzados = ACTIVADO.

Después de presionar confirmar y la implementación se completa, verá la transacción en la lista de MetaMask. El contrato aparecerá en Contratos implementados en Remix.

![Confirmed label on a transaction](/images/remix/using-remix-10.png)

Una vez que se implementa el contrato, puede interactuar con él desde Remix.

Profundice en el contrato en "Contratos implementados". Al hacer clic en el nombre, el símbolo y el totalSupply debería devolver "MyToken", "MYTOK" y "8000000000000000000000000" respectivamente. Si copia la dirección desde la que implementó el contrato y la pega en el campo balanceOf, debería ver la totalidad del saldo del ERC20 como perteneciente a ese usuario. Copie la dirección del contrato haciendo clic en el botón junto al nombre y la dirección del contrato.

![Interact with the contract from Remix](/images/remix/using-remix-11.png)

## Interactuar con un ERC-20 basado en Moonbeam de MetaMask

Ahora, abra MetaMask para agregar los tokens ERC-20 recién implementados. Antes de hacerlo, asegúrese de haber copiado la dirección del contrato de Remix. De vuelta en MetaMask, haga clic en "Agregar token" como se muestra a continuación. Asegúrese de estar en la cuenta que implementó el contrato de token.

![Add a token](/images/remix/using-remix-12.png)

Pegue la dirección del contrato copiada en el campo "Token personalizado". Los campos "Símbolo de token" y "Decimales de precisión" deben completarse automáticamente.

![Paste the copied contract address](/images/remix/using-remix-13.png)

Después de presionar "Siguiente", deberá confirmar que desea agregar estos tokens a su cuenta de MetaMask. Presione "Agregar token" y debería ver un saldo de 8 millones de MyTokens en MetaMask:

![Add the tokens to your MetaMask account](/images/remix/using-remix-14.png)

Ahora podemos enviar algunos de estos tokens ERC-20 a la otra cuenta que hemos configurado en MetaMask. Haga clic en "enviar" para iniciar la transferencia de 500 MyTokens y seleccione la cuenta de destino.

Después de presionar "siguiente", se le pedirá que confirme (similar a lo que se muestra a continuación).

![Confirmation of the token transfer](/images/remix/using-remix-15.png)

Presione "Confirmar" y, una vez que se complete la transacción, verá una confirmación y una reducción del saldo de la cuenta MyToken de la cuenta del remitente en MetaMask:

![Verify the reduction in account balance](/images/remix/using-remix-16.png)

Si posee la cuenta a la que envió los tokens, puede agregar el activo del token para verificar que llegó la transferencia.

