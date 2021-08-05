---
title: Descripción General
description: Una descripción general de las redes planificadas para Moonbeam, una parachain de smart contract compatible con Ethereum en Polkadot.
---

# Networks - Redes

Planeamos crear múltiples redes de larga duración basadas en Moonbeam. Algo a destacar es que Moonbeam se desplegará en Kusama además de Polkadot.

Nuestra hoja de ruta con respecto a las implementaciones como parachain es la siguiente:

 - Moonbase Alpha: Parachain TesNet alojado por PureStake (_Septiembre de 2020_) 
 - Moonrock: Implementación en Rococo TestNet (_May 2021_)
 - Moonriver: despliegue en Kusama (_June 2021_)
 - Moonbeam: despliegue en Polkadot (_final de Q3 - 2021_)
 
Esta estrategia nos permite eliminar el riesgo de actualizaciones de software a Moonbeam en Polkadot MainNet mientras se mantiene una velocidad de actualización razonable. Agregaremos detalles sobre cómo acceder a diferentes redes basadas en Moonbeam a medida que las redes estén disponibles.

## Moonbase Alpha {: #moonbase-alpha } 

Esta TestNet es una red alojada por PureStake. Cuenta con un esquema de Parachain/Relay Chain. El objetivo es permitir a los desarrolladores probar las características de compatibilidad de Ethereum de Moonbeam en un entorno de parachain compartido sin necesidad de ejecutar sus propios nodos o red.

[Más información sobre Moonbase Alpha](/networks/testnet/).

## Moonrock {: #moonrock } 

We decided not to participate in the first parachain deployments to Rococo because we have been running our own parachain/relay chain setup since we launched our TestNet in September 2020.

However, Moonrock was deployed to Rococo for the first time in May 2021. 
## Moonriver {: #moonriver } 

In advance of deploying to the Polkadot MainNet, [Moonbeam launched Moonriver](https://moonbeam.network/announcements/moonriver-launch-kusama/) as a parachain on the Kusama network. The parachain functionality is live on Kusama and features are progressively being released according to the [Moonriver launch schedule](https://moonbeam.network/networks/moonriver/launch/). 

We plan to exercise parachain-related functionality such as [XCMP](https://wiki.polkadot.network/docs/learn-crosschain) and [SPREE](https://wiki.polkadot.network/docs/learn-spree) on Moonriver as those features become available.

[Learn more about Moonriver](/networks/moonriver/).

## Moonbeam Polkadot MainNet {: #moonbeam-polkadot-mainnet } 

La MainNet de Moonbeam se implementará como una parachain en Polkadot. Esta red Moonbeam contará con los más altos niveles de seguridad y disponibilidad. El código que se ejecuta en MainNet generalmente habrá sido examinado a través de una o más de las otras redes enumeradas anteriormente.
