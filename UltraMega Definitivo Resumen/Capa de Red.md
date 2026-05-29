# Duran ya me cogiste 1 vez, es vuelta y vuelta, y ahora me toca a mi viejo gaga
# Organización, Direccionamiento y Reenvío en WAN
Para comprender cómo funciona una WAN y cómo se la diseña, es necesario atender tres cuestiones fundamentales:
1. Cómo está organizada la red en términos de máquinas y sus interconexiones.
2. Cómo se nombran esas máquinas e interfaces mediante un esquema de direccionamiento y
3. Cómo se construyen las tablas de reenvío que permiten que los paquetes avancen hacia su destino.
#### El primer desafío
Lograr un esquema de direccionamiento que sea verdaderamente escalable. Un direccionamiento poco escalable, es aquel cuyas direcciones se agotan rápidamente o no alcanzan para cubrir la demanda creciente de nuevas máquinas, interfaces o LANs.
#### Resolver este problema es indispensable
Sin un espacio de direcciones suficientemente grande y bien estructurado, la red simplemente no puede crecer.
#### Una vez que garantizamos que el direccionamiento puede escalar, aparece el siguiente obstáculo
Incluso con direcciones suficientes, las tablas de reenvío pueden volverse demasiado grandes si cada enrutador necesita conocer demasiados destinos. Esto nos obliga a pensar cómo organizar una WAN grande para evitar que las tablas consuman demasiada memoria y se vuelvan lentas de consultar.
## Bloque 1: Red plana de enrutadores interconectados
Comenzamos estudiando la organización más simple posible de una WAN: Una red plana formada por enrutadores interconectados sin jerarquías. El objetivo es comprender los mecanismos básicos de direccionamiento y reenvío antes de preocuparnos por la escalabilidad.
![](OrganizacionRedPlana.png)
Hardware subyacente de la capa de red:
-  Subred: formada por enrutadores interconectados.
- Hosts o LANs conectadas a subred.
#### ¿Cómo se definen las direcciones de las máquinas?
Se identifican los enrutadores. Para cada dispositivo de cómputo de un hogar u organización tenemos un rango de números de máquinas.
#### ¿Cómo hacer para distinguir máquinas de distintas organizaciones?
Una máquina de un hogar u organización se identifica con: (identificador de enrutador, número de máquina) (esto solo para la red plana).
### Tablas de reenvío
La tabla de reenvío sólo necesita entradas para los enrutadores de la subred. Entrada de tabla de enrutador formada por filas: `<enrutador de destino, línea de salida>`.
La línea de salida es la dirección de un enrutador.
![](Screenshot_2026-05-26-15-09-40_28601.png)
### Direccionamiento de interfaces de máquinas
El esquema inicial de direccionamiento identifica a cada máquina mediante un par formado por el enrutador y un número de máquina dentro de la organización. 
#### Sin embargo este modelo muestra una limitación importante
- No distingue los enlaces a través de las cuales las máquinas y los enrutadores se conectan a la red. En la práctica, una computadora puede pertenecer simultáneamente a más de una red. 
- Por ejemplo Ethernet y WiFi y un enrutador puede estar conectado a múltiples LAN mediante distintos enlaces. 
- El esquema basado en máquinas no permite identificar por cuál de esas redes se accede realmente al dispositivo como destino.
#### Para resolver este problema 
Necesitamos un esquema más preciso que identifique enlaces individuales y permita representar LAN completas de manera compacta. Esto nos lleva naturalmente al direccionamiento de interfaces, que será la base para construir tablas de reenvío más correctas y escalables. Este es el enfoque adoptado por internet.
#### ¿Qué es una interfaz?
Conexión entre host/enrutador y enlace físico. Un enrutador tiene muchas interfaces, una por cada línea de salida. 
**Un host tiene una o dos interfaces**: Con Ethernet cableada, con red inalámbrica 802.11.
Las interfaces están **conectadas entre sí por medio de conmutadores y estaciones base**.
![359](Screenshot_2026-05-26-15-16-15_15946.png)
#### ¿Cómo se definen las direcciones de interfaces de las máquinas de una red local?
Usar un par `<número de red, número de interfaz>`.
En lugar de un par, podemos concatenar los dos números binarios. Entonces una dirección puede ser un número binario de `n` bits donde los primeros bits son del número de red y los últimos del número de interfaz. 
Por ejemplo IPv4 usa direcciones de 32 bits.99
##### ¿Conviene que una tabla de reenvío contenga como destinos números de interfaz de máquinas?
**No**, porque las interfaces de máquinas son demasiadas y van a ocupar mucho espacio en la tabla de reenvío. Además, solo necesito que el paquete llegue a la LAN adecuada.
Hay muchas menos LAN que máquinas en ellas; por lo tanto, conviene que los destinos de las tablas de reenvío sean LANs expresadas de algún modo conveniente.
Por ejemplo:
- `<dirección de interfaz de inicio, dirección de interfaz de fin>`.
- `<dirección de interfaz de inicio,  cantidad total de interfaces>`.
- `<dirección de interfaz de inicio, n>`. 
	- Tal que hay $2^n$ interfaces en total.
- `<dirección de interfaz de inicio, x-n>`.
	- Tal que hay $2^n$ interfaces en total y `x` es la longitud de las direcciones de interfaz. (usada en IPv4)
Cualquiera de estas soluciones anteriores puede ser usada para indicar destinos en tablas de reenvío.
#### Ejemplo de los prefijos usados en IPv4
Significado del prefijo 128.208.0.0/24:
- la dirección IP de la interfaz más baja en el bloque es 128.208.0.0
- la porción de la red es de 24 bits
- $32-24 = 8$, por lo tanto, tengo 2⁸ interfaces en total en la red.
#### ¿Qué forma tiene una tabla de reenvío cuando se aplica la primera idea? 
`<dirección de interfaz de inicio, dirección de interfaz de fin, línea de salida>`.
#### ¿Cómo se usa la tabla de reenvío cuando llega un paquete?
Extraer la dirección de interfaz de destino del paquete entrante. Luego analizar la tabla de reenvío entrada por entrada, chequear si la dirección de interfaz extraída está entre los valores de las direcciones de interfaz de la fila. Si coinciden entradas múltiples se usa la red más pequeña.
## Bloque 2: Limitaciones de escalabilidad de las direcciones de interfaz
El espacio de direcciones puede agotarse si cada máquina o interfaz necesita una dirección única dentro de una WAN cada vez más grande. Este problema no es teórico, ocurrió en la práctica con IPv4. Por eso, antes de pensar en cómo organizar redes enormes, necesitamos resolver primero cómo hacer que el direccionamiento mismo pueda escalar. 
#### Tenemos dos soluciones posibles
1. Abandonamos el espacio de direcciones y creamos un espacio de direcciones más grande. Fue lo que se hizo con IPv4: se creó IPv con esta finalidad (En lugar de direcciones de 32 bits se usaron direcciones de 128 bits).
2. Seguimos usando el mismo espacio de direcciones inteligentemente con reutilización de direcciones. Se aplicó para el caso de IPv4 para alargar el uso de espacios de direcciones. La solución se llama NAT (traducción de dirección de red natural).
### Direcciones IPv6
Son escritas como 8 grupos de 4 dígitos hexadecimales. Para separar los grupos se usa **":"**.
Por ejemplo: 8000:0000:0000:0123:4567:89AB:CDEF.
#### Optimización
Ceros a la izquierda de grupos pueden ser omitidos, grupos con dos **":"**.
Una dirección IPv6 la podemos dividir en:
- **Identificador de red:** identifica la red principal en la que se encuentra el dispositivo. 
- **Identificador de subred:** ayuda a dividir la red principal en subredes más pequeñas.
- **Identificador de interfaz:** identifica de manera única al dispositivo dentro de la subred.
##### Ejemplo
Dada la dirección 2001:0db8:85a:0000:0000:8a2e:0370:7334. 
- identificador de red: 2001:0db8: 85a3.
- identificador de subred: 0000:0000.
- identificador de interfaz: 8a2e:0370:7334.
#### Esquema lógico de una red IPv6:
- **Nivel de red global**: 2001:db8::/32. El proveedor de servicios de red asigna un prefijo global a una organización.
- **Nivel de red interna**: 2001:db8:1::/48, la organización asigna subredes dentro de ese espacio.
- **Nivel de subred**: Son subredes divididas a partir dentro del prefijo interno. Pueden usarse para departamentos.
	- Por ejemplo: Para el departamento A usa 2001:db8:1:1::/64. Un dispositivo final del departamento A tiene una dirección 2001:db8:1:1::100.
### NAT
En la práctica, la transición a un nuevo esquema de direccionamiento global es lenta y costosa, y durante muchos años la demanda de nuevas máquinas siguió creciendo más rápido que la adopción de IPv6. Esto llevó a buscar una alternativa que permitiera extender la vida útil de IPv4 sin modificar su tamaño.
Esa alternativa es NAT, un mecanismo que mantiene el mediante la reutilización interna de rangos privados. NAT no resuelve el problema de fondo, pero permite que redes enormes sigan funcionando dentro de un espacio limitado, lo que lo convierte en una solución pragmática frente a la falta de adopción inmediata de IPv6.
#### Traducción de dirección de red natural (NAT)
Asignar una sola dirección de interfaz a cada organización para el tráfico de internet.
1. Dentro de la organización cada computadora tiene una dirección de interfaz única que se usa para el tráfico interno. O sea, estas direcciones de interfaz no se usan afuera de la LAN; solo adentro de la organización, y se repiten en distintas LAN.
2. Cuando un paquete sale de una organización y va a la WAN, se presenta una traducción de dirección (de la dirección de la  computadora en la organización a la dirección de interfaz única usada por la organización)
##### Implementación
Para que este esquema sea posible, es necesario considerar rangos de distintos tamaños para así permitir organizaciones de distinto tamaño.
Por ejemplo, hay 3 rangos de direcciones IPv4 que se han declarado como privados. Las organizaciones pueden usarlos internamente cuando deseen. Las única regla es que ningún paquete que contiene estas direcciones pueda aparecer en la internet. 
Los 3 rangos reservados son:
- 10.0.0.0         -10.255.255.255/8          (16,777,216 hosts).
- 172.16.0.0      -172.31.255.255/12         (1,048,576 hosts).
- 192.168.0.0    -192.168.255.255/16       (65,536 hosts).
#### ¿Cómo hacer cuando un paquete sale de las instalaciones de la organización?
El paquete pasa a través de una caja NAT que convierte la dirección interna de origen de IP a la dirección de la organización.
![](Screenshot_2026-05-26-16-00-56_9948.png)
Cada mensaje saliente contiene puertos de origen y de destino que sirven para identificar los procesos que usan la conexión en ambos extremos.
##### Problema
Cuando la respuesta vuelve, por ejemplo, de un servidor web, se dirige naturalmente a dirección de interfaz de la compañía, ¿Cómo sabe ahora la caja NAT con qué `<dirección de interfaz y puerto>` se reemplaza?
##### Solución: 
Distinguir entre el Número de puerto usado para identificar la máquina (usando dirección de interfaz de la red interna) y el Número de puerto usado por el protocolo de transporte (por ejemplo, puerto TCP para identificar una conexión).
Cuando llega un paquete con puerto de origen, se busca en la tabla la dirección de interfaz del nodo y el Número del puerto que se usa para la conexión.
#### Tabla de traducción de la caja NAT
Los índices en la tabla de la caja NAT son números de puerto para identificar la máquina. Una entrada de la tabla contiene los siguientes elementos:
* `<número de puerto para identificar la conexión, dirección de interfaz interna a la organización>`
##### ¿Cómo tratar un paquete que llega a la caja NAT desde la WAN?
El puerto de origen en el encabezado TCP se extrae y usa como un índice en la tabla de traducción de la caja NAT. Desde la entrada localizada, la dirección de interfaz interna y el puerto TCP se extraen e insertan en el paquete, entonces el paquete se pasa al enrutador de la compañía para su entrega normal usando la dirección de interfaz.
##### ¿Cómo tratar un paquete saliente que entra en la caja NAT?
La dirección de origen interna a la compañía se reemplaza por la dirección de interfaz de la compañía y el puerto de origen TCP se reemplaza por un índice en la tabla de traducción de la caja NAT.
## Bloque 3: Red jerárquica con regiones de igual importancia
Incluso con direcciones suficientes, una WAN grande puede generar tablas de reenvío inmensas si cada enrutador debe conocer demasiados destinos. O sea, el direccionamiento escalable es necesario, pero no suficiente: también necesitamos que las tablas de reenvío puedan mantenerse pequeñas y eficientes.
Cuando el tamaño de las subredes crece mucho, también crece mucho el tamaño de las tablas de reenvío: 
* hay más enrutadores (cuando se usa direccionamiento de máquinas)
* hay más LAN (cuando se usa direccionamiento de interfaz)
Tener tablas de reenvío grandes, consume mucha memoria en el enrutador, además se necesita usar más la CPU para examinarlas.
#### ¿Cómo hacer para que las tablas de enrutamiento no crezcan demasiado cuando crece mucho el tamaño de la subred?
Dividir los enrutadores de la WAN en **regiones**. Cada región es un grafo separado de las demás regiones, cada enrutador pertenece a una región y hay enlaces entre regiones.
![](Screenshot_2026-05-26-16-26-14_24313.png)
Los enrutadores y las regiones pueden tener dirección en una red jerárquica de regiones de igual importancia.
#### ¿Cómo definir las direcciones de los enrutadores y las regiones?
Numeramos las regiones y para cada región determinamos cantidad de enrutadores y forma de contar esa cantidad. Un enrutador va a tener como dirección un par `<número de región, identificador de enrutador>`.
#### ¿Cuáles son los destinos de la tabla de reenvío de un enrutador?
Entradas para todos los enrutadores locales, entradas para las demás regiones en las que no está el enrutador.
Y denotamos la línea de salida para un destino usando la dirección del enrutador al que se llega por esa línea de salida.
![](Screenshot_2026-05-26-16-28-08_22946.png)
En las redes enormes, una jerarquía de dos niveles es insuficiente, entonces tenemos distintas soluciones posibles:
- Agrupar las regiones en clústeres.
- Agrupar las regiones en clústeres y los clústeres en zonas. 
- Incluso podemos seguir agregando más niveles.
## Bloque 4: Red Jerárquica con área dorsal
El modelo de regiones de igual importancia nos permitió ver cómo una jerarquía básica ayuda a reducir el tamaño de las tablas de reenvío y a limitar la visibilidad de la topología. Sin embargo, esta falta de centralidad complica la administración y el control del tráfico interregional.
#### Para resolver este problema surge una organización más refinada
Las redes divididas en áreas conectadas a un  área dorsal. Este modelo introduce un backbone que concentra la conectividad global y simplifica el tránsito entre áreas, permitiendo una WAN más modular, eficiente y escalable.
Podemos organizar una red jerárquica (WAN) en áreas donde hay un área dorsal.
![](Screenshot_2026-05-26-16-42-29_402.png)
#### Organización
- Hay un área que es red dorsal
- Hay áreas que se conectan a la red dorsal.
- Un área no dorsal puede tener varias LAN dentro de ellas. 
- Las áreas no se traslapan.
- Cada enrutador está en al menos un área.
- Para ir de un área a otra hay que pasar por la red dorsal no es visible fuera de esta.
- Lo mismo con la topología de un área no dorsal
Las áreas se enumeran. El área dorsal tiene número 0.
#### ¿Cómo se clasifican los enrutadores en una red jerárquica con área dorsal?
- **Enrutadores internos**:
	* Yacen completamente dentro de un área
- **Enrutadores dorsales**:
	* Enrutadores en un área dorsal.
- **Enrutador de borde de área** (EBA)
	* Es parte de una red dorsal y a la vez de una o más áreas.
- **Enrutador de borde de WAN**
	* Inyecta en el área rutas a destinos externos en otras WAN.
#### ¿Cómo organizar un área?
1. Las líneas punto a punto entre dos enrutadores.
2. Redes de multiacceso con difusión (por ejemplo, la mayoría de las LAN).
3. Redes de multiacceso con muchos enrutadores, cada uno de los cuales se puede comunicar directamente con los otros (LAN 3 de la figura).
![](Screenshot_2026-05-26-19-28-22_15887.png)
En una red jerárquica con área dorsal, se puede usar de **direccionamiento**, las direcciones de interfaz de los enrutadores. También se identifican las LAN de organizaciones.
Se usan las tablas de reenvío de cuando se trabaja con interfaces. Recordar que para estas redes se usan como destinos de las tablas de reenvío las LAN, identificadas como mencionamos antes.
### Recapitulación
#### Problema Central
Mover paquetes eficientemente en una WAN que crece sin límites. 
#### Primero vimos
Una red plana de enrutadores para entender cómo se identifican máquinas, interfaces y LANs, y cómo se construyen tablas de reenvío básicas.
#### Limites del modelo inicial
El espacio de direcciones puede agotarse y las tablas de reenvío pueden volverse demasiado grandes.
#### Solución
Escalar el direccionamiento: 
* Ampliar el espacio de direcciones, como en IPv6, y mantener el mismo espacio pero administrarlo mejor, como hace NAT mediante la reutilización interna de direcciones. Esto mostró que el direccionamiento condiciona la evolución de toda la red.
#### Segundo límite
El crecimiento descontrolado de las tablas de reenvío. 
#### Solución
Introducción de jerarquías para reducir la información que cada enrutador debe manejar. 
1. Primero vimos redes divididas en regiones de igual importancia.
2. Luego una jerarquía más refinada: Áreas conectadas a un área dorsal, modelo común en Internet para mantener la red modular y eficiente.
#### Conclusión
En conjunto, recorrimos cómo cada solución surge como respuesta al límite del modelo anterior. Desde el direccionamiento básico hasta las jerarquías avanzadas, vimos cómo la capa de red organiza el espacio y controla la complejidad para que una WAN pueda crecer sin volverse inmanejable.

---
# Algoritmo de Enrutamiento para WANs no jerárquicas
#### ¿Cómo lograr que los paquetes encuentren su camino en una red de enrutadores interconectados, incluso cuando la topología cambia, los enlaces fallan o el tráfico se vuelve impredecible?

## Bloque 1: Problema del enrutamiento en WANs
#### Problema
Si no se usan los algoritmos de enrutamiento, algunos enrutadores pueden quedar inactivos, los caminos pueden ser innecesariamente largos, se pueden sobrecargar algunas de las líneas de comunicación y los enrutadores asociados a ellas.
#### Solución
Un **algoritmo de enrutamiento** es un algoritmo que escoge bien las rutas a usarse para enviar paquetes. Las rutas se determinan actualizando y llenando las tablas de reenvío. Un algoritmo de enrutamiento se ejecuta en los enrutadores de la WAN.
## Bloque 2: Conceptos básicos
#### ¿Cómo representar una subred como un grafo?
![](Screenshot_2026-05-26-20-24-29_9530.png)
* **Grafo** `G` = (N, E).
* `N` = **conjunto de enrutadores** = {u,v,w,x,y,z}.
* `E` = **conjunto de enlaces** = {(u,v), (u,x) (v,x), (v,w), (x,w), (x,y), (w,y), (w,z), (y,z)}.
Los arcos tienen etiquetas para el costo de atravesarlos.
* c(x, x') = **costo de enlace** (x,x')
Los costos de los arcos podrían calcularse como función de varios parámetros: 
* la distancia, ancho de banda, tráfico medio, costo de comunicación, longitud media de las colas, retardo medio, y otros factores. 
* **El costo de un camino es la suma de los costos**.
## Bloque 3: Enrutamiento de caminos más cortos
#### Situación
Una red cuya topología no cambia y cuyo tráfico varía muy poco. En este contexto, podemos permitirnos calcular las rutas una sola vez y usarlas durante largos períodos sin necesidad de actualizarlas.
#### Algoritmo de enrutamiento de caminos más cortos
Para elegir una ruta entre un par de enrutadores, encontrar en el grafo una de las rutas más cortas entre ellos. Uno de ellos es el algoritmo de Dijkstra.
Dado grafo conexo con costos en los enlaces y nodo `n` el grafo, obtiene árbol de camino más cortos desde `n` hacia todos los demás nodos.
El árbol de caminos más cortos se representa con un mapeo donde para cada nodo del grafo de la subred asigna su padre (en el árbol de caminos más cortos).
#### Pasos para calcular las tablas de reenvío usando el algoritmo de Dijkstra
1. Construir grafo de la subred con costos.
2. Ingresar grafo de la subred con costos en los enrutadores.
3. En cada enrutador construir tabla de enrutamiento, para eso:
	1. Ejecutar algoritmos de Dijkstra en el enrutador.
	2. A partir del árbol de caminos más cortos con raíz en el enrutador obtenido generar la tabla de reenvío del enrutador.
## Bloque 4: Inundación hacia un destino
#### Situación
En una red sometida a fallas frecuentes, queremos mandar un paquete de un nodo de origen `u` a un destino `v`.
Los algoritmos de caminos más cortos dejan de ser confiables porque suponen una topología relativamente estable. El cálculo de la ruta óptima se vuelve obsoleto, por ejemplo un enlace o enrutador crítico puede caer justo después de haber sido elegido como parte del camino, dejando al paquete atrapado o descartado en una región vulnerable.
#### Meta
Maximizar la probabilidad de entrega de un paquete a un destino específico, incluso cuando la topología cambia cuando el paquete está en tránsito.
#### Algoritmo de Inundación
Para enviar un paquete de un origen `u` a un destino `v` se respetan las siguientes reglas:
- `U` manda el mensaje por todas las líneas de salida.
- Cada paquete que llega a un enrutador distinto de `v` se reenvía por cada una de las líneas excepto aquella por la que llegó.
**A este algoritmo se le llama inundación.**
### Problemas de la inundación 
- La inundación genera grandes cantidades de paquetes duplicados. 
- Árbol de envío de paquetes. Cada arco representa un paquete que se envía. 
- Árbol de envío de paquetes es infinito con infinitos duplicados. Osea, se generan infinitas rutas. La causa es la presencia de ciclos en el grafo de la subred.
Hace falta limitar un poco el proceso de inundación dado en la idea anterior para resolver el problema.
#### ¿Qué información deben lleva los paquetes que se difunden?
El enrutador de origen pone un número de secuencia en cada paquete que recibe de sus hosts (así se distingue entre paquetes distintos del mismo enrutador de origen).
#### ¿Qué información debe recordar un enrutador en su registro de paquetes difundidos?
Un enrutador recuerda para cada enrutador de origen, los números de secuencia recibidos, o sea pares `<enrutador de origen, numero de secuencia>`.
#### ¿Qué pasa cuando llega un paquete a un enrutador?
Si llega un paquete a un enrutador con par `<enrutador de origen, número de secuencia>`, verifica si el par está en el registro de paquetes difundidos:
- Si no está, se lo difunde al paquete.
- En caso contrario, se lo descarta.
### Estructura de datos para el registro de paquetes difundidos
Para cada enrutador usar tabla de registro de paquetes difundidos.
![](Screenshot_2026-05-26-20-43-35_19753.png)
#### Para evitar el problema 
De que las listas enlazadas pueden crecer sin limites, agregamos una columna llamada contador que indica el mayor número de secuencia tal que llegaron paquetes con todos los números de secuencia anteriores desde ese enrutador de origen.
![](Screenshot_2026-05-26-20-44-25_7105.png)
Este mecanismo no controla hasta dónde se propaga un paquete. En redes grandes, un mensaje puede recorrer toda la subred incluso cuando solo nos interesa explorar una región cercana al origen o cuando el destino está relativamente cerca.
#### Para limitar el alcance de la inundación y hacerla más eficiente
Introducimos un nuevo mecanismo: El **contador de saltos**. El mismo reducirá la propagación a un radio controlado y evitando que la inundación se extienda más allá de lo necesario.
* Al comienzo en el enrutador de origen se inicializa el contador de saltos del paquete y se reenvía el paquete por todos los enlaces.
* Cuando llega un paquete a un enrutador se decrementa el contador de saltos. 
* Si el enrutador es el enrutador de destino: El paquete no se difunde más.
* Sino si el contador de saltos es cero: El paquete se descarta, sino el paquete se difunde.
## Bloque 5: Inundación de difusión
#### Objetivo
Ya no queremos llegar a un destino único, sino difundir un mensaje a todos los enrutadores de la red, los algoritmos de caminos más cortos se vuelven conceptualmente inapropiados: Están diseñados para optimizar un trayecto punto a punto, no para garantizar cobertura total.
#### Meta
La entrega del paquete a los nodos sin necesidad de precomputar rutas óptimas.
Vamos a considerar inundación de difusión con registro de paquetes difundidos. El registro de paquetes difundidos es la misma estructura de datos de antes.
![](Screenshot_2026-05-26-20-49-27_26708.png)
#### Si el enrutador de origen no está en la tabla
1. Se crea una entrada en la tabla para ese enrutador de origen.
2. Si el número de secuencia es 0: Se agrega 0 en el contador.
3. Sino se crea un nodo en la lista de números de secuencia.
4. Se difunde el paquete.
#### Si el enrutador de origen está en la tabla
- Si el número de secuencia es el siguiente al contador: Se actualiza el contador removiendo elementos en la lista de números de secuencia si es necesario. Luego se difunde el paquete.
- Si el numero de secuencia está en la lista o tiene el valor del contador: Se descarta el paquete.
- Sino: se agrega un nodo con el número de secuencia a la lista de números de secuencia. Luego se difunde el paquete.
#### Problema sin resolver
La inundación de difusión nos permitió enviar información a todos los enrutadores, controlando duplicados y evitando ciclos, pero todavía no resuelve el problema más general: **¿cómo puede cada enrutador construir una visión consistente de toda la red y actualizarla cuando la topología cambia?**
## Bloque 6: Enrutamiento de estado de enlace
#### ¿Qué tareas hace un enrutador?
1. Descubrir sus vecinos.
2. Medir el costo a cada uno de sus vecinos.
3. Construir un paquete diciendo lo que ha aprendido.
4. Enviar este paquete a todos los demás enrutadores (usando inundación de difusión).
5. Computar el camino más corto a cada uno de los otros enrutadores.
#### Este algoritmo es valioso porque
- Responde rápido frente a cambios en la topología de la red.
- Ayuda a derivar algoritmos de enrutamiento más sofisticados como el que usa internet (**OSPF**).
#### ¿Cómo se puede averiguar quiénes son los vecinos de un enrutador?
* Se envía paquete "Hello" a cada línea punto a punto.
* Se espera que el enrutador del otro extremo regrese una respuesta indicando quién es.
#### ¿Cómo se puede hacer para que enrutador conozca retardo a sus vecinos? (ya sabe quienes son sus vecinos)
- Enviar un paquete "ECHO" especial a través de la línea.
- Una vez que llegue al otro extremo, éste debe regresarlo inmediatamente. 
- Uso de temporizadores para medir el tiempo.
- **Método**: Se mide el tiempo de ida y vuelta y se divide por 2.
	* Problema del método: Asume implícitamente que los retardos son simétricos.
Cada enrutador construye un paquete de estado de enlace (**LSP**) conteniendo los retardos a sus vecinos.
#### ¿Qué datos debe tener un LSP?
- Identidad del emisor (para saber de quien se trata).
- Número de secuencia (para distinguir entre distintos LSP de un enrutador) .
- Lista de `<vecino, retardo al vecino>`.
#### ¿Cuándo se pueden construir los LSP?
- Construirlos a intervalos regulares.
- Construirlos cuando ocurra un evento significativo, como la caída o la reactivación de la línea o de un vecino, o el cambio apreciable de sus propiedades.
![](Screenshot_2026-05-26-20-57-58_26690.png)
### Distribución confiable de los LSP
Usamos inundación de difusión con registro de paquetes difundidos.
#### ¿Cómo es la estructura de datos del registro de paquetes difundidos que lleva cada enrutador? 
Basta con para un enrutador de origen indicar el último número de secuencia ya visto de ese enrutador de origen (este sería el LSP más reciente recibido).
#### Cuando llega un LSP a un enrutador,
* Si es nuevo (nuevo número de secuencia mayor que los anteriores), se reenvía a través de todas las líneas, excepto aquella por la que llegó.
* Si es un duplicado (número de secuencia mayor visto, pero repetido), se descarta.
* Si llega un paquete con número de secuencia menor que el mayor visto hasta el momento, se rechaza como obsoleto debido a que el enrutador tiene datos más recientes.
#### ¿Para qué son los LSP que llegan a un enrutador?
- Para construir el grafo de la red de enrutadores interconectados. Por lo tanto es necesario almacenar en búfer en el enrutador los LSP más recientes recibidos de cada origen.
#### ¿Qué elementos puede contener una fila de la tabla del búfer de LSP de un enrutador?
Enrutador de origen, número de secuencia del último LSP, datos de ese LSP.
![](Screenshot_2026-05-26-21-00-33_9514.png)
#### ¿Cuándo se puede crear o actualizar la tabla de enrutamiento de un enrutador?
* Una vez que el enrutador ha acumulado un grupo completo de paquetes de estado del enlace
#### ¿Qué se hace después?
1. Usando los LSP construir el grafo de la subred completa.
	* Cada enlace se representa dos veces, una para cada dirección.
	* Los dos valores pueden promediarse o usarse por separado.
2. Se ejecuta el algoritmo de Dijkstra para construir la ruta más corta a
todos los destinos posibles.
3. Con los resultados del mismo se actualiza la tabla de enrutamiento.
#### Problema
Un protocolo real enfrenta problemas adicionales.
* Por ejemplo, errores en números de secuencia y caída de enrutadores.
* Además es necesario de reducir la carga sobre la red. 
#### Situación
Si llega a corromperse un número de secuencia y se escribe 65540 en lugar de 4 (un error de un bit), los paquetes 5 a 65540 serán rechazados como obsoletos, dado que se piensa que el número de secuencia actual es 65540.
* Para protegerse contra errores en las líneas entre enrutadores se puede confirmar cada LSP que se recibe. 
* ¿Cómo funciona entonces la protección contra estos errores?
	* Antes de actualizarse el número de secuencia más grande, el enrutador manda una confirmación de recepción al transmisor y luego espera una respuesta afirmativa o negativa del transmisor.
		* En el primer caso se actualiza el número de secuencia más grande.
		* En el segundo caso se descarta el LSP que se recibió por estar errado.
#### Asumimos
Que una vez que un LSP más actualizado de un enrutador de origen llega a un enrutador, y no se lo encola para difusión inmediata, sino que se espera un tiempo breve. 
##### ¿Cómo esto cambia la manera a hacer la difusión?
* En ese tiempo puede llegar desde otras líneas el mismo LSP, por lo que no va a ser necesario difundir el LSP por esas líneas.
* También puede llegar un LSP más reciente del mismo origen, por lo que se cambia el LSP en el buffer por otro más reciente evitando así enviar un LSP que se quedó viejo.
* Por lo tanto se puede hacer más eficiente el algoritmo de inundación.
#### ¿Cómo modificar el búfer de LSPs para reflejar esta optimización?
* **Recordar**: recordar que además de difundir el LSP hace falta confirmarlo.
**Usar Banderas que pueden ser**:
- **Banderas de confirmación de recepción**: Indica a dónde tiene que enviarse la confirmación de recepción del paquete.
- **Banderas de envío**: Significan que el paquete debe enviarse a través de las líneas indicadas.
- Si llega un duplicado mientras el original aún está en el búfer, los bits de las banderas tienen que cambiar.
![](Screenshot_2026-05-26-21-13-57_9664.png)
#### Problema
Si los números de secuencia vuelven a comenzar, reinará la confusión (el algoritmo de inundación no está preparado para eso). 
#### ¿Cómo evitamos este problema?
* Usar un número de secuencia de longitud suficiente para que el problema anterior no suceda. Por ejemplo, de 32 bits.
	* Ejemplo: Si un enrutador produce un paquete de estado de enlace cada segundo, llevará 137 años antes de volver a empezar.
#### Problema
Si llega a caerse un enrutador (de origen), perderá el registro de su número de secuencia. Si comienza nuevamente en 0, se rechazará el siguiente paquete.
#### Para evitar esto
 Se puede hacer que la información de un enrutador caído expire a lo largo de la red luego de caerse.
* Una vez identificado que un enrutador está caído:
	- Se propaga la información de este hecho por toda la red.
	- Se hace que la información asociada al enrutador caído expire (paquete pendiente a enviar, número de secuencia más grande recibido, etc.).
	- Así que cuando ese enrutador vuelva a la vida, puede comenzar con número de secuencia 0.
---
# Algoritmos de enrutamiento para WANs jerárquicas
## Bloque 1: Introducción
Dividir la red en regiones (de igual importancia) o áreas (con un área dorsal) no es solo una cuestión administrativa: Cambia la forma en que cada enrutador percibe la WAN.
Ya no todos los enrutadores ven todo, cada uno ve su región o área con detalle y las demás con resúmenes construidos para preservar lo esencial sin arrastrar la complejidad completa.
#### ¿Qué vamos a ver?
* Cómo se construyen esos resúmenes, cómo se propagan, qué ve cada tipo de enrutador y cómo se arma finalmente el grafo global sobre el que se ejecuta Dijkstra. 
* Veremos que la jerarquía es una arquitectura que obliga a repensar el protocolo de estado de enlace: 
	* ¿Qué se inunda?
	* ¿Hasta dónde? 
	* Quién lo genera y cómo?
## Bloque 2: Enrutamiento en red de regiones
### Introducción
#### ¿Cómo adaptar el protocolo de estado de enlace para que funcione cuando cada región solo conoce su topología interna?
Para responderla, vamos a derivar un algoritmo que: 
* Mantiene el estado de enlace dentro de cada región
* Construye resúmenes de región para enviar a otras regiones atravesando enlaces desde un enrutador de borde a otro.
* Y distribuye esos resúmenes entre regiones mediante inundación controlada, que evita bucles y limita el alcance de cada paquete.
#### Resultado
El resultado es un protocolo que permite a cada enrutador reconstruir un **grafo global híbrido**: detallado en su región y resumido en las demás. Sobre ese grafo ejecutará Dijkstra para obtener rutas coherentes en
toda la WAN.
### Tipos de enrutadores
#### Tipos de enrutadores en una región:
- **Interno:** Enrutador que no tiene enlaces hacia otras regiones.
- **De borde:** Enrutador que tiene enlaces hacia otras regiones.
##### ¿Qué ve un enrutador interno sobre su región?
Todos los enrutadores de la región y los enlaces entre ellos. 
* Ejemplo: grafo de la región 5 que ven sus enrutadores.
![451](Screenshot_2026-05-27-20-35-27_17266.png)
##### ¿Qué ve un enrutador en una región sobre una región ajena?
* Ve los enrutadores de borde.
* Ve arcos entre 2 enrutadores de borde si hay caminos dirigidos entre ellos.
* Un arco entre dos enrutadores de borde tiene como peso el del camino más corto entre ellos.
* Ejemplo: Si longitud de camino es cantidad de saltos, un enrutador de región 2 ve de la región 5: `{5A, 5C}` con peso 2.
![360](Screenshot_2026-05-27-20-37-39_31687.png)
##### ¿Cómo sería el grado de toda la red que ve un enrutador?
* Hay que combinar la visión detallada de la región donde está con su visión resumida de las demás regiones.
* **Colocar**: Grafo de su región, los grafos resumidos de las demás regiones y enlaces entre enrutadores de borde de distintas regiones. 
* Ejemplo: Grafo del enrutador `2C`.
![365](Screenshot_2026-05-27-20-40-07_24620.png)
### Paquetes de estado de enlace

##### ¿Cómo sería un paquete de estado de enlace (LSP) de enrutador de dentro de una región?
* Es el LSP con información de retardos a los vecinos de un enrutador.
* Ejemplo: Armar paquete de estado de enlace de `2B`.
	* Enrutador `2B`, número de secuencia.
	* `2A, 1`.
	* `2D, 1`.
#### ¿Cómo convertir la topología interna de una región en un resumen que otros enrutadores puedan usar para enrutar sin conocer todos los detalles?
##### ¿Cómo sería el paquete de estado de enlace que resume una región?
* Nombre de enrutador de borde de región que construye el paquete.
* Número de secuencia (para distinguirlo de paquetes de estado de enlace previos).
* Información del grafo que ve un enrutador ajeno a esa región. 
* Ejemplo: LSP de la región `5`.
	* Enrutador `5A, Seq`.
	* `{5A, 5C}` con peso `2`.
Además, un enrutador de borde necesita construir un segundo tipo de paquete de estado de enlace con los enlaces con otros enrutadores de borde con los cuales está conectado directamente. Lo llamamos **LSP de enlaces externos**.
* Ejemplo: El enrutador `3B` construye el paquete de estado de enlace:
	-  `3B`, número de secuencia.
	- `1C, 1`.
	- `4B, 1`.
![349](Screenshot_2026-05-27-20-45-20_24739.png)
### Fases del protocolo
#### ¿Cuáles serían los pasos de un protocolo de enrutamiento para una red de regiones?
##### Fase 1: Para cada región `A` hacer lo siguiente
1. Se ejecuta el protocolo de estado de enlace dentro de `A`, hasta  completar la difusión de los LSP internos.
2. Cada enrutador de borde construye LSP de enlaces externos.
3. Entre los enrutadores de borde de `A` se elige un enrutador de borde designado `R`. Este enrutador va a ser el único responsable de generar el LSP de resumen de la región.
4. `R` construye el grafo resumido de `A`:
	* Los vértices son los enrutadores de borde de `A`.
	* Y los arcos son todos los pares `{E1, E2}` con costo `C`, donde `C` es el costo mínimo para ir de `E1` a `E2` dentro de `A`.
5. Usando el grafo resumido de `A`, `R` construye el LSP que resume la región `A`.
##### Fase 2
1. Distribución de LSP que resumen en las diferentes regiones.
2. Distribución de LSP de enlaces externos.
3. Construcción del grafo de la red entera de un enrutador
4. Ejecución del algoritmo de Dijkstra sobre esa red.
5. Construcción de tabla de reenvío.
#### Fase 1 del protocolo
##### ¿Cómo se aplicaba el protocolo de estado de enlace dentro de una región hasta la difusión de los paquetes de enlace inclusive?
* Tomamos una región, a ella es aplicable el protocolo de estado de enlace. Entonces cada enrutador de la región hace lo siguiente:
	1. Descubrir sus vecinos (con uso de paquetes HELLO).
	2. Medir el costo a cada uno de sus vecinos (con uso de paquete ECHO).
	3. Construir un paquete diciendo lo que ha aprendido (llamado paquete de estado de enlace - LSP).
	4. Enviar este paquete a todos los demás enrutadores (usando inundación de difusión).
* Para un enrutador de borde construir un LSP de enlaces externos:
	- Cuando envió paquetes HELLO se da cuenta por los nombres de los vecinos que son enrutadores de borde (toma notas de ellos). 
	- Manda paquetes ECHO a los otros enrutadores de borde con los que está conectado para averiguar el retardo a ellos.
	- Con la información de retardos a otros enrutadores de borde, construye el LSP de enlaces externos
##### ¿Cómo construye `R` designado el grafo resumido del área `A`?
1. `R` Construye el grafo `G` completo de `A` (a partir de los LSP internos construidos en el paso 1).
2. Para cada enrutador de borde `B` de `A`: 
	* `R` ejecuta Dijkstra clásico con raíz `B`, obteniendo el árbol de caminos mínimos desde `B` hacia todos los nodos de `A`.
	* `R` extrae de ese árbol los costos mínimos de `B` hacia los demás enrutadores de borde.
3. Con todos estos costos, `R` arma el grafo resumido de la región `A`.
![](Screenshot_2026-05-27-20-52-08_12787.png)
##### ¿Cómo se construye el paquete de estado de enlace de resumen de una región entera?
* La información de ese grafo resumido de la región se vuelca en un LSP resumido de la región.
* Ahora estamos en condiciones de explicar en detalle la fase 2 del algoritmo de enrutamiento en una red de regiones.
* Ya sabemos cómo un enrutador de borde puede representar su región mediante un grafo resumido. Pero un resumen que nadie recibe no sirve para enrutar.
#### Fase 2 del protocolo 
##### ¿Cómo hacer la distribución de los LSP que resumen las diferentes regiones?
* **Cada región `A` genera un único paquete de resumen**
	* El enrutador de borde designado `R` de la región `A` construye el paquete de estado de enlace `P` que resume la región.
-  **Difusión interna del resumen dentro de la región `A`**
	* `R` inunda `P` dentro de la región `A` usando inundación por difusión. Así, todos los enrutadores de borde de `A` reciban `P` y puedan reenviarlo hacia otras regiones.
- **Envío del resumen de `A` hacia otras regiones**:
	* Para cada enrutador de borde `B` de `A` y enlace `E` desde `B` con otra región, `B` envía `P` por `E` (recibiéndolo un enrutador de borde de la otra región).
- **Comportamiento general de un enrutador de borde al recibir un resumen**
	* Cuando un enrutador de borde recibe un LSP que resume una región distinta de la suya: 
		* Lo inunda dentro de su región, usando inundación por difusión.
		* El paquete se reenvía por todos los enlaces hacia otras regiones, excepto: Hacia una región de donde vino, para evitar bucles triviales.
##### ¿Cómo hacer la distribución de los LSP de enlaces externos?
* Un enrutador de borde de región `A` que creó el LSP `P` inunda por difusión dentro de `A` el paquete `P`. 
* Los enrutadores de borde de `A` al recibir `P` lo envían por los enlaces que los conectan con otras regiones.
* Cuando un enrutador de borde de una región distinta de `A` recibe `P`, inunda con difusión su región con el paquete `P`.
* Cuando un enrutador de borde de una región distinta de `A` recibe `P`, manda por los enlaces con otras regiones el paquete `P`.
* Una vez que los resúmenes circulan correctamente, cada  enrutador tiene piezas dispersas de información: Su topología interna y los resúmenes de las demás regiones.  El paso siguiente es ensamblar esas piezas en un grafo global coherente que represente toda la WAN desde su punto de vista.
##### ¿Cuándo se puede hacer la construcción del grafo de la red entera por un enrutador?
Un enrutador (sea de borde o interno) sólo puede construir el grafo global de toda la red cuando:
- Ya recibió todos los paquetes de estado de enlace de resumen de todas las regiones donde no está.
- Además tiene los paquetes de estado de enlace de su región y de los enlaces externos.
##### ¿Cómo un enrutador R construye el grafo entero de la red jerárquica?
El grafo global de toda la red jerárquica construido por `R` es la unión de:
* El grafo detallado de la región de `R` +	grafos resumidos de todas las regiones donde no está `R` + Enlaces inter-regiones entre enrutadores de borde.
	* (Son esenciales porque permiten pegar los grafos resumido entre sí y permiten que el grafo global sea conexo).
##### Ejemplo: Grafo construido por `2C`
![292](Screenshot_2026-05-27-21-00-15_10360.png)
Sobre el grafo total de la red jerárquica construido por un enrutador `R` se ejecuta el algoritmo de Dijkstra. Luego usando el árbol que genera el algoritmo de Dijkstra se construye la tabla de reenvío del enrutador `R`.
### Tipos de inundación
- **Inundación intra-región** de paquetes de estado de enlace de retardos de vecinos a un enrutador (distribuye la topología detallada de la región).
	* Los paquetes solo circulan dentro de la región.
	* Requiere una tabla de paquetes vistos organizada por enrutador de origen (el creador del paquete).
-  **Inundación inter-regiones:**
	* Distribuye los grafos resumidos de cada región y LSP de enlaces externos hacia las demás.
	* Los paquetes circulan entre regiones, atravesando enrutadores de borde.
	* Requiere una tabla de paquetes vistos organizada por región de origen.
- **Inundación intra-región** de paquete de estado de enlace de resumen de la región.
	- Difunde dentro de la región el resumen generado por su enrutador de borde designado.
	* Aquí se usa la misma tabla que inundación inter-regiones, porque el resumen se identifica por región de origen, no por enrutado
##### ¿Por qué se necesitan tablas de inundación separadas?
Porque cada tipo de inundación requiere una tabla que se indexa por un conjunto distinto de identificadores. No existe un índice en común que permita unificación en una sola tabla.
##### ¿Qué otras cosas diferencian las distintas inundaciones?
* Tienen alcances distintos (solo región vs entre regiones).
* Tienen semánticas distintas (topología interna, resumen de región).
* Y requieren reglas de reenvío distintas (qué reenviar, hacia dónde reenviar, qué no reenviar.
## Bloque 3: Enrutamiento en áreas interconectados
### Introducción
En este bloque damos el salto hacia un diseño real usado en Internet: OSPF.  Aquí la jerarquía ya no es simétrica: Aparece un área dorsal (área 0) que actúa como eje de interconexión entre las demás áreas. Esto introduce nuevos roles, nuevas restricciones y nuevas decisiones de diseño. La pregunta que guía este bloque es:
#### ¿Cómo debe modificarse el algoritmo anterior para funcionar en una jerarquía con un área dorsal y múltiples áreas no dorsales, considerando además que las tablas de reenvío apuntan a destinos que representan LAN, y se direccionan interfaces de máquinas?**
El resultado es un protocolo donde cada enrutador construye un grafo global de acuerdo a su posición en la jerarquía, combinando topología detallada de áreas donde está con resúmenes bipartitos de las
demás. Este bloque muestra cómo las ideas del Bloque 2 se transforman en un protocolo operativo, escalable y ampliamente desplegado.
### Grafos de Áreas
#### ¿Cómo representar un área mediante un grafo con pesos en sus arcos?
![](Screenshot_2026-05-27-22-18-33_16816.png)
- Los enrutadores se representan con nodos.
- A cada arco se le asigna un costo o retardo.
- Una conexión punto-punto entre dos enrutadores se representa por un par de arcos, uno en cada dirección. Sus pesos pueden ser diferentes.
- Una red de multi-acceso de enrutadores se representa con un nodo para la red en sí. Los arcos desde el nodo de la red a los enrutadores tienen peso 0.
- Una LAN de computadoras se representa con un nodo. Los arcos desde enrutadores conectados a la LAN tienen un peso.
Para entender cómo enrutar en esta jerarquía más estricta, necesitamos una representación adecuada de cada área.
#### ¿Qué grafo ve un enrutador de un área A en la que no está?
El enrutador ve un grafo reducido `G` de `A` (`G` representa lo único que necesita saber sobre el área `A` para enrutar hacia ella). `G` tiene como nodos:
- Los enrutadores de borde de área que conectan `A` con el área dorsal y las LAN (de computadoras) que existen dentro del área `A`.
* En G hay un arco:
	- Desde cada **EBA** (**Enrutador de Borde de Área**) de `A` hacia cada LAN del área `A`.
	- siempre y cuando existe un camino dirigido desde el EBA hacia esa LAN.
Cada arco de `G` tiene un peso igual al costo del camino más corto desde el EBA a la LAN.
![](Screenshot_2026-05-27-22-21-26_17587.png)
Pero enrutar hacia una LAN no es suficiente: también necesitamos enrutar entre áreas. Para eso, debemos entender cómo se ve el área dorsal desde el punto de vista de los enrutadores internos de un área no dorsal. Aquí aparece el segundo tipo de resumen: el resumen del área dorsal.
#### ¿Qué grafo ve un enrutador R interno a un área A del área dorsal?
`R` construye un grafo reducido `G` del área dorsal (`G` representa lo único que necesita `R` saber sobre el área dorsal para enrutar hacia las demás áreas):
* `G` tiene como nodos los EBA que están conectados al área dorsal.
* En `G` hay un arco:
	* Que va de cada EBA de A a un EBA que no es de `A`
	* Siempre y cuando existe un camino dirigido desde el EBA de `A` hacia el otro EBA.
* Cada arco de `G` tiene un peso igual al costo del camino más corto desde el EBA de A hacia el otro EBA.
![](Screenshot_2026-05-27-22-23-22_9484.png)
Al evitar un grafo completo el paquete de resumen del área dorsal es mucho más liviano, lo que reduce el consumo del ancho de banda durante la inundación.
#### ¿Cómo es el grafo del área dorsal que observa un enrutador dorsal?
Consideramos que un enrutador dorsal ve la topología completa del área dorsal
* Es un grafo que tiene:
	* Como vértices los enrutadores en el área 0 (que pueden ser internos o EBA),
	* Y como arcos los enlaces entre los enrutadores del área dorsal.
	* Cada arco tiene un peso que representa el costo de atravesar ese arco.
* En OSPF el peso de cada arco es configurado por el administrador de la red en lugar de ser estimado automáticamente (como se hacía en los protocolos anteriores).
![](Screenshot_2026-05-27-22-25-06_13920.png)
### Grafos Globales
#### ¿Cómo es el grafo de toda la red que ve un enrutador dorsal R no EBA?
El grafo visto por `R` consiste de la unión de grafos que ya analizamos en detalle:
* Grafo resumido de las áreas que ve `R` (para un área `A` es un grafo bipartito desde los EBA de `A` hacia las LAN de `A`).
* Grafo de la topología completa del área dorsal (ve todos los enrutadores del área 0 y todos los enlaces entre ellos).
![](Screenshot_2026-05-27-22-26-11_30931.png)
#### ¿Cómo es el grafo de toda la red que ve un EBA `R`?
El grafo visto por `R` consiste de la unión de grafos que ya analizamos en detalle:
* Grafo resumido de las áreas que donde no está `R` (para un área `A`, es un grafo bipartito conectando los EBA de `A` con las LAN de `A`).
* Grafo de la topología completa del área dorsal (ve todos los enrutadores del área 0 y todos los enlaces entre ellos con sus costos).
* Grafo de la topología de las otras áreas donde está `R`.
![](Screenshot_2026-05-27-22-27-34_20227.png)
### Paquetes de estado de enlace
#### Paquete de estado de enlace de retardos a los vecinos
Los paquetes de estado de enlace de retardo a los vecinos se construyen dentro de cada área no dorsal y dentro del área dorsal. Cada enrutador `R` interno a un área `A` construye un paquete de estado de enlace que contiene los retardos (costos) hacia cada uno de sus vecinos dentro de `A`.
La estructura del paquete es la misma que en el protocolo de estado de enlace que ya estudiamos: Lista de pares de vecino y retardo a él. La diferencia de OSPF con el protocolo de estado de enlace es
que los retardos (costos) no se estiman automáticamente, sino que son configurados por el administrador de la red.
#### Pasos para construir paquete de estado de enlace de retardo a los vecinos de un enrutador `R` (en área dorsal o las demás áreas).
1. **Averiguar quiénes so los vecinos**: 
	* Cuando un enrutador se inicia, envía mensajes Hello a: Todas las líneas punto a punto, al grupo de todos los enrutadores de su LAN si está en una LAN de enrutadores. 
	* A partir de las respuestas `R` aprende quiénes son sus vecinos.
2. **Recordar que los retardos a esos vecinos fueron fijados por el administrador de red**.
3. Finalmente con la información obtenida **construir el paquete de estado de enlace de retardo a los vecinos de `R`**. 
	* Con la lista de vecinos y los costos configurados, `R` construye su paquete de estado de enlace de retardos a los vecinos, con la misma estructura que en el protocolo de estado de enlace estudiado previamente.
#### Paquete de estado de enlace de resumen de área no dorsal `A`
* Este paquete es construido por un EBA `R` del área `A`.
* Contiene la información necesaria para que los enrutadores de otras áreas puedan alcanzar las LAN de `A` sin conocer su topología interna.
* El paquete incluye para cada LAN de A:
	* El EBA de `A` que la anuncia,
	* El costo mínimo desde ese EBA hasta esa LAN dentro del área `A`.
* En otras palabras: el paquete resume el grafo bipartito que ya estudiamos “EBA de `A` -> LAN de `A`”, donde cada arco está representado por el costo mínimo de su EBA hacia su LAN.
##### Los pasos para construir este tipo de paquete, son
1. **Inundación dentro del área A:** Se distribuyen por inundación los LSP de retardos a vecinos de todos los enrutadores de `A`.
2. **Reconstrucción del grafo del área A:** Con los LSP de retardos a vecinos para los enrutadores de `A`, cada EBA de `A` reconstruye la topología completa del área `A`.
3. **Ejecutar Dijkstra para cada EBA de `A`:** para cada EBA de `A` se ejecuta Dijkstra sobre el grafo del área `A` para obtener su árbol de caminos mínimos hacia todas las LAN del área.	
4. **Construcción del paquete de resumen de `A`:** Usando los arboles de caminos mínimos de los EBA, se construye el paquete de estado de enlace de resumen del área `A`, que contiene los costos mínimos desde cada EBA de `A` hacia cada LAN de `A`.
![](Screenshot_2026-05-27-22-32-15_25526.png)
#### Paquete de estado de enlace de resumen de área dorsal
Para cada área no dorsal `A`, este paquete es construido por un EBA de `A`. Contiene la información necesaria para que desde un área `A`, se pueda alcanzar los EBA de las otras áreas a través del área dorsal. En particular, para un área `A`, el paquete incluye para cada EBA `R` de `A` los costos mínimos para llegar desde `R` hacia los EBA de las demás áreas.
En otras palabras: El paquete resume el grafo bipartito que ya estudiamos:
* EBA de A -> EBA de otras áreas.
* Donde cada arco está etiquetado con el costo mínimo entre esos EBA dentro del área dorsal.
##### Para construir un paquete e este tipo, se deben seguir los siguientes pasos:
1. **Inundación dentro del área dorsal:** Se distribuyen por inundación los LSP de retardos a vecinos de todos los enrutadores de del área dorsal.
2. **Reconstrucción del grafo del área dorsal:** Con esos LSP, un EBA para cada área no dorsal reconstruye la topología completa del área dorsal.
3. **Ejecutar Dijkstra por un EBA de cada área no dorsal:** para cada área no dorsal `A`, un EBA R de esa área ejecuta Dijkstra para cada EBA de `A` sobre el grafo del área dorsal, obteniendo los árboles de caminos mínimos hacia los EBA de las demás áreas no dorsales.
4. **Construcción del paquete de resumen del área dorsal:** Usando los arboles de caminos mínimos de los EBA de `A`, el EBA que los calculó, construye el paquete de estado de enlace de resumen del área dorsal, que contiene los costos mínimos desde cada EBA de `A` hacia EBA de las otras áreas.
![](Screenshot_2026-05-27-22-35-05_30244.png)
### Pasos del protocolo 
1. **Construcción de los paquetes de estado de enlace:** Cada enrutador construye los diferentes tipos de LSP según corresponda:
	* Retardos a vecinos,
	* Resúmenes de áreas no dorsales,
	* Resúmenes del área dorsal, (los pasos para cada tipo ya fueron detallados).
2. **Inundación de resúmenes de áreas:** Para cada área no dorsal `A`, el EBA de `A` que construyó el paquete de resumen de `A` lo inunda hacia el área dorsal y hacia las demás áreas.
3. **Inundación de resúmenes del área dorsal:** Para cada área `A`, el EBA que construyó el resumen del área dorsal inicia la inundación dentro del área `A` de ese paquete del resumen del área dorsal.
4. **Construcción del grafo de la red:**
	* Cada enrutador interno de un área `A` construye su grafo global.
	* Cada enrutador interno del área dorsal construye su grafo global.
	* Cada EBA `R` de un área `A` construye su grafo global.
	* Como se construye cada grafo global fue explicado anteriormente.
5. **Ejecución del algoritmo de Dijkstra modificado:** Cada enrutador `R` ejecuta el algoritmo de Dijkstra modificado obteniendo su grafo de caminos mínimos de `R` hacia los demás enrutadores.
6. **Construcción de la tabla de reenvío:** A partir del grafo de caminos mínimos, cada enrutador R construye su tabla de reenvío.
#### Base de datos de estado de enlace (BDEE)
Cada enrutador mantiene una BDEE que contiene todos los LSP que ha recibido. La BDEE debe ser creada al iniciar el enrutador, y luego mantenerse actualizada.  Dentro de un área todos los enrutadores deben tener la misma BDEE, para construir misma visión del grafo y, por lo tanto, tablas de reenvío coherentes.
* Consecuencias de tener una BDEE:
	* La BDEE almacena información que un enrutador puede intercambiar con sus vecinos.
	* La BDEE se actualiza cuando el enrutador recibe LSP más nuevos que los que ya tiene.
### Sincronización de dos enrutadores adyacentes
Ya sabemos qué información debe circular. El siguiente desafío es cómo hacerla circular  correctamente. A diferencia del Bloque 2, OSPF usa un único mecanismo de inundación con ámbitos distintos y requiere sincronización entre enrutadores adyacentes para mantener coherencia.
* La inundación del protocolo opera mediante intercambio de información de estado de enlace entre enrutadores adyacentes.
#### ¿Qué tipos de paquetes se necesitan para intercambiar información entre enrutadores adyacentes? 
- **Paquete de descripción de base de datos (PDBD):**
	 * Contiene un resumen de todos los LSP que el enrutador emisor contiene en su BDEE.
	 * En resumen incluye:
		* El enrutador emisor y número de secuencia del LSP,
		* y, si el emisor es un EBA, también el tipo de LSP (porque un EBA genera varios tipos).
	* El receptor compara estos números de secuencia con los de su propia BDEE para determinar qué LSP faltan o están desactualizados.
- **Paquete de pedido de estado de enlace (PPEE):** Se usan para solicitar LSP específicos que el receptor necesita, según lo que detectó al analizar el PDBD. 
- **Paquete de actualización de estado de enlace (PAEE):** Se usa para mandar LSP asociado al enrutador emisor que le fue solicitado.
- **Paquete de confirmación de estado de enlace (PCEE):** Se usa para confirmar la recepción de un PAEE. Es para garantizar que la inundación es confiable y que no se pierden LSP.
#### ¿Cómo sincronizan sus BDEE dos enrutadores adyacentes?
Dos enrutadores vecinos deben sincronizar sus BDEE para asegurarse que ambos tienen exactamente la misma información de estado de enlace.
* Para coordinar el proceso uno de los vecinos actúa como maestro y el otro como esclavo.
* El maestro controla el intercambio de los PDBD.
* Durante la sincronización, los vecinos intercambian los siguientes tipos de paquete en el orden:
	* PDBD: Resumen de los LSP que cada uno tiene.
	* PPEE: Pedidos de LSP que faltan o están desactualizados.
	* PAEE: Actualizaciones que contienen los LSP completos.
	* PCEE: Confirmaciones de recepción de los PAEE.
#### Problema
En una LAN de enrutadores, sería muy ineficiente que cada enrutador intercambie mensajes de estado de enlace con todos los demás enrutadores de la LAN. Esto implica demasiadas sincronizaciones y demasiados paquetes.
* ¿Cómo evitar todo este trabajo? Elegir un **enrutador designado (DR)**
* El DR es el punto central de sincronización: es el enrutador con el que todos los demás enrutadores de la LAN intercambian y sincronizan su BDEE. De esta manera no se necesita que cada par de enrutadores se sincronice entre sí.
* El DR se encarga de recibir y distribuir la información de estado de enlace dentro de la LAN y reduce la complejidad de cuadrática a lineal en cantidad de sincronizaciones.
#### ¿Cuándo se inicia una sincronización nueva entre enrutadores adyacentes?
* Cuando un enrutador arranca.
* Cuando un enrutador detecta un nuevo vecino.
* Cuando un enlace vuelve a estar activo.
* Cuando cambia el enrutador designado en una LAN de enrutadores
* Cuando un vecino se reinicia.
	* En OSPF no hay un orden global de sincronización: cada adyacencia se sincroniza cuando se forma.
#### ¿cómo se propaga la sincronización por el área?
* La sincronización se propaga como una ola, pero no desde un único origen.
* La sincronización global del área emerge de:
	* sincronizaciones locales entre vecinos y
	* inundación confiable de LSPs.
	* No existe un árbol de sincronización, ni un coordinador del área. La coherencia aparece como efecto emergente del diseño.
#### ¿Hace falta hacer sincronización periódica del área?
No es necesario porque la coherencia se mantiene mediante:
* Sincronización puntual entre vecinos al formarse la adyacencia,
* Inundación confiable de los LSP (cuando llega uno más nuevo se propaga),
* Refresco periódico de los LSP (cada 30 minutos):
	* los LSP viejos expiran,
	* los LSP se regeneran y
	* la red se resincroniza suavemente (esto es un mantenimiento; no sincronización global)
* Sincronización automática cuando un enrutador arranca o cambia de rol.
### Mecanismo de inundación 
A diferencia del protocolo de enrutamiento en redes de regiones, aquí no se usan múltiples mecanismos de inundación separados, sino un único mecanismo de inundación, pero cada clase de LSP tiene un ámbito que determina hasta dónde se propaga.
Cada tipo de LSP tiene un alcance diferente, y eso puede dar la impresión de que existen múltiples inundaciones, pero en realidad es el mismo algoritmo aplicado a distintos ámbitos.
* Para LSP de retardo a vecinos el ámbito es dentro del área donde se originan.
* Para LSP de resumen de área no dorsal A: el ámbito es el área dorsal y las otras áreas.
* Para LSP de resumen de área dorsal el ámbito es el área del EBA que creó ese resumen.
#### ¿Se inundan primero los LSP de retardos a los vecinos y después los resúmenes?
Cada enrutador origina LSP cuando corresponde, y cada LSP se inunda según su ámbito sin un orden global rígido. Pero conceptualmente, para entender el proceso se lo puede pensar así:
* Dentro de cada área primero se inundan los LSP de retardos a vecinos. Esto permite reconstruir la topología interna.
* Luego, los EBA calculan los resúmenes de área (no dorsal y dorsal) y esos resúmenes se inundan donde corresponde, según su ámbito.
#### Aplicación del algoritmo de Dijkstra**
Para un enrutador `R` de la red se puede ejecutar el algoritmo de Dijkstra sobre el grafo de la red construido por `R`. Para obtener dicho grafo usar la BDEE de `R`. Dijkstra calcula mediante un árbol el camino más corto desde `R` a cualquier otro nodo en el grafo.
* Sin embargo, ahora aparece una novedad: Un enrutador puede tener varios caminos mínimos simultáneos hacia un destino. Necesitamos entonces una versión modificada de Dijkstra que preserve todos los predecesores mínimos.
* Queremos recordar el conjunto de caminos más cortos entre dos nodos y durante el envío de paquetes que el tráfico se divida entre ellos. 
#### Problema ¿Cómo es un algoritmo adecuado para ello?
Se puede modificar el algoritmo de Dijkstra:
* No cambia la lógica del algoritmo,
* Sólo cambia qué información que se guarda durante la ejecución.
* En vez de guardar un único predecesor por nodo, se guardan todos los predecesores que dan lugar a un costo mínimo.
* Esto convierte el resultado en un grafo acíclico de caminos mínimos y no un árbol.
![](Screenshot_2026-05-27-22-47-13_24184.png)
Con el DAG de caminos mínimos, podemos finalmente construir la tabla de reenvío. A diferencia del Bloque 2, ahora un destino puede tener varios nexthops válidos. Veamos cómo se deriva esa tabla.
Construcción de la tabla de reenvío para un enrutador `R`. El enrutador `R` toma el grafo preds producido por Dijkstra modificado ejecutado por `R`. Este grafo contiene para cada destino `d`, todos los predecesores que llevan a un camino de costo mínimo.
Para un destino `d`:
1. Se consideran todos los predecesores de d en preds.
2. Para cada uno de esos predecesores, se sube recursivamente por los predecesores de preds hasta llegar a nodos que son vecinos directos de `R`.
3. Todos los vecinos directos de `R` alcanzados por algún camino mínimo se convierten en líneas de salida en la tabla de reenvío para el destino d.  En otras palabras: Para cada destino `d`, las líneas de salida de `R` son todos los vecinos de `R` que aparecen en algún camino desde `R` hasta d en el grafo preds.
![](Screenshot_2026-05-27-22-49-00_23078.png)
# Organización y reenvío para interredes
Exploraremos cómo múltiples redes — con tecnologías, tamaños de paquete, protocolos y proveedores distintos — pueden coordinarse para comportarse como una sola interred funcional, y qué mecanismos arquitectónicos permiten que el reenvío extremo a extremo sea posible incluso en la presencia de heterogeneidad.
Responderemos las siguientes preguntas:
* ¿Qué significa realmente “interconectar redes” y qué tensiones emergen cuando intentamos que sistemas distintos colaboren? (Bloque 1)
* ¿Cómo se resuelven incompatibilidades entre redes de distintas tecnologías que deben intercambiar paquetes?
* ¿Qué tipos de mecanismos esenciales para la interoperabilidad hace falta	definir para garantizar continuidad en el servicio extremo a extremo?
* ¿Cómo evitar que las tablas de reenvío en una interred crezcan sin control y comprometan la escalabilidad del sistema?
## Bloque 1: Qué es una interred y otros conceptos básicos
Una **interred** es un conjunto de WAN interconectadas. No todas las WAN tienen el mismo protocolo, y para ir de una WAN a otra se usan dispositivos como enrutador multiprotocolo o puerta de enlace.
![](Screenshot_2026-05-27-22-56-51_21368.png)
Al pasar de una red a otra de tecnología distinta, surgen problemas:
* Con frecuencia se necesitarán conversiones de protocolo.
* Se necesitarán conversiones de direcciones.
* Diferentes tamaños máximos de paquetes usados por las diferentes redes.
##  Bloque 2: Organización de una interred
En este bloque analizaremos cómo se estructura una interred cuando múltiples redes — cada una con su propia tecnología, proveedor, modelo de servicio y escala — deben coexistir y cooperar. 
La organización no es un simple problema de conexión física: es un desafío de coordinación entre dominios administrativos, de compatibilidad entre tecnologías y de economía del tránsito.
#### Los  tipos de redes proveedoras de servicios de red que pueden interconectarse:
* Redes de acceso
* Redes regionales
* Redes globales de tránsito 
(visto en la introducción)
![](Screenshot_2026-05-27-22-58-42_17139.png)
### Relaciones entre redes
#### Relación proveedor- consumidor:
La red proveedora provee servicio de tránsito a la red consumidora. La red cliente paga a la red proveedora para entregar paquetes a otros destinos y recibir paquetes enviados de otros destinos.
* Ejemplo: AS1 red proveedora de AS2, AS3 y AS4.
![](Screenshot_2026-05-27-23-00-33_18255.png)
#### Relación de compañerismo
Los compañeros no se cobran por mandarse mensajes entre sus destinos.  El compañerismo no es transitivo. 
* Ejemplo: AS2 es compañero de AS3, AS3 es compañero de AS4.
	* (pero como no es transitivo, AS2 no es compañera de AS4)
#### Multihoming
Significa que una red consumidora está conectada con varias redes proveedoras. Esta técnica es usada para mejorar la confiabilidad, por si el camino por uno de los proveedores de servicio de red falla.
## Bloque 3: Reenvío entre redes
Habiendo entendido cómo se organiza una interred —sus proveedores, jerarquías y relaciones de cooperación— ahora podemos pasar del quién conecta al cómo circulan realmente los paquetes a través de esa estructura.
La organización administrativa y tecnológica de una interred establece el contexto, pero no resuelve por sí misma los desafíos operativos del reenvío extremo a extremo.
Este bloque se centra en los mecanismos que permiten que un paquete viaje a través de redes heterogéneas sin perder continuidad semántica ni funcional. Abordaremos tensiones clásicas: distintos tamaños máximos de paquete, diferentes formatos de encabezado, modelos de servicio
incompatibles y la necesidad de preservar el flujo extremo a extremo.  Estudiaremos dos grandes familias de soluciones: **fragmentación,** que permite adaptar paquetes a los límites de cada red, y **entunelamiento**, que encapsula paquetes para atravesar tecnologías intermedias.
### Redes con distintos tamaños de paquete máximo
Cada red impone un tamaño máximo a sus paquetes. Las cargas útiles máximas van desde 48 bytes (celdas ATM) hasta 65515 bytes (paquetes IP).
Sin embargo, si un paquete grande P quiere viajar a través de una red cuyo tamaño máximo de paquete es bastante más pequeño que P, las puertas de enlace dividen los paquetes en fragmentos, enviando cada fragmento como paquete de interred individual. Las redes tienen el problema de unir nuevamente los fragmentos.
#### ¿Cómo fragmentamos? Vemos distintos tipos de fragmentación
##### Fragmentación transparente:
la Puerta de enlace de salida de la red hace reensamblado de fragmentos.
* Cuando un paquete de tamaño excesivo llega a una puerta de enlace, esta lo divide en fragmentos.
* Todos los fragmentos se dirigen a la misma puerta de enlace de salida, donde se recombinan las piezas.
* Las redes ATM (de circuitos virtuales) tienen hardware especial para esta estrategia.
![](Screenshot_2026-05-27-23-03-01_54.png)
Esto tiene las siguientes desventajas:
* Sobrecarga para reensamblar y volver a fragmentar repetidamente.
* Todos los paquetes deben salir por la misma puerta de enlace (afecta el desempeño).
##### Fragmentación no transparente
El reensamblado de paquetes solo ocurre en el host de destino.
* Una vez que se ha fragmentado un paquete, cada fragmento se trata como si fuera un paquete original. Todos los paquetes pasan por la puerta de enlace de salida.
* La recombinación ocurre en el host de destino.
* IPv4 funciona de este modo.
![](Screenshot_2026-05-27-23-05-03_15126.png)
Como desventaja, tiene que:
* Requiere que todos los hosts puedan hacer el reensamblado.
* Al fragmentarse un paquete grande aumenta la sobrecarga total, pues cada fragmento debe tener un encabezado.
Para determinar el tamaño de los fragmentos, el protocolo de interred define un tamaño de fragmento elemental. Al fragmentarse un paquete todas las partes son iguales al tamaño de fragmento elemental, excepto la última que puede ser más corta.
#### ¿Cómo saber a qué pertenece un fragmento?
Se numera el paquete original.
#### ¿Qué informaciones necesitamos saber sobre un fragmento?
* Idea 1: Número de fragmento y cantidad total de fragmentos  
* Idea 2: desplazamiento del fragmento en el paquete original y si hay mas fragmentos. Esto es lo que es usado por IPv4.
#### Fragmentación en IPv4
Los enlaces de red tienen MTU (tamaño máximo de transferencia) que corresponde a la trama a nivel de capa de enlace más larga posible. El campo de identificación es necesario para que el host de destino determine a qué datagrama pertenece un fragmento recién llegado.
* Todos los fragmentos de un datagrama contienen el mismo valor en el campo de identificación.
![359](Screenshot_2026-05-27-23-07-49_31187.png)
![](Screenshot_2026-05-27-23-08-27_27266.png)
MF es un bit que significa más fragmentos. Todos los fragmentos excepto el último tienen establecido este bit, que es necesario para saber cuándo han llegado todos los fragmentos de un datagrama.  
El desplazamiento del fragmento (offset) indica en qué parte del datagrama actual va este fragmento. Todos los fragmentos excepto el último del datagrama deben tener un múltiplo de 8 bytes que es la unidad de fragmentación elemental.
Dado que se proporcionan 13 bits, puede haber un máximo de 8192 fragmentos por datagrama.
DF de un bit significa (cuando fijado en 1) una orden de no fragmentar (porque el destino es incapaz de juntar las piezas de nuevo).}
![](Screenshot_2026-05-27-23-09-16_27833.png)
### Redes con distintos formatos de paquetes
Después de estudiar la fragmentación como estrategia para adaptar paquetes a los límites físicos y tecnológicos de cada red, podemos reconocer que este mecanismo resuelve solo una parte del problema de interoperabilidad: qué hacer cuando el obstáculo es el tamaño del paquete.
Sin embargo, en una interred real también aparecen incompatibilidades más profundas, como formatos de encabezado distintos, modelos de servicio incompatibles o incluso tecnologías que no pueden interpretar directamente los paquetes de otra red.
En esos casos, fragmentar no alcanza. Necesitamos un mecanismo que permita transportar un paquete “tal cual es” a través de una red que no lo entiende. Ese mecanismo es el **entunelamiento**, que veremos a continuación como una forma de encapsular paquetes para que puedan atravesar tecnologías intermedias sin perder su identidad original.
#### Problema: Un host de origen h1 y de destino h2 están en la misma clase de red
pero hay una red diferente en medio.  
* ¿Cómo hacer para mandar un paquete de h1 a h2?  
	* Usar entunelamiento  
	* Los paquetes son encapsulados en la red del medio usando un encabezado de ésta.
#### Ejemplos de entunelamiento
![](Screenshot_2026-05-27-23-11-12_16071.png)
## Bloque 4: Tablas de reenvío en interredes 
 Después de analizar cómo se logra el reenvío entre redes heterogéneas —mediante fragmentación, entunelamiento y otros mecanismos de adaptación— estamos en condiciones de mirar un problema de otra escala: cómo se decide hacia dónde reenviar.
* Resolver incompatibilidades técnicas permite que un paquete pueda circular, pero la interred también necesita saber por qué camino enviarlo.
* A medida que crece el número de redes y prefijos, esta decisión se vuelve cada vez más costosa si no se aplican técnicas de compresión y organización del espacio de direcciones.
* En el próximo bloque estudiaremos cómo la agregación de prefijos y la estructuración jerárquica del direccionamiento permiten que las tablas de reenvío sigan siendo manejables incluso en interredes de gran tamaño
En este bloque exploraremos cómo una interred escala su capacidad de reenvío sin que las tablas de los enrutadores se vuelvan inmanejables.
* A medida que crece el número de redes, prefijos y destinos, mantener una entrada por cada LAN se vuelve impracticable.
* Analizaremos cómo la representación jerárquica de destinos y la agregación de prefijos permiten reducir drásticamente el tamaño de las tablas, preservando eficiencia y velocidad de búsqueda.
#### Asumimos
Que los destinos son LANs y que direccionamos interfaces de máquinas. Consideremos un enrutador R en la interred ¿Tiene sentido que R contenga una fila por cada LAN en la interred?
* Como la interred tiene demasiadas LAN, esto va a hacer la tabla de reenvío demasiado grande. Por lo tanto la respuesta es no.
#### ¿Cómo se puede achicar entonces la tabla de reenvío? 
* Idea 1: que los destinos lejanos (en otras redes distintas de R) sean rangos de direcciones de interfaces que cubren varias LAN.
* Idea 2: que los destinos lejanos (en otras redes distintas de R) sea un prefijo que contenga varios prefijos de LANs. Esto es usado por IP. A esto se le llama agregación de prefijos.
![](Screenshot_2026-05-27-23-13-22_15826.png)
Cuando se usa agregación de prefijos, éste es un proceso automático. **La agregación de prefijos es fuertemente usada en la Internet y puede reducir el tamaño de las tablas de los enrutadores en alrededor de 200.000 prefijos.**
![](Screenshot_2026-05-27-23-13-58_22490.png)
# Enrutamiento de interredes
## Introducción
Organización del enrutamiento en 2 niveles
El modelo de dos niveles permite escalar el enrutamiento sin que cada enrutador deba conocer la topología completa de la interred. Los dos niveles de enrutamiento que vemos, son:
* Protocolo de puerta de enlace interior (PPEI). Cada red usa su propio protocolo interno. Pueden ser distintos entre sí. 
* Protocolo de puerta de enlace exterior (PPEE). Es el protocolo en común para enrutar entre redes. Todas las redes deben usar el mismo PPEE. 
#### ¿Cuántos protocolos distintos PPEI pueden usarse?
La interred puede usar diferentes protocolos PPEI en sus redes.
#### ¿Porque estudiar los protocolos de puerta de enlace exterior (PPEE)?
Es necesario estudiar protocolos de puerta de enlace exterior (PPEE) porque:
* Las tablas de reenvío deben permitir mandar mensajes entre máquinas conectadas a redes diferentes.  El PPEE permite agregar información a ser usada con ese fin a las tablas de reenvío de los enrutadores.
* El enrutamiento de PPEE se preocupa de establecer las rutas a usar (que pasan por diferentes WAN) para permitir que se comuniquen máquinas pertenecientes a distintas WAN.
#### Lo que un protocolo exterior NO puede asumir es que:
- No puede ver la topología interna de otras redes.
- No puede depender de que todos usen el mismo PPEI.
- No puede imponer métricas globales.
- No puede forzar a los enrutadores internos a participar. 
El diseño debe funcionar con mínima información.
#### Limitaciones Fundamentales
Encontrar un camino óptimo hacia un destino es imposible en la práctica, Por que cada red usa su propio protocolo interno y asigna métricas con criterios independientes. No existe una métrica global coherente para comparar caminos que cruzan por varias redes.
Uno podría querer resumir cada WAN e inundar la interred con los resúmenes de WANs para después poder armar el grafo de la interred en cada enrutador, sin embargo, esto no es posible, porque:
- Un proveedor de servicios de red no quiere que la competencia sepa detalles internos de su WAN (por ejemplo, cuántas puertas de enlace tiene, cómo están interconectadas entre sí) – esto es **ocultación hacia adentro**.
- Un proveedor de red puede querer ocultar con qué otras WAN está interconectada – esto es **ocultación de relaciones con otras WAN**. Por ejemplo, una WAN puede querer ocultar que esta conectada a otra WAN vecina, porque no le resulta rentable económicamente, aunque sea el camino físico más directo.
- Un proveedor quiere que las rutas que se usen hacia un destino sea por acuerdos de negocio con otras WAN o por políticas, para proteger los acuerdos comerciales y visibilidad de terceros.
##### Por lo tanto
* **Visibilidad limitada:** Un enrutador en una inter-red sabe que puede llegar a un destino, pero no tiene idea de cómo es el camino por dentro de las otras WAN.  Cada WAN se comporta como una caja negra para las demás.
* **Soberanía de WAN**: Una puerta de enlace tiene el derecho político de decidir qué información comparte y qué tráfico permite pasar.
#### Como no se usan resúmenes de WANs para armar un grafo global, ¿De qué otra manera puede controlar una WAN la información que quiere hacer visible?
* Anuncio de caminos: Avisando a WANs vecinas caminos a prefijos (LANs o agregación de ellas).  
* Listas de ruta: se intercambian caminos que indican la lista de WANs por las que debe pasar para llegar a esos destinos.
#### ¿Qué requisitos pedir para un protocolo de puerta de enlace exterior?
* **Robustez y alcance:**  
	* Visibilidad global de destinos: Transportar avisos sobre destinos que no están en la misma WAN.  
	* Escalabilidad: Abstraer la complejidad interna de otras WAN para no saturar las tablas de reenvío.  
	* Garantizar rutas libres de bucles: Encontrar caminos hacia prefijos evitando ciclos de enrutamiento en ellas.  
* **Autonomía y negocio:**  
	* Selección inteligente: capacidad de elegir entre múltiples caminos posibles hacia un destino basándose en la conveniencia.  
	* Soberanía de las WAN : Respetar las políticas de cada WAN a lo largo del camino.
#### ¿Qué sería una Política?
Una política son reglas que expresan:  
* Preferencias de enrutamiento (qué caminos se prefieren).  
* Restricciones de enrutamiento (qué caminos están prohibidos).
#### ¿Qué información ve un enrutador en el nivel PPEE? 
* Abstracción de topología : No ve la infraestructura interna de otras WAN. 
* Visión parcial y agregada: Solo percibe prefijos, los identificadores de algunas WAN, algunas puertas de enlace (por ejemplo, de contacto directo). 
#### Implementación
Los PPEE se ejecutan sobre las puertas de enlace (enrutadores mulitprotocolo). 
#### Responsabilidades críticas de las puertas de enlace:
Para que la interred sea estable y rentable, la puerta de enlace debe:
* Seleccionar Rutas: Evaluar y elegir la mejor opción entre múltiples caminos hacia un mismo destino (basándose en métricas y políticas).
* Filtrar Anuncios: Publicar rutas hacia sus vecinos únicamente si cumplen con las políticas y acuerdos comerciales locales. 
* Transparencia de Camino: Avisar a sus vecinos el camino exacto (secuencia de WANs) que están usando.
La Puerta de enlace es un ‘guardian’ de los intereses de su WAN
Con esta organización conceptual, ahora podemos derivar dos protocolos concretos: 
* uno simple basado en inundación controlada,
* y otro similar a BGP.
## Enrutamiento basado en inundación
Antes de hablar de protocolos sofisticados, conviene comenzar por el extremo opuesto: ¿qué pasa si intentamos resolver el enrutamiento entre redes usando solo las herramientas que ya conocemos?
En este bloque vamos a construir un protocolo exterior sin optimizaciones, sin agregación, sin sesiones, sin prefijos. Sólo con: inundación, caminos explícitos, y direccionamiento de enrutadores. Este modelo deliberadamente simple nos permite ver, sin distracciones, qué problemas  estructurales debe resolver cualquier protocolo de enrutamiento entre WANs
El objetivo no es eficiencia, sino transparencia conceptual: ver el mecanismo desnudo.
#### Suposiciones iniciales
* Consideramos una interred formada por varias WAN interconectadas.  
* Asumimos que las puertas de enlace pertenecen simultáneamente a las WAN que interconectan.  
* Objetivo: construir un protocolo de enrutamiento adecuado para este tipo de interredes.  
* Consideramos 'destinos' a los enrutadores conectados a LAN. Este conjunto es mucho más pequeño que el total de los enrutadores presentes en todas las WAN.
### Abstracción de la interred en los enrutadores
No tiene sentido que cada enrutador vea toda la topología de la interred de manera detallada, porque una interred completa es demasiado grande para que cada enrutador mantenga toda su topología.
#### ¿Cómo reducimos la cantidad de información que debe ver un enrutador? Basta con que cada enrutador conozca de otra WAN: 
* Sólo algunos destinos. 
* Sólo algunas puertas de enlace. 
* Sólo alguna otra WAN con la que esta conectada.
No necesita ver la topología interna completa de todas las WAN.
### Grafo de Interred
#### ¿Cómo podemos representar el grafo de una interred?
* los nodos son enrutadores multiprotocolo,
* Y un lado entre dos enrutadores multiprotocolo significa que esos enrutadores están conectados vía una WAN.
![](Screenshot_2026-05-27-23-26-42_6131.png)
### Información de rutas
El protocolo no puede calcular caminos óptimos globales, pero igual necesita algún tipo de información de ruta para: 
- evitar loops, 
- comparar rutas alternativas,
- elegir un camino razonable, 
- exportar rutas entre WANs
#### La información mínima de rutas que sirve para todo esto
* WAN-PATH: lista de pares: <puerta de enlace, WAN> Cada par indica por qué puerta y por qué WAN pasó el aviso en su recorrido hacia al enrutador de destino.
* Destino: La dirección del enrutador de destino al que se refiere la ruta
![](Screenshot_2026-05-27-23-27-53_15821.png)
#### ¿Cómo hacer para propagar información de rutas?
* Si la ruta a anunciar entra por puerta de enlace P entonces, se inunda por difusión esa ruta por todas las WAN conectadas a P donde esa ruta es válida, menos por la WAN por la que llegó.

El protocolo de enrutamiento que vamos a definir usa únicamente dos tipos de avisos: 
- Aviso de ruta: informa la existencia de un camino hacia un destino. 
- Aviso de remoción de ruta. Invalida un camino previamente enunciado.
### Avisos de rutas
Un aviso de ruta contiene tres elementos fundamentales: 
- Destino = R (dirección del enrutador al que se refiere la ruta) 
- Secuencia = n (número de secuencia que permite identificar cuál es el aviso más reciente) 
- Camino (WAN-PATH): lista de pares que describe por dónde pasó el aviso en su recorrido hacia el destino.
#### Relación con la tabla de inundación
- La tabla de inundación no guarda rutas completas.
- Sólo guarda referencias a los paquetes de aviso.
- Los paquetes de aviso con sus caminos explícitos se guardan localmente en el enrutador.
#### Inundación
Tabla de inundación:
- Guarda entradas de la forma:<puerta de enlace de origen, destino, número de secuencia, id_paquete>
- Para cada puerta de enlace de origen, mantiene una lista ordenada lexicográficamente por:
	* Destino y 
	* Número de secuencia.
##### ¿Qué referencia?
Cada entrada apunta al paquete de aviso correspondiente, que contiene el camino explícito (WAN-PATH). Los paquetes referenciados se guardan localmente en el enrutador.
##### ¿Para qué sirve?
- Mantener un registro histórico de ruta vistas (útil para comparar rutas y elegir la mejor).
- Saber cuál es el aviso mas reciente para cada destino (comparando secuencias).
- Evitar reprocesar avisos viejos (si llega aviso con secuencia menor o igual, se descarta).
- Evitar loops (el camino explícito permite detector si la Puerta ya apareció).
#### Exportación de avisos
Situación: Una puerta de enlace recibe un aviso por la WAN_A a la que pertenece y decide exportarlo a la WAN_B ¿Cuáles son los pasos del proceso? 
1. Aplicar política de salida: la puerta de enlace evalúa: cumple_política_salida(aviso, WAN_B) 
2. Si no cumple: no se exporta. 
3. Si se cumple: extender el camino explícito. Antes de exportar, se agrega al final de la WAN-PATH: . Esto registra por dónde llegó el aviso. Se reenvía el aviso por WAN_B. Se envía el aviso actualizado hacia la WAN vecina, permitiendo que otras puertas de enlace conozcan la ruta
Resultado de la exportación de aviso: El camino explícito crece cada vez que una ruta cruza de una WAN a otra, permitiendo: rastrear el recorrido, evitar loops, comparar rutas, y aplicar políticas de exportación de manera local.
#### Origen del primer aviso de ruta
##### ¿De dónde sale el primer aviso? 
Del propio destino R: R anuncia su propia existencia, genera el primer aviso de ruta, y ese aviso comienza a inundarse dentro de la WAN donde se encuentra.
Esto marca el nacimiento de la ruta. Notar que Si R está en WAN_A: 
* una puerta de enlace P que está en la WAN_A y WAN_B, recibe aviso emitido por R dentro de WAN_A. 
* Luego P puede exportarlo a WAN_B (si su política de salida lo permite)
### Avisos de remoción
Un aviso de remoción indica que una ruta previamente anunciada deja de ser válida.  Incluye el camino a remover.
Las rutas una vez anunciadas, permanecen vigentes hasta que un aviso de remoción explícito las invalida. 
#### ¿Cuál es el comportamiento del enrutador al recibir un aviso de remoción? 
1. Eliminar la ruta correspondiente: se borra de la tabla de inundación la entrada indicada al camino indicado. 
2. Propagar la remoción: El aviso se reenvía igual que un aviso de ruta, respetando políticas de salida.
3. Recalcular la mejor ruta: Se elige una nueva ruta entre las restantes (si existen)
4. Actualizar la tabla de reenvío: si hay una nueva mejor ruta, se instala. Si no la hay, se elimina la entrada
#### ¿Dónde se genera un aviso de remoción? 
Cualquier puerta de enlace que detecte que una ruta ya no es válida, puede generar un aviso de remoción. 
#### ¿Cuándo detecta una puerta de enlace que debe generar un aviso de remoción?
Cuando la puerta de enlace G pierde conectividad hacia el destino R en su WAN_A porque el enrutador se apaga o se desconecta. G genera: 
* `AVISO_REMOVER(destino=R, secuencia=nueva, camino=[], alcance=GLOBAL)`
El mismo debe propagarse globalmente.  
Un cambio de política de entrada que invalida rutas ya aceptadas, lleva a generar avisos de remoción.  Esta remoción debe hacerse dentro de la WAN donde se aplica la política. El parámetro alcance en AVISO_REMOVER debe fijarse en LOCAL.
Un cambio de política de salida no genera remociones, porque las rutas ya anunciadas permanecen vigentes hasta que se remuevan explícitamente.
### Independencia entre dominios
Cada WAN funciona como un dominio autónomo: sus decisiones internas no deben afectar automáticamente a otras WAN. 
#### ¿Qué significa la independencia entre dominios? 
- Cada WAN controla qué rutas acepta (mediante sus políticas de entrada.) 
- Cada WAN controla qué rutas exporta (mediante sus políticas de salida.) 
- Los cambios internos no se propagan hacia afuera (por ejemplo, remociones locales de rutas por políticas.) 
- Las rutas exportadas permanecen válidas en otras WAN aunque la WAN de origen cambie sus reglas internas
#### La independencia entre dominios es importante porque:
- Evita que decisiones locales contaminen toda la interred. 
- Permite que cada WAN tenga sus propias reglas, preferencias y restricciones. 
- Mantiene la coherencia del protocolo: las remociones globales solo ocurren por pérdida real de conectividad, no por cambios internos.
Resumen: Independencia entre dominios = cada WAN es un espacio de inundación autónomo, con políticas propias, y sus decisiones internas no invalidan rutas fuera de ella
#### Información confiable
Información confiable vs no confiable
- No todos los avisos de ruta deben aceptarse. La confiabilidad depende de su origen, su trayectoria y sus propiedades.
-  Un aviso se considera confiable cuando:
	-  Proviene de una puerta de enlace autorizada 
	- Llega desde una WAN permitida 
	- Su camino no incluye puertas prohibidas 
	- Su secuencia es coherente con el estado actual 
	- Su longitud es razonable 
	- Cumple las políticas internas de la WAN (restricciones administrativas o de seguridad)
Estos avisos pueden entrar en la tabla de inundación.  Los avisos no confiables deben ser rechazados por las políticas de entrada o no exportados por las políticas de salida.  La distinción entre información confiable y no confiable es la base para aplicar políticas de entrada y salida, y para mantener la independencia entre dominios.
### Políticas de entrada
Función de decisión: la política de entrada se modela como una función abstracta. `cumple_política_entrada(aviso)`.
Determina si un aviso puede entrar a la tabla de inundación. 
#### ¿Qué evalúa una política de entrada?
A modo de ejemplo, la puerta de enlace puede rechazar un aviso si
- El camino pasa por una puerta de enlace prohibida (por razones administrativas o de seguridad.)
- La WAN de origen no está permitida (control de dominios de confianza).
- El destino pertenece a un conjunto bloqueado (filtrado de destinos no deseados)
- El camino es demasiado largo (evita procesar avisos obsoletos)
- La puerta de enlace emisora no está autorizada (control de quién puede anunciar rutas.
##### Las políticas de entrada permiten: 
- Controlar qué rutas se aceptan. 
- Proteger la WAN de rutas no deseadas. 
- Mantener la tabla de inundación limpia y coherente.
### Políticas de salida
Función de decisión: la política de salida se modela como una función abstracta.
* `cumple_política_salida(aviso, WAN_destino)`.
Determina si el aviso puede exportarse hacia una WAN vecina.
#### ¿Qué evalúa la política de salida?
A modo de ejemplo, la puerta de enlace puede decidir no exportar un aviso si:
- El destino pertenece a un conjunto bloqueado (filtrado de destinos no deseados hacia ciertas WANs)
- No anuncio rutas que no me conviene propagar (criterios administrativos, económicos o de seguridad).
- No anuncio rutas que no aprendí por canales confiables (control de confianza sobre el origen de la información)
##### Las políticas de salida permiten
- Controlar qué rutas se propagan hacia otras WAN. 
- Limitar la visibilidad de rutas internas. 
- Proteger la interred de información no confiable 
- Mantener independencia entre dominios.
### Selección de rutas a un destino
#### ¿Cómo decide un enrutador llegar a un destino R? 
Para elegir la mejor ruta hacia un destino R, el enrutador realiza:
1. Búsqueda en la tabla de inundación: Recupera todas las entradas asociadas al destino R (todas las secuencias que recibió).
2. Recuperación de los avisos: Obtiene los paquetes de aviso correspondientes, cada uno con su camino explicito (WAN-PATH).
3. Aplicación del criterio de selección: compara todas las rutas candidatas y elige la mejor.
#### Criterios de selección de rutas
1. Menor cantidad de WAN en el camino: prefiere rutas que atraviesan menos dominios. 
2. Si hay empate: menor costo de salida (se elige la ruta cuyo primer salto tiene el menor costo)
3. Si persiste el empate: función de preferencia local. () Criterio configurable por la WAN o la puerta de enlace (por ejemplo, preferir ciertos vecinos, políticas internas, etc.))
### Construcción de la tabla de reenvío
Construcción de tabla de reenvío (Enrutador E -> Destino R) 
1. Selección de la mejor ruta: El enrutador E elige la mejor ruta disponible hacia el destino R, usando los criterios de selección.
2. Si no existe ninguna ruta hacia R: se elimina la entrada correspondiente a R de la tabla de reenvío de E. 
3. Caso con una ruta válida: Si existe al menos una ruta hacia R:
	1. Identificar la puerta de enlace de salida P. Es la puerta de enlace por la cual P debe salir de su WAN para seguir el camino hacia R. 
	2. Usar la tabla de reenvío ya existente hacia P. La tabla de reenvío de E ya contiene línea de salida para llegar a P (resultado del algoritmo interno de enrutamiento).
	3. Reutilizar esa línea de salida. Esa misma línea de salida es la que E debe usar para llegar a R
#### Resumen conceptual
No se recalculan caminos internos cada vez. La tabla de reenvío hacia P ya representa el camino más corto. Para llegar a R, basta seguir el mismo primer salto.
### Recapitulación
#### Propiedades del protocolo 
- *Convergencia dentro de cada WAN*: si no hay cambios en la interred, todos los enrutadores de una misma WAN terminan con la misma información de rutas. 
- *Evita loops por inspección del camino:* Cada aviso incluye su camino explícito. Los enrutadores pueden detectar y descartar rutas que formen ciclos. 
- *Permite políticas locales:* cada WAN y cada puerta de enlace puede aplicar criterios propios para aceptar, rechazar o exportar avisos. 
- *Permite múltiples rutas candidatas:* Los enrutadores pueden almacenar varias rutas hacia un mismo destino, y elegir la mejor según criterios definidos. 
- *Permite remoción explícita:* Las rutas no expiran automáticamente. Solo se eliminan mediante avisos de remoción. 
- *No es un protocolo optimizado:* Este protocolo prioriza claridad conceptual sobre eficiencia. No optimiza selección de rutas, no optimiza propagación de avisos, no optimiza convergencia. Es un modelo didáctico para estudiar las ideas fundamentales.
## Hacia BGP desde Primeros Principios
### Introducción
#### El problema de fondo
* La interconexión de múltiples WAN no escala con inundación, porque:
	- Cada WAN tiene su propio PPEI.
	- Los protocolos intra-WAN no ven rutas externas. 
	- Si inundamos rutas externas dentro de cada WAN: 
		* Los enrutadores internos pasan a participar del protocolo exterior,
		* Las tablas internas crecen sin límite,
		* Cada cambio externo afecta a todos los enrutadores internos. 
	* Esto rompe la escalabilidad y mezcla planos que pueden estar separados. 
**Conclusión:** Necesitamos un protocolo PPEE donde solo las puertas de enlace participan, reduciendo el tamaño de las tablas internas y aislando los enrutadores internos de la dinámica externa.
#### EGP como advertencia histórica
 EGP es un diseño que no podía escalar. 
 EGP es un protocolo plano, sin jerarquía, sin política y sin agregación. Tenía:
 - Inundación de rutas externas dentro de cada WAN. 
 - Los enrutadores internos terminaban cargando todas las rutas del mundo. 
 - Sin prefijos, sin agregación: cada red era una entrada independiente. 
 - Sin política, sin control administrativo sobre qué anunciar o aceptar. 
 - Sin independencia entre WANs: un cambio externo afectaba a todos.
 Conclusión: EGP no escala: mezcla planos, sobrecarga los enrutadores internos y carece de los mecanismos necesarios para operar en una interred grande y autónoma
#### Lecciones del fracaso conceptual de EGP: Qué no debe hacer un protocolo de puerta de enlace exterior
- No debe inundar rutas externas dentro de la WAN. 
- No debe obligar a enrutadores internos a participar del enrutamiento exterior.
- No debe depender de la topología física.
- No debe anunciar miles de destinos individuales sin agregación. 
- No debe carecer de política para controlar qué se anuncia y qué se acepta.
- No debe carecer de independencia entre WANs (cada cambio externo no debe afectar a todos). 
**Conclusión**: Un PPEE moderno debe evitar todos estos errores: necesita prefijos, agregación, política, independencia y sesiones punto a punto.
### Requisitos arquitectónicos
Qué debe hacer un protocolo exterior para escalar 
- Mantener autonomía de cada WAN (SA).                        
- Anunciar prefijos, no destinos individuales. 
- Permitir agregación en las puertas de enlace. 
- Usar tablas de reenvío de los enrutadores internos en lugar de inundación. 
- Transportar avisos con fiabilidad (sesiones punto a punto). 
- Evitar loops en rutas anunciadas. 
- Implementar política explícita para controlar qué se acepta y qué se anuncia.
### Separación de PPEI/PPEE
Separación estricta entre PPEI y PPEE 
- PPEI (OSPF u otro): 
	- Calcula rutas internas, 
	- No ve rutas externas, 
	- Recibe prefijos agregados como LSAs externos. 
- PPEE (BGP): 
	- Corre solo en puertas de enlace, 
	- Aprende rutas externas, 
	- Aplica política, 
	- Agrega prefijos, 
	- Inyecta LSAs externos en el PPEI. 
Superposición de grafos, no un grafo global: El PPEI mantiene la topología interna, el PPEE intercambia rutas externas entre WANs.
### Suposiciones iniciales
- Asumimos que tenemos una red donde las WAN son usadas por proveedores de servicios de red. En internet estas WAN se llaman **sistemas autónomos (SA)**. 
- Asumimos que cada WAN corre OSPF. 
- El enrutamiento de PPEE se preocupa de establecer las rutas a usar (que pasan por diferentes WAN) para permitir que se comuniquen máquinas pertenecientes a distintas WAN. 
- Para enrutamiento PPEE encontrar un camino óptimo es imposible en la práctica porque en cada WAN OSPF usa criterios diferentes para determinar costos de enlaces.
### Agregación  en la puerta de enlace
La operación clave que permite escala.
La puerta de enlace aprende miles de rutas externas por medio de BGP,  aplica sus políticas y realiza agregación de prefijos de esas rutas para sintetizar varios destinos en uno más compacto. 
El resultado es un prefijo agregado que se inyecta dentro del dominio OSPF como un paquete de estado de enlace externo (que contiene el prefijo agregado y el next-hop hacia la puerta de enlace) que es difundido por OSPF por todas las áreas. De este modo, todos los enrutadores internos pueden reenviar tráfico hacia redes externas sin conocer la complejidad del enrutamiento inter-WAN. Solo ven:  “este prefijo externo está detrás de esta puerta de enlace”.
La puerta de enlace actúa así como un punto de condensación que protege la red interna del caos externo, manteniendo las tablas internas pequeñas, estables y completamente independientes de la dinámica del protocolo exterior.
### Información de rutas
Como no se puede manejar información de caminos óptimos, ¿qué tipo información sobre rutas manejar? 
* Cuando un enrutador avisa una LAN, incluye con la dirección de la LAN, una ruta que pasa por varias WAN para llegar a la LAN. 
* Una ruta se compone de los siguientes elementos: 
	- NEXT-HOP: la dirección de la interfaz de la puerta de enlace que comienza la ruta hacia la LAN de destino.
	-  WAN PATH: contiene las WAN por las cuales el aviso de la LAN de destino ha pasado. 
	- Dirección de LAN de destino: por ejemplo, un rango de direcciones de interfaz o un prefijo.
![](Screenshot_2026-05-28-11-21-38_27143.png)
### Relaciones entre las WAN
Si hay una **relación de proveedor-consumidor** en las WAN, los tipos de ruta que publica la WAN proveedor son las rutas a todos los destinos en la interred al consumidor sobre el enlace que los conecta. Así el consumidor va a tener rutas para enviar paquetes para todos lados.
Y el tipo de rutas que publica la WAN consumidora, son rutas a los destinos en su red al proveedor. Esto permite al proveedor enviar tráfico al consumidor solo para esas direcciones.
Si hay una relación de compañerismo, los SA (Sistemas Autónomos) compañeros mandan publicidad de enrutamiento de uno al otro para los destinos que residen en sus redes.
### Función de las puertas de enlace
Para el enrutamiento es necesario encontrar algún camino de WANs para la LAN de destino deseado que es libre de ciclos. Además, los caminos deben respetar las políticas de las WAN a lo largo del camino. 
#### Tareas que hace una puerta de enlace: 
- Tiene que hacer una elección de varias rutas a una LAN de destino; 
- va a elegir la mejor de acuerdo con sus propias políticas locales y esta va a ser la ruta que avisa. 
- Una puerta de enlace avisa el camino exacto que está usando para cada destino.
### Sesiones BGP
Una vez que la puerta de enlace agrega prefijos y los inyecta como LSAs (Link State Advertisement) externos en OSPF, la red interna queda completamente aislada de la complejidad del enrutamiento entre sistemas autónomos., pero este aislamiento solo es posible si el intercambio de rutas externas se realiza fuera del dominio OSPF, mediante un mecanismo que no dependa de inundación ni de topología interna. 
Aquí es donde aparece la necesidad de **sesiones BGP:** conexiones lógicas punto a punto entre puertas de enlace que permiten transportar rutas externas de manera confiable, controlada y sujeta a políticas. Estas sesiones reemplazan la inundación del modelo anterior y se convierten en el **canal exclusivo por el cual los sistemas autónomos negocian, filtran y actualizan la información** de prefijos que luego será agregada e inyectada hacia adentro.
En otras palabras, para que la red interna permanezca simple, estable y protegida, el intercambio exterior debe pasar a un plano separado, sostenido por sesiones BGP.
#### ¿Cómo hacer para evitar inundación?
La clave: sesiones explícitas entre puertas de enlace.  
Las rutas externas se intercambian a través de una sesión, es decir, una conexión lógica punto a punto entre dos puertas de enlace. Cada sesión se transporta sobre TCP, que brinda confiabilidad y orden, y TCP a su vez, se enruta usando la tabla de reenvío interna construida por OSPF.  
Al apoyarse en el mecanismo de reenvío común, las rutas externas no se inundan dentro de la WAN:
- No hay duplicados de avisos ni propagación indiscriminada.
- La sesión es lógica, no física: no depende de la topología interna de la WAN.
- Cada sesión es explícita, controlada y establecida solo entre puertas de enlace, lo que permite aplicar políticas finas y mantener la independencia entre dominios.
#### Problema: ¿Cómo hacer para propagar información de rutas en BGP de manera confiable?  
En BGP pares de puertas de enlace intercambian información de rutas sobre conexiones TCP semipermanentes usando el puerto 179.  Hay típicamente una conexión BGP TCP para:
- Cada enlace que conecta directamente dos puertas de enlace (o enrutadores BGP) en dos SA diferentes y
- Entre puertas de enlace dentro del SA 
Para cada conexión TCP, los 2 enrutadores al final de la conexión se llaman compañeros BGP. Los compañeros BGP se avisan rutas.
#### Sesiones BGP: 
- La conexión TCP con todos los mensajes BGP enviados por la conexión se llama sesión BGP.
- Una sesión BGP entre puertas de enlace de dos SA se llama sesión externa BGP (eBGP) 
- Una sesión BGP entre puertas de enlace en el mismo SA se llama sesión interna BGP (iBGP) 
- **Las líneas de las sesiones BGP no siempre se corresponden con los enlaces físicos.**
![](Screenshot_2026-05-28-11-27-49_25560.png)
Una sesión BGP es una relación lógica entre dos puertas de enlace, no una relación física entre enrutadores adyacentes.
#### ¿Cómo viaja un aviso BGP?
- Una puerta de enlace genera un mensaje de aviso.
- Este mensaje se encapsula en BGP.
- El mensaje TCP se encapsula en IP.
- El paquete IP se reenvía usando la tabla de reenvío creada por OSPF.
- Los enrutadores internos tratan el paquete IP como tráfico normal, no como control.
#### ¿Cómo se garantiza que un aviso de ruta llegue?
 Antes de establecer una sesión BGP, cada puerta de enlace debe verificar que la otra es alcanzable por IP usando el OSPF. Si OSPF puede llevar paquetes IP hasta la otra puerta de enlace, entonces:
 - Establece sesión BGP con la otra pueta de enlace. 
 - La conexión TCP queda abierta durante horas o días. 
 - Los avisos BGP fluyen por esa sesión. 
 - Si la sesión se cae, BGP la vuelve a intentar. 
 Todas las puertas de enlace corren TCP.
>[!tip] Una sesión BGP no se crea para cada aviso. Se crea una vez, se mantiene abierta y todos los avisos viajan por ella.
### AS-PATH
(Path de Sistemas Autónomos)
Evita loops sin depender de la topología .
La clave: trayectoria explícita, no conocimiento global
- Cada anuncio lleva la secuencia de SAs por la que pasó (AS-PATH). 
- Si un SA aparece dos veces se detecta un loop inmediatamente. 
- No se necesita topología global ni mapas de la interred. 
- Permite política basada en trayectoria (preferencias, restricciones, filtrado por origen o camino)
### Mensajes BGP
Una vez establecidas las sesiones BGP entre puertas de enlace, el siguiente paso es entender qué viaja realmente por esas sesiones. 
La sesión define el canal: confiable, punto a punto, sostenido por TCP y aislado de la topología interna, pero el funcionamiento del protocolo depende de los mensajes que se intercambian a través de ese canal, porque son ellos los que permiten anunciar prefijos, retirar rutas, mantener viva la sesión y negociar capacidades. En otras palabras, si la sesión es el “túnel” que conecta dos sistemas autónomos, los mensajes BGP son el contenido que circula dentro de ese túnel, y constituyen el mecanismo concreto mediante el cual se propagan rutas externas y se aplican políticas.
Los mensajes de BGP son cuatro y cada uno cumple una función específica dentro del establecimiento y mantenimiento de la sesión BGP y el intercambio de información de enrutamiento. 
- **OPEN:** inicia la sesión BGP tras establecer TCP.
- **UPDATE:** anuncia o retira rutas. 
- **KEEPALIVE:** mantiene viva la sesión. 
- **NOTIFICATION:** informa errores y cierra la sesión.
#### Mensaje OPEN
 Se envía después de establecer la conexión TCP.  Negocia parámetros esenciales: 
 - versión de BGP, 
 - SA local, 
 - Hold Time, 
 - Router ID, 
 - Capabilities (IPv6, multipath, etc.). 
 Si ambos extremos aceptan los parámetros → la sesión continúa. Normalmente se envía un KEEPALIVE inmediatamente después
#### Mensaje UPDATE 
Anuncia nuevas rutas o retira rutas inválidas. 
 Contiene atributos BGP (AS-PATH, NEXT_HOP, LOCAL_PREF, etc.) y se envía cada vez que cambia la mejor ruta conocida. 
Estructura exacta del UPDATE:
1. Withdrawn Routes Length 
2. Withdrawn Routes (prefijos retirados) 
3. Total Path Attribute Length
4. Path Attributes
5. NLRI (prefijos anunciados)
#### Mensaje KEEPALIVE
Se envía periódicamente si no hay UPDATEs.  Evita que la sesión se cierre por inactividad y el intervalo depende del Hold Time negociado en el OPEN., si el Hold Time = 0 → no se envían KEEPALIVE
#### Mensaje NOTIFICATION
Se envía cuando ocurre un error que requiere terminar la sesión. Indica la causa del error (mensaje mal formado, parámetros inválidos, timeout, etc.).  Tras enviarlo, el enrutador cierra la conexión TCP inmediatamente. **No se usa para advertencias menores: solo para errores críticos**.
#### Flujo típico de una sesión BGP: 
1. Se establece la conexión TCP entre puertas de enlace. 
2. Se intercambian mensajes OPEN. 
3. Se envía un KEEPALIVE inicial para confirmar la sesión. 
4. Se intercambian UPDATEs para anunciar/retirar rutas. 
5. Se envían KEEPALIVE periódicos si no hay UPDATEs. 
6. Si ocurre un error → NOTIFICATION → cierre de sesión
### Estructuras internas de BGP
Una vez que entendemos cómo se establecen las sesiones BGP y qué mensajes circulan por ellas, el siguiente paso natural es preguntarnos dónde y cómo se almacena toda esa información dentro de una puerta de enlace.
Los mensajes son el mecanismo de intercambio, pero no son el estado del protocolo: cada puerta de enlace debe mantener registros persistentes de todas las rutas aprendidas, de las rutas seleccionadas y de las rutas que decide anunciar. Para eso, BGP organiza su información en un conjunto de estructuras internas que separan claramente lo que se recibe, lo que se decide usar y lo que se anuncia. 
Estas estructuras permiten aplicar políticas, comparar rutas, elegir la mejor y mantener coherencia sin depender de la topología interna. Pasamos ahora a estudiar estas estructuras, que constituyen el “sistema nervioso” del funcionamiento interno de BGP.
#### ¿Qué necesita almacenar una puerta de enlace BGP?
Necesitamos guardar todo lo recibido, incluso lo que no vamos a usar. Después procesaremos esas rutas filtrando por política y eligiendo la mejor ruta por destino. También debemos preparar rutas para anunciar hacia afuera.  
**Conclusión**: necesitamos una estructura para almacenar todas las rutas recibidas.
#### Estructura 1: Adj-RIB-In 
Guarda todas las rutas recibidas de cada vecino BGP. 
Hay una Adj-RIB-In por vecino y dentro de cada una se organizan las rutas por prefijo, junto con sus atributos BGP. 
Necesitamos Adj-RIB-In porque no podemos decidir nada sin ver todas las rutas recibidas. 
* Se actualiza con cada mensaje UPDATE recibido. 
- Si llega una ruta por primera vez para un prefijo de un vecino: se la agrega. 
- Si llega una ruta actualizada para un prefijo de un vecino (p.ej. cambió el AS_PATH o el NEXT_HOP): la ruta actualizada reemplaza la anterior para ese vecino. 
- Si llega un retiro, se elimina la ruta correspondiente de la tabla.
Una vez que se filtran las rutas de Adj-RIB-In por política y se elige la mejor ruta por destino, hace falta una estructura extra para guardar esta información, pues se tiene en cuenta para: 
- decidir si se anuncian hacia otros SA, 
- Inyectarlas en OSPF y, 
- para actualizar la tabla de reenvío. 
Usamos entonces Loc-RIB
#### Estructura 2: Loc-RIB (la tabla de mejores rutas) 
Guarda una única mejor ruta por destino.  Es el resultado del proceso de selección. 
Dedujimos: Adj-RIB-In → selección → Loc-RIB
Con estas estructuras ya podemos: aplicar política, elegir rutas, anunciar rutas, inyectar rutas en OSPF. Esto es suficiente para un protocolo derivado desde primeros principios. • Pero BGP termina agregando dos estructuras más por razones operativas, no conceptuales.
#### Estructura 3: Adj-RIB-Out 
Guarda las rutas preparadas para enviar a cada vecino BGP. 
Permite aplicar políticas de exportación. Se construye a partir de la Loc-RIB aplicando políticas de salida. Puede haber diferencias en las rutas anunciadas a distintos vecinos, incluso para el mismo prefijo. 
#### Estructura 4:  RIB (RoutingInformation Base) 
Solo las rutas seleccionadas en la Loc-RIB que son mejores que las rutas existentes en la RIB se instalan en la RIB para ser usadas en el reenvío de paquetes. Las rutas de Loc-RIB se comparan con otras rutas en la RIB para decidir cuál ruta se instala para el encaminamiento.
>[!tip] La RIB no es parte de BGP: Es parte del sistema operativo del enrutador
### Política en BGP
Una vez que entendemos cómo BGP organiza internamente la información —separando lo que recibe, lo que selecciona y lo que anuncia mediante sus distintas RIBs — aparece de inmediato la pregunta clave: ¿cómo decide una puerta de enlace qué rutas usar y cuáles propagar? 
Las estructuras internas no son solo un mecanismo de almacenamiento; son el espacio donde se aplican las reglas administrativas que cada sistema autónomo impone sobre el tráfico que acepta y el tráfico que anuncia. En otras palabras, las RIBs son el soporte técnico que permite implementar la política de enrutamiento, que es el verdadero corazón del funcionamiento de BGP. 
#### El PPEE como mecanismo administrativo, no solo técnico 
La política es el corazón del diseño exterior:
- Qué anunciar a otros SA. 
- Qué aceptar de los vecinos. 
- Qué preferir entre múltiples rutas externas. 
- Qué evitar según restricciones administrativas. 
- Cómo influir en el tráfico entrante y saliente. 
**La idea central**: La política no es un accesorio, es la función principal del PPEE y la razón por la que existe
En conjunto, estas políticas permiten controlar el flujo de información de enrutamiento, optimizar rutas, evitar bucles, cumplir acuerdos comerciales y mantener la estabilidad y seguridad de la red.  
#### Políticas de entrada (import policies)
Se aplican a rutas recibidas antes de entrar a la Loc-RIB: 
- Filtrar rutas no deseadas. 
- Modificar atributos (ej. LOCAL_PREF) para influir en la selección. 
- Clasificar rutas para reglas internas (El uso de comunidades para esto queda en material opcional.)
#### Políticas de salida (export policies)
Se aplican a rutas anunciadas a los vecinos: 
- Filtrar rutas que no deben enviarse. 
- Modificar atributos como AS_PATH al anunciar rutas externas. 
- Controlar anuncios según acuerdos o topología. (El uso de comunidades para esto también queda en material opcional).
### Atributos adicionales para ruta
Una vez entendidas las políticas en BGP —qué rutas se aceptan, cuáles se descartan y cuáles se anuncian— surge naturalmente la siguiente pregunta:
#### ¿cómo decide una puerta de enlace entre varias rutas aceptables hacia un mismo prefijo?**
Las políticas permiten filtrar y moldear el conjunto de rutas candidatas, pero no determinan por sí mismas cuál es la mejor. Para eso, BGP necesita información adicional: un conjunto de atributos de ruta que describen propiedades del camino, preferencias administrativas y características operativas.  Estos atributos —como LOCAL_PREF, AS-PATH, MED, NEXT_HOP, entre otros— son los insumos que alimentan el proceso de selección.
Vemos dos atributos adicionales, los cuales introducimos con problemas:
##### LOCAL_PREF
Problema: muchas veces un SA tiene varias puertas de enlace alternativas para salir hacia un destino externo. Pero no todas son equivalentes:
- algunas salidas son mas convenientes por costos, 
- otras tienen mayor capacidad (tasa de datos), 
- otras se prefieren por relaciones comerciales (compañerismo, cliente-proveedor), 
- o se desea privilegiar ciertos acuerdos con otros SA. 
Y el atributo AS-PATH no puede expresar política interna. Solo describe la trayectoria, no las preferencias del SA. 
BGP usa el atributo LOCAL-PREF para asignar preferencias internas. 
LOCAL-PREF se usa dentro del SA y es un  atributo para las rutas: cuanto mayor valor, más preferida la ruta.  Permite expresar política interna de forma simple y determinista. 
¿Cómo se aplica? Basta con asignar distintos valores de LOCAL_PREF a las rutas según la puerta de enlace de salida. 
##### MED
##### Problema: Un SA puede tener múltiples puertas de enlace por las que un SA conectado a él puede entrar. Pero no todas las entradas son iguales
- algunas tienen mayor capacidad, 
- otras están menos congestionadas, 
- algunas son mas baratas o preferidas por acuerdos, 
- otras se quiere desalentar para balancear la carga.
El atributo AS-PATH tampoco puede expresar preferencias entrantes. Solo describe la trayectoria, no cuál entrada conviene usar. 
La necesidad: Si vas a entrar a mi SA, preferí esta puerta de enlace. Pero sin imponerlo, porque BGP es un protocolo entre administraciones independientes. 
BGP usa el atributo MED (Multi Exit Discriminator) 
Cuanto menor el valor, más preferida la entrada. Se envía hacia afuera, al vecino, y permite expresar preferencias entrantes sin modificar el AS-PATH. 
Es una sugerencia, que el vecino puede respetar o no. 
¿Cómo se aplica? Asignando distintos valores de MED a las rutas según la puerta de enlace por la que se desea que entren. 
>[!tip] MED complementa LOCAL-PREF: Uno decide por donde salgo y otro sugiere por donde entrar
### Elección de ruta a un prefijo
**Situación**: Un enrutador puede recibir múltiples rutas al mismo prefijo. La mejor ruta a un prefijo debe guardarse en la Loc-RIB. 
**Problema**: ¿Cómo escoge el enrutador una de esas rutas al mismo prefijo? 
Para elegir una sola, hay que aplicar un algoritmo determinista sobre las rutas en la Adj-RIB-In
#### Solución (estándar RFC 4271): 
1. **Verificar NEXT_HOP alcanzable:** el NEXT_HOP debe poder resolverse en la tabla de enrutamiento local. Si no es alcanzable, la ruta se descarta.
2. **LOCAL_PREF**: Las rutas con el mayor valor LOCAL-PREF son elegidas. Este valor puede ser fijado por el enrutador o aprendido dentro del mismo SA. Es un atributo propagado internamente que expresa política del SA. 
3. **Longitud del AS_PATH:** Entre las rutas restantes, la ruta con el AS PATH más corto es elegida (cantidad de saltos SA). 
4. **MED:** Se prefiere la ruta con el MED más bajo. Por defecto, se compara solo entre rutas provenientes del mismo SA vecino 
5. **Preferir rutas eBGP sobre iBGP:** Si aun hay empate, se prefiere la ruta aprendida por eBGP antes que una aprendida por iBGP. 
6. **Costo IGP al NEXT_HOP (hot-potato routing):** Se elige la ruta cuyo NEXT-HOP está más cerca según el PPEI. El SA se saca el tráfico de encima lo antes posible.
7. **Ruta más antigua:** para evitar oscilaciones, se prefiere la ruta aprendida primero. 
8. **Router ID del vecino:** Si persiste el empate, se elige la ruta al vecino con el Router ID más bajo. 
9. **Dirección IP del vecino:** Último desempate: se elige la ruta del vecino con la IP más baja
##### Algoritmo fijo, política flexible 
Tenemos entonces un único algoritmo estándar: Todos las puertas de enlace usan la misma secuencia para elegir la mejor ruta. Este orden está definido en una RFC y no se modifica
La razón es asegurar la interoperabilidad global entre miles de SA independientes, pero cada SA controla las entradas al algoritmo: Antes de que el algoritmo compare rutas, el SA puede aplicar políticas propias: Cambiar LOCAL-PREF, ajustar MED, filtrar rutas, reescribir AS-PATH, agregar comunidades (material opcional), modificar atributos según acuerdos comerciales.
**Resultado**: cada SA toma decisiones diferentes, sin romper el estándar.
![](Screenshot_2026-05-28-11-41-01_1133.png)
### Creación de entrada en la tabla de reenvío
Una vez que BGP completa el proceso de selección y determina la mejor ruta hacia un prefijo, el protocolo ya no tiene más decisiones que tomar sobre ese destino. A partir de ese momento, el problema deja de ser “¿qué ruta prefiero?” y pasa a ser “¿cómo hago para que los paquetes realmente sigan ese camino?”. Aquí es donde BGP entrega el control al plano de reenvío: la ruta seleccionada se traduce en un siguiente salto concreto, que debe instalarse en la tabla de reenvío interna del enrutador.
Esta instalación no copia toda la información de BGP, sino solo lo necesario para reenviar paquetes: el prefijo y la interfaz o puerta de salida correspondiente. Con este paso, la decisión de BGP se convierte en acción operativa, y el tráfico real comienza a fluir siguiendo la ruta elegida
#### Problema: ¿cómo determina un enrutador por qué puerto reenviar paquetes hacia un prefijo externo (perteneciente a otro SA)?
BGP sólo indica qué ruta es la mejor, pero no especifica cómo llegar físicamente al NEXT-HOP. Solución: La RIB contiene la ruta óptima R al prefijo x. Para convertir esta ruta en una entrada de reenvío, el enrutador debe: 
1. Resolver el NEXT-HOP de R usando OSPF. 
2. Usar OSPF para encontrar la mejor ruta intra-SA hacia ese NEXT_HOP.
3. identificar el puerto de salida asociado a esa ruta interna.
4. Instalar la entrada en la tabla de reenvío asociando: prefijo x -> puerto de salida correspondiente al NEXT HOP. 
**Sincronización continua:**  La tabla de reenvío se actualiza automáticamente cuando cambian: la topología interna (OSPF) , las rutas BGP, o el NEXT-HOP.  De esta forma el plano de reenvío siempre refleja el estado actual del SA.
### Recapitulación
Uso de estructuras internas: 
- Los avisos recibidos se guardan en RIB de entrada (Adj-RIB-In). 
- Sobre esta base se aplican las políticas de entrada y luego se eligen las mejores rutas. 
- Las rutas seleccionadas se guardan en la RIB de decisión (Loc-RIB). 
- Desde las Loc-RIB se aplican las políticas de salida y el resultado se guarda en RIB de salida (Adj-RIB-Out), desde donde se extraen los avisos que se envían por las sesiones BGP.
![452](Screenshot_2026-05-28-11-42-54_20405.png)
 Ideas clave del bloque 
 - La separación PPEI/PPEE es esencial para escalar. 
 - Los enrutadores internos no deben ver rutas externas. 
 - Las puertas de enlace agregan prefijos antes de inyectarlos en OSPF. 
 - Las sesiones TCP permiten control fino, confiabilidad y no-inundación. 
 - El AS-PATH evita loops sin necesidad de topología global. 
 - Las políticas determinan qué se anuncia y qué se acepta
## Idea unificadora de los protocolos de interred
1. Anunciar destinos: Toda interred comienza cuando una red informa qué destinos existen detrás de ella.
2. Propagar rutas: Esas rutas deben viajar entre dominios autónomos, con mecanismos que eviten ciclos y mantengan coherencia.
3. Filtrar con políticas: Cada red decide qué acepta y qué anuncia, preservando su autonomía administrativa. 
4. Comparar rutas: Los avisos pueden llegar por múltiples caminos; cada puerta de enlace debe evaluarlos según criterios locales. 
5. Elegir la mejor ruta: La selección es siempre local; cada dominio decide qué camino prefiere hacia cada destino. 
6. Instalar el siguiente salto: La ruta elegida se traduce en un next-hop concreto, reutilizando el enrutamiento interno.
7. Mantener coherencia sin conocer toda la red: El desafío central: enrutar globalmente con información parcial, sin topología completa, sin métrica universal y sin control centralizado.