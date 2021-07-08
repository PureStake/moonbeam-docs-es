---
title: Desplegar un Contrato
description: Aprenda a desplegar contratos inteligentes basados en Solidity sin modificar y sin cambios en un nodo Moonbeam con un script simple usando Web3.js, Ethers.js o Web3.py.
---

# Uso de bibliotecas Ethereum para desplegar contratos inteligentes en Moonbeam

![Ethereum Libraries Integrations Moonbeam](/images/sendtx/web3-libraries-banner.png)

## Introducción

Esta guía describe el uso del compilador Solidity y tres bibliotecas Ethereum diferentes para firmar y enviar una transacción en Moonbeam manualmente. Las tres bibliotecas cubiertas por este tutorial son:

 - [Web3.js](https://web3js.readthedocs.io/)
 - [Ethers.js](https://docs.ethers.io/)
 - [Web3.py](https://web3py.readthedocs.io/)

Además, se utilizarán otras dos bibliotecas para compilar el contrato inteligente:

 - [Solc-js](https://www.npmjs.com/package/solc) para compilar contratos inteligentes Solidity usando JavaScript
 - [Py-solc-x](https://pypi.org/project/py-solc-x/) para compilar contratos inteligentes Solidity usando Python

!!! nota
    --8<-- 'text/common/assumes-mac-or-ubuntu-env.md'

## Comprobación de requisitos previos

Los ejemplos que utilizan web3.js y ethers.js necesitan que instales Node.js y NPM previamente. Para web3.py, necesita Python y PIP. En el momento de redactar esta guía, las versiones utilizadas fueron:

 - Node.js v15.10.0
 - NPM v7.5.3
 - Python v3.6.9 (web3 requiere Python >= 3.5.3 and < 4)
 - PIP3 v9.0.1

A continuación, cree un directorio para almacenar todos los archivos relevantes:

```
mkdir incrementer && cd incrementer/
```

Para las bibliotecas de JavaScript, primero, puede crear un `package.json` archivo simple (no es obligatorio):

```
npm init --yes
```

En el directorio, instale la biblioteca correspondiente y el compilador Solidity (_web3.py_ y _py-solc-x_ están instalados en el directorio predeterminado de PIP3):

=== "Web3.js"
    ```
    npm i web3 npm i solc@0.8.0
    ```

=== "Ethers.js"
    ```
    npm i ethers npm i solc@0.8.0
    ```

=== "Web3.py"
    ```
    pip3 install web3 pip3 install py-solc-x
    ```

Las versiones utilizadas cuando se publicó esta guía fueron

 - Web3.js v1.33 (`npm ls web3`)
 - Ethers.js v5.0.31 (`npm ls ethers`)
 - Solc (JS) v0.8.0 (`npm ls solc`)
 - Web3.py v5.17.0 (`pip3 show web3`)
 - Py-solc-x v1.1.0 (`pip3 show py-solc-x`)

La configuración de este ejemplo será relativamente simple y contendrá los siguientes archivos:

 - **_Incrementer.sol_** — el archivo con nuestro código de solidez
 - **_compile.\*_** — compila el contrato con el compilador de Solidity
 - **_deploy.\*_**: manejará la implementación en nuestro nodo Moonbeam local
 - **_get.\*_** — hará una llamada al nodo para obtener el valor actual del número
 - **_increment.\*_** —  hará una transacción para incrementar el número almacenado en el nodo Moonbeam
 - **_reset.\*_** — la función a llamar que reiniciará el número almacenado a cero

## El archivo del contrato

El contrato utilizado es un simple incrementador, arbitrariamente llamado _Incrementer.sol_, que puede encontrar [aquí](/snippets/code/web3-contract-local/Incrementer.sol). El código de Solidez es el siguiente:

```solidity
--8<-- 'code/web3-contract-local/Incrementer.sol'
```

La `constructor` función, que se ejecuta cuando se implementa el contrato, establece el valor inicial de la variable numérica almacenada en la cadena (el valor predeterminado es 0). La `increment` función agrega el `_value` proporcionado al número actual, pero es necesario enviar una transacción que modifica los datos almacenados. Por último, la `reset` función restablece el valor almacenado a cero.

!!! nota
    Este contrato es un ejemplo simple solo con fines ilustrativos y no maneja valores envueltos.

## Compilación del contrato

El único propósito del archivo de compilación es utilizar el compilador Solidity para generar el código de bytes y la interfaz (ABI) de nuestro contrato. Puede encontrar el fragmento de código para cada biblioteca aquí (se nombraron arbitrariamente `compile.*`):

 - Web3.js: [_compile.js_](/snippets/code/web3-contract-local/compile.js)
 - Ethers.js: [_compile.js_](/snippets/code/web3-contract-local/compile.js)
 - Web3.py: [_compile.py_](/snippets/code/web3py-contract/compile.py)

!!! nota
    El archivo de compilación para ambas bibliotecas de JavaScript es el mismo, ya que comparten los enlaces de JavaScript para el compilador de Solidity (mismo paquete)

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/compile.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/web3-contract-local/compile.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/compile.py'
    ```

### Web3.js and Ethers.js

En la primera parte del [the script](/snippets/code/web3-contract-local/compile.js), se busca la ruta del contrato y se lee su contenido.

A continuación, se crea el objeto de entrada del compilador Solidity y se pasa como entrada a la `solc.compile` función.

Por último, extrae los datos del `Incrementer` contrato del `Incrementer.sol` archivo y expórtalo para que el script de implementación pueda utilizarlo.

### Web3.py

En la primera parte del [the script](/snippets/code/web3py-contract/compile.py), el archivo del contrato se compila utilizando la `solcx.compile_files` función. Tenga en cuenta que el archivo del contrato está en el mismo directorio que el script de compilación.

!!! nota
    Al ejecutar el, `compile.py` es posible que obtenga un error que indique que `Solc` debe instalarse. Si es así, elimine el comentario de la línea del archivo que se ejecuta `solcx.install_solc()` y vuelva a ejecutar el archivo de compilación con `python3 compile.py`. Puede encontrar más información en [este enlace](https://pypi.org/project/py-solc-x/).

A continuación, y finalizando el script, se exportan los datos del contrato. En este ejemplo, solo se definieron la interfaz (ABI) y el código de bytes.

## Desplegar el contrato

Independientemente de la biblioteca, la estrategia para desplegar el contrato inteligente compilado es algo similar. Se crea una instancia de contrato utilizando su interfaz (ABI) y su código de bytes. Desde esta instancia, se utiliza una función de implementación para enviar una transacción firmada que implementa el contrato. Puede encontrar el fragmento de código para cada biblioteca aquí (se nombraron arbitrariamente `deploy.*`):

 - Web3.js: [_deploy.js_](/snippets/code/web3-contract-local/deploy.js)
 - Ethers.js: [_deploy.js_](/snippets/code/ethers-contract-local/deploy.js)
 - Web3.py: [_deploy.py_](/snippets/code/web3py-contract/deploy.py)

Para simplificar, el archivo de implementación se compone de dos secciones. En la primera sección ("Definir proveedor y variables"), se importan la biblioteca a utilizar y el ABI y el código de bytes del contrato. Además, se definen el proveedor y la cuenta de (con la clave privada). Tenga en cuenta que `providerRPC` tiene el punto final RPC del nodo de desarrollo estándar y el de [Moonbase Alpha](/networks/testnet/).

La segunda sección ("Desplegar contrato") describe la parte real del despliegue del contrato. Tenga en cuenta que para este ejemplo, el valor inicial de la `number` variable se estableció en 5. Algunas de las conclusiones clave se analizan a continuación.

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/deploy.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-contract-local/deploy.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/deploy.py'
    ```

!!! nota
   El script _deploy.\*_ Proporciona la dirección del contrato como salida. Esto resulta útil, ya que se utiliza para los archivos de interacción del contrato.

### Web3.js

En la primera parte del [script](/snippets/code/web3-contract-local/deploy.js), la `web3` instancia (o proveedor) se crea usando el `Web3` onstructor con el proveedor RPC. Al cambiar el proveedor de RPC proporcionado al constructor, puede elegir a qué red desea enviar la transacción.

La clave privada y la dirección pública asociada a ella se definen para firmar la transacción y con fines de registro. Solo se requiere la clave privada. Además, el código de bytes y la interfaz (ABI) del contrato se obtienen de la exportación de la compilación.

En la segunda sección, se crea una instancia de contrato proporcionando el ABI. A continuación, `deploy` se utiliza la función, que necesita el código de bytes y los argumentos de la función constructora. Esto generará el objeto de transacción del constructor.

Posteriormente, la transacción del constructor se puede firmar usando el `web3.eth.accounts.signTransaction()` método. El campo de datos corresponde al código de bytes y los argumentos de entrada del constructor se codifican juntos. Tenga en cuenta que el valor del gas se obtiene mediante la  `estimateGas()` opción dentro de la transacción del constructor.

Por último, se envía la transacción firmada y la dirección del contrato se muestra en el terminal.

### Ethers.js

En la primera parte del [script](/snippets/code/ethers-contract-local/deploy.js), se pueden especificar diferentes redes con un nombre, URL de RPC (obligatorio) e ID de cadena. El proveedor (similar a la `web3` instancia) se crea con el `ethers.providers.StaticJsonRpcProvider` método. Una alternativa es utilizar el `ethers.providers.JsonRpcProvide(providerRPC)` método, que solo requiere la dirección de punto final RPC del proveedor. Pero esto podría crear problemas de compatibilidad con especificaciones de proyectos individuales.

La clave privada se define para crear una instancia de billetera, que también requiere el proveedor del paso anterior. La instancia de billetera se usa para firmar transacciones. Además, el código de bytes y la interfaz (ABI) del contrato se obtienen de la exportación de la compilación.

En la segunda sección, se crea una instancia de contrato con `ethers.ContractFactory()`,la ABI, el código de bytes y la billetera. Por lo tanto, la instancia del contrato ya tiene un firmante. A continuación, `deploy` se utiliza la función, que necesita los argumentos de entrada del constructor. Esto enviará la transacción para la implementación del contrato. Para esperar el recibo de la transacción, puede utilizar el  `deployed()` método de la transacción de implementación del contrato.

Por último, la dirección del contrato se muestra en la terminal.

### Web3.py

En la primera parte del [script](/snippets/code/web3py-contract/deploy.py), la `web3` instancia (o proveedor) se crea usando el`Web3(Web3.HTTPProvider(provider_rpc))` método con el proveedor RPC. Al cambiar el proveedor RPC, puede elegir a qué red desea enviar la transacción.

La clave privada y la dirección pública asociada a ella se definen para firmar la transacción y establecer la dirección de remitente.

En la segunda sección, se crea una instancia de contrato con `web3.eth.contract()`, proporcionando la ABI y el código de bytes importados del archivo de compilación. A continuación, la transacción del constructor se puede construir utilizando el `constructor().buildTransaction()` método de la instancia del contrato. Tenga en cuenta que dentro de `constructor()`, debe especificar los argumentos de entrada del constructor. La `from` cuenta también debe describirse. Asegúrese de utilizar el asociado con la clave privada. Además, el recuento de transacciones se puede obtener con el `web3.eth.getTransactionCount(address)` método.

La transacción del constructor se puede firmar usando `web3.eth.account.signTransaction()`, pasando la transacción del constructor y la clave privada.

Por último, se envía la transacción firmada y la dirección del contrato se muestra en el terminal.

## Lectura del contrato (métodos de llamada)

Los métodos de llamada son el tipo de interacción que no modifica el almacenamiento del contrato (cambiar variables), lo que significa que no es necesario enviar ninguna transacción.

Repasemos el archivo _get.\*_ (El más simple de todos), que recupera el valor actual almacenado en el contrato. Puede encontrar el fragmento de código para cada biblioteca aquí (se nombraron arbitrariamente `get.*`):

 - Web3.js: [_get.js_](/snippets/code/web3-contract-local/get.js)
 - Ethers.js: [_get.js_](/snippets/code/ethers-contract-local/get.js)
 - Web3.py: [_get.py_](/snippets/code/web3py-contract/get.py)

Para simplificar, el archivo get se compone de dos secciones. En la primera sección ("Definir proveedor y variables"), se importan la biblioteca a utilizar y el ABI del contrato. Además, se define el proveedor y la dirección del contrato. Tenga en cuenta que `providerRPC` tiene el punto final RPC del nodo de desarrollo estándar y el de [Moonbase Alpha](/networks/testnet/).

La segunda sección ("Función de llamada") describe la llamada real al contrato. Independientemente de la biblioteca, se crea una instancia de contrato (vinculada a la dirección del contrato), desde la cual se consulta el método de llamada. Algunas de las conclusiones clave se analizan a continuación.

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/get.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-contract-local/get.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/get.py'
    ```

### Web3.js

En la primera parte del [script](/snippets/code/web3-contract-local/get.js), la `web3` instancia (o proveedor) se crea usando el `Web3` constructor con el proveedor RPC. Al cambiar el proveedor de RPC proporcionado al constructor, puede elegir a qué red desea enviar la transacción.

La interfaz del contrato (ABI) y la dirección también son necesarias para interactuar con él.

En la segunda sección, se crea una instancia de contrato `web3.eth.Contract()` proporcionando la ABI y la dirección. A continuación, el método para llamada se puede consultar con la  `contract.methods.methodName(_input).call()`función, en sustitución `contract`, `methodName` y `_input` con la instancia de contrato, la función de llamada y entrada de la función (si es necesario). Esta promesa, cuando se resuelva, devolverá el valor solicitado.

Por último, el valor se muestra en la terminal.

### Ethers.js

En la primera parte del [script](/snippets/code/ethers-contract-local/get.js), se pueden especificar diferentes redes con un nombre, URL de RPC (obligatorio) e ID de cadena. El proveedor (similar a la `web3` instancia) se crea con el `ethers.providers.StaticJsonRpcProvider`método. Una alternativa es utilizar el `ethers.providers.JsonRpcProvide(providerRPC)` método, que solo requiere la dirección de punto final RPC del proveedor. Pero esto podría crear problemas de compatibilidad con especificaciones de proyectos individuales.

La interfaz del contrato (ABI) y la dirección también son necesarias para interactuar con él.

En la segunda sección, se crea una instancia de contrato con `ethers.Contract()`, proporcionando su dirección, ABI y el proveedor. A continuación, el método a llamar se puede consultar con la `contract.methodName(_input)` función, reemplazando `contract` `methodName`, y `_input` con la instancia del contrato, la función a llamar y la entrada de la función (si es necesario). Esta promesa, cuando se resuelva, devolverá el valor solicitado.

Por último, el valor se muestra en la terminal.

### Web3.py

En la primera parte del [script](/snippets/code/web3py-contract/get.py), la `web3` instancia (o proveedor) se crea usando el `Web3(Web3.HTTPProvider(provider_rpc))` método con el proveedor RPC. Al cambiar el proveedor RPC, puede elegir a qué red desea enviar la transacción.

La interfaz del contrato (ABI) y la dirección también son necesarias para interactuar con él.

En la segunda sección, se crea una instancia de contrato `web3.eth.contract()` proporcionando la ABI y la dirección. A continuación, el método para llamada se puede consultar con la  `contract.functions.method_name(input).call()` función, en sustitución `contract`, `method_name` y `input` con la instancia de contrato, la función de llamada y entrada de la función (si es necesario). Esto devuelve el valor solicitado.

Por último, el valor se muestra en la terminal.

## Interacción con el contrato (métodos de envío)

Los métodos de envío son el tipo de interacción que modifica el almacenamiento del contrato (cambiar variables), lo que significa que una transacción debe firmarse y enviarse.

Primero, repasemos el archivo _increment.\*_ Que incrementa el número actual almacenado en el contrato por un valor dado. Puede encontrar el fragmento de código para cada biblioteca aquí (se nombraron arbitrariamente `increment.*`):

 - Web3.js: [_increment.js_](/snippets/code/web3-contract-local/increment.js)
 - Ethers.js: [_increment.js_](/snippets/code/ethers-contract-local/increment.js)
 - Web3.py: [_increment.py_](/snippets/code/web3py-contract/increment.py)

Para simplificar, el archivo de incremento se compone de dos secciones. En la primera sección ("Definir proveedor y variables"), se importan la biblioteca a utilizar y el ABI del contrato. `increment` También se definen el proveedor, la dirección del contrato y el valor de la función. Tenga en cuenta que `providerRPC` tiene el punto final RPC del nodo de desarrollo estándar y el de [Moonbase Alpha](/networks/testnet/).

La segunda sección ("Función de envío") describe la función real que se llamará con la transacción. Independientemente de la biblioteca, se crea una instancia de contrato (vinculada a la dirección del contrato), desde la cual se consulta la función a utilizar.

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/increment.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-contract-local/increment.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/increment.py'
    ```

El segundo archivo que interactúa con el contrato es el archivo _reset.\*_ file, Que pone a cero el número almacenado en el contrato. Puede encontrar el fragmento de código para cada biblioteca aquí (se nombraron arbitrariamente `reset.*`):

 - Web3.js: [_reset.js_](/snippets/code/web3-contract-local/reset.js)
 - Ethers.js: [_reset.js_](/snippets/code/ethers-contract-local/reset.js)
 - Web3.py: [_reset.py_](/snippets/code/web3py-contract/reset.py)

La estructura de cada archivo es muy similar a su contraparte _increment.\*_ Para cada biblioteca. La principal diferencia es el método que se llama.

=== "Web3.js"
    ```
    --8<-- 'code/web3-contract-local/reset.js'
    ```

=== "Ethers.js"
    ```
    --8<-- 'code/ethers-contract-local/reset.js'
    ```

=== "Web3.py"
    ```
    --8<-- 'code/web3py-contract/reset.py'
    ```

### Web3.js

En la primera parte del script ([incrementar](/snippets/code/web3-contract-local/increment.js) o [restablecer](/snippets/code/web3-contract-local/reset.js) archivos), la `web3` instancia (o proveedor) se crea usando el `Web3` constructor con el proveedor RPC. Al cambiar el proveedor de RPC proporcionado al constructor, puede elegir a qué red desea enviar la transacción.

La clave privada y la dirección pública asociada a ella se definen para firmar la transacción y con fines de registro. Solo se requiere la clave privada. Además, la interfaz del contrato (ABI) y la dirección son necesarias para interactuar con él. Si es necesario, puede definir cualquier variable requerida como entrada a la función con la que va a interactuar.

En la segunda sección, se crea una instancia de contrato `web3.eth.Contract()` proporcionando la ABI y la dirección. A continuación, se puede construir el objeto transacción con la  `contract.methods.methodName(_input)` función, en sustitución `contract`, `methodName` y `_input` on la instancia contrato, función a llamar, y la entrada de la función (si es necesario).

Posteriormente, la transacción se puede firmar mediante el `web3.eth.accounts.signTransaction()` método. El campo de datos corresponde al objeto de transacción del paso anterior. Tenga en cuenta que el valor del gas se obtiene mediante la `estimateGas()` opción dentro del objeto de transacción.

Por último, se envía la transacción firmada y el hash de la transacción se muestra en el terminal.

### Ethers.js

En la primera parte del script ([incrementar](/snippets/code/ethers-contract-local/increment.js) o [ restablecer](/snippets/code/ethers-contract-local/reset.js) archivos), se pueden especificar diferentes redes con un nombre, URL RPC (obligatorio) e ID de cadena. El proveedor (similar a la `web3` instancia) se crea con el`ethers.providers.StaticJsonRpcProvider` método. Una alternativa es utilizar el `ethers.providers.JsonRpcProvide(providerRPC)` método, que solo requiere la dirección de punto final RPC del proveedor. Pero esto podría crear problemas de compatibilidad con especificaciones de proyectos individuales.

La clave privada se define para crear una instancia de billetera, que también requiere el proveedor del paso anterior. La instancia de billetera se usa para firmar transacciones. Además, la interfaz del contrato (ABI) y la dirección son necesarias para interactuar con él. Si es necesario, puede definir cualquier variable requerida como entrada a la función con la que va a interactuar.

En la segunda sección, se crea una instancia de contrato con `ethers.Contract()`, proporcionando su dirección, ABI y billetera. Por lo tanto, la instancia del contrato ya tiene un firmante. A continuación, la transacción correspondiente a una función específica se puede enviar con la `contract.methodName(_input)` función, en sustitución `contract`, `methodName` y `_input` con la instancia de contrato, la función de llamada y entrada de la función (si es necesario). Para esperar el recibo de una transacción, puede utilizar el `wait()` método de la transacción de implementación del contrato.

Por último, el hash de la transacción se muestra en la terminal.

### Web3.py

En la primera parte del script ([incrementar](/snippets/code/web3py-contract/increment.py) o [restablecer](/snippets/code/web3py-contract/reset.py) archivos), la `web3` instancia (o proveedor) se crea usando el `Web3(Web3.HTTPProvider(provider_rpc))` método con el proveedor RPC. Al cambiar el proveedor RPC, puede elegir a qué red desea enviar la transacción.

La clave privada y la dirección pública asociada a ella se definen para firmar la transacción y establecer la dirección de remitente. Además, la interfaz del contrato (ABI) y la dirección también son necesarias para interactuar con él.

En la segunda sección, se crea una instancia de contrato `web3.eth.contract()` proporcionando la ABI y la dirección. A continuación, se puede construir el objeto transacción con la `contract.functions.methodName(_input).buildTransaction` función, en sustitución `contract`, `methodName` y `_input` con la instancia contrato, función a llamar, y la entrada de la función (si es necesario). En `buildTransaction()`, el interior , la `from` cuenta debe describirse. Asegúrese de utilizar el asociado con la clave privada. Además, el recuento de transacciones se puede obtener con el  `web3.eth.getTransactionCount(address)` método.

La transacción se puede firmar utilizando `web3.eth.account.signTransaction()`, pasando el objeto de transacción del paso anterior y la clave privada.

Por último, el hash de la transacción se muestra en la terminal.

## Ejecución de los scripts

Para esta sección, el código que se mostró anteriormente se adaptó para apuntar a un nodo de desarrollo, que puede ejecutar siguiendo [este tutorial](/getting-started/local-node/setting-up-a-node/). Además, cada transacción se envió desde la cuenta prefinanciada que viene con el nodo:

--8<-- 'text/metamask-local/dev-account.md'

Primero, implemente el contrato ejecutando (tenga en cuenta que se cambió el nombre del directorio para cada biblioteca):

=== "Web3.js"
    ```
    node deploy.js
    ```

=== "Ethers.js"
    ```
    node deploy.js
    ```

=== "Web3.py"
    ```
    python3 deploy.py
    ```

Esto desplegará el contrato y devolverá la dirección:

=== "Web3.js"
    ![Deploy Contract Web3js](/images/deploycontract/contract-deploy-web3js.png)

=== "Ethers.js"
    ![Deploy Contract Etherjs](/images/deploycontract/contract-deploy-ethers.png)

=== "Web3.py"
    ![Deploy Contract Web3py](/images/deploycontract/contract-deploy-web3py.png)

A continuación, ejecute el archivo de incremento. Puede usar el archivo get para verificar el valor del número almacenado en el contrato antes y después de incrementarlo:

=== "Web3.js"
    ```
    # Get value
    node get.js 
    # Increment value
    increment.js
    # Get value
    node get.js
    ```

=== "Ethers.js"
    ```
    # Get value
    node get.js 
    # Increment value
    increment.js
    # Get value
    node get.js
    ```

=== "Web3.py"
    ```
    # Get value
    python3 get.py 
    # Increment value
    python3 increment.py
    # Get value
    python3 get.py
    ```

Esto mostrará el valor antes de la transacción de incremento, el hash de la transacción y el valor después de:

=== "Web3.js"
    ![Increment Contract Web3js](/images/deploycontract/contract-increment-web3js.png)

=== "Ethers.js"
    ![Increment Contract Etherjs](/images/deploycontract/contract-increment-ethers.png)

=== "Web3.py"
    ![Increment Contract Web3py](/images/deploycontract/contract-increment-web3py.png)

Por último, ejecute el archivo de reinicio. Una vez más, puede usar el archivo get para verificar el valor del número almacenado en el contrato antes y después de restablecerlo:

=== "Web3.js"
    ```
    # Get value
    node get.js 
    # Reset value
    node reset.js 
    # Get value
    node get.js
    ```

=== "Ethers.js"
    ```
    # Get value
    node get.js 
    # Reset value
    node reset.js 
    # Get value
    node get.js
    ```

=== "Web3.py"
    ```
    # Get value
    python3 get.py 
    # Reset value
    python3 reset.py
    # Get value
    python3 get.py
    ```

Esto mostrará el valor antes de la transacción de reinicio, el hash de la transacción y el valor después de:

=== "Web3.js"
    ![Reset Contract Web3js](/images/deploycontract/contract-reset-web3js.png)

=== "Ethers.js"
    ![Reset Contract Etherjs](/images/deploycontract/contract-reset-ethers.png)

=== "Web3.py"
    ![Reset Contract Web3py](/images/deploycontract/contract-reset-web3py.png)

