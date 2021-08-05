---
title: MathWallet
description: Esta guía le explica cómo conectar Mathwallet, una billetera basada en navegador que funciona con Ethereum, a Moonbeam.
---

# Interactuar con Moonbeam usando MathWallet
 
![Intro banner](/images/mathwallet/mathwallet-banner.png)

## Introducción {: #introduction } 

MathWallet [announced](https://mathwallet.org/moonbeam-wallet/en/) that is now natively supporting the [Moonbase Alpha TestNet](/networks/moonbase/) and [Moonriver](/networks/moonriver/). This means that you are now able to interact with Moonbase Alpha and Moonriver using another wallet besides MetaMask.

In this tutorial, we'll go through how to setup MathWallet to connect to our [TestNet](#connect-to-moonbase-alpha) and [Moonriver](#connect-to-moonriver). We'll also present a brief example of using MathWallet as a Web3 provider for other tools such as [Remix](/integrations/remix/).

## Checking Prerequisites {: #checking-prerequisites } 

Primero, debe instalar la extensión del navegador MathWallet instalada, que puede obtener de su [sitio web](https://mathwallet.org/en-us/).

Con la extensión del navegador instalada, ábrala y establezca una contraseña.

![Set wallet password](/images/mathwallet/mathwallet-images-1.png)
## Connect to Moonbase Alpha {: #connect-to-moonbase-alpha } 

In this part, we'll go through the process of connecting MathWallet to Moonbase Alpha. Enable Moonbase Alpha under Settings (top right gear icon) -> Networks -> Ethereum.

![Enable Moonbase Alpha](/images/mathwallet/mathwallet-images-2.png)

Y, por último, en la pantalla principal, haga clic en Cambiar red y seleccione Moonbase Alpha

![Connect to Moonbase Alpha](/images/mathwallet/mathwallet-images-3.png)

¡Y eso es todo, ahora tiene MathWallet conectado a Moonbase Alpha TestNet! Tu billetera debería verse así:

![Wallet Connected to Moonbase Alpha](/images/mathwallet/mathwallet-images-4.png)

## Connect to Moonriver {: #connect-to-moonriver } 

Getting started with Moonriver is a straightforward process. All you have to do is click Switch Network and select Moonriver

![Connect to Moonriver](/images/mathwallet/mathwallet-images-5.png)

And that is it, you now have MathWallet connected to Moonriver! Your wallet should look like this:

![Wallet Connected to Moonriver](/images/mathwallet/mathwallet-images-6.png)

## Agregar una billetera {: #adding-a-wallet } 

The following steps will show you how to interact with the Moonbase Alpha TestNet, but can also be used to interact with Moonriver.

After you are connected to Moonbase Alpha, you can now create a wallet to get an account and start interacting with the TestNet. Currently, there are three ways to add a wallet:

 - Crea una billetera
 - Importar una billetera existente usando una clave privada o mnemotécnica
 - Conectar la billetera de hardware (_no compatible por ahora_)

### Crea una billetera {: #create-a-wallet } 

The following steps for creating a wallet can be modified for Moonriver.

Para crear una nueva billetera, haga clic en el :heavy_plus_sign: signo junto a "Moonbase Alpha" y seleccione "Crear billetera".

![MathWallet create a wallet](/images/mathwallet/mathwallet-images-7.png)

Establezca y confirme un nombre de billetera. A continuación, asegúrese de almacenar de forma segura el mnemónico, ya que proporciona acceso directo a sus fondos. Una vez que haya completado el proceso, debería ver su billetera recién creada con su dirección pública asociada.

![MathWallet wallet created](/images/mathwallet/mathwallet-images-8.png)

### Importar una billetera {: #import-a-wallet } 

Para crear una nueva billetera, haga clic en el :heavy_plus_sign: signo junto a "Moonbase Alpha" y seleccione "Importar billetera".

![MathWallet import a wallet](/images/mathwallet/mathwallet-images97.png)

A continuación, seleccione entre importar utilizando un mnemónico o una clave privada. Para la primera opción, ingrese el mnemónico palabra por palabra, separados por espacios. Para la segunda opción, ingrese la clave privada (ya sea con el `0x` prefijo o no, funciona en ambos sentidos).

![MathWallet private key or mnemonic import](/images/mathwallet/mathwallet-images-10.png)

Después de hacer clic en Siguiente, configure un nombre de billetera, ¡y eso es todo! Debería ver su billetera importada con su dirección pública asociada.

![MathWallet imported wallet](/images/mathwallet/mathwallet-images-11.png)

## Usando MathWallet {: #using-mathwallet } 

MathWallet serves as a Web3 provider in tools such as [Remix](/integrations/remix/). By having MathWallet connected to Moonbase Alpha or Moonriver, you can deploy contracts as you would like using MetaMask, signing the transactions with MathWallet instead.

For example, in Remix, when deploying a smart contract to Moonbase Alpha, make sure you select the "Injected Web3" option in the "Environment" menu. If you have MathWallet connected, you will see the TestNet chain ID just below the box (_1287_) and your MathWallet account injected into Remix as well. When sending a transaction, you should see a similar pop-up from MathWallet:

![MathWallet sign transaction](/images/mathwallet/mathwallet-images-12.png)

Al hacer clic en "Aceptar", está firmando esta transacción y el contrato se implementará en Moonbase Alpha TestNet.

