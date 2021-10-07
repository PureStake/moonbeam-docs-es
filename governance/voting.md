---
title: Votar una Propuesta
description: Cómo votar una propuesta para que se apruebe o rechace en Moonbeam a través de funciones de gobernanza
---

# Propuestas

![Governance Moonbeam Banner](/images/tokens/governance/voting/voting-banner.png)

## Introducción {: #introduction } 

Una vez que una propuesta llega a referendos públicos, los poseedores de tokens pueden votar sobre ella usando sus propios tokens. Dos factores definen el peso que tiene un voto: la cantidad de tokens bloqueados y la duración del bloqueo (llamado condena). Esto es para asegurar que haya una aceptación económica del resultado para evitar la venta de votos. En consecuencia, cuanto más tiempo esté dispuesto a bloquear sus tokens, más fuerte será su voto. También tiene la opción de no bloquear tokens en absoluto, pero el peso de los votos se reduce drásticamente.

Los referendos son esquemas de votación simples, inclusivos y basados ​​en intereses. Cada referéndum tiene una propuesta asociada que sugiere que se lleve a cabo una acción. Tienen una duración fija, después de la cual se cuentan los votos, y la acción se promulga si se aprueba el voto.

En Moonbeam, los usuarios podrán crear, segundar y votar propuestas utilizando su dirección H160 y su clave privada, es decir, su cuenta habitual de Ethereum.

Con el lanzamiento de [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0), los usuarios de la red ahora pueden enviar propuestas para referendos públicos y votar sobre ellas. Esta guía describe cómo votar sobre una propuesta que ha llegado a un referéndum público. Puede encontrar una guía sobre cómo enviar una propuesta [aquí](/governance/proposals/).

Puede encontrar más información relacionada con la [gobernanza](https://wiki.polkadot.network/docs/learn-governance) y la [participación en la democracia](https://wiki.polkadot.network/docs/maintain-guides-democracy) en las páginas Wiki de Polkadot.

!!! nota
    Esta guía se realizó con una versión personalizada de Moonbeam con períodos cortos de lanzamiento / promulgación solo con fines de demostración.

## Definiciones {: #definitions } 

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
|       Período de votación       |     |  36000 bloques (5 dias)  |
|      Promulgar período       |     | 7200 bloques (1 dias) |
| Número máximo de votos |     |                                      100                                       |

## Hoja de ruta de una propuesta {: #roadmap-of-a-proposal } 

--8<-- 'text/governance/roadmap.md'

## Votar en un referéndum {: #voting-on-a-referendum } 

Esta sección repasa el proceso de votación de un referéndum. La guía asume que ya existe una, en este caso, la creada en [esta guía](/governance/proposals/).

Para votar una propuesta en la red, debe utilizar la interfaz de PolkadotJS Apps. Para hacerlo, primero debe importar una cuenta estilo Ethereum (dirección H160), lo que puede hacer siguiendo [esta guía](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account). Para este ejemplo, se importaron tres cuentas y se nombraron con nombres súper originales: Alice, Bob y Charley.

![Accounts in PolkadotJS](/images/tokens/governance/proposals-1.png)

¡La propuesta que se está votando pondrá el saldo de Bob en "1500" a través de la gobernanza! 

### Cómo votar {: #how-to-vote } 

Votar en Moonbeam es bastante sencillo. Todo lo relacionado con la gobernabilidad vive en la pestaña "Democracia", donde (en la imagen) se puede notar que hay un `1`, que indica que hay un tema de democracia pendiente (ya sean propuestas o referendos). Una vez allí, puede ver los detalles del referéndum que desea votar haciendo clic en la flecha junto a la descripción. El número junto a la acción y la descripción se llama índice de referéndum (en este caso, es 0). Cuando esté listo, haga clic en el botón "Votar".

![Vote Button](/images/legacy/governance/governance-vote-1.png)

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

![Vote Submission](/images/legacy/governance/governance-vote-2.png)

!!! nota
   Los períodos de bloqueo que se muestran en la imagen anterior no deben tomarse como referencia. Esta guía se realizó con una versión personalizada de Moonbeam con períodos cortos de lanzamiento / promulgación solo con fines de demostración.

En este caso, Alice y Bob han decidido "Vote Aye" en la propuesta con una convicción de `6x`. Por otro lado, Charley ha decidido "Vote No" en la propuesta, pero optó por no bloquear ningún token (sus tokens solo se bloquean durante la duración del referéndum), por lo que su condena fue `0.1x`.Con tales distribuciones de votos, los resultados parciales se pueden ver en la pestaña principal "Democracia".

![Vote Information](/images/legacy/governance/governance-vote-3.png)

De la votación, hay algunas conclusiones clave:

 - El voto ponderado de Alice es 10800 unidades. Es decir, sus 1800 fichas bloqueadas multiplicaron su convicción por x6
 - El voto ponderado de Charley es de 80 unidades. Es decir, sus 800 fichas sin período de bloqueo (solo durante el referéndum) hicieron que su factor de convicción fuera x0.1
 - Tanto el período de votación restante como el tiempo antes de que se promulgue la propuesta (si se aprueba) se muestran en la pantalla
 - La participación general (en porcentaje) es solo del 0,21%. Esto se calcula como el número total de tokens de votación (2600) dividido por la cantidad total de tokens en la red (1.22M en este caso)
 -  Aunque la participación es bastante baja, la propuesta se aprueba tentativamente debido a la aprobación de la supermayoría. Se puede encontrar más información en [esta sección](/governance/voting/#positive-turnout-bias)
 - Es importante anotar el índice del referéndum, ya que es necesario para desbloquear los tokens más adelante cuando expire el período de bloqueo. Actualmente no hay forma de recuperar el índice de referéndum una vez que se haya promulgado

Una vez que haya expirado el período de votación, la propuesta será visible en la pestaña "Dispatch" si se aprueba. Aquí también puede ver el tiempo que queda hasta que se promulgue la propuesta.

![Proposal Enactment](/images/tokens/governance/voting/vote-5.png)

Recuerde que, para este ejemplo, la función `setBalance` se usó para establecer el saldo de Bob en 1500 tokens. Una vez que haya pasado el período de promulgación, puede volver a la pestaña "Cuentas" para verificar que la propuesta se convirtió en ley.

![Proposal Result](/images/legacy/governance/governance-vote-5.png)

### Votación delegada {: #delegate-voting }

Los titulares de tokens tienen la opción de delegar su voto a otra cuenta en cuya opinión confían. La cuenta que se delega no necesita realizar ninguna acción en particular. Cuando votan, el peso del voto (es decir, las fichas multiplicadas por el multiplicador de convicciones elegido por el delegador) se agrega a su voto.

Para delegar su voto, primero navegue al menú "Extrinsics" en la pestaña "Desarrolladores".

![Extrinsics Menu](/images/tokens/governance/voting/vote-6.png)

Aquí, debe proporcionar la siguiente información:

 1. Seleccione la cuenta desde la que desea delegar su voto
 2. Elija la pallet con la que desea interactuar. En este caso, es la de "democracy"
 3. Elija el método extrínseco que se utilizará para la transacción. Esto determinará los campos que deben completarse en los siguientes pasos. En este caso, es "delegate"
 4. Seleccione la cuenta a la que desea delegar su voto
 5. Establece la convicción del voto, que determina su peso (`vote_weight = tokens * conviction_multiplier`). El multiplicador de convicciones está relacionado con la cantidad de períodos de promulgación durante los que se bloquearán los tokens. En consecuencia, cuanto más tiempo esté dispuesto a bloquear sus tokens, más fuerte será su voto. También tiene la opción de no bloquear tokens en absoluto, pero el peso de los votos se reduce drásticamente
 6. Establezca la cantidad de tokens que desea delegar a la cuenta proporcionada antes
 7. Haga clic en el botón "Enviar transacción" y firme la transacción.

![Extrinsics Transaction for Delegation](/images/tokens/governance/voting/vote-7.png)

En este ejemplo, Alice delegó un peso total de 1000 (1000 tokens con un factor de convicción x1) a Charley.

!!! nota
   Otra forma de delegar votos es en la pestaña "Cuentas". Haga clic en los tres puntos de la cuenta desde la que desea delegar su voto y complete la información como antes.

Una vez que la cuenta haya delegado su voto, el peso total del voto delegado se asignará a la opción que la cuenta seleccionó. Para este ejemplo, Charley ha decidido votar a favor de una propuesta que se encuentra en referéndum público. Votó con un peso total de 800 (800 fichas con un factor de convicción x1). Pero debido a que Alice le delegó 1000 votos, los votos "Sí" suman 1800 unidades.

![Total Votes with Delegation](/images/tokens/governance/voting/vote-9.png)

Para eliminar la delegación, repita el proceso descrito anteriormente, pero seleccione la extrinsic `undelegate` en el paso 3.

De la delegación de votos, hay algunas conclusiones clave:

 - Si un titular de los tokens eliminara la delegación de votos durante un referéndum público en el que se utilizaron los votos delegados, estos se eliminarían del recuento.
 - Un titular de tokens que delegó votos todavía tiene una participación económica. Esto significa que si la opción que el delegador seleccionó fuera a ganar, los tokens delegados se bloquean por el número de períodos de bloqueo.
 - Los tokens delegados para votar ya no forman parte del saldo libre del titular del token. Para leer más sobre los tipos de saldos, puede visitar [este sitio](https://wiki.polkadot.network/docs/build-protocol-info#free-vs-reserved-vs-locked-vs-vesting-balance)
 - Un titular de tokens que delegó tokens no puede participar en un referéndum público. Primero, el poseedor del token debe anular la delegación de su voto.
 - Un titular de tokens que delegó tokens debe desbloquear manualmente sus tokens bloqueados después de que el período de bloqueo haya expirado. Para ello es necesario conocer el índice de referéndum
 - 
### Desbloqueo de tokens {: #unlocking-locked-tokens } 

Cuando los poseedores de tokens votan, los tokens utilizados se bloquean y no se pueden transferir. Puede verificar si tiene tokens bloqueados en la pestaña "Cuentas", expandiendo los detalles de la cuenta de la dirección para consultar. Allí, verá diferentes tipos de saldos (puede leer más información sobre cada tipo [aquí](https://wiki.polkadot.network/docs/build-protocol-info#free-vs-reserved-vs-locked-vs-vesting-balance)). Si pasa el cursor sobre el icono junto a "democracia", se mostrará un panel de información que le indicará el estado actual de lo bloqueado. Los diferentes estados de bloqueo incluyen:

 - Bloqueado debido a un referéndum en curso, lo que significa que ha usado sus tokens y tiene que esperar hasta que finalice el referéndum, incluso si ha votado con un factor de convicción sin bloqueo
 - Bloqueado debido al multiplicador de convicción seleccionado, que muestra el número de bloques y el tiempo restante
 - Bloqueo caducado, lo que significa que ahora puede recuperar sus tokens

![Account Lock Status](/images/tokens/governance/voting/vote-10.png)

Una vez que expire el bloqueo, puede solicitar la devolución de sus tokens. Para hacerlo, navegue hasta el menú "Extrínsics" en la pestaña "Desarrolladores".

![Extrinsics Menu](/images/tokens/governance/voting/vote-11.png)

Aquí, se deben enviar dos extrínsecos diferentes. Primero, debe proporcionar la siguiente información:

 1. Seleccione la cuenta de la que desea recuperar sus token
 2. Elija la pallet con la que desea interactuar. En este caso, es la de "democracy"
 3. Elija el método extrínseco que se utilizará para la transacción. Esto determinará los campos que deben completarse en los siguientes pasos. En este caso, es el extrinsic `removevote`. Este paso es necesario para desbloquear los tokens. Este elemento extrínseco también se puede utilizar para eliminar su voto de un referéndum.
 4. ngrese el índice de referéndum. Este es el número que apareció en el lado izquierdo de la pestaña "Democracia". En este caso, es 0
 5. Haga clic en el botón "Enviar transacción" y firme la transacción.

![Remove Vote Extrinsics](/images/tokens/governance/voting/vote-12.png)

Para el siguiente extrínseco, debe proporcionar la siguiente información:

 1. Seleccione la cuenta de la que desea recuperar sus token
 2. Elija la pallet con la que desea interactuar. En este caso, es la de "democracy"
 3. Elija el método extrínseco que se utilizará para la transacción. Esto determinará los campos que deben completarse en los siguientes pasos. En este caso, es el extrinsic`desbloquear` 
 4. Ingrese la cuenta de destino que recibirá los tokens desbloqueados. En este caso, los tokens se devolverán a Alice.
 5. Haga clic en el botón "Enviar transacción" y firme la transacción.

![Unlock Extrinsics](/images/tokens/governance/voting/vote-13.png)

Una vez que se realiza la transacción, los tokens bloqueados deben desbloquearse. Para volver a verificar, puede volver a la pestaña "Cuentas" y ver que, para este ejemplo, Alice tiene su saldo completo como "transferible".

![Check Balance](/images/tokens/governance/voting/vote-14.png)

## Sesgo de participación positiva {: #positive-turnout-bias } 

Los referendos públicos utilizan una métrica de sesgo de participación positiva, es decir, una fórmula de aprobación por supermayoría. La ecuación es la siguiente:

![Positive Turnout Bias](/images/tokens/governance/voting/vote-bias.png)

Dónde:

 - **Aprobar** — número de votos "Aye" (incluye el multiplicador de convicciones)
 - **En contra** — número de votos "Nay" (incluye el multiplicador de convicciones)
 - **Participación** — el número total de fichas de votación (sin incluir el multiplicador de convicciones)
 - **Electorado** —  el número total de tokens emitidos en la red.

En el ejemplo anterior, estos números fueron:

|  Variable  |     |         Valor         |
| :--------: | :-: | :-------------------: |
|  Aprobar   |     |   10800 (1800 x 6)    |
|  En contra  |     |    80 (800 x 0.1)     |
|  Participación  |     |   2600 (1800 + 800)   |
| Electorado |     |         1.22M         |
| **Resultado** |     | 1.5 < 9.8 (¡Aye gana!) |

En resumen, se requiere una gran mayoría de votos aye para aprobar una propuesta con baja participación, pero a medida que aumenta la participación, se convierte en una mayoría simple.
