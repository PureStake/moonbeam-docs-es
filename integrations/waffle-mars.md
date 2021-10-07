---
title: Waffle & Mars
description: Aprenda a usar Waffle y Mars para escribir, compilar, probar e implementar contratos inteligentes de Ethereum en Moonbeam.
---

# Usando Waffle & Mars en Moonbeam

![Waffle and Mars on Moonbeam](/images/builders/interact/waffle-mars/waffle-mars-banner.png)

## Introducción {: #introduction } 

[Waffle](https://getwaffle.io/)  es una biblioteca para compilar y probar contratos inteligentes, y [Mars](https://github.com/EthWorks/Mars) es un administrador de implementación. Juntos, Waffle y Mars se pueden usar para escribir, compilar, probar e implementar contratos inteligentes de Ethereum. Dado que Moonbeam es compatible con Ethereum, Waffle y Mars se pueden usar para implementar contratos inteligentes en un nodo de desarrollo Moonbeam o Moonbase Alpha TestNet.

Waffle utiliza dependencias mínimas, tiene una sintaxis que es fácil de aprender y ampliar, y proporciona tiempos de ejecución rápidos al compilar y probar contratos inteligentes. Además, es compatible con [TypeScript](https://www.typescriptlang.org/) y utiliza [Chai matchers](https://ethereum-waffle.readthedocs.io/en/latest/matchers.html) para que las pruebas sean fáciles de leer y escribir.

Mars proporciona un marco simple y compatible con TypeScript para crear scripts de implementación avanzados y mantenerse sincronizado con los cambios de estado. Mars se centra en la infraestructura como código, lo que permite a los desarrolladores especificar cómo se deben implementar sus contratos inteligentes y luego usar esas especificaciones para manejar automáticamente los cambios de estado y las implementaciones.

En esta guía, creará un proyecto de TypeScript para escribir, compilar y probar un contrato inteligente usando Waffle, luego implementarlo en Moonbase Alpha TestNet usando Mars.

## Comprobación de requisitos previos  {: #checking-prerequisites } 

--8<-- 'text/common/install-nodejs.md'

En el momento de redactar esta guía, las versiones utilizadas fueron 15.12.0 and 7.6.3, respectivamente.

Waffle y Mars se pueden usar con un nodo de desarrollo Moonbeam que se ejecute localmente, pero para los fines de esta guía, se implementará en Moonbase Alpha. Por lo tanto, necesitará una cuenta financiada para el desarrollo. Puede optar por [crear una cuenta con MetaMask](/getting-started/testnet/metamask/#creating-a-wallet) o [crear una cuenta con PolkadotJS Apps](/integrations/wallets/polkadotjs/#creating-or-importing-an-h160-account). 

Una vez que haya creado una cuenta, deberá exportar la clave privada que se utilizará en esta guía. Antes de continuar, asegúrese de que su cuenta tenga fondos y, si es necesario, obtenga `DEV` tokens del [faucet](/getting-started/testnet/faucet/).

## Cree un proyecto de TypeScript con Waffle & Mars {: #create-a-typescript-project-with-waffle-mars } 

TPara comenzar, creará un proyecto de TypeScript e instalará y configurará algunas

1.  Cree el directorio del proyecto y cámbielo a él:
```
mkdir waffle-mars && cd waffle-mars
```

2.  Inicialice el proyecto. Lo que creará un `package.json` en el directorio:
```
npm init -y
```

3.  Instale las siguientes dependencias:
```
npm install ethereum-waffle ethereum-mars ethers \
@openzeppelin/contracts typescript ts-node chai \
@types/chai mocha @types/mocha
```
    - [Waffle](https://github.com/EthWorks/Waffle) - for writing, compiling, and testing smart contracts
    - [Mars](https://github.com/EthWorks/Mars) - for deploying smart contracts to Moonbeam
    - [Ethers](https://github.com/ethers-io/ethers.js/) - for interacting with Moonbeam's Ethereum API
    - [OpenZeppelin Contracts](https://github.com/OpenZeppelin/openzeppelin-contracts) - the contract you'll be creating will use OpenZeppelin's ERC20 base implementation
    - [TypeScript](https://github.com/microsoft/TypeScript) - the project will be a TypeScript project
    - [TS Node](https://github.com/TypeStrong/ts-node) - for executing the deployment script you'll create later in this guide
    - [Chai](https://github.com/chaijs/chai) - an assertion library used alongside Waffle for writing tests
    - [@types/chai](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/HEAD/types/chai) - contains the type definitions for chai
    - [Mocha](https://github.com/mochajs/mocha) - a testing framework for writing tests alongside Waffle
    - [@types/mocha](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/HEAD/types/mocha) - contains the type definitions for mocha

4.  Cree un archivo de configuración de [TypeScript](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html):
```
touch tsconfig.json
```

5.  Agregue una configuración básica de TypeScript:
```
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2019",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "module": "CommonJS",
    "composite": true,
    "sourceMap": true,
    "declaration": true,
    "noEmit": true
  }
}
```

Ahora, debe tener un proyecto básico de TypeScript con las dependencias necesarias para comenzar a construir con Waffle y Mars.

## Agregar un contrato {: #add-a-contract } 

Para esta guía, creará un contrato ERC-20 que acuña una cantidad específica de tokens al creador del contrato. Está basado en la plantilla Open Zeppelin ERC-20.

1. Cree un directorio para almacenar sus contratos y un archivo para el contrato inteligente:
```
mkdir contracts && cd contracts && touch MyToken.sol
```

2. Agregue el siguiente contrato a MyToken.sol:
```
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor() ERC20("MyToken", "MYTOK") {}

    function initialize(uint initialSupply) public {
      _mint(msg.sender, initialSupply);
    }
}
```

En este contrato, está creando un token ERC20 llamado MyToken con el símbolo MYTOK, que le permite, como creador del contrato, acuñar tantos MYTOK como desee.

## Utilice Waffle para compilar y probar {: #use-waffle-to-compile-and-test } 

### Compilar con Waffle {: #compile-with-waffle } 

Ahora que ha escrito un contrato inteligente, el siguiente paso es usar Waffle para compilarlo. Antes de sumergirse en la compilación de su contrato, deberá configurar Waffle.

1. Regrese al directorio raíz del proyecto y cree un archivo `waffle.json` para configurar Waffle:

```
cd .. && touch waffle.json
```

2. Edite `waffle.json` para especificar las configuraciones del compilador, el directorio que contiene sus contratos y más. Para este ejemplo, usaremos `solcjs` y la versión de Solidity que usó para el contrato, que es `0.8.0`:

```json
{
  "compilerType": "solcjs", // Specifies compiler to use
  "compilerVersion": "0.8.0", // Specifies version of the compiler
  "compilerOptions": {
    "optimizer": { // Optional optimizer settings
      "enabled": true, // Enable optimizer
      "runs": 20000 // Optimize how many times you want to run the code
    }
  },
  "sourceDirectory": "./contracts", // Path to directory containing smart contracts
  "outputDirectory": "./build", // Path to directory where Waffle saves compiler output
  "typechainEnabled": true // Enable typed artifact generation
}
```

3. Agregue un script para ejecutar Waffle en `package.json`:
```json
"scripts": {
  "build": "waffle"
},
```

Eso es todo lo que necesita hacer para configurar Waffle, ahora está todo listo para compilar el contrato `MyToken` usando el `build` script:

```
npm run build
```

![Waffle compiler output](/images/builders/interact/waffle-mars/waffle-mars-1.png)

Después de compilar sus contratos, Waffle almacena la salida JSON en el directorio de `build`. El contrato de esta guía se basa en la plantilla ERC-20 de Open Zeppelin, por lo que los archivos JSON ERC-20 relevantes también aparecerán en el `build` de compilación. 

### Prueba con Waffle {: #test-with-waffle } 

Antes de implementar su contrato y enviarlo a la naturaleza, primero debe probarlo. Waffle proporciona un marco de prueba avanzado y tiene muchas herramientas para ayudarlo con las pruebas.

Realizará pruebas contra Moonbase Alpha TestNet y necesitará la URL de RPC correspondiente para conectarse a él: `https://rpc.testnet.moonbeam.network`. Dado que ejecutará pruebas en TestNet, es posible que lleve un par de minutos ejecutar todas las pruebas. Si desea una experiencia de prueba más eficiente, puede activar un [nodo de desarrollo Moonbeam](/getting-started/local-node/setting-up-a-node/) utilizando el [`instant seal`](/getting-started/local-node/setting-up-a-node/#node-options). Ejecutar un nodo de desarrollo Moonbeam local con la función de `instant seal` es similar a la experiencia rápida e iterativa que obtendría con [Ganache](https://www.trufflesuite.com/ganache).

1. Cree un directorio para contener sus pruebas y un archivo para probar su contrato `MyToken`:
```
mkdir test && cd test && touch MyToken.test.ts
```

2. Abra el archivo `MyToken.test.ts` y configure su archivo de prueba para usar el complemento Solidity de Waffle y use el proveedor JSON-RPC personalizado de Ethers para conectarse a Moonbase Alpha:

```typescript
import { use, expect } from 'chai';
import { Provider } from '@ethersproject/providers';
import { solidity } from 'ethereum-waffle';
import { ethers, Wallet } from 'ethers';
import { MyToken, MyTokenFactory } from '../build/types';

// Tell Chai to use Waffle's Solidity plugin
use(solidity);

describe ('MyToken', () => {
  // Use custom provider to connect to Moonbase Alpha
  let provider: Provider = new ethers.providers.JsonRpcProvider('https://rpc.testnet.moonbeam.network');
  let wallet: Wallet;
  let walletTo: Wallet;
  let token: MyToken;

  beforeEach(async () => {
    // Logic for setting up the wallet and deploying MyToken will go here
  });

  // Tests will go here
})
```

3. Antes de que se ejecute cada prueba, querrá crear billeteras y conectarlas al proveedor, usar las billeteras para implementar una instancia del contrato `MyToken` y luego llamar a la función de `initialize` una vez con un suministro inicial de 10 tokens:

```typescript
  beforeEach(async () => {
    const PRIVATE_KEY = '<insert-your-private-key-here>'
    // Create a wallet instance using your private key & connect it to the provider
    wallet = new Wallet(PRIVATE_KEY).connect(provider);

    // Create a random account to transfer tokens to & connect it to the provider
    walletTo = Wallet.createRandom().connect(provider);

    // Use your wallet to deploy the MyToken contract
    token = await new MyTokenFactory(wallet).deploy();

    // Mint 10 tokens to the contract owner, which is you
    let contractTransaction = await token.initialize(10);

    // Wait until the transaction is confirmed before running tests
    await contractTransaction.wait();
  });
```

4. Ahora puedes crear tu primera prueba. La primera prueba verificará su saldo inicial para asegurarse de que recibió el suministro inicial de 10 tokens. Sin embargo, para seguir las buenas prácticas de prueba, escriba primero una prueba fallida:

```typescript
it('Mints the correct initial balance', async () => {
  expect(await token.balanceOf(wallet.address)).to.equal(1); // This should fail
});
```

5. Antes de que pueda ejecutar su primera prueba, deberá volver a la dirección raíz y agregar un archivo de configuración Mocha  `.mocharc.json`:

```
cd .. && touch .mocharc.json
```

6. Ahora edite el archivo `.mocharc.json` para configurar Mocha:

```json
{
  "require": "ts-node/register/transpile-only", // Use ts-node to transpile the code for tests
  "timeout": 600000, // Set timeout to 10 minutes
  "extension": "test.ts" // Specify extension for test files
}
```

7. También deberá agregar un script en `package.json` para ejecutar sus pruebas:

```json
"scripts": {
  "build": "waffle",
  "test": "mocha",
},
```

8. Ya está todo listo para ejecutar las pruebas, simplemente use el script `test` que acaba de crear y ejecutar:

```
npm run test
```
Tenga en cuenta que el proceso podría demorar unos minutos porque las pruebas se están ejecutando contra Moonbase Alpha, pero si todo funcionó como se esperaba, debería tener una prueba fallida.

9. A continuación, puede volver atrás y editar la prueba para verificar si hay 10 tokens:

```typescript
it('Mints the correct initial balance', async () => {
  expect(await token.balanceOf(wallet.address)).to.equal(10); // This should pass
});
```
10. Si vuelve a ejecutar las pruebas, ahora debería ver una prueba que pasa:

```
npm run test
```

11. Ha probado la capacidad de acuñar tokens; a continuación, probará la capacidad de transferir los tokens acuñados. Si desea escribir una prueba fallida primero nuevamente, eso está a la altura, sin embargo, la prueba final debería verse así:

```typescript
it('Should transfer the correct amount of tokens to the destination account', async () => {
  // Send the destination wallet 7 tokens
  await (await token.transfer(walletTo.address, 7)).wait();

  // Expect the destination wallet to have received the 7 tokens
  expect(await token.balanceOf(walletTo.address)).to.equal(7);
});
```

¡Felicitaciones, ahora debería tener dos exámenes aprobados! En total, su archivo de prueba debería verse así:

```typescript
import { use, expect } from 'chai';
import { Provider } from '@ethersproject/providers';
import { solidity } from 'ethereum-waffle';
import { ethers, Wallet } from 'ethers';
import { MyToken, MyTokenFactory } from '../build/types';

use(solidity);

describe ('MyToken', () => {
  let provider: Provider = new ethers.providers.JsonRpcProvider('https://rpc.testnet.moonbeam.network');
  let wallet: Wallet;
  let walletTo: Wallet;
  let token: MyToken;

  beforeEach(async () => {
    const PRIVATE_KEY = '<insert-your-private-key-here>'
    wallet = new Wallet(PRIVATE_KEY).connect(provider);
    walletTo = Wallet.createRandom().connect(provider);
    token = await new MyTokenFactory(wallet).deploy();
    let contractTransaction = await token.initialize(10);
    await contractTransaction.wait();
  });

  it('Mints the correct initial balance', async () => {
    expect(await token.balanceOf(wallet.address)).to.equal(10);
  });

  it('Should transfer the correct amount of tokens to the destination account', async () => {
    await (await token.transfer(walletTo.address, 7)).wait();
    expect(await token.balanceOf(walletTo.address)).to.equal(7);
  });
})
```

Si desea escribir más pruebas por su cuenta, podría considerar probar transferencias desde cuentas sin fondos o transferencias desde cuentas sin fondos suficientes.

## Use Mars para implementar en Moonbase Alpha {: #use-mars-to-deploy-to-moonbase-alpha } 

Después de compilar sus contratos y antes de la implementación, tendrá que generar artefactos de contrato para Mars. Mars usa los artefactos de contrato para verificaciones de tipo en implementaciones. Luego, deberá crear un script de implementación e implementar el contrato inteligente `MyToken`.

Recuerde, realizará la implementación en Moonbase Alpha y deberá utilizar las configuraciones de TestNet:

--8<-- 'text/testnet/testnet-details.md'

La implementación se dividirá en tres secciones: [generar artefactos](#generar-artefactos), [crear-una secuencia-de-comandos-de-implementacion](#crear-una-secuencia-de-comandos-de-implementación), y [implementar con Mars](#desplegar-con-mars). 

### Generar artefactos {: #generate-artifacts } 

Los artefactos deben generarse para Mars para que las comprobaciones de tipo estén habilitadas dentro de los scripts de implementación.

1. Actualice el script existente para ejecutar Waffle en `package.json` para incluir Mars:

```json
"scripts": {
  "build": "waffle && mars",
  "test": "mocha",
},
```

2. Genere los artefactos y cree el archivo `artifacts.ts` necesario para las implementaciones:

```
npm run build
```
![Salida del compilador Waffle y Mars](/images/builders/interact/waffle-mars/waffle-mars-2.png)

Si abre el directorio `build` ahora debería ver un archivo `artifacts.ts` que contiene los datos de artefactos necesarios para las implementaciones. Para continuar con el proceso de implementación, deberá escribir un script de implementación. El script de implementación se utilizará para indicarle a Mars qué contrato implementar, en qué red y qué cuenta se utilizará para activar la implementación.

### Crear una secuencia de comandos de implementación {: #create-a-deployment-script } 

Ahora debe configurar la implementación del contrato `MyToken` en Moonbase Alpha TestNet.

En este paso, creará el script de implementación que definirá cómo se debe implementar el contrato. Mars ofrece una función de `deploy` a la que puede pasar opciones, como la clave privada de la cuenta para implementar el contrato, la red para implementar y más. Dentro de la función de `deploy` es donde se definen los contratos a implementar. Mars tiene una función de `contract` que acepta el `name`, `artifact`, y el `constructorArgs`. Esta función se utilizará para implementar el contrato `MyToken` con un suministro inicial de 10 MYTOK.


1. Cree un directorio `src` para contener sus scripts de implementación y cree el script para implementar el contrato `MyToken`:
```
mkdir src && cd src && touch deploy.ts
```

2. En `deploy.ts`, use la función de `deploy` de Mars para crear un script para implementar en Moonbase Alpha usando la clave privada de su cuenta:
```javascript
import { deploy } from 'ethereum-mars';

const privateKey = "<insert-your-private-key-here>";
deploy({network: 'https://rpc.testnet.moonbeam.network', privateKey},(deployer) => {
  // Deployment logic will go here
});
```

3. Configure la función de  `deploy` para desplegar el contrato `MyToken` creado en los pasos anteriores:
```javascript
import { deploy, contract } from 'ethereum-mars';
import { MyToken } from '../build/artifacts';

const privateKey = "<insert-your-private-key-here>";
deploy({network: 'https://rpc.testnet.moonbeam.network', privateKey}, () => {
  contract('myToken', MyToken, [10]);
});
```

4. Agregue un script de scripts al objeto de `scripts` en `package.json`:
```json
  "scripts": {
    "build": "waffle && mars",
    "test": "mocha",
    "deploy": "ts-node src/deploy.ts"
  },
```

Hasta ahora, debería haber creado un script de implementación en `deploy.ts` que desplegará el contrato `MyToken` en Moonbase Alpha, y agregó la capacidad de llamar fácilmente al script e implementar el contrato.

### Desplegar con Mars {: #deploy-with-mars } 

Ha configurado la implementación, ahora es el momento de implementar en Moonbase Alpha.

1. Despliegue el contrato con el script que acaba de crear:
```
npm run deploy
```

2. En su Terminal, Mars le pedirá que presione `ENTER` para enviar su transacción:
![Mars confirm deployment](/images/builders/interact/waffle-mars/waffle-mars-3.png)

Si tiene éxito, debería ver los detalles sobre su transacción, incluido su hash, el bloque en el que se incluyó y su dirección.

![Mars deployment output](/images/builders/interact/waffle-mars/waffle-mars-4.png)

¡Felicidades! ¡Has implementado un contrato en Moonbase Alpha usando Waffle y Mars!

## Proyecto de ejemplo {: #example-project } 

Si desea ver un ejemplo completo de un proyecto de Waffle y Marte en Moonbeam, consulte el ejemplo de [moonbeam-waffle-mars-example](https://github.com/EthWorks/moonbeam-waffle-mars-example) creado por el equipo detrás de Waffle y Marte, EthWorks.
