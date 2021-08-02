---
title: Proponer una Acción
description: Cómo enviar una propuesta para votar en Moonbeam a través de funciones de gobernanza
---

# Propuestas

![Governance Moonbeam Banner](/images/governance/governance-proposal-banner.png)

## Introducción {: #introduction } 

Como se menciona en la [página de descripción general de la gobernanza](/governance/overview/#definitions),una propuesta es una presentación a la cadena en la que el titular de un token sugiere que el sistema lleve a cabo una acción.

Las propuestas son uno de los elementos centrales del sistema de gobernanza porque son la principal herramienta para que las partes interesadas propongan acciones / cambios, que luego votan otras partes interesadas.

En Moonbeam, los usuarios podrán crear, segundar y votar propuestas utilizando su dirección H160 y su clave privada, es decir, su cuenta habitual de Ethereum.

Con el lanzamiento de [Moonbase Alpha v6](https://github.com/PureStake/moonbeam/releases/tag/v0.6.0),  los usuarios de la red ahora pueden enviar propuestas para referendos públicos en TestNet. Esta guía describe el proceso de cómo crear una propuesta. Los pasos irán desde su creación hasta que llegue a referendos públicos. Puede encontrar una guía sobre cómo votar una propuesta [aquí](/governance/voting/).

Se puede encontrar más información en las páginas Wiki de Polkadot relacionadas con la [gobernanza](https://wiki.polkadot.network/docs/learn-governance#council) y la [participación en la democracia](https://wiki.polkadot.network/docs/maintain-guides-democracy).

!!! nota
   Esta guía se realizó con una versión personalizada de Moonbeam con períodos cortos de lanzamiento / promulgación solo con fines de demostración.

## Definiciones {: #definitions } 

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
|     Periodo de lanzamiento       |     | 7200 bloques (1 dias) |
|     Período de reflexión      |     |   50400 bloques (7 dias)   |
| Depósito mínimo de preimagen |     |                                 0,004 DEV                                  |
| Depósito mínimo de propuesta |     |                                   4 DEV                                     |

## Hoja de ruta de una propuesta {: #roadmap-of-a-proposal } 

--8<-- 'text/governance/roadmap.md'

## Proponer una acción {: #proposing-an-action } 

En este apartado se repasa el proceso de creación de una propuesta, desde una preimagen hasta que llega a los referendos públicos. En lugar de hacer un ejemplo genérico, esta guía creará una propuesta real que servirá de base para esta guía y otras.

Para hacer una propuesta en la red, debe utilizar la interfaz de PolkadotJS Apps. Para hacerlo, primero debe importar una cuenta estilo Ethereum (dirección H160), lo que puede hacer siguiendo [esta guía](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account). Para este ejemplo, se importaron tres cuentas y se nombraron con nombres súper originales: Alice, Bob y Charley.

![Accounts in PolkadotJS](/images/governance/governance-proposal-1.png)

La propuesta establecerá el balance de Bob en `1500` a través de la gobernanza.

### Enviando una Preimagen de la Propuesta {: #submitting-a-preimage-of-the-proposal } 

El primer paso es enviar una pre imagen de la propuesta. Esto se debe a que el costo de almacenamiento de las preimágenes más grandes puede ser bastante elevado, ya que la pre imagen contiene toda la información concerniente a la propuesta en sí misma. Con esta configuración, una cuenta con más fondos puede enviar la pre imagen y otra cuenta puede enviar la propuesta.

Todo lo relacionado con la gobernanza se encuentra en la pestaña “Democracia”. Una vez ahí, seleccione el botón “Enviar preimagen”.

![Submit Preimage](/images/governance/governance-proposal-2.png)

Aquí necesitará proveer la información siguiente:

 1. Seleccione la cuenta desde la que desea enviar la preimagen.
 2. Elija la pallet con la que desea interactuar y la función (o acción) disponible para proponer. La acción que escoja determinará los campos que debe rellenar en los siguientes pasos. En este caso, es la pallet`democracy` y la función `setBalance` 
 3. Establezca la dirección desde la cual desea cambiar el balance.
 4. Establezca el nuevo balance que esta dirección deberá mantener. Para leer más acerca de los tipos de balances, puede visitar [este link](https://wiki.polkadot.network/docs/build-protocol-info#free-vs-reserved-vs-locked-vs-vesting-balance)
 5. Copie el hash de la preimagen. Esto representa la propuesta. Utilizará este hash al enviar la propuesta real
 6. Click en el boton "Enviar preimage" y firmar la transacción.

![Fill in the Preimage Information](/images/governance/governance-proposal-3.png)

!!! nota
    Asegúrate de copiar el hash de la preimagen, ya que es necesario para enviar la propuesta.

Note que el costo de almacenamiento de la pre imagen se muestra en el botón de la esquina izquierda de esta ventana. Después de haber enviado la transacción, podrá ver algunas confirmaciones en la esquina superior derecha de la interfaz de la Polkadot JS Apps, pero nada habrá cambiado en la pantalla principal correspondiente a la democracia. No obstante, no debe preocuparse. Si la transacción ha sido confirmada, la pre imagen ha sido enviada.

### Enviando una propuesta {: #submitting-a-proposal } 

Una vez que ya ha comprometido la pre imagen (consulte la sección anterior), el siguiente hito en el roadmap es enviar la propuesta correspondiente a la misma. Para hacerlo, seleccione “enviar propuesta” en la pantalla principal de democracia.

![Submit proposal](/images/governance/governance-proposal-4.png)

Aquí, necesitará proveer la siguiente información:

 1. Seleccione la cuenta desde la cual desea enviar la propuesta (en este caso, Alice)
 2. Introduzca el hash de la pre imagen correspondiente a la propuesta. En este ejemplo, es el hash de  `setBalance`, la pre imagen de la sección anterior.
 3. Establezca el balance a bloquear. Este es el número de tokens que el usuario que propone vincula a su propuesta. Recuerde que la propuesta con la mayor cantidad de tokens bloqueados va a referéndum. El depósito mínimo es mostrado justo debajo de esta pestaña de entrada.
 4. Click en el botón "Enviar propuesta" y firme la transacción.

![Fill in the Proposal Information](/images/governance/governance-proposal-5.png)

!!! nota
    Los tokens pueden estar bloqueados por una cantidad de tiempo indeterminada, ya que no se puede determinar cuando una propuesta podrá convertirse en referéndum (ni si lo hará).

Después que la transacción es enviada, podrá ver algunas confirmaciones en la esquina superior derecha de la interfaz de Polkadot JS Apps. También debería poder ver la propuesta listada en la sección “Propuestas”, mostrándose la propuesta y la cantidad de tokens bloqueados, y ahora está lista para ser secundada.

![Proposal listed](/images/governance/governance-proposal-6.png)

### Secundando una propuesta {: #seconding-a-proposal } 

El secundar una propuesta significa que está de acuerdo con ella y quiere respaldarla con sus tokens para ayudarla a alcanzar el referéndum público. La cantidad de tokens a ser bloqueados será igual al depósito de la propuesta original- ni más, ni menos.

!!! nota
    Una misma cuenta puede secundar una propuesta en múltiples ocasiones. Esto es por diseño, ya que una cuenta pudiera enviar tokens a diferentes direcciones y usarlas para secundar la propuesta. Lo que es tomado en cuenta, es el número de tokens respaldando la propuesta, no el número de comprobantes que ha recibido.

Esta sección describe los pasos para secundar la propuesta hecha en la sección anterior. Para hacerlo, seleccione el botón “Secundar” que está disponible para cada propuesta que se muestre en la lista de propuestas. 

![Proposal listed to Second](/images/governance/governance-proposal-7.png)

Aquí, necesita proveer la siguiente información:

 1. Seleccione la cuenta que desea usar para secundar la propuesta (en este caso, Charly)
 2. Verifique el número de tokens requeridos para secundar la propuesta.
 3. Seleccione el botón “Second” y firme la transacción.

![Fill in Second Information](/images/governance/governance-proposal-8.png)

!!! nota
Los tokens pueden estar bloqueados por una cantidad de tiempo indeterminada, ya que no se puede determinar cuando una propuesta podrá convertirse en referéndum (o si lo hará).

Después que la transacción es enviada, podrá ver algunas confirmaciones en la esquina superior derecha de la interfaz de Polkadot JS Apps. También debería poder ver la propuesta listada en la sección “Propuestas”, mostrando la propuesta y la cantidad de tokens bloqueados y enumerando los usuarios que han secundado la propuesta.

![Proposal Seconded](/images/governance/governance-proposal-9.png)

