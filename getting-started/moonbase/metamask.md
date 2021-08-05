---
title: Integrar MetaMask
description: Aprenda a usar MetaMask con Moonbeam TestNet. Este tutorial le muestra cómo conectar una instalación predeterminada de MetaMask a Moonbase Alpha.
---

# Conecte MetaMask a Moonbase Alpha

## Introducción {: #introduction } 

This guide outlines the steps needed to connect MetaMask to Moonbase Alpha. In contrast to the MetaMask guide for a Moonbeam development node, this is much simpler because you don't need to connect to a local running Moonbeam node. Let's jump right into it.

Si ya tiene MetaMask instalado, puede conectarlo fácilmente a la red de prueba Moonbase Alpha:

<div class="button-wrapper">
    <a href="#" class="md-button connectMetaMask">Conectar MetaMask</a>
</div>

!!! nota
    MetaMask aparecerá pidiendo permiso para agregar Moonbase Alpha como una red personalizada. Una vez que apruebe los permisos, MetaMask cambiará su red actual a Moonbase Alpha.
    
--8<-- 'text/common/create-metamask-wallet.md'

## Conexión a Moonbase Alpha {: #connecting-to-moonbase-alpha } 

Una vez que termine de crear o importar una billetera, verá la interfaz principal de MetaMask. Aquí, haga clic en el logotipo superior derecho y vaya a Configuración.

![MetaMask3](/images/testnet/testnet-metamask3.png)

Vaya a la pestaña Redes y haga clic en el botón "Agregar red".

![MetaMask4](/images/testnet/testnet-metamask4.png)

Aquí, complete la siguiente información y luego haga clic en Guardar:

  - Network Name: `Moonbase Alpha`
  - RPC URL: `{{ networks.moonbase.rpc_url }}`
  - ChainID: `{{ networks.moonbase.chain_id }}`
  - Symbol (Optional): `DEV`
  - Block Explorer: `{{ networks.moonbase.block_explorer }}`

![MetaMask5](/images/testnet/testnet-metamask5.png)

¡Eso es! Ha conectado correctamente MetaMask a Moonbeam TestNet, Moonbase.
