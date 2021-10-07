---
title: Moonbeam Truffle Box
description: Comience a usar Moonbeam Truffle Box para una forma rápida y preconfigurada de implementar sus contratos inteligentes Solidity en Moonbeam usando Truffle.
---
# Moonbeam Truffle Box

![Intro diagram](/images/builders/tools/truffle-banner.png)

## Introducción {: #introduction }

Como parte de un esfuerzo continuo para ayudar a los desarrolladores que quieren comenzar a trabajar en Moonbeam, hemos [lanzado la caja Moonbeam Truffle](https://moonbeam.network/announcements/moonbeam-truffle-box-available-solidity-developers/). Con él, los desarrolladores encontrarán una configuración estándar para comenzar a implementar contratos inteligentes en Moonbeam rápidamente. Con la caja Moonbeam Truffle, también hemos incorporado el complemento Moonbeam Truffle, que introduce algunos comandos para ejecutar un nodo de desarrollo en su entorno local como una imagen de Docker. Esto elimina el proceso de configuración de un nodo local (que puede demorar hasta 40 minutos cuando se construye su binario) y es una solución rápida y fácil para comenzar a desarrollar en su entorno local.

Este tutorial lo guiará a través del proceso de configuración de la caja, utilizando el complemento Moonbeam Truffle y la implementación de contratos tanto en un nodo de desarrollo Moonbeam como en Moonbase Alpha usando Truffle con la configuración base de la caja.

!!! nota
   Esta guía se basa en una instalación de Ubuntu 18.04. En el momento de escribir este artículo, las versiones de Node.js y npm utilizadas eran 15.2.1 y 7.0.8 respectivamente. Se requieren versiones de Node.js superiores a 10.23.0. También notamos un error al instalar los paquetes con la versión 7.0.15 de npm. Puede degradar npm ejecutando `npm install -g npm@version`, configurando la versión a la deseada.

## Comprobación de requisitos previos {: #checking-prerequisites } 

--8<-- 'text/common/install-nodejs.md'

En el momento de redactar esta guía, las versiones utilizadas fueron 15.2.1 y 7.0.8, respectivamente. A continuación, podemos instalar opcionalmente Truffle de forma global. Para hacerlo, puede ejecutar:


```
npm install -g truffle
```

En el momento de redactar esta guía, la versión utilizada era 5.1.51.

## Descarga y configuración de Truffle Box {: #downloading-and-setting-up-the-truffle-box } 

Para comenzar con la caja Moonbeam Truffle, si tiene Truffle instalado globalmente, puede ejecutar:

```
mkdir moonbeam-truffle-box && cd moonbeam-truffle-box
truffle unbox PureStake/moonbeam-truffle-box
```

![Unbox Moonbeam Truffle box](/images/builders/interact/truffle/truffle-1.png)

Sin embargo, la caja también tiene Truffle como dependencia en caso de que no quieras tenerlo instalado globalmente. En tal caso, puede clonar directamente el siguiente repositorio:

```
git clone https://github.com/PureStake/moonbeam-truffle-box
cd moonbeam-truffle-box
``` 

Con los archivos en su sistema local, el siguiente paso es instalar todas las dependencias ejecutando:

```
npm install
```

!!! nota
    Notamos un error al instalar los paquetes con la versión 7.0.15 de npm. Puede degradar npm ejecutando `npm install -g npm@version` y configurando la versión a la deseada. Por ejemplo, 7.0.8 o 6.14.9.

Eso concluye con todos los requisitos previos que necesita para usar la caja Moonbeam Truffle.

## Funcionalidades básicas {: #basic-functionalities } 

La caja está preconfigurada con dos redes: `dev` (para un nodo de desarrollo) y `moonbase` (Moonbeam TestNet). También se incluye, como ejemplo, un contrato de token ERC20 y un script de prueba simple. El compilador de Solidity configurado de forma predeterminada es `^0.7.0`, pero se puede cambiar según sea necesario. Si tiene experiencia con Truffle, esta configuración le resultará familiar.

```js
const HDWalletProvider = require('@truffle/hdwallet-provider');
// Moonbeam Development Node Private Key
const privateKeyDev =
   '99B3C12287537E38C90A9219D4CB074A89A16E9CDB20BF85728EBD97C343E342';
// Moonbase Alpha Private Key --> Please change this to your own Private Key with funds
// NOTE: Do not store your private key in plaintext files
//       this is only for demostration purposes only
const privateKeyMoonbase =
   'YOUR_PRIVATE_KEY_HERE_ONLY_FOR_DEMOSTRATION_PURPOSES';

module.exports = {
   networks: {
      // Standalode Network
      dev: {
         provider: () => {
            //...
            return new HDWalletProvider(
               privateKeyDev,
               'http://localhost:9933/'
            );
         },
         network_id: 1281,
      },
      // Moonbase Alpha TestNet
      moonbase: {
         provider: () => {
            //...
            return new HDWalletProvider(
               privateKeyMoonbase,
               'https://rpc.testnet.moonbeam.network'
            );
         },
         network_id: 1287,
      },
   },
   // Solidity 0.7.0 Compiler
   compilers: {
      solc: {
         version: '^0.7.0',
      },
   },
   // Moonbeam Truffle Plugin
   plugins: ['moonbeam-truffle-plugin'],
};
```

El `truffle-config.js` archivo también incluye la clave privada de la cuenta de génesis para el nodo de desarrollo. La dirección asociada con esta clave contiene todos los tokens en este entorno de desarrollo. Para implementaciones en Moonbase Alpha TestNet, debe proporcionar la clave privada de una dirección que contenga fondos. Para hacerlo, puede crear una cuenta en MetaMask, financiarla usando el [grifo TestNet](https://docs.moonbeam.network/getting-started/testnet/faucet/), y exportar su clave privada.

Al igual que con el uso de Truffle en cualquier red Ethereum, puede ejecutar los comandos normales para compilar, probar e implementar contratos inteligentes en Moonbeam. Por ejemplo, puede probar los siguientes comandos utilizando el contrato de token ERC20 incluido:

```
truffle compile # compiles the contract
truffle test #run the tests included in the test folder
truffle migrate --network network_name  #deploys to the specified network
```

Dependiendo de la red en la que desee implementar los contratos, debe sustituir network_name por dev (para apuntar al nodo de desarrollo) o moonbase (para apuntar a TestNet).

!!! nota
    Si no tiene Truffle instalado globalmente, puede usar `npx truffle` o `./node_modules/.bin/truffle` en `truffle` lugar de.

## El complemento Moonbeam Truffle {: #the-moonbeam-truffle-plugin } 

Para configurar un nodo de desarrollo Moonbeam, actualmente puede seguir [este tutorial](/getting-started/local-node/setting-up-a-node/). El proceso toma alrededor de 40 minutos en total y necesita instalar Substrate y todas sus dependencias. El complemento Moonbeam Truffle proporciona una forma de comenzar con un nodo de desarrollo mucho más rápido, y el único requisito es tener instalado Docker (en el momento de redactar este documento, la versión de Docker utilizada era la 19.03.6). Para obtener más información sobre la instalación de Docker, visite [esta página](https://docs.docker.com/get-docker/). Para descargar la imagen de Docker, ejecute la siguiente línea:

```
truffle run moonbeam install
``` 

![Install Moonbeam Truffle box](/images/legacy/trufflebox/trufflebox-01.png)

 
Luego, tiene un conjunto de comandos disponibles para controlar el nodo incluido en la imagen de Docker:
 
```
truffle run moonbeam start
truffle run moonbeam status
truffle run moonbeam pause
truffle run moonbeam unpause
truffle run moonbeam stop
truffle run moonbeam remove
```

Cada uno de los comandos mostrados anteriormente realiza la siguiente acción:

-  Inicio: inicia un nodo de desarrollo Moonbeam. Esto proporciona dos puntos finales RPC: 
      - HTTP: `http://127.0.0.1:9933` 
      - WS: `ws://127.0.0.1:9944`
-  Estado: le dice al usuario si hay un nodo de desarrollo Moonbeam en ejecución
-  Pausa: pausa el nodo de desarrollo si se está ejecutando
-  Despausar: reanuda el nodo de desarrollo si está en pausa.
-  Detener: detiene el nodo de desarrollo si se está ejecutando. Esto también elimina el contenedor Docker.
-  Eliminar: elimina la imagen de Docker purestake / moonbase

Puede ver el resultado de estos comandos en la siguiente imagen:

![Install Moonbeam Truffle box](/images/builders/interact/truffle/truffle-5.png)

Si está familiarizado con Docker, puede omitir los comandos del complemento e interactuar con la imagen de Docker directamente.

## Probando la caja Truffle de Moonbeam {: #testing-the-moonbeam-truffle-box } 

La caja tiene los requisitos mínimos para ayudarlo a comenzar. Primero compilemos los contratos ejecutando:

```
truffle compile
``` 
![Compile Contracts](/images/legacy/trufflebox/trufflebox-03.png)

Recuerde que si tiene Truffle instalado globalmente, puede omitir la parte ./node_modules/.bin/ en los comandos. Con el contrato compilado, podemos ejecutar la prueba básica incluida en el cuadro (tenga en cuenta que Ganache se usa para estas pruebas y no el nodo de desarrollo Moonbeam):

```
truffle test
```

![Test Contract Moonbeam Truffle box](/images/legacy/trufflebox/trufflebox-04.png)

Después de ejecutar el comando de instalación del complemento, que descarga la imagen de Docker del nodo de desarrollo Moonbeam, iniciemos el nodo local e implementemos el contrato de token en nuestro entorno local:

```
truffle run moonbeam start
truffle migrate --network dev
```

![Deploy on Dev Moonbeam Truffle box](/images/legacy/trufflebox/trufflebox-05.png)

Por último, podemos implementar nuestro contrato de token en Moonbase Alpha, pero primero, asegúrese de establecer una clave privada con fondos en el archivo truffle-config.js. Una vez configurada la clave privada, podemos ejecutar el comando migrate apuntando a TestNet.

```
truffle migrate --network moonbase
```

![Deploy on Moonbase Moonbeam Truffle box](/images/legacy/trufflebox/trufflebox-06.png)

Y eso es todo, ha utilizado la caja Moonbeam Truffle para implementar un contrato de token ERC20 simple tanto en su nodo de desarrollo Moonbeam como en Moonbase Alpha.
 
