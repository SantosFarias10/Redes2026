# Capa de Aplicación
Las aplicaciones distribuidas necesitan coordinarse, intercambiar información y entenderse entre sí, aún cuando están en máquinas distintas, usan sistemas operativos distintos, están escritas en lenguajes distintos, se ejecutan en redes heterogéneas.

La red por sí sola no resuelve cómo dos aplicaciones acuerdan qué decirse ni cómo interpretarlo.

La capa de aplicación define **cómo las aplicaciones usan la red** para intercambiar información y coordinarse. Es donde se diseña la lógica de interacción entre programas distribuídos. Todo lo demás (transporte, red enlace) existe para que esta capa pueda funcionar.

El propósito es entender cómo se construyen las aplicaciones distribuídas y porqué se diseñan así.

La capa de aplicación responde a tres preguntas clave:
1. ¿Quién habla con quién?
	* Para esto se definen arquitecturas de red: Cliente-servidor, P2P, Híbridas. Tienen implicancias en escalabilidad, desempeño y robustez.
2. ¿Qué mensajes se intercambian y qué significan?
	* Para esto se definen protocolos de aplicación. Partes constitutivas: tipos de mensajes, sintaxis, semántica, reglas y estado. Derivaremos un ejemplo de diseño de un protocolo de transferencia de archivos (inspirado en FTP)
3. ¿Cómo se implementa esa comunicación?
	* Para esto se usarán tecnologías de implementación:
	    - sockets(bajo nivel)
	    - Web (alto nivel) como plataforma de aplicaciones
	    - Abstracciones y compromisos
# Arquitecturas de Aplicaciones de Red
### ¿Cómo organizamos una aplicación distribuida?
* La arquitectura es el esqueleto de un aplicación de red: define quien hace qué y como fluye la información.

El propósito es entender las formas fundamentales de organizar los nodos que participan en una aplicación de red y cómo esa organización condiciona la escalabilidad, la robustez, el desempeño, la distribución de responsabilidades.

### ¿Qué es una arquitectura para aplicaciones de red?
* Tipos de nodos. Cada tipo de nodo tiene responsabilidades o se ocupa de ciertas tareas. La comunicación entre nodos de dos tipos sigue un conjunto de reglas.
* La importancia de usar una arquitectura para aplicaciones de red es que sirve de guía para diseñar una aplicación de red.
Elegir una arquitectura es decidir cómo fluye la información y cómo se reparte el trabajo dentro de la aplicación.
## Arquitecturas cliente-servidor
![](Screenshot_2026-06-21-18-32-10_26302.png)
- Tenemos nodo cliente y nodo servidor
- El servidor es la componente dominante que controla el estado, coordina los clientes, almacena datos y es el único punto de acceso.
- El cliente hace pedidos de diferentes tipos y el servidor los procesa y responde.
- Características: control fuerte, simplicidad, pero poca escalabilidad sin infraestructura adicional.
### Características de los nodos:
- Para los servidores:
	* Ubicación: suele ser fija.
	* Direcciones IP: suele no cambiar 
	* Tiempo de vida: es común que sea largo.
- Para los clientes:
	* Ubicación: pueden ser fijos o móviles.
	* Direcciones IP: suelen ser dinámicas.
	* Tiempo de vida: suele ser más corta (usar el servidor y salir)
#### Cuadro de pasos que realiza una aplicación cliente-servidor que se apoya en:

| UDP                                                                                                              | TCP                                                                                                                        |
| :--------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------- |
| 1. El servidor elige un puerto y se queda esperando pedidos                                                      | 1. El servidor elige un puerto y queda esperando conexiones                                                                |
| 2. El cliente construye un paquete UDP completo con toda la información necesaria (parámetros)                   | 2. El cliente envía un pedido de conexión&nbsp;                                                                            |
| 3. El cliente envía un paquete UDP a la dirección IP y puerto del servidor                                       | 3. Cuando el servidor recibe el pedido, acepta la conexión&nbsp;                                                           |
| 4. El servidor lee el paquete UDP completo, identifica qué cliente lo envió e interpreta el pedido.              | 4. El cliente envía&nbsp; un mensaje de solicitud a través de la conexión ya establecida.                                  |
| 5. El servidor construye un mensaje de respuesta que debe caber en un solo paquete UDP&nbsp;                     | 5. El servidor recibe el mensaje en el mismo orden en que fue enviado, lo interpreta y decide qué respuesta generar        |
| 6. El servidor envía la respuesta al cliente                                                                     | 6. El servidor envía un mensaje de respuesta por la misma conexión.                                                        |
| 7. El cliente espera un paquete de respuesta por un tiempo limitado (timeout)                                    | 7. Mientras la conexión siga abierta, el cliente puede enviar uno o varios pedidos adicionales sin necesidad de reconectar |
| 8. Si ocurre el timeout, el cliente puede reenviar el pedido o abandonar según la lógica de la aplicación.&nbsp; | 8. Cuando el cliente termina de usar el servicio pide cerrar la conexión.                                                  |
|                                                                                                                  | 9. El servidor confirma el cierre y ambos liberan la conexión                                                              |

## Arquitecturas Peer to Peer (P2P)
La arquitectura cliente-servidor tiene un costo: el servidor se convierte en un punto único de carga, de falla y de control. Surge entonces el modelo peer to peer donde los nodos dejan de ser meros consumidores y pasan a ser participantes activos en la red. 

*Nodos*:
- Hosts llamados peers (compañeros)
- Puede haber dos estilos de P2P
	- ningún apoyo en servidores : P2P puro.
	- Mínimo apoyo en servidores: predominantemente P2P

**Responsabilidades:**
- Peers:
	- Piden servicios de otros peers
	- Proveen servicios en retorno a pedidos de otros compañeros
- Servidor mínimo:
	- Mantiene la lista de nodos activos semipermanentes
	- Entrega algunos compañeros iniciales a un compañero que recién entra.
	- Autenticación básica.

>[!characters] Característica de los peers
suelen tener IP dinámica y privada. Los compañeros pueden entrar y salir en cualquier momento.

La aparición de nuevos compañeros introduce:
- Más almacenamiento distribuido, más poder de cómputo.
- Mayor redundancia y resilencia (replicar datos, aumentar tolerancia a fallos de compañeros)
- Aumento del trabajo de servidores mínimos que se usan 
- Más fuentes y menor latencia promedio

Ejemplos:
- Distribución de archivos: BitTorrent, Emule
- Telefonía por internet. Skype
- Bases de datos distribuídas
- Transacciones entre pares con crypto monedas.
### Distribución de archivos
¿Cómo saber que compañeros están activos? Tener una máquina con información de compañeros a la que se le puede preguntar. En BitTorrent se llama *Tracker*. ¿Qué tareas debe hacer un tracker?
- A pedido de un compañero el tracker puede entregar la lista de los IP de los compañeros activos.
- Un compañero se registra en el tracker
- Un compañero puede informar que se retira o avisar periódicamente al tracker que sigue ahí.

Asumimos que un archivo se divide en trozos. Permite mejor manejo de velocidad de subida lenta y de bajada rápida. Provee flexibilidad: un compañero no necesita tener un archivo entero para colaborar, solo algunos trozos. En un momento dado diferentes compañeros activos tienen diferentes subconjuntos de trozos de un archivo.
#### ¿Qué trozo conviene pedir primero?
Asumir que se puede pedir a un compañero que informe qué trozos tiene de un archivo. Asumir que un compañero puede armar el cuadro de los trozos de un archivo que tienen los demás compañeros activos.
Entonces, conviene pedir los trozos menos comunes primero, para evitar que compañeros con trozos menos comunes se retiren y no pueda descargar esos trozos, viéndonos obligados a postergar la descarga del archivo.

Como la velocidad de subida es limitada, un peer no puede mandar trozos a todo el mundo que me pida, sino me puedo saturar y enviar trozos demasiado lento. Por lo tanto, un compañero va a tener que decidir a quien enviar trozos primero, por ejemplo la regla tit-for-tat: enviar trozos a aquellos X compañeros que le mandan trozos a una velocidad mayor, reevaluando periódicamente los X mejores compañeros.

Peero, un compañero puede enviar siempre trozos a los mismos compañeros y los otros compañeros se mueren de inanición. Elegir aleatoriamente cada Y segundos otro compañero al cual enviar trozos.
## Arquitecturas híbridas
El enfoque P2P funciona bien mientras la red es relativamente simple y los nodos pueden coordinarse sin demasiadas dificultades. Sin embargo, cuando la aplicación crece, aparecen necesidades más complejas: algunos nodos necesitan ayuda para conectarse, otros requieren asistencia para iniciar la comunicación, y a veces hace falta mantener cierta información organizada par que la red funciones de manera fluida.

Se vuelve necesario incorporar servidores más capaces, que no solo ayuden a encontrarse, sino que también colaboren en tareas de organización, coordinación y manejo de situaciones más complejas.

Una arquitectura híbrida combina elementos del modelo cliente-servidor y del modelo P2P para aprovechar las ventajas de ambos. En lugar de depender completamente de un servidor central o distribuir toda la responsabilidad entre pares, asignan algunas funciones de coordinación a servidores y las tareas de transferencia o procesamiento a los nodos pares. Las arquitecturas híbridas buscan un equilibrio: un sistema que pueda crecer sin colapsar, pero que mantenga puntos de referencia estables para organizar la interacción entre nodos. Aquí los servidores son más pesados

Surgen como respuesta a dos tensiones clásicas:
- La necesidad de escalabilidad y distribución de carga (fortalezas del P2P).
- La necesidad de coordinación, descubrimiento de nodos y control (fortalezas del cliente-servidor)
### Asuntos contemplados por la coordinación
- Asignación de roles: decidir quién hace qué.
- Sincronización: asegurar que las acciones ocurran en el orden correcto.
- Consistencia del estado: mantener información coherente entre nodos.
- Manejo de conflictos: resolver situaciones donde dos nodos quieren hacer cosas incompatibles (lo que genera resultados incorrectos, inconsistentes o desordenados)
### Funciones de nodos
Los **servidores** pueden:
- Hacer señalización compleja: descibrimiento, negociación, establecimiento de sesión y mantenimiento de parte del estado
- Participar parcialmente en la distribución de contenido.
- Autenticación 
- Indexación
- Coordinación 

Los **compañeros** realizan la transferencia de datos o el procesamiento distribuido. Esto permite reducir la carga del servidor sin perder orden ni coherencia en el sistema.
### Características
- Mejor control y seguridad
- Mejor experiencia del usuario
- Menos descentralización real
- Más infraestructura necesaria
## Comparación de los tipos de arquitectura

| Criterio&nbsp;            | Cliente- servidor                                                  | Predominante P2P                                                | Híbrida                                                                                                |
| :------------------------ | :----------------------------------------------------------------- | :-------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------- |
| Escalabilidad             | Limitada por capacidad del servidor.&nbsp;                         | Muy alta: la carga se reparte entre los nodos.&nbsp;            | Alta: los servidores ayudan, pero los pares trabajan.                                                  |
| Robustez                  | Baja: si el servidor falla, el servicio se cae.                    | Alta: la red funciona cuando algunos nodos se desconectan.      | Media-Alta: los servidores ayudan a estabilizar, pero no son únicos puntos de falla.                   |
| Control                   | Centralizado: el servidor decide y organiza todo.&nbsp;            | Distribuido: cada nodo participa sin jefe central.              | Mixto: algunas cosas se controlan desde servidores, otras desde los pares.                             |
| Costos de infraestructura | Altos: el servidor debe ser potente y estar siempre disponible.    | Bajo: la mayor parte lo hacen&nbsp; los nodos.                  | Medios: se necesitan servidores más capaces que en P2P, pero no tan críticos como en cliente-servidor. |
| Rendimiento bajo carga    | Puede degradarse si muchos clientes acceden al mismo tiempo.&nbsp; | Mejora con más nodos: cuantos más participantes, más capacidad. | Suele ser estable: los servidores ordenan y los pares distribuyen la carga.                            |

# Protocolos de Aplicaciones de Red
Una arquitectura por sí sola, no alcanza para que los nodos puedan colaborar: saber quién hace qué no explica cómo se comunican ni qué reglas siguen para coordinarse. Para que una aplicación distribuida funcione, los nodos necesitan un lenguaje común, un conjunto de mensajes y normas que les permita interactuar de manera coherente. 

Un **protocolo** es el lenguaje de la aplicación distribuida: sin él, no hay coordinación posible. 
El propósito, es comprender cómo dos aplicaciones acuerdan qué mensajes intercambiar, cómo interpretarlos y cuándo enviarlos, para lograr una interacción coherente y predecible.
### ¿Qué cosas definir en un protocolo de aplicación de red?
- Tipos de mensajes
- Sintaxis del mensaje
- Semántica del mensaje 
- Reglas
- Estado de la aplicación 

El **estado** es la información relevante sobre la interacción en curso que la aplicación mantiene entre mensajes, es la memoria que permite que una aplicación distribuída tenga continuidad, coherencia y sentido a lo largo de una interacción.
El estado es necesario, porque sin estado cada mensaje sería ambiguo, no habría forma de interpretar acciones en contexto, no se podrían mantener sesiones, transferencias ni operaciones compuestas y la aplicación no podría coordinarse con otras.
#### Ejemplos típicos de estado:
- Quién es el otro extremo (identidad, autenticación)
- En qué parte del diálogo estamos (fase del protocolo)
- Qué recursos estamos usando (p. ej: directorio actual, archivo abierto, etc)
- Qué se envió o recibió antes (para interpretar lo que viene después)
- Configuraciones activas: son decisiones o parámetros que la aplicación negocia o establece durante la interacción
### Ejemplo: protocolo de transferencia de archivos
El protocolo puede tener mensajes de comandos, de feedback (de respuesta a comandos), y mensajes con información (directorios, archivo)
![](Screenshot_2026-06-21-18-45-26_17280.png)
Este protocolo puede manejar la siguiente información de estado: directorio corriente, y autenticación previa.

# Tecnologías de Implementación
Un protocolo es una idea abstracta: describe la lógica de interacción, no la forma concreta de implementarla. Para que un protocolo cobre vida, necesitamos herramientas que permitan enviar y recibir datos, gestionar conexiones y materializar la comunicación entre nodos. Es aquí donde entran en juego las tecnologías de implementación, que determinan el nivel de control, la complejidad y las capacidades reales de una aplicación distribuida. 
## Sockets
Un **socket** es un punto de conexión que permite a una aplicación enviar y recibir datos por la red sin conocer los detalles internos de TCP o UDP, usando una interfaz uniforme en cualquier sistema operativo.

Un socket no es un protocolo: es una abstracción para acceder a protocolos de transporte. Los sockets se usan para invocar a TCP o UDP a través del sistema operativo.

Los sockets:
- **Unifican:** la misma interfaz de programación de aplicaciones para TCP y UDP.
- **Simplifican:** esconden la complejidad del transporte.
- **Estandarizan:** permiten que programas escritos en distintos lenguajes se comuniquen igual.
- **Aíslan:** la aplicación se enfoca en su lógica, no en la mecaánica de la red
### Socket API:
Conjunto básico de funciones que permiten a dos programas comunicarse a través de la red enviando y recibiendo datos directamente sobre protocolos como TCP y UDP. Obliga a manejar algunos detalles de transporte como puertos, direcciones, envío y recepción de datos.

Los sockets existen para que las aplicaciones puedan comunicarse por la red sin tener que implementar la red. Son la frontera entre la lógica de aplicación y los mecanismos de transporte.

![](Screenshot_2026-06-21-18-47-42_27316.png)
![](Screenshot_2026-06-21-18-48-13_11833.png) 
El **servidor TCP:** prepara un punto de encuentro
1. **Crear socket (socket)**: Preparar un punto de comunicación.
2. **Asociar a un puerto (bind):** Elegir en qué puerto va a escuchar.
3. **Escuchar (listen):** Indicar que está listo para aceptar conexiones.
4. **Aceptar conexión (accept):** Cuando llega un cliente, se crea una conexión TCP independiente.
5. **Enviar/recibir datos:** Intercambio confiable y ordenado
6. **Cerrar conexión:** Liberar recursos cuando termina la interacción.

**Cliente TCP:** inicia la comunicación.
1. **Crear socket:** Preparar su punto de comunicación.
2. **Conectar (connect):** iniciar la conexión TCP con la IP y puerto del servidor.
3. **Enviar/recibir datos:** Usar la conexión ya establecida para intercambiar mensajes.
4. **Cerrar conexión:** Indicar que terminó de usar el servicio.
## La Web
A medida que las aplicaciones crecieron en complejidad, surgió la necesidad de contar con un nivel más alto de organización: un conjunto de reglas comunes que permitiera a cualquier cliente comunicarse con cualquier servidor sin tener que reinventar todo desde cero.
Ese salto de abstracción es lo que da origen a la Web, donde protocolos como HTTP definen no solo cómo se envían los datos, sino también qué significan y cómo deben comportarse los participantes. 

La web se apoya en sockets, pero los envuelve en un marco mucho más estructurado, estandarizado y fácil de usar. Pasamos así de construir la comunicación "a mano" a trabajar con un ecosistema que ya trae convenciones, formatos y comportamientos bien definidos.

La Web es una plataforma de aplcacones de red basada en protocolos estandarizados (como HTTP/HTTPS) que permite acceder a información y servicios desde cualquier navegador, sin manejar detalles de bajo nivel como sockets.

**Ventajas de la web:**
- Se trabaja con una abstracción más alta al no tener que gestionar paquetes, puertos, ni protocoloes de capa de transporte.
- Cualquier dispositivo con navegador puede usar la aplicación sin instalar nada.
- La Web permite enfocarse en la lógica de la plicación en lugar de la infraestructura de red 
- Permite interoperabilidad entre lenguajes, sistemas y dispositivos
- Es la base de la mayoría de las aplicaciones modernas.

| Criterio                            | Sockets                                                        | Web                                                              |
| :---------------------------------- | :------------------------------------------------------------- | :--------------------------------------------------------------- |
| Nivel de abstracción                | Bajo: control directo sobre el envío y la recepción de datos   | Alto: el protocolo ya define estructura, significado y reglas    |
| Flexibilidad                        | Muy alta: se puede diseñar cualquier protocolo propio.         | Media: se siguen reglas del protocolo HTTP y sus métodos.        |
| Estructura de los mensajes          | La define el programador desde cero.                           | Ya viene estandarizada (p. ej: con métodos, encabezados, cuerpo) |
| Facilidad de uso&nbsp;              | Requiere más trabajo&nbsp; cuidado con detalles                | Más simple: muchas tareas ya están resueltas.<br><br>            |
| Control sobre la comunicación&nbsp; | Total: tiempos, formato, orden, flujo                          | Parcial: HTTP decide gran parte del comportamiento               |
| Interoperabilidad                   | Limitada: ambos extremos debern implementar el mismo protocolo | Muy alta: cualquier navegador puede comunicarse&nbsp;<br><br>    |
| Costos de desarrollo                | Más altos: hay que diseñar y mantener el protocolo.            | Más bajo: usan estándares y herramientas existentes.             |
| Seguridad                           | Depende de lo que programes                                    | HTTPS resuelve cifrado y autenticación básica                    |


### Elegir Sockets cuando:
- necesitas máximo control sobre la comunicación;
- quieres diseñar un protocolo propio; 
- la aplicación requiere baja latencia o comunicación continua (juegos, chat, streaming);
- el formato de los mensajes debe ser muy eficiente.
### Elegir Web (HTTP/HTTPS) cuando:
- quieres simplicidad y evitar reinventar protocolos; 
- necesitas interoperabilidad con navegadores, móviles o servicios externos;
- vas a crear un sitio web o una aplicación web;
- buscas herramientas maduras, seguridad integrada y facilidad de pruebas.
