---
title: Depurar y rastrear
description: Aprenda a aprovechar la API de depuración de Geth y el módulo OpenEthereum Trace en Moonbeam
---

# API de depuración y módulo de seguimiento

![Full Node Moonbeam Banner](/images/debugtrace/debugtrace-banner.png)

## Introducción

Tanto la API de depuración de Geth como el módulo de seguimiento de OpenEthereum proporcionan métodos RPC no estándar para obtener una visión más profunda del procesamiento de transacciones.

Con el lanzamiento de Moonbase Alpha v7, como parte del objetivo de Moonbeam de proporcionar una experiencia de Ethereum perfecta para los desarrolladores, ambos métodos `debug_traceTransaction` y `trace_filter` RPC ya están disponibles.

La compatibilidad con ambos métodos RPC es un hito importante porque muchos proyectos, como [The Graph](https://thegraph.com/) o [Blockscout](https://docs.blockscout.com/),  dependen de ellos para indexar datos de blockchain.

Ambas llamadas son bastante pesadas por parte del nodo. Por lo tanto, es necesario realizar este RPC contra un nodo que se ejecute localmente con el `--ethapi=debug` indicador para `debug_traceTransaction`, y / o el `--ethapi=trace` indicador para `trace_filter`. Actualmente, puede activar dos tipos diferentes de nodos:

 - **Nodo de desarrollo Moonbeam** — ejecute su propia instancia Moonbeam en su entorno privado. Para hacerlo, puede seguir [esta guía](/getting-started/local-node/setting-up-a-node/). Asegúrese de consultar la [sección de banderas avanzadas](/getting-started/local-node/setting-up-a-node/#advanced-flags-and-options)
 - **Nodo Moonbase Alpha** — ejecute un nodo completo de TestNet y acceda a sus propios puntos finales privados. Para hacerlo, puede seguir [esta guía](/node-operators/networks/full-node/). Asegúrese de consultar la [sección de banderas avanzadas](/node-operators/networks/full-node/#advanced-flags-and-options)

## API de depuración de Geth

La `debug_traceTransaction` implementación de RPC sigue [las pautas de la API de depuración de Geth](https://geth.ethereum.org/docs/rpc/ns-debug#debug_tracetransaction).

El método RPC requiere que se ejecute el hash de la transacción. Como parámetros opcionales, puede proporcionar lo siguiente:

 - **disableStorage** — una entrada: boolean (predeterminado: _false_). Establecer esto en verdadero deshabilita la captura de almacenamiento
 - **disableMemory** — una entrada: boolean (predeterminado: _false_). Establecer esto en verdadero deshabilita la captura de memoria
 - **disableStack** — una entrada: booleana (predeterminado: _false_). Establecer esto en verdadero deshabilita la captura de pila

El seguimiento de transacciones basado en JavaScript no es compatible en este momento.

## Módulo de seguimiento

La `trace_filter` implementación de RPC sigue las [pautas del módulo de seguimiento de OpenEthereum](https://openethereum.github.io/JSONRPC-trace-module#trace_filter).

El método RPC requiere cualquiera de los siguientes parámetros opcionales:

 - **fromBlock** — una entrada: número de bloque (`hex`), `earliest` que es el bloque de génesis o `latest`(predeterminado) el mejor bloque disponible. Trazar bloque de inicio
 - **toBlock** —  una entrada: el número de bloque (`hex`), `earliest` que es el bloque génesis o el `latest` mejor bloque disponible. Bloque de finalización de seguimiento
 - **fromAddress** —  una entrada: matriz de direcciones. Filtre las transacciones realizadas desde estas direcciones únicamente. Si se proporciona una matriz vacía, no se realiza ningún filtrado con este campo
 - **toAddress** — una entrada: matriz de direcciones. Filtre las transacciones realizadas desde estas direcciones únicamente. Si se proporciona una matriz vacía, no se realiza ningún filtrado con este campo
 - **después** —  una entrada: offset (`uint`), el valor predeterminado es `0`. Número de desplazamiento (o inicio) de seguimiento
 - **contar** — una entrada: número de trazas (`uint`). Número de trazas para mostrar en un lote

## Pruébalo en Moonbase Alpha

Como se mencionó anteriormente, para usar ambas funciones, debe tener un nodo que se ejecute con los indicadores `debug` y `trace` Para este ejemplo, se utiliza un nodo completo local Moonbase Alpha, con el punto final RPC HTTP en `http://127.0.0.1:9933`.  Si tiene un nodo en ejecución, debería ver un registro de terminal similar:

![Debug API](/images/debugtrace/debugtrace-images1.png)

Por ejemplo, para la `debug_traceTransaction` llamada, puede realizar la siguiente solicitud JSON RPC en su terminal (en este caso, para el hash de la transacción `0x04978f83e778d715eb074352091b2159c0689b5ae2da2554e8fe8e609ab463bf`):

```
curl http://localhost:9933 -H "Content-Type:application/json;charset=utf-8" -d \
  '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"debug_traceTransaction",
    "params": ["0x04978f83e778d715eb074352091b2159c0689b5ae2da2554e8fe8e609ab463bf"]
  }'
```

El nodo responde con la información de la transacción reproducida paso a paso (la respuesta se recortó porque es bastante larga):

![Trace Debug Node Running](/images/debugtrace/debugtrace-images2.png)

Para la `trace_filter` lamada, puede realizar la siguiente solicitud JSON RPC en su terminal (en este caso, el filtro es del bloque 20000 al 25000, solo para transacciones donde está el destinatario `0x4E0078423a39EfBC1F8B5104540aC2650a756577`, comenzará con un desplazamiento cero y proporcionará los primeros 20 seguimientos ):

```
curl http://localhost:9933 -H "Content-Type:application/json;charset=utf-8" -d \
  '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"trace_filter", "params":[{"fromBlock":"0x4E20","toBlock":"0x5014","toAddress":["0x4E0078423a39EfBC1F8B5104540aC2650a756577"],"after":0,"count":20}]
  }'
```

El nodo responde con la información de seguimiento correspondiente al filtro (la respuesta se recortó porque es bastante larga).

![Trace Filter Node Running](/images/debugtrace/debugtrace-images3.png)

