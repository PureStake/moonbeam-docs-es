---
title: Usando MetaMask
description: Este tutorial le muestra cómo interactuar con un nodo Moonbeam local mediante una instalación predeterminada del complemento del navegador MetaMask.
---

# Interactuar con un nodo Moonbeam usando MetaMask

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//hrpBd2-a7as' frameborder='0' allowfullscreen></iframe></div>
<style>.caption { font-family: Open Sans, sans-serif; font-size: 0.9em; color: rgba(170, 170, 170, 1); font-style: italic; letter-spacing: 0px; position: relative;}</style><div class='caption'>Puede encontrar todo el código relevante para este tutorial en la <a href="{{ config.site_url }}resources/code-snippets/">página de fragmentos de código</a></div>

## Introducción

MetaMask se puede utilizar para conectarse a Moonbeam a través de Moonbase Alpha TestNet o mediante un nodo de desarrollo Moonbeam que se ejecute localmente.

Esta guía describe los pasos necesarios para conectar MetaMask a un nodo de desarrollo Moonbeam autónomo para enviar tokens entre cuentas. Si aún no ha configurado su propio nodo de desarrollo local, consulte [este tutorial](/getting-started/local-node/setting-up-a-node/), o siga las instrucciones en el [repositorio de GitHub](https://github.com/PureStake/moonbeam/).

!!! nota
    Este tutorial se creó utilizando la etiqueta tutorial-v7 que se basa en la versión v0.7.0 de [Moonbase Alpha](https://github.com/PureStake/moonbeam/releases/tag/v0.7.0). La plataforma Moonbeam y los componentes de [Frontier](https://github.com/paritytech/frontier) en los que se basa para la compatibilidad con Ethereum basada en sustrato aún se encuentran en un desarrollo muy activo. Los ejemplos de esta guía asumen que tiene un entorno basado en MacOS o Ubuntu 18.04 y deberá adaptarse en consecuencia para Windows.
    
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

Puede interactuar con Moonbeam de dos formas: mediante el uso de puntos finales de Substrate RPC o mediante el uso de puntos finales RPC compatibles con Web3. Los últimos puntos finales se sirven actualmente desde el mismo servidor RPC que los RPC de sustrato. En este tutorial, usaremos los puntos finales de Web3 RPC para interactuar con Moonbeam.

## Instale la extensión MetaMask

Primero, comenzamos con una instalación de [MetaMask](https://metamask.io/) nueva y predeterminada de la tienda Chrome. Después de descargar, instalar e inicializar la extensión, siga la guía "Comenzar". Allí, debe crear una billetera, establecer una contraseña y almacenar su frase secreta de respaldo (esto le brinda acceso directo a sus fondos, así que asegúrese de guardarlos en un lugar seguro). Una vez completado, importaremos la cuenta de desarrollo:

![Import dev account into MetaMask](/images/metamask/using-metamask-1.png)

Los detalles de las cuentas de desarrollo que vienen prefinanciadas para este nodo de desarrollo son los siguientes:

--8<-- 'code/setting-up-node/dev-accounts.md'

--8<-- 'code/setting-up-node/dev-testing-account.md'

En la pantalla de importación, seleccione "Clave privada" y pegue una de las claves enumeradas anteriormente. Para este ejemplo usaremos la clave de Gerald:

![Paste your account key into MetaMask](/images/metamask/using-metamask-2.png)

Debería terminar con una "Cuenta 2" importada que se ve así:

![MetaMask displaying your new Account 2](/images/metamask/using-metamask-3.png)

## Conectando MetaMask a Moonbeam

MetaMask se puede configurar para conectarse a su nodo de desarrollo local o al Moonbase Alpha TestNet. 

Para conectar MetaMask a Moonbeam, vaya a Configuración -> Redes -> Agregar red. Aquí es donde puede configurar a qué red le gustaría que se conecte MetaMask, usando las siguientes configuraciones de red:

Nodo de desarrollo Moonbeam:

--8<-- 'text/metamask-local/development-node-details.md'

Moonbase Alpha TestNet:

--8<-- 'text/testnet/testnet-details.md'

Para el propósito de este tutorial, conectemos MetaMask a nuestro nodo de desarrollo Moonbeam que se ejecuta localmente.

![Enter your new network information into MetaMask](/images/metamask/using-metamask-4.png)

Cuando presiona "guardar" y sale de la pantalla de configuración de red, MetaMask debe estar conectado al nodo de desarrollo local de Moonbeam a través de su Web3 RPC, y debería ver la cuenta de desarrollo de Moonbeam con un saldo de 1207925.8196 DEV.

![Your new Moonbeam account with a balance of 1207925.8196](/images/metamask/using-metamask-5.png)

## Iniciar una transferencia

Intentemos enviar algunos tokens con MetaMask.

Para simplificar, transferiremos de esta cuenta de desarrollo a la que se creó al configurar MetaMask. Haga clic en "Enviar" para iniciar la transferencia. En consecuencia, podemos utilizar la opción "Transferir entre mis cuentas". Transfieramos 100 tokens y dejemos todas las demás configuraciones como están:

![Initiating a token transfer](/images/metamask/using-metamask-6.png)

Una vez que haya enviado la transacción, la verá "pendiente" hasta que se confirme, como se muestra en la siguiente imagen:

![Transaction confirmation](/images/metamask/using-metamask-7.png)

Tenga en cuenta que el saldo de la Cuenta 2 se ha reducido en la cantidad enviada + las tarifas de gas. Pasando a la Cuenta 1, vemos que han llegado los 100 tokens enviados:

![New balance in Account 1](/images/metamask/using-metamask-8.png)

Si regresa a su terminal donde tiene su nodo Moonbeam en ejecución, comenzará a ver bloques que se crean a medida que llegan las transacciones:

![Moonbeam Development Node](/images/metamask/using-metamask-9.png)

!!! nota
    Si termina restableciendo su nodo de desarrollo usando el comando Substrate purge-chain, deberá restablecer su cuenta de MetaMask Genesis usando Configuración -> Avanzado -> Restablecer cuenta. Esto borrará el historial de transacciones de sus cuentas y restablecerá el nonce. ¡Asegúrate de no borrar nada que quieras conservar!
 
