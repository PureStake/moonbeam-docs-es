---
title: MathWallet
description: Esta guía le explica cómo conectar Mathwallet, una billetera basada en navegador que funciona con Ethereum, a Moonbeam.
---

# Interactuar con Moonbeam usando MathWallet
 
![Intro banner](/images/mathwallet/mathwallet-banner.png)

## Introducción

MathWallet [anunció](https://mathwallet.org/moonbeam-wallet/en/) que ahora es compatible de forma nativa con [Moonbase Alpha TestNet](/networks/testnet/). Esto significa que ahora puede interactuar con Moonbase Alpha utilizando otra billetera además de MetaMask.

En este tutorial, veremos cómo configurar MathWallet para conectarse a nuestro TestNet. También presentaremos un breve ejemplo del uso de MathWallet como proveedor de Web3 para otras herramientas como [Remix](/integrations/remix/).

## Conecte MathWallet a Moonbeam

En esta parte, veremos el proceso de conexión de MathWallet con Moonbase Alpha.

Primero, debe instalar la extensión del navegador MathWallet instalada, que puede obtener de su [sitio web](https://mathwallet.org/en-us/).

Con la extensión del navegador instalada, ábrala y establezca una contraseña.

![Set wallet password](/images/mathwallet/mathwallet-images-1.png)

A continuación, habilitemos Moonbase Alpha en Configuración (icono de engranaje superior derecho) -> Redes -> Ethereum.

![Enable Moonbase Alpha](/images/mathwallet/mathwallet-images-2.png)

Y, por último, en la pantalla principal, haga clic en Cambiar red y seleccione Moonbase Alpha

![Connect to Moonbase Alpha](/images/mathwallet/mathwallet-images-3.png)

¡Y eso es todo, ahora tiene MathWallet conectado a Moonbase Alpha TestNet! Tu billetera debería verse así:

![Wallet Connected to Moonbase Alpha](/images/mathwallet/mathwallet-images-4.png)

## Agregar una billetera

Ahora que MathWallet está conectado a Moonbase Alpha, ahora podemos crear una billetera para obtener una cuenta y comenzar a interactuar con TestNet. Actualmente, hay tres formas de agregar una billetera:

 - Crea una billetera
 - Importar una billetera existente usando una clave privada o mnemotécnica
 - Conectar la billetera de hardware (_no compatible por ahora_)

### Crea una billetera

Para crear una nueva billetera, haga clic en el :heavy_plus_sign: signo junto a "Moonbase Alpha" y seleccione "Crear billetera".

![MathWallet create a wallet](/images/mathwallet/mathwallet-images-5.png)

Establezca y confirme un nombre de billetera. A continuación, asegúrese de almacenar de forma segura el mnemónico, ya que proporciona acceso directo a sus fondos. Una vez que haya completado el proceso, debería ver su billetera recién creada con su dirección pública asociada.

![MathWallet wallet created](/images/mathwallet/mathwallet-images-6.png)

### Importar una billetera

Para crear una nueva billetera, haga clic en el :heavy_plus_sign: signo junto a "Moonbase Alpha" y seleccione "Importar billetera".

![MathWallet import a wallet](/images/mathwallet/mathwallet-images-7.png)

A continuación, seleccione entre importar utilizando un mnemónico o una clave privada. Para la primera opción, ingrese el mnemónico palabra por palabra, separados por espacios. Para la segunda opción, ingrese la clave privada (ya sea con el `0x` prefijo o no, funciona en ambos sentidos).

![MathWallet private key or mnemonic import](/images/mathwallet/mathwallet-images-8.png)

Después de hacer clic en Siguiente, configure un nombre de billetera, ¡y eso es todo! Debería ver su billetera importada con su dirección pública asociada.

![MathWallet imported wallet](/images/mathwallet/mathwallet-images-9.png)

## Usando MathWallet

MathWallet sirve como proveedor Web3 en herramientas como [Remix](/integrations/remix/). Al tener MathWallet conectado a Moonbase Alpha, puede implementar contratos como le gustaría usando MetaMask, firmando las transacciones con MathWallet en su lugar.

Por ejemplo, en Remix, al implementar un contrato inteligente, asegúrese de seleccionar la opción "Web3 inyectado" en el menú "Entorno". Si tiene MathWallet conectado, verá el ID de la cadena de TestNet justo debajo de la casilla ( _1287_ ) y su cuenta MathWallet también inyectada en Remix. Al enviar una transacción, debería ver una ventana emergente similar de MathWallet:

![MathWallet sign transaction](/images/mathwallet/mathwallet-images-10.png)

Al hacer clic en "Aceptar", está firmando esta transacción y el contrato se implementará en Moonbase Alpha TestNet.

