---
title: Tecnología
description: Moonbeam se construye utilizando Rust y el marco Substrate, lo que permite herramientas ricas para la implementación, pero también permite la especialización y optimización.
---

# Tecnología

## La pila de desarrollo Moonbeam

Moonbeam es una plataforma de blockchain de contrato inteligente construida en el lenguaje de programación Rust utilizando el marco Substrate.  

### Lenguaje de programación Rust

Rust es un buen lenguaje para implementar una blockchain, ya que tiene un alto rendimiento como C y C ++, pero tiene características de seguridad de memoria integradas que se aplican en el momento de la compilación, lo que evita muchos errores comunes y problemas de seguridad que pueden surgir de C y C ++ implementaciones.

### Marco de sustrato

Substrate proporciona un amplio conjunto de herramientas para crear cadenas de bloques, incluido un entorno de ejecución en tiempo de ejecución que habilita una función de transición de estado genérica y un conjunto de módulos conectables que proporcionan implementaciones de varios subsistemas de cadenas de bloques.

Moonbeam aprovecha múltiples pallets de marcos de sustrato existentes para proporcionar servicios y funcionalidad clave de blockchain, incluidas las estructuras de datos centrales de blockchain, redes peer-to-peer, mecanismos de consenso, cuentas, activos y saldos. Las pallets personalizadas y la lógica en el tiempo de ejecución implementan el comportamiento y la funcionalidad específicos de Moonbeam, como la integración de token entre cadenas. Para los pallets apalancados, Moonbeam se esforzará por permanecer lo más cerca posible del código base de Substrate ascendente e incorporará correcciones de errores, mejoras y nuevas funciones de Substrate de manera continua.

## Tiempo de ejecución de Blockchain

El tiempo de ejecución principal de Moonbeam especifica la función de transición de estado y el comportamiento de la blockchain Moonbeam. El tiempo de ejecución de Moonbeam se construye usando [FRAME](/resources/glossary/#substrate-frame-pallets). Incluye varios palets estándar así como varios personalizados. El tiempo de ejecución se compila en un binario [WebAssembly (Wasm)](/resources/glossary/#webassemblywasm) y en un binario nativo. Estas versiones compiladas se ejecutarán en la cadena de retransmisión de Polkadot y en los entornos de nodo Moonbeam.

!!! nota
    Las pallets de marco de sustrato son una colección de módulos basados en Rust que brindan la funcionalidad que se requiere al construir una blockchain. WebAssembly es un estándar abierto que define un formato de código binario portátil. Es compatible con diferentes lenguajes de programación, compiladores y navegadores. Encuentre más definiciones [en nuestro glosario](/resources/glossary/).

Algunas de las pallets de marco de sustrato clave utilizadas en el tiempo de ejecución de Moonbeam incluyen:

 - **Saldos** — soporte para cuentas, saldos y transferencias.
 - **EVM** —  implementación completa de EVM basada en Rust basada en SputnikVM. Proporciona la lógica de transición de estado para contratos inteligentes basados en Moonbeam.
 - **Ethereum** — proporciona emulación del procesamiento de bloques Ethereum para EVM.
 - **RPC-Ethereum** —  Implementación de Web3 RPC en Substrate.
 - **Consejo** —  incluye mecanismos de gobernanza en torno al consejo y propuestas.
 - **Democracia** — funcionalidad para la votación pública ponderada de los titulares de tokens.
 - **Ejecutivo** — capa de orquestación que envía llamadas a otros módulos en tiempo de ejecución.
 - **Índices** — compatibilidad con nombres cortos fáciles de usar para direcciones de cuentas.
 - **Sistema** — proporciona tipos de bajo nivel, almacenamiento y funciones de blockchain.
 - **Tesorería** — tesorería en cadena que se puede utilizar para financiar bienes públicos como una ranura parachain.

Moonbeam también utiliza la biblioteca Cumulus para proporcionar integración a la cadena de relés Polkadot.

Además de estas pallets de marco de sustrato, implementaremos módulos con funcionalidad específica de Moonbeam, incluida la mecánica y las recompensas de la collator y otros componentes básicos del desarrollador.

## Arquitectura de compatibilidad de Ethereum

Los contratos inteligentes en Moonbeam se pueden implementar utilizando Solidity, Vyper y cualquier otro lenguaje que pueda compilar contratos inteligentes en un código de bytes compatible con EVM. Moonbean tiene como objetivo proporcionar un entorno seguro y de baja fricción para el desarrollo, la prueba y la ejecución de contratos inteligentes que sea compatible con la cadena de herramientas para desarrolladores de Ethereum existente.  

El comportamiento de ejecución y la semántica de los contratos inteligentes basados en Moonbeam se esforzarán por estar lo más cerca posible de Ethereum Layer 1. Moonbeam es un solo fragmento, por lo que las llamadas de contrato cruzado tienen la misma semántica de ejecución sincrónica que en Ethereum Layer 1.

![Diagram showing the interactions made possible through Moonbeam's Ethereum compatibility](/images/technology-diagram.png)

Arriba se muestra un flujo de interacción de alto nivel. Un nodo Moonbeam recibe una llamada Web3 RPC desde una DApp o una herramienta de desarrollo Ethereum existente, como Truffle. El nodo tendrá RPC de Web3 y RPC de substrate disponibles, lo que significa que puede usar herramientas Ethereum o Substrate al interactuar con un nodo Moonbeam. Estas llamadas RPC son manejadas por funciones de tiempo de ejecución de sustrato asociadas. El tiempo de ejecución del sustrato verifica las firmas y maneja cualquier extrínseco. Las llamadas de contratos inteligentes finalmente se pasan al EVM para ejecutar las transiciones de estado.

Al basar nuestra implementación de EVM en Substrate Pallet-EVM, obtenemos una implementación completa de EVM basada en Rust y el apoyo del equipo de ingeniería de Parity.
