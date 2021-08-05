---
title: Descripción General
description: Moonbeam proporciona funciones de staking en las que los titulares de tokens nominan a los collators con sus tokens y obtienen recompensas.
---

# Staking en Moonbeam

![Staking Moonbeam Banner](/images/staking/staking-overview-banner.png)

## Introducción {: #introduction } 

Moonbeam utiliza un mecanismo de producción de bloques basado en [el modelo Proof-of-Stake de Polkadot](https://wiki.polkadot.network/docs/learn-consensus) donde hay collators y validators. Los [Collators](https://wiki.polkadot.network/docs/learn-collator) mantienen la parachain (en este caso, Moonbeam) mediante la recopilación de transacciones de los usuarios y la producción de pruebas de transición de estados para los [validators](https://wiki.polkadot.network/docs/learn-validator) de la Relay Chain.

El conjunto de collators (nodos que producen bloques) se selecciona en función de su stake en la red. Y aquí es donde entra el Staking.

Los Collators (y los titulares de tokens si nominan) tienen un stake en la red del cual puede haber una disminución o recorte(slash) si cometen fallos. Por lo tanto, cuanto mayor sea el stake, mayor será la seguridad de la red. Cuanto mayor sea el stake, es más probable que el collator sea seleccionado para producir un bloque y ganar recompensas, que comparten con sus nominadores. De esta manera, los miembros de la red están incentivados a hacer stake de sus tokens para mejorar la seguridad general.

## Definiciones generales {: #general-definitions } 

--8<-- 'text/staking/staking-definitions.md'

=== "Moonbase Alpha"

    |             Variable             |  |                                                  Value                                                  |
    |:--------------------------------:|::|:-------------------------------------------------------------------------------------------------------:|
    |     Minimum nomination stake     |  |                          {{ networks.moonbase.staking.min_nom_stake }} DEV                              |
    |        Minimum nomination        |  |                          {{ networks.moonbase.staking.min_nom_amount}} DEV                              |
    | Maximum nominators per collators |  |                             {{ networks.moonbase.staking.max_nom_per_col }}                             |
    | Maximum collators per nominator  |  |                             {{ networks.moonbase.staking.max_col_per_nom }}                             |
    |              Round               |  | {{ networks.moonbase.staking.round_blocks }} blocks ({{ networks.moonbase.staking.round_hours }} hours) |
    |          Bond duration           |  |                            {{ networks.moonbase.staking.bond_lock }} rounds                             |

=== "Moonriver"

    |             Variable             |  |                                                   Value                                                   |
    |:--------------------------------:|::|:---------------------------------------------------------------------------------------------------------:|
    |     Minimum nomination stake     |  |                           {{ networks.moonriver.staking.min_nom_stake }} MOVR                             |
    |        Minimum nomination        |  |                           {{ networks.moonriver.staking.min_nom_amount}} MOVR                             |
    | Maximum nominators per collators |  |                             {{ networks.moonriver.staking.max_nom_per_col }}                              |
    | Maximum collators per nominator  |  |                             {{ networks.moonriver.staking.max_col_per_nom }}                              |
    |              Round               |  | {{ networks.moonriver.staking.round_blocks }} blocks ({{ networks.moonriver.staking.round_hours }} hours) |
    |          Bond duration           |  |                             {{ networks.moonriver.staking.bond_lock }} rounds                             |

## Distribución de recompensas  {: #reward-distribution } 

Al final de cada ronda (300 bloques), los collators son recompensados por su trabajo de 2 rondas atras.

Cuando los collators se unen al grupo, establecen una comisión para cobrar a sus nominadores por el servicio que brindan. Por lo tanto, la distribución de recompensas es la siguiente:

 - La comisión se resta de la recompensa a distribuir
 - El collator obtiene las recompensas correspondientes a su stake en la red, más la comisión
 - El resto de las recompensas se distribuyen entre los nominadores según el monto en stake.


Matemáticamente hablando, para los collators, la recompensa sería así:

![Staking Collator Reward](/images/staking/staking-overview-1.png)

Donde el stake corresponde a la cantidad de tokens vinculados(bond) por el collator con respecto al total stake de ese collator (nominaciones contables).

Para cada nominador, la recompensa se vería así:

![Staking Nominator Reward](/images/staking/staking-overview-2.png)

Donde el stake  corresponde a la cantidad de tokens vinculados(bond) por cada nominador con respecto al total stake de ese collator.

## Try it out {: #try-it-out } 

En Moonbase Alpha TestNet, los titulares de tokens pueden hacer stake  y ganar recompensas (para familiarizarse con el sistema, ya que el token no tiene ningún valor real).

Para hacerlo, puede consultar [esta guía](/staking/stake/).

--8<-- 'text/moonriver-launch/staking-phase-4.md'
