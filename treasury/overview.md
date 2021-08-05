---
title: Descripción general
description: Como parachain de Polkadot, Moonbeam utilizará una tesorería  on-chain controlada por los miembros del consejo, que permite a las partes interesadas presentar propuestas para promover en la red.
---

# Tesorería en Moonbeam

![Treasury Moonbeam Banner](/images/treasury/treasury-overview-banner.png)

## Introducción {: #introduction } 

Una tesorería es una colección de fondos administrada on-chain. Moonbeam tendrá una tesorería comunitaria para apoyar las iniciativas para promover la red. Esta tesorería se financiará con un porcentaje de las comisiones de transacción de la red y será gestionada por  el "Council" o Consejo.

Cada red basada en Moonbeam tendrá su propia tesorería. En otras palabras, Moonbase Alpha TestNet, Moonshadow en Westend, Moonriver en Kusama y Moonbeam en Polkadot tendrán cada uno su respectiva tesorería.

## Definiciones generales {: #general-definitions } 

Alguna terminología importante para entender con respecto a las tesorerías:

- **Council** — un grupo de personas electas que controlan cómo se gastarán los fondos de tesorería
- **Proposal** — un plan o sugerencia para promover la red que presentan las partes interesadas para ser aprobadas por el consejo.
- **Proposal bond** — un depósito equivalente a un porcentaje del monto total del gasto de la propuesta
- **Proposal bond minimum** — monto mínimo para la proposal bond.  Esta cantidad debe pagarse como bond si es superior al porcentaje del depósito.
- **Spend period** — la cantidad de días, en bloques, durante los cuales la tesorería financia tantas propuestas como sea posible sin exceder el máximo
- **Maximum approved proposals** — la cantidad máxima de propuestas que pueden esperar en la lista de gastos.

=== "Moonbase Alpha"
    |             Variable             |     |                                                             Valor                                                      |
    | :------------------------------: | :-: | :--------------------------------------------------------------------------------------------------------------------: |
    |           Proposal bond          |     |                            {{ networks.moonbase.treasury.proposal_bond }}% del gasto propuesto                       |
    |       Proposal bond minimum      |     |                                  {{ networks.moonbase.treasury.proposal_bond_min }} DEV                              |
    |           Spend period           |     |  {{ networks.moonbase.treasury.spend_period_blocks }} blocks ({{ networks.moonbase.treasury.spend_period_days}} días)  |
    |     Maximum approved proposals   |     |                                  {{ networks.moonbase.treasury.max_approved_proposals }}                               |
    |     % del fees de las transacciones   |     |                                  {{ networks.moonbase.treasury.tx_fees_allocated }}                               |

=== "Moonriver"
    |             Variable             |     |                                                             Valor                                                      |
    | :------------------------------: | :-: | :--------------------------------------------------------------------------------------------------------------------: |
    |           Proposal bond          |     |                            {{ networks.moonriver.treasury.proposal_bond }}% del gasto propuesto                      |
    |       Proposal bond minimum      |     |                                  {{ networks.moonriver.treasury.proposal_bond_min }} MOVR                              |
    |           Spend period           |     |  {{ networks.moonriver.treasury.spend_period_blocks }} blocks ({{ networks.moonriver.treasury.spend_period_days}} días)  |
    |     Maximum approved proposals   |     |                                  {{ networks.moonriver.treasury.max_approved_proposals }}                               |
     |     % del fees de las transacciones   |     |                                  {{ networks.moonriver.treasury.tx_fees_allocated }}                               |

## Tesorería comunitaria {: #community-treasury } 

Para financiar la Tesorería , se le asignará un porcentaje de las comisiones de transacción de cada bloque. El porcentaje restante de las tarifas se quema (consulte la tabla anterior). La Tesorería permite a las partes interesadas presentar propuestas de gastos para ser revisadas y votadas por el "Council". Estas propuestas de gasto deben incluir iniciativas para promover la red o impulsar la participación de la red. Algunas iniciativas de la red podrían incluir la financiación de integraciones o colaboraciones, eventos comunitarios, alcance de la red y más. 

Para disuadir el spam, las propuestas deben enviarse con un depósito, también conocido como "proposal bond".The proposal bond debe ser mayor que el monto mínimo, conocido como el  "proposal bond minimum", que puede modificarse mediante una propuesta de gobernanza. Por lo tanto, cualquier holderue tenga suficientes tokens para cubrir el depósito puede enviar una propuesta. Si el proponente no tiene fondos suficientes para cubrir el depósito, la extrinsic fallará debido a fondos insuficientes, pero las tarifas de transacción aún se deducirán.

Una vez que se ha presentado una propuesta, está sujeta a la gobernanza y el consejo vota sobre ella. Si la propuesta es rechazada, el depósito se perderá y se transferirá hacia la tesorería. Si el consejo lo aprueba, la propuesta entra en una cola para ser colocada en un período de gasto. Si la cola de gastos contiene el número máximo de propuestas aprobadas, la presentación de la propuesta fallará de manera similar a como lo haría si el saldo del proponente es demasiado bajo.

Una vez que la propuesta se encuentre en un "spend period" o "período de gasto", los fondos se distribuirán al beneficiario y el depósito original se devolverá al proponente. Si la tesorería se queda sin fondos, las propuestas aprobadas restantes permanecerán almacenadas hasta el próximo período de gasto cuando la tesorería tenga fondos suficientes nuevamente.

El camino feliz para una propuesta de tesorería se muestra en el siguiente diagrama:

![Treasury Proposal Happy Path Diagram](/images/treasury/treasury-proposal-roadmap.png)
