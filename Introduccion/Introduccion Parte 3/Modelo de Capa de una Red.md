# Bloque 8
Finalmente, integramos todo lo visto antes en un modelo por capas, donde capa nivel ofrece servicios al superior.
## Modelos de Capas de una Red
![](ModeloDeCapas.png)
* **Interfaces entre Capas**: Operaciones y servicios primitivos ofrecidos por una capa a capa superior
El **Significado de una Capa**:
* Una capa `n` se piensa como una conversación entre la capa `n` de una maquina con la capa `n` de otra maquina.
* **Protocolo de capa `n`:** Reglas y convenciones usadas en la conversación entre la capa `n` de una maquina y la capa `n` de otra maquina.
* **Arquitectura de red**: Conjunto de capas y protocolos.

La **Capa 5 de Aplicación** produce un mensaje y lo pasa a la capa de transporte.
La **Capa 4 de Transporte** pone un encabezado en el mensaje para identificarlo y pasa el resultado a la capa de Red. El encabezado contiene Numeros de Secuencia para que la capa de transporte de la maquina de destino entregue los mensajes en el orden correcto.
La **Capa 3 de Red** pone limitaciones en el tamaño de los mensajes, divide en **Paquetes** los mensajes que llegan. A cada paquete se le coloca otro encabezado. Pasa los paquetes a la capa de enlace de datos.
La **Capa 2 de Enlace de Datos** agrega un encabezado y un terminador, a cada pieza. Pasa la unidad resultante a la capa física para su transmisión.
La **maquina receptora** pasa hacia arriba de capa en capa, perdiendo los encabezados conforme avanza.
![](ModeloDeCapas2.png)