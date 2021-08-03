---
title: Ejecutar un nodo
description: Cómo ejecutar un nodo Parachain completo para Moonbeam Network para tener su RPC Endpoint o producir bloques
---

# Ejecutar un nodo en Moonbeam

![Full Node Moonbeam Banner](/images/fullnode/fullnode-banner.png)

## Introducción {: #introduction } 

Ejecutar un nodo completo en una red basada en Moonbeam le permite conectarse a la red, sincronizar con un nodo de arranque, obtener acceso local a los puntos finales de RPC, crear bloques en la parachain y más.

Hay múltiples despliegues de Moonbeam, incluyendo Moonbase Alpha TestNet, Moonriver en Kusama, y ​​eventualmente habrá Moonbeam en Polkadot. Así es como se nombran estos entornos y sus correspondientes nombres de [archivo de especificación de cadena](https://substrate.dev/docs/en/knowledgebase/integrate/chain-spec):

|    Red     |     | Alojado por |     |   Nombre de cadena    |
| :------------: | :-: | :-------: | :-: | :-------------: |
| Moonbase Alpha |     | PureStake |     |    {{ networks.moonbase.chain_spec }}     |
|   Moonriver    |     |  Kusama   |     |    {{ networks.moonriver.chain_spec }}    |
|    Moonbeam    |     | Polkadot  |     | _not available_ |

Esta guía está destinada a personas con experiencia en la compilación de nodos de blockchain basados en sustrato[Substrate](https://substrate.dev/). A parachain node is similar to a typical Substrate node, but there are some differences. Un nodo de parachain es similar a un nodo de sustrato típico, pero existen algunas diferencias. Un nodo de parachain de sustrato es una compilación más grande porque contiene código para ejecutar el parachain en sí, así como código para sincronizar la cadena de retransmisión y facilitar la comunicación entre los dos. Como tal, esta compilación es bastante grande y puede tardar más de 30 minutos y requiere 32 GB de memoria.

!!! nota
    Moonbase Alpha todavía se considera un Alphanet y, como tal , _no_ tendrá un tiempo de actividad del 100%. La parachain _se_ purgará de vez en cuando. Durante el desarrollo de su aplicación, asegúrese de implementar un método para volver a desplegar sus contratos y cuentas en una nueva parachain rápidamente. Las purgas de cadenas se anunciarán a través de nuestro [canal de Discord](https://discord.gg/PfpUATX) con al menos 24 horas de anticipación.

## Requisitos {: #requirements } 

Las especificaciones mínimas recomendadas para ejecutar un nodo se muestran en la siguiente tabla. Para nuestro despliegue de Kusama y Polkadot MainNet, los requisitos de disco serán mayores a medida que la red crezca.

=== "Moonbase Alpha"
    |  Componente   |     | Requisito                                                                                                                |
    | :----------: | :-: | :------------------------------------------------------------------------------------------------------------------------- |
    |   **CPU**    |     | 8 Cores (Fastest per core speed)                                                                      |
    |   **RAM**    |     | 16 GB                                                                         |
    |   **SSD**    |     | 50 GB (to start)                                                                                            |
    | **Firewall** |     | P2P port must be open to incoming traffic:<br>&nbsp; &nbsp; - Source: Any<br>&nbsp; &nbsp; - Destination: 30333, 30334 TCP |

=== "Moonriver"
    |  Componente   |     | Requisito                                                                                                                |
    | :----------: | :-: | :------------------------------------------------------------------------------------------------------------------------- |
    |   **CPU**    |     | 8 núcleos (más rápido por velocidad de núcleo)                                                                    |
    |   **RAM**    |     | 16 GB                                                                         |
    |   **SSD**    |     | 300 GB (para empezar)                                                                              |
    | **Firewall** |     | El puerto P2P debe estar abierto al tráfico entrante:<br>&nbsp; &nbsp; - Origen: Any<br>&nbsp; &nbsp; - Destino: 30333, 30334 TCP |


!!! nota
    Si no ve un `Imported` mensaje (sin la `[Relaychain]` etiqueta) cuando ejecuta un nodo, es posible que deba volver a verificar la configuración de su puerto.

## Puertos en ejecución {: #running-ports } 

Como se indicó anteriormente, los nodos de retransmisión / parachain escucharán en varios puertos. Los puertos de sustrato predeterminados se utilizan en la parachain, mientras que la cadena de relés escuchará en el siguiente puerto superior.

Los únicos puertos que deben estar abiertos para el tráfico entrante son los designados para P2P.

### Puertos predeterminados para un nodo completo de Parachain {: #default-ports-for-a-parachain-full-node } 

|  Descripción   |     |                Puerto                 |
| :------------: | :-: | :---------------------------------: |
|    **P2P**     |     | {{ networks.parachain.p2p }} (TCP)  |
|    **RPC**     |     |    {{ networks.parachain.rpc }}     |
|     **WS**     |     |     {{ networks.parachain.ws }}     |
| **Prometheus** |     | {{ networks.parachain.prometheus }} |

### Puertos predeterminados de la cadena de relés incorporada {: #default-ports-of-embedded-relay-chain } 

|  Descripción   |     |                 Puerto                  |
| :------------: | :-: | :-----------------------------------: |
|    **P2P**     |     | {{ networks.relay_chain.p2p }} (TCP)  |
|    **RPC**     |     |    {{ networks.relay_chain.rpc }}     |
|     **WS**     |     |     {{ networks.relay_chain.ws }}     |
| **Prometheus** |     | {{ networks.relay_chain.prometheus }} |

## Instrucciones de instalación: Docker {: #installation-instructions-docker } 

Un nodo Moonbeam se puede activar rápidamente usando Docker. Para obtener más información sobre la instalación de Docker, visite [esta página](https://docs.docker.com/get-docker/). En el momento de escribir este artículo, la versión de Docker utilizada era la 19.03.6. Cuando se conecte a Moonriver en Kusama, llevará unos días sincronizar completamente la cadena de relés Kusama incorporada. Asegúrese de que su sistema cumpla con los [requisitos](#requisitos).

Cree un directorio local para almacenar los datos de la cadena:

=== "Moonbase Alpha"
    ```
    mkdir {{ networks.moonbase.node_directory }}
    ```

=== "Moonriver"
    ```
    mkdir {{ networks.moonriver.node_directory }}
    ```

A continuación, asegúrese de establecer la propiedad y los permisos correspondientes para el directorio local que almacena los datos de la cadena. En este caso, configure los permisos necesarios para un usuario específico o actual (reemplace `DOCKER_USER` por el usuario real que ejecutará el `docker` comando):

=== "Moonbase Alpha"
    ```
    # chown to a specific user
    chown DOCKER_USER {{ networks.moonbase.node_directory }}

    # chown to current user
    sudo chown -R $(id -u):$(id -g) {{ networks.moonbase.node_directory }}
    ```

=== "Moonriver"
    ```
    # chown to a specific user
    chown DOCKER_USER {{ networks.moonriver.node_directory }}

    # chown to current user
    sudo chown -R $(id -u):$(id -g) {{ networks.moonriver.node_directory }}
    ```

Ahora, ejecute el comando docker run. Si está configurando un nodo de clasificación, asegúrese de seguir los fragmentos de código para "Collator". Tenga en cuenta que debe reemplazar `YOUR-NODE-NAME` en dos lugares diferentes.

### Nodo completo {: #full-node } 

=== "Moonbase Alpha"
    ```
    docker run --network="host" -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_release_tag }} \
    --base-path=/data \
    --chain {{ networks.moonbase.chain_spec }} \
    --name="YOUR-NODE-NAME" \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

=== "Moonriver"
    ```
    docker run --network="host" -v "{{ networks.moonriver.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonriver.parachain_release_tag }} \
    --base-path=/data \
    --chain {{ networks.moonriver.chain_spec }} \
    --name="YOUR-NODE-NAME" \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

### Collator {: #collator } 

=== "Moonbase Alpha"
    ```
    docker run --network="host" -v "{{ networks.moonbase.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonbase.parachain_release_tag }} \
    --base-path=/data \
    --chain {{ networks.moonbase.chain_spec }} \
    --name="YOUR-NODE-NAME" \
    --validator \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

=== "Moonriver"
    ```
    docker run --network="host" -v "{{ networks.moonriver.node_directory }}:/data" \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    purestake/moonbeam:{{ networks.moonriver.parachain_release_tag }} \
    --base-path=/data \
    --chain {{ networks.moonriver.chain_spec }} \
    --name="YOUR-NODE-NAME" \
    --validator \
    --execution wasm \
    --wasm-execution compiled \
    --pruning archive \
    --state-cache-size 1 \
    -- \
    --pruning archive \
    --name="YOUR-NODE-NAME (Embedded Relay)"
    ```

Si está utilizando MacOS, puede encontrar todos los fragmentos de código [aquí](/snippets/text/full-node/macos-node/).

Una vez que Docker extrae las imágenes necesarias, se iniciará su nodo completo Moonbeam (o Moonriver), mostrando mucha información, como la especificación de la cadena, el nombre del nodo, el rol, el estado de génesis y más:

![Full Node Starting](/images/fullnode/fullnode-docker1.png)

!!! nota
    Si desea ejecutar un punto final RPC, conectar polkadot.js.org o ejecutar su propia aplicación, use los indicadores `--unsafe-rpc-external` y / o  `--unsafe-ws-external` ejecute el nodo completo con acceso externo a los puertos RPC. Más detalles están disponibles ejecutando `moonbeam --help`.  

!!! nota
    Puede especificar un puerto Prometheus personalizado con la `--prometheus-port XXXX` bandera (reemplazando `XXXX` con el número de puerto real). Esto es posible tanto para la parachain como para la cadena de relés integrada.

El comando anterior habilitará todos los puertos expuestos necesarios para el funcionamiento básico, incluidos los puertos P2P y Prometheus (telemetría). Este comando es compatible para usar con la telemetría del Watchdog del nodo de Gantree. Si desea exponer puertos específicos, habilítelos en la línea de comando de ejecución de Docker como se muestra a continuación. Sin embargo, hacerlo evitará que el contenedor Gantree Node Watchdog (telemetría) acceda al contenedor Moonbeam, así que no haga esto cuando ejecute un collator a menos que comprenda las [docker networking](https://docs.docker.com/network/).

```
docker run -p {{ networks.relay_chain.p2p }}:{{ networks.relay_chain.p2p }} -p {{ networks.parachain.p2p }}:{{ networks.parachain.p2p }} -p {{ networks.parachain.rpc }}:{{ networks.parachain.rpc }} -p {{ networks.parachain.ws }}:{{ networks.parachain.ws }} #rest of code goes here
```

Durante el proceso de sincronización, verá mensajes tanto de la cadena de relés incorporada como de la parachain (sin etiqueta). Estos mensajes muestran un bloque de destino (estado de red en vivo) y un mejor bloque (estado sincronizado de nodo local).

![Full Node Starting](/images/fullnode/fullnode-docker2.png)

!!! nota
    Llevará unos días sincronizar completamente la cadena de relés de Kusama incorporada. Asegúrese de que su sistema cumpla con los [requisitos](#requisitos). 

Si siguió las instrucciones de instalación para Moonbase Alpha, una vez sincronizado, tendrá un nodo de Moonbase Alpha TestNet ejecutándose localmente.

Si siguió las instrucciones de instalación de Moonriver, una vez sincronizado, estará conectado a sus compañeros y verá los bloques que se producen en la red Moonriver. Tenga en cuenta que, en este caso, también debe sincronizar con la cadena de relés de Kusama, lo que puede demorar algunos días.

## Instrucciones de instalación: binario {: #installation-instructions-binary } 

Esta sección pasa por el proceso de usar el binario de lanzamiento y ejecutar un nodo completo Moonbeam como un servicio systemd. Los siguientes pasos se probaron en una instalación de Ubuntu 18.04. Moonbeam puede funcionar con otras versiones de Linux, pero Ubuntu es actualmente la única versión probada.

Para construir manualmente los binarios usted mismo, consulte la guía [Compilar Moonbeam Binary](/node-operators/networks/compile-binary).

### Usar el binario de lanzamiento {: #use-the-release-binary } 

Hay un par de formas de comenzar con el binario Moonbeam. Puede compilar el binario usted mismo, pero todo el proceso puede tardar unos 30 minutos en instalar las dependencias y crear el binario. Si está interesado en seguir esta ruta, consulte la página [Compilar el binario](/) de nuestra documentación.

O puede usar el [binario de lanzamiento](https://github.com/PureStake/moonbeam/releases) para comenzar de inmediato.

Use `wget` para obtener el binario de la última versión:


=== "Moonbase Alpha"
    ```
    wget https://github.com/PureStake/moonbeam/releases/download/{{ networks.moonbase.parachain_release_tag }}/moonbeam
    ```

=== "Moonriver"
    ```
    wget https://github.com/PureStake/moonbeam/releases/download/{{ networks.moonriver.parachain_release_tag }}/moonbeam
    ``` 

Para verificar que ha descargado la versión correcta, puede ejecutarla `sha256sum moonbeam` en su terminal, debería recibir el siguiente resultado:

=== "Moonbase Alpha"
    ```
    {{ networks.moonbase.parachain_sha256sum }}
    ```

=== "Moonriver"
    ```
    {{ networks.moonriver.parachain_sha256sum }}
    ```

Una vez que haya recuperado el binario, puede usarlo para ejecutar el servicio systemd.

### Ejecución del servicio Systemd {: #running-the-systemd-service } 

Los siguientes comandos configurarán todo lo relacionado con la ejecución del servicio.

Primero, creemos una cuenta de servicio para ejecutar el servicio:

=== "Moonbase Alpha"
    ```
    adduser moonbase_service --system --no-create-home
    ```

=== "Moonriver"
    ```
    adduser moonriver_service --system --no-create-home
    ```

A continuación, cree un directorio para almacenar el binario y los datos. Asegúrese de establecer la propiedad y los permisos en consecuencia para el directorio local que almacena los datos de la cadena:

=== "Moonbase Alpha"
    ```
    mkdir {{ networks.moonbase.node_directory }}
    chown moonbase_service {{ networks.moonbase.node_directory }}
    ```

=== "Moonriver"
    ```
    mkdir {{ networks.moonriver.node_directory }}
    chown moonriver_service {{ networks.moonriver.node_directory }}
    ```

Ahora, copie el binario construido en la última sección a la carpeta creada. Si [compiló el binario](/node-operators/networks/compile-binary/) usted mismo, deberá copiar el binario en el directorio de destino (`./target/release/{{ networks.moonbase.binary_name }}`). De lo contrario, copie el binario Moonbeam en la raíz:

=== "Moonbase Alpha"
    ```
    cp ./{{ networks.moonbase.binary_name }} {{ networks.moonbase.node_directory }}
    ```

=== "Moonriver"
    ```
    cp ./{{ networks.moonriver.binary_name }} {{ networks.moonriver.node_directory }}
    ```

El siguiente paso es crear el archivo de configuración systemd. Si está configurando un nodo de clasificación, asegúrese de seguir los fragmentos de código para "Collator". Tenga en cuenta que tiene que:

 - Reemplazar `YOUR-NODE-NAME` en dos lugares diferentes
 - Verifique que el binario esté en la ruta correcta como se describe a continuación (_ExecStart_)
 - Vuelva a verificar la ruta base si ha utilizado un directorio diferente
 - Nombra el archivo `/etc/systemd/system/moonbeam.service`

#### Nodo completo {: #full-node } 

=== "Moonbase Alpha"
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
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --base-path {{ networks.moonbase.node_directory }} \
         --chain {{ networks.moonbase.chain_spec }} \
         --name "YOUR-NODE-NAME" \
         -- \
         --port {{ networks.relay_chain.p2p }} \
         --rpc-port {{ networks.relay_chain.rpc }} \
         --ws-port {{ networks.relay_chain.ws }} \
         --pruning=archive \
         --name="YOUR-NODE-NAME (Embedded Relay)"

    [Install]
    WantedBy=multi-user.target
    ```

=== "Moonriver"
    ```
    [Unit]
    Description="Moonriver systemd service"
    After=network.target
    StartLimitIntervalSec=0
    
    [Service]
    Type=simple
    Restart=on-failure
    RestartSec=10
    User=moonriver_service
    SyslogIdentifier=moonriver
    SyslogFacility=local7
    KillSignal=SIGHUP
    ExecStart={{ networks.moonriver.node_directory }}/{{ networks.moonriver.binary_name }} \
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --base-path {{ networks.moonriver.node_directory }} \
         --chain {{ networks.moonriver.chain_spec }} \
         --name "YOUR-NODE-NAME" \
         -- \
         --port {{ networks.relay_chain.p2p }} \
         --rpc-port {{ networks.relay_chain.rpc }} \
         --ws-port {{ networks.relay_chain.ws }} \
         --pruning=archive \
         --name="YOUR-NODE-NAME (Embedded Relay)"
    
    [Install]
    WantedBy=multi-user.target
    ```
#### Collator {: #collator } 

=== "Moonbase Alpha"
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
         --validator \
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --base-path {{ networks.moonbase.node_directory }} \
         --chain {{ networks.moonbase.chain_spec }} \
         --name "YOUR-NODE-NAME" \
         -- \
         --port {{ networks.relay_chain.p2p }} \
         --rpc-port {{ networks.relay_chain.rpc }} \
         --ws-port {{ networks.relay_chain.ws }} \
         --pruning=archive \
         --name="YOUR-NODE-NAME (Embedded Relay)"

    [Install]
    WantedBy=multi-user.target
    ```

=== "Moonriver"
    ```
    [Unit]
    Description="Moonriver systemd service"
    After=network.target
    StartLimitIntervalSec=0

    [Service]
    Type=simple
    Restart=on-failure
    RestartSec=10
    User=moonriver_service
    SyslogIdentifier=moonriver
    SyslogFacility=local7
    KillSignal=SIGHUP
    ExecStart={{ networks.moonriver.node_directory }}/{{ networks.moonriver.binary_name }} \
         --validator \
         --port {{ networks.parachain.p2p }} \
         --rpc-port {{ networks.parachain.rpc }} \
         --ws-port {{ networks.parachain.ws }} \
         --pruning=archive \
         --state-cache-size 1 \
         --base-path {{ networks.moonriver.node_directory }} \
         --chain {{ networks.moonriver.chain_spec }} \
         --name "YOUR-NODE-NAME" \
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
    Puede especificar un puerto Prometheus personalizado con la `--prometheus-port XXXX` bandera (reemplazando `XXXX` con el número de puerto real). Esto es posible tanto para la paracadena como para la cadena de relés integrada.

¡Casi llegamos! Regístrese e inicie el servicio ejecutando:

```
systemctl enable moonbeam.service
systemctl start moonbeam.service
```

Y, por último, verifique que el servicio se esté ejecutando:

```
systemctl status moonbeam.service
```

![Service Status](/images/fullnode/fullnode-binary1.png)

También puede verificar los registros ejecutando:

```
journalctl -f -u moonbeam.service
```

![Service Logs](/images/fullnode/fullnode-binary2.png)

## Opciones y banderas avanzadas {: #advanced-flags-and-options } 

--8<-- 'text/setting-up-node/advanced-flags.md'

## Actualización del cliente {: #updating-the-client } 

AA medida que continúa el desarrollo de Moonbeam, a veces será necesario actualizar el software de su nodo. Los operadores de nodo serán notificados en nuestro [canal de Discord](https://discord.gg/PfpUATX) cuando las actualizaciones estén disponibles y si son necesarias (algunas actualizaciones de clientes son opcionales). El proceso de actualización es sencillo y es el mismo para un nodo completo o un collator.

Primero, detenga el contenedor docker o el servicio systemd:

```
sudo docker stop `CONTAINER_ID`
# or
sudo systemctl stop moonbeam
```

Luego, instale la nueva versión repitiendo los pasos descritos anteriormente, asegurándose de que está utilizando la última etiqueta disponible. Después de la actualización, puede iniciar el servicio nuevamente.

### Purgando la Cadena {: #purging-the-chain } 

Ocasionalmente, Moonbase Alpha podría purgarse y reiniciarse en torno a actualizaciones importantes. Como siempre, los operadores de nodos serán notificados con anticipación (a través de nuestro [canal de Discord](https://discord.gg/PfpUATX) si esta actualización va acompañada de una purga. También puede purgar su nodo si su directorio de datos individual se corrompe.

Para hacerlo, primero detenga el contenedor de la ventana acoplable o el servicio systemd:

```
sudo docker stop `CONTAINER_ID`
# or
sudo systemctl stop moonbeam
```

A continuación, elimine el contenido de la carpeta donde se almacenan los datos de la cadena (tanto para la cadena parachain como para la cadena de retransmisión):

```
sudo rm -rf {{ networks.moonbase.node_directory }}/*
```

Por último, instale la versión más reciente repitiendo los pasos descritos anteriormente, asegurándose de que está utilizando la última etiqueta disponible. Si es así, puede iniciar un nuevo nodo con un directorio de datos nuevo.

## Telemetría {: #telemetry } 

Para habilitar el servidor de telemetría de su nodo Moonbase Alpha o Moonriver, puede seguir [este tutorial](/node-operators/networks/telemetry/).

No es necesario ejecutar telemetría en un nodo completo. Sin embargo, es un requisito para los collators.

Además, puede consultar la [telemetría Moonbase Alpha](https://telemetry.polkadot.io/#list/Moonbase%20Alpha) actual y los datos de [telemetría Moonriver](https://telemetry.polkadot.io/#list/Moonriver).

## Registros y resolución de problemas {: #logs-and-troubleshooting } 

Verá registros tanto de la cadena de relés como de la parachain. La cadena de relés tendrá el prefijo `[Relaychain]`, mientras que la parachain no tiene prefijo.

### Puertos P2P no abiertos {: #p2p-ports-not-open } 

Si no ve un `Imported` mensaje (sin la `[Relaychain]` etiqueta), debe verificar la configuración del puerto P2P. El puerto P2P debe estar abierto al tráfico entrante.

### En sintonía {: #in-sync } 

Ambas cadenas deben estar sincronizadas en todo momento, y debería ver los mensajes `Imported` o `Idle` y tener pares conectados.

### Génesis no coincide {: #genesis-mismatching } 

Moonbase Alpha TestNet a menudo se actualiza. En consecuencia, es posible que vea el siguiente mensaje:

```
DATE [Relaychain] Bootnode with peer id `ID` is on a different
chain (our genesis: GENESIS_ID theirs: OTHER_GENESIS_ID)
```

Por lo general, esto significa que está ejecutando una versión anterior y deberá actualizarla.

Anunciamos las actualizaciones (y la purga de la cadena correspondiente) a través de nuestro [canal de Discord](https://discord.gg/PfpUATX) al menos 24 horas de anticipación.

