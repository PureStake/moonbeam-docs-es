---
title: Cómo Hacer Staking
description: Una guía que muestra cómo puede apostar sus tokens en Moonbeam al nominar a los collators
---

# Cómo apostar tus tokens

![Staking Moonbeam Banner](/images/staking/staking-stake-banner.png)

## Introducción {: #introduction } 

Los collators (productores de bloques) con la participación más alta en la red se unen al grupo activo de collators, del cual son seleccionados para ofrecer un bloque a la cadena de relevo.

Los poseedores de tokens pueden agregar a la participación de los collators usando sus tokens, un proceso llamado nominación (también conocido como participación). Cuando lo hacen, responden por ese collator específico, y su nominación es una señal de confianza.

Los collators reciben parte de las recompensas en bloque como parte del modelo inflacionario simbólico. Pueden compartirlos como recompensas de apuesta con sus nominadores, considerando su contribución porcentual a su participación en la red.

Con el lanzamiento de [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0), los usuarios de la red ahora pueden apostar sus tokens para nominar a los collators. Esta guía describe todos los pasos para hacerlo.

## Definiciones generales {: #general-definitions } 

--8<-- 'text/staking/staking-definitions.md'

Actualmente, para Moonbase Alpha:

|             Variable             |     |                         Valor                        |
| :------------------------------: | :-: | :---------------------------------------------------: |
|     Participación mínima de nominación	     |     |     5     |
|        Nominación mínima        |     |     5    | 
| Máximo de collators por nominador |     |     25     |
|              Redondo               |     | 300 bloques (1 hora) |
|          Duración del bono           |     |     2 rondas  |

## Definiciones de extrínsecos {: #extrinsics-definitions } 

Hay muchos aspectos extrínsecos relacionados con el pallet de replanteo, por lo que no todos se tratan en esta guía. Sin embargo, esta lista define todos los aspectos extrínsecos asociados con el proceso de nominación:

!!! nota
    Los extrínsecos pueden cambiar en el futuro a medida que se actualice la pallet de replanteo
    
 - **nominar** — dos entradas: dirección del collator a nominar y monto. Extrínseco para nominar a un collator. La cantidad debe ser de al menos 5 tokens
 - **leaveNominators** — sin entradas. Extrínseco para dejar el conjunto de nominadores. En consecuencia, todas las nominaciones en curso serán revocadas.
 - **nominatorBondLess** — dos entradas: dirección de un intercalador designado y monto. Extrínseco para reducir la cantidad de tokens apostados para un collator ya nominado. La cantidad no debe disminuir el total total apostado por debajo de 5 tokens.
 - **nominatorBondMore** — dos entradas: dirección de un collator designado y monto. Extrínseco para aumentar la cantidad de tokens apostados para un collator ya nominado
 - **revokeNomination** — una entrada: dirección de un collator designado. Extrínseco para eliminar una nominación existente

## Recuperar la lista de collators {: #retrieving-the-list-of-collators } 

Antes de comenzar a apostar tokens, es importante recuperar la lista de collators disponibles en la red. Para hacerlo, navegue hasta "Estado de la cadena" en la pestaña "Desarrollador".

![Staking Account](/images/staking/staking-stake-10.png)

Aquí, proporcione la siguiente información:

 1. Head to the "Developer" tab 
 2. Click on "Chain State"
 3. Choose the pallet to interact with. In this case, it is the `parachainStaking` pallet
 4. Choose the state to query. In this case, it is the `selectedCandidates` or `candidatePool` state
 5. Send the state query by clicking on the "+" button
 
Cada extrínseco proporciona una respuesta diferente:

 - **selectedCandidates** — devuelve el conjunto activo actual de collators, es decir, los 32 collators principales por tokens totales apostados (incluidas las nominaciones)
 - **candidatePool** — devuelve la lista actual de todos los collators, incluidos los que no están en el conjunto activo.

![Staking Account](/images/staking/staking-stake-2.png)

## Get the Collator Nominator Count {: #get-the-collator-nominator-count }

First, you need to get the `collator_nominator_count` as you'll need to submit this parameter in a later transaction. To do so, you'll have to run the following JavaScript code snippet from within [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/js):



```js
// Simple script to get collator_nominator_count
// Remember to replace COLLATOR_ADDRESS with the address of desired collator.
const collatorAccount = 'COLLATOR_ADDRESS'; 
const collatorInfo = await api.query.parachainStaking.collatorState2(collatorAccount);
console.log(collatorInfo.toHuman()["nominators"].length);
```

 1. Head to the "Developer" tab 
 2. Click on "JavaScript"
 3. Copy the code from the previous snippet and paste it inside the code editor box 
 4. (Optional) Click the save icon and set a name for the code snippet, for example, "Get collator_nominator_count". This will save the code snippet locally
 5. Click on the run button. This will execute the code from the editor box
 6. Copy the result, as you'll need it when initiating a nomination

![Get collator nominator count](/images/staking/staking-stake-3.png)

## Get your Number of Existing Nominations {: #get-your-number-of-existing-nominations }
If you've never made a nomination from your address you can skip this section. However, if you're unsure how many existing nominations you have, you'll want to run the following JavaScript code snippet to get `nomination_count` from within [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/js):

```js
// Simple script to get your number of existing nominations.
// Remember to replace YOUR_ADDRESS_HERE with your nominator address.
const yourNominatorAccount = 'YOUR_ADDRESS_HERE'; 
const nominatorInfo = await api.query.parachainStaking.nominatorState(yourNominatorAccount);
console.log(nominatorInfo.toHuman()["nominations"].length);
```

 1. Head to the "Developer" tab 
 2. Click on "JavaScript"
 3. Copy the code from the previous snippet and paste it inside the code editor box 
 4. (Optional) Click the save icon and set a name for the code snippet, for example, "Get existing nominations". This will save the code snippet locally
 5. Click on the run button. This will execute the code from the editor box
 6. Copy the result, as you'll need it when initiating a nomination

![Get existing nomination count](/images/staking/staking-stake-4.png)

## Cómo nominar un collator {: #how-to-nominate-a-collator } 

Esta sección repasa el proceso de nominación de collators. El tutorial utilizará los siguientes collators como referencia:

|  Variable  |     |                      Habla a                      |
| :--------: | :-: | :------------------------------------------------: |
| Collator 1 |     | 0x4c5A56ed5A4FF7B09aA86560AfD7d383F4831Cce |
| Collator 2 |     | 0x62d2e7324f9274fac3893a59aff8e944a323a495 |

Para acceder a las funciones de replanteo, debe utilizar la interfaz de PolkadotJS Apps. Para hacerlo, primero debe importar / crear una cuenta estilo Ethereum (dirección H160), lo que puede hacer siguiendo [esta guía](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account).

Para este ejemplo, se importó una cuenta y se nombró con un nombre súper original: Alice.

Actualmente, se debe acceder a todo lo relacionado con el replanteo a través del menú "Extrínsecos", en la pestaña "Desarrollador":

![Staking Account](/images/staking/staking-stake-5.png)

Para designar un collator, proporcione la siguiente información:

 1. Seleccione la cuenta desde la que desea apostar sus tokens
 2. Elija la pallet con la que desea interactuar. En este caso, es el `parachainStaking` palet
 3. Elija el método extrínseco que se utilizará para la transacción. Esto determinará los campos que deben completarse en los siguientes pasos. En este caso, es el `nominate` extrínseco
 4. Establezca la dirección del collator que desea nominar. En este caso, se establece en `0x4c5A56ed5A4FF7B09aA86560AfD7d383F4831Cce`
 5. Establezca la cantidad de tokens que desea apostar
 6. Input the `collator_nominator_count` you [retrieved above from the JavaScript console](/staking/stake/#get-the-collator-nominator-count)
 7. Input the `nomination_count` [you retrieved from the Javascript console](/staking/stake/#get-your-number-of-existing-nominations). This is `0` if you haven't yet nominated a collator
 8. Click the "Submit Transaction" button and sign the transaction

![Staking Join Nominators Extrinsics](/images/staking/staking-stake-6.png)

!!! note
    The parameters used in steps 6 and 7 are for gas estimation purposes and do not need to be exact. However, they should not be lower than the actual values. 

Una vez que se confirma la transacción, puede regresar a la pestaña "Cuentas" para verificar que tiene un saldo reservado (igual a la cantidad de tokens apostados).

Para verificar una nominación, puede navegar hasta "Estado de la cadena" en la pestaña "Desarrollador".

![Staking Account and Chain State](/images/staking/staking-stake-7.png)

Aquí, proporcione la siguiente información:

 1. Elija la pallet con la que desea interactuar. En este caso, es el `parachainStaking` palet
 2. Choose the state to query. In this case, it is the `nominatorState`
 3. Make sure to enable the "include option" slider
 4. Envíe la consulta de estado haciendo clic en el botón "+"

![Staking Chain State Query](/images/staking/staking-stake-8.png)

En la respuesta, debería ver su cuenta (en este caso, la cuenta de Alice) con una lista de las nominaciones. Cada nominación contiene la dirección de destino del collator y la cantidad.

Puede seguir los mismos pasos que se describen para designar otros collators en la red. Por ejemplo, Alice también nominó `0x62d2e7324f9274fac3893a59aff8e944a323a495`.

## Cómo detener las nominaciones {: #how-to-stop-nominations } 

Si ya es un nominador, tiene dos opciones para detener sus nominaciones: usar el `revokeNomination` extrínseco para retirar sus tokens de un collator específico, o usar el `leaveNominators` extrínseco para revocar todas las nominaciones en curso.

Este ejemplo es una continuación de la sección anterior y se supone que tiene al menos dos nominaciones activas.

Puede eliminar su nominación de un collator específico navegando al menú "Extrínsecos" en la pestaña "Desarrollador". Aquí, proporcione la siguiente información:

 1. Seleccione la cuenta de la que desea eliminar su nominación
 2. Elija la pallet con la que desea interactuar. En este caso, es el `parachainStaking` palet
 3. Elija el método extrínseco que se utilizará para la transacción. Esto determinará los campos que deben completarse en los siguientes pasos. En este caso, es el `revokeNomination` extrínseco
 4. Establezca la dirección del collator de la que desea eliminar su nominación. En este caso, se establece en `0x62d2e7324f9274fac3893a59aff8e944a323a495`
 5. Haga clic en el botón "Enviar transacción" y firme la transacción.

![Staking Revoke Nomination Extrinsic](/images/staking/staking-stake-9.png)

Una vez que se confirma la transacción, puede verificar que su nominación fue eliminada en la opción "Estado de la cadena" en la pestaña "Desarrollador".

Aquí, proporcione la siguiente información:

 1. Elija la pallet con la que desea interactuar. En este caso, es el `parachainStaking` palet
 2. Elija el estado a consultar. En este caso, es el `nominatorState` estado
 3. Asegúrate de deshabilitar el control deslizante "incluir opciones".
 4. Envíe la consulta de estado haciendo clic en el botón "+"

![Staking Revoke Nomination Chain State](/images/staking/staking-stake-8.png)

En la respuesta, debería ver su cuenta (en este caso, la cuenta de Alice) con una lista de las nominaciones. Cada nominación contiene la dirección de destino del collator y la cantidad.

Como se mencionó anteriormente, también puede eliminar todas las nominaciones en curso con la `leaveNominators` extrínseca (en el paso 3 de las instrucciones "Extrínsecas"). Este extrínseco no requiere entrada:

![Staking Leave Nominatiors Extrinsic](/images/staking/staking-stake-10.png)

Una vez que se confirma la transacción, su cuenta no debe aparecer en el `nominatorState` estado cuando se le solicite, y no debe tener saldo reservado (relacionado con la participación).

## Recompensas de apuesta {: #staking-rewards } 

A medida que los collators reciben recompensas por la producción de bloques, los nominadores también obtienen recompensas. En [esta página](/staking/overview/#reward-distribution) se puede encontrar una breve descripción general de cómo se calculan las recompensas.

En resumen, los nominadores obtendrán recompensas en función de su participación en el total de nominaciones para el collator que se recompensa (incluida la participación del collator también).

En el ejemplo anterior, Alice fue recompensada con `0.0044` tokens después de dos rondas de pago:

![Staking Reward Example](/images/staking/staking-stake-1.png)
