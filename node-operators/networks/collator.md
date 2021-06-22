---
title: Collators
description: Instrucciones sobre cómo convertirse en un collator en Moonbeam Network una vez que esté ejecutando un nodo
---

# Ejecutar un intercalador en Moonbeam

![Collator Moonbeam Banner](/images/fullnode/collator-banner.png)

## Introducción

Los intercaladores son miembros de la red que mantienen las paracaídas en las que participan. Ejecutan un nodo completo (tanto para su parachain particular como para la cadena de relés) y producen la prueba de transición de estado para los validadores de cadenas de relés.

Con el lanzamiento de Moonbase Alpha v6, los usuarios pueden activar nodos completos y activar la `collate` función y participar en el ecosistema como agrupadores.

Esta guía lo guiará a través de los pasos para hacer girar su nodo intercalador, que es una extensión de un nodo completo.

## Requisitos tecnológicos

Desde una perspectiva técnica, las collators deben cumplir los siguientes requisitos:

 - Tener un nodo completo ejecutándose con las opciones de clasificación. Para hacerlo, siga el [tutorial completo de un nodo](/node-operators/networks/full-node/)  , considerando los fragmentos de código específicos para los collators
 - Habilite el servidor de telemetría para su nodo completo. Para hacerlo, sigue [este tutorial](/node-operators/networks/telemetry/)

## Requisitos de cuenta y participación

Al igual que los validadores de Polkadot, debe crear una cuenta (aunque en este caso, es una cuenta H160) y tener una participación nominada (tokens DEV) para poder recopilar. Actualmente, los espacios están limitados a, pero pueden aumentar con el tiempo.  

Los collators deben tener un mínimo de {{ networks.moonbase.staking.collator_min_stake }} DEV para ser considerados elegibles (convertirse en candidatos). Solo los {{ networks.moonbase.staking.max_collators }} mejores collators por participación nominada estarán en el grupo activo.  

!!! note
    Actualmente, crear o importar una cuenta en PolkadotJS a través de una semilla mnemotécnica dará como resultado una dirección pública diferente si luego intentas importar esta cuenta a una billetera Ethereum como MetaMask. Esto se debe a que PolkadotJS usa BIP39, mientras que Ethereum usa BIP32 o BIP44. 

## Cuenta en PolkadotJS

Un collator tiene una cuenta asociada con sus actividades de clasificación. Esta cuenta se usa para identificarlo como un productor de bloques y enviar los pagos de las recompensas del bloque.

Actualmente, tiene dos formas de proceder con respecto a tener una cuenta en [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/accounts):

 - Importar una cuenta H160 existente (o crear una nueva) desde carteras o servicios externos como [MetaMask](/integrations/wallets/metamask/) y [MathWallet](/integrations/wallets/mathwallet/)
 - Cree una nueva cuenta H160 con [PolkadotJS](/integrations/wallets/polkadotjs/)

Una vez que tenga una cuenta H160 importada a PolkadotJS, debería verla en la pestaña "Cuentas". Asegúrese de tener su dirección pública a mano (`PUBLIC_KEY`), ya que es necesaria para configurar su [implementación de su nodo completo](/node-operators/networks/full-node/) con las opciones de clasificación.

![Account in PolkadotJS](/images/fullnode/collator-polkadotjs1.png)

## Conviértase en candidato a collator

Una vez que su nodo se está ejecutando y sincronizado con la red, se convierte en un candidato de collator (y se une al grupo de candidatos) siguiendo los pasos a continuación en [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/accounts):

 1. Vaya a la pestaña "Desarrolladores" y haga clic en "Extrínsecos".
 2. Seleccione la cuenta que desea asociar con sus actividades de recopilación
 3. Confirme que su cuenta de compaginadora esté financiada con al menos {{ networks.moonbase.staking.collator_min_stake }} Tokens DEV más algunos extra por tarifas de transacción
 4. Seleccione la `parachainStaking` paleta en el menú "enviar los siguientes elementos extrínsecos"
 5. Abra el menú desplegable, que enumera todos los elementos extrínsecos posibles relacionados con el replanteo, y seleccione la `joinCandidates()` función
 6. Establezca la fianza en al menos {{ networks.moonbase.staking.collator_min_stake }},que es la cantidad mínima para ser considerado candidato a collator. Para este cheque, solo cuenta la fianza de alistador. Las nominaciones adicionales no cuentan
 7. Envíe la transacción. Siga el asistente y firme la transacción con la contraseña que estableció para la cuenta.

![Join Collators pool PolkadotJS](/images/fullnode/collator-polkadotjs2.png)

!!! note
    Los nombres de las funciones y el requisito de fianza mínima están sujetos a cambios en versiones futuras.

Como se mencionó anteriormente, solo los 32 mejores collators por participación nominada estarán en el conjunto activo.

## Dejar de clasificar

Similar a la `chill()` función de Polkadot , para salir del grupo de candidatos del collator, siga los mismos pasos que antes, pero seleccione la `leaveCandidates()` función en el paso 5.

## Tiempos

La siguiente tabla presenta algunos de los tiempos en lo que respecta a las diferentes acciones relacionadas con las actividades de recopilación:

|                Acción               |   |   Rondas  |   |   Horas  |
|:-----------------------------------:|:-:|:---------:|:-:|:--------:|
|  Unirse / dejar candidatos de collator |   |     2     |   |    4     |
|      Agregar / eliminar nominaciones |   |     1     |   |    2     |
|Pagos de recompensas (después de la ronda actual)|   |     2     |   |    4     |


!!! note 
    Los valores presentados en la tabla anterior están sujetos a cambios en versiones futuras.

