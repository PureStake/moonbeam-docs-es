---
title: Descripción general
description: Una descripción general de las redes planificadas para Moonbeam, una parachain de smart contract compatible con Ethereum en Polkadot.
---

# Networks - Redes

Planeamos crear múltiples redes de larga duración basadas en Moonbeam. Algo a destacar es que Moonbeam se desplegará en Kusama además de Polkadot.

Nuestra hoja de ruta con respecto a las implementaciones como parachain es la siguiente:

 - Moonbase Alpha: Parachain TesNet alojado por PureStake (_Septiembre de 2020_) 
 - Moonrock: Implementación en Rococo TestNet (_tbd_)
 - Moonriver: despliegue en Kusama (_final de Q2 - 2021_)
 - Moonbeam: despliegue en Polkadot (_final de Q3 - 2021_)
 
Esta estrategia nos permite eliminar el riesgo de actualizaciones de software a Moonbeam en Polkadot MainNet mientras se mantiene una velocidad de actualización razonable. Agregaremos detalles sobre cómo acceder a diferentes redes basadas en Moonbeam a medida que las redes estén disponibles.

## Moonbase Alpha

Esta TestNet es una red alojada por PureStake. Cuenta con un esquema de Parachain/Relay Chain. El objetivo es permitir a los desarrolladores probar las características de compatibilidad de Ethereum de Moonbeam en un entorno de parachain compartido sin necesidad de ejecutar sus propios nodos o red.

[Más información sobre Moonbase Alpha](/networks/testnet/).

## Moonrock  

Hemos decidido no participar en las primeras implementaciones de parachain en Rococo. Hemos estado ejecutando nuestra propia configuración de Parachain/ Relay chain desde que lanzamos nuestra TestNet en septiembre de 2020.

Sin embargo, esperamos implementar Moonbeam como una parachain en Rococo TestNet una vez que las características de interoperabilidad estén disponibles. Esto proporcionará un lugar para probar estas características con otras cadenas.

## Moonriver

Moonbeam se lanzará como una parachain en la red Kusama, antes de la implementación en Polkadot MainNet ([más detaslles aquí](https://www.purestake.com/news/moonbeam-on-kusama/)). Esto requiere que la funcionalidad de parachain esté activa en Kusama.

Planeamos ejecutar las funciones relacionadas a la Parachain, tales como el [Crowdloan](https://wiki.polkadot.network/docs/en/learn-crowdloans), [XCMP](https://wiki.polkadot.network/docs/en/learn-crosschain), y [SPREE](https://wiki.polkadot.network/docs/en/learn-spree) en Moonriver a medida que esas funciones estén disponibles.

## Moonbeam Polkadot MainNet

La MainNet de Moonbeam se implementará como una parachain en Polkadot. Esta red Moonbeam contará con los más altos niveles de seguridad y disponibilidad. El código que se ejecuta en MainNet generalmente habrá sido examinado a través de una o más de las otras redes enumeradas anteriormente.
