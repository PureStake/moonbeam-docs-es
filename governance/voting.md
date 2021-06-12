---
title: Votar una propuesta
description: Cómo votar una propuesta para que se apruebe o rechace en Moonbeam a través de funciones de gobernanza
---

# Propuestas

![Governance Moonbeam Banner](/images/governance/governance-voting-banner.png)

## Introducción

Una vez que una propuesta llega a referendos públicos, los poseedores de tokens pueden votar sobre ella usando sus propios tokens. Dos factores definen el peso que tiene un voto: la cantidad de tokens bloqueados y la duración del bloqueo (llamado condena). Esto es para asegurar que haya una aceptación económica del resultado para evitar la venta de votos. En consecuencia, cuanto más tiempo esté dispuesto a bloquear sus tokens, más fuerte será su voto. También tiene la opción de no bloquear tokens en absoluto, pero el peso de los votos se reduce drásticamente.

Los referendos son esquemas de votación simples, inclusivos y basados ​​en intereses. Cada referéndum tiene una propuesta asociada que sugiere que se lleve a cabo una acción. Tienen una duración fija, después de la cual se cuentan los votos, y la acción se promulga si se aprueba el voto.

En Moonbeam, los usuarios podrán crear, segundar y votar propuestas utilizando su dirección H160 y su clave privada, es decir, su cuenta habitual de Ethereum.

Con el lanzamiento de [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0), los usuarios de la red ahora pueden enviar propuestas para referendos públicos y votar sobre ellas. Esta guía describe cómo votar sobre una propuesta que ha llegado a un referéndum público. Puede encontrar una guía sobre cómo enviar una propuesta [aquí](/governance/proposals/).

Puede encontrar más información relacionada con la [gobernanza](https://wiki.polkadot.network/docs/en/learn-governance) y la [participación en la democracia](https://wiki.polkadot.network/docs/en/maintain-guides-democracy) en las páginas Wiki de Polkadot.

!!! nota
    Esta guía se realizó con una versión personalizada de Moonbeam con períodos cortos de lanzamiento / promulgación solo con fines de demostración.

## Definiciones

Algunos de los parámetros clave de esta guía son los siguientes:

 - **Período de votación** — el tiempo que los poseedores de fichas tienen para votar por un referéndum (duración de un referéndum)
 - **Voto** —  una herramienta utilizada por los titulares de tokens para aprobar o rechazar una propuesta. El peso que tiene un voto se define por dos factores: el número de tokens bloqueados y la duración del bloqueo (llamado condena).
 - **Participación** — el número total de fichas de votación
 - **Electorado** — el número total de tokens emitidos en la red.
 - **Número máximo de votos** — el número máximo de votos por cuenta
 - **Período de promulgación** — el tiempo entre la aprobación y la promulgación de una propuesta (hacer ley). También es el período mínimo de bloqueo al votar.
 - **Período de bloqueo** — el tiempo (después de la promulgación de la propuesta) que se bloquean las fichas de los votantes ganadores. Los usuarios aún pueden usar estos tokens para apostar o votar
 - **Delegación** — el acto de transferir su poder de voto a otra cuenta hasta una cierta condena.

Actualmente, para Moonbase Alpha:

|        Variable         |     |                                                         Valor                                                         |
| :---------------------: | :-: | :-------------------------------------------------------------------------------------------------------------------: |
|       Período de votación       |     |  {{ networks.moonbase.democracy.vote_period.blocks}} bloques ({{ networks.moonbase.democracy.vote_period.days}} dias)  |
|      Promulgar período       |     | {{ networks.moonbase.democracy.enact_period.blocks}} bloques ({{ networks.moonbase.democracy.enact_period.days}} dias) |
| Número máximo de votos |     |                                      {{ networks.moonbase.democracy.max_votes}}                                       |

## Hoja de ruta de una propuesta

--8<-- 'text/governance/roadmap.md'

## Votar en un referéndum

Esta sección repasa el proceso de votación de un referéndum. La guía asume que ya existe una, en este caso, la creada en [esta guía](/governance/proposals/).

Para votar una propuesta en la red, debe utilizar la interfaz de PolkadotJS Apps. Para hacerlo, primero debe importar una cuenta estilo Ethereum (dirección H160), lo que puede hacer siguiendo [esta guía](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account). Para este ejemplo, se importaron tres cuentas y se nombraron con nombres súper originales: Alice, Bob y Charley.

![Accounts in PolkadotJS](/images/governance/governance-proposal-1.png)

¡La propuesta que se está votando pondrá el saldo de Bob en "1500" a través de la gobernanza! 

### Cómo votar

Votar en Moonbeam es bastante sencillo. Todo lo relacionado con la gobernabilidad vive en la pestaña "Democracia", donde (en la imagen) se puede notar que hay un `1`, que indica que hay un tema de democracia pendiente (ya sean propuestas o referendos). Una vez allí, puede ver los detalles del referéndum que desea votar haciendo clic en la flecha junto a la descripción. El número junto a la acción y la descripción se llama índice de referéndum (en este caso, es 0). Cuando esté listo, haga clic en el botón "Votar".

![Vote Button](/images/governance/governance-vote-1.png)

Aquí, debe proporcionar la siguiente información:

 1. Selecciona la cuenta con la que quieres votar
 2. Ingrese la cantidad de tokens con los que desea votar. Estos se bloquearán durante el tiempo especificado en el siguiente paso.
 3. Establece la condena del voto, que determina su peso (`vote_weight = tokens * conviction_multiplier`). El multiplicador de convicciones está relacionado con la cantidad de períodos de promulgación durante los que estarán bloqueados los tokens. En consecuencia, cuanto más tiempo esté dispuesto a bloquear sus tokens, más fuerte será su voto. También tiene la opción de no bloquear los tokens en absoluto, pero el peso de los votos se reduce drásticamente (los tokens siguen bloqueados durante la duración del referéndum)

   | Períodos de bloqueo |     | Multiplicador de convicciones |
   | :----------: | :-: | :-------------------: |
   |      0       |     |          0.1          |
   |      1       |     |           1           |
   |      2       |     |           2           |
   |      4       |     |           3           |
   |      8       |     |           4           |
   |      16      |     |           5           |
   |      32      |     |           6           |

 4. Haga clic en "Vote Aye" para aprobar la propuesta o "Vote No" para rechazar la propuesta y, luego, firme la transacción.

![Vote Submission](/images/governance/governance-vote-2.png)

!!! nota
   Los períodos de bloqueo que se muestran en la imagen anterior no deben tomarse como referencia. Esta guía se realizó con una versión personalizada de Moonbeam con períodos cortos de lanzamiento / promulgación solo con fines de demostración.

En este caso, Alice y Bob han decidido "Vote Aye" en la propuesta con una convicción de `6x`. Por otro lado, Charley ha decidido "Vote No" en la propuesta, pero optó por no bloquear ningún token (sus tokens solo se bloquean durante la duración del referéndum), por lo que su condena fue `0.1x`.Con tales distribuciones de votos, los resultados parciales se pueden ver en la pestaña principal "Democracia".

![Vote Information](/images/governance/governance-vote-3.png)

De la votación, hay algunas conclusiones clave:

 - El voto ponderado de Alice es 10800 unidades. Es decir, sus 1800 fichas bloqueadas multiplicaron su convicción por x6
 - Charley's weighted vote is 80 units. That is, his 800 tokens with no locking period (only during referendum) made his conviction factor x0.1
 - Both the remaining voting period and time before the proposal is enacted (if passed) are shown on the screen
 - The overall turnout (in percentage) is just 0.21%. This is calculated as the total number of voting tokens (2600) divided by the total amount of tokens in the network (1.22M in this case)
 - Even though the turnout is quite low, the proposal is tentatively approved because of the super-majority approval. More information can be found in [this section](/governance/voting/#positive-turnout-bias)
 - It is important to write down the referendum index, as this is needed to unlock the tokens later when the locking period expires. Currently there is no way to retrieve the referendum index once it has been enacted

After the voting period has expired, the proposal will be visible under the "Dispatch" tab if approved. In here, you can also see the time remaining until the proposal is enacted.

![Proposal Enactment](/images/governance/governance-vote-4.png)

Remember that, for this example, the `setBalance` function was used to set Bob's balance to 1500 tokens. Once the enactment period has passed, you can go back to the "Accounts" tab to verify that the proposal was made law.

![Proposal Result](/images/governance/governance-vote-5.png)

### Delegate Voting

Token holders have the option to delegate their vote to another account whose opinion they trust. The account being delegated does not need to make any particular action. When they vote, the vote weight (that is, tokens times the conviction multiplier chose by the delegator) is added to its vote.

To delegate your vote, first, navigate to the "Extrinsics" menu under the "Developers" tab.

![Extrinsics Menu](/images/governance/governance-vote-6.png)

Here, you need to provide the following information:

 1. Select the account from which you want to delegate your vote
 2. Choose the pallet you want to interact with. In this case, it is the `democracy` pallet
 3. Choose the extrinsic method to use for the transaction. This will determine the fields that need to fill in the following steps. In this case, it is `delegate` extrinsic
 4. Select the account to which you want to delegate your vote
 5. Set the vote conviction, which determines its weight (`vote_weight = tokens * conviction_multiplier`). The conviction multiplier is related to the number of enactment periods the tokens will be locked for. Consequently, the longer you are willing to lock your tokens, the stronger your vote will be weighted. You also have the option of not locking tokens at all, but vote weight is drastically reduced
 6. Set the number of tokens you want to delegate to the account provided before
 7. Click the "Submit Transaction" button and sign the transaction

![Extrinsics Transaction for Delegation](/images/governance/governance-vote-7.png)

In this example, Alice delegated a total weight of 1000 (1000 tokens with an x1 conviction factor) to Charley.

!!! note
    Another way to delegate votes is under the "Accounts" tab. Click on the three dots of the account from which you want to delegate your vote and fill in the information as before.

Once the account you have delegated your vote to votes, the total vote weight delegated will be allocated to the option that the account selected. For this example, Charley has decided to vote in favor of a proposal that is in public referendum. He voted with a total weight of 800 (800 tokens with an x1 conviction factor). But because Alice delegated 1000 vote weight to him, "Aye" votes total 1800 units.

![Total Votes with Delegation](/images/governance/governance-vote-8.png)

To remove delegation, repeat the process described before, but select the `undelegate` extrinsic in step 3.

From vote delegation, there are some key takeaways:

 - If a token holder were to remove the vote delegation during a public referendum where the delegated votes were used, these would be removed from the tally
 - A token holder that delegated votes still has an economic buy-in. This means that if the option the delegator selected were to win, the tokens delegated are locked for the number of lock periods
 - The tokens delegated for voting are no longer part of the token holder's free balance. To read more about the types of balances, you can visit [this site](https://wiki.polkadot.network/docs/en/build-protocol-info#free-vs-reserved-vs-locked-vs-vesting-balance)
 - A token holder that delegated tokens can't participate in public referendum. First, the token holder must undelegate his vote
 - A token holder that delegated tokens needs to manually unlock his locked tokens after the locking period has expired. For this, it is necessary to know the referendum index

### Unlocking Locked Tokens

When token holders vote, the tokens used are locked and cannot be transferred. You can verify if you have any locked tokens in the "Accounts" tab, expanding the address's account details to query. There, you will see different types of balances (you can read more information about each type [here](https://wiki.polkadot.network/docs/en/build-protocol-info#free-vs-reserved-vs-locked-vs-vesting-balance)). If you hover over the icon next to "democracy," an information panel will show telling you the current status of your lock. Different lock status includes:

 - Locked because of an ongoing referendum, meaning that you've used your tokens and have to wait until the referendum finishes, even if you've voted with a no-lock conviction factor
 - Locked because of the conviction multiplier selected, displaying the number of blocks and time left
 - Lock expired, meaning that you can now get your tokens back

![Account Lock Status](/images/governance/governance-vote-9.png)

Once the lock is expired, you can request your tokens back. To do so, navigate to the "Extrinsics" menu under the "Developers" tab.

![Extrinsics Menu](/images/governance/governance-vote-10.png)

Here, two different extrinsics need to be sent. First, you need to provide the following information:

 1. Select the account from which you want to recover your tokens
 2. Choose the pallet you want to interact with. In this case, it is the `democracy` pallet
 3. Choose the extrinsic method to use for the transaction. This will determine the fields that need to fill in the following steps. In this case, it is `removeVote` extrinsic. This step is necessary to unlock the tokens. This extrinsic can be used as well to remove your vote from a referendum
 4. Enter the referendum index. This is the number that appeared on the left-hand side in the "Democracy" tab. In this case, it is 0
 5. Click the "Submit Transaction" button and sign the transaction

![Remove Vote Extrinsics](/images/governance/governance-vote-11.png)

For the next extrinsic, you need to provide the following information:

 1. Select the account from which you want to recover your tokens
 2. Choose the pallet you want to interact with. In this case, it is the `democracy` pallet
 3. Choose the extrinsic method to use for the transaction. This will determine the fields that need to fill in the following steps. In this case, it is `unlock` extrinsic
 4. Enter the target account that will receive the unlocked tokens. In this case, the tokens will be returned to Alice
 5. Click the "Submit Transaction" button and sign the transaction

![Unlock Extrinsics](/images/governance/governance-vote-12.png)

Once the transaction goes through, the locked tokens should be unlocked. To double-check, you can go back to the "Accounts" tab and see that, for this example, Alice has her full balance as "transferable."

![Check Balance](/images/governance/governance-vote-13.png)

## Positive Turnout Bias

Public referenda use a positive turnout bias metric, that is, a super-majority approval formula. The equation is the following:

![Positive Turnout Bias](/images/governance/governance-vote-bias.png)

Where:

 - **Approve** — number of "Aye" votes (includes the conviction multiplier)
 - **Against** — number of "Nay" votes (includes the conviction multiplier)
 - **Turnout** — the total number of voting tokens (without including the conviction multiplier)
 - **Electorate** — the total number of tokens issued in the network

In the previous example, these numbers were:

|  Variable  |     |         Value         |
| :--------: | :-: | :-------------------: |
|  Approve   |     |   10800 (1800 x 6)    |
|  Against   |     |    80 (800 x 0.1)     |
|  Turnout   |     |   2600 (1800 + 800)   |
| Electorate |     |         1.22M         |
| **Result** |     | 1.5 < 9.8 (Aye wins!) |

In short, a heavy super-majority of aye votes is required to approve a proposal at low turnouts, but as turnout increases, it becomes a simple majority.

