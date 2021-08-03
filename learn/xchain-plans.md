---
title: Integración Multicadena
description: Las integraciones de multicadena de Moonbeam admitirán cadenas paracaídas basadas en Polkadot y cadenas que no sean de Polkadot, como Ethereum.
---

# Planes de integración entre cadenas

Una de las características clave planificadas para Moonbeam es proporcionar una manera fácil para que los desarrolladores utilicen contratos inteligentes para integrarse con otras cadenas en el ecosistema Polkadot.

Polkadot define un protocolo de integración de bajo nivel, llamado paso de mensajes entre cadenas (XCMP), que se puede usar para facilitar la comunicación entre paracaídas dentro de la red Polkadot y compartir lógica confiable entre cadenas en la red Polkadot, llamados enclaves de ejecución de tiempo de ejecución protegidos compartidos (JUERGA). Parity está en el proceso de implementación de XCMP y se encuentra en la fase de diseño de SPREE al momento de redactar este documento (julio de 2020). Después del lanzamiento de la red principal de Polkadot, la compatibilidad con XCMP y SPREE se lanzará como actualizaciones de la cadena de retransmisión de Polkadot. Planeamos implementar y soportar escenarios de integración basados en estos protocolos cuando estén disponibles.

## Una analogía: el "Bash de Linux" {: #an-analogy-the-bash-of-polkadot } 

Para nosotros, Polkadot tendrá una función similar a algo como Linux. Ambas son plataformas orientadas a desarrolladores que vienen con bibliotecas para facilitar la creación de aplicaciones. 

Recuerde la antigua filosofía de Unix, donde los usuarios construyen herramientas que hacen un trabajo y lo hacen bien. Esto es similar a la especialización que esperamos que suceda con las paracaídas en Polkadot. En Linux, puede combinar y componer estas herramientas especialmente diseñadas para lograr efectos de orden superior utilizando un bash similar a un shell. Anticipamos que los contratos inteligentes basados en Moonbeam proporcionarán un entorno análogo "similar a bash", donde los contratos inteligentes especializados y la funcionalidad de parachain pueden componerse para lograr objetivos de orden superior.

Puede darse el caso de que los proyectos comiencen como uno o más contratos inteligentes Moonbeam y migren con el tiempo para convertirse en "aplicaciones nativas", que podrían ser parathreads o parachains en el contexto de Polkadot, si necesitan más rendimiento o un control más directo sobre sus economías.

## Integración con otras cadenas basadas en Polkadot {: #integration-with-other-polkadot-based-chains } 

El escenario inicial que más nos interesa es permitir el movimiento de tokens de otras cadenas a tokens basados en Moonbeam, de modo que puedan usarse dentro de DeFi y otras aplicaciones en la plataforma. Una vez que finaliza su trabajo, estos activos pueden retroceder o salir a otras cadenas.

A medida que evolucionen las características de integración de la red Polkadot, continuaremos brindando formas para que los desarrolladores accedan a esas integraciones desde contratos inteligentes y compongan características en todas las cadenas en contratos inteligentes Moonbeam.

## Integración con Ethereum {: #integration-with-ethereum } 

La conectividad a Ethereum es una capacidad importante necesaria para que Moonbeam pueda admitir proyectos basados en Ethereum, particularmente en implementaciones híbridas donde los proyectos se implementan simultáneamente en Ethereum y Moonbeam. Hay al menos un proyecto en desarrollo, independiente de Moonbeam, para construir un puente Ethereum basado en parachain. Una vez que este puente esté operativo, proporcionará un mecanismo para mover tokens, estados y mensajes hacia y desde Ethereum aprovechando Polkadot.

Hasta que haya un puente basado en parachains en producción, planeamos proporcionar dos soluciones para proyectos que desean integrar Ethereum y Moonbeam:

 1. Una utilidad que puede exportar el estado de Ethereum a un archivo binario, donde este archivo binario se puede usar para importar ese estado a Moonbeam. Cada uso de esta utilidad sería una migración unidireccional.
 2. Un puente Ethereum punto a punto integrado directamente incorporado en Moonbeam. Este puente permitiría el movimiento de tokens y las consultas y mensajes de estado entre cadenas. A medida que se desarrolla el ecosistema de Polkadot, esperamos múltiples opciones de integración de Ethereum como opciones para los proyectos que se implementan en Moonbeam.
