---
title: Enviar una Transacción
description: Aprenda a crear y enviar transacciones en la red compatible con Ethereum de Moonbeam con un script simple usando web3.js, ethers.js o web3.py.
---

# Uso de bibliotecas Ethereum para enviar transacciones en Moonbeam

![Ethereum Libraries Integrations Moonbeam](/images/sendtx/web3-libraries-banner.png)

## Introducción {: #introduction } 

Esta guía describe el uso de tres bibliotecas Ethereum diferentes para firmar y enviar manualmente una transacción en Moonbeam. Las tres bibliotecas cubiertas en este tutorial son:

 - [Web3.js](https://web3js.readthedocs.io/)
 - [Ethers.js](https://docs.ethers.io/)
 - [Web3.py](https://web3py.readthedocs.io/)

!!! nota
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

## Comprobación de requisitos previos {: #checking-prerequisites } 

Los ejemplos que utilizan web3.js y ethers.js requieren una instalación previa de Node.js y NPM. El ejemplo que usa web3.py requiere Python y PIP. En el momento de redactar esta guía, las versiones utilizadas fueron:

 - Node.js v15.10.0
 - NPM v7.5.3
 - Python v3.6.9 (web3 requires Python >= 3.5.3 and < 4)
 - PIP3 v9.0.1

A continuación, cree un directorio para almacenar todos los archivos relevantes:

```
mkdir transaction && cd transaction/
```

Para las bibliotecas de JavaScript, primero puede crear un package.jsonarchivo simple (no es obligatorio):

```
npm init --yes
```

En el directorio, instale la biblioteca que se utilizará (web3.py está instalado en el directorio predeterminado de PIP3):

=== "Web3.js"
    ```
    npm i web3
    ```

=== "Ethers.js"
    ```
    npm i ethers
    ```

=== "Web3.py"
    ```
    pip3 install web3
    ```

Las versiones utilizadas al momento de la publicación de esta guía fueron:

 - Web3.js v1.33 (`npm ls web3`)
 - Ethers.js v5.0.31 (`npm ls ethers`)
 - Web3.py v5.17.0 (`pip3 show web3`)

## El archivo de transacciones {: #the-transaction-file } 

Solo se necesita un archivo para ejecutar una transacción entre cuentas. El script que se muestra en esta sección transferirá 1 Token desde una dirección de origen (desde la cual tiene la clave privada) a otra dirección. Puede encontrar el fragmento de código para cada biblioteca aquí (se nombraron arbitrariamente `transaction.*`):

 - Web3.js: [_transaction.js_](/snippets/code/web3-tx-local/transaction.js)
 - Ethers.js: [_transaction.js_](/snippets/code/ethers-tx-local/transaction.js)
 - Web3.py: [_transaction.py_](/snippets/code/web3py-tx/transaction.py)

Cada uno de los archivos, independientemente de la biblioteca utilizada, se ha dividido en tres secciones. En la primera sección ("Definir proveedor y variables"), se importa la biblioteca a utilizar y se definen el proveedor y otras variables (las variables dependen de la biblioteca). Tenga en cuenta que `providerRPC` tiene el punto final RPC del nodo de desarrollo estándar y el de [Moonbase Alpha](/networks/testnet/).

La segunda sección ("Crear e implementar transacciones") describe las funciones necesarias para enviar la transacción en sí. Algunas de las conclusiones clave se analizan a continuación.

=== "Web3.js"
    ```
    --8<-- 'code/web3-tx-local/transaction.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-tx-local/transaction.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-tx/transaction.py'
    ```

### Web3.js {: #web3js } 

En la primera sección del [script](/snippets/code/web3-tx-local/transaction.js), la `web3` instancia (o proveedor) se crea usando el `Web3` constructor con el proveedor RPC. Al cambiar el proveedor de RPC proporcionado al constructor, puede elegir a qué red desea enviar la transacción.

La clave privada y la dirección pública asociada a ella se definen para firmar la transacción y para fines de registro, respectivamente. Solo se requiere la clave privada.

La `addressTo`, donde se enviará la transacción, también se define aquí y es obligatorio.

En la segunda sección, el objeto de transacción se crea con el `to`, `value`, y `gas` campos. Estos describen el destinatario, la cantidad a enviar y el gas consumido por la transacción (21000 en este caso). Puede usar la `web3.utils.toWei()` función para ingresar el valor en ETH (por ejemplo) y obtener la salida en WEI. La transacción se firma con la clave privada utilizando el `web3.eth.accounts.signTransaction()` método. Tenga en cuenta que esto devuelve una promesa que debe resolverse.

A continuación, con la transacción firmada (puede `console.log(createTransaction)` ver los valores de vrs), puede enviarla usando el `web3.eth.sendSignedTransaction()`método, proporcionando la transacción firmada ubicada en `createTransaction.rawTransaction`.

Por último, ejecute la función de implementación asincrónica.

### Ethers.js {: #ethersjs } 

En la primera sección del [script](/snippets/code/ethers-tx-local/transaction.js),se pueden especificar diferentes redes con un nombre, URL de RPC (obligatorio) e ID de cadena. El proveedor (similar a la `web3` instancia) se crea con el `ethers.providers.StaticJsonRpcProvider` método. Una alternativa es utilizar el `ethers.providers.JsonRpcProvide(providerRPC)` método, que solo requiere la dirección de punto final RPC del proveedor. Pero esto podría crear problemas de compatibilidad con especificaciones de proyectos individuales.

La clave privada se define para crear una instancia de billetera, que también requiere el proveedor del paso anterior. La instancia de billetera se usa para firmar transacciones.

La `addressTo`, donde se enviará la transacción, también se define aquí y es obligatorio.

En la segunda sección, una función asincrónica envuelve el `wallet.sendTransaction(txObject)` método. El objeto de transacción es bastante simple. Solo requiere la dirección del destinatario y el monto a enviar. Tenga en cuenta que `ethers.utils.parseEther()` se puede usar, que maneja las conversiones de unidades necesarias de ETH a WEI, similar a usar `ethers.utils.parseUnits(value,'ether')`.

Una vez que se envía la transacción, puede obtener la respuesta de la transacción (nombrada `createReceipt` n este ejemplo), que tiene algunas propiedades. Por ejemplo, puede llamar al `createReceipt.wait()` método para esperar hasta que se procese la transacción (el estado del recibo es correcto).

Por último, ejecute la función de implementación asincrónica.

### Web3.py {: #web3py } 

En la primera sección del [script](/snippets/code/web3py-tx/transaction.py), la `web3` instancia (o proveedor) se crea usando el `Web3(Web3.HTTPProvider(provider_rpc))` método con el proveedor RPC. Al cambiar el proveedor RPC, puede elegir a qué red desea enviar la transacción.

La clave privada y la dirección pública asociada a ella se definen para firmar la transacción y con fines de registro. No se requiere la dirección pública.

La `addressTo`, donde se enviará la transacción, también se define aquí y es obligatorio.

En la segunda sección, el objeto de transacción se crea con los `nonce`, `gasPrice`, `gas`, `to`, y `value` fields.campos. Estos describen el recuento de transacciones, el precio del gas (0 para el desarrollo y Moonbase Alpha), el gas (21000 en este caso), el destinatario y la cantidad a enviar. Tenga en cuenta que el recuento de transacciones se puede obtener con el `web3.eth.getTransactionCount(address)` método. Además, puede usar la `web3.toWei()` función para ingresar el valor en ETH (por ejemplo) y obtener la salida en WEI. La transacción se firma con la clave privada utilizando el `web3.eth.account.signTransaction()` método.

A continuación, con la transacción firmada, puede enviarla utilizando el `web3.eth.sendSignedTransaction()` método, proporcionando la transacción firmada ubicada en  `createTransaction.rawTransaction`.

## El archivo de equilibrio {: #the-balance-file } 

Antes de ejecutar el script, otro archivo verifica los saldos de ambas direcciones antes y después de que se necesite la transacción. Esto se puede hacer fácilmente con una simple consulta del saldo de una cuenta.

Puede encontrar el fragmento de código para cada biblioteca aquí (los archivos se nombraron arbitrariamente `balances.*`):

 - Web3.js: [_balances.js_](/snippets/code/web3-tx-local/balances.js)
 - Ethers.js: [_balances.js_](/snippets/code/ethers-tx-local/balances.js)
 - Web3.py: [_balances.py_](/snippets/code/web3py-tx/balances.py)

Para simplificar, el archivo de saldo se compone de dos secciones. Como antes, en la primera sección ("Definir Proveedor y Variables"), se importa la biblioteca a usar, y se definen el proveedor y la dirección desde / hacia (para verificar los saldos).

La segunda sección ("Función de llamada de saldo") describe las funciones necesarias para recuperar los saldos de las cuentas previamente definidas. Tenga en cuenta que `providerRPC` tiene el punto final RPC del nodo de desarrollo estándar y el de [Moonbase Alpha](/networks/testnet/). Algunas de las conclusiones clave se analizan a continuación.

=== "Web3.js"
    ```
    --8<-- 'code/web3-tx-local/balances.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-tx-local/balances.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-tx/balances.py'
    ```

### Web3.js {: #web3js } 

La primera sección del [script](/snippets/code/web3-tx-local/balances.js) es muy similar a la del [archivo de transacciones](/getting-started/local-node/send-transaction/#web3js). La principal diferencia es que no se necesita una clave privada porque no es necesario enviar una transacción.

En la segunda sección, una función asíncrona envuelve el método web3 usado para obtener el equilibrio de una dirección, `web3.eth.getBalance(address)`. Una vez más, puede aprovechar la `web3.utils.fromWei()` función para transformar el saldo en un número más legible en ETH.

### Ethers.js {: #ethersjs } 

La primera sección del [script](/snippets/code/ethers-tx-local/balances.js) es muy similar a la del [archivo de transacciones](/getting-started/local-node/send-transaction/#ethersjs). La principal diferencia es que no se necesita una clave privada porque no es necesario enviar una transacción. Al contrario, `addressFrom` hay que definir las necesidades.

En la segunda sección, una función asincrónica envuelve el método de proveedor utilizado para obtener el saldo de una dirección, que es `provider.getBalance(address)`. Una vez más, puede aprovechar la `ethers.utils.formatEther()`función para transformar el saldo en un número más legible en ETH.

### Web3.py {: #web3py } 

La primera sección del [script](/snippets/code/web3py-tx/balances.py) es muy similar a la del [archivo de transacciones](/getting-started/local-node/send-transaction/#web3py). La principal diferencia es que no se necesita una clave privada porque no es necesario enviar una transacción.

En la segunda sección, el `web3.eth.getBalance(address)` método se utiliza para obtener el saldo de una dirección de destino. Una vez más, puede aprovechar la `eb3.fromWei()` función para transformar el saldo en un número más legible en ETH.

## Ejecución de los scripts {: #running-the-scripts } 

Para esta sección, el código que se mostró anteriormente se adaptó para apuntar a un nodo de desarrollo, que puede ejecutar siguiendo [este tutorial](/getting-started/local-node/setting-up-a-node/). Además, cada transacción se envió desde la cuenta prefinanciada que viene con el nodo:

--8<-- 'text/metamask-local/dev-account.md'

Primero, verifique los saldos de ambas direcciones antes de la transacción ejecutando (tenga en cuenta que se cambió el nombre del directorio para cada biblioteca):

=== "Web3.js"
    ```
    node balances.js
    ```

=== "Ethers.js"
    ```
    node balances.js
    ```

=== "Web3.py"
    ```
    python3 balances.py
    ```

A continuación, ejecute el script _transaction.\*_ script Para ejecutar la transacción:

=== "Web3.js"
    ```
    node transaction.js
    ```

=== "Ethers.js"
    ```
    node transaction.js
    ```

=== "Web3.py"
    ```
    python3 transaction.py
    ```

AY, por último, vuelva a comprobar el saldo para asegurarse de que la transferencia se haya realizado correctamente. Toda la ejecución debería verse así:

=== "Web3.js"
    ![Send Tx Web3js](/images/sendtx/sendtx-web3js.png)

=== "Ethers.js"
    ![Send Tx Etherjs](/images/sendtx/sendtx-ethers.png)

=== "Web3.py"
    ![Send Tx Web3py](/images/sendtx/sendtx-web3py.png)

