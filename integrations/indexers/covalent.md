title: Covalent API
description: Consulta de datos de Blockchain con la API Covalent Moonbeam
---

# Introducción a la API de Covalent

![The Graph on Moonbeam](/images/covalent/covalentbannerimage.png)

## Introducción

Covalent proporciona una API unificada para brindar total transparencia y visibilidad a los activos en todas las redes blockchain. En pocas palabras, Covalent ofrece una única API que le permite extraer datos de transacciones de blockchain detallados y granulares de múltiples blockchains sin código. La API Covalent unificada le permite crear aplicaciones completamente nuevas o aumentar las existentes sin configurar ni mantener la infraestructura. Covalent es compatible con Moonbase Alpha y planea ser compatible con Moonbeam y Moonriver.

## Comprobación de requisitos previos

Todas las solicitudes requieren autenticación; necesitará [una clave de API gratuita](https://www.covalenthq.com/platform/#/auth/register/) para utilizar la API de Covalent. Además, necesitará lo siguiente:

 - ener MetaMask instalado y [conectado a Moonbase](/getting-started/moonbase/metamask/)
 - Tener una cuenta con fondos, que puede obtener de [Mission Control](/getting-started/moonbase/faucet/)

## Tipos de Endpoints

La API Covalent tiene dos clases de endpoints:

 - **Clase A** — endpoints que devuelven datos de blockchain enriquecidos aplicables a todas las redes de blockchain, por ejemplo: saldos, transacciones, eventos de registro, etc.
 - **Clase B** — endpoints que son para un protocolo específico en una blockchain, p. ej. Uniswap es solo para Ethereum y no es aplicable a otras redes blockchain

## Fundamentos de la API Covalent
 - La API de Covalent es RESTful y está diseñada en torno a los principales recursos que están disponibles a través de la interfaz web.
 - La versión actual de la API es la versión 1.
 - El formato de devolución predeterminado para todos los endpoints es JSON 
 - Todas las solicitudes requieren autenticación; necesitará [una clave API gratuita](https://www.covalenthq.com/platform/#/auth/register/) para usar la API Covalent
 - La URL raíz de la API es https://api.covalenthq.com/v1/ 
 - Todas las solicitudes se realizan a través de HTTPS (las llamadas a través de HTTP simple fallarán)
 - La frecuencia de actualización de las API es en tiempo real: 30 segundos o 2 bloques, y lotes de 10 mo 40 bloques.

## Endpoints admitidos
 - **Saldos** —  obtenga saldos de tokens para una dirección. Devuelve una lista de todos los saldos de tokens ERC20 y NFT, incluidos ERC721 y ERC1155, junto con sus precios al contado actuales (si están disponibles)
 - **Transacciones** —  recupera todas las transacciones de una dirección, incluidos los eventos de registro decodificados. Realiza un rastreo profundo de la blockchain para recuperar todas las transacciones que hacen referencia a esta dirección
 - **Transferencias** — obtenga transferencias de tokens ERC20 para una dirección junto con precios históricos de tokens (si están disponibles)
 - **Holders de tokens** — devuelve una lista paginada de holders de tokens.
 - **Eventos de registro (contrato inteligente)** — devuelve una lista paginada de eventos de registro decodificados emitidos por un contrato inteligente en particular
 - **Eventos de registro (Topic Hash)** — Devuelve una lista paginada de eventos de registro decodificados con uno o más topic hashes separados por una coma


### Solicitar formato
   | Endpoint |     | Formato |
   | :---------- | :-: | :------------------- |
   |      Saldos       |     |          api.covalenthq.com/v1/1287/address/{address}/balances_v2/          |
   |      Actas       |     |           api.covalenthq.com/v1/1287/address/{address}/transactions_v2/|
   |      Traslados       |     |           api.covalenthq.com/v1/1287/address/{address}/transfers_v2/           |
   |      Holders de tokens       |     |           api.covalenthq.com/v1/1287/tokens/{contract_address}/token_holders/           |
   |      Eventos de registro (contrato inteligente)       |     |           api.covalenthq.com/v1/1287/events/address/{contract_address}/           |
   |      Eventos de registro (Topic Hash)      |     |           api.covalenthq.com/v1/1287/events/topics/{topic}/           |

## Pruébalo
Primero, asegúrese de tener [su clave API](https://www.covalenthq.com/platform/#/auth/register/) que comienza con “ckey_”. El Endpoint de los holders de tokes devuelve una lista de todos los poseedores de tokens de un token en particular. Para esta llamada a la API, necesitará lo siguiente:

 - Tu clave de API
 - ID de cadena Alpha Moonbase: 1287
 - Dirección del contrato (token ERTH en este ejemplo): 0x08B40414525687731C23F430CEBb424b332b3d35

### Usando Curl
Intente ejecutar el comando a continuación en una ventana de terminal después de reemplazar el marcador de posición con su clave API.

```
curl https://api.covalenthq.com/v1/1287/tokens/\
0x08B40414525687731C23F430CEBb424b332b3d35/token_holders/ \
-u {YOUR API KEY HERE}:
```
!!! note
    Los dos puntos `:` después de la clave de API son importantes porque, de lo contrario, se le pedirá una contraseña (que no es necesaria).


La API de Covalent devolverá una lista de titulares de tokens para el token ERTH. A menos que ya posea algunos tokens ERTH, su dirección no aparecerá en esa lista. Dirígete a [Moonbase Alpha ERC-20 Faucet](https://moonbase-minterc20.netlify.app/) para generar algunas fichas ERTH para ti. Ahora repita la misma solicitud de API Covalent anterior. La API de Covalent se actualiza en tiempo real, por lo que ahora debería ver su dirección en la lista de titulares de tokens para el token ERTH.

## Ejemplos de Javascript
Copie y pegue el bloque de código a continuación en su entorno preferido, o [JSFiddle](https://jsfiddle.net/).Después de configurar la clave API, establezca la dirección constante. Recuerde que nuestra identificación de cadena es `1287` para Moonbase Alpha.

=== "Usando Fetch"
    ```js
    // set your API key
	const APIKEY = 'YOUR API KEY HERE';

	function getData() {
    const address = '0xFEC4f9D5B322Aa834056E85946A32c35A3f5aDD8'; //example
    const chainId = '1287'; //Moonbeam Testnet (Moonbase Alpha Chain ID)
    const url = new URL(`https://api.covalenthq.com/v1/${chainId}/address/${address}/balances_v2/`);
    
    url.search = new URLSearchParams({
        key: APIKEY
    })

    // use fetch API to get Covalent data
    fetch(url)
    .then((resp) => resp.json())
    .then(function(data) {
        const result = data.data;
  
        console.log(result)
        return result
        }
	)}

    getData();
    ```

=== "Usando Async"
    ```js
    // set your API key
    const APIKEY = 'YOUR API KEY HERE';
	const address = '0xFEC4f9D5B322Aa834056E85946A32c35A3f5aDD8'; //example
	const chainId = '1287'; //Moonbeam Testnet (Moonbase Alpha Chain ID)
	const url = new URL(`https://api.covalenthq.com/v1/${chainId}/address/${address}/balances_v2/`);

    url.search = new URLSearchParams({
        key: APIKEY
    })

    async function getData() {
    	const response = await fetch(url);
    	const result = await response.json();
    	console.log(result)
    	return result;
	}

	getData();

    ```

La salida debe parecerse a la siguiente. El endpoint de saldos devuelve una lista de todos los saldos de tokens ERC20 y NFT, incluidos ERC721 y ERC1155, junto con sus precios al contado actuales (si están disponibles).

![Javascript Console Output](/images/covalent/covalentjs.png)

## Ejemplo de Python
Covalent no tiene un contenedor API oficial. Para consultar la API directamente, tendrá que usar la [biblioteca de solicitudes](https://pypi.org/project/requests/) de Python. Instale solicitudes en su entorno desde la línea de comandos con `pip install requests`. Luego impórtelo y úselo en su código. Utilice los métodos get de verbos HTTP para devolver la información de la API. Copie y pegue el bloque de código a continuación en su entorno preferido y ejecútelo. La salida debe ser similar a la captura de pantalla anterior, sin embargo, el formato puede variar según su entorno.

```python
import requests

def fetch_wallet_balance(address):
	api_url = 'https://api.covalenthq.com'
    endpoint = f'/v1/1287/address/{address}/balances_v2/'
    url = api_url + endpoint
    r = requests.get(url, auth=('YOUR API KEY HERE',''))
    print(r.json())
    return(r.json())

#Example address request
fetch_wallet_balance('0xFEC4f9D5B322Aa834056E85946A32c35A3f5aDD8')

```

!!! note
    El segundo parámetro de `auth` está vacío, porque no se requiere contraseña; su clave API es todo lo que se necesita.

### Bibliotecas construidas por la comunidad
Actualmente, Covalent tiene bibliotecas en Python, Node y Go, que la comunidad crea y mantiene como parte del [Programa Covalent Alchemists](https://www.covalenthq.com/ambassador/). Las herramientas han sido creadas por la comunidad para proporcionar valor a los usuarios de Covalent API y están [disponibles aquí](https://www.covalenthq.com/docs/tools/community).

!!! note
    Note: Estas herramientas NO son mantenidas por Covalent y los usuarios deben hacer su debida diligencia al evaluar estas herramientas antes de usarlas en sus proyectos.

