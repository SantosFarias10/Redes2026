# Bloque 3
## Conectar LANs
### Enrutadores
Supongamos que tenemos varias LAN y ponemos un nodo intermedio para interconectarlas (**Enrutador**).

![](ConectarLANs.png)
##### ¿Cómo sabe el enrutador porque línea de salida mandar un mensaje a una de las maquinas de A a la F?
Definiendo una **Tabla de Reenvió**
![](TablaDeReenvio.png)

##### ¿Como puede manejar el enrutador que recibe varios mensajes que se van acumulando para ser enviados por una línea de salida?
Usando una **Cola de Mensajes** en la línea de salida.

### Almacenamiento y Reenvío
##### ¿Como hace el enrutador para mandar un mensaje de A a F?
1. El enrutador consulta la tabla de reenvio y descubre que hay que usar la línea l2.
2. Encola el mensaje en la cola de la línea l2.
3. Cuando el mensaje llega a la cabeza de la cola el enrutador lo reenvía.
A esto se lo llama **Almacenamiento y Reenvió**

##### ¿Cuánto demora el Almacenamiento y Reenvío?
![](AlmacenamientoYReenvio.png)
$$
d_{nodal} = d_{proc} + d_{queue} + d_{trans} + d_{prop}
$$
* $d_{nodal}:$ Procesamiento del Nodo.
* $d_{queue}:$ Demora por Encolado.
* $d_{trans}:$ Demora en Transmitir los Paquetes.

---
## Redes de Área Amplia (WAN)
Si se intenta conectar cientos o miles de LAN a un solo enrutador, simplemente colapsa. Ahí es donde necesitamos pasar a una estructura mas grande, conocida como ***Wide Area Network***.

![](WAN.png)
* Los círculos son enrutadores y los rectángulos computadoras.
##### ¿Qué opciones hay para mandar un Mensaje de H1 a H2?
Una posibilidad es A, C,  E, F. Otra puede ser A, B, D, E, F. Por lo tanto, hay que elegir una ruta para el envió de Mensajes. Supongamos que tenemos las tablas de reenvió, que determinan la ruta que se usa:
![](TablasDeReenvioWAN.png)
Si la tabla de A cambia, como se observa en la imagen, y queremos mandar un mensaje de H1 a H2, lo que va a suceder es que la ruta a usar va a cambiar porque va a ir por B, por lo que para cambiar las rutas a usar se modifica la tabla de reenvío.

---
## Algoritmo de Enrutamiento
Al pasar de unas pocas LAN interconectadas a WAN, ya no tenemos unos pocos enrutadores tomando decisiones, sino decenas o cientos de ellos, cada uno con su propia tabla de reenvió y su propio trafico. Por lo que elegir por donde enviar un paquete deja de ser una tarea trivial: Las rutas posibles se multiplican, las condiciones de la red cambian constantemente y ningún enrutador puede tener una visión completa y perfecta del estado global.

#### ¿Seguir cualquier ruta al destino da lo mismo? 
NO. Las rutas difieren en la cantidad de enrutadores por los que hay que pasar, el trafico, las tasas de datos soportadas por los enlaces, etc. Por lo que no todas las rutas son iguales.

#### ¿Como hacer para poder tener acceso a las mejores rutas?
Modificando las tablas de reenvío con ese propósito.
Los algoritmos que modifican estas tablas se llaman **Algoritmos de Enrutamiento**.

---
### Ejemplos de WAN
* Sistema de Fibra a la Casa:
![](SistemaDeFibraALaCasa.png)
* Sistema Telefónico:
	* Cada domiciolio esta conectado por un cable de cobre a una ***End Office***.
	* Toda oficina central esta conectada a una **Toll Office**, estas son usadas para el reenvio de mensajes y estan unidas pro cables de fibra optica.
![](SistemaTelefonico.png)
