---
title: TestNet
description: Una descripción general de la configuración actual de Moonbeam TestNet, Moonbase Alpha e información sobre cómo comenzar a construir sobre ella usando Solidity.
---

# Moonbase Alpha TestNet

_Updated April 5, 2021_

## Objetivo

La primera Moonbeam TestNet, llamada Moonbase Alpha, tiene como objetivo proporcionar a los desarrolladores un lugar para comenzar a experimentar y construir en Moonbeam en un entorno compartido. Dado que Moonbeam se implementará como una parachain en Kusama y Polkadot, queremos que nuestra TestNet refleje nuestra configuración. Por esta razón, decidimos que tenía que ser una configuración basada en parachain  en lugar de una configuración de desarrollo de substrate.

Para recopilar la mayor cantidad de comentarios posible y proporcionar una resolución rápida de problemas, hemos configurado en [Discord un canal dedicado a Moonbase AlphaNet.](https://discord.gg/PfpUATX).

## Configuración inicial

Moonbase Alpha tiene la siguiente configuración:

 - Moonbeam funciona como una parachain conectada a una Relay Chain.
 - La  parachain tiene dos collators (alojados por PureStake) que son collating blocks. Los collators externos pueden unirse a la red. Solo el top  {{ networks.moonbase.staking.max_collators }} de los nodos collators son los elegidos, esto es en base a stake.
 - La Relay Chain, aloja tres validadores (alojados por PureStake) para finalizar los bloques de la relay chain. Uno de ellos es seleccionado para finalizar cada bloque recopilado por los collators de Moonbeam. Esta configuración proporciona espacio para expandirse a una configuración de dos parachains en el futuro.
 - Hay dos RPC endpoints (alojados por PureStake). Las personas pueden ejecutar nodos completos para acceder a sus propios RPC endpoints privados

![TestNet Diagram](/images/testnet/Moonbase-Alpha-v7.png)

## Características o Funciones

Están disponibles las siguientes funciones:

??? release v1 "_Septiembre de 2020_"
    - Producción de bloques Ethereum totalmente emulada en substrate (paleta Ethereum).
    - Funciones despachables para interactuar con la implementación de Rust EVM [paleta EVM]
(https://docs.rs/pallet-evm/2.0.1/pallet_evm/))
    - Soporte nativo de Ethereum RPC (Web3) en Substrate ([Frontier](https://github.com/paritytech/frontier)). Esto proporciona compatibilidad con las herramientas de desarrollo de Ethereum como MetaMask, Remix y Truffle.

??? release v2 "_Octubre de 2020_"
    - Soporte de suscripción de eventos (pub/sub), que es un componente faltante en el lado de Web3 RPC y comúnmente utilizado por los desarrolladores de DApp. Puede encontrar un tutorial sobre cómo suscribirse a eventos [aquí](/integrations/pubsub/)
    - Soporte para los siguientes contratos precompilados: [ecrecover](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-01-ecrecover-hash-v-r-s), [sha256](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-02-sha-256-data), [ripemd160](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-03-ripemd-160-data) y la [función de identidad](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-04-datacopy-data) (o copia de datos)

??? release v3 "_Noviembre 2020_"
    - Unificación de cuentas de Substrate y Ethereum bajo el formato H160, un esfuerzo que llamamos [Cuentas Unificadas](https://medium.com/moonbeam-network/moonbase-alpha-v3-introducing-unified-accounts-88fae3564cda). En consecuencia, sólo habrá un tipo de cuenta en el sistema representada por una única dirección
    - Actualizaciones al soporte de suscripción de eventos, agregando la posibilidad de usar comodines y formato condicional para temas. Puedes encontrar más información [aquí](https://docs.moonbeam.network/integrations/pubsub/#using-wildcards-and-conditional-formatting)
    - Polkadot JS Apps ahora admite de forma nativa direcciones H160 y llaves ECDSA. Puede usar su dirección estilo Ethereum para funciones de substrate (cuando estén disponibles) como staking, saldos y gobernanza. Puedes encontrar más información [aquí](/integrations/wallets/polkadotjs/)

??? release v4 "_Deciembre 2020_"
    - Actualizado a la versión más reciente del protocolo de parachain de Polkadot ([Parachains V1](https://w3f.github.io/parachain-implementers-guide/)), que solucionó varios problemas con la sincronización de nodos, allanando el camino para tener múltiples collators para sincronizar en la misma parachain
    - Varias mejoras en nuestras funciones de compatibilidad con Ethereum:
        * El ID de suscripción de evento ahora regresa  una ID estilo Ethereum
        * Problemas de estimación de gas(fees) solucionados para casos de uso específicos
        * Se agregó soporte para el mensaje de motivo de reversión
        * Soporte para transacciones Ethereum sin ChainId

??? release v5 "_Enero de 2021_"      
    - Se agregó una versión personalizada de la [paleta Staking ](https://wiki.polkadot.network/docs/en/learn-staking) (solo para fines de prueba y desarrollo)
    - Se agregó soporte para consultar transacciones pendientes mientras están en el pool.
    - Se corrigieron algunos problemas sobre eventos pasados y otras correcciones menores relacionadas con los contratos inteligentes
    - Varias mejoras internas que incluyen una optimización del tiempo de ejecución de EVM, lo que lo hace entre 15 y 50 veces más rápido
    - Soporte para los contratos precompilados [modexp](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x05-bigmodexp-base-exp-mod)

??? release v6 "_Febrero de 2021_"      
    - Lanzamiento público de la  [paleta de Staking](https://wiki.polkadot.network/docs/en/learn-staking) personalizada. Ahora los titulares de tokens pueden nominar a los collators y ganar recompensas
    - Añadida la [paleta de Democracia](https://github.com/paritytech/substrate/tree/HEAD/frame/democracy). Los titulares de tokens pueden [enviar propuestas](/governance/proposals/) y [votar sobre ellas](/governance/voting/)
    - Actualizado a la última versión de [Frontier RPC](https://github.com/paritytech/frontier), que aumenta la eficiencia de ejecución de EVM en un factor de 5
    - El límite de gas(fees) se ha elevado a 15M por bloque, con un límite de 13M por transacción.

??? release v7 "_Abril de 2021_"      
    - Se agregó soporte para los módulos de depuración / seguimiento de Ethereum. Estos están desactivados de forma predeterminada, para usarlos debe activar un nodo completo y activar la función
    - Se corrigieron problemas de propagación de bloques para que ya no se limite a los collators, mejorando la estabilidad de la red
    - Se agregaron el “Consejo y el Comité Técnico”, ampliando las funciones de gobernanza
    - El módulo de Staking se ha refactorizado, con nuevos nombres para mejorar la experiencia del usuario final
    - Se agregaron tres nuevas precompilaciones: [Bn128Add](https://eips.ethereum.org/EIPS/eip-196), [Bn128Mul](https://eips.ethereum.org/EIPS/eip-196) and [Bn128Pairing](https://eips.ethereum.org/EIPS/eip-197)

### Notas de lanzamiento

Para obtener más detalles sobre las actualizaciones de Moonbase Alpha, consulte las siguientes notas de la versión:

 - [Moonbase Alpha v2](https://github.com/PureStake/moonbeam/releases/tag/v0.2.0)
 - [Moonbase Alpha v3](https://github.com/PureStake/moonbeam/releases/tag/v0.3.0)
 - [Moonbase Alpha v4](https://github.com/PureStake/moonbeam/releases/tag/v0.4.0)
 - [Moonbase Alpha v5](https://github.com/PureStake/moonbeam/releases/tag/v0.5.0)
 - [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0)
 - [Moonbase Alpha v7](https://github.com/PureStake/moonbeam/releases/tag/v0.7.0)

### Versiones futuras

Características que pueden implementarse en el futuro:

 - Funciones de tesorería con la ([paleta de Tesorería](https://github.com/paritytech/substrate/tree/master/frame/treasury))

## Cómo empezar

--8<-- 'text/testnet/connect.md'

## Telemetría

Puede ver la información actual de telemetría Moonbase Alpha visitando [this link](https://telemetry.polkadot.io/#list/Moonbase%20Alpha).

## Tokens

--8<-- 'text/testnet/faucet.md'

## Etapa temprana de Proof of Stake

Con el lanzamiento de Moonbase Alpha v6, TestNet ahora se está ejecutando con un sistema Proof of Stake en etapa inicial. Esto significa que, con fines de prueba, se alentará a los socios de Moonbeam a ser los primeros collators de la red.

A medida que avanza Moonbase Alpha, esperamos evolucionar hacia una red Proof of Stake completamente descentralizada.

## Limitaciones

Este es la primera TestNet para Moonbeam, por lo que existen algunas limitaciones.

Algunas [precompilaciones](https://docs.klaytn.com/smart-contract/precompiled-contracts) aún no se han incluido en esta versión. Puede consultar una lista de precompilaciones compatibles [aquí](/integrations/precompiles/). Sin embargo, todas las funciones integradas están disponibles.

Desde el lanzamiento de Moonbase Alpha v6, el límite máximo de gas(fees) por bloque se ha establecido en 15M, con un límite máximo de gas por transacción de 13M.


Los usuarios solo tienen acceso a la parachain Moonbeam. En redes futuras, agregaremos acceso a la relay chain para que los usuarios puedan probar la transferencia de tokens.

## Purga de cadena

Esta red está en desarrollo activo. Ocasionalmente, es posible que se necesiten purgas de la cadena para restablecer la blockchain a su estado inicial. Esto es necesario cuando se realizan actualizaciones o mantenimiento importantes de TestNet. Anunciaremos cuándo se llevará a cabo una purga de la cadena a través de nuestro canal de [Discord](https://discord.gg/PfpUATX) al menos con 24 horas de anticipación.

Tenga en cuenta que PureStake no migrará el estado de la cadena. Por lo tanto, todos los datos almacenados en la blockchain se perderán cuando se lleve a cabo una purga de la cadena. Sin embargo, como no hay límite de gas(fees), los usuarios pueden recrear fácilmente su estado de prepurga.

