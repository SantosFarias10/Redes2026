# Bloque 7
Acá pasamos del transporte de datos a la lógica de las aplicaciones.
#### Ejercicio
Un procesos Cliente quiere pedir una pizza a un procesos Servidor ubicado en otra maquina. Diseñar como se comunican el proceso Cliente y el proceso Servidor para completar un pedido de pizza.
* ¿Que datos tiene el pedido del cliente?
* ¿Como sabe el cliente que el servidor recibió el pedido?
* Como representa el servidor el estado del pedido? supongamos que el pedido va pasando por diferentes estados (recibido, pagado, enviado).
* ¿Que datos tiene las respuestas del servidor?

	* **Tipos de Paquetes**;
		* Paquetes de pedido de pizza con tipo y tamaño de pizza, nombre y dirección del cliente.
		* Paquete de confirmación de recepción del pedido con tipo, tamaño de pizza y nombre del cliente.
		* Paquete de cobro con nombre de cliente, tipo y tamaño de pizza y el monto cobrado.
		* Paquete de envió del pedido con nombre del cliente, tipo y tamaño de pizza y horario aproximado de entrega y empresa que hace la entrega.
	* **Estado del Pedido**: Tipo y tamaño de pizza, nombre de cliente y estado (pedido recibido, pedido cobrado, pedido enviado).
	* **Reglas de Comunicación**: Primero el cliente manda un paquete de pedido, luego el servidor contesta con un paquete de confirmación del pedido y finalmente envía un paquete de pedido enviado.

---
## Protocolos
#### ¿Que observamos del ejercicio anterior?
* Hay que definir tipos de mensajes con su estructura.
* Hay que definir reglas de comunicación entre los procesos.
* Hay que definir información de estado.
A esto se le llama **Protocolo**. 
Entonces tenemos aplicaciones que proporcionan servicios (llamadas **Aplicaciones de Red**) definidas mediante protocolos.

Hasta ahora vimos como los protocolos de aplicación permiten que dos procesos intercambien mensajes siguiendo reglas bien definidas.
Pero antes de escribir un protocolo, necesitamos algo mas básico: Identificar que procesos participan en la aplicaciones, como se comunican entre si y que roles cumple cada uno. Esta organización previa es lo que se conoce como **Arquitectura de la Aplicación de Red**
La arquitectura define la estructura general de la comunicación, y el protocolo especifica los detalles de como se lleva a cabo.

---
## Arquitectura
#### ¿En el ejercicio que roles cumplen los procesos?
Uno solo pide y le otro solo responde o atiende.
A esto se le llama **Arquitectura Cliente-Servidor**: Un proceso actúa como cliente (inicia pedidos) y otro como servidor (atiende pedidos).
Pero no todas las aplicaciones se organizan asi.
En una **Arquitectura Peer-To-Peer**, los procesos pueden cumplir ambos roles: a veces piden, a veces responden, y pueden compartir recursos entre si sin un servidor central.

En general, una **Arquitectura** describe que procesos participan en la aplicación, que roles cumplen y como se comunican entre si.
La arquitectura ayuda a diseñar la aplicacion, ya que define la estructura basica sobre la cual luego se construyen los protocolos de comunicacion.