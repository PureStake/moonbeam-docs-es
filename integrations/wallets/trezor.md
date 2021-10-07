---
title: Trezor
description: Esta guía le muestra cómo usar su billetera de hardware Trezor para firmar transacciones en Moonbeam, aprovechando sus características de compatibilidad con Ethereum
---

# Cartera de hardware Trezor

![Intro diagram](/images/tokens/connect/trezor/trezor-banner.png)

## Introducción {: #introduction } 

Las carteras de hardware proporcionan una forma más segura de almacenar fondos criptográficos porque la clave privada (utilizada para firmar transacciones) se almacena fuera de línea. Trezor ofrece dos soluciones de billetera de hardware al momento de escribir: Trezor One y Trezor Model T.

Debido a que Moonbeam es totalmente compatible con Ethereum, ¡puede usar su dispositivo Trezor para firmar transacciones en Moonbeam!

Este tutorial le muestra cómo comenzar con su billetera de hardware Trezor en Moonbase Alpha. La guía solo ilustra los pasos para un dispositivo Trezor Model T, pero también puede seguirlos con un Trezor One.

Tenga en cuenta que su dispositivo Trezor firmará transacciones en cualquier red MetaMask a la que esté conectado.

## Comprobación de requisitos previos {: #checking-prerequisites } 

Antes de comenzar, actualice [Trezor Suite](https://suite.trezor.io/) a la última versión disponible. Además, asegúrese de que su dispositivo de billetera de hardware Trezor ejecute el firmware más reciente. El Trezor wiki ofrece tutoriales sobre cómo actualizar el firmware de ambos [Trezor One](https://wiki.trezor.io/User_manual:Updating_the_Trezor_device_firmware__T1) y [Trezor Model T](https://wiki.trezor.io/User_manual:Updating_the_Trezor_device_firmware).

En el momento de redactar este documento, se utilizaron las siguientes versiones:

 - Trezor Suite 21.5.1
 - Trezor One firmware v1.10.0
 - Trezor Model T firmware v2.4.0

Además, necesitará MetaMask como intermediario entre su dispositivo Trezor y Moonbase Alpha. Asegúrese de que su MetaMask esté [conectada a Moonbase Alpha](/integrations/wallets/metamask/). Tenga en cuenta que su dispositivo Trezor firmará transacciones en cualquier red MetaMask a la que esté conectado.

## Importar su cuenta de Trezor a MetaMask {: #importing-your-trezor-account-to-metamask } 

Para comenzar, debe haber configurado una billetera (ya sea estándar o una billetera oculta). Una vez que haya conectado su dispositivo Trezor, lo desbloquee y obtenga una configuración de billetera en Trezor Suite. A continuación, para importar su cuenta de Trezor Ethereum a MetaMask, siga los siguientes pasos:

 1. Haga clic en el logotipo de la esquina superior derecha para expandir el menú.
 2. Seleccione "Conectar cartera de hardware"

![Cartera de hardware MetaMask Connect](/images/tokens/connect/ledger/ledger-1.png)

Inmediatamente después, se le pedirá que seleccione qué billetera de hardware le gustaría usar en MetaMask. En el momento de escribir este artículo, solo se admiten las carteras de hardware Ledger y Trezor. Si tiene su dispositivo Trezor listo para funcionar, siga los siguientes pasos:

 1. Seleccione el logo de Trezor
 2. Haga clic en "Continuar"

![Cartera de hardware MetaMask Select Trezor](/images/tokens/connect/trezor/trezor-2.png)

Después de hacer clic en el botón, `TrezorConnect` debería aparecer una nueva pestaña llamada , donde deberá emparejar su dispositivo. Esto no es necesario si ha abierto Trezor Suite y su dispositivo está conectado. Aquí, haga clic en "Emparejar dispositivos.


![Trezor Hardware Wallet Connect Pair Device](/images/tokens/connect/trezor/trezor-3.png)

En la siguiente pantalla, siga los siguientes pasos:

 1. Haga clic en "Buscar dispositivos". Esto abrirá un menú que muestra a qué dispositivo Trezor (si está disponible) desea conectarse
 2. Seleccione el dispositivo Trezor que desea usar
 3. Haga clic en "Conectar"

![Trezor Hardware Wallet Connect Wizard Seleccionar y conectar dispositivo](/images/tokens/connect/trezor/trezor-4.png)

Una vez que haya conectado su dispositivo, debe permitir que MetaMask lea sus claves públicas. Por lo tanto, haga clic en "Permitir una vez para esta sesión". Opcionalmente, también puede marcar la casilla "No volver a preguntarme".

![Trezor Hardware Wallet Connect Wizard Permitir lectura de claves públicas](/images/tokens/connect/trezor/trezor-5.png)

A continuación, se le preguntará si desea exportar la clave pública de su cuenta Ethereum (la pestaña se recortó y se etiquetó como 1 en la siguiente imagen). Inmediatamente después, se le solicitará una opción para usar la opción de frase de contraseña de Trezor (pestaña recortada y etiquetada como 2 en la imagen). Si desea utilizar la billetera predeterminada, simplemente haga clic en "Entrar". Por el contrario, siga [el artículo de la wiki de Trezor sobre carteras de frases de contraseña](https://wiki.trezor.io/Passphrase).

![Trezor Hardware Wallet Connect Wizard Allow Export and Passphrase](/images/tokens/connect/trezor/trezor-6.png)

Si MetaMask pudo conectarse correctamente a su dispositivo Trezor, debería ver una lista de cinco cuentas de estilo Ethereum. Por el contrario, verifique que haya conectado correctamente su dispositivo Trezor a la computadora y que esté desbloqueado. También puede repetir el proceso con la aplicación Trezor Suite abierta.


De esta lista de cinco cuentas de Ethereum, siga los siguientes pasos:

 1. Seleccione las cuentas que le gustaría importar desde su dispositivo Trezor
 2. Haga clic en "Desbloquear"

![Trezor Seleccione las cuentas de Ethereum para importar](/images/tokens/connect/trezor/trezor-7.png)

Si ha importado su cuenta de estilo Trezor Ethereum con éxito, debería verla en la pantalla principal de MetaMask como se muestra en la siguiente imagen:

![MetaMask importó correctamente la cuenta de Trezor](/images/tokens/connect/trezor/trezor-8.png)

Ahora ha importado con éxito una cuenta compatible con Moonbeam desde su dispositivo Trezor y ahora está listo para comenzar a [firmar transacciones usando su billetera de hardware](#firmar-una-transaccion-con-su-trezor).

## Firmar una transacción con su Trezor {: #signing-a-transaction-using-your-trezor } 

Si ha importado con éxito [su cuenta de Trezor a MetaMask](#importar-su-cuenta-de-trezor-a-metamask), está listo para firmar transacciones en Moonbeam usando su dispositivo Trezor. Este tutorial le mostrará cómo enviar una transacción simple en Moonbase Alpha TestNet, pero se aplica a otras redes del ecosistema Moonbeam.

Primero, asegúrese de que su cuenta de Trezor esté [financiada con tokens DEV](/getting-started/moonbase/faucet/). A continuación, haga clic en el botón "Enviar".


![MetaMask Trezor Account Funded](/images/tokens/connect/trezor/trezor-9.png)

`TrezorConnect` Debería aparecer una pestaña pidiendo permiso para leer las claves públicas de su dispositivo y preparar su Trezor para la transacción y la firma de datos. Una vez que esté listo, haga clic en "Permitir una vez para esta sesión". Opcionalmente, también puede marcar la casilla "No volver a preguntarme".

![La billetera de hardware Trezor permite la lectura de claves públicas y la firma](/images/tokens/connect/trezor/trezor-10.png)

Como lo haría en una transacción estándar, configure la dirección del destinatario, ingrese la cantidad de tokens para enviar, revise los detalles de la transacción y confírmela. Esto iniciará el asistente de firma de transacciones en su dispositivo Trezor. Aquí, siga los siguientes pasos:

 1. Revise todos los detalles de la transacción. Tenga en cuenta que el token corresponde a la red a la que está conectado MetaMask. **En este caso, son tokens DEV y no UNKN.**
 2. Una vez que se hayan verificado todos los detalles, mantenga presionado el botón para confirmar


!!! nota
    En el momento de escribir este artículo, el nombre del token para todas las redes relacionadas con Moonbeam siempre se muestra como UNKN. Tenga en cuenta que el token que se está manejando es el correspondiente a la red a la que está conectado MetaMask.


![Transacción de signo de billetera de hardware Trezor](/images/tokens/connect/trezor/trezor-11.png)

Inmediatamente después de haber aprobado la transacción, MetaMask la envía a la red. Una vez que se confirma la transacción, se mostrará como "Enviar" en la pantalla principal de MetaMask.

![MetaMask Trezor Transacción Wizard](/images/tokens/connect/trezor/trezor-12.png)

¡Y eso es todo! Firmó una transacción en Moonbase Alpha usando su billetera de hardware Trezor.

El proceso de interactuar con contratos inteligentes usando su dispositivo Trezor es similar. Asegúrese de verificar los datos que se están firmando en su dispositivo Trezor antes de confirmar la transacción.
