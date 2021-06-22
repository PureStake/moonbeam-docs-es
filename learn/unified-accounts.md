---
title: Cuentas Unificadas
description: Moonbeam ahora usa el sistema de cuentas H160 basado en Ethereum de forma nativa y es compatible con Polkadot JS Apps
---
# Cuentas Unificadas

![Intro diagram](/images/learn/unifiedaccounts-banner.png)

## Introducción

Con el [lanzamiento de la actualización v3](https://www.purestake.com/news/moonbeam-network-upgrades-account-structure-to-match-ethereum/) para Moonbase Alpha TestNet, hemos realizado actualizaciones significativas en el sistema de cuentas subyacente en Moonbeam, reemplazando las cuentas y claves predeterminadas de estilo Substrate con cuentas y claves de estilo Ethereum.

La interfaz de Polkadot JS Apps también se actualizó para que admita de forma nativa direcciones H160 y claves ECDSA. Puede consultar [este tutorial](/integrations/wallets/polkadotjs/) para obtener más información sobre esta integración.

## Blockchain compatible con sustrato EVM

Cualquier parachain en el ecosistema de Polkadot puede ofrecer una implementación completa de EVM, lo que brinda la posibilidad de ejecutar contratos inteligentes basados en Solidity con cambios mínimos o nulos. El sustrato hace posible esta integración: simplemente conecte el [pallet EVM](https://docs.rs/pallet-evm/2.0.1/pallet_evm/) en su tiempo de ejecución para el soporte de EVM, y el [pallet Ethereum con Frontier](https://github.com/paritytech/frontier) para tener compatibilidad con Ethereum RPC. La disponibilidad de estos módulos de código abierto que Moonbeam ha desarrollado con Parity ha llevado a múltiples paracaídas a ofrecer compatibilidad con Ethereum en sus cadenas.

Pero hay una trampa importante. Con la configuración descrita anteriormente, un usuario (digamos Alice) puede tener una dirección estilo Ethereum (formato H160), que tiene 40 + 2 caracteres hexadecimales de longitud, en una cadena basada en sustrato. Esta dirección coincide con una clave privada, que se puede utilizar para firmar transacciones en el lado Ethereum de la cadena. Además, la dirección se asigna en una ranura de almacenamiento dentro de la paleta Substrate Balance a una dirección estilo Substrate (formato H256). 

Sin embargo, Alice solo conoce la clave privada de la dirección H160 y no la de la versión mapeada. Por lo tanto, no puede enviar transacciones con su dirección H256 y está limitada solo a realizar operaciones de solo lectura a través de la API de Substrate. Como consecuencia, Alice necesita otra dirección H256 que coincida con una clave privada diferente para poder operar en el lado del sustrato de la cadena, que incluye, entre otros, participación, saldos y gobernanza. 

El siguiente diagrama ilustra esta configuración.

![Old account system diagram](/images/learn/unifiedaccounts-images-1.png)

Esto puede crear fricciones y una mala experiencia de usuario para Alice. Primero, tiene que mover tokens a su dirección H256 asignada a H160 para poder realizar transacciones e implementar contratos a través del EVM. En segundo lugar, también necesita mantener un saldo en su otra dirección H256 (para la que tiene una clave privada diferente) para usar las funciones basadas en sustrato. En resumen, Alice necesita un mínimo de dos claves privadas para tener lo mejor de ambos mundos.

## Cuentas unificadas de Moonbeam

El objetivo de Moonbeam es crear un entorno totalmente compatible con Ethereum en Polkadot con la mejor experiencia de usuario posible. Esto se extiende más allá del conjunto de características base de Ethereum, con características adicionales como gobernanza en cadena, participación e integraciones entre cadenas.

Con Cuentas Unificadas, un usuario (llamémosle Bob) solo necesitará una única dirección H160, con su clave privada correspondiente, para hacer todo lo que mencionamos anteriormente, incluidas las funciones EVM y Substrate.

El diagrama de esta nueva configuración tiene el siguiente aspecto.

![New account system diagram](/images/learn/unifiedaccounts-images-2.png)

Eso es todo, Bob solo tiene una clave privada que coincide con una dirección. No necesita mover saldos entre 2 cuentas diferentes y puede acceder a todas las funciones con una sola cuenta y clave privada. Hemos estandarizado esta cuenta única para cumplir con la dirección H160 de estilo Ethereum y los estándares clave ECDSA.
