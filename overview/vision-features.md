---
title: Visión
description: Moonbeam está diseñado para permitir un futuro multi-chain, donde los usuarios y activos pueden moverse libremente a través de muchas cadenas especializadas y heterogéneas.
---

# Nuestra visión para Moonbeam

Creemos en un futuro de múlti-chain con muchas cadenas y usuarios activos en esas cadenas. En este contexto, hemos creado Moonbeam: una plataforma de contrato inteligente que proporciona un entorno compatible con Ethereum para crear aplicaciones descentralizadas. Moonbeam fue diseñado para servir a estos nuevos tipos de activos y usuarios que existen en dos o más cadenas.

Las plataformas de contratos inteligentes existentes están diseñadas para dar servicio a los usuarios y activos en una cadena única específica. Al proporcionar la funcionalidad de contrato inteligente entre cadenas, Moonbeam permite a los desarrolladores cambiar las cargas de trabajo y la lógica existentes, a Moonbeam, además de extender el alcance de sus aplicaciones a nuevos usuarios y activos en otras cadenas.

La integración cross-chain o cadena cruzadas de Moonbeam se logra, convirtiéndose en una [parachain](/resources/glossary/#parachains) en la red Polkadot.  La [red Polkadot](/resources/glossary/#polkadot) proporciona integración y conectividad entre parachains que están conectadas a la red y a otras cadenas no basadas en Polkadot, como Ethereum y Bitcoin, a través de puentes. 

## Quién se beneficia de Moonbeam

Hay tres públicos principales que pueden beneficiarse más de la funcionalidad de cadena cruzada de Moonbeam:

### Proyectos existentes basados en Ethereum

Los proyectos que luchan con los desafíos de costo y escalabilidad en Ethereum pueden usar Moonbeam para:

 - Mover partes de sus cargas de trabajo existentes en conjunto con el estado de Capa 1 de Ethereum, con cambios mínimos requeridos.
 - Implementar un enfoque híbrido, donde las aplicaciones viven en Ethereum y Moonbeam simultáneamente.  
 - Extender su alcance a la red Polkadot y otras cadenas que están conectadas a Polkadot.  

### Proyectos del ecosistema Polkadot

Los proyectos que necesitan la funcionalidad de contrato inteligente pueden usar Moonbeam para:

 - Aumentar sus parachain y parathreads existentes. 
 - Agregar una nueva funcionalidad que es necesaria pero no está incluida enla  [Relay Chain de Polkadot](/resources/glossary/#relay-chain). Por ejemplo, podrían crear un lugar donde los equipos puedan financiar colectivamente sus proyectos, implementar lockdrops y procesar otras transacciones financieras más complejas que las proporcionadas por la funcionalidad básica de [Substrate](/resources/glossary/#substrate) functionality.  
 - Aprovechar la extensa y madura cadena de herramientas de desarrollo de Ethereum. 

### Desarrolladores de nuevas DApps

Las personas y los equipos que quieran intentar construir sobre Polkadot pueden usar Moonbeam para:

 - Aprovechar la funcionalidad especializada de las parachains de Polkadot mientras llega a los activos y usuarios de otras cadenas. 
 - Componer la funcionalidad de las parachains de Polkadot utilizando Moonbeam como una capa de integración ligera que agrega los servicios de red antes de presentarlos a los usuarios finales. La implementación de un servicio compuesto utilizando integraciones predefinidas en una plataforma de contrato inteligente será mucho más rápido y fácil (en muchos casos) que crear un runtime de Substrate completo y realizar las integraciones usted mismo.


## Funciones y características clave

Moonbeam logra estos objetivos con las siguientes características clave:

 - **Descentralizado y sin permisos** , que proporciona un requisito básico para la resistencia a la censura y el soporte para muchos casos de uso de DApp existentes y futuras.  
 - **Contiene una implementación completa de EVM** , que permite migrar los contratos inteligentes basados en Solidity con un cambio mínimo y con los resultados de ejecución esperados.
 - **Implementa la API de Web3 RPC**  para que los front-ends de DApp existentes se puedan migrar con un cambio mínimo requerido, por lo que las herramientas existentes basadas en Ethereum, como Truffle, Remix y MetaMask, se pueden usar sin modificaciones para Moonbeam.
 - **Compatible con Substrate Ecosystem Toolset** , que incluye exploradores de bloques, bibliotecas de desarrollo front-ends y billeteras, lo que permite a los desarrolladores y usuarios usar la herramienta adecuada para lo que están tratando de lograr.
 - **Integración nativa de cadenas cruzadas** a través de la red Polkadot y mediante puentes de tokens, lo que permite el movimiento de tokens, la visibilidad del estado y el paso de mensajes con Ethereum ademas de otras cadenas. 
 - **Gobernanza On-chain** para permitir que las partes interesadas, desarrollen rápida y fácilmente el protocolo base de acuerdo con las necesidades del desarrollador y de la comunidad.  

Esta combinación única de elementos llena un vacío de mercado estratégico, al tiempo que permite a Moonbeam abordar las necesidades futuras de los desarrolladores a medida que la red de Polkadot crece con el tiempo. Construir su propia cadena con Substrate es poderoso, pero también viene con una serie de responsabilidades adicionales, como aprender e implementar el runtime de la cadena en Rust, crear una economía de tokens e incentivar una comunidad de operadores de nodos.

Para muchos desarrolladores y proyectos, un enfoque de contrato inteligente compatible con Ethereum será mucho más simple y rápido de implementar. Lo que  al construir estos contratos inteligentes en Moonbeam, los desarrolladores aún pueden integrarse con otras cadenas y obtener valor de los efectos de red basados en Polkadot.
