# Hiper Mega Ultra Resumen Definitivo, Duran viejo gaga no me vas a ganar
La capa de transporte provee comunicación lógica entre procesos de aplicación en diferentes hosts. O sea, los procesos se comunican como si estuvieran directamente conectados. La capa de red sólo comunica hosts; la CT va más allá: comunica procesos.
#### ¿Qué mejora con respecto a la capa de red?
* Retransmisión de paquetes perdidos en redes no orientadas a la conexión.
* Regula la tasa de transmisión ante congestión en la red.
#### ¿Dónde se ejecuta la capa de transporte?
Se ejecuta por completo en los hosts/sistemas finales (computadoras de origen y destino). Los routers y switches intermedios no participan: Sólo reenvían paquetes sin inspeccionar la capa de transporte.
#### ¿De quién depende la capa de transporte?
La capa de transporte depende de la capa de red (IP) para mover paquetes entre hosts pero agrega: entrega confiable, control de flujo y multiplexación.
#### ¿Qué son las entidades de transporte?
**Entidad de transporte (ET)**: Es el software (o hardware) que implementa la lógica de la capa de transporte en cada host.
### Problemas que resuelve la capa de transporte
La Capa Transporte soluciona problemas de la comunicación en red:
- **Confiabilidad:** uso de temporizadores y retransmisiones para recuperar paquetes perdidos.
- **Direccionamiento:** identificar el proceso correcto en el host destino. El proceso destino podría no estar activo, o el cliente podría no saber cuál usar.
- **Eficiencia:** uso de búferes para una comunicación confiable sin desperdiciar recursos.
- **Control de flujo:** evitar que un emisor rápido sature a un receptor lento
- **Control de congestión:** regular la cantidad de paquetes inyectados a la red: evitar que los búferes de los nodos intermedios se saturen y pierdan (hagan drop de) paquetes. Si la capa de red pierde paquetes por congestión, la CT puede compensarlo.
### Segmento y encapsulamiento
![](Segmento.png)
Los **Segmento** son la **unidad de datos del protocolo de transporte**.
Los segmentos se encapsulan en paquetes (Capa Red) y luego en tramas (capa de enlace). 
#### ¿Qué es el encapsulamiento?
El Encapsulamiento es agregar un encabezado de la capa inferior al contenido de la capa superior, como una carta dentro de un sobre dentro de una caja.
#### ¿Qué es el ACK?
Confirmaciones de recepción (ACK): El receptor notifica al emisor que los datos llegaron bien.
#### ¿Qué se confirma?
Tanto paquetes de datos como paquetes de control.
### Paquete
Un **paquete** es una **unidad de datos** que viaja por la red.
- Pertenece a la **Capa de Red** (por ejemplo, IP).
- Tiene:
    - **Cabecera** (dirección origen/destino, TTL, etc.)
    - **Datos**.
- Es lo que los routers reenvían.
### Segmento
- Pertenece a la **Capa de Transporte** (TCP o UDP).
- Es la unidad de datos que maneja el protocolo de transporte.
- Contiene:
    - Cabecera de transporte (puertos, número de secuencia, etc.)
    - Datos de la aplicación.
En TCP se llama **segmento** En UDP muchas veces se dice **datagrama UDP**.
## TCP
TCP es el protocolo de transporte más usado en internet. Garantiza que los datos lleguen completos, en orden y sin errores, incluso cuando la red debajo (IP) no lo garantiza.
### Modelo de Servicio
- **Flujo de bytes confiable** (stream): La aplicación ve una 'tubería' continua de bytes entre origen y destino
- **Adaptativo**: Ajusta velocidad y retransmisiones según las condiciones de la red en tiempo real.
### Problemas que resuelve
TCP aborda los siguientes problemas:
- **Retransmisión de paquetes**: Mediante números de secuencia, ACKs y temporizadores.
- **Duración de temporizadores**: Algoritmos complejos para fijar tiempos de espera adaptativos.
- **Manejo de conexiones**: Estableces y terminar conexiones entre pares de procesos.
- **Direccionamiento**: Identificar procesos en hosts remotos mediante puertos. 
- **Control de congestión**: Regular la inyección de paquetes para no saturar la red.
- **Control de flujo**: Evitar que un emisor rápido desborde a un receptor lento.
TCP se hace cargo de todas las responsabilidades de la capa de transporte.
### Flujo de datos y Segmentación
#### ¿Cómo maneja los datos de la aplicación?
- La aplicación envía un flujo contínuo de bytes a TCP. TCP no conoce los límites de los mensajes de la aplicación: solo ve un **stream de bytes**.
- TCP fragmenta ese flujo en segmentos de hasta 64 KB porque la red tiene límites de tamaño por paquete.
- Cada segmento se envía dentro de un datagrama IP independiente.
#### Flujo completo (de punta a punta)
App origen -> ETCP fragmenta -> IP encapsula -> red transporta -> ETCP destino en reensambla---> App destino
La app destino recibe el mismo flujo de bytes, como si estuviera conectada directamente.
### Sockets y conexiones
Para usar TCP, tanto el cliente como el servidor crean sockets (puntos de acceso al servicio).
**Dirección de socket = dirección IP + número de puerto.**
Se debe establecer una conexión explícita entre un socket emisor y uno receptor. Un mismo socket puede tener múltiples conexiones simultáneas. Cada conexión se identifica por el par: `<socket_origen, socket_destino>`.
### Números de secuencia y Segmentos
Cada byte del flujo de datos tiene su propio número de secuencia de 32 bits. Esto impone un límite teórico al tamaño del flujo $2^{32}$ = 4 GB.
Los números de secuencia permiten al receptor enviar confirmaciones de recepción (ACK) precisas. Permiten detectar datos faltantes, duplicados y desordenados.
```
Segmento = Encabezado TCP +datos (0 o más bytes)
```
#### ¿Qué limita el tamaño de un segmento TCP?
- **Límite IP**: Cada segmento debe caber en la carga útil del datagrama IP en la capa de red (máx. 65.515 bytes)
- **MTU** (**Unidad Máxima de Transferencia**): Cada red física impone un límite al tamaño de trama. El segmento debe caber en la MTU. En la práctica, la MTU suele ser de 1500 bytes (carga útil de Ethernet)
El **tamaño máximo de segmento** (MSS) es el menor entre el límite IP y la MTU de la red, desconectando encabezados.
En Ethernet: MSS típico ~ 1460 bytes (1500-20 IP -20 TCP).
### Temporizadores y retransmisiones
La capa de red (IP) no garantiza que los datagramas (paquetes) se entregarán, ni que llegarán correctamente.
#### Solución 
- Si un segmento llega correctamente -> el receptor envía un ACK (confirmación).
- Si el temporizador expira sin recibir ACK -> TCP retransmite el segmento.
- TCP es responsable de gestionar los temporizadores y ejecutar las retransmisiones según sea necesario.
#### Problema
Los datagramas pueden llegar fuera de orden. En redes de datagramas, cada paquete puede tomar rutas diferentes y llegar en orden distinto al de envío. Esto es un problema porque la capa de aplicación, en muchos casos, necesita procesar los datos en el orden original del envío. 
#### Solución de TCP 
Usa los números de secuencia para reensamblar los segmentos en la secuencia correcta antes de entregarlos a la aplicación.
### Mecanismo de ACK
Funciona de la siguiente manera: 
1. Al enviar un segmento, el emisor inicia un temporizador.
2. Al llegar al destino, la ETCP receptora responde con un segmento que contiene el número de confirmación (ACK number).
	* **El ACK number es el siguiente número de secuencia que espera recibir**.
	* El ACK puede ir acompañado de datos (si hay algo para enviar en el sentido inverso, en una comunicación bi-direccional).
3. Si el temporizador expira antes de recibir el ACK → el emisor retransmite el segmento.
#### Desafíos del orden y retransmisión
1. **Segmentos fuera de orden**
	* Por ejemplo: los bytes 3072–4095 pueden llegar antes que los bytes 2048–3071.
	* **Consecuencia:** TCP debe almacenar los segmentos adelantados en un búfer de reordenamiento y esperar a que lleguen los faltantes antes de entregar a la aplicación.
	* Tampoco puede confirmar un hueco: si llegó 1–999 y 2000–2999, solo puede confirmar hasta 999.
2. **Segmentos retardados**
	* Si un segmento tarda más que el temporizador, TCP lo retransmite (posiblemente de forma innecesaria si el original aún está en tránsito).
	* **Solución:** el receptor descarta duplicados usando los números de secuencia (ya tiene esos bytes → los ignora).
#### ¿Qué pasa al retransmitir un segmento?
Las retransmisiones pueden incluir rangos de bytes diferentes al segmento original.
* #### ¿Por qué?
	* Al momento de retransmitir, la aplicación puede haber agregado nuevos datos al búfer de envío. TCP puede combinar los bytes pendientes en un nuevo segmento de tamaño diferente. 
	* **Consecuencia para el receptor**: El receptor no puede asumir que los segmentos llegan con los mismos rangos que fueron enviados originalmente. Debe llevar un control byte a byte de qué bytes se recibieron correctamente.
### Estructura del encabezado
Todo segmento TCP tiene tres partes:
1. **Encabezado fijo**: 20 bytes. Contiene puertos, números de secuencia/ACK, flags y control de flujo. (fíjate que son las primeras 5 filas del cuadro 5 * 32 bits = 160 bits /8= 20 bytes)
2. **Opciones**: Longitud variable (en palabras de 32 bits). Negocian parámetros como MSS.
3. **Datos**: (Opcionales): Un segmento puede ser solo encabezado (ej.: un ACK puro).
#### Estructura del encabezado
![](EstructuraDeUnSegmento.png)
- **Segmentos sin datos**
	* Se usan para enviar ACKs y mensajes de control (SYN, FIN). Solo contienen el encabezado TCP, sin datos de aplicación.
- **Puerto de origen y puerto de destino**
	* Cada uno ocupa 16 bits (valores de 0 a 65.535). Puertos conocidos: 80 (HTTP), 443 (HTTPS), 22 (SSH), 25 (SMTP). 
	* IP + puerto = socket (punto terminal único de 48 bits que identifica un proceso en un host).
		* **El par `<socket_origen, socket_destino>` identifica de forma única cada conexión TCP.**
- **Número de secuencia (32 bits)**
	* Identifica la posición del primer byte de datos del segmento dentro del flujo de bytes total. 
	* Por ejemplo: Si el flujo empieza en byte 0 y un segmento contiene los bytes 1000-1499, su número de secuencia es 1000.
- **Número de confirmación (ACK number, 32 bits)**
	* Indica el siguiente byte que el receptor espera recibir. Por ejemplo: Si recibió correctamente hasta el byte 1499, el ACK number será 1500. 
	* Confirma implícitamente todos los bytes anteriores (ACK acumulativo).
### Flag ACK (1 bit en el encabezado)
- Si `ACK = 1`: el campo «número de confirmación» es válido → el segmento confirma datos recibidos.
- Si `ACK = 0`: el campo se ignora → no hay confirmación en este segmento.
	* **Piggybacking**: ACKs «gratis» con datos: En la práctica, casi todos los segmentos (excepto el primer SYN) llevan ACK = 1, porque TCP aprovecha cada envío para confirmar lo recibido. 
	* Ejemplo concreto: `A` envía datos (seq=1000, 500 bytes) → `B` responde con datos propios y ACK=1500 en el mismo segmento → confirma los 500 bytes de `A` sin un paquete extra.
* **Longitud del encabezado (4 bits)**
	* Indica el número de palabras de 32 bits en el encabezado TCP. Es necesario porque el encabezado tiene tamaño variable (por las opciones).
	* Mínimo: 5 palabras = 20 bytes (encabezado fijo, sin opciones). Máximo: 15 palabras = 60 bytes (con opciones llenas).
- **Campo de opciones (longitud variable)** 
	- Permite negociar parámetros entre emisor y receptor al inicio de la conexión.
		- MSS: tamaño máximo de segmento que acepta cada lado.
		- Window Scale: ampliar la ventana de recepción más allá de 64 KB.
		- SACK: confirmaciones selectivas (confirmar bloques no contiguos). Máximo espacio para opciones: 40 bytes (60 - 20 del encabezado fijo).
### Flags del encabezado
* **URG (Urgent)**: Indica que el segmento contiene datos urgentes que deben procesarse de inmediato. El campo Urgent Pointer acompaña este indicador y señala la posición en el flujo de datos donde terminan los datos urgentes.
* **PSH (Push)**: Sirve para pedir al receptor que procese y entregue los datos inmediatamente al nivel superior(aplicación) en lugar de esperar a completar el buffer. Esto se usa en escenarios donde la inmediatez es clave.
* **RST (Reset)**: Se utiliza para reiniciar una conexión. Se puede enviar, por ejemplo, cuando hay un error crítico en la comunicación o se quiere rechazar una conexión no deseada.
* **Urgent Pointer**: Complementa el indicador URG. Su propósito es especificar la ubicación del último byte de datos urgentes dentro del segmento.
* **CWR (Congestion Window Reduced) y ECE (Explicit Congestion Notification Echo)**: Relacionados con el manejo de congestión en la red. CWR indica que el transmisor ha reducido su ventana de congestión. ECE señala que el receptor ha detectado congestión a través de notificaciones explícitas.
## Direccionamiento
#### Problema
El direccionamiento explícito de los destinos.
#### ¿Cómo hacer para que un proceso servidor adecuado atienda a las necesidades de una máquina cliente?
Consideraremos los siguientes casos
### Caso 1: El cliente necesita un tipo de servicio, sin embargo no sabe cuál proceso servidor es adecuado para contactar.
Asumimos que los procesos servidores están activos.
#### ¿Cómo hacer que el cliente se entere de un proceso servidor para un tipo de servicio?
* #### Solución
	* Existe un proceso especial llamado **servidor de directorio** que para cada tipo de servicio sabe cuáles son los puertos de los servidores que prestan ese tipo de servicio. Pasos seguidos:
		1. El usuario establece una conexión con el servidor de directorio (que escucha en un puerto bien conocido).
		2. El usuario envía un mensaje especificando el nombre del servicio.
		3. El servidor de directorio le devuelve la dirección puerto.
		4. El usuario libera la conexión con el servidor de directorio y establece una nueva con el servicio deseado
#### ¿Cómo se hace cuando se crea un servicio nuevo?
El servicio nuevo debe registrarse en el servidor de directorio, dando su nombre de servicio como la dirección de su puerto. El servidor de directorio registra esta información en su base de datos.
### Caso 2: Servidor inactivo -> Protocolo Inicial de Conexión
#### Problema
El cliente conoce al proceso servidor adecuado, pero este se encuentra inactivo y no puede responder a solicitudes. El servidor existe en la máquina remota, pero su proceso no está corriendo en ese momento, entonces se necesita un mecanismo para activar al servidor bajo demanda cuando llega una solicitud.
#### Solución 
Usar un **servidor de procesos** (protocolo inicial de conexión), un intermediario que activa servidores inactivos cuando se los necesita.
 - El servidor de procesos escucha en los puertos de servidores de menor uso que están inactivos.
 - Cuando recibe una solicitud, genera (fork) el servidor correspondiente y le transfiere la conexión del cliente.
 Pasos detallados (con cuadro al final):
1.  **Escucha múltiple:** El servidor de procesos escucha en un grupo de puertos simultáneamente, esperando solicitudes de conexión entrantes de clientes.
2. **Solicitud CONNECT:** Un cliente emite una solicitud CONNECT,  especificando el número de puerto del servicio que desea utilizar.
3. **Redirección:** Si no hay ningún servidor activo esperando en ese puerto, la solicitud llega al servidor de procesos, quien obtiene la conexión.
4. **Generación (fork):** El servidor de procesos genera (fork) el servidor solicitado y le permite heredar la conexión existente con el cliente, de modo que la comunicación continúa sin interrupción
5. **Servicio activo:** El nuevo servidor recientemente activado realiza el trabajo solicitado por el cliente. Mientras tanto, el servidor de procesos vuelve a escuchar en los puertos, listo para atender nuevas solicitudes entrantes.
![](ProtocoloInicialDeConexion.png)
### Puertos bien conocidos
Son los puertos con números menores a 1024, reservados para servicios estándar del sistema. Estos números están asignados por la IANA (internet assigned numbers Auth)
#### ¿Qué son los daemons?
**Demonios (daemons):** Son procesos servidores que se ejecutan en segundo plano y se asocian a un puerto específico en el momento del arranque del sistema.
Por Ejemplo: El demonio FTP se conecta al puerto 21 durante el arranque y queda escuchando solicitudes de transferencia de archivo.
#### Problema
Si cada servicio tiene su propio demonio ejecutándose permanentemente, se podría llenar la memoria con demonios que están inactivos la mayor parte del tiempo, desperdiciando recursos del sistema.
#### Solución
Un único demonio llamado inetd (Internet daemon) reemplaza a múltiples demonios individuales. Este proceso:
- Escucha simultáneamente en un conjunto de puertos y espera solicitudes de conexión entrantes. 
- Cuando un cliente emite un pedido CONNECT a un puerto específico, inetd verifica si ya hay un servidor escuchando en ese puerto.
- Si no hay servidor activo, inetd bifurca (fork) un nuevo proceso, ejecuta el demonio apropiado en él, y ese demonio maneja la solicitud del cliente.
#### Archivo de configuración de inetd
- inetd determina qué puertos debe supervisar a partir de un archivo de configuración (generalmente `/etc/inetd.conf`). Este archivo lista los servicios disponibles, sus puertos y los programas a ejecutar.
- Los demonios asociados a estos puertos solo se activan cuando hay trabajo, ahorrando memoria y recursos del sistema.
- Estrategia híbrida: Demonios permanentes + inetd.
	- En la práctica se usa una estrategia combinada: Demonios permanentes en los puertos más utilizados (ej: HTTP puerto 80, SSH puerto 22), porque el costo de tenerlos siempre activos se justifica por su alta demanda. Inetd gestiona los demás servicios de menor uso, activándolos bajo demanda para ahorrar recursos. Esta decisión la toma el administrador del sistema, según el perfil de uso de cada servidor.
## Entrega confiable
La Capa de Red puede perder paquetes (drop por buffer lleno o en la capa de enlace, perdidos por errores del canal), duplicarlos o entregarlos fuera de orden. 
La Capa de Transporte debe (o puede) solucionar esto y es responsable de garantizar (o no) entrega efectiva de los segmentos al host destino, y entrega ordenada: que los datos lleguen en el mismo orden en que fueron enviados por la capa de aplicación.

Esto se logra mediante mecanismos como números de secuencia, ACKs y temporizadores 
1. El emisor asigna números de secuencia a cada segmento, respetando el orden del flujo de datos.
2. Al enviar un segmento, dispara un temporizador de retransmisión.
3. EL receptor envía confirmaciones de recepción (ACK) por cada segmento recibido correctamente.
4. Si el temporizador expira sin recibir ACK, el emisor retransmite el segmento.
5. El receptor reensambla en orden los segmentos recibidos y los entrega a la capa de aplicación.

La capa de transporte dispone de tres mecanismos fundamentales:
1. ACK (confirmación de recepción):
	- El receptor envía un paquete ACK para confirmar que recibió los datos correctamente.
	- Si el emisor recibe el ACK, entonces sabe que el paquete llegó bien.
2. Temporizadores (timers):
	* ¿Cómo saber si un paquete se perdió? 
	* Si pasa cierto tiempo sin recibir ACK. EL temporizador mide ese tiempo de espera máximo (timeout)
3. Retransmisiones:
	* Si el temporizador expira sin ACK, entonces el emisor retransmite el paquete.   
Sin embargo, los mecanismos básicos (ACK, temporizadores, retransmisiones) no son suficientes por sí solos. Se necesita un protocolo de entrega confiable que defina reglas precisas para el emisor y receptor. 
#### ¿Porqué hay varios protocolos? 
Porque el protocolo óptimo depende de las características de la red:
- **Latencia**: cuánto tarda un paquete en llegar 
- **Tasa de errores**: qué tan frecuente es la pérdida/corrupción
- **Capacidad**: tasa de datos que puede manejar la red.
La capa de transporte debe incluir al menos un protocolo de entrega confiable. La capa de enlace incluye protocolos de entrega confiable enfocados en un solo salto, mientras que los de la capa de transporte lo hacen extremo a extremo (recordar que transporte esta sólo en los sistemas finales).
### Números de secuencia
Cada paquete lleva un número de secuencia único. Cuando llega un paquete, el receptor revisa si ya recibió ese número. Si es duplicado, lo descarta. Si es nuevo, lo entrega a la aplicación.
## Protocolo de Parada y Espera
#### ¿Cuándo usar parada y espera?
- Cuando la latencia de la red es baja 
- Cuando el RTT (Round Trip Time o tiempo de ida y vuelta) es bajo comparado con el tiempo de transmitir un paquete.
	- **Consecuencia**: El ACK llega antes de que se termine de preparar el siguiente paquete, entonces sólo se puede tener un paquete "en vuelo" a la vez.
#### Funcionamiento
- Envía un paquete y se detiene hasta recibir confirmación.
- Si llega ACK, envía el siguiente paquete.
- Si expira el temporizador, retransmite el mismo paquete.
* Es el protocolo más simple, ideal cuando RTT es bajo.
#### Supuestos del protocolo
El canal puede perder paquetes de datos y ACKs
#### Comportamiento del emisor: 
1. Envía paquete P y para (no envía más hasta recibir respuesta).
2. Espera un tiempo razonable (timeout).
3. Si llega ACK, entonces envía siguiente paquete (GO TO 1).
4. Si expira timeout, entonces retransmite P (GO TO 2).
Si el ACK llega tarde, se retransmite un duplicado, entonces el receptor lo detecta por el número de secuencia y lo descarta.
### Ejemplo
![](ProtocoloDeParadaYEspera1.png)
![](ProtocoloDeParadaYEspera2.png)
### Evaluación
Para evaluar si parada y espera es eficiente, haremos un análisis del mejor caso con simplificaciones. Si aún en el mejor caso el rendimiento es bajo, entonces el protocolo no es adecuado.
#### Variables del análisis de Parada y Espera
`L` = longitud del segmento (bits)
`T` = tasa de transmisión (bits/seg)
`D` = demora de propagación
`RTT` = ida y vuelta = `2.D`
#### ¿Cuánto tarda en enviar un paquete? 
tiempo de transmisión = $\frac{L}{T}$
#### ¿Cuánto del RTT se calcula con D?
`2.D`
#### $U_{Sender}$
$$U_{sender} = \frac{L/R}{RTT + L/R}$$
Este valor es entre 0 y 1 (se puede expresar en %)
### Ejemplo del desempeño
![](DesempeñoParadaYEspera.png)
Si usamos parada y espera con alta latencia, el emisor pasa la mayor parte del tiempo ocioso esperando el ACK, osea hay una utilización muy baja del enlace.
## Protocolo de Tubería
#### Situación
* La latencia de la red es alta.
* El RTT es mucho mayor que el tiempo de transmitir un paquete, entonces se pueden enviar múltiples paquetes antes de recibir el primer ACK.
#### Solución
El **protocolo de tubería**, permite enviar múltiples paquetes sin esperar confirmación de cada uno. Mantiene el "tubo" lleno, aprovecha mejor el ancho de banda disponible. 
Necesita números de secuencia más grandes, buffers y reglas de retransmisión más complejas.

#### Tubería
El emisor puede enviar múltiples paquetes al vuelo a ser confirmado. El rango de números de secuencia debe incrementarse usando palabras de más de un bit. Hay que usar búferes en el emisor.
![](Tuberia.png)
La Entidad de Transporte (ET) emisora debe manejar búferes para los mensajes. Esto es necesario porque: puede hacer falta retransmitirlos.
El emisor almacena en búfer todos los segmentos hasta que se confirma su recepción.
### Distintos tipos de protocolos

| Retroceso-N (Go-Back-N)                                          | Repetición Selectiva                                               |
| :--------------------------------------------------------------- | :----------------------------------------------------------------- |
| Ventana de N paquetes sin confirmar                              | Ventana de N paquetes sin confirmar                                |
| Si se pierde un paquete: retransmite ese y todos los siguientes. | Si se pierde un paquete: retransmite sólo ese paquete.             |
| Receptor: sólo acepta paquetes en orden (ACK acumulativo)        | Receptor: acepta paquetes fuera de orden (buffer + ACK individual) |
| Más simple, pero puede desperdiciar ancho de banda.              | Más eficiente, pero más complejo (buffer en receptor)              |
|                                                                  |                                                                    |
#### ¿Cuál Protocolo elegir?
Ambos protocolos usan pipelining, pero difieren en en como manejan los paquetes perdidos.
1. Protocolo Retroceso-N (Go-Back-N):
	* Receptor simple: descarta paquetes fuera de orden, ideal cuando la tasa de errores es baja.
2. Protocolo de Repetición Selectiva:
	* Receptor más complejo: guarda paquetes fuera de orden en buffer. Mejor cuando la tasa de errores es significativa.
La elección depende de las condiciones de la red.
## Retroceso-N
#### ¿En qué situaciones se usa Retroceso-N?
- La latencia es grande (RTT alto -> se justifica el pipelining).
- La proporción de errores o pérdida de paquetes es muy baja.
- Rara vez se demoran los paquetes.
**Razonamiento**: Si los errores son raros, el receptor puede ser más simple y eficiente. No necesita buffer para reordenar paquetes, sólo descarta los que llegan fuera de orden. El costo de retransmitir todo es bajo porque rara vez ocurre. Si los errores son frecuentes, la repetición selectiva será más eficiente.
#### ¿Qué pasa si se pierde un paquete?
Tenemos una **restricción clave**: La Capa de Transporte receptora debe entregar datos en orden a la capa de aplicación. Retroceso N, descarta los paquetes correctos que llegaron después del perdido. 
Entonces:
- El receptor descarta todos los paquetes posteriores al perdido.
- No envía ACK para los paquetes descartados.
- El emisor no recibe ACK -> expira el temporizador.
- El emisor "retrocede" y retransmite todos los paquetes desde el perdido en adelante.
### Comportamiento del receptor
El receptor usa **ACK acumulativo**: Confirma hasta el mayor Número de secuencia recibido en orden. 
Al recibir paquete `n`:
- **Caso 1** "`n` está en orden y correcto": Envía ACK (n) y entrega los datos del paquete `n` a la capa superior.
- **Caso 2** "`n` está fuera de orden, dañado o duplicado": Descarta paquete `n` y reenvía ACK del último paquete recibido en orden.
**El receptor no necesita buffer**
### Comportamiento del emisor
El emisor mantiene un único temporizador para el paquete más antiguo no confirmado. Si expira el temporizador, retransmite todos los paquetes no confirmados (desde el más antiguo). 
Si llega un ACK nuevo:
- Si quedan paquetes sin confirmar, reinicia el temporizador.
- Si todos están confirmados, detiene el temporizador.
#### Supuestos del emisor
- Todos los búferes son del mismo tamaño (un paquete por búfer)
- La cantidad de búferes es fija, calculada para llenar el "tubo" durante un RTT.
#### Ventana del emisor
Es el conjunto de Números de secuencia asignados a esos búferes. Define cuántos paquetes puede tener 'en vuelo' simultáneamente. Se 'desliza' hacia adelante cuando llegan ACKs confirmando paquetes.
La ventana permite hasta `N` paquetes consecutivos sin confirmar. 
Ventana emisora = tramas enviadas sin ACK positivo o tramas listas para ser enviadas.
![](RetrocesoNVentana.png)
* Timeout (n): Retransmite paquete `n` y todos los paquetes de mayor Número de secuencia en la ventana.
### ACK acumulativo y expectedSeqNum
El ACK lleva el Número de secuencia más alto recibido en orden consecutivo. Esto se llama **ACK acumulativo**: Confirma todo hasta ese punto.
#### ¿Qué pasa si se pierde un paquete intermedio?
Los paquetes siguientes generan ACKs duplicados (repiten el último ACK en orden).
#### ¿Qué es el expectedSeqNum|?
**expectedSeqNum** = Número de secuencia más chico que aún no llegó.
![](RetrocesoNEnAccion.png)
### Tamaño máximo de la ventana emisora
El tamaño máximo de la ventana emisora es `MAX_SEQ` (no `MAX_SEQ + 1`). Si fuera igual al espacio de secuencia completo, el receptor no podrá distinguir paquetes nuevos de retransmisiones.
El tamaño de la ventana emisora no puede superar `MAX_SEQ` cuando
hay `MAX_SEQ + 1` números de secuencia. Esto garantiza que nunca se reciclen Números de secuencias mientras haya paquetes sin confirmar, por lo que elimina la ambigüedad.
### Problema principal de Retroceso N
- El uso ineficiente del canal cuando hay segmentos perdidos o demorados.
- Un solo paquete perdido obliga a retransmitir todos los siguientes, aunque hayan llegado bien.
- En redes con alta tasa de pérdida, esto desperdicia mucho ancho de banda.
## Repetición Selectiva
#### ¿En qué situaciones usar Repetición Selectiva?
- La latencia es alta (como GBN (*Go Back N*)).
- Además la tasa de errores/pérdida de paquetes es significativa.
- Los paquetes pueden demorarse y llegar fuera de orden.
El enfoque de repetición selectiva, está en un receptor más complejo: Acepta paquetes fuera de orden, usa buffer, envía ACK individuales.
**Tradeoff:** Código más complejo, pero mucho más eficiente con alta tasa de errores.
Repetición selectiva guarda en buffer los paquetes que llegan después del paquete perdido. Sólo se retransmite el paquete perdido.
### Estrategia del receptor (buffer)
Los paquetes correctos que llegan después de un paquete dañado `E`, se almacenan en un buffer (no se descartan).
Cuando el paquete `E` finalmente llega correcto, el receptor entrega a la capa de aplicación, en orden, tanto `E` como todos los paquetes consecutivos almacenados en el buffer.
### Retransmisiones y NAK
Mecanismo básico de retransmisión:
- El temporizador del paquete `E` expira, el emisor lo retransmite.
- Cada paquete tiene su propio temporizador (a diferencia de GBN que usa uno solo). Es más complejo.
#### Mejora (uso de NAK)
El receptor detecta que falta un paquete y envía un **NAK** al emisor. El emisor retransmite antes de que expire el temporizador (mejor rendimiento).
#### Entonces el receptor
- Confirma individualmente cada paquete recibido correctamente (ACK por paquete).
- Almacena en buffer los paquetes fuera de orden hasta poder entregarlos en secuencia a la aplicación.
#### Y el emisor
- Solo retransmite paquetes cuyo ACK no llegó o que recibieron un NAK (confirmación negativa).
- Mantiene un temporizador individual para cada paquete no confirmado (a diferencia de GBN que usa uno solo).
### Ventana del Emisor
#### Estructura de la ventana emisora 
Contiene `N` Números de secuencia consecutivos. Limita la cantidad de paquetes no confirmados en tránsito.
#### Tipos de paquetes dentro de la ventana del emisor
- **Enviados y confirmados**: Aún en ventana porque antes hay paquetes sin confirmar (no puede avanzar la ventana).
- **Enviados y NO confirmados**: En tránsito, esperando ACK.
- **Listos para enviar**: En buffer, esperando turno. 
A diferencia de GBN, aquí pueden haber "huecos" de paquetes confirmados entre no confirmados.
### Ventana corrediza del receptor
El receptor necesita almacenar paquetes que llegan fuera de orden mientras espera el paquete faltante. Para representar qué paquetes puede almacenar el receptor, usamos una ventana corrediza (*sliding window*):
- Un intervalo de Números de secuencia dentro del espacio total.
- Define qué paquetes el receptor está dispuesto a aceptar y guardar en buffer.
- Se desliza hacia adelante a medida que se entregan paquetes a la aplicación.
### Tipos de paquetes en ventana receptora
- **Esperados y no recibidos** (los que faltan)
- **Recibidos y fuera de orden** (almacenados en buffer, esperando al faltante)
- **Aceptables pero no llegados** (dentro de la ventana, todavía en tránsito)
Un paquete en buffer se entrega sólo cuando todos los que le preceden ya fueron entregados a la capa de aplicación.
![](VentanaRepeticionSelectiva.png)
### Recepción de paquetes en el receptor
La ventana emisora comienza en tamaño 0 y crece hasta `MAX_SEQ`. El receptor tiene un buffer dedicado para cada Número de secuencia en su ventana.
#### ¿Que ocurre cuando llega un paquete?
1. Se verifica si su Número de secuencia cae dentro de la ventana receptora.
2. Si está dentro y no fue recibido aún -> se acepta y almacena en el buffer correspondiente.
3. Si está fuera de la ventana -> se descarta (puede ser un duplicado).
![](EmisorReceptor.png)
### Ejemplo
![](RepeticionSelectivaEnAccion.png)
### Tamaño máximo de ventana receptora
```
ventana receptora = (MAX_SEQ + 1)/2
```
#### ¿Porqué?
Con ventanas más grandes, el receptor no puede distinguir entre paquetes nuevos y retransmisiones de la ronda anterior. La mitad del espacio de secuencia, garantiza que las ventanas de emisor y receptor nunca se solapan. Con tamaños mayores de ventana, el protocolo no funciona correctamente.
### Piggybacking
Resuelve el problema de transmitir datos eficientemente en ambas direcciones.
Cuando llega un segmento `S` con datos, el receptor espera a que la aplicación le pase el siguiente paquete `P` para enviar. El ACK de `S` se anexa a `P` usando el campo ACK del encabezado del segmento de salida.
![](Piggybacking.png)
En **comunicación bidireccional**, en lugar de enviar ACKs en paquetes separados, se puede superponer el ACK dentro de un paquete de datos que viaja en dirección contraria.
#### Cómo funciona
La Capa de Transporte espera a que haya un paquete de datos para enviar en la dirección de regreso. El ACK se incluye (va a caballito, *piggybacking*) dentro de ese paquete de datos
* **Ventaja**: Reduce el tráfico en la red (menos paquetes de ACK independientes).
#### Problema con piggybacking, ¿Qué pasa si no hay tráfico de regreso?
El ACK se retrasa indefinidamente y el emisor cree que el paquete se
perdió.
#### Solución, Temporizador auxiliar (`start_ack_timer`)
* Al recibir un paquete en secuencia, se arranca un temporizador
auxiliar.
* Si aparece tráfico de regreso antes del timeout → el ACK va a caballito.
* Si expira el temporizador sin tráfico → se envía un ACK independiente.
El timer auxiliar debe ser corto para asegurarse que la ACK de un paquete correctamente recibido llegue antes que el emisor termine su temporización y retransmita el paquete.
## Control Flujo
El control de flujo busca evitar que un emisor rápido desborde a un receptor lento. El emisor debe ajustar su ritmo de envío a la capacidad de procesamiento de datos del receptor, evitando así la pérdida de información por desbordamiento de búferes.
#### ¿En qué capa?
- La Capa de Enlace de Datos controla flujo entre dos máquinas vecinas conectadas directamente (host <--> router o router <--> router).
- La Capa de Transporte controla flujo extremo a extremo entre procesos en los hosts, sin importar cuántos saltos intermedios haya.
#### El receptor necesita búferes porque
1. **El emisor puede ir más rápido:**
	* Si los segmentos llegan más rápido de lo que el receptor puede procesarlos, hay que guardarlos temporalmente en algún sitio.
2. **Procesar en lotes es más eficiente:**
	* Acumular varios segmentos antes de pasarlos a la capa de aplicación reduce la sobrecarga de procesamiento.
3. **Los segmentos llegan desordenados:**
	* Si llegan segmentos posteriores antes que los previos, hay que almacenarlos hasta completar el orden correcto (por ejemplo, el sistema de retransmisión selectiva usado en TCP).
Sin búferes, cualquier desajuste de velocidad o desorden causa pérdida de datos.
### Problemas a los que nos enfrentamos
#### 1. La Aplicación lee lenta
La aplicación receptora no lee los datos al instante: está ocupada con otras tareas y tarda en retirar los segmentos del búfer.
La consecuencia es: si la aplicación tarda demasiado en leer, el emisor satura los búferes del receptor y se pierden (drop) datos
![](LaAplicacionLeeLenta.png)
#### 2. La red causa retrasos
Este problema  **surge desde la capa de red**. La capa de red también puede dejar al receptor con menos capacidad de búfer, y se da en distintas situaciones:
- **Un paquete llega dañado**
	* Un enrutador en la ruta corrompe un paquete. El receptor lo llega a detectar. Los paquetes buenos que llegan después no pueden entregarse a la aplicación hasta que se retransmita el dañado. Mientras, ocupan buffer.
- **Cambio de rutas**
	* El algoritmo de enrutamiento cambia rutas lentas por rutas más rápidas. Algunos paquetes llegan fuera de orden y hay que almacenarlos hasta recomponer la secuencia y entregarlos a la aplicación.
#### 3. Muchas conexiones a la vez
Este problema **surge desde la propia capa de transporte**. La capa de transporte comparte un presupuesto fijo de memoria entre todas las conexiones abiertas del host.
![](MuchasConexionesALaVez.png)
Sumado a las situaciones de la aplicación y la red, esto puede producir desbordamiento de búferes en el receptor.
### Si la capa de enlace ya hace control de flujo ¿Porqué lo necesita también la capa de transporte?
Porque la capa de enlace sólo ve el salto vecino. Controla el flujo entre dos nodos físicamente conectados. No sabe si la aplicación lee lenta, si hay muchas conexiones abiertas, si hubo reordenamiento de paquetes.
En cambio, la capa de transporte ve todo el camino extremo a extremo. Es la única que puede reaccionar a los problemas (**1,2 y 3**). Necesita su propio protocolo para ajustar el ritmo según el receptor real y evitar que se desborden sus búferes.
Entonces, los protocolos de capa de enlace confiables (ACK, retransmisión) no bastan para evitar el desbordamiento de búferes. Hace falta un protocolo específico de control de flujo en la capa de transporte.
### Si el receptor tiene varias conexiones abiertas, ¿Cómo reparte los búferes entre ellas?
#### Tenemos dos enfoques posibles
1. **Pool compartido**
	* Todas la conexiones comparten el mismo conjunto de búferes. Esto aprovecha mejor la memoria si el tráfico es desigual, pero una conexión ruidosa puede afectar a las demás.
2. **Búferes dedicados**
	* Cada conexión tiene un conjunto de búferes específico y reservado. Esto aísla cada conexión de las demás, pero puede desperdiciar memoria si unas conexiones no las usan.
### Hacia un protocolo dinámico
Sabemos que:
- Los búferes del receptor se pueden llenar por distintas causas
- El receptor y el emisor deben ajustar dinámicamente sus asignaciones
- Esto significa ventanas de tamaño variable..
#### Problema
El emisor y el recpetor saben cosas distintas: El emisor sabe cuántos datos le gustaría enviar, pero no cuántos puede enviar realmente. Mientras que el receptor sabe cuánto espacio libre tiene pero no cuándo el emisor querrá enviar.
#### Posible Solución
**Una posible solución es que el emisor solicite espacio de búfer al receptor y el receptor le otorga explícitamente cuánto puede usar**. 
El emisor solicita espacio: "sé cuántos datos quiero enviar, pido `N` búferes". Así evita enviar más de lo que el receptor aguanta, y evita saturar búferes y perder datos.
El receptor, otorga lo que puede: "Según mi carga, reservo `X` búferes para esta conexión". Debe poder repartir búferes entre varias conexiones y ajustar dinámicamente si aumenta la carga.
Esta interacción de solicitud se llama ***handshake*** (Según sere xd).

No hace falta enviar ACKs y grants por separado, el receptor puede incorporar la reserva de búferes y el ACK en un mismo segmento. Así se ahorran mensajes.
El emisor lleva un contador (otorgado por el receptor ), si la asignación disponible llega a 0, el emisor debe detenerse por completo hasta que el receptor le otorgue más búferes.
## Control de flujo en TCP
TCP no obliga a implementaciones estrictas. En particular, no se requiere que:
1. **El emisor envíe al instante**: No hace falta mandar datos en cuanto llegan de la aplicación.
2. **El receptor confirme al instante**: Los ACK pueden agruparse y retrasarse ligeramente.
3. **El receptor entregue al instante**: Los datos pueden quedarse en el búfer hasta que convenga entregarlos.
#### ¿Porqué importa?
Esta flexibilidad se puede explotar para mejorar el rendimiento: Agrupar segmentos pequeños, retrasar ACKs para aprovechar piggybacking, sincronizar lecturas con el ciclo de la CPU, etc.
#### En TCP
**Números de secuencia = posición de byte**. Los números de secuencia representan bytes dentro del flujo, no paquetes individuales. El receptor sólo puede decir: “tengo estos rangos de bytes en búfer”. Entonces necesitamos un mecanismo distinto para anunciar espacio.
#### Mejoras
Dado que TCP identifica bytes del flujo (no paquetes), se pueden hacer dos mejoras:
1. **No se guardan las cabeceras:**
	* En el esquema anterior, el receptor almacenaba paquetes completos, con cabeceras incluidas, que ocupan espacio. 
	* **En TCP**: Sólo se guarda el payload útil del flujo, el buffer retiene más datos útiles en el mismo espacio.
2. **El emisor ya no pide búferes:**
	* No necesita enviar un mensaje "pido `N` búferes" como en el protocolo anterior. 
	* **En TCP**: El receptor anuncia por iniciativa propia cuánto espacio tiene,**se elimina el primer paso del handshake**.
### El búfer circular del receptor 
Cada conexión TCP tiene, en el receptor un búfer circular de tamaño `RcvBuffer`. Cuando la app lee datos, los retira del inicio del buffer y libera espacio para datos nuevos al final. Esto es exactamente el socket recv.
````
import socket
data = s.recv(1024) # read 1024 bytes
````
La ventaja del circular, es que no importa donde esté cada byte; lo relevante es cuánto espacio libre queda. Ese espacio libre es lo que el receptor anunciará al emisor.
![](Screenshot_2026-05-26-11-37-52_23787.png)
### ¿Cómo le dice el receptor al emisor cuánto espacio libre tiene?
Usa la ventana de recepción. En cada segmento que envía (incluido el ACK), el receptor incluye un campo llamado **rwnd** (*receive window*) con el número de bytes consecutivos que puede aceptar a partir de ahora.
El emisor nunca puede tener en vuelo (enviados y sin confirmar) más de rwnd de bytes.
![](Screenshot_2026-05-26-11-40-21_15393.png)
El buffer entonces tiene **3 regiones**:
1. Datos ya entregados a la app.
2. Datos recibidos, esperando entrega.
3. Espacio libre = rwnd.
O sea, cuánto espacio queda libre en este instante.
![](Screenshot_2026-05-26-11-41-55_2295.png)
El emisor también tiene un buffer circular propio donde almacena los datos que envía. Los mantiene ahí hasta recibir el ACK, por si tiene que retransmitir.
#### ¿Cuánto puede enviar realmente?
bytes enviables = min (tamaño del búfer del emisor, rwnd).
El emisor nunca puede enviar más bytes que el menor de estos limites. 
* **Como límite propio tiene a su búfer**: No tiene sentido enviar más de lo que cabe en su propia memoria esperando confirmación.
* **El límite del receptor es el rwnd anunciado**: Si envía más, el receptor no podrá aceptarlo y los bytes extra se descartarán.
### Fórmula de Rwnd
````
rwnd = RcvBuffer - (LastByteRcvd - LastByteRead)
tamaño del bufer - lo que está esperando a ser leído
````
rwnd (receive window), espacio libre en el búfer que el receptor anuncia
* `RcvBuffer`: tamaño total del búfer asignado.
* `LastByteRcvd`: último byte llegado al receptor.
* `LastByteRead`: último byte leído por la aplicación.
### Reglas del Receptor en TCP
1. Cuando llegan bytes en orden y en secuencia, los coloca en el búfer de recepción.
2. Al confirmar (ACK) la llegada de datos, anuncia al emisor el nuevo tamaño de ventana (rwnd).
3. Si el búfer está lleno, anuncia `rwnd=0` y el emisor debe pausar los envíos.
4. Cuando la aplicación lee `X` bytes del búffer, libera ese espacio y puede anunciar `rwnd=X`.
El receptor avisa continuamente al emisor cuánto puede aceptar.
### Reglas del emisor en TCP
1. Si el receptor anuncia `rwnd=0` el emisor no puede enviar datos.
2. Los bytes enviados pero aún no confirmados nunca pueden exceder la ventana.
```
LastByteSent - LastByteAcked <= rwnd
```
### Casos Especiales de TCP
#### Pérdida de Segmentos
##### ¿Cómo recuperar segmentos perdidos sin retransmitirlos todos?
* **Solución 1**: NAK
	* Un NAK se envía cuando el receptor detecta una brecha entre el número de secuencia esperado y el recibido. Solicita expresamente los segmentos faltantes mediante un campo de opciones.
	* Una vez recibidos los segmentos faltantes, el receptor envía un ACK acumulativo confirmando todos los datos que tiene en búfer.
	* En TCP estándar no existe NAK explícito, en su lugar, el receptor reenvía el mismo ACK acumulativo (ACK duplicado) por cada segmento que llega fuera de orden. Al recibir 3 ACKs duplicados, el emisor dispara *Fast Retransmit* sin esperar el timeout.
* **Solución 2**: ACKs selectivos (SACK)
	* El receptor le dice al emisor exactamente qué rangos de bytes ha recibido, así este reenvía sólo lo que le falta. 
	* Tenemos dos opciones dentro de los segmentos TCP:
		* **SACK - Permitted option**.
			* TCP · Kind = 4 · Length = 2 bytes. 
			* Cuándo: En el campo Options del segmento SYN y SYN-ACK del three-way handshake.
			* Qué indica: Que el extremo soporta SACK. Si ambos la envían, la conexión usará SACK (negociación RFC 2018)
		* **SACK option**
			* TCP · Kind = 5 · hasta 4 bloques por opción
			* Cuándo: El receptor la adjunta dentro del campo Options de los ACKs cuando detecta huecos en la secuencia — viaja en el mismo segmento.
			* Qué contiene: Pares **(Left Edge, Right Edge)** de 32 bits cada uno, los bloques recibidos fuera de orden por encima del ACK acumulativo.
		* **Con SACK el emisor sabe exactamente qué falta**: retransmisión más precisa y eficiente que con ACK acumulativo simple. Pero estas son opciones, no obligaciones de implementación. 
#### Ejemplo
ACK = 1001 + SACK = [2001–3001, 4001–5001] → faltan los bytes 1001–2000 y 3001–4000.
### Ventana cero
Con `rwnd=0`, el emisor no puede enviar datos normales, pero hay 2 excepciones.
1. **Datos urgentes**: Pueden enviarse datos marcados como urgentes ( ej: para que el usuario aborte un proceso en la máquina remota)
2. **Sonda de 1 byte**: El emisor puede enviar un segmento de 1 byte para forzar al receptor a re-anunciar el tamaño de ventana.
#### ¿Porqué existe la sonda? 
Si el aviso de ventana del receptor se pierde, el emisor podría esperar indefinidamente. La sonda de un byte evita el bloqueo irreversible forzando una nueva actualización de rwnd.
### Problema: la ventana de 64 KB es pequeña
El campo Window Size de la cabecera TCP es de 16 bits, por lo que la ventana anunciada no puede superar $2^{16} -1 = 65\ 535 \ bytes (~64KB)$. En enlaces con mucho ancho de banda y mucho retardo ese máximo no alcanza para llenar el pipe. Por otro lado, cambiar el campo de 16 bits rompería compatibilidad, La solución estándar es escalar ese valor mediante una opción TCP: *Window Scale*
Ambos extremos acuerdan un factor de escala aplicado al campo de ventana: tamaño efectivo = `rwnd * 2^k`.
#### ¿Cómo funciona?
Durante el SYN del handshake, cada extremo indica cuántos bits desplazar (hasta 14)
Tamaño máximo resultante $2^{16}* 2^{14} = 2^{30} \ bytes ~= 1 GB\  de\  ventana$.
Window Scale es estándar en las implementaciones TCP actuales; sin él, las conexiones en redes rápidas no alcanzarían el throughput disponible.
## Control de Congestion
La congestión ocurre cuando la red no puede seguir el ritmo del tráfico que le llega. Si un emisor manda más datos de los que la subred puede transportar, los enrutadores intermedios se saturan, los búferes se llenan y empiezan a descartar paquetes.
#### Como Ocurre
1. **Llegan demasiados paquetes**: El tráfico entrante supera la capacidad de reenvío del enrutador.
2. **Los búferes se desbordan**: La cola de salida se llena y el enrutador no puede almacenar más.
3. **Se descartan paquetes**: El enrutador tira segmentos; el emisor deberá retransmitirlos.
### Problema
Necesitamos controlar el ritmo de envío según la capacidad real de la red.
![](Screenshot_2026-05-26-12-24-05_21363.png)
La capa de transporte (TCP en el emisor) es quien regula el ritmo, los enrutadores solo reenvían o descartan.
#### ¿Cómo Controla la congestión TCP?
TCP mide, detecta y reacciona:
- Mide:
	- TCP mantiene la ventana de congestión (VC): el máximo de bytes que el emisor puede tener sin confirmar en un momento dado.
- Detectar:
	- El emisor reconoce la congestión cuando se pierden paquetes, observable por **timeouts** o **ACKs duplicados**.
- Reacciona:
	- Cuando detecta congestión, el emisor reduce el tamaño de la VC para enviar menos datos y aliviar la red.
**Ventana de congestión**: Bytes que el emisor puede tener en la red sin haber recibido ACK todavía. También se le llama **cwnd** (*congestion window*).
#### ¿Cómo detecta TCP la congestión?
TCP infiere la congestión a partir de la pérdida de paquetes. Cuando expira el temporizador de retransmisión, el paquete probablemente se perdió.
##### ¿Porqué? Hay dos posibles causas:
1. **Ruido en el canal** (capa física / enlace)
	* Un error físico corrompe los bits y el paquete se descarta (rara hoy con fibra óptica).
2. **Enrutador congestionado**:
	* Los búferes están llenos y el enrutador descarta paquetes (causa dominante en Internet)
Por eso todos los algoritmos de congestión de TCP asumen que una pérdida = congestión.
#### ¿Qué tamaño le damos a la VC?
Si la VC es muy chica desperdiciamos capacidad; si es muy grande generamos congestión.
![](Screenshot_2026-05-26-12-29-27_21763.png)
### Slow Start
Empezar con una ventana pequeña y duplicarla hasta detectar que la red se satura. Cada RTT duplica los segmentos en vuelo, crecimiento exponencial hasta encontrar el límite.
Se llama slow porque arranca desde 1 segmento en lugar de saturar la red de entrada, aunque la ventana luego crezca exponencial, el punto de partida es mínimo.
![](Screenshot_2026-05-26-12-34-22_13932.png)
#### Paso a Paso
![](Screenshot_2026-05-26-12-35-02_1093.png)
#### Reglas Claves
* Si ACK a tiempo
```
VC <- VC + n segmentos 
```
Cuando la VC es de `n` segmentos y los `n` ACK llegan a tiempo, se agregan `n` STM a la VC. Resultado: duplicación por RTT.
* Si hay timeout
```
VC <- VC/2
```
La VC se recorta a la mitad y no se enviarán ráfagas mayores a ese nuevo valor. Es la primera forma de reaccionar a la congestión.
#### Problema de Slow Start
1. REACCIÓN EXCESIVA
	* Cortar a la mitad puede ser demasiado, si la red puede soportar más que la mitad de la VC actual, se desaprovecha capacidad disponible de la subred.
2. DETECCIÓN LENTA
	* EL timeout tarda mucho en dispararse. Hasta que expire el temporizador, el emisor no sabe que hubo pérdida y sigue inyectando paquetes con una VC mayor a lo que la red soporta, como consecuencia se agrava la congestión y la retransmisión llega tarde.
#### ¿Cómo detectar pérdida antes del timeout?
**Observación clave**: Los ACK que llegan al emisor traen información útil aún cuando no confirman nuevos datos.
- Asumimos que cada segmento que llega al receptor dispara un ACK.
- El ACK siempre indica el próximo byte en orden recibido.
- Eso significa que el emisor puede oír la pérdida vía los ACKs duplicados sin esperar a que expire el temporizador.
![](Screenshot_2026-05-26-12-39-11_32080.png)
pero OJO **no todo ACK duplicado implica pérdida**.
Los paquetes también pueden llegar desordenados y disparar ACKs duplicados espurios.
* **Caso 1**: Reordenamiento (falso positivo)
	* Los segmentos pueden tomar rutas distintas y llegar fuera de orden al receptor. Se disparan 1-2 ACKs duplicados pero ningún paquete se perdió. *Pocos duplicados*.
* **Caso 2**: Pérdida real
	* El segmento `N` no llega al receptor. Los segmentos posteriores si, y cada uno dispara un ACK repitiendo "espero `N`". *Muchos duplicados seguidos*.
Entonces contamos los ACKs
![](Screenshot_2026-05-26-12-44-46_22913.png)
## TCP Tahoe
Tahoe Combina arranque lento con un umbral que divide las fases de crecimiento.
#### Idea nueva 
El umbral (SSTHRESH, *slow start threshold*).
- **Debajo del umbral**: Arranque lento
	* La VC se duplica cada RTT,  crecimiento exponencial, para encontrar rápido la capacidad de la red.
- **Sobre el umbral**: Evitación de congestión 
	* La VC crece de manera lineal (1+ MSS por RTT). Nos acercamos con cuidado a la capacidad máxima. MSS = *Maximum Segment Size* ( = STM en castellano).
### Reacción ante pérdida
Ante pérdida, timeout o 3 ACKs duplicados, Tahoe reacciona igual:
```
ssthresh <- VC actual / 2
VC <- 1 MSS
```
1. Se detecta pérdida (timeout o 3 ACKs duplicados)
	* El emisor registra el evento de congestión.
2. Se guarda la mitad como umbral (ssthresh <- VC actual /2)
	* Es la capacidad estimada segura de la red.
3. La ventana vuelve al mínimo VC <- 1 MSS
	* Se empieza desde cero porque no sabemos si el estado de la red cambió.
4. Arranque lento hasta el umbral, luego crecimiento lineal (exp -> lineal en ssthresh)
	* Debajo del umbral se duplica; arriba solo +1 MSS por RTT.
### Estado estable: El "diente de sierra"
#### Patrón típico 
- La VC sube de forma lineal en evitación de congestión.
- Cuando llega al punto de saturación, se pierde un paquete.
- La VC se corta a la mitad (pasa a ser el nuevo umbral) y vuelve a 1 MSS.
- Arranque lento rápido hasta el umbral, luego crecimiento lineal otra vez.
Este ciclo es la forma clásica "**diente de sierra**".
#### Crítica
 Tahoe no distingue entre timeout y 3 ACKs duplicados. Ante 3 ACKs duplicados, la red todavía está entregando, no está totalmente colapsada. Aún así, Tahoe reduce la VC a 1 MSS. Es demasiado conservador para ese caso; desperdicia capacidad.
![](Screenshot_2026-05-26-14-12-27_5387.png)
### Resumen Visual
![](Screenshot_2026-05-26-14-13-35_10485.png)
## TCP Reno
Reno trata distinto a una pérdida detectada por ACKs duplicados que por un timeout:
* **Timeout** (la red se colapsó)
	* El "reloj de ACKs" se detuvo, probablemente ningún paquete está llegando. 
	* Acción: Mismo tratamiento que Tahoe: VC <- 1 MSS arranque lento.
* **3 ACKs duplicados** (la red sigue andando)
	* Están llegando paquetes posteriores: la red entrega, hay congestión pero no colapso. 
	* Acción: Salta el arranque lento y entra en recuperación rápida.
Tahoe y Reno son iguales al inicio de la conexión y ante timeouts. Difieren solo ante 3 ACKs duplicados.

| Evento                | TCP Tahoe                       | TCP Reno                                |
| :-------------------- | :------------------------------ | :-------------------------------------- |
| Inicio de conexión    | Arranque lento                  | Arranque lento (igual)                  |
| Timeout               | VC &larr; 1 MSS, arranque lento | VC &larr; 1 MSS, arranque lento (igual) |
| 3 ACKs duplicados     | VC &larr; 1MSS , arranque lento | VC &larr; ssthresh, recuperación rápida |
| Después de la pérdida | Vuelve a empezar desde 1        | Sigue enviando con la mitad de la VC    |
### TCP Reno en el tiempo
![](Screenshot_2026-05-26-14-18-32_20628.png)
Reno sigue siendo "diente de sierra" pero con dientes más chicos. Es la base de los TCP actuales.
### Adicionales importantes
#### La congestión también se detecta por delay
El problema de detectar congestión sólo con pérdidas es que, antes de que haya pérdidas: 
- Las colas en routers  crecen.
- El RTT aumenta.
- Se acumula delay.
Las señales basadas en delay, permiten usar el aumento del RTT como indicador temprano.
* RTT esperado (sin cola)
* RTT medido (con cola)
* Si RTT es mayor, entonces hay cola y entonces hay congestión.
La congestión aparece antes del drop. El delay permite detectarla antes.
#### Fairness en TCP
##### ¿Cómo se reparte la red entre múltiples flujos?
Un sistema es **fair** si flujos similares obtienen igual throughput. 
* 2 flujos ~ 50% c/u.
* 3 flujos ~ 33% c/u.
Pero no siempre es justo, tienen problemas como:
- flujos con menor RTT -> ganan más ancho de banda.
- TCP vs UDP -> UDP puede ser "injusto".
- Flujos cortos vs largos -> los cortos pierden.
TCP logra fairness mediante additive increase (sube lento) y multiplicative decrease (baja fuerte).
##### Resultado 
* flujos agresivos -> pierden más -> bajan
* flujos lentos -> crecen progresivamente.
Esto converge hacia equilibrio.
#### Control de flujo + Control de congestión en TCP
![](Screenshot_2026-05-26-14-25-36_5944.png)
## Duplicados Retrasados
No se pueden entregar segmentos duplicados a la capa de aplicación. Como consecuencia, el receptor debe poder decidir para cada segmento que llega, si es duplicado o no.
#### La manera de detectar duplicados eficientemente 
A través de **números de secuencia**:
- Numerar cada segmento con un número de secuencia.
- Dos segmentos con número distinto, son distintos.
Esto funcionaría perfecto si los números fueran de tamaño arbitrario, pero el espacio de secuencia es finito.
#### ¿Porqué?
Los segmentos tienen longitud máxima, el número de secuencia debe entrar en el campo del encabezado. Este campo tiene longitud fija, sólo hay un número finito de valores posibles.
Como consecuencia, los números se reutilizan. Numerar los segmentos sólo con eso ya no alcanza.
### Dos escenarios donde aparecen problemas
#### Escenario A: Dentro de una conexión
El espacio de secuencia se cicla:
- Los números van 0 > 1 -> 2 -> 3, vuelven a 0 y suben hasta 3 de nuevo. 
- Un segmento con número 3 queda demorado y llega cuando ya pasó el segundo 3.
- Resultado: El receptor lo acepta como nuevo: el espacio de secuencia se reutilizó y el duplicado vivió demasiado en la red
![](DuplicadosRetrasadosDentroDeUnaConexion.png)
#### Escenario B: Entre Conexiones
En el ejemplo:
- Espacio de secuencia de 2 bits (0-3).
- Antes de la segunda conexión se libera la primera.
- La segunda conexión llega rápido al número 2.
- Un duplicado retrasado de la primera conexión con número 2 aún vive en la red.
- Es aceptado por error en la segunda conexión.
![](DuplicadosRetrasadosEntreConexiones.png)
### La idea unificadora: Limitar el tiempo de vida 
Asegurar que ningún paquete viva más de `T` segundos en la red. 
Aplica a: paquetes de datos, retransmisiones, confirmaciones de recepción (ACKs). Eliminar paquetes viejos que sigan circulando hace que ambos escenarios sean manejables.
#### Solución para el escenario A 
El origen no debe reutilizar un número de secuencia dentro de `T` segundos (tiempo de vida del paquete).
Funciona porque, cuando se llega a un número ya usado, ese segmento viejo ya murió hace tiempo en la red. El espacio de secuencia debe ser lo bastante grande para que no se cicle dentro de `T` segundos.
### Ejercicio:
Una conexión usa segmentos de 1500 B, transmite a 10 Mbps y el tiempo de vida máximo de segmento es T = 30s ¿De cuántos bits debe ser el n° de secuencia?
#### Solución 
Hay que contar cuántos segmentos como máximo se pueden transmitir en `T` segundos.
- **Bits transmitidos en T**: $10^7 \ bits/s \ * 30s = 3* 10^8 bits$
- **Bits por segmento**: $1500*8 \ = 12000\ bits$
- **Segmentos máximos en T**: $3 * 10^8 / 1,2 *10^4 = 25000\ segmentos$
-  **Bits necesarios**: $log_2(25000) \approx 14,6$ (esto se usa para saber cuantos bits son necesarios para direccionar esos 25000 segmentos)
**Respuesta**: 15 bits.
#### Solución para el escenario B
Si la nueva conexión empieza en 0, puede chocar con duplicados de la conexión anterior.
Entonces, cada conexión elige un número inicial de secuencia distinto para no entrar en el rango de los duplicados de conexiones anteriores.
**Implementación 1: Número inicial aleatorio**
* Al crear una nueva conexión, cada extremo elige un número aleatorio de 32 bits. Ese número pasa a ser el número inicial de secuencia para los datos.
* Esto tiende a funcionar porque la probabilidad de que un duplicado retrasado caiga en el rango de la nueva conexión es baja gracias a la elección aleatoria.
**Implementación 2: número inicial atado al reloj**
* Cada host tiene un contador binario que se incrementa en intervalos uniformes. El número inicial de cada nueva conexión se toma del valor actual del reloj. Los relojes de los hosts no necesitan estar sincronizados. 
* Esto funciona porque el reloj avanza monotónicamente: El número inicial es siempre posterior al usado por la conexión previa, fuera del rango de sus duplicados. 
### Ejemplo (con esquema basado en reloj)
- Pulso de reloj cada $4\  \mu s$
- Tiempo de vida máximo de paquete $T=\ 120 s$
- Número de secuencia: $32 \ bits$
- El reloj corre lo bastante rápido como para que cada nueva conexión abra en un valor distinto, y lo bastante lento como para no agotar los 32 bits dentro de T segundos.