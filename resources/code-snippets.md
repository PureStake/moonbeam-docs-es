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

**Información del nodo de desarrollo de Moonbeam:**

--8<-- 'text/metamask-local/development-node-details.md'

**Moonbase Alpha TestNet:**

--8<-- 'text/testnet/testnet-details.md'
