---
title: Contratos Precompilados
description:  Aprenda a usar contratos precompilados en Moonbase Alpha, Moonbeam Network TestNet que es único por su completa compatibilidad con Ethereum. 
---

# Contratos precompilados en Moonbase Alpha

## Introducción {: #introduction } 

Otra característica agregada con el [lanzamiento de Moonbase Alpha v2](https://moonbeam.network/announcements/moonbase-alpha-v2-contract-events-pub-sub-capabilities/) es la inclusión de algunos [contratos precompilados](https://docs.klaytn.com/smart-contract/precompiled-contracts) que están disponibles de forma nativa en Ethereum.

Actualmente se incluyen cinco precompilaciones, que incluyen: ecrecover, sha256, ripemd-160, la función de identidad y la exponenciación modular.

En esta guía, explicaremos cómo usar y / o verificar estas precompilaciones.

## Comprobación de requisitos previos {: #checking-prerequisites } 

--8<-- 'text/common/install-nodejs.md'

En el momento de redactar esta guía, las versiones utilizadas fueron 15.2.1 y 7.0.8, respectivamente. También necesitaremos instalar el paquete Web3 ejecutando:

```
npm install --save web3
```

Para verificar la versión instalada de Web3, puede usar el `ls` comando:

```
npm ls web3
```
En el momento de redactar esta guía, la versión utilizada era 1.3.0. También usaremos [Remix](/integrations/remix/), conectándolo a Moonbase Alpha TestNet a través de [MetaMask](/integrations/wallets/metamask/).

## Verificar firmas con ECRECOVER {: #verify-signatures-with-ecrecover } 

La función principal de esta precompilación es verificar la firma de un mensaje. En términos generales, alimenta `ecrecover` los valores de la firma de la transacción y devuelve una dirección. La firma se verifica si la dirección devuelta es la misma que la dirección pública que envió la transacción.

Pasemos a un pequeño ejemplo para mostrar cómo aprovechar esta función precompilada. Para hacerlo, necesitamos recuperar los valores de la firma de la transacción (v, r, s). Por lo tanto, firmaremos y recuperaremos el mensaje firmado donde se encuentran estos valores:

```solidity
const Web3 = require('web3');

// Provider
const web3 = new Web3('https://rpc.testnet.moonbeam.network');

// Address and Private Key
const address = '0x6Be02d1d3665660d22FF9624b7BE0551ee1Ac91b';
const pk1 = '99B3C12287537E38C90A9219D4CB074A89A16E9CDB20BF85728EBD97C343E342';
const msg = web3.utils.sha3('supercalifragilisticexpialidocious');

async function signMessage(pk) {
   try {
   // Sign and get Signed Message
      const smsg = await web3.eth.accounts.sign(msg, pk);
      console.log(smsg);
   } catch (error) {
      console.error(error);
   }
}

signMessage(pk1);
```

Este código devolverá el siguiente objeto en la terminal:

```js
{
  message: '0xc2ae6711c7a897c75140343cde1cbdba96ebbd756f5914fde5c12fadf002ec97',
  messageHash: '0xc51dac836bc7841a01c4b631fa620904fc8724d7f9f1d3c420f0e02adf229d50',
  v: '0x1b',
  r: '0x44287513919034a471a7dc2b2ed121f95984ae23b20f9637ba8dff471b6719ef',
  s: '0x7d7dc30309a3baffbfd9342b97d0e804092c0aeb5821319aa732bc09146eafb4',
  signature: '0x44287513919034a471a7dc2b2ed121f95984ae23b20f9637ba8dff471b6719ef7d7dc30309a3baffbfd9342b97d0e804092c0aeb5821319aa732bc09146eafb41b'
}
```
Con los valores necesarios, podemos ir a Remix para probar el contrato precompilado. Tenga en cuenta que esto también se puede verificar con la biblioteca Web3 JS, pero en nuestro caso, iremos a Remix para asegurarnos de que está usando el contrato precompilado en la blockchain. El código de Solidez que podemos utilizar para verificar la firma es el siguiente:

```solidity
pragma solidity ^0.7.0;

contract ECRECOVER{
    address addressTest = 0x12Cb274aAD8251C875c0bf6872b67d9983E53fDd;
    bytes32 msgHash = 0xc51dac836bc7841a01c4b631fa620904fc8724d7f9f1d3c420f0e02adf229d50;
    uint8 v = 0x1b;
    bytes32 r = 0x44287513919034a471a7dc2b2ed121f95984ae23b20f9637ba8dff471b6719ef;
    bytes32 s = 0x7d7dc30309a3baffbfd9342b97d0e804092c0aeb5821319aa732bc09146eafb4;
    
    
    function verify() public view returns(bool) {
        // Use ECRECOVER to verify address
        return (ecrecover(msgHash, v, r, s) == (addressTest));
    }
}
```

Usando el [compilador y la implementación Remix](/getting-started/local-node/using-remix/) y con [MetaMask apuntando a Moonbase Alpha](/getting-started/testnet/metamask/), podemos implementar el contrato y llamar al `verify()` método que devuelve _verdadero_ si la dirección devuelta por `ecrecover` es igual a la dirección utilizada para firmar el mensaje (relacionada con la clave privada y necesita configurarse manualmente en el contrato).

## Hash con SHA256 {: #hashing-with-sha256 } 

Esta función hash devuelve el hash SHA256 de los datos proporcionados. Para probar esta precompilación, puede utilizar esta [herramienta en línea](https://md5calc.com/hash/sha256) para calcular el hash SHA256 de cualquier cadena que desee. En nuestro caso, lo haremos con `Hello World!`. Podemos dirigirnos directamente a Remix e implementar el siguiente código, donde se establece el hash calculado para la `expectedHash` variable:

```solidity
pragma solidity ^0.7.0;

contract Hash256{
    bytes32 public expectedHash = 0x7f83b1657ff1fc53b92dc18148a1d65dfc2d4b1fa3d677284addd200126d9069;

    function calculateHash() internal pure returns (bytes32) {
        string memory word = 'Hello World!';
        bytes32 hash = sha256(bytes (word));
        
        return hash;        
    }
    
    function checkHash() public view returns(bool) {
        return (calculateHash() == expectedHash);
    }
}

```
Una vez que se implementa el contrato, podemos llamar al `checkHash()` método que devuelve _verdadero_ si el hash devuelto por `calculateHash()` es igual al hash proporcionado.

## Hashing con RIPEMD-160 {: #hashing-with-ripemd160 } 

Esta función hash devuelve un hash RIPEMD-160 a partir de los datos proporcionados. Para probar esta precompilación, puede utilizar esta [herramienta en línea](https://md5calc.com/hash/ripemd160) para calcular el hash RIPEMD-160 de cualquier cadena. En nuestro caso, lo haremos de nuevo con `Hello World!`. Reutilizaremos el mismo código que antes, pero usaremos la `ripemd160` función. Tenga en cuenta que devuelve una `bytes20`variable de tipo:

```solidity
pragma solidity ^0.7.0;

contract HashRipmd160{
    bytes20 public expectedHash = hex'8476ee4631b9b30ac2754b0ee0c47e161d3f724c';

    function calculateHash() internal pure returns (bytes20) {
        string memory word = 'Hello World!';
        bytes20 hash = ripemd160(bytes (word));
        
        return hash;        
    }
    
    function checkHash() public view returns(bool) {
        return (calculateHash() == expectedHash);
    }
}
```
Con el contrato implementado, podemos llamar al `checkHash()` método que devuelve _verdadero_ si el hash devuelto por `calculateHash()` es igual al hash proporcionado.

## La función de identidad {: #the-identity-function } 

También conocida como copia de datos, esta función sirve como una forma más económica de copiar datos en la memoria. El compilador Solidity no lo admite, por lo que debe llamarse con ensamblado en línea. El [siguiente código](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x-04-datacopy-data) (adaptado a Solidity), se puede utilizar para llamar a este contrato precompilado. Podemos usar esta [herramienta en línea](https://web3-type-converter.onbrn.com/) para obtener bytes de cualquier cadena, ya que esta es la entrada del método `callDataCopy()`.

```solidity
pragma solidity ^0.7.0;

contract Identity{
    
    bytes public memoryStored;

    function callDatacopy(bytes memory data) public returns (bytes memory) {
    bytes memory result = new bytes(data.length);
    assembly {
        let len := mload(data)
        if iszero(call(gas(), 0x04, 0, add(data, 0x20), len, add(result,0x20), len)) {
            invalid()
        }
    }
    
    memoryStored = result;

    return result;
    }
}
```
Con el contrato implementado, podemos llamar al `callDataCopy()` método y verificar si `memoryStored` coincide con los bytes que pasa como entrada de la función.

## Exponenciación modular {: #modular-exponentiation } 

Esta quinta precompilación calcula el resto cuando un entero _b_ (base) se eleva a la _e_ -ésima potencia (el exponente) y se divide por un entero positivo _m_ (el módulo).

El compilador Solidity no lo admite, por lo que debe llamarse con ensamblado en línea. El [siguiente código](https://docs.klaytn.com/smart-contract/precompiled-contracts#address-0x05-bigmodexp-base-exp-mod) se simplificó para mostrar la funcionalidad de esta precompilación. 

```solidity
pragma solidity ^0.7.0;

contract ModularCheck {

    uint public checkResult;

    // Function to Verify ModExp Result
    function verify( uint _base, uint _exp, uint _modulus) public {
        checkResult = modExp(_base, _exp, _modulus);
    }

    function modExp(uint256 _b, uint256 _e, uint256 _m) public returns (uint256 result) {
        assembly {
            // Free memory pointer
            let pointer := mload(0x40)
            // Define length of base, exponent and modulus. 0x20 == 32 bytes
            mstore(pointer, 0x20)
            mstore(add(pointer, 0x20), 0x20)
            mstore(add(pointer, 0x40), 0x20)
            // Define variables base, exponent and modulus
            mstore(add(pointer, 0x60), _b)
            mstore(add(pointer, 0x80), _e)
            mstore(add(pointer, 0xa0), _m)
            // Store the result
            let value := mload(0xc0)
            // Call the precompiled contract 0x05 = bigModExp
            if iszero(call(not(0), 0x05, 0, pointer, 0xc0, value, 0x20)) {
                revert(0, 0)
            }
            result := mload(value)
        }
    }
}
```

Puedes probar esto en [Remix](/integrations/remix/).  Usa la función `verify()`,  pasando la base, el exponente y el módulo. La función almacenará el valor en la `checkResult` variable. 

