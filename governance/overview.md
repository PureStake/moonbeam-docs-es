---
title: Descripción General
description: Como parachain de Polkadot, Moonbeam utilizará un sistema de gobernanza on-chain, lo que permitirá un voto ponderado de las partes interesadas en los referendos públicos.
---

# Gobernanza en Moonbeam

![Governance Moonbeam Banner](/images/governance/governance-overview-banner.png)

## Introducción

Moonbeam es una red descentralizada que estará gobernada por una comunidad de titulares de tokens, incluidos desarrolladores core, desarrolladores de aplicaciones, collators, usuarios y otros contribuyentes.

Nuestro objetivo es facilitar la participación de los titulares de tokens de cada una de estas categorías como parte del lanzamiento de la red.

## Definiciones generales

Con un gran poder viene una gran responsabilidad. Algunos parámetros importantes que debe comprender antes de comprometerse con la gobernanza de Moonbeam incluyen:

 - **Propuestas** — acciones o elementos propuestos por los titulares de tokens. Esto debe ser apoyado por otros usuarios para pasar al referéndum
 - **Referéndum** — cuando la comunidad vota la propuesta más apoyada. Puede haber un máximo de cinco referendos activos a la vez, a menos que haya un referéndum de emergencia en curso
 - **Período de lanzamiento** — con qué frecuencia se lanzan nuevos referendos públicos
 - **Período de votación** — tiempo que tienen los titulares de tokens para votar por un referéndum (duración de un referéndum)
 - **Período de votación acelerada** — Duración de la votación de propuestas de emergencia que abordan cuestiones críticas
- **Votación** —  los titulares de tokens votan los referendos sobre una base ponderada de stake y convicción. Las convicciones se refieren al tiempo que desean bloquear sus tokens cuando votan: cuanto más tiempo estén bloqueados, más peso tiene su voto. Los referendos que pasan están sujetos a una promulgación retrasada para que las personas que no estén de acuerdo con la dirección de la decisión tengan tiempo de salir de la red.
 - **Período de promulgación** — tiempo entre la aprobación y la promulgación de una propuesta (hacer ley). También es el período mínimo necesario para bloquear fondos al proponer una acción.
 - **Período de bloqueo** — tiempo (después de la promulgación de la propuesta) durante el cual se bloquean los tokens de los votantes ganadores. Los usuarios aún pueden usar estos tokens para staking o votar
 - **Período Cool-off** - La duración de un veto del comité técnico antes de que se pueda volver a presentar la propuesta.
 - **Delegación** —  acto de transferir su poder de voto a otra cuenta hasta una cierta conviccion.

## Principios

Los principios rectores "soft" para el compromiso con el proceso de gobernanza de Moonbeam incluyen:

 - Ser inclusivo con los titulares de tokens que desean involucrarse con Moonbeam y que se ven afectados por las decisiones de gobernanza.
 - Favorecer la participación de los titulares de tokens, incluso con opiniones contrarias a las nuestras, frente a la falta de participación.
 - Un compromiso con la apertura y transparencia en el proceso de toma de decisiones.
 - Trabajar para mantener el bien común de la red por delante de cualquier beneficio personal.
 - Actuar en todo momento como un agente moral que considera las consecuencias de la acción (o inacción) desde un punto de vista moral.
 - Ser pacientes y generosos en nuestras interacciones con otros titulares de tokens, pero sin tolerar el lenguaje, las acciones y el comportamiento abusivos o destructivos.

Estos puntos se inspiraron en gran medida en los escritos de Vlad Zamfir sobre gobernanza. Consulte sus artículos, [especialmente este](https://medium.com/@Vlad_Zamfir/how-to-participate-in-blockchain-governance-in-good-faith-and-with-good-manners-bd4e16846434).

## Mecánicas de gobernanza on-chain

El proceso de gobernanza "hard" para Moonbeam será impulsado por un proceso on-chain y aprovechará las pallets de Democracia, Consejo y Tesorería de [Substrate](/resources/glossary/#substrate-frame-pallets), de forma similar a como se rigen Kusama y la Relay Chain de Polkadot. La intención general de estos módulos es permitir que la mayoría de los tokens de la red determinen los resultados de las decisiones clave en la red. Estos puntos de decisión vienen en forma de votaciones ponderadas sobre los referendos propuestos.

Algunos de los componentes principales de este modelo de gobernanza incluyen:

 - **Referéndum** — una propuesta para un cambio en el sistema Moonbeam que incluye valores para parámetros clave, actualizaciones de código o cambios en el propio sistema de gobierno.
 - **Votación** — los titulares de tokens votarán los referendos sobre una base ponderada por stake. Los referendos que pasan, están sujetos a una promulgación retrasada, de modo que las personas que no están de acuerdo con la dirección de la decisión tienen tiempo para salir de la red.
 - **Consejo** — un grupo de personas electas que tienen derechos especiales de voto dentro del sistema. Se espera que los miembros del consejo propongan referendos para votación y tengan la capacidad de vetar referendos de fuentes públicas. Hay elecciones continuas para los miembros del consejo en las que los titulares de GLMR votarán por los miembros del consejo nuevos o existentes.
 - **Comité Técnico**: un grupo de personas elegidas por el Consejo que tienen derechos especiales de voto. Al igual que en Polkadot y Kusama, el Comité Técnico tiene la capacidad (junto con el Consejo) de acelerar la votación y la implementación de referendos de emergencia en circunstancias urgentes. Se puede crear un referéndum acelerado junto con los referéndum activos existentes. Es decir, un referéndum de emergencia no sustituye a los referéndum actualmente activos.
 - **Tesorería** —  colección de fondos que se pueden gastar mediante la presentación de una propuesta junto con un depósito. Las propuestas de gasto deben ser aprobadas por el consejo. Las propuestas rechazadas darán lugar a que el proponente pierda su depósito.

Vea [esta descripción general en el sitio web de Polkadot](https://polkadot.network/a-walkthrough-of-polkadots-governance/) y [esta publicación de wiki](https://wiki.polkadot.network/docs/learn-governance) para obtener más detalles sobre cómo estas pallets de marco de Substrate implementan la gobernanza en cadena.

## Derechos de voto del Consejo y del Comité Técnico

Esta sección cubre algunos antecedentes sobre la votación y describe los parámetros de votación del protocolo tal como están configurados actualmente. Existe un límite en la cantidad de tiempo en bloques que el comité técnico y el consejo tienen para votar sobre las mociones. Las mociones pueden terminar en menos bloques si ya se han presentado suficientes votos para determinar el resultado. Se pueden abrir un maximo de {{ networks.moonbase.democracy.max_proposals}} propuestas tanto en el comité técnico como en el consejo

Los parámetros de votación se establecen actualmente de la siguiente manera:

|             Variable             |     |                         Valor                         |
| :------------------------------: | :-: | :---------------------------------------------------: |
|     Período de votación     |     |     36000 bloques (5 días)     |
|        Período de votación acelerada        |     |     1200 bloques (4 horas)     | 
|          Período de promulgación           |     |     7200 bloques (1 día)  |
| Período Cool-off |     |    50400 bloques (7 días)  |
|              Depósito mínimo              |     | 4 GLMR |

**Votaciones que pueden ser Canceladas:**

 * El comité técnico puede cancelar una propuesta antes de que esta haya sido aceptada, solo por unanimidad
 * Un solo miembro del comité técnico puede vetar una propuesta entrante al consejo , sin embargo solo puede realizar el veto una vez, y solo durante el periodo cool-off de ({{ networks.moonbase.democracy.cool_period.days}} días)

## Pruébalo en Moonbase Alpha

Actualmente, en nuestra Moonbase Alpha TestNet, los titulares de tokens pueden enviar propuestas y votar en referendos. Para hacerlo, consulte las siguientes guías:

 - [Presentar una propuesta](/governance/proposals/)
 - [Votar una propuesta](/governance/voting/)

Los componentes de la Tesorería aún no se han implementado.
