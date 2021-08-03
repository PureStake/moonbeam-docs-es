---
title: Polkadot JS Apps
description: Siga este tutorial rápido para aprender a usar las direcciones H160 estándar Ethereum de Moonbeam con aplicaciones basadas en sustrato como Polkadot JS.
---
# Polkadot JS Apps

![Intro diagram](/images/polkadotjs/polkadotjs-banner.png)

## Introducción {: #introduction } 

Con el [lanzamiento de la actualización v3](https://www.purestake.com/news/moonbeam-network-upgrades-account-structure-to-match-ethereum/) para Moonbase Alpha TestNet, hemos realizado actualizaciones significativas en el sistema de cuentas subyacente en Moonbeam, reemplazando las cuentas y claves predeterminadas de estilo Substrate con cuentas y claves de estilo Ethereum.

La interfaz de Polkadot JS Apps también se actualizó para que admita de forma nativa direcciones H160 y claves ECDSA. Entonces, en este tutorial, verifiquemos esta nueva integración de cuentas basadas en Ethereum en el sitio de Polkadot JS Apps.

## Conectando a Moonbase Alpha {: #connecting-to-moonbase-alpha } 

Primero, necesitamos conectarlo a Moonbase Alpha TestNet haciendo clic en el logo de la esquina superior izquierda y seleccionando Moonbase Alpha (bajo Test Networks).

![Connect to Moonbase Alpha](/images/polkadotjs/polkadotjs-app1.png)

Después de cambiar, el sitio de Polkadot JS no solo se conectará a Moonbase Alpha, sino que también cambiará su estilo para hacer una combinación perfecta.

![Connect to Moonbase Alpha](/images/polkadotjs/polkadotjs-app2.png)

## Creación o importación de una cuenta H160 {: #creating-or-importing-an-h160-account } 

Veamos cómo podemos crear una nueva cuenta o importar una cuenta MetaMask ya existente a Polkadot JS Apps. Primero, navegue a la sección de cuentas y haga clic en el botón Agregar cuenta.

![Connect to Moonbase Alpha](/images/polkadotjs/polkadotjs-app3.png)

Esto abrirá una ventana emergente del asistente que lo guiará a través del proceso de agregar una cuenta a la interfaz de Polkadot JS Apps. Asegúrese de hacer clic en el menú desplegable y cambiar de Mnemonic a Raw seed, esto le permite agregar una cuenta a través de una clave privada.

!!! nota
    Actualmente, solo puede crear o importar cuentas en PolkadotJS a través de una clave privada. Hacerlo con el mnemónico dará como resultado una dirección pública diferente si luego intentas importar esta cuenta a una billetera Ethereum como MetaMask. Esto se debe a que PolkadotJS usa BIP39, mientras que Ethereum usa BIP32 o BIP44.

![Connect to Moonbase Alpha](/images/polkadotjs/polkadotjs-app4.png)

A continuación, si desea crear una nueva cuenta, asegúrese de almacenar la clave privada que muestra el asistente. Si desea importar una cuenta existente, ingrese su clave privada que puede exportar desde MetaMask, en este caso estamos importando la siguiente cuenta:

- Llave privada: `28194e8ddb4a2f2b110ee69eaba1ee1f35e88da2222b5a7d6e3afa14cf7a3347`
- Direccion publica: `0x44236223aB4291b93EEd10E4B511B37a398DEE55` 

!!! nota
    Nunca revele sus claves privadas, ya que dan acceso directo a sus fondos. Los pasos de esta guía son solo para fines de demostración. 
    
Asegúrese de incluir el prefijo de la clave privada, es decir, `0x`. Si ingresó la información correctamente, la dirección pública correspondiente debería aparecer en la esquina superior izquierda de la ventana.

![Connect to Moonbase Alpha](/images/polkadotjs/polkadotjs-app5.png)

Haga clic en siguiente y finalice el asistente configurando un nombre de cuenta y una contraseña. Tras un mensaje de confirmación, deberías ver en la pestaña principal de Cuentas la dirección con el saldo correspondiente: en nuestro caso, la dirección de Bob. Además, podemos superponer la extensión MetaMask para ver que ambos saldos son iguales.

![Connect to Moonbase Alpha](/images/polkadotjs/polkadotjs-app6.png)

## Envío de una transacción a través de la API de Substrate {: #sending-a-transaction-through-substrates-api } 

Ahora, demostremos el potencial del esquema de Cuentas Unificadas de Moonbeam haciendo una transferencia a través de la API Substrate usando Polkadot JS Apps. Recuerde que estamos interactuando con Substrate usando una dirección H160 estilo Ethereum. Para hacerlo, hemos importado otra cuenta llamada Charley con 5 `DEV` tokens.

![Connect to Moonbase Alpha](/images/polkadotjs/polkadotjs-app7.png)

A continuación, haga clic en el botón enviar de Bob, que abre otro asistente que lo guía a través del proceso de envío de una transacción. Establezca la dirección de envío y la cantidad, que para nuestro ejemplo son 5 tokens DEV. Cuando esté listo, haga clic en el botón "Realizar transferencia".

![Connect to Moonbase Alpha](/images/polkadotjs/polkadotjs-app8.png)

Una vez firmada la transacción con la contraseña, Polkadot JS mostrará algunos mensajes en la esquina superior derecha mientras se procesa. Una vez confirmado, debería ver los saldos actualizados para cada cuenta.

![Connect to Moonbase Alpha](/images/polkadotjs/polkadotjs-app8.png)

¡Y eso es todo! Estamos entusiasmados de poder admitir cuentas H160 en Polkadot JS Apps, ya que creemos que esto mejorará en gran medida la experiencia del usuario en Moonbeam Network y sus características de compatibilidad con Ethereum.
