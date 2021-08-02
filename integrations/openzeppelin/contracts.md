---
title: Contratos y Bibliotecas
description:  Aprenda cómo crear contratos comunes de OpenZeppelin con su Contracts Wizard fácilmente y cómo desplegarlos en Moonbeam gracias a sus características de compatibilidad con Ethereum compatibility features
---

# Contratos y bibliotecas de OpenZeppelin

![OpenZeppelin Contracts Banner](/images/openzeppelin/ozcontracts-banner.png)

## Introducción {: #introduction } 

Los contratos y las bibliotecas de OpenZeppelin se han convertido en un estándar en la industria. Ayudan a los desarrolladores a minimizar el riesgo, ya que sus plantillas de código de fuente abierta se prueban en batalla para Ethereum y otras cadenas de bloques. Su código incluye los despliegues más utilizados de los estándares y complementos de ERC y, a menudo, aparece en guías y tutoriales de la comunidad.

Debido a que Moonbeam es totalmente compatible con Ethereum, todos los contratos y bibliotecas de OpenZeppelin se pueden desplegar sin ningún cambio.

Esta guía se divide en dos secciones. La primera parte describe los Contracts Wizard de OpenZeppelin, una excelente herramienta en línea para ayudarlo a crear contratos inteligentes utilizando el código de OpenZeppelin. La segunda sección proporciona una guía paso a paso sobre cómo implementar estos contratos en Moonbeam.

## OpenZeppelin Contract Wizard {: #openzeppelin-contract-wizard } 

OpenZeppelin ha desarrollado una herramienta generadora de contratos interactiva en línea basada en la web que probablemente sea la forma más fácil y rápida de escribir su contrato inteligente utilizando el código OpenZeppelin. La herramienta se llama Contract Wizard y puede encontrarla en su [sitio de documentación](https://docs.openzeppelin.com/contracts/4.x/wizard).

Actualmente, el Contract Wizard admite los siguientes estándares de ERC:

 - [**ERC20**](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) — un estándar de token fungible que sigue a [EIP-20](https://eips.ethereum.org/EIPS/eip-20). Fungible significa que todos los tokens son equivalentes e intercambiables, es decir, de igual valor. Un ejemplo típico de tokens fungibles son las monedas fiduciarias, donde cada billete de igual denominación tiene el mismo valor.
 - [**ERC721**](https://ethereum.org/en/developers/docs/standards/tokens/erc-721/) —  un contrato de token no fungible que sigue a [EIP-721](https://eips.ethereum.org/EIPS/eip-721). No fungible significa que cada token es diferente y, por lo tanto, único. Un token ERC721 puede representar la propiedad de ese artículo único, ya sea un artículo coleccionable en un juego, un estado real, etc.
 - [**ERC1155**](https://docs.openzeppelin.com/contracts/4.x/erc1155) — también conocido como contrato de múltiples tokens, porque puede representar tokens tanto fungibles como no fungibles en un solo contrato inteligente. Sigue [EIP-1155](https://eips.ethereum.org/EIPS/eip-1155)

El asistente consta de las siguientes secciones:

 1. **Selección de estándar de token** — muestra todos los diferentes estándares admitidos por el asistente
 2. **Configuración** — proporciona la configuración de referencia para cada estándar de token, como el nombre del token, el símbolo, la pre-acuñación (suministro de token cuando se despliega el contrato) y la URI (para tokens no fungibles).
 3. **Funciones** —  lista de todas las funciones disponibles para cada estándar de token. Puede encontrar más información sobre las diferentes funciones en los siguientes enlaces:
     - [ERC20](https://docs.openzeppelin.com/contracts/4.x/api/token/erc20)
     - [ERC721](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721)
     - [ERC1155](https://docs.openzeppelin.com/contracts/4.x/api/token/erc1155)
 4. **Control de acceso** —  lista de todos los [mecanismos de control de acceso](https://docs.openzeppelin.com/contracts/4.x/access-control) disponibles para cada estándar de token
 5. **Visualización de código interactivo** — muestra el código de contrato inteligente con la configuración establecida por el usuario

![OpenZeppelin Contracts Wizard](/images/openzeppelin/ozwizard-images1.png)

Una vez que haya configurado su contrato con todas las configuraciones y características, es tan fácil como copiar y pegar el código en su archivo de contrato.

## Despliegue de contratos OpenZeppelin en Moonbeam {: #deploying-openzeppelin-contracts-on-moonbeam } 

Esta sección describe los pasos para desplegar contratos de OpenZeppelin en Moonbeam. Cubre los siguientes contratos:

 - ERC20 ((tokens fungibles)
 - ERC721 (tokens no fungibles)
 - ERC1155 (estándar multi-token)

Todo el código de los contratos se obtuvo utilizando OpenZeppelin [Contract Wizard](https://docs.openzeppelin.com/contracts/4.x/wizard).
 
### Comprobación de requisitos previos {: #checking-prerequisites } 

Los pasos descritos en esta sección asumen que tiene [MetaMask](https://metamask.io/) instalado y conectado a Moonbase Alpha TestNet. La implementación del contrato se realiza utilizando [Remix IDE](https://remix.ethereum.org/) a través del entorno "Injected Web3". Puede encontrar los tutoriales correspondientes en los siguientes enlaces:


 - [Interactuar con Moonbeam usando MetaMask](/integrations/wallets/metamask/)
 - [Interactuar con Moonbeam usando Remix](/integrations/remix/)

### Despliegue de un token ERC20 {: #deploying-an-erc20-token } 

Para este ejemplo, se desplegará un token ERC20 en Moonbase Alpha. El código final utilizado combina diferentes contratos de OpenZeppelin:


 - **ERC20.sol** — despliegue del token ERC20 con las características opcionales de la interfaz base. Incluye el mecanismo de suministro con una `mint` función, pero debe llamarse explícitamente desde el contrato principal.
 - **Ownable.sol** — extensión para restringir el acceso a ciertas funciones

El contrato de token de mintable ERC20 OpenZeppelin proporciona una `mint` función a la que el propietario del contrato solo puede llamar. De forma predeterminada, el propietario es la dirección de despliegue del contrato. También hay una premint de `1000` enviados al implementador del contrato configurado en la `constructor` función.

El primer paso es ir a [Remix](https://remix.ethereum.org/) y seguir los siguientes pasos:

 1. Haga clic en el icono "Crear nuevo archivo" y establezca un nombre de archivo. Para este ejemplo, se configuró `ERC20.sol`
 2. Asegúrese de que el archivo se haya creado correctamente. Haga clic en el archivo para abrirlo en el editor de texto.
 3. Escriba su contrato inteligente usando el editor de archivos. Para este ejemplo, se utilizó el siguiente código:

```sol
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract MyToken is ERC20, Ownable {
    constructor() ERC20("MyToken", "MTK") {
        _mint(msg.sender, 1000 * 10 ** decimals());
    }

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }
}
```

Este contrato inteligente de tokens ERC20 se extrajo del [Contract Wizard](#openzeppelin-contract-wizard), estableciendo una premint de `1000` tokens y activando la `Mintable` función.

![Introducción a Remix](/images/openzeppelin/ozcontracts-images1.png)

Una vez que su contrato inteligente esté escrito, puede compilarlo siguiendo los siguientes pasos:

 1. Dirígete al "Compilador de solidez"
 2. Haga clic en el botón compilar
 3. lternativamente, puede marcar la función "Compilación automática"

![Compile el contrato ERC20 con Remix](/images/openzeppelin/ozcontracts-images2.png)

Con el contrato compilado, está listo para implementarlo siguiendo los siguientes pasos:

 1. Dirígete a la pestaña "Desplegar y ejecutar transacciones"
 2. Cambie el entorno a "Injected Web3". Esto utilizará el proveedor inyectado de MetaMask. En consecuencia, el contrato se desplegará en cualquier red a la que esté conectado MetaMask. MetaMask puede mostrar una ventana emergente que indica que Remix está tratando de conectarse a su billetera
 3. Seleccione el contrato adecuado para desplegar. En este ejemplo, es el `MyToken` contrato dentro del `ERC20.sol` archivo.
 4. Si todo está listo, haga clic en el botón "Desplegar". Revise la información de la transacción en MetaMask y confírmela
 5. Después de unos segundos, la transacción debería confirmarse y debería ver su contrato en "Contratos desplegados".

![Desplegar contrato ERC20 con Remix](/images/openzeppelin/ozcontracts-images3.png)

¡Y eso es todo! Ha desplegado un contrato de token ERC20 utilizando los contratos y las bibliotecas de OpenZeppelin. A continuación, puede interactuar con su contrato de token a través de Remix o agregarlo a MetaMask.

### Despliegue de un token ERC721 {: #deploying-an-erc721-token } 

Para este ejemplo, se desplegará un token ERC721 en Moonbase Alpha. El código final utilizado combina diferentes contratos de OpenZeppelin:

 - **ERC721** — Despliegue del token ERC721 con las características opcionales de la interfaz base. Incluye el mecanismo de suministro con una `_mint` función, pero debe llamarse explícitamente desde el contrato principal.
 - **Burnable** — extensión para permitir que los tokens sean destruidos por sus propietarios (o direcciones aprobadas)
 - **Enumerable** — extensión para permitir la enumeración en cadena de tokens
 - **Ownable.sol** — extensión para restringir el acceso a ciertas funciones

El contrato de token de mintable ERC721 OpenZeppelin proporciona una `mint` función que solo puede ser invocada por el propietario del contrato. De forma predeterminada, el propietario es la dirección de despliegue del contrato.

Al igual que con el [contrato ERC20](#despliegue-de-un-token-erc20), el primer paso es ir a [Remix](https://remix.ethereum.org/) y crear un nuevo archivo. Para este ejemplo, el nombre del archivo será `ERC721.sol`.

A continuación, deberá escribir el contrato inteligente y compilarlo. Para este ejemplo, se utiliza el siguiente código:

```sol
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract MyToken is ERC721, ERC721Enumerable, ERC721Burnable, Ownable {
    constructor() ERC721("MyToken", "MTK") {}

    function safeMint(address to, uint256 tokenId) public onlyOwner {
        _safeMint(to, tokenId);
    }

    function _baseURI() internal pure override returns (string memory) {
        return "Test";
    }

    function _beforeTokenTransfer(address from, address to, uint256 tokenId)
        internal
        override(ERC721, ERC721Enumerable)
    {
        super._beforeTokenTransfer(from, to, tokenId);
    }

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721, ERC721Enumerable)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}
```

Este símbolo ERC721 contrato inteligente se extrajo del [Contract Wizard](#openzeppelin-contract-wizard), estableciendo el `Base URI` como `Test` y la activación de las `Mintable`, `Burnable`, y `Enumerable` características.

Con el contrato compilado, diríjase a la pestaña "Desplegar y ejecutar transacciones". Aquí, necesitas:

 1. Cambie el entorno a "Injected Web3". Esto utilizará el proveedor inyectado de MetaMask. En consecuencia, el contrato se desplegará en cualquier red a la que esté conectado MetaMask. MetaMask puede mostrar una ventana emergente que indica que Remix está tratando de conectarse a su billetera
 2. Seleccione el contrato adecuado para implementar. En este ejemplo, es el `MyToken` contrato dentro del `ERC721.sol` archivo.
 3. Si todo está listo, haga clic en el botón "Desplegar". Revise la información de la transacción en MetaMask y confírmela
 4. Después de unos segundos, la transacción debería confirmarse y debería ver su contrato en "Contratos desplegados".

![Desplegar contrato ERC721 con Remix](/images/openzeppelin/ozcontracts-images4.png)

¡Y eso es todo! Ha desplegado un contrato de token ERC721 utilizando los contratos y las bibliotecas de OpenZeppelin. A continuación, puede interactuar con su contrato de token a través de Remix o agregarlo a MetaMask.

### Despliegue de un token ERC1155 {: #deploying-an-erc1155-token } 

Para este ejemplo, se desplegará un token ERC1155 en Moonbase Alpha. El código final utilizado combina diferentes contratos de OpenZeppelin:

 - **ERC1155** —  Despliegue del token ERC1155 con las características opcionales de la interfaz base. Incluye el mecanismo de suministro con una `_mint` función, pero debe llamarse explícitamente desde el contrato principal.
 - **Pausable** — extensión para permitir pausar la transferencia de tokens, acuñaciones y quemaduras
 - **Ownable.sol** — extensión para restringir el acceso a ciertas funciones

El contrato de token ERC1155 de OpenZeppelin proporciona una `_mint` función que solo se puede llamar en la `constructor` función. Por lo tanto, este ejemplo crea 1000 tokens con un ID de `0`, y 1 token único con un ID de `1`.

El primer paso es ir a [Remix](https://remix.ethereum.org/) y crear un nuevo archivo. Para este ejemplo, el nombre del archivo será `ERC1155.sol`.

Como se muestra para el [token ERC20](#despliegue-de-un-token-erc20), deberá escribir el contrato inteligente y compilarlo. Para este ejemplo, se utiliza el siguiente código:

```sol
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/security/Pausable.sol";

contract MyToken is ERC1155, Ownable, Pausable {
    constructor() ERC1155("Test") {
        _mint(msg.sender, 0, 1000 * 10 ** 18, "");
        _mint(msg.sender, 1, 1, "");
    }

    function setURI(string memory newuri) public onlyOwner {
        _setURI(newuri);
    }

    function pause() public onlyOwner {
        _pause();
    }

    function unpause() public onlyOwner {
        _unpause();
    }

    function _beforeTokenTransfer(address operator, address from, address to, uint256[] memory ids, uint256[] memory amounts, bytes memory data)
        internal
        whenNotPaused
        override
    {
        super._beforeTokenTransfer(operator, from, to, ids, amounts, data);
    }
}
```

Este contrato inteligente de token ERC1155 se extrajo del [Contract Wizard](#openzeppelin-contract-wizard), estableciendo no `Base URI` y activando la `Pausable` función. La función del constructor se modificó para incluir la acuñación de un token fungible y no fungible.

Con el contrato compilado, diríjase a la pestaña "Implementar y ejecutar transacciones". Aquí, necesitas:

 1. Cambie el entorno a "Injected Web3". Esto utilizará el proveedor inyectado de MetaMask. En consecuencia, el contrato se desplegará en cualquier red a la que esté conectado MetaMask. MetaMask puede mostrar una ventana emergente que indica que Remix está tratando de conectarse a su billetera
 2. Seleccione el contrato adecuado para desplegar. En este ejemplo, es el `MyToken` ccontrato dentro del `ERC1155.sol` archivo.
 3. Si todo está listo, haga clic en el botón "Desplegar". Revise la información de la transacción en MetaMask y confírmela
 4. Después de unos segundos, la transacción debería confirmarse y debería ver su contrato en "Contratos desplegados".

![Desplegar contrato ERC1155 con Remix](/images/openzeppelin/ozcontracts-images5.png)

¡Y eso es todo! Ha desplegado un contrato de token ERC1155 utilizando los contratos y las bibliotecas de OpenZeppelin. A continuación, puede interactuar con su contrato de token a través de Remix.
