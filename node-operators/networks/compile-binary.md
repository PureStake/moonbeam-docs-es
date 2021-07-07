---
title: Compilar el binario
description: Cómo compilar el binario Moonbeam para ejecutar un nodo Parachain completo, obtener acceso a los puntos finales RPC y producir bloques para Moonbeam Network.
---

# Compilar el binario Moonbeam

![Full Node Moonbeam Banner](/images/fullnode/compile-binary-banner.png)

## Introducción

Hay varias formas de comenzar a ejecutar un nodo completo en la red Moonbeam. Esta guía pasa por el proceso de compilación del binario Moonbeam a partir del código fuente de Rust. Para obtener una descripción general más general de los nodos en ejecución, o para comenzar con Docker, consulte la página [Ejecutar un Nodo](/node-operators/networks/full-node) de nuestra documentación.

Esta guía está destinada a personas con experiencia en la compilación de nodos de cadena de bloques basados [Substrate](https://substrate.dev/) Un nodo de paracadena es similar a un nodo de sustrato típico, pero existen algunas diferencias. Un nodo de parachain de sustrato es una compilación más grande porque contiene código para ejecutar el parachain en sí, así como código para sincronizar la relay chain y facilitar la comunicación entre los dos. Como tal, esta compilación es bastante grande y puede tardar más de 30 minutos y requiere 32 GB de memoria.

## Compilar el binario

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

A continuación, instale un entorno de desarrollo de sustrato, incluido Rust, ejecutando:

```
--8<-- 'code/setting-up-node/substrate.md'
```

Por último, compile el binario de parachain:

```
cargo build --release
```

![Compiling Binary](/images/fullnode/compile-binary1.png)

Si aparece un _error de carga no encontrada_ en la terminal, agregue manualmente Rust a la ruta de su sistema o reinicie su sistema:

```
--8<-- 'code/setting-up-node/cargoerror.md'
```

Ahora puede usar el binario Moonbeam para [ejecutar un servicio systemd ](/node-operators/networks/full-node/#running-the-systemd-service).
