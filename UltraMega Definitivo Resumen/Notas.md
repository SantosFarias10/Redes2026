# Control de Flujo
**Busca evitar que un emisor rápido desborde a un receptor lento**, ajustando su ritmo de envío a la capacidad de procesamiento de datos del receptor.
#### ¿En qué capa?
La **Capa de Enlace de Datos** controla el flujo entre dos máquinas vecinas conectadas directamente, como `host <--> router` o `router <--> router`.
En cambio la **capa de transporte** controla el flujo extremo a extremo entre los procesos en los hosts.
#### El receptor necesita búferes ya que
* **El emisor puede ir mas rápido** 
	* Entonces los segmentos llegan más rápido de lo que el receptor puede procesarlos, por lo que hay que guardarlos temporalmente.
* **Procesar en lotes es más eficiente**
	* O sea si acumulamos varios segmentos antes de pasarlos a la capa de aplicación reducimos la sobrecarga de procesamiento.
* **Los segmentos llegan desordenados**
	* Por lo que hay que almacenar los segmentos desordenados hasta completar el orden correcto.
### Problemas a los que se enfrenta
1. **Aplicaciones lentas**
	* Las aplicaciones receptoras no leen los datos al instante
	* La consecuencia es que el emisor satura los búferes del receptor y se pierden datos.
2. **La red causa retrasos**
	* Este problema viene de la capa de red.
	* La capa de red deja al receptor con menos capacidad en los búferes.
	* Se puede dar por
		* **Paquetes dañados**: Si un paquete se corrompe, los paquetes buenos que llegan después no pueden entregarse hasta que se retransmita el paquete dañado.
		* **Cambios de Rutas**: El algoritmo de enrutamiento cambia rutas lentas por rutas más rápidas, lo que causa que algunos paquetes lleguen fuera de orden. Estos paquetes ocupan lugar en el búfer hasta recomponer la secuencia y entregarlos a la aplicación.
3. **Muchas conexiones a la vez**
	* Este problema surge desde la propia capa de transporte.
	* La capa de transporte comparte la memoria entre todas las conexiones abiertas del host, por lo que si tenemos muchas conexiones el búfer del receptor se reduce y aumenta el riesgo de desbordamiento.
### Si en la capa de enlace ya hay un control de flujo. ¿Porqué lo necesitamos también en la capa de transporte?
#### Capa de enlace
Porque en la capa de enlace de datos los mecanismo de control de flujo operan únicamente a nivel de **Salto a Salto**
* O sea, entre dos nodos que están físicamente conectados
La capa de enlace no sabe el estado general del sistema, no tiene forma de saber si la aplicación lee lenta, si hay muchas conexiones abiertas, si hubo reordenamiento de paquetes, etc.
#### Capa de transporte
En cambio el control de flujo en la capa de transporte **tiene una visión de extremo a extremo**.
Es la única que puede reaccionar a los problemas que vimos (1, 2 y 3).
Por lo tanto, los mecanismos confiables de la capa de enlace (como los ACKs y retransmisiones) no son suficientes para evitar que los búferes del receptor se desborden.
### Si el receptor tiene varias conexiones abiertas ¿Cómo reparte los búferes entre ellas?
#### Tenemos dos enfoques
1. **Pool compartido**
	* Todas las conexiones comparten el mismo conjunto de búferes
	* La ventaja es que se **aprovecha mejor la memoria** si el tráfico es desigual.
	* La consecuencia es que una conexión "ruidosa" (o sea con mucho trafico) puede ocupar el espacio y afectar a las demás conexiones.
* **Búferes dedicados**
	* A cada conexión se le asigna un conjunto de búferes específicos y reservados.
	* Esto lo que hace es que aísla cada conexión de las demás, protegiéndolas de picos de tráficos ajenos.
	* La desventaja es que puede desperdiciar memoria si algunas conexiones están inactivas o no utilizan todo el espacio que se les asigno.
### Hacia un protocolo dinámico
#### Sabemos que
* Los búferes del receptor se pueden llenar por distintas causas
- El receptor y el emisor deben ajustar dinámicamente sus asignaciones de memoria,
	- Esto significa ventanas de tamaño variable.
#### Problema
El emisor y el receptor saben cosas distintas:
* El emisor sabe cuántos datos le gustaría enviar, pero no cuántos puede enviar realmente.
* El receptor sabe cuánto espacio libre tiene, pero no sabe cuánto espacio el emisor querrá para enviar.
#### Posible solución
El emisor le solicita espacio de búfer al receptor y el receptor le otorga explícitamente cuánto puede usar.
Esta interacción de solicitud se llama **handshake** (supuestamente).
* No hace falta enviar ACKs ya que el receptor puede incorporar la reserva de búferes y el ACK en un mismo segmento.
* El emisor lleva un contador (otorgado por el receptor), si la asignación disponible llega a 0 => el emisor debe detenerse por completo hasta que el receptor le otorgue más búferes.
## Control de Flujo TCP
TCP no obliga a implementaciones estrictas. En particular, **no se requiere** que:
1. **El emisor envíe al instante**
	* Por lo que no hace falta mandar datos en cuanto llegan de la aplicación.
2. **El receptor confirme al instante**
	* Los ACK pueden agruparse y retrasarse ligeramente.
3. **El receptor entregue al instante**
	* Los datos pueden quedarse en el búfer hasta que convenga entregarlos.
#### Y esto ¿Porqué importa?
Esta flexibilidad nos sirve para **mejorar el rendimiento**: 
* Agrupar segmentos pequeños,
* retrasar ACKs para aprovechar piggybacking,
* sincronizar lecturas con el ciclo de la CPU,
* etc.
### En TCP
* El **Números de secuencia = posición de byte**
	* En los protocolos genéricos, los números de secuencia identifican paquetes individuales. Por lo tanto, las máquinas negocian en **términos de paquetes** (El emisor pide espacio para `N` paquetes y el receptor le dice "Te reservo `X` búferes")
	* Pero, TCP ve los datos como un flujo continuo de bytes. Por lo que, el número de secuencia en TCP identifica la posición de un byte específico.
	* Por lo tanto, el receptor cuando recibe una petición de búferes dice "Tengo estos rangos de bytes en búfer".
#### Mejoras
Dado que TCP identifica bytes del flujo (no paquetes), se pueden hacer dos mejoras:
1. **No se guardan las cabeceras:**
	* En los esquemas anteriores, cuando un paquete llegaba al receptor, este se guardaba en la memoria, incluyendo sus cabeceras las cuales ocupan espacio innecesario.
	* La mejora en TCP consiste en que el receptor extrae únicamente el *payload* (los datos útiles de la aplicación) y descarta las cabeceras antes de guardar la información en su búfer
2. **El emisor ya no pide búferes:**
	* No necesita enviar un mensaje "pido `N` búferes" como en el protocolo anterior.
	* **En TCP**: El emisor nunca pide permiso de espacio. En su lugar, el receptor toma la iniciativa y anuncia constantemente cuánto espacio libre tiene en cada segmento o confirmación (ACK) que envía de regreso.
### Búfer circular del receptor
Cada conexión TCP tiene en el receptor un búfer circular de tamaña `RcvBuffer`.
A medida que los datos van llegando desde la red, se almacenan temporalmente en este búfer esperando a ser procesados. Cuando la app lee los datos, lo que hace exactamente es "retirar" esos bytes del inicio del búfer y libera espacio para datos nuevos al final.
La ventaja es que no importa donde esté cada byte, lo relevante es cuánto espacio libre queda. Ese espacio libre es lo que el receptor anunciará al emisor.
#### ¿Cómo le dice el receptor al emisor cuánto espacio libre tiene?
Usando la **ventana de recepción**. En cada segmento que envía (incluido el ACK), incluye un campo llamado `rwnd` (*receive window*) con el número de bytes consecutivos que puede aceptar a partir de ahora.
>[!tip] El emisor NUNCA puede tener en vuelo (enviados y sin confirmar) más de `rwnd` de bytes.
#### Por lo que
El buffer tiene 3 regiones
1. Datos ya entregados a la app.
2. Datos recibidos, esperando entrega.
3. Espacio libre (`rwnd`).
### Buffer circular del emisor
El emisor también tiene un buffer circular propio donde almacena los datos que envía. Los mantiene ahí hasta recibir el ACK, por si tiene que retransmitir.
#### ¿Cuánto puede enviar realmente?
```
bytes enviables = min (tamaño del búfer del emisor, rwnd).
```
El emisor nunca puede enviar más bytes que el menor de estos limites:
* Como límite propio tiene a su **búfer**:
	* Ya que no tiene sentido enviar más de lo que cabe en su propia memoria esperando confirmación.
* El límite del receptor es el `rwnd`:
	* Si se envía más, el receptor no puede aceptarlo y los bytes extra se descartarán.
### ¿Cómo se calcula `rwnd`?
```
rwnd = RcvBuffer - (LastByteRcvd - LastByteRead)

o sea

rwnd = tamaño del buffer - lo que está esperando a ser leído
```
Donde:
* `RcvBuffer` es el tamaño total del búfer asignado.
* `LastByteRcvd` es el último byte llegado al receptor.
* `LastByteRead` es el último byte leído por la aplicación.
### Reglas del receptor en TCP
1. Cuando llegan bytes en orden y en secuencia, los coloca en el búfer de recepción.
2. Al confirmar (ACK) la llegada de datos, anuncia al emisor el nuevo tamaño de ventana      (`rwnd`).
3. Si el búfer está lleno, anuncia `rwnd = 0` y el emisor debe pausar los envíos.
4. Cuando la aplicación lee `X` bytes del búffer, libera ese espacio y puede anunciar      `rwnd = X`.
>[!tip] El receptor avisa continuamente al emisor cuánto puede aceptar.
### Reglas del emisor en TCP
1. Si el receptor anuncia `rwnd = 0` el emisor no puede enviar datos.
2. Los bytes enviados pero aún no confirmados nunca pueden exceder la ventana.
```
LastByteSent - LastByteAcked <= rwnd
```
### Casos especiales: Pérdida de segmentos
#### ¿Cómo recuperar segmentos perdidos sin retransmitirlos todos?
##### Solución 1: NAK
* Se envía un NAK cuando el receptor detecta una brecha entre el número de secuencia esperado y el recibido. Solicita los segmentos faltantes mediante un campo de opciones.
* Cuando recibe los segmentos faltantes, el receptor envía un ACK acumulativo confirmando todos los datos que tiene el búfer.
* En TCP no existe NAK, por lo que el receptor reenvía el mismo ACK acumulativo (ACK duplicado) por cada segmento que llega fuera de orden. Al recibir 3 ACKs duplicados, el emisor dispara *fast retransmit* sin esperar el timeout.
##### Solución 2: ACK selectivos (SACK)
* El receptor le dice al emisor exactamente qué rangos de bytes ha recibido, así el emisor puede reenvía sólo lo que le falta.
* Hay dos opciones dentro de los segmentos TCP:
	1. SACK - Permitted option.
		* Indica que el extremo soporta SACK. Si ambos la envían, la conexión usará SACK.
	2. SACK option
* Con SACK el emisor sabe exactamente qué falta
	* Por lo que tenemos una retransmisión más precisa y eficiente que con ACK acumulativo.
	* Pero estas son opciones y no obligaciones de implementación.
### Ventana Cero
Con `rwnd = 0`, el emisor no puede enviar datos normales, pero hay 2 **excepciones**.
1. **Datos urgentes**
	* Pueden enviarse datos marcados como urgentes.
2. **Sonda de 1 byte**
	* El emisor puede enviar un segmento de 1 byte para forzar al receptor a reanunciar el tamaño de ventana.
#### ¿Porqué existe la sonda?
Por si el aviso de ventana del receptor se pierde, el emisor podría esperar indefinidamente. La sonda de un byte evita el bloqueo irreversible forzando una nueva actualización de `rwnd`.
# Control de Congestion
