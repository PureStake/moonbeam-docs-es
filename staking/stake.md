---
title: Cómo apostar
description: Una guía que muestra cómo puede apostar sus tokens en Moonbeam al nominar a los alzadores
---

# Cómo apostar tus tokens

![Staking Moonbeam Banner](/images/staking/staking-stake-banner.png)

## Introducción

Los alzadores (productores de bloques) con la participación más alta en la red se unen al grupo activo de alzadores, del cual son seleccionados para ofrecer un bloque a la cadena de relevo.

Los poseedores de tokens pueden agregar a la participación de los clasificadores usando sus tokens, un proceso llamado nominación (también conocido como participación). Cuando lo hacen, responden por ese clasificador específico, y su nominación es una señal de confianza.

Los clasificadores reciben parte de las recompensas en bloque como parte del modelo inflacionario simbólico. Pueden compartirlos como recompensas de apuesta con sus nominadores, considerando su contribución porcentual a su participación en la red.

Con el lanzamiento de [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0), los usuarios de la red ahora pueden apostar sus tokens para nominar a los clasificadores. Esta guía describe todos los pasos para hacerlo.

## Definiciones generales

--8<-- 'text/staking/staking-definitions.md'

Actualmente, para Moonbase Alpha:

|             Variable             |     |                         Valor                        |
| :------------------------------: | :-: | :---------------------------------------------------: |
|     Participación mínima de nominación	     |     |     5     |
|        Nominación mínima        |     |     5    | 
| Máximo de alzadores por nominador |     |     25     |
|              Redondo               |     | 300 bloques (1 hora) |
|          Duración del bono           |     |     2 rondas  |

## Definiciones de extrínsecos

Hay muchos aspectos extrínsecos relacionados con el palé de replanteo, por lo que no todos se tratan en esta guía. Sin embargo, esta lista define todos los aspectos extrínsecos asociados con el proceso de nominación:

!!! nota
    Los extrínsecos pueden cambiar en el futuro a medida que se actualice la paleta de replanteo
    
 - **nominar** — dos entradas: dirección del clasificador a nominar y monto. Extrínseco para nominar a un clasificador. La cantidad debe ser de al menos {{ networks.moonbase.staking.min_nom_amount }} tokens
 - **leaveNominators** — sin entradas. Extrínseco para dejar el conjunto de nominadores. En consecuencia, todas las nominaciones en curso serán revocadas.
 - **nominatorBondLess** — dos entradas: dirección de un intercalador designado y monto. Extrínseco para reducir la cantidad de tokens apostados para un clasificador ya nominado. La cantidad no debe disminuir el total total apostado por debajo de {{ networks.moonbase.staking.min_nom_stake }} tokens.
 - **nominatorBondMore** — dos entradas: dirección de un clasificador designado y monto. Extrínseco para aumentar la cantidad de tokens apostados para un clasificador ya nominado
 - **revokeNomination** — una entrada: dirección de un clasificador designado. Extrínseco para eliminar una nominación existente

## Recuperar la lista de clasificadores

Antes de comenzar a apostar tokens, es importante recuperar la lista de clasificadores disponibles en la red. Para hacerlo, navegue hasta "Estado de la cadena" en la pestaña "Desarrollador".

![Staking Account](/images/staking/staking-stake-10.png)

Aquí, proporcione la siguiente información:

 1. Elija el palet con el que interactuar. En este caso, es el `parachainStaking` palet
 2. Elija el estado a consultar. En este caso, es el estado `selectedCandidates` o `candidatePool` 
 3. Envíe la consulta de estado haciendo clic en el botón "+"
 
Cada extrínseco proporciona una respuesta diferente:

 - **selectedCandidates** — devuelve el conjunto activo actual de clasificadores, es decir, los {{ networks.moonbase.staking.max_collators }} clasificadores principales por tokens totales apostados (incluidas las nominaciones)
 - **candidatePool** — devuelve la lista actual de todos los clasificadores, incluidos los que no están en el conjunto activo.

![Staking Account](/images/staking/staking-stake-11.png)

## Cómo nominar un clasificador

Esta sección repasa el proceso de nominación de clasificadores. El tutorial utilizará los siguientes clasificadores como referencia:

|  Variable  |     |                      Habla a                      |
| :--------: | :-: | :------------------------------------------------: |
| Clasificador 1 |     | 0x4c5A56ed5A4FF7B09aA86560AfD7d383F4831Cce |
| Clasificador 2 |     | 0x62d2e7324f9274fac3893a59aff8e944a323a495 |

Para acceder a las funciones de replanteo, debe utilizar la interfaz de PolkadotJS Apps. Para hacerlo, primero debe importar / crear una cuenta estilo Ethereum (dirección H160), lo que puede hacer siguiendo [esta guía](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account).

Para este ejemplo, se importó una cuenta y se nombró con un nombre súper original: Alice.

Actualmente, se debe acceder a todo lo relacionado con el replanteo a través del menú "Extrínsecos", en la pestaña "Desarrollador":

![Staking Account](/images/staking/staking-stake-1.png)

Para designar un clasificador, proporcione la siguiente información:

 1. Seleccione la cuenta desde la que desea apostar sus tokens
 2. Elija la paleta con la que desea interactuar. En este caso, es el `parachainStaking` palet
 3. Elija el método extrínseco que se utilizará para la transacción. Esto determinará los campos que deben completarse en los siguientes pasos. En este caso, es el `nominate` extrínseco
 4. Establezca la dirección del clasificador que desea nominar. En este caso, se establece en `0x4c5A56ed5A4FF7B09aA86560AfD7d383F4831Cce`
 5. Establezca la cantidad de tokens que desea apostar
 6. Haga clic en el botón "Enviar transacción" y firme la transacción.
 
![Staking Join Nominators Extrinsics](/images/staking/staking-stake-2.png)

Una vez que se confirma la transacción, puede regresar a la pestaña "Cuentas" para verificar que tiene un saldo reservado (igual a la cantidad de tokens apostados).

Para verificar una nominación, puede navegar hasta "Estado de la cadena" en la pestaña "Desarrollador".

![Staking Account and Chain State](/images/staking/staking-stake-3.png)

Aquí, proporcione la siguiente información:

 1. Elija la paleta con la que desea interactuar. En este caso, es el `parachainStaking` palet
 2. Elija el estado a consultar. En este caso, es el `nominators` estado
 3. Asegúrate de deshabilitar el control deslizante "opción de inclusión".
 4. Envíe la consulta de estado haciendo clic en el botón "+"

![Staking Chain State Query](/images/staking/staking-stake-4.png)

En la respuesta, debería ver su cuenta (en este caso, la cuenta de Alice) con una lista de las nominaciones. Cada nominación contiene la dirección de destino del clasificador y la cantidad.

Puede seguir los mismos pasos que se describen para designar otros clasificadores en la red. Por ejemplo, Alice también nominó `0x62d2e7324f9274fac3893a59aff8e944a323a495`.

## Cómo detener las nominaciones

Si ya es un nominador, tiene dos opciones para detener sus nominaciones: usar el `revokeNomination` extrínseco para retirar sus tokens de un clasificador específico, o usar el `leaveNominators` extrínseco para revocar todas las nominaciones en curso.

Este ejemplo es una continuación de la sección anterior y se supone que tiene al menos dos nominaciones activas.

Puede eliminar su nominación de un clasificador específico navegando al menú "Extrínsecos" en la pestaña "Desarrollador". Aquí, proporcione la siguiente información:

 1. Seleccione la cuenta de la que desea eliminar su nominación
 2. Elija la paleta con la que desea interactuar. En este caso, es el `parachainStaking` palet
 3. Elija el método extrínseco que se utilizará para la transacción. Esto determinará los campos que deben completarse en los siguientes pasos. En este caso, es el `revokeNomination` extrínseco
 4. Establezca la dirección del clasificador de la que desea eliminar su nominación. En este caso, se establece en `{{ networks.moonbase.staking.collators.address2 }}`
 5. Haga clic en el botón "Enviar transacción" y firme la transacción.

![Staking Revoke Nomination Extrinsic](/images/staking/staking-stake-7.png)

Una vez que se confirma la transacción, puede verificar que su nominación fue eliminada en la opción "Estado de la cadena" en la pestaña "Desarrollador".

Aquí, proporcione la siguiente información:

 1. Elija la paleta con la que desea interactuar. En este caso, es el `parachainStaking` palet
 2. Elija el estado a consultar. En este caso, es el `nominatorState` estado
 3. Asegúrate de deshabilitar el control deslizante "incluir opciones".
 4. Envíe la consulta de estado haciendo clic en el botón "+"

![Staking Revoke Nomination Cain State](/images/staking/staking-stake-8.png)

En la respuesta, debería ver su cuenta (en este caso, la cuenta de Alice) con una lista de las nominaciones. Cada nominación contiene la dirección de destino del clasificador y la cantidad.

Como se mencionó anteriormente, también puede eliminar todas las nominaciones en curso con la `leaveNominators` extrínseca (en el paso 3 de las instrucciones "Extrínsecas"). Este extrínseco no requiere entrada:

![Staking Leave Nominatiors Extrinsic](/images/staking/staking-stake-9.png)

Una vez que se confirma la transacción, su cuenta no debe aparecer en el `nominatorState` estado cuando se le solicite, y no debe tener saldo reservado (relacionado con la participación).

## Recompensas de apuesta

A medida que los clasificadores reciben recompensas por la producción de bloques, los nominadores también obtienen recompensas. En [esta página](/staking/overview/#reward-distribution) se puede encontrar una breve descripción general de cómo se calculan las recompensas.

En resumen, los nominadores obtendrán recompensas en función de su participación en el total de nominaciones para el clasificador que se recompensa (incluida la participación del clasificador también).

En el ejemplo anterior, Alice fue recompensada con `0.0044` tokens después de dos rondas de pago:

![Staking Reward Example](/images/staking/staking-stake-10.png)
