---
title: Hardhat
description: Utilice Hardhat para compilar, implementar y depurar contratos inteligentes de Ethereum en Moonbeam.
---

# Construyendo con Hardhat en Moonbeam

![Hardhat Create Project](/images/hardhat/hardhat-banner.png)

## Introducción {: #introduction } 

Hardhat es un entorno de desarrollo de Ethereum que ayuda a los desarrolladores a gestionar y automatizar las tareas recurrentes inherentes a la creación de contratos inteligentes y DApps. Hardhat puede interactuar directamente con la API Ethereum de Moonbeam, por lo que también se puede utilizar para implementar contratos inteligentes en Moonbeam.

Esta guía cubrirá cómo usar Hardhat para compilar, implementar y depurar contratos inteligentes de Ethereum en Moonbase Alpha TestNet.

## Comprobación de requisitos previos {: #checking-prerequisites } 

--8<-- 'text/common/install-nodejs.md'

En el momento de redactar esta guía, las versiones utilizadas fueron 15.7.0 y 7.4.3, respectivamente.

Además, necesitará lo siguiente:

 - Tener MetaMask instalado y [conectado a Moonbase](/getting-started/testnet/metamask/)
 - Tener una cuenta con fondos, que puede obtener de [Mission Control](/getting-started/testnet/faucet/)

Una vez que se hayan cumplido todos los requisitos, estará listo para construir con Hardhat.

## Iniciar un proyecto de casco {: #starting-a-hardhat-project } 

Para iniciar un nuevo proyecto, cree un directorio para él:

```
mkdir hardhat && cd hardhat
```

Luego, inicialice el proyecto ejecutando:

```
npm init -y
```

Notará una nueva creación  `package.json`, que seguirá creciendo a medida que instale las dependencias del proyecto.

Para comenzar con Hardhat, lo instalaremos en nuestro directorio de proyecto recién creado:

```
npm install hardhat
```

Una vez instalado, ejecute:

```
npx hardhat
```

Esto creará un archivo de configuración de Hardhat (`hardhat.config.js`) en nuestro directorio de proyectos.

!!! nota
    `npx` se utiliza para ejecutar ejecutables instalados localmente en su proyecto. Aunque Hardhat se puede instalar globalmente, recomendamos instalarlo localmente en cada proyecto para que pueda controlar la versión proyecto por proyecto.

Después de ejecutar el comando, elija  `Create an empty hardhat.config.js`:

![Hardhat Create Project](/images/hardhat/hardhat-images-1.png)

## El archivo del contrato {: #the-contract-file } 

Vamos a almacenar nuestro contrato en el `contracts` directorio. Créelo:

```
mkdir contracts && cd contracts
```

El contrato inteligente que implementaremos como ejemplo se llamará Box: permitirá a las personas almacenar un valor que luego se puede recuperar.

Guardaremos este archivo como `contracts/Box.sol`:

```solidity
// contracts/Box.sol
pragma solidity ^0.8.1;

contract Box {
    uint256 private value;

    // Emitted when the stored value changes
    event ValueChanged(uint256 newValue);

    // Stores a new value in the contract
    function store(uint256 newValue) public {
        value = newValue;
        emit ValueChanged(newValue);
    }

    // Reads the last stored value
    function retrieve() public view returns (uint256) {
        return value;
    }
}
```

## Archivo de configuración de casco {: #hardhat-configuration-file } 

Modifiquemos nuestro archivo de configuración de Hardhat para que podamos compilar e implementar este contrato en Moonbase Alpha.

Si aún no lo ha hecho, cree una cuenta MetaMask, [conéctese a Moonbase Alpha](/getting-started/testnet/metamask/), y financie a través de [Mission Control](/getting-started/testnet/faucet/).Usaremos la clave privada de la cuenta creada para implementar el contrato.

Comenzamos por requerir el [complemento](https://hardhat.org/plugins/nomiclabs-hardhat-ethers.html) ethers, que trae la biblioteca [ethers.js][/integrations/ethers/] que le permite interactuar con la blockchain de una manera simple. Podemos instalar el `ethers` complemento ejecutando:

```
npm install @nomiclabs/hardhat-ethers ethers
```

NA continuación, importamos la clave privada que hemos recuperado de MetaMask y la almacenamos en un `.json` archivo.

!!! nota
    Administre siempre sus claves privadas con un administrador secreto designado o un servicio similar. Nunca guarde ni confíe sus claves privadas dentro de sus repositorios.

Dentro de `module.exports`, debemos proporcionar la versión de Solidity (de `0.8.1` acuerdo con nuestro archivo de contrato) y los detalles de la red:

 - Nombre de red: `moonbase`
 - URL: `{{ networks.moonbase.rpc_url }}`
 - ID de cadena: `{{ networks.moonbase.chain_id }}`

Si desea implementar en un nodo de desarrollo Moonbeam local, puede usar los siguientes detalles de red:

 - Nombre de red: `dev`
 - URL: `{{ networks.development.rpc_url }}`
 - ID de cadena: `{{ networks.development.chain_id }}`

El archivo de configuración de Hardhat debería verse así:

```js
// ethers plugin required to interact with the contract
require('@nomiclabs/hardhat-ethers');

// private key from the pre-funded Moonbase Alpha testing account
const { privateKey } = require('./secrets.json');

module.exports = {
  // latest Solidity version
  solidity: "0.8.1",

  networks: {
    // Moonbase Alpha network specification
    moonbase: {
      url: `{{ networks.moonbase.rpc_url }}`,
      chainId: {{ networks.moonbase.chain_id }},
      accounts: [privateKey]
    }
  }
};
```

A continuación, creemos un `secrets.json`, donde se almacena la clave privada mencionada anteriormente. Asegúrese de agregar el archivo a su proyecto `.gitignore`, y nunca revelar su clave privada. El `secrets.json` archivo debe contener una `privateKey` entrada, por ejemplo:

```js
{
    "privateKey": "YOUR-PRIVATE-KEY-HERE"
}
```

¡Felicidades! ¡Estamos listos para el despliegue!

## Compilación de solidez {: #compiling-solidity } 

Nuestro contrato `Box.sol` utiliza Solidity 0.8.1. Asegúrese de que el archivo de configuración de Hardhat esté configurado correctamente con esta versión de solidez. Si es así, podemos compilar el contrato ejecutando:

```
npx hardhat compile
```

![Hardhat Contract Compile](/images/hardhat/hardhat-images-2.png)

Después de la compilación, `artifacts` se crea un directorio: contiene el código de bytes y los metadatos del contrato, que son `.json` archivos. Es una buena idea agregar este directorio a su `.gitignore`.

## Implementar el contrato {: #deploying-the-contract } 

Para implementar el contrato inteligente de Box, necesitaremos escribir un simple `deployment script`. Primero, creemos un nuevo directorio (`scripts`). Dentro del directorio recién creado, agregue un nuevo archivo `deploy.js`.

```
mkdir scripts && cd scripts
touch deploy.js
```

A continuación, necesitamos escribir nuestro script de implementación usando `ethers`. Debido a que lo ejecutaremos con Hardhat, no necesitamos importar ninguna biblioteca. El script es una versión simplificada del utilizado en [este tutorial](/getting-started/local-node/deploy-contract/#deploying-the-contract).

Comenzamos creando una instancia local del contrato con el `getContractFactory()` método. A continuación, usemos el `deploy()` método que existe dentro de esta instancia para iniciar el contrato inteligente. Por último, esperamos su implementación utilizando `deployed()`. Una vez implementado, podemos obtener la dirección del contrato dentro de la instanciación del cuadro.

```js
// scripts/deploy.js
async function main() {
   // We get the contract to deploy
   const Box = await ethers.getContractFactory('Box');
   console.log('Deploying Box...');

   // Instantiating a new Box smart contract
   const box = await Box.deploy();

   // Waiting for the deployment to resolve
   await box.deployed();
   console.log('Box deployed to:', box.address);
}

main()
   .then(() => process.exit(0))
   .catch((error) => {
      console.error(error);
      process.exit(1);
   });
```

Usando el `run` comando, ahora podemos implementar el `Box` contrato para `Moonbase Alpha`:

```
  npx hardhat run --network moonbase scripts/deploy.js
```

!!! nota
    Para desplegar en un nodo de desarrollo del rayo de luna, reemplace `moonbase` por `dev` el `run` comando.

Después de unos segundos, el contrato se implementa y debería ver la dirección en la terminal.

![Hardhat Contract Deploy](/images/hardhat/hardhat-images-3.png)

¡Felicitaciones, su contrato está activo! Guarde la dirección, ya que la usaremos para interactuar con esta instancia de contrato en el siguiente paso.

## Interactuar con el contrato {: #interacting-with-the-contract } 

Usemos Hardhat para interactuar con nuestro contrato recién implementado en Moonbase Alpha. Para hacerlo, inicie `hardhat console` ejecutando:

```
npx hardhat console --network moonbase
```

!!! nota
    Para desplegar en un nodo de desarrollo del rayo de luna, reemplace `moonbase` por `dev` el `console` comando.

Luego, agregue las siguientes líneas de código una línea a la vez. Primero, creamos una instancia local del `Box.sol`ccontrato una vez más. No se preocupe por el `undefined` resultado que obtendrá después de que se ejecute cada línea:

```js
const Box = await ethers.getContractFactory('Box');
```

A continuación, conectemos esta instancia a una existente pasando la dirección que obtuvimos al implementar el contrato:

```js
const box = await Box.attach('0x425668350bD782D80D457d5F9bc7782A24B8c2ef');
```

Después de adjuntar el contrato, estamos listos para interactuar con él. Mientras la consola todavía está en sesión, llamemos al `store` método y almacenemos un valor simple:

```
await box.store(5)
```

La transacción será firmada por su cuenta Moonbase y transmitida a la red. La salida debería verse similar a:

![Transaction output](/images/hardhat/hardhat-images-4.png)

Observe su dirección etiquetada `from`, la dirección del contrato y la `data` ue se está pasando. Ahora, recuperemos el valor ejecutando:

```
(await box.retrieve()).toNumber()
```

Deberíamos ver `5` o el valor que ha almacenado inicialmente.

¡Felicitaciones, ha completado el tutorial de Hardhat! 🤯 🎉

Para obtener más información sobre Hardhat, complementos de hardhat y otras funciones interesantes, visite [hardhat.org](https://hardhat.org/).
