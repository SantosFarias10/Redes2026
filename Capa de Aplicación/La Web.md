# La Web
La Web es un sistema global que permite acceder y compartir información mediante documentos interconectados. Funciona sobre Internet, pero agrega un conjunto de tecnologías - como el modelo cliente-servidor, los **navegadores**, los servidores web y el protocolo HTTP- que hacen posible la experiencia que usamos a diario.
## Páginas Web
En la web, hay páginas web. xd
### Páginas web estáticas
Una página web estática es un documento accesible a través de la web que un navegador puede solicitar, interpretar y mostrar al usuario.
Una página web estática tiene distintos elementos:
- Referencias a medias (imagen JPEG, archivo de audio, video MPEG, icono GIF)
- Vínculos a otras páginas web
- Texto
Considerando los vínculos a otras páginas, se forma un grafo de páginas web (llamado hipertexto) para páginas de sólo texto, e hipermedia para páginas conteniendo medias 

La web original = hipertexto + transferencia de archivos. 
Vemos 3 tipos de páginas web:
- **Páginas web estáticas:** siempre muestra lo mismo; son documentos.
- **Páginas web dinámicas:** son generadas y cambian según datos del usuario o de bases de datos. Pueden usar parámetros y bases de datos, y su apariencia puede ser como la de una página web estática, pero generada por código.
- **Página única:** toda la interfaz del usuario (UI) está en una página; pedidos de información/datos al servidor. Su apariencia suele parecerse más a la UI de las aplicaciones de escritorio que a páginas web estáticas.
## Sitios web  y aplicaciones web
Un **sitio web** es un conjunto de páginas web relacionadas (usualmente estáticas) producida por una organización o persona; con único nombre de dominio y en al menos un servidor web.

Una **aplicación web** es un servicio al que el usuario accede desde su navegador para realizar tareas, resolver problemas o gestionar información, sin necesidad de instalar software en su computadora. Una aplicación web procesa lógica, toma decisiones, interactúa con bases de datos y genera contenido dinámico según lo que el usuario hace.

Para que una aplicación web sea una social media web app, hay que agregar:contenido creado y compartido por el usuario, y comunicación entre las personas: posts, comentaros, mensajes.
## Infraestructura para la Web
Las páginas y aplicaciones no existen aisladas: requieren  una infraestructura que permita su distribución y acceso. En este bloque analizaremos los componentes que hacen posible la comunicación entre navegadores y servidores, y cómo se integran para ofrecer una experiencia coherente.

La web se apoya sobre una arquitectura cliente-servidor. El cliente ejecuta en un navegador (browser) El servidor ejecuta en un servidor web.
Necesitamos en la web, un tipo de protocolo que permita solicitar páginas web (indicando parámetros si es preciso) y recibir páginas web (se usa HTTP). Para especificar la estructura de la página web, se usa un lenguaje con sintaxis apropiada, HTML.

Un buscador web es un servicio que permite encontrar páginas en la web. Para ello el usuario ingresa una **expresión conteniendo palabras clave**, el buscador para responder consulta un índice (que relaciona palabras clave con páginas). El buscador web retorna una lista ordenada de páginas con textos explicativos.
Entonces, la tecnología para la web inicial es: HTTP,  browsers, servidores web, HTML.
### ¿Cómo integrar browser,servidor web, HTTP y HTML?
- El browser pide (usando HTTP) una página/objetos a un servidor web. 
- El servidor web retorna (usando HTTP) la página/objetos pedidos.
- Si el servidor retornó una página en HTML, el browser interpreta el texto HTML y despliega la página en una pantalla.
![](Screenshot_2026-06-21-18-53-45_23143.png)

## Identificación de páginas web y formularios
Para que un navegador pueda solicitar contenido, necesita identificarlo correctamente. Para ejecutar páginas dinámicas se necesita estudiar cómo se envían datos desde el usuario hacia el servidor. Esto nos introduce en el uso de URLs, parámetros y formularios, elementos esenciales para la interacción dinámica.
### ¿Qué informaciones necesito usar para identificar una página estática?
- Nombre de dominio de host que contiene la página (para la institución)
- Camino a la página (en el sistema de archivos)
- Nombre del documento que contiene la página (página estática)
![](Screenshot_2026-06-21-18-54-51_26987.png)
### ¿Qué informaciones necesito para identificar una página dinámica?
- Informaciones anteriores (URL)
- Parámetros (el nombre y el valor)
- Por ejemplo:`` demo_get2.asp?fname=Henry&name=Ford ``

>[!tip] Para ingresar parámetros de página dinámica usar formulario. 
### ¿Qué tipos de comandos se podrían usar para editar un formulario (es decir, para ingresar los parámetros de una página dinámica)? 
Campos de texto (con scroll o no), selección simple (radio button), selección múltiple (check box), botón para enviar formulario.
Para ejecutar una página dinámica hace falta vincularla a un formulario, se indica el vínculo en la descripción del formulario.
#### ¿Cómo indicar que se ejecute una página dinámica?
Al presionar botón de envío de formulario se hace pedido al servidor de ejecución de página dinámica con parámetros de formulario.
![](Screenshot_2026-06-21-18-55-51_16471.png)

## Navegadores web
El navegador es la puerta de entrada del usuario a la Web. Analizaremos cómo decide qué tipo de documento recibe, como lo procesa y qué mecanismos utiliza para mostrarlo en pantalla.
### ¿Cuándo llega un documento del servidor web, cómo se sabe el navegador de qué tipo de documento se trata?
- No todas las páginas contienen solo HTML. Junto con la página que devuelve el servidor indica de que tipo de documento se trata (llamado tipo MIME)
- El browser tiene un conjunto de tipos MIME integrados que sabe desplegar (como text/HTML)).
### ¿Cómo hacer si el tipo MIME de una página no es de los integrados?
Para ver documentos de tipos MIME no integrados se usan visores. Se usa una tabla de tipos MIME que asocia un tipo MIME con un visor.
### ¿Cuáles son los pasos para averiguar el IP de un URL?
- El navegador revisa en el caché local si ya se conoce el IP del dominio. 
- Si no está en caché, se usa UDP para enviar una consulta DNS al resolver configurado (puerto de destino 53)
- EL resolver devuelve usando UDP la IP final.
- Entonces el browser va a saber a qué IP conectarse.
### ¿Cuáles son entonces los pasos usando TCP?
- EL cliente inicia conexión TCP con servidor web usando puerto 80.
- El servidor web acepta la conexión 
- Mensajes HTTP de pedido y de respuesta son intercambiados entre el browser y el servidor web usando el canal TCP.
- La conexión TCP se cierra.
## Servidores web
### ¿Cómo sería un servidor web con un solo hilo de ejecución?. Tener en cuenta que:
- El servidor solo puede hacer una cosa por vez.
- Maneja las conexiones entrantes en secuencia.
- Si el cliente tarda en enviar datos, el hilo queda bloqueado. No puede aceptar nuevas conexiones ni procesar otras solicitudes.
- El servidor parsea encabezados, ejecuta la lógica de la aplicación, consulta archivos o bases de datos, genera la respuesta. Todo esto ocurre sin la posibilidad de atender a otros clientes. 
- El servidor puede usar una caché de páginas estáticas en l amemoria. Acceder a páginas en la caché es mucho más rápido que accederla en disco. 
#### La secuencia de pasos entonces queda:
1. Acepta conexión (ACCEPT)
2. Lee solicitud (READ)
3. Procesa la lógica
4. Genera respuesta (WRITE)
5. Cierra la conexión (CLOSE)
6. Go to 1
#### ¿Cómo podría estar organizado internamente un servidor web concurrente?
Uso de discos: SSD para contenido estático (HTML, imágenes, etc.), HDD para logs o backups. Cada disco maneja una parte distinta del contenido.
### Tipos de hilos:
Front end (distribuye peticiones), módulo de procesamiento - MP (procesa peticiones y responde al cliente)
### Servidores web concurrentes:
![](Screenshot_2026-06-21-18-57-30_12407.png)


## Protocolo para la Web
Metas: derivar un protocolo para la web.
**¿Qué cosas debería considerar un protocolo para la web?**
- Formatos de mensajes para pedido de páginas estáticas, objetos de ejecución de páginas dinámicas.
- Formatos de mensajes para operaciones que mantienen sistema de archivos en servidor web.
- Formato de mensajes para enviar páginas a browser 
- Manejo de información de estado de sesión.
- Seguridad: encriptación de mensajes
- Retroalimentación cuando no se pueden responder los pedidos.
- Comunicación confiable.

**¿En qué protocolo de capa de transporte conviene apoyarse para definir un protocolo para la web?**
Conviene TCP porque una respuesta puede requerir varios paquetes y al fragmentar al información aparecen los problemas que resuelve TCP.

**¿Qué tipos de mensajes necesitamos para un protocolo para la web?**
Mensajes de pedido y mensajes de respuesta.

Si tenemos que diseñar desde cero cómo un navegador usa conexiones TCP para descargar una página y todos sus recursos, podríamos utilizar dos estrategias:
- No persistencia: Una conexión por recurso, y una conexión para la página (en HTTP 1.0)
- Persistencia: Una sola conexión para todo en HTTP 1.1. Los pedidos son procesados en orden y los resultados se mandan en orden.
### Situaciones:
- Asumir que usamos una sola conexión persistente y enviamos los pedidos en orden, recibiendo también las respuestas en orden
#### Caso 1: imagina que un recurso tarda mucho, ¿Qué sucede?
- Si una respuesta se demora, bloquea todas las demás.
- Por lo tanto hay que evitar que esto suceda.
==Soluciones posibles==:
1. **Alterar el orden de las respuestas a los pedidos:**
   Para que las respuestas rápidas no tengan que esperar; así puede mostrares lo más posible de manera rápida.
2. **Mezclar pedacitos de varias respuestas:**
   Así se evita que una respuesta  grande bloquee a las demás en la misma conexión ; el navegador puede recibir primero lo que necesita antes.
3. **Indicar prioridades para pedidos de recursos:** 
   Evita que recursos pesados eviten la preparación de recursos livianos.
#### Caso 2: Supongamos que modificamos el protocolo para reducir la cantidad de pedidos de recursos (osea, el servidor puede enviar recursos sin que el cliente los pida uno a uno) ¿Cuál ventaja tiene hacer esto?
Cada pedido tradicional implíca:
1. El cliente detecta que necesita un recurso
2. Envía un request
3. Espera un RTT
4. Recién entonces empieza a recibir la respuesta
Si eliminamos el paso 1-2 (porque el servidor ya sabe que recursos vas a necesitar), te ahorras al menos un RTT por recurso. En redes móviles o de alta latencia, esto es oro.
#### Caso 3: Supongamos que modificamos el protocolo para indicar prioridades para pedidos de recursos, ¿Qué beneficios trae esto?
El servidor  puede procesar antes los pedidos prioritarios, generar antes sus datos y tenerlos listos para enviar apenas le toque el turno. Esto reduce el tiempo hasta que el cliente recibe lo crítico; permite reducir latencia percibida sin romper el orden de las respuestas. 
Evitar que recursos pesados retrasen la preparación de recursos livianos.
#### Solución implementada por HTTP 2.0:
- Enviar muchos pedidos y muchas respuestas en paralelo dentro de una misma conexión, intercalando fragmentos ("frames") de cada uno.
- El servidor web puede enviar recursos antes de que el cliente los pida.
- El clente puede indicar prioridades para el envío de recursos.
### Formato de los pedidos
**¿Que tipos de pedidos hay a un servidor en un protocolo para la web?**
- Pedido de página estática
- Pedido de página dinámica
- Pedido de datos
- Pedido de operación sobre sistema de archivos del servidor web (agregar página, borrar página)

**¿Qué tipos de inforamciones conviene poner en un mensaje de pedido en un protocolo para la web? **
- Campo para versión del protocolo
- Campo URL (para pediod de todo tipo de páginas)
- Campo que indica para qué es el pedido (operación sobre Sistema de archivos, pedido de páginas, etc)
- Campo de datos (para enviar parámetros de formulario, para enviar documento a subir en servidor web) de tamaño variable.
- Campos de informaciones adicionales (con nombre de la información y valor de la misma). La cantidad de estos campos varía de un pedido a otro.
![](Screenshot_2026-06-21-19-03-15_17507.png)
### Formato de las respuestas
**¿Qué tipos de informaciones conviene poner en un mensaje de respuesta el servidor web en un protocolo para la web?**
- Mensaje de feedback sobre el pedido.
- Campo con documento enviado (página estática o página generada, datos) de tamaño variable
- Campos con informaciones adicionales (con nombre de la información y valor de la misma). Cantidad de variables de estos. Por ejemplo, datos del servidor, tipo de contenido de la respuesta, longitud del contenido.
**Partes de una respuesta HTTP**
1. Línea de estado
2. (opcional) encabezados de respuesta
3. Luego viene el cuerpo de la respuesta: por ejemplo
   - página estática usando archivo en formato HTML
   - datos pedidos usando documento en formato XML
![](Screenshot_2026-06-21-19-04-36_27720.png)
## Manejo de estado de sesión 
**Propósito**: derivar cómo manejar el estado de sesión en la web y entender la solución de las cookies.
HTTP no recuerda nada entre un pedido y otro, pero las aplicaciones sí necesitan memoria. 
Al usar una aplicación web,  HTTP a veces si recuerda algunas cosas, como el contenido del carrito de compras.
![](Screenshot_2026-06-21-19-06-30_28286.png)

**¿Qué es el estado de sesión de una aplicación web?** 
Es la información que la aplicación guarda sobre el usuario entre una petición y la otra para mantener continuidad, personalización y seguridad en su experiencia.

**¿Cómo nombrar y dar valor a una información de estado de sesión?**
Usando un par: nombre de la información y valor

**¿Dónde hace falta saber el estado de sesión?¿Del lado del cliente o del lado del servidor?**
Considerar el ejemplo de un carrito de compras en una app de comercio electrónico

**¿Donde almacenar el estado de sesión?**
- Del lado del browser:
  Por ejemplo: las informaciones de estado se llaman cookies y se guardan en un directorio de cookies.
- Del lado del servidor: 
	- En base de datos tradicional (en disco). Por ejemplo: base de datos relacional basada en SQL.
	- En memoria RAM, no en disco (esto es mucho más veloz). Por ejemplo: Redis (puede estar en la máquina del servidor web como otro proceso o en otra máquina en la nube).
	    - El servidor genera un session ID;
	    - ese ID se guarda en una cookie;
	    - el servidor guarda en Redis un objeto asociado a es ID con las informaciones de estado de sesión.
### Situaciones:
**(Asumir que la información de estado se guarda en el cliente, y se quiere hacer un pedido al servidor )**
#### Caso 1: ¿Cómo identificar la información de estado de sesión  que hay que mandar junto con un pedido  servidor web?
Considerar que:
- No todas las informaciones de sesión son para la misma aplicación web que corre en el servidor.
- No todas las informaciones de sesión para la aplicación son para todas las páginas dinámicas.
Entonces,
- Para una información de estado de sesión guardar el dominio para el que se va a usar
- Para una información de estado de sesión guardar el camino y nombre de página dinámica que se va a usar. Con los cookies solo se guarda el camino.

**(Asumir que la información de estado de sesión se guarda en el cliente, y que el servidor web modificó cierta información de estado de sesión como consecuencia de un pedido el cliente)**
#### Caso 2: ¿Qué hay que hacer para tener esa información de estado de sesión actualizada en el cliente?
- El servidor debe enviar explícitamente esa información en la respuesta
- El cliente la reemplaza en su almacenamiento local
- el cliente debe usar ese nuevo estado en las peticiones
### Duración de estado de sesión 
**Si no controlamos la duración del estado de sesión, aparecen problemas de seguridad, datos viejos, sesiones que nunca mueren, inconsistencias entre cliente y servidor y una mala experiencia de usuario**
#### ¿Cómo expresar la duración de una información de estado de sesión? Guardar el día y la hora en que va a expirar una información de estado de sesión.
Supongamos que la información de estado de sesión se guarda en un folder del cliente
- ¿Qué hacer cuando expira una información de estado de sesión?
  El browser puede borrar esa información (borrado periódico)
- ¿Qué hacer con información de estado de sesión cuando el usuario sale del browser?
  El browser puede eliminar esa información de estado de sesión cuando el usuario sale del browser.
- ¿Qué hacer si el servidor quiere que una información de estado de sesión que aún no expiró sea eliminada?
  El servidor al responder envía la información de estado de sesión con una fecha caducada.
## Páginas dinámicas 
**Repaso**: páginas dinámicas son páginas web generadas por programas que se ejecutan del lado del servidor. Pueden ser programadas en lenguaje de programación o de scripting.
#### Pasos gruesos para generar una página dinámica:
1. El mensaje es entregado a un programa para procesarlo.
2. El programa solicita la información a un servidor de bases de datos.
3. El servidor de bases de datos responde con la información requerida
4. El programa genera una página HTML personalizada y la envía al cliente.
5. El browser muestra la página recibida al usuario.
![](Screenshot_2026-06-21-19-08-27_19412.png)
### ¿Qué tareas finales suelen hacer las páginas dinámicas?
- Procesar parámetros de formularios
- Procesar información especial del pedido (ej: encabezados de pedido HTTP )
- Pedir datos a fuentes de datos (ej bases de datos)
- Generar página web con los datos recibidos
- Generar feedback e informaciones especiales de respuesta 
Tecnologías para producir páginas dinámicas: PHP, Java Server Pages, etc.

[[PHP]]
>[!tips] Acordarse en poner el link a PHP
## Cuadro la Web

![](Screenshot_2026-06-21-19-10-13_1648.png) 
![](Screenshot_2026-06-21-19-10-36_11525.png)