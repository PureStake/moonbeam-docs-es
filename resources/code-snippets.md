---
title: Fragmentos de Código
description: Para que sea más fácil comenzar con Moonbeam, aquí hay fragmentos de código para cada uno de los tutoriales que hemos creado.
---

# Fragmentos de Código

## Configuración de un Nodo Moonbeam Local {: #setting-up-a-local-moonbeam-node } 

**Clonar el repositorio de tutoriales de Moonbeam:**

```
git clone -b {{ networks.development.build_tag }} https://github.com/PureStake/moonbeam
cd moonbeam
```

**Instalar Substrate y sus prerrequisitos:**

```
--8<-- 'code/setting-up-node/substrate.md'
```

**Agregar Rust a la ruta del sistema:**

```
--8<-- 'code/setting-up-node/cargoerror.md'
```

**Construir el nodo de desarrollo:**

```
--8<-- 'code/setting-up-node/build.md'
```

**Ejecutar el nodo en modo desarrollo:**

```
--8<-- 'code/setting-up-node/runnode.md'
```

**Depurar la cadena, eliminar todo dato previo, si se ejecutó un nodo en modo desarrollo con anterioridad:**

```
./target/release/moonbeam-development purge-chain --dev
```

**Ejecutar el nodo en modo desarrollo, suprimiendo la información del bloque, excepto los errores de impresión en la consola:**

```
./target/release/moonbeam-development --dev -lerror
```

## Cuenta Génesis  {: #genesis-account } 

--8<-- 'text/metamask-local/dev-account.md'

## Cuentas de Desarrollo {: #development-accounts } 

--8<-- 'code/setting-up-node/dev-accounts.md'

--8<-- 'code/setting-up-node/dev-testing-account.md'

## MetaMask {: #metamask } 

=== "Moonbeam Development Node"

    - Network Name: `Moonbeam Dev`
    - RPC URL: `{{ networks.development.rpc_url }}`
    - ChainID: `{{ networks.development.chain_id }}`
    - Symbol (Optional): `DEV`
    - Block Explorer (Optional): `{{ networks.development.block_explorer }}`

=== "Moonbase Alpha TestNet"

    - Network Name: `Moonbase Alpha`
    - RPC URL: `{{ networks.moonbase.rpc_url }}`
    - ChainID: `{{ networks.moonbase.chain_id }}`
    - Symbol (Optional): `DEV`
    - Block Explorer (Optional): `{{ networks.moonbase.block_explorer }}`

=== "Moonriver"

    - Network Name: `Moonriver`
    - RPC URL: `{{ networks.moonriver.rpc_url }}`
    - ChainID: `{{ networks.moonriver.chain_id }}`
    - Symbol (Optional): `MOVR`
    - Block Explorer (Optional): `{{ networks.moonriver.block_explorer }}`
