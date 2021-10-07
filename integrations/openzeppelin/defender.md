---
title: Defender
description:  Aprenda a usar OpenZeppelin Defender para administrar contratos inteligentes de forma segura en Moonbeam, gracias a sus funciones de compatibilidad con Ethereum
---

# OpenZeppelin Defender

![OpenZeppelin Defender Banner](/images/builders/tools/openzeppelin/oz-defender-banner.png)

## Introducción {: #introduction } 

OpenZeppelin Defender es una aplicación basada en web que permite a los desarrolladores realizar y automatizar operaciones de contratos inteligentes de forma segura. Defender ofrece diferentes componentes:

 - [**Admin**](https://docs.openzeppelin.com/defender/admin) — para automatizar y asegurar todas sus operaciones de contratos inteligentes, como controles de acceso, actualizaciones y pausas.
 - [**Relay**](https://docs.openzeppelin.com/defender/relay) — para construir con una infraestructura de transacciones privada y segura con la implementación de retransmisores privados.
 - [**Autotasks**](https://docs.openzeppelin.com/defender/autotasks) — para crear scripts automatizados para interactuar con sus contratos inteligentes
 - [**Sentinel**](https://docs.openzeppelin.com/defender/sentinel) — para monitorear los eventos, funciones y transacciones de su contrato inteligente, y recibir notificaciones por correo electrónico.
 - [**Advisor**](https://docs.openzeppelin.com/defender/advisor) — para aprender e implementar las mejores prácticas en torno al desarrollo, las pruebas, la supervisión y las operaciones.

OpenZeppelin Defender ahora se puede utilizar en Moonbase Alpha TestNet. Esta guía le mostrará cómo comenzar con Defender y hacer una demostración del componente Admin para pausar un contrato inteligente. Puede encontrar más información sobre los otros componentes en los enlaces mencionados anteriormente.

Para obtener más información, el equipo de OpenZeppelin ha escrito un excelente [doc site](https://docs.openzeppelin.com/defender/) para Defender.

## Empezando con Defender en Moonbase Alpha {: #getting-started-with-defender-on-moonbase-alpha } 

Esta sección describe los pasos para comenzar con OpenZeppelin Defender en Moonbase Alpha.
 
### Comprobación de requisitos previos {: #checking-prerequisites } 

Los pasos descritos en esta sección asumen que tiene [MetaMask](https://metamask.io/)  instalado y conectado a Moonbase Alpha TestNet. Si no ha conectado MetaMask a TestNet, consulte nuestra [guía de integración de MetaMask](/integrations/wallets/metamask/).

Además, debe registrarse para obtener una cuenta gratuita de OpenZeppelin Defender, lo que puede hacer en el [website de Defender](https://defender.openzeppelin.com/).

El contrato utilizado en esta guía es una extensión del contrato `Box.sol` utilizado en la [guía de actualización de contratos inteligentes](https://docs.openzeppelin.com/learn/upgrading-smart-contracts),de la documentación de OpenZeppelin. Además, el contrato se actualizó y se [pausó](https://docs.openzeppelin.com/contracts/4.x/api/security#Pausable) para aprovechar al máximo el componente Admin. Puede implementar su contrato utilizando el siguiente código y siguiendo la [guía de actualización de contratos inteligentes](https://docs.openzeppelin.com/learn/upgrading-smart-contracts):

```sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts-upgradeable/security/PausableUpgradeable.sol";
import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

contract PausableBox is Initializable, PausableUpgradeable, OwnableUpgradeable {
    uint256 private value;
 
    // Emitted when the stored value changes
    event ValueChanged(uint256 newValue);

    // Initialize
    function initialize() initializer public {
        __Ownable_init();
        __Pausable_init_unchained();
    }
 
    // Stores a new value in the contract
    function store(uint256 newValue) whenNotPaused public {
        value = newValue;
        emit ValueChanged(newValue);
    }
 
    // Reads the last stored value
    function retrieve() public view returns (uint256) {
        return value;
    }
    
    function pause() public onlyOwner {
        _pause();
    }

    function unpause() public onlyOwner {
        _unpause();
    }
}
```

### Conectando Defender a Moonbase Alpha {: #connecting-defender-to-moonbase-alpha } 

Una vez que tenga una cuenta OpenZeppelin Defender, inicie sesión en la [Defender App](https://defender.openzeppelin.com/). En la pantalla principal, con MetaMask [conectado a Moonbase Alpha](/getting-started/moonbase/metamask/) haga clic en el botón "Conectar billetera" de la esquina superior derecha:

![OpenZeppelin Defender Connect](/images/builders/tools/openzeppelin/oz-defender-1.png)

Si tiene éxito, debería ver su dirección y un texto que diga "Conectado a Moonbase Alpha".

## Usando el Componente Admin {: #using-the-admin-component } 

Esta sección describe los pasos para comenzar con el componente Admin de OpenZeppelin Defender para administrar contratos inteligentes en Moonbase Alpha.

### Importando su contrato {: #importing-your-contract } 

El primer paso para usar Defender Admin es agregar el contrato que desea administrar. Para hacerlo, haga clic en el botón "Agregar contrato" cerca de la esquina superior derecha. Esto lo llevará a la pantalla "importar contrato", donde debe:


 1. Establezca un nombre de contrato. Esto es solo para fines de visualización.
 2. Seleccione la red donde se implementa el contrato que desea administrar. Esto es particularmente útil cuando se implementa un contrato con la misma dirección en varias redes. Para este ejemplo, ingrese `Moonbase Alpha`
 3. Ingrese la dirección del contrato
 4. Pega el contrato ABI. Esto se puede obtener en [Remix](/integrations/remix/) o en el archivo `.json` generalmente creado después del proceso de compilación (por ejemplo, en Truffle o HardHat)
 5. Verifique que las características del contrato se hayan detectado correctamente
 6. Una vez que haya verificado toda la información, haga clic en el botón "Agregar"

![OpenZeppelin Defender Admin Add Contract](/images/builders/tools/openzeppelin/oz-defender-2.png)

Si todo se importó correctamente, debería ver su contrato en la pantalla principal del componente Admin:

![OpenZeppelin Defender Admin Contract Added](/images/builders/tools/openzeppelin/oz-defender-3.png)

### Crear una propuesta de contrato {: #create-a-contract-proposal } 

Las propuestas son acciones a realizar en el contrato. En el momento de redactar este artículo, hay tres propuestas / acciones principales que pueden llevarse a cabo:

- **Pause** — disponible si se detecta la función de pausa. Pausa las transferencias, la acuñación y la quema de tokens
- **Upgrade** — disponible si se detecta la función de actualización. Permite [actualizar un contrato a través de un contrato proxy](https://docs.openzeppelin.com/learn/upgrading-smart-contracts)
- **Admin action** — llamada a cualquier función en el contrato administrado

En este caso, se crea una nueva propuesta para pausar el contrato. Para hacerlo, siga los siguientes pasos:

 1. Haga click en el botón "Nueva propuesta" para ver todas las opciones disponibles
 2. Haga click en "Pausa"

![OpenZeppelin Defender Admin Contract New Pause Proposal](/images/builders/tools/openzeppelin/oz-defender-4.png)

Esto abrirá la página de la propuesta, donde se deben completar todos los detalles relacionados con la propuesta. En este ejemplo, debe proporcionar la siguiente información:

 1. Dirección de la cuenta de administrador. También puede dejar este campo vacío si desea ejecutar la acción desde su billetera actual (si tiene todos los permisos necesarios)
 2. Título de la propuesta
 3. Descripción de la propuesta. Aquí, debe proporcionar tantos detalles como sea posible para otros miembros / gerentes del contrato (si usa una billetera MultiSig)
 4. Haga click en "Crear propuesta de pausa".

![OpenZeppelin Defender Admin Contract Pause Proposal Details](/images/builders/tools/openzeppelin/oz-defender-5.png)

Una vez que la propuesta se crea correctamente, debe aparecer en el panel de administración del contrato.


![OpenZeppelin Defender Admin Contract Proposal List](/images/builders/tools/openzeppelin/oz-defender-6.png)

### Aprobar una propuesta de contrato {: #approve-a-contract-proposal } 

Con la propuesta de contrato creada, el siguiente paso es aprobarla y ejecutarla. Para hacerlo, vaya a la propuesta y haga clic en "Aprobar y ejecutar".

![OpenZeppelin Defender Admin Contract Proposal Pause Approve](/images/builders/tools/openzeppelin/oz-defender-7.png)


Esto iniciará una transacción que debe firmarse con MetaMask, después de lo cual el estado de la propuesta debe cambiar a "Ejecutado (confirmación pendiente)". Una vez que se procesa la transacción, el estado debe mostrar "Ejecutado".

![OpenZeppelin Defender Admin Contract Proposal Pause Executed](/images/builders/tools/openzeppelin/oz-defender-8.png)

También puede ver que el estado del contrato ha cambiado de "En ejecución" a "En pausa". ¡Estupendo! Ahora sabe cómo usar el componente Admin para administrar sus contratos inteligentes.
