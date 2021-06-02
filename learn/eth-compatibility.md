---
title: Compatibilidad con Eth
description: Puede parecer abrumador pasar a una paracadena de Polkadot si estás acostumbrado a Ethereum. Esto es lo que puede esperar cuando utilice Moonbeam por primera vez.
---

# Compatibilidad con Ethereum

## Diferencias entre Moonbeam y Ethereum

Si bien Moonbeam se esfuerza por ser compatible con la API Web3 y EVM de Ethereum, existen varias diferencias importantes en Moonbeam.

Primero, Moonbeam usa un mecanismo de consenso basado en Proof of Stake, lo que significa que los conceptos de Proof of Work, como dificultad, tíos, hashrate, etc., generalmente no tienen significado dentro de Moonbeam. Para las API que devuelven valores relacionados con la Prueba de trabajo de Ethereum, devolvemos valores predeterminados. Es casi seguro que los contratos existentes de Ethereum que dependen de componentes internos de Prueba de trabajo (por ejemplo, contratos de grupo de minería) no funcionarán como se esperaba en Moonbeam.

Otra diferencia significativa entre Moonbeam y Ethereum es que Moonbeam incluye un amplio conjunto de funciones de gobernanza en cadena basadas en la funcionalidad Substrate. Estos módulos de gobernanza en cadena incluyen funciones para impulsar actualizaciones de la propia cadena de bloques en función de la votación ponderada por tokens.

## Lo que permanece igual

Si está moviendo partes de sus cargas de trabajo existentes y el estado de Ethereum Layer 1 a Moonbeam, puede esperar cambios mínimos requeridos (aparte de las excepciones mencionadas anteriormente). Sus aplicaciones, contratos y herramientas se mantendrán prácticamente sin cambios.

Soportes Moonbeam:

 - **Contratos inteligentes basados en solidez**
 - **Herramientas del ecosistema** (por ejemplo, Exploradores de bloques, bibliotecas de desarrollo front-end, billeteras)
 - **Herramientas de desarrollo** (por ejemplo, Truffle, Remix, MetaMask)
 - **Tokens de Ethereum a través de puentes** (por ejemplo, movimiento de tokens, visibilidad del estado, transmisión de mensajes)
