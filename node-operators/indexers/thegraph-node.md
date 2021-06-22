---
title: Nodo de TheGraph
description: Cree API utilizando el protocolo de indexación Graph en Moonbeam
---

# Ejecución de un nodo de gráfico en Moonbeam

![The Graph Node on Moonbeam](/images/thegraph/thegraphnode-banner.png)

## Introducción

Un nodo de TheGraph obtiene eventos de una blockchain para actualizar de manera determinista un almacén de datos que luego se puede consultar a través de un punto final GraphQL.

Hay dos formas de configurar un nodo de TheGraph: puede usar Docker para ejecutar una imagen todo en uno o puede ejecutar su [implementación de Rust](https://github.com/graphprotocol/graph-node). Los pasos descritos en esta guía solo cubrirán la alternativa de Docker, ya que es más conveniente y puede configurar un nodo de TheGraph muy rápidamente.

!!! note
    Los pasos descritos en esta guía se han probado en entornos MacOs y basados ​​en Ubuntu 18.04, y deberán adaptarse en consecuencia para otros sistemas.

## Comprobación de requisitos previos

Antes de sumergirse en la configuración de un nodo de TheGraph, debe tener lo siguiente instalado en su sistema:

 - [Docker](https://docs.docker.com/get-docker/)
 - [Docker Compose](https://docs.docker.com/compose/install/)
 - [JQ](https://stedolan.github.io/jq/download/)

Además, debe tener un nodo en ejecución con la `--ethapi=trace` opción habilitada. Actualmente, puede activar dos tipos diferentes de nodos:

 - **Nodo de desarrollo Moonbeam** — ejecute su propia instancia Moonbeam en su entorno privado. Para hacerlo, puede seguir [esta guía](/getting-started/local-node/setting-up-a-node/). Asegúrese de consultar la [sección de banderas avanzadas](/getting-started/local-node/setting-up-a-node/#advanced-flags-and-options)
 - **Nodo Moonbase Alpha** — ejecute un nodo completo de TestNet y acceda a sus propios puntos finales privados. Para hacerlo, puede seguir [esta guía](/node-operators/networks/full-node/). Asegúrese de consultar la [sección de banderas avanzadas](/node-operators/networks/full-node/#advanced-flags-and-options)

En esta guía, un nodo de TheGraph se ejecuta contra un nodo completo Moonbase Alpha con la `--ethapi=trace` bandera.

## Ejecución de un nodo de gráfico

El primer paso es clonar el [repositorio Graph Node](https://github.com/graphprotocol/graph-node/):

```
git clone https://github.com/graphprotocol/graph-node/ \
&& cd graph-node/docker
```

A continuación, ejecute el `setup.sh` archivo. Esto extraerá todas las imágenes de Docker necesarias y escribirá la información necesaria en el `docker-compose.yml` archivo.

```
./setup.sh
```

El final de los registros del comando anterior debería tener un aspecto similar a:

![Graph Node setup](/images/thegraph/thegraphnode-images1.png)

Una vez que todo está configurado, debe modificar el "entorno Ethereum" dentro del `docker-compose.yml` archivo, de modo que apunte al punto final del nodo contra el que está ejecutando este nodo de TheGraph. Tenga en cuenta que el `setup.sh` archivo detecta `Host IP` y escribe su valor, por lo que deberá modificarlo en consecuencia.

```
ethereum: 'mbase:http://localhost:9933'
```

Todo el `docker-compose.yml` archivo debería tener un aspecto similar a:

```
version: '3'
services:
  graph-node:
    image: graphprotocol/graph-node
    ports:
      - '8000:8000'
      - '8001:8001'
      - '8020:8020'
      - '8030:8030'
      - '8040:8040'
    depends_on:
      - ipfs
      - postgres
    environment:
      postgres_host: postgres
      postgres_user: graph-node
      postgres_pass: let-me-in
      postgres_db: graph-node
      ipfs: 'ipfs:5001'
      ethereum: 'mbase:http://localhost:9933'
      RUST_LOG: info
  ipfs:
    image: ipfs/go-ipfs:v0.4.23
    ports:
      - '5001:5001'
    volumes:
      - ./data/ipfs:/data/ipfs
  postgres:
    image: postgres
    ports:
      - '5432:5432'
    command: ["postgres", "-cshared_preload_libraries=pg_stat_statements"]
    environment:
      POSTGRES_USER: graph-node
      POSTGRES_PASSWORD: let-me-in
      POSTGRES_DB: graph-node
    volumes:
      - ./data/postgres:/var/lib/postgresql/data
```

Por último, para ejecutar el nodo de TheGraph, simplemente ejecute el siguiente comando:

```
docker-compose up
```

![Graph Node compose up](/images/thegraph/thegraphnode-images2.png)

Después de un tiempo, debería ver los registros relacionados con la sincronización del nodo de TheGraph con el último bloque disponible en la red:

![Graph Node logs](/images/thegraph/thegraphnode-images3.png)

¡Y eso es todo! Tiene un nodo de TheGraph ejecutándose en Moonbase Alpha TestNet. No dude en adaptar este ejemplo también a un nodo de desarrollo Moonbeam.
