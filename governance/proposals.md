---
title: Proponer una acción
description: Cómo enviar una propuesta para votar en Moonbeam a través de funciones de gobernanza
---

# Propuestas

![Governance Moonbeam Banner](/images/governance/governance-proposal-banner.png)

## Introducción

Como se menciona en la [página de descripción general de la gobernanza](/governance/overview/#definitions),una propuesta es una presentación a la cadena en la que el titular de un token sugiere que el sistema lleve a cabo una acción.

Las propuestas son uno de los elementos centrales del sistema de gobernanza porque son la principal herramienta para que las partes interesadas propongan acciones / cambios, que luego votan otras partes interesadas.

En Moonbeam, los usuarios podrán crear, segundar y votar propuestas utilizando su dirección H160 y su clave privada, es decir, su cuenta habitual de Ethereum.

Con el lanzamiento de [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0),  los usuarios de la red ahora pueden enviar propuestas para referendos públicos en TestNet. Esta guía describe el proceso de cómo crear una propuesta. Los pasos irán desde su creación hasta que llegue a referendos públicos. Puede encontrar una guía sobre cómo votar una propuesta [aquí](/governance/voting/).

Se puede encontrar más información en las páginas Wiki de Polkadot relacionadas con la [gobernanza](https://wiki.polkadot.network/docs/en/learn-governance#council) y la [participación en la democracia](https://wiki.polkadot.network/docs/en/maintain-guides-democracy).

!!! nota
   Esta guía se realizó con una versión personalizada de Moonbeam con períodos cortos de lanzamiento / promulgación solo con fines de demostración.

## Definiciones

Algunos de los parámetros clave de esta guía son los siguientes:

 - **Propuesta** — acción o elementos propuestos por los usuarios de la red.
 - **En segundo lugar** —  otras partes interesadas pueden secundar (aprobar) una propuesta si están de acuerdo con ella y quieren ayudarla a llegar a referendos públicos. Esto requiere igualar el depósito del proponente original.
 - **Hash de preimagen** —  hash de la propuesta que se va a promulgar. El primer paso para hacer una propuesta es enviar una preimagen. El hash es solo su identificador. El proponente de la preimagen puede ser diferente al usuario que propone esa preimagen como propuesta formal.
 - **Depósito mínimo de preimagen** —  cantidad mínima de tokens que el proponente debe pagar al enviar una preimagen
 - **Depósito mínimo de propuesta** — cantidad mínima de tokens que el proponente debe vincular al enviar una propuesta. Los tokens pueden estar bloqueados por un período de tiempo indeterminado porque se desconoce cuándo una propuesta puede convertirse en un referéndum (si es que alguna vez lo hace). Esto es cierto para los tokens vinculados tanto por el proponente como por los usuarios que secundan la propuesta.
 - **Período de lanzamiento** — duración (en bloques) durante la cual no se puede volver a presentar una propuesta después de haber sido vetada.

Actualmente, para Moonbase Alpha:

|         Variable         |     |                                                          Valor                                                          |
| :----------------------: | :-: | :---------------------------------------------------------------------------------------------------------------------: |
|     Periodo de lanzamiento       |     | {{ networks.moonbase.democracy.launch_period.blocks}} bloques ({{ networks.moonbase.democracy.launch_period.days}} dias) |
|     Período de reflexión      |     |   {{ networks.moonbase.democracy.cool_period.blocks}} bloques ({{ networks.moonbase.democracy.cool_period.days}} dias)   |
| Depósito mínimo de preimagen |     |                                 {{ networks.moonbase.democracy.min_preim_deposit}} DEV                                  |
| Depósito mínimo de propuesta |     |                                    {{ networks.moonbase.democracy.min_deposit}} DEV                                     |

## Hoja de ruta de una propuesta

--8<-- 'text/governance/roadmap.md'

## Proponer una acción

En este apartado se repasa el proceso de creación de una propuesta, desde una preimagen hasta que llega a los referendos públicos. En lugar de hacer un ejemplo genérico, esta guía creará una propuesta real que servirá de base para esta guía y otras.

Para hacer una propuesta en la red, debe utilizar la interfaz de PolkadotJS Apps. Para hacerlo, primero debe importar una cuenta estilo Ethereum (dirección H160), lo que puede hacer siguiendo [esta guía](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account). Para este ejemplo, se importaron tres cuentas y se nombraron con nombres súper originales: Alice, Bob y Charley.

![Accounts in PolkadotJS](/images/governance/governance-proposal-1.png)

The proposal will set Bob's balance to `1500` via governance!

### Submitting a Preimage of the Proposal

The first step is to submit a preimage of the proposal. This is because the storage cost of large preimages can be pretty hefty, as the preimage contains all the information regarding the proposal itself. With this configuration, one account with more funds can submit a preimage and another account can submit the proposal.

Everything related to governance lives under the "Democracy" tab. Once there, click on the "Submit preimage" button.

![Submit Preimage](/images/governance/governance-proposal-2.png)

Here, you need to provide the following information:

 1. Select the account from which you want to submit the preimage
 2. Choose the pallet you want to interact with and the dispatchable function (or action) to propose. The action you choose will determine the fields that need to fill in the following steps. In this case, it is the `democracy` pallet and the `setBalance` function
 3. Set the address of which you want to change the balance
 4. Set the new balance that this address will hold. To read more about the types of balances, you can visit [this site](https://wiki.polkadot.network/docs/en/build-protocol-info#free-vs-reserved-vs-locked-vs-vesting-balance)
 5. Copy the preimage hash. This represents the proposal. You will use this hash when submitting the actual proposal
 6. Click the "Submit preimage" button and sign the transaction

![Fill in the Preimage Information](/images/governance/governance-proposal-3.png)

!!! note
    Make sure you copy the preimage hash, as it is necessary to submit the proposal.

Note that the storage cost of the preimage is displayed at the bottom left corner of this window. After the transaction is submitted, you will see some confirmations on the top right corner of the PolkadotJS Apps interface, but nothing will have changed in the main democracy screen. However, don't worry. If the transaction is confirmed, the preimage has been submitted.

### Submitting a Proposal

Once you have committed the preimage (check the previous section), the roadmap's next major milestone is to submit the proposal related to it. To do so, in the main democracy screen, click on "Submit proposal."

![Submit proposal](/images/governance/governance-proposal-4.png)

Here, you need to provide the following information:

 1. Select the account from which you want to submit the proposal (in this case, Alice)
 2. Enter the preimage hash related to the proposal. In this example, it is the hash of the `setBalance` preimage from the previous section
 3. Set the locked balance. This is the number of tokens the proposer bonds with his proposal. Remember that the proposal with the most amount of tokens locked goes to referendum. The minimum deposit is displayed just below this input tab
 4. Click the "Submit proposal" button and sign the transaction

![Fill in the Proposal Information](/images/governance/governance-proposal-5.png)

!!! note
    Tokens might be locked for an indeterminate amount of time because it is unknown when a proposal may become a referendum (if ever).

After the transaction is submitted, you will see some confirmations on the top right corner of the PolkadotJS Apps interface. You should also see the proposal listed in the "Proposals" section, displaying the proposer and the amounts of tokens locked, and it is now ready to be seconded!

![Proposal listed](/images/governance/governance-proposal-6.png)

### Seconding a Proposal

To second a proposal means that you agree with it and want to back it up with your tokens to help it reach public referenda. The amount of tokens to be locked is equal to the proposer's original deposit - no more, no less.

!!! note
    A single account can second a proposal multiple times. This is by design, as an account could just send tokens to different addresses and use them to second the proposal. What counts is the number of tokens backing up a proposal, not the number of vouches it has received.

This section outlines the steps to second the proposal made in the previous section. To do so, click the "Second" button that is available for each proposal that shows up in the proposals list.

![Proposal listed to Second](/images/governance/governance-proposal-7.png)

Here, you need to provide the following information:

 1. Select the account you want to second the proposal with (in this case, Charley)
 2. Verify the number of tokens required to second the proposal
 3. Click the "Second" button and sign the transaction

![Fill in Second Information](/images/governance/governance-proposal-8.png)

!!! note
    Tokens might be locked for an indeterminate amount of time because it is unknown when a proposal may become a referendum (if ever)

After the transaction is submitted, you will see some confirmations on the top right corner of the PolkadotJS Apps interface. You should also see the proposal listed in the "Proposals" section, displaying the proposer and the amounts of tokens locked and listing the users that have seconded this proposal!

![Proposal Seconded](/images/governance/governance-proposal-9.png)

