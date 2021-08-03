---
title: Suscripción a Eventos
description: Utilice la funcionalidad de publicación y suscripción similar a Ethereum para suscribirse a eventos específicos en la cadena compatible con Ethereum de Moonbeam.
---

# Suscríbete a eventos en Moonbase Alpha

## Introducción {: #introduction } 
La capacidad de suscribirse a eventos de estilo Ethereum se agregó con el [lanzamiento de Moonbase Alpha v2](https://moonbeam.network/announcements/testnet-upgrade-moonbase-alpha-v2/). En esta guía, describiremos los tipos de suscripción disponibles y las limitaciones actuales.

## Comprobación de requisitos previos {: #checking-prerequisites } 
Los ejemplos de esta guía se basan en un entorno Ubuntu 18.04. También necesitará lo siguiente:

 - Tener MetaMask instalado y [conectado a Moonbase](/getting-started/testnet/metamask/)
 - Tener una cuenta con fondos. Puede obtener esto de [Mission Control](/getting-started/testnet/faucet/)
 - Implementa tu propio token ERC-20 en Moonbase. Puede hacerlo siguiendo [nuestro tutorial de Remix](/getting-started/local-node/using-remix/), mientras primero apunta MetaMask a Moonbase

--8<-- 'text/common/install-nodejs.md'

En el momento de redactar esta guía, las versiones utilizadas fueron 14.6.0 y 6.14.6, respectivamente. También necesitaremos instalar el paquete Web3 ejecutando:

```
npm install --save web3
```

Para verificar la versión instalada de Web3, puede usar el `ls` comando:

```
npm ls web3
```

En el momento de redactar esta guía, la versión utilizada era 1.3.0. 

## Suscripción a registros de eventos en Moonbase Alpha {: #subscribing-to-event-logs-in-moonbase-alpha } 
Cualquier contrato que sigue el estándar token de ERC-20 emite un evento relacionado con una transferencia de fichas, es decir, `event Transfer(address indexed from, address indexed to, uint256 value)`. Para este ejemplo, nos suscribiremos a los registros de dichos eventos. Usando la biblioteca web3.js, necesitamos el siguiente código:

```js
const Web3 = require('web3');
const web3 = new Web3('wss://wss.testnet.moonbeam.network');

web3.eth.subscribe('logs', {
    address: 'ContractAddress',
    topics: ['0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef']
}, (error, result) => {
    if (error)
        console.error(error);
})
    .on("connected", function (subscriptionId) {
        console.log(subscriptionId);
    })
    .on("data", function (log) {
        console.log(log);
    });
```

Tenga en cuenta que nos estamos conectando al punto final de WebSocket de Moonbase Alpha. Usamos el `web3.eth.subscribe(‘logs’,  options [, callback])` método para suscribirnos a los registros, filtrados por las opciones dadas. En nuestro caso, las opciones son la dirección del contrato desde donde se emiten los eventos y los temas utilizados para describir el evento. Puede encontrar más información sobre temas en [esta publicación de Medium](https://medium.com/mycrypto/understanding-event-logs-on-the-ethereum-blockchain-f4ae7ba50378). Si no se incluyen temas, se suscribe a todos los eventos emitidos por el contrato. Para filtrar solo el evento Transferir, necesitamos incluir la firma del evento, calculada como:

```js
EventSignature = keccak256(Transfer(address,address,uint256))
```

El resultado del cálculo se muestra en el fragmento de código anterior. Volveremos a filtrar por temas más adelante. El resto del código maneja la función de devolución de llamada. Una vez que ejecutemos este código, obtendremos un ID de suscripción y el terminal esperará cualquier evento a través de esa suscripción:

![Subscription ID](/images/testnet/testnet-pubsub1.png)

A continuación, se enviará una transferencia de token ERC-20 con los siguientes parámetros:

 - De la dirección: 0x44236223aB4291b93EEd10E4B511B37a398DEE55
 - A la dirección: 0x8841701Dba3639B254D9CEe712E49D188A1e941e
 - Valor (tokens): 1000000000000000000 - es decir, 1 con 18 ceros

Una vez enviemos la transacción, aparecerá en el terminal el log del evento emitido por la transacción:

![Log of the transfer event](/images/testnet/testnet-pubsub2.png)

Analicemos la respuesta recibida. Nuestro evento de destino envía dos piezas de información indexada: las direcciones `from` y `to` (en ese orden), que se tratan como temas. El otro dato compartido por nuestro evento es el número de tokens, que no está indexado. Por tanto, hay un total de tres temas (el máximo son cuatro), que corresponden al código de operación LOG3:

![Description of LOG3](/images/testnet/testnet-pubsub3.png)

En consecuencia, puede ver que las direcciones `from` y `to` están contenidas dentro de los temas devueltos por los registros. Las direcciones de Ethereum tienen 40 caracteres hexadecimales (1 carácter hexadecimal son 4 bits, por lo tanto, 160 bits o formato H160). Por lo tanto, se necesitan los 24 ceros adicionales para llenar el espacio hasta H256, que tiene 64 caracteres hexadecimales. 

Los datos no indexados se devuelven en el `data` campo de los registros, pero se codifican en bytes32 / hexadecimal. Para decodificarlo podemos usar, por ejemplo, esta [herramienta en línea](https://web3-type-converter.onbrn.com/),  y verificar que `data` en realidad es 1 (más 18 ceros).

Si el evento devuelve varios valores no indexados, se agregarán uno tras otro en el mismo orden en que los emite el evento. Por lo tanto, cada valor se obtiene al deconstruir los datos en partes separadas de 32 bytes (o 64 caracteres hexadecimales).

### Uso de comodines y formato condicional {: #using-wildcards-and-conditional-formatting } 

En la versión v2 que introdujo la función de suscripción a registros, hubo algunas limitaciones con respecto al uso de comodines y formato condicional para los temas. Sin embargo, con el lanzamiento de [Moonbase Alpha v3](https://www.purestake.com/news/moonbeam-network-upgrades-account-structure-to-match-ethereum/), esto ahora es posible.

Utilizando el mismo ejemplo que en la sección anterior, suscribamos los eventos del contrato del token con el siguiente código:

```js
const Web3 = require('web3');
const web3 = new Web3('wss://wss.testnet.moonbeam.network');

web3.eth
   .subscribe(
      'logs',
      {
         address: 'ContractAddress',
         topics: [
            null,
            [
               '0x00000000000000000000000044236223aB4291b93EEd10E4B511B37a398DEE55',
               '0x0000000000000000000000008841701Dba3639B254D9CEe712E49D188A1e941e',
            ],
         ],
      },
      (error, result) => {
         if (error) console.error(error);
      }
   )
   .on('connected', function (subscriptionId) {
      console.log(subscriptionId);
   })
   .on('data', function (log) {
      console.log(log);
   });
```

Aquí, al usar el comodín null en su lugar para la firma del evento, filtramos para escuchar todos los eventos emitidos por el contrato al que nos suscribimos. Pero con esta configuración, también podemos usar un segundo campo de entrada (`topic_1`) para definir un filtro por dirección como se mencionó anteriormente. En el caso de nuestra suscripción, estamos notificando que solo queremos recibir eventos donde `topic_1` esté una de las direcciones que proporcionamos. Tenga en cuenta que las direcciones deben estar en formato H256. Por ejemplo, la dirección `0x44236223aB4291b93EEd10E4B511B37a398DEE55` debe ingresarse como `0x00000000000000000000000044236223aB4291b93EEd10E4B511B37a398DEE55`. Como antes, la salida de esta suscripción mostrará la firma del evento `topic_0` para decirnos qué evento fue emitido por el contrato.

![Conditional Subscription](/images/testnet/testnet-pubsub7.png)

Como se muestra, después de proporcionar las dos direcciones con formato condicional, recibimos dos registros con el mismo ID de suscripción. Los eventos emitidos por transacciones de diferentes direcciones no arrojarán ningún registro a esta suscripción.

Este ejemplo mostró cómo podríamos suscribirnos solo a los registros de eventos de un contrato específico, pero la biblioteca web3.js proporciona otros tipos de suscripción que veremos en las siguientes secciones.

## Suscribirse a transacciones pendientes entrantes {: #subscribe-to-incoming-pending-transactions } 
Para suscribirnos a transacciones pendientes, podemos usar el `web3.eth.subscribe(‘pendingTransactions’, [, callback])` mmétodo, implementando la misma función de devolución de llamada para verificar la respuesta. Esto es mucho más simple que nuestro ejemplo anterior y devuelve el hash de transacción de las transacciones pendientes.

![Subscribe pending transactions response](/images/testnet/testnet-pubsub4.png)

Podemos verificar que este hash de transacción es el mismo que se muestra en MetaMask (o Remix).

## Suscribirse a los encabezados de bloque entrantes {: #subscribe-to-incoming-block-headers } 
Otro tipo disponible en la biblioteca Web3.js es suscribirse a nuevos encabezados de bloque. Para hacerlo, usamos el `web3.eth.subscribe('newBlockHeaders' [, callback])` método, implementando la misma función de devolución de llamada para verificar la respuesta. Esta suscripción proporciona encabezados de bloque entrantes y se puede usar para rastrear cambios en la blockchain.

![Subscribe to block headers response](/images/testnet/testnet-pubsub5.png)

Tenga en cuenta que solo se muestra un encabezado de bloque en la imagen. Estos mensajes se muestran para cada bloque producido para que puedan llenar la terminal bastante rápido.

## Compruebe si un nodo está sincronizado con la red {: #check-if-a-node-is-synchronized-with-the-network } 
Con pub / sub también es posible verificar si un nodo en particular al que está suscrito está actualmente sincronizado con la red. Para eso, podemos aprovechar el `web3.eth.subscribe(‘syncing' [, callback])` método, implementando la misma función de devolución de llamada para verificar la respuesta. Esta suscripción devolverá un objeto cuando el nodo esté sincronizado con la red.

![Subscribe to syncing response](/images/testnet/testnet-pubsub6.png)

## Limitaciones actuales {: #current-limitations } 

La implementación de pub / sub en [Frontier](https://github.com/paritytech/frontier) aún está en desarrollo activo. Esta primera versión permite a los desarrolladores de DApp (o usuarios en general) suscribirse a tipos de eventos específicos, pero aún existen algunas limitaciones. Es posible que haya notado en ejemplos anteriores que algunos de los campos no muestran la información adecuada con la versión actual publicada, y eso se debe a que Frontier aún no admite ciertas propiedades.


