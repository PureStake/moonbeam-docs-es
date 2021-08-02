---
title: Configurar un Nodo
description: Siga este tutorial para aprender cómo configurar su primer nodo Moonbeam. También aprenderá cómo conectarlo y controlarlo con la GUI de Polkadot JS.
---

# Configuración de un nodo Moonbeam y conexión a la GUI de Polkadot JS

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//p_0OAHSlHNM' frameborder='0' allowfullscreen></iframe></div>
<style>.caption { font-family: Open Sans, sans-serif; font-size: 0.9em; color: rgba(170, 170, 170, 1); font-style: italic; letter-spacing: 0px; position: relative;}</style><div class='caption'>Puede encontrar todo el código relevante para este tutorial en la <a href="{{ config.site_url }}resources/code-snippets/">página de fragmentos de código</a></div>

## Introducción {: #introduction } 

Esta guía describe los pasos necesarios para crear un nodo de desarrollo para probar las características de compatibilidad de Ethereum de Moonbeam.

!!! nota
    Este tutorial fue creado usando la etiqueta tutorial-v7 de [Moonbase Alpha](https://github.com/PureStake/moonbeam/releases/tag/tutorial-v7). La plataforma Moonbeam y los componentes de [Frontier en los](https://github.com/paritytech/frontier) que se basa para la compatibilidad con Ethereum basada en sustratos aún se encuentran en un desarrollo muy activo. Los ejemplos de esta guía asumen que tiene un entorno basado en MacOS o Ubuntu 18.04 y deberá adaptarse en consecuencia para Windows.
    
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

Un nodo de desarrollo Moonbeam es su propio entorno de desarrollo personal para crear y probar aplicaciones en Moonbeam. Para los desarrolladores de Ethereum, es comparable a Ganache. Le permite comenzar rápida y fácilmente sin la sobrecarga de una cadena de relés. Puede activar su nodo con la `--sealing` opción de crear bloques de forma instantánea, manual o en un intervalo personalizado después de recibir las transacciones. De forma predeterminada, se creará un bloqueo cuando se reciba una transacción, que es similar a la función instamine de Ganache.

Si sigue hasta el final de esta guía, tendrá un nodo de desarrollo Moonbeam ejecutándose en su entorno local, con 10 [cuentas prefinanciadas](#cuentas-de-desarrollo-prefinanciadas),  y podrá conectarlo a la GUI predeterminada de Polkadot JS.

Hay dos formas de comenzar a ejecutar un nodo Moonbeam: puede usar [Docker para ejecutar un binario prediseñado](#introduccion-a-docker) o puede [compilar el binario localmente](#introduccion-al-archivo-binario) y configurar un nodo de desarrollo usted mismo. El uso de Docker es una forma rápida y conveniente de comenzar, ya que no tendrá que instalar Substrate y todas las dependencias, y también puede omitir el proceso de construcción del nodo. Requiere que [instales Docker](https://docs.docker.com/get-docker/). Por otro lado, si decide que desea pasar por el proceso de creación de su propio nodo de desarrollo, podría tardar aproximadamente 30 minutos o más en completarse, según su hardware.

## Introducción a Docker {: #getting-started-with-docker } 

El uso de Docker le permite activar un nodo en cuestión de segundos. Una vez que tenga Docker instalado, puede ejecutar el siguiente comando para descargar la imagen correspondiente:

```
docker pull purestake/moonbeam:{{ networks.development.build_tag }}
```

El final del registro de la consola debería verse así:

![Docker - imaged pulled](/images/setting-up-a-node/setting-up-node-1.png)

Una vez que se descarga la imagen de Docker, el siguiente paso es ejecutar la imagen.

Puede ejecutar la imagen de Docker con lo siguiente:

=== "Ubuntu"
    ```
    docker run --rm --name {{ networks.development.container_name }} --network host \
    purestake/moonbeam:{{ networks.development.build_tag }} \
    --dev
    ```

=== "MacOS"
    ```
    docker run --rm --name {{ networks.development.container_name }} -p 9944:9944 -p 9933:9933 \
    purestake/moonbeam:{{ networks.development.build_tag }} \
    --dev --ws-external --rpc-external
    ```

=== "Windows"
    ```
    docker run --rm --name {{ networks.development.container_name }} -p 9944:9944 -p 9933:9933 \
    purestake/moonbeam:{{ networks.development.build_tag }} \
    --dev --ws-external --rpc-external
    ```

Esto debería activar un nodo de desarrollo Moonbeam en modo de sello instantáneo para pruebas locales, de modo que los bloques se creen instantáneamente a medida que se reciben las transacciones. Si tiene éxito, debería ver una salida que muestra un estado inactivo esperando a que se creen los bloques:

![Docker - output shows blocks being produced](/images/setting-up-a-node/setting-up-node-2.png)

Para obtener más información sobre algunas de las banderas y opciones utilizadas en el ejemplo, consulte [Opciones y flags comunes](#flags-y-opciones-comunes). Si desea ver una lista completa de todos los indicadores, opciones y subcomandos, abra el menú de ayuda ejecutando:

```
docker run --rm --name {{ networks.development.container_name }} \
purestake/moonbeam \
--help
```

Para continuar con el tutorial, la siguiente sección no es necesaria ya que ya ha activado un nodo con Docker. Puede pasar a [Conectar aplicaciones JS de Polkadot a un nodo Moonbeam local](#conectando-polkadotjs-apps-a-un-nodo-moonbeam-local).

## Introducción al archivo binario {: #getting-started-with-the-binary-file } 

!!! nota
    Si sabe lo que está haciendo, puede descargar directamente los archivos binarios precompilados adjuntos a cada versión en la [página de versiones de Moonbeam](https://github.com/PureStake/moonbeam/releases). Estos no funcionarán en todos los sistemas. Por ejemplo, los binarios solo funcionan con Linux x86-64 con versiones específicas de dependencias. La forma más segura de garantizar la compatibilidad es compilar el binario en el sistema desde donde se ejecutará.

Primero, comience clonando una etiqueta específica del repositorio Moonbeam que puede encontrar aquí:

[https://github.com/PureStake/moonbeam/](https://github.com/PureStake/moonbeam/)

```
git clone -b {{ networks.development.build_tag }} https://github.com/PureStake/moonbeam
cd moonbeam
```

A continuación, instale Substrate y todos sus requisitos previos (incluido Rust) ejecutando:

```
--8<-- 'code/setting-up-node/substrate.md'
```

Una vez que haya seguido todos los procedimientos anteriores, es hora de construir el nodo de desarrollo ejecutando:

```
--8<-- 'code/setting-up-node/build.md'
```

Si aparece un _error de carga no encontrada_ en la terminal, agregue manualmente Rust a la ruta de su sistema (o reinicie su sistema):

```
--8<-- 'code/setting-up-node/cargoerror.md'
```

!!! nota
    La construcción inicial llevará un tiempo. Dependiendo de su hardware, debe esperar aproximadamente 30 minutos para que finalice el proceso de compilación.

Así es como debería verse el final de la salida de la compilación:

![End of build output](/images/setting-up-a-node/setting-up-node-3.png)

Luego, querrá ejecutar el nodo en modo dev usando el siguiente comando:

```
--8<-- 'code/setting-up-node/runnode.md'
```

!!! nota
    Para las personas que no están familiarizadas con Substrate, la `--dev` marca es una forma de ejecutar un nodo basado en Substrate en una configuración de desarrollador de un solo nodo con fines de prueba. Puede obtener más información `--dev` al respecto en [este tutorial de sustrato](https://substrate.dev/docs/en/tutorials/create-your-first-substrate-chain/interact).

Debería ver una salida similar a la siguiente, que muestra un estado inactivo a la espera de que se produzcan bloques:

![Output shows blocks being produced](/images/setting-up-a-node/setting-up-node-4.png)

Para obtener más información sobre algunas de las banderas y opciones utilizadas en el ejemplo, consulte [Opciones y banderas comunes](#flags-y-opciones-comunes). Si desea ver una lista completa de todos los indicadores, opciones y subcomandos, abra el menú de ayuda ejecutando:

```
./target/release/moonbeam --help
```
## Conectando PolkadotJS Apps a un nodo Moonbeam local {: #connecting-polkadot-js-apps-to-a-local-moonbeam-node } 

El nodo de desarrollo es un nodo basado en sustrato, por lo que puede interactuar con él utilizando herramientas de sustrato estándar. Los dos puntos finales de RPC proporcionados son:

 - HTTP: `http://127.0.0.1:9933`
 - WS: `ws://127.0.0.1:9944` 

Comience conectándose a él con Polkadot JS Apps. Abra un navegador para: [https://polkadot.js.org/apps/#/explorer](https://polkadot.js.org/apps/#/explorer). Esto abrirá Polkadot JS Apps, que se conecta automáticamente a Polkadot MainNet.

![Polkadot JS Apps](/images/setting-up-a-node/setting-up-node-5.png)

Haga clic en la esquina superior izquierda para abrir el menú para configurar las redes y luego navegue hacia abajo para abrir el submenú Desarrollo. Allí, querrá alternar la opción "Nodo local", que apunta a Polkadot JS Apps `ws://127.0.0.1:9944`. A continuación, seleccione el botón Cambiar y el sitio debería conectarse a su nodo de desarrollo Moonbeam.

![Select Local Node](/images/setting-up-a-node/setting-up-node-6.png)

Con Polkadot JS Apps conectadas, verá el nodo de desarrollo Moonbeam esperando que lleguen las transacciones para comenzar a producir bloques.

![Select Local Node](/images/setting-up-a-node/setting-up-node-7.png)

## Consultando el estado de la cuenta {: #querying-account-state } 

Con el lanzamiento de [Moonbase Alpha v3](https://www.purestake.com/news/moonbeam-network-upgrades-account-structure-to-match-ethereum/), Moonbeam ahora funciona bajo un formato de cuenta única, que es el H160 estilo Ethereum y ahora también es compatible con Polkadot JS Apps. Para verificar el saldo de una dirección, simplemente puede importar su cuenta en la pestaña Cuentas. Puede encontrar más información en la sección [Cuentas unificadas](/learn/unified-accounts/).
 
Sin embargo, aprovechando las capacidades RPC completas de Ethereum de Moonbeam, también puede usar [MetaMask](/getting-started/local-node/using-metamask/) para verificar el saldo de esa dirección. Además, también puede utilizar otras herramientas de desarrollo, como [Remix](/getting-started/local-node/using-remix/) y [Truffle](/getting-started/local-node/using-truffle/).

## Flags y opciones comunes {: #common-commands-flags-and-options } 

### Purgando la Cadena {: #purging-the-chain } 

Cuando se ejecuta un nodo a través del archivo binario, los datos se almacenan en un directorio local que normalmente se encuentra en `~/.local/shared/moonbeam/chains/development/db`. Si desea iniciar una nueva instancia del nodo, puede eliminar el contenido de la carpeta o ejecutar el siguiente comando dentro de la `moonbeam` carpeta:

```
./target/release/moonbeam purge-chain --dev -y
```

Esto eliminará la carpeta de datos, tenga en cuenta que todos los datos de la cadena ahora se pierden.

Si usó Docker, la carpeta de datos está relacionada con el contenedor de Docker.

### Banderas de nodo {: #node-flags } 

Las banderas no toman un argumento. Para usar una bandera, agréguela al final de un comando. Por ejemplo:

```
--8<-- 'code/setting-up-node/runnode.md'
```

- `--dev`: Especifica la cadena de desarrollo
- `--no-telemetry`: Desactiva la conexión al servidor de telemetría de sustrato. Para las cadenas globales, la telemetría está activada de forma predeterminada. La telemetría no está disponible si está ejecutando un (`--dev`) nodo de desarrollo ( ).
- `--tmp`: Ejecuta un nodo temporal en el que toda la configuración se eliminará al final del proceso.
- `--rpc-external`: Escuche todas las interfaces RPC
- `--ws-external`: Escuche todas las interfaces de Websocket

### Opciones de nodo {: #node-options } 

Las opciones aceptan un argumento al lado derecho de la opción. Por ejemplo:

```
--8<-- 'code/setting-up-node/runnodewithsealinginterval.md'
```

- `-l <log pattern>` or `--log <log pattern>`: establece un filtro de registro personalizado. La sintaxis del patrón de registro es `<target>=<level>`. Por ejemplo, para imprimir todos los registros de RPC, el comando sería el siguiente: `-l rpc=trace`.
- `--sealing <interval>`: Cuando los bloques deben sellarse en el servicio de desarrollo. Argumentos aceptados para el intervalo: `instant`, `manual`, o un número que representa el intervalo del temporizador en milisegundos (por ejemplo, `6000` tendrán el nodo producir bloques de cada 6 segundos). El valor predeterminado es `instant`.
- `--rpc-port <port>`: Establece el puerto TCP del servidor HTTP RPC. Acepta un puerto como argumento.
- `--ws-port <port>`: Establece el puerto TCP del servidor RPC de WebSockets. Acepta un puerto como argumento.

Para obtener una lista completa de banderas y opciones, active su nodo de desarrollo Moonbeam con `--help` agregado al final del comando.

## Opciones y banderas avanzadas {: #advanced-flags-and-options } 

--8<-- 'text/setting-up-node/advanced-flags.md'

Por ejemplo, al ejecutar el binario:

```
./target/release/moonbeam --dev --execution=Native --ethapi=debug,trace
```

## Cuentas de desarrollo prefinanciadas {: #pre-funded-development-accounts } 

Su nodo de desarrollo Moonbeam viene con diez cuentas prefinanciadas para el desarrollo. Las direcciones se derivan del mnemónico de desarrollo canónico de Substrate:

```
bottom drive obey lake curtain smoke basket hold race lonely fit walk
```

--8<-- 'code/setting-up-node/dev-accounts.md'

Partida de los [MetaMask Usando](/getting-started/local-node/using-metamask/) sección para empezar a interactuar con sus cuentas.

Además, con el nodo de desarrollo se incluye una cuenta con financiación previa que se utiliza con fines de prueba:

--8<-- 'code/setting-up-node/dev-testing-account.md'
