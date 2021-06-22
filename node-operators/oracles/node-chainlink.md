---
title: Nodo de Chainlink
description: Cómo configurar un nodo de Oracle Chainlink para Moonbeam Network para alimentar datos en cadena para ser utilizados por Smart Contracts.
---

# Ejecute un nodo de Oracle Chainlink en Moonbeam

![Chainlink Moonbeam Banner](/images/chainlink/chainlinknode-banner.png)

## Introducción

Como red abierta y sin permisos, cualquiera puede optar por operar un Oracle que proporcione datos a contratos inteligentes que se ejecutan en Moonbeam.

Este artículo proporciona una descripción general con respecto a la configuración de un Oracle Chainlink en Moonbase Alpha.

!!! note
    Los ejemplos proporcionados son solo para fines de demostración. Las contraseñas **DEBEN** administrarse de forma segura y nunca almacenarse en texto plano. Estos ejemplos asumen un entorno basado en Ubuntu 18.04, pero se incluyen llamadas para MacOs. Esta guía es solo para una configuración de desarrollo, no la use para un entorno de producción.

## Modelo de solicitud básico

Antes de profundizar en cómo empezar, es importante comprender los conceptos básicos del "modelo de solicitud básico".

--8<-- 'text/chainlink/chainlink-brm.md'

## Usuarios avanzados

Si está familiarizado con la ejecución de nodos de Chainlink Oracle, esta información lo ayudará a comenzar en Moonbase Alpha TestNet rápidamente:

 - Documentación de Chainlink, que se puede encontrar [aquí](https://docs.chain.link/docs/running-a-chainlink-node)
 - Punto final de Moonbase Alpha WSS: `wss://wss.testnet.moonbeam.network`
 - ID de cadena alfa de Moonbase: `1287`
 - Token LINK en Moonbase Alpha: `0xa36085F69e2889c224210F603D836748e7dC0088`
 - Obtén tokens Moonbase Alpha de [nuestro Faucet](/getting-started/testnet/faucet/)

## Empezando

Esta guía le explicará el proceso de configuración del nodo de Oracle, resumido como:

 - Configurar un nodo Chainlink conectado a Moonbase Alpha
 - Nodo de fondos
 - Implementar un contrato de Oracle
 - Crear un trabajo en el nodo Chainlink
 - Bond nodo y Oracle
 - Prueba usando un contrato de cliente

Los requisitos básicos son:

 - Docker para ejecutar contenedores de nodo Postgres DB y ChainLink. Para obtener más información sobre la instalación de Docker, visite [esta página](https://docs.docker.com/get-docker/)
 - Una cuenta con fondos. Puede crear uno con [Metamask](/integrations/wallets/metamask/), que se puede financiar a través de [nuestro Faucet](https://docs.moonbeam.network/getting-started/testnet/faucet/)
 - Acceda al Remix IDE en caso de que desee utilizarlo para implementar el contrato de Oracle. Puedes encontrar más información sobre Remix en Moonbeam [aquí](/integrations/remix/)

## Configuración de nodo

Primero, creemos un nuevo directorio para colocar todos los archivos necesarios. Por ejemplo:

```
mkdir -p ~/.chainlink-moonbeam //
cd ~/.chainlink-moonbeam
```

A continuación, creemos una base de datos de Postgres con Docker. Para hacerlo, ejecute el siguiente comando (los usuarios de MacOs pueden reemplazarlo `--network host \` por `-p 5432:5432`):

```
docker run -d --name chainlink_postgres_db \
    --volume chainlink_postgres_data:/var/lib/postgresql/data \
    -e 'POSTGRES_PASSWORD={YOU_PASSWORD_HERE}' \
    -e 'POSTGRES_USER=chainlink' \
    --network host \
    -t postgres:11
```

Asegúrese de reemplazarlo  `{YOU_PASSWORD_HERE}` con una contraseña real.

!!! note
    Recordatorio, no almacene ninguna contraseña de producción en un archivo de texto sin formato. Los ejemplos proporcionados son solo para fines de demostración.

Docker procederá a descargar las imágenes necesarias si no se encuentran disponibles. Ahora, necesitamos crear un archivo de entorno para Chainlink en el directorio recién creado. Este archivo se lee en la creación del contenedor Chainlink. Los usuarios de MacOs pueden reemplazar `localhost` por `host.docker.internal`.

```
echo "ROOT=/chainlink
LOG_LEVEL=debug
ETH_CHAIN_ID=1287
MIN_OUTGOING_CONFIRMATIONS=2
LINK_CONTRACT_ADDRESS={LINK TOKEN CONTRACT ADDRESS}
CHAINLINK_TLS_PORT=0
SECURE_COOKIES=false
GAS_UPDATER_ENABLED=false
ALLOW_ORIGINS=*
ETH_URL=wss://wss.testnet.moonbeam.network
DATABASE_URL=postgresql://chainlink:{YOUR_PASSWORD_HERE}@localhost:5432/chainlink?sslmode=disable
MINIMUM_CONTRACT_PAYMENT=0" > ~/.chainlink-moonbeam/.env
```

Aquí, además de la contraseña (`{YOUR_PASSWORD_HERE}`), debemos proporcionar el contrato del token de enlace (`{LINK TOKEN CONTRACT ADDRESS}`). Una vez que hemos creado el archivo de entorno, también necesitamos un `.api` archivo que almacene el usuario y la contraseña utilizados para acceder a la API del nodo, la IU del operador del nodo y la línea de comando Chainlink.

```
echo "{AN_EMAIL_ADDRESS}" >  ~/.chainlink-moonbeam/.api
echo "{ANOTHER_PASSWORD}"   >> ~/.chainlink-moonbeam/.api
```

Establezca una dirección de correo electrónico y otra contraseña. Por último, necesitamos otro archivo que almacene la contraseña de la billetera para la dirección del nodo:

```
echo "{THIRD_PASSWORD}" > ~/.chainlink-moonbeam/.password
```

Ahora que hemos terminado de crear todos los archivos necesarios, podemos lanzar los contenedores con el siguiente comando (los usuarios de MacOs pueden reemplazar `--network host \` por `-p 6688:6688`):

```
docker run -d --name chainlink_oracle_node \
    --volume $(pwd):/chainlink \
    --env-file=.env \
    --network host \
    -t smartcontract/chainlink:0.9.2 \
        local n \
        -p /chainlink/.password \
        -a /chainlink/.api
```

Para verificar que todo se está ejecutando y que los registros están progresando, use:

```
docker ps #Containers Running
docker logs --tail 50 {container_id} #Logs progressing
```

![Docker logs](/images/chainlink/chainlinknode-image1.png)

## Configuración de contrato

Con el nodo de Oracle en ejecución, configuremos el lado del contrato inteligente de las cosas.

Primero, necesitamos recuperar la dirección que el nodo de Oracle usará para enviar transacciones y escribir datos en cadena. Para recuperar la dirección, inicie sesión en la [IU del nodo ChainLink](http://localhost:6688/) (ubicada en `http://localhost:6688/`) usando las credenciales del `.api` archivo.

![Chainlink login](/images/chainlink/chainlinknode-image2.png)

Vaya a la 'Página de configuración' y copie la dirección del nodo. Utilice el [Moonbeam Faucet](https://docs.moonbeam.network/getting-started/testnet/faucet/)  para financiarlo.

![Chainlink address](/images/chainlink/chainlinknode-image3.png)

A continuación, necesitamos implementar el contrato de Oráculo, que es el middleware entre la cadena y el nodo. El contrato emite un evento con toda la información necesaria, que es leído por el nodo de Oráculo. Luego, el nodo cumple la solicitud y escribe los datos solicitados en el contrato de la persona que llama.

El código fuente del contrato de Oracle se puede encontrar en el repositorio oficial de GitHub de Chainlink [aquí](https://github.com/smartcontractkit/chainlink/tree/develop/evm-contracts/src/v0.6). Para este ejemplo, usaremos Remix para interactuar con Moonbase Alpha e implementar el contrato. En Remix, podemos copiar el siguiente código:

```
pragma solidity ^0.6.6;

import "https://github.com/smartcontractkit/chainlink/evm-contracts/src/v0.6/Oracle.sol";
```

Después de compilar el contrato, diríjase a la pestaña "Implementar y ejecutar transacciones", ingrese la dirección del token de enlace e implemente el contrato. Una vez desplegado, copie la dirección del contrato.

![Deploy Oracle using Remix](/images/chainlink/chainlinknode-image4.png)

Por último, tenemos que vincular el nodo de Oracle y el contrato inteligente de Oracle. Un nodo puede escuchar las solicitudes enviadas a un determinado contrato de Oracle, pero solo los nodos autorizados (también conocidos como vinculados) pueden cumplir la solicitud con un resultado.

Para configurar esta autorización, podemos utilizar la función `setFulfillmentPermission()` del contrato de Oráculo. Esto necesita dos parámetros:

 - La dirección del nodo que queremos vincular al contrato (lo que hicimos en un paso anterior)
 - Un booleano que indica el estado del enlace. En este caso, lo configuramos en `true`

Podemos usar la instancia del contrato implementada en Remix para hacerlo, y verificar que el nodo de Oracle esté autorizado con la función de visualización `getAuthorizationStatus()`, pasando la dirección del nodo de Oracle.

![Authorize Chainlink Oracle Node](/images/chainlink/chainlinknode-image5.png)

## Crear trabajo en el nodo de Oracle

El último paso para tener un Chainlink Oracle completamente configurado es crear un trabajo. Consultando [la documentación oficial de Chainlink:](https://docs.chain.link/docs/job-specifications):

> Las especificaciones de un trabajo, o especificaciones, contienen las tareas secuenciales que el nodo debe realizar para producir un resultado final. Una especificación contiene al menos un iniciador y una tarea, que se analizan en detalle a continuación. Las especificaciones se definen utilizando JSON estándar para que sean legibles por humanos y puedan ser analizadas fácilmente por el nodo Chainlink.

Al ver un Oracle como un servicio de API, un Job aquí sería una de las funciones a las que podemos llamar y que devolverá un resultado. Para crear nuestro primer trabajo, vaya a las [secciones de trabajos de su nodo](http://localhost:6688/jobs)  y haga clic en "Nuevo trabajo".

![Chainlink Oracle New Job](/images/chainlink/chainlinknode-image6.png)

A continuación, pegue el siguiente JSON. Esto creará un trabajo que solicitará el precio ETH actual en USD. Asegúrese de ingresar su dirección de contrato de Oráculo (`YOUR_ORACLE_CONTRACT_ADDRESS`).

```
{
  "initiators": [
    {
      "type": "runlog",
      "params": { "address": "YOUR_ORACLE_CONTRACT_ADDRESS" }
    }
  ],
  "tasks": [
    {
      "type": "httpget",
      "params": { "get": "https://min-api.cryptocompare.com/data/price?fsym=ETH&tsyms=USD" }
    },
    {
      "type": "jsonparse",
      "params": { "path": [ "USD" ] }
    },
    {
      "type": "multiply",
      "params": { "times": 100 }
    },
    { "type": "ethuint256" },
    { "type": "ethtx" }
  ]
}
```

![Chainlink New Job JSON Blob](/images/chainlink/chainlinknode-image7.png)

¡Y eso es todo! Ha configurado completamente un nodo de Chainlink Oracle que se ejecuta en Moonbase Alpha.

## Prueba el Oracle

Para verificar que Oracle esté funcionando y respondiendo solicitudes, siga nuestro tutorial de [uso de Oracle](/integrations/oracles/chainlink/). La idea principal es implementar un contrato de cliente que solicita a Oracle, y Oracle escribe los datos solicitados en el almacenamiento del contrato.

