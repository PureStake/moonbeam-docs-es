---
title: MetaMask
description: Esta guía le explica cómo conectar MetaMask, una billetera Ethereum basada en navegador, a Moonbeam.
---

# Interactuando con Moonbeam usando MetaMask

![Intro diagram](/images/integrations/integrations-metamask-banner.png)

## Introducción {: #introduction } 

Los desarrolladores pueden aprovechar las funciones de compatibilidad con Ethereum de Moonbeam para integrar herramientas, como [MetaMask](https://metamask.io/), en sus DApps. Al hacerlo, pueden usar la biblioteca inyectada que proporciona MetaMask para interactuar con la blockchain.

Actualmente, MetaMask se puede configurar para conectarse a dos redes: un nodo de desarrollo Moonbeam o Moonbase Alpha TestNet.

Si ya tiene MetaMask instalado, puede conectar fácilmente MetaMask a Moonbase Alpha TestNet:

<div class="button-wrapper">
    <a href="#" class="md-button connectMetaMask" value="moonbase">Connect MetaMask</a>
</div>

!!! nota
    MetaMask aparecerá pidiendo permiso para agregar Moonbase Alpha como una red personalizada. Una vez que apruebe los permisos, MetaMask cambiará su red actual a Moonbase Alpha.

Aprenda [a integrar un botón Connect MetaMask](#integra-metamask-en-una-dapp) en su dapp, para que los usuarios puedan conectarse a Moonbase Alpha con un simple clic de un botón.

## Conecte MetaMask a Moonbeam {: #connect-metamask-to-moonbeam } 

Una vez que haya instalado [MetaMask](https://metamask.io/) puede conectarlo a Moonbeam haciendo clic en el logotipo superior derecho y abriendo la configuración.

![MetaMask3](/images/testnet/testnet-metamask3.png)

A continuación, vaya a la pestaña Redes y haga clic en el botón "Agregar red".

![MetaMask4](/images/testnet/testnet-metamask4.png)

Aquí puede configurar MetaMask para las siguientes redes:

Nodo de desarrollo Moonbeam:

--8<-- 'text/metamask-local/development-node-details.md'

Moonbase Alpha TestNet:

--8<-- 'text/testnet/testnet-details.md'

## Tutoriales paso a paso {: #step-by-step-tutorials } 

En el caso de que estés interesado en guías paso a paso más detalladas para configurar MetaMask to Moonbeam, puedes acudir a nuestros tutoriales específicos:

 - MetaMask en un [nodo de desarrollo Moonbeam](/getting-started/local-node/using-metamask/)
 - MetaMask en [Moonbase Alpha](/getting-started/testnet/metamask/)

## Integra MetaMask en una DApp {: #integrate-metamask-into-a-dapp } 

Con el lanzamiento de la [API de redes personalizadas](https://consensys.net/blog/metamask/connect-users-to-layer-2-networks-with-the-metamask-custom-networks-api/), de MetaMask , se puede solicitar a los usuarios que agreguen Testnet de Moonbeam, Moonbase Alpha.

Esta sección lo guiará a través del proceso de agregar un botón "Conectarse a Moonbase Alpha" que pedirá a los usuarios que conecten sus cuentas de MetaMask a Moonbase Alpha. Sus usuarios ya no necesitarán saber o preocuparse por las configuraciones de red de Moonbase Alpha y agregar una red personalizada a MetaMask. Para interactuar con Moonbeam desde su dApp, todo lo que los usuarios deberán hacer es hacer clic en algunos botones para conectarse a Moonbase Alpha y comenzar.

MetaMask inyecta una API Ethereum global en los sitios web que visitan los usuarios `window.ethereum`,lo que permite que los sitios web lean y soliciten los datos de la blockchain de los usuarios. Utilizará el proveedor de Ethereum para guiar a sus usuarios a través del proceso de agregar Moonbase Alpha como una red personalizada. En general, deberás:

- Compruebe si existe el proveedor de Ethereum y si es MetaMask
- Solicitar la dirección de la cuenta del usuario
- Agregue Moonbase Alpha como una nueva cadena

Esta guía se divide en dos secciones. Primero, cubrirá la adición de un botón que se usará para activar MetaMask para que aparezca y se conecte a Moonbase Alpha. La segunda parte de la guía creará la lógica para conectar al usuario a MetaMask. De esta manera, cuando hace clic en el botón, puede probar la funcionalidad a medida que avanza en la guía.

### Comprobación de requisitos previos {: #checking-prerequisites } 

Para agregar el botón Conectar MetaMask, necesitará un proyecto de JavaScript y la extensión del navegador MetaMask instalada para las pruebas locales.

Se recomienda utilizar el `detect-provider` paquete de utilidades de MetaMask para detectar el proveedor inyectado en `window.ethereum`. El paquete maneja la detección del proveedor para la extensión MetaMask y MetaMask Mobile. Para instalar el paquete en su proyecto de JavaScript, ejecute:

```
npm install @metamask/detect-provider
```
### Agregar un botón {: #add-a-button } 

Comenzará agregando un botón que se usará para conectar MetaMask a Moonbase Alpha. Desea comenzar con el botón para que cuando cree la lógica en el siguiente paso, pueda probar el código a medida que avanza en la guía.

Se llamará a la función que crearemos en la siguiente sección de la guía `configureMoonbaseAlpha`. Entonces, el botón al hacer clic debería llamar `configureMoonbaseAlpha`.

```html
<button onClick={configureMoonbaseAlpha()}>Connect to Moonbase Alpha</button>
```

### Agregar lógica {: #add-logic } 

Ahora que ha creado el botón, debe agregar la `configureMoonbaseAlpha` unción que se utilizará al hacer clic.

1. Detecta el proveedor en `window.ethereum` y comprueba si es MetaMask. Si desea una solución simple, puede acceder directamente `window.ethereum`. O puede usar el `detect-provider` paquete de MetaMask y este detectará el proveedor de la extensión MetaMask y MetaMask Mobile por usted.

```javascript
import detectEthereumProvider from '@metamask/detect-provider';

const configureMoonbaseAlpha = async () => {
    const provider = await detectEthereumProvider({ mustBeMetaMask: true });
    if (provider) {
        // Logic will go here    
    } else {
        console.error("Please install MetaMask");
    }
}
```

2. Solicite las cuentas del usuario llamando al `eth_requestAccounts` método. Esto hará que MetaMask aparezca y le pedirá al usuario que seleccione las cuentas a las que le gustaría conectarse. Detrás de escena, los permisos se verifican llamando `wallet_requestPermissions`. Actualmente, los únicos permisos son para `eth_accounts`.Por lo tanto, en última instancia, está verificando que tiene acceso a las direcciones de los usuarios devueltos `eth_accounts`. Si está interesado en obtener más información sobre el sistema de permisos, consulte [EIP-2255](https://eips.ethereum.org/EIPS/eip-2255).
 
```javascript
import detectEthereumProvider from '@metamask/detect-provider';

const configureMoonbaseAlpha = async () => {
    const provider = await detectEthereumProvider({ mustBeMetaMask: true });
    if (provider) {
        try {
            await provider.request({ method: "eth_requestAccounts"});
        } catch(e) {
            console.error(e);
        }  
    } else {
        console.error("Please install MetaMask");
    }
}
```
<img src="/images/integrations/integrations-metamask-1.png" alt="Integrate MetaMask into a Dapp - Select account" style="width: 50%; display: block; margin-left: auto; margin-right: auto;"/>
<img src="/images/integrations/integrations-metamask-2.png" alt="Integrate MetaMask into a Dapp - Connect account" style="width: 50%; display: block; margin-left: auto; margin-right: auto;" />

3. Agregue Moonbase Alpha como una nueva cadena llamando `wallet_addEthereumChain`. Esto le pedirá al usuario que otorgue permiso para agregar Moonbase Alpha como una red personalizada.
 
```javascript
import detectEthereumProvider from '@metamask/detect-provider';

const configureMoonbaseAlpha = async () => {
    const provider = await detectEthereumProvider({ mustBeMetaMask: true });
    if (provider) {
        try {
            await provider.request({ method: "eth_requestAccounts"});
            await provider.request({
                method: "wallet_addEthereumChain",
                params: [
                    {
                        chainId: "0x507", // Moonbase Alpha's chainId is 1287, which is 0x507 in hex
                        chainName: "Moonbase Alpha",
                        nativeCurrency: {
                            name: 'DEV',
                            symbol: 'DEV',
                            decimals: 18
                        },
                       rpcUrls: ["https://rpc.testnet.moonbeam.network"],
                       blockExplorerUrls: ["https://moonbase-blockscout.testnet.moonbeam.network/"]
                    },
                ]
            })
        } catch(e) {
            console.error(e);
        }  
    } else {
        console.error("Please install MetaMask");
    }
}
```

<img src="/images/integrations/integrations-metamask-3.png" alt="Integrate MetaMask into a Dapp - Add network" style="width: 50%; display: block; margin-left: auto; margin-right: auto;"/>

Una vez que la red se haya agregado con éxito, también le pedirá al usuario que cambie a Moonbase Alpha.

<img src="/images/integrations/integrations-metamask-4.png" alt="Integrate MetaMask into a Dapp - Switch to network" style="width: 50%; display: block; margin-left: auto; margin-right: auto;"/>

Entonces, ahora debería tener un botón que, al hacer clic, guíe a los usuarios a través de todo el proceso de conexión de sus cuentas MetaMask a Moonbase Alpha.

<img src="/images/integrations/integrations-metamask-5.png" alt="Integrate MetaMask into a Dapp - Account connected to Moonbase Alpha"/>

### Confirmar conexión {: #confirm-connection } 

Es posible que tenga una lógica que se base en saber si un usuario está conectado a Moonbase Alpha o no. Quizás desee deshabilitar el botón si el usuario ya está conectado. Para confirmar que un usuario está conectado a Moonbase Alpha, puede llamar `eth_chainId`, que devolverá el ID de cadena actual del usuario:

```javascript
    const chainId = await provider.request({
        method: 'eth_chainId'
    })
    // Moonbase Alpha's chainId is 1287, which is 0x507 in hex
    if (chainId === "0x507"){
        // At this point, you might want to disable the "Connect" button
        // or inform the user that they are already connected to the
        // Moonbase Alpha testnet
    }
```

### Escuche los cambios en la cuenta {: #listen-to-account-changes } 

Para asegurarse de que su proyecto o dApp se mantenga actualizado con la información más reciente de la cuenta, puede agregar el `accountsChanged` edetector de eventos que proporciona MetaMask. MetaMask emite este evento cuando `eth_accounts` cambia el valor de retorno de . Si se devuelve una dirección, es la cuenta más reciente de su usuario la que proporcionó permisos de acceso. Si no se devuelve ninguna dirección, eso significa que el usuario no ha proporcionado ninguna cuenta con permisos de acceso.

```javascript
    provider.on("accountsChanged", (accounts) => {
        if (accounts.length === 0) {
            // MetaMask is locked or the user doesn't have any connected accounts
            console.log('Please connect to MetaMask.');
        } 
    })
```

### Escuche los cambios en la cadena {: #listen-to-chain-changes } 

Para mantener su proyecto o dApp actualizado con cualquier cambio en la cadena conectada, querrá suscribirse al `chainChanged` evento. MetaMask emite este evento cada vez que cambia la cadena conectada.

```javascript
    provider.on("chainChanged", () => {
        // MetaMask recommends reloading the page unless you have good reason not to
        window.location.reload();
    })
```

MetaMask recomienda volver a cargar la página cada vez que cambie la cadena, a menos que haya una buena razón para no hacerlo, ya que es importante estar siempre sincronizado con los cambios en la cadena.
