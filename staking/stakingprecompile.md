---
title: Precompilado de Staking 
description: Demostración de la interfaz de precompilación solidity de Moonbeam Parachain Staking Ethereum 
---

# Precompilado de Staking 

![Staking Moonbeam Banner](/images/staking/staking-precompile-banner.png)

## Introducción {: #introduction } 

Recientemente debutó una pallet de proof of stake llamada [Parachain-Staking](https://github.com/PureStake/moonbeam/tree/master/pallets/parachain-staking/src), que permite a los poseedores de tokens (nominadores) expresar exactamente a qué candidatos a collators les gustaría apoyar y con qué cantidad de stake.  El diseño de la pallet Parachain-Staking es tal que impone riesgo / recompensa compartido en la cadena entre delegadores y collators.

El módulo Staking está codificado en Rust y es parte de una pallet que normalmente no es accesible desde el lado Ethereum de Moonbeam. Sin embargo, una precompilación de Staking  permite a los desarrolladores acceder a las funciones de staking utilizando la API de Ethereum en un contrato precompilado ubicado en la dirección `{{networks.moonbase.staking.precompile_address}}`. La precompilación de Staking se lanzó por primera vez en el [lanzamiento de Moonbase Alpha v8](https://moonbeam.network/announcements/testnet-upgrade-moonbase-alpha-v8/).

## La interfaz Solidity de Parachain-Staking {: #the-parachainstaking-solidity-interface } 

[StakingInterface.sol](https://github.com/PureStake/moonbeam/blob/master/precompiles/parachain-staking/StakingInterface.sol) es una interfaz a través de la cual los contratos de solidity contracts pueden interactuar con Parachain-Staking.La belleza es que los desarrolladores de solidity no tienen que aprender la API de Substrate. En cambio, pueden interactuar con las funciones de staking utilizando la interfaz de Ethereum con la que están familiarizados.

La interfaz incluye las siguientes funciones:

 - **is_nominator**(*address* collator) — función de solo lectura que comprueba si la dirección especificada es actualmente un nominador en staking 
 - **is_candidate**(*address* collator) — función de solo lectura que comprueba si la dirección especificada es actualmente un candidato a collator
 - **min_nomination**() — función de solo lectura que obtiene la cantidad mínima de nominación
 - **join_candidates**(*uint256* amount) — permite que la cuenta se una al conjunto de collators candidatos con un monto bond específico
 - **leave_candidates**() — elimina inmediatamente la cuenta del grupo de candidatos para evitar que otros la seleccionen como collator desencadena el unbonding despues que hayan transcurrido las rondas de BondDuration
 - **go_offline**() — deja temporalmente el set de los collators candidatos sin realizar el unbonding
 - **go_online**() — vuelve a unirse al set de lo collators candidatos después de llamar la acción  go_offline()
 - **candidate_bond_more**(*uint256* more) — el collator candidato incrementa el bond en un monto específico 
 - **candidate_bond_less**(*uint256* less) —el collator candidato reduce el bond en un monto específico 
 - **nominate**(*address* collator, *uint256* amount) — sí el caller no es un nominador,esta función los agrega al conjunto de nominadores. Sí el  caller ya es un nominador, entonces ajusta el monto de su nominación.
 - **leave_nominators**() — abandona el conjunto de nominadores y revoca todas las nominaciones en curso.
 - **revoke_nominations**(*address* collator) — revocar una nominación específica
 - **nominator_bond_more**(*address* collator, *uint256* more) — el nominador incrementa el bond en un monto específico 
 - **nominator_bond_less**(*address* collator, *uint256* less) — el nominador reduce el bond en un monto específico 

## Interactuando con la precompilación de Staking {: #interacting-with-the-staking-precompile } 

### Comprobación de requisitos previos {: #checking-prerequisites } 
El siguiente ejemplo se demuestra en Moonbase Alpha, sin embargo, es compatible con todas las redes, incluidas Moonriver y Moonbeam.

 - Tener MetaMask instalado y [conectado a Moonbase Alpha](/getting-started/moonbase/metamask/)
 - Tener una cuenta con más de `{{networks.moonbase.staking.min_nom_stake}}` tokens. Puede obtener esto de [Mission Control](/getting-started/moonbase/faucet/)

!!! nota
    El siguiente ejemplo requiere más que `{{networks.moonbase.staking.min_nom_stake}}` debido al monto mínimo de nominación más las tarifas de gas. Si necesita mas que lo dispuesto por la faucet, comuníquese con nosotros en Discord y estaremos encantados de ayudarlo. 

### Remix Set Up {: #remix-set-up } 
1. Obtenga una copia de [StakingInterface.sol](https://github.com/PureStake/moonbeam/blob/master/precompiles/parachain-staking/StakingInterface.sol)
2. Copie y pegue el contenido del archivo en un archivo Remix llamado StakingInterface.sol

![Copying and Pasting the Staking Interface into Remix](/images/staking/staking-precompile-1.png)

### Compilar el contrato {: #compile-the-contract } 
1. Haga clic en la pestaña Compilar, la segunda desde arriba
2. Compile [Staking Interface.sol](https://github.com/PureStake/moonbeam/blob/master/precompiles/parachain-staking/StakingInterface.sol)

![Compiling StakingInteface.sol](/images/staking/staking-precompile-2.png)

### Acceder al contrato {: #access-the-contract } 
1. Haga clic en la pestaña Implementar y ejecutar, directamente debajo de la pestaña Compilar en Remix. **Nota**: no estamos implementando un contrato aquí, sino que estamos accediendo a un contrato precompilado que ya está implementado
2. Asegúrese de que "Injected Web3" esté seleccionado en el menú desplegable Environment 
3. Asegúrese de que "ParachainStaking - StakingInterface.sol" esté seleccionado en el menú desplegable Contract. Dado que se trata de un contrato precompilado, no es necesario implementarlo, sino que proporcionaremos la dirección de la precompilación en el campo  “At Address” 
4. Proporcione la dirección de la precompilación de Staking: `{{networks.moonbase.staking.precompile_address}}` y haga clic en “At Address”

![Provide the address](/images/staking/staking-precompile-3.png)

### Nominar a  un Collator {: #nominate-a-collator } 
Para este ejemplo, vamos a nominar a un collator. Los nominadores son poseedores de token que hacen stake ,dando fe de collators específicos. Cualquier usuario que tenga una cantidad mínima de {{networks.moonbase.staking.min_nom_stake}} tokens como free balance puede convertirse en nominador. 

1. Expanda el panel con la dirección del contrato. Localice la función nominar y expanda el panel para ver los parámetros
2. Proporcione la dirección de un collator como `{{ networks.moonbase.staking.collators.address1 }}`
3. Proporcione la cantidad a nominar en WEI. Hay un mínimo de  `{{networks.moonbase.staking.min_nom_stake}}` tokens para nominar, por lo que la cantidad más baja en WEI es `5000000000000000000`
4. Presione "transact" y confirme la transacción en Metamask

![Nominate a Collator](/images/staking/staking-precompile-4.png)

### Verificar nominación {: #verify-nomination } 

Para verificar que su nominación fue exitosa, puede verificar el estado de la cadena en Polkadot.js Apps. Primero, agregue su dirección de metamask a la [address book en Polkadot.js Apps](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/addresses). Si ya ha completado este paso, puede pasar a la siguiente sección.

#### Añadir direcciones Metamask al Address Book {: #add-metamask-address-to-address-book } 
1. Vaya a Cuentas -> Address Book 
2. Clic en "Add contact"
3. Agregue su dirección de Metamask
4. Proporcione un apodo para la cuenta.

![Add to Address Book](/images/staking/staking-precompile-5.png)

#### Verificar el estado del nominador {: #verify-nominator-state } 
1. Para verificar que su nominación fue exitosa, diríjase a [Polkadot.js Apps](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fwss.testnet.moonbeam.network#/chainstate) y navegue a Desarrollador -> Estado de la cadena
2. Seleccione la pallet "parachainStaking" 
3. Seleccione el query "nominatorState" 
4. Haga clic en el botón "Más" para devolver los resultados y verificar su nominación

![Verify Nomination](/images/staking/staking-precompile-6.png)

### Revocación de una nominación {: #revoking-a-nomination } 

Para revocar una nominación y recibir sus tokens,call el metodo `revoke_nomination`, proporcionando la misma dirección con la que comenzó la nominación arriba. Puede verificar el estado de su nominador nuevamente en Polkadot.js Apps para confirmar.

![Revoke Nomination](/images/staking/staking-precompile-7.png)
