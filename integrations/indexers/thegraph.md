---
title: The Graph
description: Cree API utilizando el protocolo de indexación Graph en Moonbeam
---

# Usando The Graph en Moonbeam

![The Graph on Moonbeam](/images/thegraph/thegraph-banner.png)

## Introducción

Los protocolos de indexación organizan la información de manera que las aplicaciones puedan acceder a ella de manera más eficiente. Por ejemplo, Google indexa todo Internet para proporcionar información rápidamente cuando busca algo.

Graph es un protocolo de indexación descentralizado y de código abierto para consultar redes como Ethereum. En resumen, proporciona una forma de almacenar de manera eficiente los datos emitidos por eventos de contratos inteligentes para que otros proyectos o dApps puedan acceder a ellos fácilmente.

Además, los desarrolladores pueden crear API, llamadas Subgraphs. Los usuarios u otros desarrolladores pueden usar Subgraphs para consultar datos específicos de un conjunto de contratos inteligentes. Los datos se obtienen con una API GraphQL estándar. Puede visitar [su documentación](https://thegraph.com/docs/introduction#what-the-graph-is) para leer más sobre el protocolo Graph.

Con la introducción de los módulos de rastreo de Ethereum en [Moonbase Alpha v7](https://github.com/PureStake/moonbeam/releases/tag/v0.7.0), The Graph es capaz de indexar datos de blockchain en Moonbeam.

Esta guía lo lleva a través de la creación de un subgráfico simple para un contrato de lotería en Moonbase Alpha.

## Comprobación de requisitos previos

Para usar The Graph en Moonbase Alpha, tiene dos opciones:

 - Ejecute un Graph Node contra Moonbase Alpha y apunte su Subgraph hacia él. Para hacerlo, puede seguir [este tutorial](/node-operators/indexers/thegraph-node/)
 - Apunte su Subgraph a la API Graph a través del [sitio web Graph Explorer](https://thegraph.com/explorer/). Para hacerlo, debe crear una cuenta y tener un token de acceso.
 
## El contrato de lotería

Para este ejemplo, se utiliza un simple contrato de lotería. Puede encontrar el archivo Solidity en [este enlace](https://github.com/PureStake/moonlotto-subgraph/blob/main/contracts/MoonLotto.sol). 

El contrato alberga una lotería en la que los jugadores pueden comprar un boleto para sí mismos o regalar uno a otro usuario. Cuando haya pasado 1 hora, si hay 10 participantes, el siguiente jugador que se una a la lotería ejecutará una función que elige al ganador. Todos los fondos almacenados en el contrato se envían al ganador, después de lo cual comienza una nueva ronda.

Las principales funciones del contrato son las siguientes:

 - **joinLottery** —  sin entradas. Función para ingresar a la ronda actual de la lotería, el valor (cantidad de tokens) enviado al contrato debe ser igual al precio del boleto
 - **giftTicket** —  una entrada: la dirección del destinatario del ticket. Similar a, `joinLottery` pero el propietario del boleto se puede configurar en una dirección diferente
 - **enterLottery** — una entrada: la dirección del propietario del boleto. Una función interna que maneja la lógica de los billetes de lotería. Si ha pasado una hora y hay al menos 10 participantes, llama a la `pickWinner` función
 - **pickWinner** —  sin entradas. Una función interna que selecciona al ganador de la lotería con un generador de números pseudoaleatorios (no es seguro, solo con fines de demostración). Maneja la lógica de transferir fondos y restablecer la variable para la próxima ronda de lotería.

### Eventos del contrato de lotería

The Graph utiliza los eventos emitidos por el contrato para indexar los datos. El contrato de lotería emite solo dos eventos:

 - **PlayerJoined** — en la `enterLottery` función. Proporciona información relacionada con la última entrada de lotería, como la dirección del jugador, la ronda de lotería actual, si el boleto fue regalado y el monto del premio de la ronda actual.
 - **LotteryResult** — en la `pickWinner` función. Proporciona información sobre el sorteo de una ronda en curso, como la dirección del ganador, la ronda de lotería actual, si el boleto ganador fue un regalo, el monto del premio y la marca de tiempo del sorteo.

## Crear un subgrafo

Esta sección describe el proceso de creación de un subgrafo. Para Lottery Subgraph, se preparó un [repositorio de GitHub](https://github.com/PureStake/moonlotto-subgraph) con todo lo que necesita para ayudarlo a comenzar. El repositorio también incluye el contrato de lotería, así como un archivo de configuración de Hardhat y un script de implementación. Si no está familiarizado con él, puede consultar nuestra [guía de integración de Hardhat](/integrations/hardhat/). 

Para comenzar, primero clone el repositorio e instale las dependencias:

```
git clone https://github.com/PureStake/moonlotto-subgraph \
&& cd moonlotto-subgraph && yarn
```

Ahora, puede crear los tipos de TypeScript para The Graph ejecutando:

```
npx graph codegen --output-dir src/types/
```

!!! note
    La creación de los tipos requiere que tenga los archivos ABI especificados en el `subgraph.yaml`archivo. Este repositorio de muestra ya tiene el archivo, pero generalmente se obtiene después de compilar el contrato. Consulte el [Moonlotto repo](https://github.com/PureStake/moonlotto-subgraph) para obtener más información.

El `codegen` comando también se puede ejecutar usando `yarn codegen`.

Para este ejemplo, el contrato se implementó en `0x44ddD2EC5BE2A7f3e4A465C21600bE8df644093f`. Puede encontrar más información sobre cómo implementar un contrato con Hardhat en nuestro [tutorial de integraciones](/integrations/hardhat/). Además, el archivo "README" en el [repositorio de Moonloto](https://github.com/PureStake/moonlotto-subgraph) tiene los pasos necesarios para compilar e implementar el contrato si es necesario.

### Estructura básica de los subgrafos

En términos generales, los Subgraphs definen los datos que The Graph indexará de la blockchain y la forma en que se almacenan. Los subgrafos tienden a tener algunos de los siguientes archivos:

 - **subgraph.yaml** — es un archivo YAML que contiene el [manifiesto del Subgraph](https://thegraph.com/docs/define-a-subgraph#the-subgraph-manifest),  es decir, información relacionada con los contratos inteligentes que indexa el Subgraph.
 - **schema.graphql** — es un archivo de [esquema GraphQL](https://thegraph.com/docs/define-a-subgraph#the-graphql-schema) que define el almacén de datos para el Subgraph que se está creando y su estructura. Está escrito usando el [esquema de definición de la interfaz GraphQL.](https://graphql.org/learn/schema/#type-language)
 - **AssemblyScript mappings** — código en TypeScript (luego compilado en [AssemblyScript](https://github.com/AssemblyScript/assemblyscript)) que se utiliza para traducir los datos de eventos del contrato a las entidades definidas en el esquema.

No hay un orden en particular a seguir al modificar los archivos para crear un subgrafo.

### Schema.graphql

Es importante delinear qué datos deben extraerse de los eventos del contrato antes de modificar el `schema.graphql`.Los esquemas deben definirse teniendo en cuenta los requisitos de la propia dApp. Para este ejemplo, aunque no hay una dApp asociada a la lotería, se definen cuatro entidades:

 - **Ronda** — se refiere a una ronda de lotería. Almacena un índice de la ronda, el premio otorgado, la marca de tiempo de cuando comenzó la ronda, la marca de tiempo de cuándo se sorteó el ganador e información sobre los boletos participantes, que se deriva de la `Ticket` entidad.
 - **Jugador** — se refiere a un jugador que ha participado en al menos una ronda. Almacena su dirección e información de todas sus entradas participantes, que se deriva de la `Ticket` entidad
 - **Boleto** — se refiere a un boleto para ingresar a una ronda de lotería. Almacena si el boleto fue regalado, la dirección del propietario, la ronda desde la que es válido el boleto y si fue un boleto ganador.

En resumen, the `schema.graphql` debería verse como el siguiente fragmento:

```
type Round @entity {
  id: ID!
  index: BigInt!
  prize: BigInt! 
  timestampInit: BigInt!
  timestampEnd: BigInt
  tickets: [Ticket!] @derivedFrom(field: "round")
}

type Player @entity {
  id: ID!
  address: Bytes!
  tickets: [Ticket!] @derivedFrom(field: "player")
}

type Ticket @entity {
  id: ID!
  isGifted: Boolean!
  player: Player!
  round: Round!
  isWinner: Boolean!
}
```

### Manifiesto de subgrafo

El `subgraph.yaml` archivo, o el manifiesto de Subgraph, contiene la información relacionada con el contrato inteligente que se indexa, incluidos los eventos que tienen los datos necesarios para mapear. Luego, los nodos Graph almacenan esos datos, lo que permite que las aplicaciones los consulten.

Algunos de los parámetros más importantes del `subgraph.yaml` archivo son:

 - **repositorio** — se refiere al repositorio de Github del subgrafo
 - **esquema / archivo** — se refiere a la ubicación del `schema.graphql` archivo
 - **dataSources/name** — se refiere al nombre del subgrafo
 - **red** — se refiere al nombre de la red. Este valor **debe** establecerse en  `mbase` para cualquier Subgraph que se implemente en Moonbase Alpha
 - **dataSources/source/address** — se refiere a la dirección del contrato de interés
 - **dataSources/source/abi** — se refiere al lugar donde se almacena la interfaz del contrato dentro de la `types` carpeta creada con el `codegen` comando
 - **dataSources/source/startBlock** — se refiere al bloque de inicio desde el cual comenzará la indexación. Idealmente, este valor debería estar cerca del bloque en el que se creó el contrato. Puede utilizar [Blockscout](https://moonbase-blockscout.testnet.moonbeam.network/) para obtener esta información proporcionando la dirección del contrato. Para este ejemplo, el contrato se creó en el bloque `{{ networks.moonbase.thegraph.block_number }}`
 - **dataSources/mapping/file** — se refiere a la ubicación del archivo de mapeo
 - **dataSources/mapping/entities** — se refiere a las definiciones de las entidades en el `schema.graphql` archivo
 - **dataSources/abis/name** — se refiere al lugar donde se almacena la interfaz del contrato dentro del `types/dataSources/name`
 - **dataSources/abis/file** — se refiere a la ubicación donde se `.json` almacena el archivo con el ABI del contrato
 - **dataSources/eventHandlers** — no es necesario definir ningún valor aquí, pero esta sección se refiere a todos los eventos que indexará The Graph
 - **dataSources/eventHandlers/event** —  se refiere a la estructura de un evento que se rastreará dentro del contrato. Debe proporcionar el nombre del evento y su tipo de variables
 - **dataSources/eventHandlers/handler** —  se refiere al nombre de la función dentro del `mapping.ts` archivo que maneja los datos del evento
 - 
En resumen,`subgraph.yaml` debería verse como el siguiente fragmento:

```
specVersion: 0.0.2
description: Moonbeam lottery subgraph tutorial
repository: https://github.com/PureStake/moonlotto-subgraph
schema:
  file: ./schema.graphql
dataSources:
  - kind: ethereum/contract
    name: MoonLotto
    network: mbase
    source:
      address: '{{ networks.moonbase.thegraph.lotto_contract }}'
      abi: MoonLotto
      startBlock: {{ networks.moonbase.thegraph.block_number }}
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.4
      language: wasm/assemblyscript
      file: ./src/mapping.ts
      entities:
        - Player
        - Round
        - Ticket
        - Winner
      abis:
        - name: MoonLotto
          file: ./artifacts/contracts/MoonLotto.sol/MoonLotto.json
      eventHandlers:
        - event: PlayerJoined(uint256,address,uint256,bool,uint256)
          handler: handlePlayerJoined
        - event: LotteryResult(uint256,address,uint256,bool,uint256,uint256)
          handler: handleLotteryResult
```

### Mapeos

Los archivos de asignaciones son los que transforman los datos de la blockchain en entidades definidas en el archivo de esquema. Cada controlador de eventos dentro del `subgraph.yaml` archivo debe tener una función posterior en el mapeo.

El archivo de mapeo utilizado para el ejemplo de Lotería se puede encontrar en [este enlace](https://github.com/PureStake/moonlotto-subgraph/blob/main/src/mapping.ts).

En general, la estrategia de cada función de controlador es cargar los datos del evento, verificar si ya existe una entrada, organizar los datos como se desee y guardar la entrada. Por ejemplo, la función de controlador para el `PlayerJoined` evento es la siguiente:

```
export function handlePlayerJoined(event: PlayerJoined): void {
  // ID for the round:
  // round number
  let roundId = event.params.round.toString();
  // try to load Round from a previous player
  let round = Round.load(roundId);
  // if round doesn't exists, it's the first player in the round -> create round
  if (round == null) {
    round = new Round(roundId);
    round.timestampInit = event.block.timestamp;
  }
  round.index = event.params.round;
  round.prize = event.params.prizeAmount;

  round.save();

  // ID for the player:
  // issuer address
  let playerId = event.params.player.toHex();
  // try to load Player from previous rounds
  let player = Player.load(playerId);
  // if player doesn't exists, create it
  if (player == null) {
    player = new Player(playerId);
  }
  player.address = event.params.player;

  player.save();

  // ID for the ticket (round - player_address - ticket_index_round):
  // "round_number" + "-" + "player_address" + "-" + "ticket_index_per_round"
  let nextTicketIndex = event.params.ticketIndex.toString();
  let ticketId = roundId + "-" + playerId + "-" + nextTicketIndex;

  let ticket = new Ticket(ticketId);
  ticket.round = roundId;
  ticket.player = playerId;
  ticket.isGifted = event.params.isGifted;
  ticket.isWinner = false;

  ticket.save();  
}
```

## Implementar un subgrafo

Si va a utilizar la API Graph (servicio alojado), debe:

 - Cree una cuenta de [Graph Explorer](https://thegraph.com/explorer/) necesitará una cuenta de Github
 - Vaya a su tablero y anote el token de acceso
 - Cree su subgrafo a través del botón "Agregar subgrafo" en el sitio Graph Explorer. Anote el nombre del subgrafo

!!! note
    Todos los pasos se pueden encontrar en [este enlace](https://thegraph.com/docs/deploy-a-subgraph).
 
Si usa un nodo de TheGraph local, puede crear su subgrafo ejecutando el siguiente código:

```
npx graph create <username>/<subgraphName> --node <graph-node>
```

Dónde:

 - **nombre de usuario** — se refiere al nombre de usuario relacionado con el subgrafo que se está creando
 - **subgraphName** — se refiere al nombre de Subgraph
 - **graph-node** — se refiere a la URL del servicio alojado que se utilizará. Normalmente, para un nodo de TheGraph local es `http://127.0.0.1:8020`

Una vez creado, puede implementar su Subgraph ejecutando el siguiente comando con los mismos parámetros que antes:

```
npx graph deploy <username>/<subgraphName> \
--ipfs <ipfs-url> \
--node <graph-node> \
--access-token <access-token>
```

Dónde:

 - **nombre de usuario** — se refiere al nombre de usuario utilizado al crear el subgrafo
 - **subraphName** — se refiere al nombre de subgrafo definido al crear el subgrafo
 - **ifps-url**  — hace referencia a la URL de IFPS. Si usa la API Graph, puede usar `https://api.thegraph.com/ipfs/`. Para su nodo de TheGraph local, el valor predeterminado `http://localhost:5001`
 - **graph-node** — se refiere a la URL del servicio alojado que se utilizará. Si usa la API Graph, puede usar `https://api.thegraph.com/deploy/`.  Para su nodo de TheGraph local, el valor predeterminado es `http://localhost:8020`
 - **token de acceso** —  se refiere al token de acceso para usar la API Graph. Si está utilizando un nodo de TheGraph local, este parámetro no es necesario

Los registros del comando anterior deberían tener un aspecto similar a:

![The Graph deployed](/images/thegraph/thegraph-images1.png)

Las DApps ahora pueden usar los puntos finales de Subgraph para recuperar los datos indexados por el protocolo The Graph.
