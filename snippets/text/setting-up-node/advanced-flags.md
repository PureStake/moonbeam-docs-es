Con el lanzamiento de Moonbase Alpha v7, los nodos también brindan acceso a algunos métodos RPC no estándar, que permiten a los desarrolladores inspeccionar y depurar transacciones durante el tiempo de ejecución. Actualmente, hay dos funciones disponibles:

 - API de depuración de Geth: más específicamente, el método `debug_traceTransaction` Esto intentará ejecutar la transacción de la misma manera en que se ejecutó. Puede leer más sobre este método RPC en [este enlace](https://geth.ethereum.org/docs/rpc/ns-debug#debug_tracetransaction)
 - Módulo de seguimiento OpenEthereum:: más específicamente, el método `trace_filter`. Esto devuelve el seguimiento que coincide con un filtro específico proporcionado como entrada a la llamada RPC. Puede leer más sobre este método RPC en [este enlace](https://openethereum.github.io/JSONRPC-trace-module#trace_filter)

Las funciones mencionadas anteriormente se pueden activar utilizando las siguientes banderas:

 - `--ethapi=debug`: habilita la API de depuración de Geth para la llamada RPC `debug_traceTransaction` 
 - `--ethapi=trace`: habilita el módulo de seguimiento OpenEthereum para la llamada RPC `trace_filter` 

!!! note
    Las funciones de depuración / seguimiento aún se están desarrollando activamente. Debido a que estas solicitudes exigen mucho CPU, se recomienda ejecutar el nodo con el indicado `--execution=Native`. Esto utilizará el tiempo de ejecución nativo incluido como parte del ejecutable del nodo en lugar del binario Wasm almacenado en la cadena.

Puede combinar ambos indicadores al ejecutar un nodo.

De forma predeterminada, el número máximo de entradas de rastreo que puede devolver una sola solicitud de `trace_filter` es 500. Una solicitud que exceda este límite devolverá un error. Puede establecer un límite máximo diferente con la siguiente bandera:

 - `--ethapi-trace-max-count <uint>`: establece el número máximo de entradas de seguimiento que devolverá el nodo

Los bloques procesados por solicitudes se almacenan temporalmente en la memoria caché durante un cierto período de tiempo (el valor predeterminado es `300` segundos), después de lo cual se eliminan. Puede establecer una hora diferente para la eliminación con la siguiente bandera:

 - `-ethapi-trace-cache-duration <uint>`: establece la duración (en segundos) después de la cual se descarta la caché de `trace_filter,` para un bloque dado
