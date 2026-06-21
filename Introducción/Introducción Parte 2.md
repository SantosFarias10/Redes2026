# Conectando Redes de Área Local (LAN)
De la comunicación local pasamos a la necesidad de unir múltiples redes y decidir por donde deben viajar los mensajes. Para lograrlo aparecen los enrutadores, las tablas de reenvío, las colas, el almacenamiento y reenvío, y los algoritmos de enrutamiento.

Este bloque responde a: ¿Cómo conectamos múltiples LAN para formar redes más grandes que funcionen de manera eficiente?
## Enrutadores
Los enrutadores son nodos intermedios para conectar varias LAN.
- Para saber por qué línea de salida mandar un mensaje a una de las máquinas debemos definir una tabla para ello (**tabla de reenvío**).
![](Screenshot_2026-06-21-11-02-17_9047.png)
- Para que el  enrutador pueda manejar recibir tantos mensajes que se van acumulando para ser enviados por una línea de salida, se usa una cola de mensajes en la línea de salida.
## Almacenamiento y reenvío
Necesitamos entender cuánto tarda el enrutador para mandar mensajes, por eso descomponemos la demora total en sus partes constitutivas.
### ¿Cómo hace el enrutador para mandar un mensaje de A a F??
1. El enrutador consulta la tabla de reenvío y descubre que hay que usar la línea `l2`.
2. Encola mensaje en cola de línea `l2`.
3. Cuando el mensaje llega a la cabeza de la cola el enrutador lo reenvía.
A esto se le llama **almacenamiento y reenvío**.
### ¿Cuánto demora el almacenamiento y reenvío?
![](Screenshot_2026-06-21-11-04-36_27014.png)
## Redes de área amplia
Si intentamos conectar cientos o miles de LAN a un solo enrutador, simplemente colapsa. Ahí es donde necesitamos pasar a una estructura más grande: las redes de área amplia (*wide area network*- WAN).

Una WAN puede cubrir desde un país hasta un continente entero.
- Si hay demasiadas LAN para conectar en un sólo enrutador, usamos más de uno y conectamos los enrutadores entre sí mediante cables (conexiones punto a punto).
![285](Screenshot_2026-06-21-11-06-16_31075.png)
>[!tips] Las tablas de reenvío en su conjunto determinan la ruta que se usa.
## Algoritmos de enrutamiento
Al pasar de unas pocas LAN interconectadas a redes de área amplia, la escala cambia por completo. Tenemos decenas o cientos de enrutadores distribuidos geográficamente, cada uno con su propia tabla de reenvío y su propio tráfico.

En este escenario, elegir por dónde enviar un paquete deja de ser trivial, ningún enrutador puede tener una visión completa y perfecta del estado global. Por eso, en las WAN aparece un nuevo desafío arquitectónico: necesitamos mecanismos sistemáticos para calcular, actualizar y coordinar rutas.

Hay rutas mejores que otras para ir a un destino, la mejor ruta entre dos dispositivos es la ruta más corta entre esos dispositivos. Para llegar a esto, debemos modificar las tablas de reenvío con ese propósito. Los algoritmos que modifican esas tablas, se llaman algoritmos de enrutamiento: [[Algoritmos de enrutamiento para WANs no jerárquicas]] y [[Algoritmos de enrutamiento para WANs jerárquicas]]
>[!tips] Agregar los enlaces cuando llegue a enrutamiento
## Ejemplos de redes de área amplia 
Las WAN que usamos todos los días, como las redes de fibra hasta el hogar o las redes telefónicas, son ejemplos donde estos mecanismos se aplican a gran escala, con miles de enrutadores y múltiples tecnológicas físicas.
- Sistema de fibra a la casa: 
	- Divisor óptico para subdividir un cable de fibra óptica en varios (cada uno va a una casa), usualmente menos de 100.
	- Cada casa tiene un terminador de red óptica para convertir entre señales ópticas y eléctricas.
	- Tasas de transferencia de 100 Mbps o 300 Mbps
![](Screenshot_2026-06-21-11-11-55_31096.png)
-  Sistema telefónico fijo (por ejemplo: DSL):
	- Cada domicilio está conectado por un cable de cobre a una End office (oficina central).
	- Toda oficina central está conectada a una Toll office.
	- Toll offices con usadas para reenvío de mensajes y están unidas por cables (de fibra óptica).
![](Screenshot_2026-06-21-11-13-20_13393.png)
# Conectando Redes de Área Amplia
Conectar LANs es útil dentro de una red de área amplia. Las redes de área amplia son manejadas por organizaciones que se ocupan de proveer servicios de red. Pero hace falta escalar aún más la perspectiva interconectando redes de área amplia.
Este bloque responde a: ¿Cómo se organiza una red como la internet a nivel global para que cualquier red pueda comunicarse con cualquier otra?
## Redes de acceso
Una sola empresa no puede prestar el servicio de conectar todas las LAN del planeta, por X motivos.

Para poder interconectar todas las LAN entre sí hacen falta varias redes de acceso interconectadas. A esto se le llama **interred**. 

>[!tip] La internet es una interred

![](Screenshot_2026-06-21-11-19-17_4517.png)
## Redes globales de tránsito
Las redes de acceso nos permiten conectar hogares, empresas o instituciones a un proveedor de servicio de red. Pero una vez que el tráfico sale de esa red de acceso, necesita recorrer distancias mucho mayores y atravesar múltiples dominios. En ese punto, la infraestructura de acceso ya no alcanza: no está diseñada para mover grandes volúmenes de tráfico entre ciudades, países o continentes.

¿Cómo resolvemos esto? Conectamos cada red de acceso a una red global de tránsito, y las redes globales de tránsito se conectan entre sí.
![](Screenshot_2026-06-21-11-23-25_9570.png)

Las redes globales de tránsito pueden competir entre sí. Una red global de tránsito contiene enrutadores de alta velocidad interconectados usando enlaces de fibra óptica de alta velocidad. Cada red de acceso o global de tránsito es manejada independientemente.
## Redes regionales
Las redes globales de tránsito forman la infraestructura que permite mover tráfico entre países y continentes, conectando grandes proveedores y sosteniendo la escala planetaria de redes como la internet. Sin embargo, las redes globales no siempre llegan a todas las regiones para conectar directamente sus redes de acceso. Surge entonces la necesidad de una solución intermedia que conecte las redes de acceso dentro de una región y las vincule con la infraestructura global.

Se usa una red regional en la región que se va a conectar con alguna red global de tránsito:
- Redes de acceso pagan a redes regionales
- Redes regionales pagan a redes globales de tránsito
- Redes regionales pueden conectarse entre sí.
![](Screenshot_2026-06-21-11-24-46_9974.png)
# Redes Proveedoras de Contenido
En la práctica, enormes plataformas - servicios de video, redes sociales, buscadores, almacenamiento en la nube - generan volúmenes masivos de datos que deben llegar a usuarios distribuidos por todo el mundo. Y estas plataformas no siempre quieren depender de las reglas, los costos o las políticas de servicio de las redes de tránsito o regionales.

Para evitar esos costos y limitaciones, y para mejorar la calidad de entrega de su propio contenido, se vuelve necesaria una solución que permita que el contenido de estas plataformas llegue eficientemente a todas las redes de acceso.

Por eso se usan **Redes proveedoras de contenido (CDN)** (por ejemplo: Google, Microsoft, Apple, Meta). Las mismas son infraestructuras propias diseñadas para llevar su contenido lo más cerca posible  de los usuarios.

## Puntos de Presencia (PoPs)
Los POPs son nodos distribuidos que reducen latencia y evitan tránsito global. Son como servidores en distintos lugares del mundo, acercarlos a los usuarios y conectarlos a redes de acceso o regionales.

Un PoP está pensado para entregar contenido a usuarios cercanos, no para generarlo ni coordinarlo. Si solo existieran los PoPs, aparecen tres problemas inevitables:
- No pueden generar contenido: no tienen la infraestructura para producir, transcodificar o almacenar la versión maestra.
- No pueden mantenerlo actualizado: cada PoP tendría que obtener actualizaciones desde algún origen remoto.
- No pueden replicar contenido entre ellos sin pagar tránsito: si un PoP copia contenido desde otro PoP lejano, el tráfico cruza redes regionales o globales, generando altos costos y alta latencia.
Conclusión: Los PoPs  no pueden ser el origen del contenido ni coordinar su distribución global.

¿Qué necesita un PoP para cumplir su función?
- almacena contenido popular localmente para reducir latencia, usando una caché dentro de un PoP que guarda sólo lo más pedido.
- atiende a muchos usuarios a la vez a través de servidores de borde para atender usuarios y balanceadores de carga para repartir la carga
- se conecta a redes de acceso o regionales para entregar contenido,  usando equipos de redes, enlaces de alta capacidad y acuerdos de peering local con redes de acceso y regionales.
- recibir contenido actualizado desde algún centro de datos

![[Pasted image 20260316200403.png]]

## Centros de datos

Los centros de datos aparecen como la única forma viable de tener la versión maestra del contenido, procesarlo (transcodificarlo, empaquetarlo, indexarlo), mantenerlo actualizado, replicarlo hacia los PoPs, coordinar consistencia entre múltiples ubicaciones, balancear carga y manejar picos globales. 
Los PoPs quedan así aliviados: solo almacenan copias parciales y sirven contenido localmente.

![[Pasted image 20260316195100.png]]

Un centro de datos genérico es una instalación diseñada para alojar infraestructura de cómputo y almacenamiento que soporta aplicaciones, servicios y datos de todo tipo. Sus funciones típicas incluyen:
- Ejecutar aplicaciones empresariales
- alojar servicios en la nube
- Procesar cargas de cómputo (machine learning, análisis de datos)
- almacenar información corporativa
- soportar servicios internos de una organización 
- correr aplicaciones web, APIs, microservicios, etc.


Un centro de datos necesita:
- Generar contenido (procesarlo, transcodificarlo, empaquetarlo)
- Mantener la versión maestra de cada objeto
- Coordinar actualizaciones para que todos los PoPs tengan contenido consistente 
- Replicar contenido hacia los PoPs de forma eficiente
- Almacenar grandes volúmenes de datos 
- Balancear carga global entre regiones
- Tener alta disponibilidad (fallas, redundancia, energía, clima)
- Conectarse a una red propia que distribuya contenido sin pagar tránsito global.

>[!question] ¿Cómo se genera y procesa contenido a  gran escala?
Se usa capa de procesamiento pesado con: servidores de cómputo (para análisis de contenido, generación de metadatos, validación y empaquetado), clústers de procesamiento y sistemas de transcodificación.
La transcodificación es el proceso de convertir un contenido digital de un formato, resolución o tasa de bits a otro, para que pueda ser entregado de manera eficiente a distintos tipos de dispositivos, redes y condiciones de ancho de banda.

¿Cómo se mantiene una versión maestra del contenido?
	Se usa capa de almacenamiento maestro: sistemas de almacenamiento de objetos distribuidos en varios nodos, sistemas de archivos distribuidos, bases de datos.


>[!question] ¿Cómo se coordinan actualizaciones entre regiones?
Se usa capa de control y coordinación: 
>- Orquestación (qué tarea va a qué máquina)
>- Coordinación global (qué PoP recibe qué contenido)
>- Gestión de versiones
>- Consistencia (que los PoPs tengan lo que deben tener)
>- Políticas de distribución (popularidad, geografía)
>- Monitoreo y telemetría (estado de PoPs, carga, fallos)
>- Replicación (decidir cuándo y cómo mover contenido)

¿Cómo se asegura disponibilidad contínua?
- Redundancia energética
- climatización 
- Monitoreo
- failover regional
	  El failover regional es el mecanismo mediante el cual un servicio puede migrar automáticamente su operación desde una región que falla hacia otra región que sigue funcionando, sin que los usuarios pierdan acceso al contenido.
	  Este mecanismo garantiza continuidad incluso ante fallas grandes: cortes eléctricos, problemas climáticos, errores de software, congestión extrema o caídas de conectividad.
![[Pasted image 20260316202213.png]]
## Red privada global
Incluso con centros de datos, si cada réplica hacia los PoPs viaja por redes de tránsito global, volvemos al problema original: alto costo, alta latencia y dependencia de terceros.

Para llevar contenido desde los centros de datos a los PoPs sin pagar tránsito, usamos una red privada global propia del proveedor de contenido que conecte centros de datos y PoPs sin depender de tránsito global. Esto permitirá: enlaces troncales privados, rutas optimizadas para replicación masiva. 
Con esto el proveedor puede mover contenido desde centros de datos hacia la periferia (PoPs) sin pagar tránsito y con baja latencia.