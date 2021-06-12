Un nodo de Oracle tiene un conjunto de ID de trabajo, donde cada uno corresponde a una tarea que puede ser solicitada por un usuario, por ejemplo, buscar un feed de precios. Para ello, el usuario debe enviar una solicitud a través de un contrato, lo llamaremos contrato _Cliente_ pasando la siguiente información:

 - Dirección de Oracle: dirección del contrato desplegado por el nodo de Oracle
 - Job ID: tarea a ejecutar
 - Pago: pago en tokens LINK que recibirá Oracle por atender la solicitud

Esta solicitud en realidad envía un _transferAndCall_ al contrato del token LINK, que maneja el pago y transmite la solicitud al contrato de Oracle. Aquí, se emite un evento con la solicitud, que es captada por el nodo de Oracle. A continuación, el nodo obtiene los datos necesarios y ejecuta la función _fulfilOracleRequest_ que ejecuta una devolución de llamada que almacena la información solicitada en el contrato del Cliente. El siguiente diagrama explica este flujo de trabajo.

![Basic Request Diagram](/images/chainlink/chainlink-basicrequest.png)
