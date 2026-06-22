# Conectando Procesos
Este bloque responde a ¿Cómo logramos que programas específicos en máquinas distintas se comuniquen correctamente, incluso cuando la red es imperfecta?

Para distinguir los mensajes para un programa con los mensajes para otro programa, los mensajes deben indicar cuál es el programa receptor usando un identificador de programa (puerto)
## Ordenamiento de paquetes
Si mandamos dos paquetes de un mismo mensaje, esos paquetes no necesariamente van a seguir la misma ruta, puede que cambien las rutas entre paquetes porque se ejecutó el algoritmo de enrutamiento .
Si los dos paquetes de un mensaje son enviados, no necesariamente van a llegar en orden (uno puede perderse y debe ser reenviado, otro puede seguir una ruta más corta), entonces pueden llegar fuera de orden.

Entonces, es necesario ordenar los paquetes de un mensaje porque una aplicación de red necesita procesar el mensaje completo ordenado.
## Control de congestión 
 Cuando muchos flujos simultáneos compiten por la misma red, aunque cada flujo pueda ordenar sus paquetes, la red puede saturarse, perder paquetes masivamente o volverse inestable. A este fenómeno se lo llama congestión y requiere mecanismos específicos para evitar que la red colapse cuando múltiples procesos intentan comunicarse al mismo tiempo.
La congestión ocurre cuando la red no puede manejar la carga de paquetes que recibe de manera aceptable.
![](Screenshot_2026-06-21-16-09-30_2645.png)
### ¿Cómo controlar la congestión?
Las computadoras emisoras se enteran de la congestión y reducen el tráfico de salida. Esta idea puede ser implementada de muchas maneras. A esto se le llama control de congestión 
## Control de flujo 
El control de congestión evita que la red se sature cuando muchos flujos compiten por los mismos enrutadores. Pero este mecanismo solo considera el estado de la red, no el estado de los procesos que se comunican. 
Incluso si hay congestión, un receptor puede saturarse si el emisor envía datos más rápido de lo que puede procesarlos. Cuando eso ocurre, el receptor pierde paquetes y el emisor probablemente va a interpretar esas pérdidas como congestión reaccionando de manera incorrecta.
Para evitar que el emisor sobrecargue al receptor, es necesario definir un mecanismo adicional de control de flujo.
- Si un emisor envía paquetes a un receptor más rápido que la capacidad del receptor de procesar cada paquete, se satura de paquetes el búfer del receptor hasta que este ya no puede almacenar más paquetes que le llegan y comienza a perder paquetes.
![](Screenshot_2026-06-21-16-11-36_7085.png)Para evitar esto, se debe hacer uso de la retroalimentación al emisor. Osea, el receptor le indica al emisor cuándo y cuánto puede enviar
[[Control de flujo (CT)]]
>[!tip] Acordarse de poner el link al control de flujo
## Comunicación confiable
El control de flujo evita que el emisor envíe datos más rápido de lo que el receptor puede procesar, protegiendo al host de desbordes y pérdidas locales. Pero aún cuando el receptor no se satura, eso no garantiza que la comunicación sea correcta: la red puede perder, duplicar o desordenar paquetes, y los procesos necesitan mecanismos para detectar y corregir esos problemas. Por eso, además del control del flujo, es necesario un proceso que asegure una comunicación confiable entre procesos, permitiendo reconstruir los datos completos y en el orden adecuado.
- Los paquetes pueden perderse por varias razones: 
	- Por congestión en los enrutadores cuando las colas se llenan;
	- Porque el emisor envía más rápido que lo que el receptor puede procesar y este descarta paquetes
	- por errores físicos o daños en los paquetes durante su transmisión 	
- Para manejar la pérdida de paquetes:
	 - Los paquetes recibidos son confirmados por el receptor.
	- El emisor detecta que paquetes faltan, porqué no recibió sus confirmaciones de recepción durante un cierto tiempo. Ese tiempo se mide con un temporizador asociado a cada paquete.
	- Si un paquete no es confirmado, el emisor lo retransmite
# Aplicaciones de Red
Este bloque responde a ¿Cómo diseñamos aplicaciones que usan la red para ofrecer servicios reales?
## Protocolos
Un protocolo define tipos de mensajes con su estructura, define reglas de comunicación entre los procesos e información de estado.
Entonces tenemos aplicaciones que proporcionan servicios (llamadas aplicaciones de red) definidas mediante protocolos.

Antes de escribir un protocolo, necesitamos algo básico: identificar qué procesos participan en la aplicación, cómo se comunican entre sí y qué roles cumple cada uno. Esa organización previa - quién habla con quién, quién inicia, quién responde, quién almacena, quien distribuye- es lo que se conoce como arquitectura de la aplicación de red.

La arquitectura define la estructura general de la comunicación, y el protocolo especifica los detalles de cómo se lleva a cabo. Por eso, antes de diseñar un protocolo de aplicación, necesitamos entender las distintas arquitecturas posibles. En general, una arquitectura describe qué procesos participan en la aplicación, qué roles cumplen y cómo se comunican entre sí. 

tipos de arquitecturas:
- arquitecturas cliente-servidor: un proceso actúa como cliente (inicia pedidos) y otro como servidor (atiende pedidos)
- arquitectura peer to peer: los procesos pueden cumplir ambos roles: a veces piden, a veces responden, y pueden compartir recursos entre sí sin un servidor central
[[Capa de Aplicación]]
>[!tip] Acordarse de poner el link

# Modelo de Capa de Red
Este bloque responde a ¿Cómo organizamos toda la complejidad de una red en una arquitectura clara, modular y escalable?
![](Screenshot_2026-06-21-16-15-50_12065.png)
![](Screenshot_2026-06-21-16-16-19_6544.png)
![](Screenshot_2026-06-21-16-16-51_31925.png)
## [[TCP-IP]]
>[!tip] Acordarse de meter el link

Interfaces entre capas: operaciones y servicios primitivos ofrecidos por una capa a capa superior.
### Significado de una capa:
- Una capa n se piensa como una conversación entre la capa n de una máquina con la capa n de otra máquina
- para especificar cómo es esta conversación se definen protocolos.
Protocolo de capa n : reglas y convenciones usadas en la conversación entre la capa n de una máquina y la capa n de otra máquina
arquitectura de red: conjunto de capas y protocolos 
![](Screenshot_2026-06-21-16-18-03_6169.png)
**En la máquina receptora el mensaje pasa hacia arriba de capa en capa, perdiendo los encabezados conforme avanza.**

Procesos de aplicación (capa 5 o capa de aplicación):
	Produce un mensaje y lo pasa a la capa 4 para su transmisión. Por ejemplo: browser, e-mail, chat, ftp, etc. 
Capa de transporte (capa 4):
	Pone un encabezado en el mensaje para identificarlo y pasa el resultado a la capa 3.
	 El encabezado contiene números de secuencia para que la capa 4 en la máquina de destino entregue los mensajes en el orden correcto.
Capa de red (Capa 3):
	 Hay limitaciones en el tamaño de los mensajes de capa 3. Divide en paquetes los mensajes que llegan. A cada paquete se le coloca un encabezado, decide cuál de las líneas que salen usar ( cuando la máquina es un enrutador). Pasa los paquetes a la capa 2.
Capa de enlace de datos (Capa 2):
	Agrega un encabezado y un terminador, a cada pieza. Pasa la unidad resultante a la capa 1 para su transmisión.
![](Screenshot_2026-06-21-16-19-12_26507.png)