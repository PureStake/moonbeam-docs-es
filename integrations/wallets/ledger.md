---
title: Ledger
description: Esta guía le muestra cómo usar su billetera de hardware Ledger para firmar transacciones en Moonbeam, aprovechando sus características de compatibilidad con Ethereum
---

# Cartera de hardware de Ledger

![Intro diagram](/images/ledger/ledger-banner.png)

## Introdución 

Las carteras de hardware proporcionan una forma más segura de almacenar fondos criptográficos porque la clave privada (utilizada para firmar transacciones) se almacena fuera de línea. Ledger ofrece dos soluciones de billetera de hardware en el momento de escribir este artículo: Ledger Nano S y Ledger Nano X.

Debido a que Moonbeam es totalmente compatible con Ethereum, y Ledger ahora admite el inicio de sesión en diferentes redes de Chain ID, ¡puede usar su dispositivo Ledger para firmar transacciones en Moonbeam!

Este tutorial le muestra cómo comenzar con su billetera de hardware Ledger en Moonbase Alpha. La guía solo ilustra los pasos para un dispositivo Ledger Nano X, pero también puede seguirlos con un Ledger Nano S. El mismo proceso se puede aplicar a las otras redes del ecosistema Moonbeam.


## Comprobación de requisitos previos

Antes de comenzar, actualice [Ledger Live](https://www.ledger.com/ledger-live/download) a la última versión disponible. Además, asegúrese de que su dispositivo de billetera de hardware Ledger ejecute el firmware más reciente. El sitio web de soporte de Ledger ofrece tutoriales sobre cómo actualizar el firmware de los dispositivos [Ledger Nano S](https://support.ledger.com/hc/en-us/articles/360002731113-Update-Ledger-Nano-S-firmware) y [Ledger Nano X](https://support.ledger.com/hc/en-us/articles/360013349800-Update-Ledger-Nano-X-firmware).

Una vez que esté ejecutando la última versión de firmware, asegúrese de estar ejecutando la aplicación Ethereum más reciente. El sitio web de soporte de Ledger ofrece un tutorial sobre [cómo instalar la aplicación Ethereum](https://support.ledger.com/hc/en-us/articles/360009576554-Ethereum-ETH-).

En el momento de redactar este documento, se utilizaron las siguientes versiones:

 - Ledger Live 2.29
 - Ledger Nano S firmware v2.0.0
 - Ledger Nano X firmware v1.3.0
 - Ethereum app v1.8.5

Además, necesitará MetaMask como intermediario entre su dispositivo Ledger y Moonbase Alpha. Asegúrese de que su MetaMask esté [conectada a Moonbase Alpha](/integrations/wallets/metamask/).Los usuarios de Chrome (versión 91) necesitan algunos pasos adicionales, que [se detallan en este tutorial](#chrome-browser).  El uso de Firefox resultará en una experiencia mucho más simple / directa.


Tenga en cuenta que su dispositivo Ledger firmará transacciones en cualquier red MetaMask a la que esté conectado.


## Importación de su Ledger a MetaMask

Para comenzar, debe conectar su dispositivo Ledger a la computadora, desbloquearlo y abrir la aplicación Ethereum. A continuación, para importar su cuenta Ethereum Ledger a MetaMask, siga los siguientes pasos:


 1. Haga clic en el logotipo de la esquina superior derecha para expandir el menú.
 2. Seleccione "Conectar cartera de hardware"


![Cartera de hardware MetaMask Connect](/images/ledger/ledger-images1.png)

En la siguiente pantalla, se le pedirá que seleccione qué billetera de hardware le gustaría usar en MetaMask. En el momento de escribir este artículo, solo se admiten las carteras de hardware Ledger y Trezor. Aquí, siga los siguientes pasos:

 1. Seleccione el logotipo de Ledger
 2. Haga clic en "Continuar"

![Cartera de hardware MetaMask Select Ledger](/images/ledger/ledger-images2.png)

Si MetaMask pudo conectarse correctamente a su dispositivo Ledger, debería ver una lista de cinco cuentas de estilo Ethereum. Por el contrario, verifique que Ledger Live esté cerrado, que haya conectado su dispositivo Ledger a la computadora, lo haya desbloqueado y que la aplicación Ethereum esté abierta. Si está utilizando Chrome, consulte estos [pasos adicionales](#chrome-browser).

De esta lista de cinco cuentas de Ethereum, siga los siguientes pasos:

 1. Seleccione las cuentas que le gustaría importar desde su dispositivo Ledger
 2. Haga clic en "Desbloquear"


![MetaMask selecciona las cuentas de Ethereum para importar](/images/ledger/ledger-images3.png)

Si ha importado su cuenta de estilo Ledger Ethereum con éxito, debería verla en la pantalla principal de MetaMask como se muestra en la siguiente imagen:

![MetaMask importó correctamente la cuenta del libro mayor](/images/ledger/ledger-images4.png)

Ahora ha importado con éxito una cuenta compatible con Moonbeam desde su dispositivo Ledger y ahora está listo para comenzar a [firmar transacciones usando su billetera de hardware](#signing-a-transaction-using-your-ledger).

### Navegador Chrome

A partir de la versión 91 de Chrome, los usuarios que deseen conectar su dispositivo Ledger a MetaMask deben ejecutar la última versión de Ledger Live (v2.29 en el momento de escribir este artículo).
 
Además, en MetaMask, deben habilitar el soporte de Ledger Live. Para hacerlo, siga los siguientes pasos:

 1. Expanda el menú superior derecho y vaya a "Configuración".
 2. Navega a "Avanzado"
 3. Habilite la función "Usar Ledger Live"

Con esta función habilitada, MetaMask abrirá Ledger Live cuando intente conectarse a su dispositivo Ledger. Puede leer más sobre esto en esta [publicación de blog de MetaMask](https://metamask.zendesk.com/hc/en-us/articles/360020394612-How-to-connect-a-Trezor-or-Ledger-Hardware-Wallet).

## Firmar una transacción Ledger

Si ha importado con éxito [su cuenta de Ledger a MetaMask](#importing-your-ledger-account-to-metamask),  está listo para firmar transacciones en Moonbeam usando su dispositivo Ledger. Este tutorial le mostrará cómo enviar una transacción simple en Moonbase Alpha TestNet, pero se aplica a otras redes del ecosistema Moonbeam.

Primero, asegúrese de que su cuenta de Ledger esté [financiada con tokens DEV](/getting-started/moonbase/faucet/). A continuación, haga clic en el botón "Enviar".

![Cuenta MetaMask Ledger financiada](/images/ledger/ledger-images5.png)

Como lo haría en una transacción estándar, configure la dirección del destinatario, ingrese la cantidad de tokens para enviar, revise los detalles de la transacción y confírmela. Esto iniciará el asistente de firma de transacciones en su dispositivo Ledger. Aquí, siga los siguientes pasos:


 1. Haga clic en el botón para pasar a la siguiente pantalla. Su dispositivo Ledger solo le advierte que revise la transacción
 2. Verifique la cantidad de tokens que se envían. Tenga en cuenta que el token corresponde a la red a la que está conectado MetaMask. **En este caso, son tokens DEV y no ETH.** Cuando esté listo, pase a la siguiente pantalla
 3. Verifique la dirección del destinatario y pase a la siguiente pantalla
 4. Verifique el ID de cadena de la red. Esta información confirma a qué red está conectada MetaMask. Por ejemplo, para Moonbase Alpha, el ID de la cadena es 1287, Moonriver 1285 (aún no en vivo) y Moonbeam 1284 (aún no en vivo). Cuando esté listo, pase a la siguiente pantalla
 5. Verifique las tarifas máximas aplicables a esta transacción. Este es el precio de la gasolina multiplicado por el límite de gasolina que ha establecido en MetaMask. Cuando esté listo, pase a la siguiente pantalla
 6. Si está de acuerdo con todos los detalles de la transacción, apruebe. Esto firmará la transacción y hará que MetaMask la envíe. Por el contrario, pase a la siguiente pantalla
 7. Si no está de acuerdo con todos los detalles de la transacción, rechácela. Esto cancelará la transacción y MetaMask la marcará como fallida

!!! note
    En el momento de escribir este artículo, el nombre del token siempre se muestra como `ETH`.  Tenga en cuenta que el token que se está manejando es el correspondiente a la red a la que está conectado MetaMask.


![MetaMask Ledger Transacción Wizard](/images/ledger/ledger-images6.png)

Inmediatamente después de haber aprobado la transacción, MetaMask la envía a la red. Una vez que se confirma la transacción, se mostrará como "Enviar" en la pantalla principal de MetaMask.

![MetaMask Ledger Transacción Wizard](/images/ledger/ledger-images7.png)

¡Y eso es todo! ¡Ha firmado una transacción en Moonbase Alpha usando su billetera de hardware Ledger!

## Interactuar con contratos usando su Ledger

De forma predeterminada, los dispositivos Ledger no admiten un datacampo en el objeto de transacción. En consecuencia, los usuarios no pueden implementar contratos inteligentes ni interactuar con ellos.

Sin embargo, si desea utilizar su billetera de hardware Ledger para transacciones relacionadas con contratos inteligentes, debe cambiar un parámetro de configuración dentro de la aplicación Ethereum. Para hacerlo, siga los siguientes pasos:

 1. Abra la aplicación Ledger Ethereum
 2. Navega a "Configuración"
 3. Busque la página "Datos del contrato". Debe indicar "NO permitido" en la parte inferior.
 4. Seleccione / valide la opción para cambiar su valor a "Permitido"

!!! note
    Esta opción es necesaria para usar su dispositivo Ledger para interactuar con los contratos de token ERC20 que podrían vivir dentro del ecosistema Moonbeam.

![Libro mayor de MetaMask Permitir contratos Tx](/images/ledger/ledger-images8.png)
