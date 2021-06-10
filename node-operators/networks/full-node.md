---
title: Ejecutar un Nodo
description: Cómo ejecutar un nodo Parachain completo para Moonbeam Network para tener su RPC Endpoint o producir bloques
---

# Ejecutar un nodo en Moonbeam

![Full Node Moonbeam Banner](/images/fullnode/fullnode-banner.png)

## Introducción

Con el lanzamiento de Moonbase Alpha v6, puede activar un nodo que se conecta a Moonbase Alpha TestNet, se sincroniza con un bootnode, proporciona acceso local a sus puntos finales de RPC e incluso crea bloques en la parachain.

En nuestro TestNet, la cadena de retransmisión está alojada y gestionada por PureStake. Pero a medida que avanza el desarrollo, también habrá implementaciones en Kusama y luego en Polkadot. Así es como se nombrarán estos próximos entornos y el nombre de sus [archivos de especificación de cadena](https://substrate.dev/docs/en/knowledgebase/integrate/chain-spec) correspondientes:

|    La red     |     | Alojado por |     |   Nombre de cadena    |
| :------------: | :-: | :-------: | :-: | :-------------: |
| Moonbase Alpha |     | PureStake |     |    alphanet     |
|   Moonriver    |     |  Kusama   |     | _no disponible_ |
|    Moonbeam    |     | Polkadot  |     | _no disponible_ |

Esta guía está destinada a personas con experiencia en la ejecución de cadenas basadas en [Substrate](https://substrate.dev/). Ejecutar un parachain es similar a ejecutar un nodo Substrate con algunas diferencias. Un nodo de paracadena de sustrato ejecutará dos procesos: uno para sincronizar la cadena de relés y otro para sincronizar la paracadena. Como tal, muchas cosas se duplican, por ejemplo, el directorio de la base de datos, los puertos utilizados, las líneas de registro y más.

!!! nota
    Moonbase Alpha todavía se considera un Alphanet, y, como tal , no tendrá un tiempo de actividad del 100%. La paracadena se purgará de vez en cuando. Durante el desarrollo de su aplicación, asegúrese de implementar un método para volver a implementar sus contratos y cuentas en una nueva parachain rápidamente. Las purgas de cadenas se anunciarán a través de nuestro [canal de Discord con](https://discord.gg/PfpUATX) al menos 24 horas de anticipación.

## Requisitos

Las especificaciones mínimas recomendadas para ejecutar un nodo se muestran en la siguiente tabla. Para nuestras implementaciones de Kusama y Polkadot MainNet, los requisitos de disco serán mayores a medida que la red crezca.

|  Componente   |     | Requisito                                                                                                                |
| :----------: | :-: | :------------------------------------------------------------------------------------------------------------------------- |
|   **CPU**    |     | 8 núcleos (más rápido por velocidad de núcleo)                                                                      |
|   **RAM**    |     | 16 GB (fase de desarrollo inicial, aún no optimizado)                                                                        |
|   **SSD**    |     | 50 GB  (para empezar en nuestro TestNet)                                                                                            |
| **Cortafuegos** |     | El puerto P2P debe estar abierto al tráfico entrante:<br>&nbsp; &nbsp; -  Origen: Cualquiera<br>&nbsp; &nbsp; - Destino: 30333, 30334 TCP |

!!! nota
    Si no ve un `Imported` mensaje (sin la `[Relaychain]` etiqueta) cuando ejecuta un nodo, es posible que deba volver a verificar la configuración de su puerto.

## Puertos en ejecución

Como se indicó anteriormente, los nodos de retransmisión / paracadena escucharán en varios puertos. Los puertos de sustrato predeterminados se utilizan en la paracadena, mientras que la cadena de relés escuchará en el siguiente puerto superior.

Los únicos puertos que deben estar abiertos para el tráfico entrante son los designados para P2P.

### Puertos predeterminados para un nodo completo de Parachain

|  Descripción   |     |                Puerto                 |
| :------------: | :-: | :---------------------------------: |
|    **P2P**     |     | {{ networks.parachain.p2p }} (TCP)  |
|    **RPC**     |     |    {{ networks.parachain.rpc }}     |
|     **WS**     |     |     {{ networks.parachain.ws }}     |
| **Prometeo** |     | {{ networks.parachain.prometheus }} |

### Puertos predeterminados de la cadena de relés incorporada

|  Descripción   |     |                 Puerto                  |
| :------------: | :-: | :-----------------------------------: |
|    **P2P**     |     | {{ networks.relay_chain.p2p }} (TCP)  |
|    **RPC**     |     |    {{ networks.relay_chain.rpc }}     |
|     **WS**     |     |     {{ networks.relay_chain.ws }}     |
| **Prometeo** |     | {{ networks.relay_chain.prometheus }} |

## Instrucciones de instalación: Docker

Un nodo Moonbase Alpha se puede activar rápidamente usando Docker. Para obtener más información sobre la instalación de Docker, visite [esta página](https://docs.docker.com/get-docker/). En el momento de redactar este artículo, la versión de Docker utilizada era la 19.03.6.

Primero, cree un directorio local para almacenar los datos de la cadena:

```
mkdir {{ networks.moonbase.node_directory }}
```

A continuación, configure los permisos necesarios para un usuario específico o actual (reemplace `DOCKER_USER` por el usuario real que ejecutará el `docker` comando):

```
# chown to a specific user
chown DOCKER_USER {{ networks.moonbase.node_directory }}

# chown to current user
sudo chown -R $(id -u):$(id -g) {{ networks.moonbase.node_directory }}
```

!!! nota
    Asegúrese de establecer la propiedad y los permisos correspondientes para el directorio local que almacena los datos de la cadena.

Ahora, ejecute el comando docker run. Tenga en cuenta que tiene que:

 - Reemplazar `YOUR-NODE-NAME` en dos lugares diferentes.
 - Para clasificadores, reemplácelo `PUBLIC_KEY` la dirección pública que se asociará con las actividades de clasificación.

!!! nota
    Si está configurando un nodo de clasificación, asegúrese de seguir los fragmentos de código para "Clasificador".

### Nodo completo

=== "Ubuntu"
    ```
    docker run --network="host" -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_docker_tag }} \
    --rpc-cors all \
    --base-path=/data \
    --chain alphanet \
    --name="YOUR-NODE-NAME" \
    --execution wasm \
    --wasm-execution compiled \
    --in-peers 200 \
    --out-peers 200 \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

=== "MacOS"
    ```
    docker run -p {{ networks.parachain.rpc }}:{{ networks.parachain.rpc }} -p {{ networks.parachain.ws }}:{{ networks.parachain.ws }} -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_docker_tag }} \
    --ws-external \
    --rpc-external \
    --rpc-cors all \
    --base-path=/data \
    --chain alphanet \
    --name="YOUR-NODE-NAME" \
    --execution wasm \
    --wasm-execution compiled \
    --in-peers 200 \
    --out-peers 200 \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```
### Clasificador

=== "Ubuntu"
    ```
    docker run --network="host" -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_docker_tag }} \
    --base-path=/data \
    --chain alphanet \
    --name="YOUR-NODE-NAME" \
    --collator \
    --author-id PUBLIC_KEY \
    --execution wasm \
    --wasm-execution compiled \
    --in-peers 200 \
    --out-peers 200 \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

=== "MacOS"
    ```
    docker run -p {{ networks.parachain.rpc }}:{{ networks.parachain.rpc }} -p {{ networks.parachain.ws }}:{{ networks.parachain.ws }} -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_docker_tag }} \
    --ws-external \
    --rpc-external \
    --base-path=/data \
    --chain alphanet \
    --name="YOUR-NODE-NAME" \
    --collator \
    --author-id PUBLIC_KEY \
    --execution wasm \
    --wasm-execution compiled \
    --in-peers 200 \
    --out-peers 200 \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

Una vez que Docker extrae las imágenes necesarias, se iniciará el nodo completo de Moonbase Alpha, mostrando mucha información, como la especificación de la cadena, el nombre del nodo, el rol, el estado de génesis y más:

![Full Node Starting](/images/fullnode/fullnode-docker1.png)

!!! nota
    Si tiene problemas con la telemetría predeterminada, puede agregar la marca `--no-telemetry` para ejecutar el nodo completo sin la telemetría activada.

!!! nota
    Puede especificar un puerto Prometheus personalizado con la `--prometheus-port XXXX` bandera (reemplazando `XXXX` con el número de puerto real). Esto es posible tanto para la paracadena como para la cadena de relés integrada.

El comando anterior habilitará todos los puertos expuestos necesarios para el funcionamiento básico, incluidos los puertos P2P y Prometheus (telemetría). Este comando es compatible para usar con la telemetría del Watchdog del nodo de Gantree. Si desea exponer puertos específicos, habilítelos en la línea de comando de ejecución de Docker como se muestra a continuación. Sin embargo, hacerlo evitará que el contenedor Gantree Node Watchdog (telemetría) acceda al contenedor Moonbeam, así que no haga esto cuando ejecute un clasificador a menos que comprenda las [redes de la ventana acoplable.](https://docs.docker.com/network/).

```
docker run -p {{ networks.relay_chain.p2p }}:{{ networks.relay_chain.p2p }} -p {{ networks.parachain.p2p }}:{{ networks.parachain.p2p }} -p {{ networks.parachain.rpc }}:{{ networks.parachain.rpc }} -p {{ networks.parachain.ws }}:{{ networks.parachain.ws }} #rest of code goes here
```

Durante el proceso de sincronización, verá mensajes tanto de la cadena de relés incorporada como de la paracadena (sin etiqueta). Estos mensajes muestran un bloque de destino (TestNet) y un mejor bloque (estado sincronizado del nodo local).

![Full Node Starting](/images/fullnode/fullnode-docker2.png)

Una vez sincronizado, ¡tiene un nodo de Moonbase Alpha TestNet ejecutándose localmente!

## Instrucciones de instalación: binario

Esta sección pasa por el proceso de compilar el binario y ejecutar un nodo completo Moonbeam como un servicio systemd. Los siguientes pasos se probaron en una instalación de Ubuntu 18.04. Moonbase Alpha puede funcionar con otras versiones de Linux, pero Ubuntu es actualmente la única versión probada.

### Compilando el binario

Los siguientes comandos construirán la última versión de Moonbeam parachain.

Primero, comencemos clonando el repositorio moonbeam.

```
git clone https://github.com/PureStake/moonbeam
cd moonbeam
```

Echemos un vistazo a la última versión:

```
git checkout tags/$(git tag | tail -1)
```

A continuación, instale Substrate y todos sus requisitos previos, incluido Rust, ejecutando:

```
--8<-- 'code/setting-up-node/substrate.md'
```

Por último, compile el binario de parachain:

```
cargo build --release
```

![Compiling Binary](/images/fullnode/fullnode-binary1.png)

Si aparece un _error de carga no encontrada_ en la terminal, agregue manualmente Rust a la ruta de su sistema o reinicie su sistema:

```
--8<-- 'code/setting-up-node/cargoerror.md'
```

### Ejecución del servicio Systemd

Los siguientes comandos configurarán todo lo relacionado con la ejecución del servicio.

Primero, creemos una cuenta de servicio para ejecutar el servicio:

```
adduser moonbase_service --system --no-create-home
```

A continuación, cree un directorio para almacenar el binario y los datos y establezca los permisos necesarios:

```
mkdir {{ networks.moonbase.node_directory }}
chown moonbase_service {{ networks.moonbase.node_directory }}
```

!!! nota
   Asegúrese de establecer la propiedad y los permisos correspondientes para el directorio local que almacena los datos de la cadena.

Ahora, copie el binario construido en la última sección a la carpeta creada:

```
cp ./target/release/{{ networks.moonbase.binary_name }} {{ networks.moonbase.node_directory }}
```

El siguiente paso es crear el archivo de configuración systemd. Tenga en cuenta que tiene que:

 - Reemplazar `YOUR-NODE-NAME` en dos lugares diferentes
 - Verifique que el binario esté en la ruta correcta como se describe a continuación (_ExecStart_)
 - Vuelva a verificar la ruta base si ha utilizado un directorio diferente
 - Para clasificadores, reemplace `PUBLIC-KEY` la clave pública de su dirección Ethereum H160 creada anteriormente
 - Nombra el archivo `/etc/systemd/system/moonbeam.service`

!!! nota
    Si está configurando un nodo de clasificación, asegúrese de seguir los fragmentos de código para "Clasificador".

=== "Nodo completo"
    ```
    [Unit]
    Description="Moonbase Alpha systemd service"
    After=network.target
    StartLimitIntervalSec=0

    [Service]
    Type=simple
    Restart=on-failure
    RestartSec=10
    User=moonbase_service
    SyslogIdentifier=moonbase
    SyslogFacility=local7
    KillSignal=SIGHUP
    ExecStart={{ networks.moonbase.node_directory }}/{{ networks.moonbase.binary_name }} \
         --parachain-id 1000 \
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --unsafe-rpc-external \
         --unsafe-ws-external \
         --rpc-methods=Safe \
         --rpc-cors all \
         --log rpc=info \
         --base-path {{ networks.moonbase.node_directory }} \
         --chain alphanet \
         --name "YOUR-NODE-NAME" \
        --in-peers 200 \
        --out-peers 200 \
         -- \
         --port {{ networks.relay_chain.p2p }} \
         --rpc-port {{ networks.relay_chain.rpc }} \
         --ws-port {{ networks.relay_chain.ws }} \
         --pruning=archive \
         --name="YOUR-NODE-NAME (Embedded Relay)"

    [Install]
    WantedBy=multi-user.target
    ```

=== "Clasificador"
    ```
    [Unit]
    Description="Moonbase Alpha systemd service"
    After=network.target
    StartLimitIntervalSec=0

    [Service]
    Type=simple
    Restart=on-failure
    RestartSec=10
    User=moonbase_service
    SyslogIdentifier=moonbase
    SyslogFacility=local7
    KillSignal=SIGHUP
    ExecStart={{ networks.moonbase.node_directory }}/{{ networks.moonbase.binary_name }} \
         --parachain-id 1000 \
         --collator \
         --author-id PUBLIC_KEY \
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --unsafe-rpc-external \
         --unsafe-ws-external \
         --rpc-methods=Safe \
         --log rpc=info \
         --base-path {{ networks.moonbase.node_directory }} \
         --chain alphanet \
         --name "YOUR-NODE-NAME" \
         --in-peers 200 \
         --out-peers 200 \
         -- \
         --port {{ networks.relay_chain.p2p }} \
         --rpc-port {{ networks.relay_chain.rpc }} \
         --ws-port {{ networks.relay_chain.ws }} \
         --pruning=archive \
         --name="YOUR-NODE-NAME (Embedded Relay)"

    [Install]
    WantedBy=multi-user.target
    ```

!!! nota
    Si tiene problemas con la telemetría predeterminada, puede agregar la marca `--no-telemetry` para ejecutar el nodo completo sin la telemetría activada.
    
!!! nota
    Puede especificar un puerto Prometheus personalizado con la `--promethues-port XXXX` bandera (reemplazando `XXXX` con el número de puerto real). Esto es posible tanto para la paracadena como para la cadena de relés integrada.

¡Casi llegamos! Regístrese e inicie el servicio ejecutando:

```
systemctl enable moonbeam.service
systemctl start moonbeam.service
```

Y, por último, verifique que el servicio se esté ejecutando:

```
systemctl status moonbeam.service
```

![Service Status](/images/fullnode/fullnode-binary2.png)

También puede verificar los registros ejecutando:

```
journalctl -f -u moonbeam.service
```

![Service Logs](/images/fullnode/fullnode-binary3.png)

## Opciones y banderas avanzadas

--8<-- 'text/setting-up-node/advanced-flags.md'

## Actualización del cliente

A medida que continúa el desarrollo de Moonbeam, a veces será necesario actualizar el software de su nodo. Los operadores de nodos serán notificados en nuestro [canal de Discord](https://discord.gg/PfpUATX) cuando las actualizaciones estén disponibles y si son necesarias (algunas actualizaciones de clientes son opcionales). El proceso de actualización es sencillo y es el mismo para un nodo completo o un clasificador.

Primero, detenga el contenedor docker o el servicio systemd:

```
sudo docker stop `CONTAINER_ID`
# or
sudo systemctl stop moonbeam
```

Luego, instale la nueva versión repitiendo los pasos descritos anteriormente, asegurándose de que está utilizando la última etiqueta disponible. Después de la actualización, puede volver a iniciar el servicio.

### Purgando la Cadena

Ocasionalmente, Moonbase Alpha podría purgarse y reiniciarse en torno a actualizaciones importantes. Como siempre, los operadores de nodos serán notificados con anticipación (a través de nuestro [canal Discord](https://discord.gg/PfpUATX)) si esta actualización va acompañada de una purga. También puede purgar su nodo si su directorio de datos individual se corrompe.

Para hacerlo, primero detenga el contenedor de la ventana acoplable o el servicio systemd:

```
sudo docker stop `CONTAINER_ID`
# or
sudo systemctl stop moonbeam
```

A continuación, elimine el contenido de la carpeta donde se almacenan los datos de la cadena (tanto para la cadena paracadena como para la cadena de retransmisión):

```
sudo rm -rf {{ networks.moonbase.node_directory }}/*
```

Por último, instale la versión más reciente repitiendo los pasos descritos anteriormente, asegurándose de que está utilizando la última etiqueta disponible. Si es así, puede iniciar un nuevo nodo con un directorio de datos nuevo.

## Telemetría

Para habilitar el servidor de telemetría de su nodo Moonbase Alpha, puede seguir [este tutorial](/node-operators/networks/telemetry/).

No es necesario ejecutar telemetría en un nodo completo. Sin embargo, es un requisito para los alzadores.

Además, puede ver la información actual de telemetría Moonbase Alpha visitando [este enlace](https://telemetry.polkadot.io/#list/Moonbase%20Alpha).

## Registros y resolución de problemas

Verá registros tanto de la cadena de relés como de la paracadena. La cadena de relés tendrá el prefijo `[Relaychain]`, mientras que la paracadena no tiene prefijo.

### Puertos P2P no abiertos

Si no ve un `Imported`mensaje (sin la `[Relaychain]` etiqueta), debe verificar la configuración del puerto P2P. El puerto P2P debe estar abierto al tráfico entrante.

### En sintonía

Ambas cadenas deben estar sincronizadas en todo momento, y debería ver los mensajes `Imported` o `Idle` y tener pares conectados.

### Génesis no coincide

Moonbase Alpha TestNet a menudo se actualiza. En consecuencia, es posible que vea el siguiente mensaje:

```
DATE [Relaychain] Bootnode with peer id `ID` is on a different
chain (our genesis: GENESIS_ID theirs: OTHER_GENESIS_ID)
```

Por lo general, esto significa que está ejecutando una versión anterior y deberá actualizarla.

Anunciamos las actualizaciones (y la purga de la cadena correspondiente) a través de nuestro [canal de Discord con](https://discord.gg/PfpUATX) al menos 24 horas de anticipación.
