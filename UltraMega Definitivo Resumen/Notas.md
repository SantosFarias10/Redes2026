# Duran viejo gaga no me vas a ganar
# Capa de Transporte
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
La congestión ocurre cuando la red no puede seguir el ritmo del tráfico que le llega. O sea, es cuando un emisor manda más datos de los que la subred puede transportar.
#### Cómo ocurre
1. **Llegan demasiados paquetes**: El tráfico entrante supera la capacidad de reenvío del enrutador.
2. **Los búferes se desbordan**: La cola de salida se llena y el enrutador no puede almacenar más.
3. **Se descartan paquetes**: El enrutador tira segmentos.
### El Problema
Necesitamos controlar el ritmo de envío según la capacidad real de la red.
Es la capa de transporte (TCP en el emisor) quien regula el ritmo.
#### ¿Cómo controla la congestión TCP?
TCP hace lo siguiente
* **Mide**
	* Mantiene la **venta de congestión** (`cwnd` o VC): El máximo de bytes que el emisor puede tener sin confirmar en un momento dado.
* **Detecta**
	* ¿Cómo sabe el emisor que la red se está congestionando? Porque asume que **si se pierde un paquete, es porque un enrutador se saturó y lo descartó**
	* El emisor se da cuenta de esta pérdida utilizando los mecanismos
		* Timeout: El temporizador expiró porque el ACK nunca llegó.
		* ACKs duplicados: Llegan 3 ACKs repetidos porque los paquetes se desordenaron debido al hueco que dejó el paquete perdido.
* **Reacciona**:
	* Cuando el emisor detecta la pérdida, entiende que hay embotellamiento en la red.
	* Su reacción inmediata es reducir drásticamente el tamaño de su ventana de congestión
	* Al hacer esto, el emisor se auto-limita y envía menos datos por viaje, dándole tiempo a los enrutadores intermedios para vaciar sus colas y aliviar la red.
#### ¿Cómo detecta TCP la congestión?
TCP detecta la congestión a partir de la pérdida de paquetes. Cuando expira el temporizador de retransmisión, el paquete probablemente se perdió.
##### ¿Porqué? hay 2 posibles causas
1. **Ruido en el cable** (capa física / enlace)
	* Un error físico corrompe los bits y el paquete se descarta.
2. **Enrutador congestionado**
	* Los búferes están llenos y el enrutador descarta paquetes.
#### ¿Qué tamaño le damos a la Ventana de Congestión?
Si la ventana de congestión es muy chica desperdiciamos capacidad, si es muy grande generamos congestión.
Por lo que hay que empezar dándole poco tamaño y aumenta hasta que la red indique que llegamos al límite **arranque lento**.
### Arranque Lento
* Este algoritmo empieza con una ventana de congestión pequeña y se duplica hasta detectar que la red se satura.
* Se llama "Lento" porque arranca desde 1 segmento.
* Una vez que el emisor envía ese primer segmento y recibe confirmación, el algoritmo dicta que la ventana de congestión crece sumando un segmento por cada ACK recibido.
	* O sea, RTT 1: envía 1 segmento. Recibe 1 ACK, la ventana crece a 2.
	* RTT 2: Envía 2 segmentos, Recibe 2 ACKs, la ventana crece a 4.
	* RTT 3: Envía 4 segmentos. Recibe 4 ACKs, la ventana crece a 8.
* Esto hace que el crecimiento sea exponencial.
* Este crecimiento continúa hasta que ocurre una de estas dos cosas:
	* Encuentra el límite de la red, o sea se produce congestión
	* Alcanza un umbral de seguridad (`ssthresh`). Si llega a un umbral predefinido, deja de crecer de forma exponencial y pasa a un crecimiento lineal.
#### Paso a Paso
1. Inicio: Emisor y receptor acuerdan el Segmento de tamaño máximo (STM). La ventana arranca en 1 STM y envía 1 segmento.
2. Confirmación: Si el ACK llega antes del timeout, la ventana crece en 1 STM por cada ACK recibido en esa ráfaga.
3. Duplicación por RTT: Al confirmar `n` segmentos, la ventana pasa de `n` a `2n`, el ritmo crece exponencialmente.
4. Hasta el límite: Crece hasta que ocurre un timeout o se alcanza el tamaño de la ventana del receptor.
#### Reglas claves
1. Si el ACK llega a tiempo
```
ventana = ventana + n (segmentos)
```
2. Si hay timeout
```
ventanta = ventata / 2
```
* La ventana se recorta a la mitad y no se enviarán ráfagas mayores.
#### Problemas del arranque lento
1. **Reacción Excesiva**
	* Al haber un timeout, corta a la mitad puede ser demasiado, si la red puede soportar más que la mitad de la ventana actual => se desaprovecha la capacidad de la subred.
2. **Detección Lenta**
	* El timeout tarda en dispararse. El emisor no sabe que hubo pérdidas hasta que se expire el temporizador, por lo que sigue inyectando paquetes con una ventana mayor a la que la red soporta.
#### ¿Cómo detectar las pérdidas antes del timeout?
A través de los ACK
* Asumimos que cada segmento que llega al receptor dispara un ACK.
* El ACK siempre indica el próximo byte en orden recibe.
* Eso significa que el emisor puede oír la pérdida vía los ACKs duplicados sin esperar a que expire el temporizador.
##### ¡¡¡OJO!!!
No todo ACK duplicado implica pérdida.
Recordad que los paquetes pueden llegar desordenados y disparar ACKs duplicados Falsos.
* Caso 1: Reordenamiento (**falso positivo**)
	* Llegan segmentos fuera de orden.
	* Se dispara 1 - 2 ACKs duplicados pero ningún paquete se perdió.
	* **Pocos duplicados**.
* **Caso 2: Pérdida real**
	* El segmento `N` no llego al receptor.
	* Los segmentos posteriores si llegaron, y cada uno dispara un ACK diciendo "Espero `N`"
	* **Muchos duplicados seguidos**.
Por eso es que se cuentan los ACKs
## TCP Tahoe
Tahoe Combina arranque lento con un umbral que divide las fases de crecimiento.
#### Idea nueva
El umbral (SSTHRESH, *slow start threshold*).
- **Debajo del umbral** usamos Arranque lento
	* La ventana se duplica cada RTT,  crecimiento exponencial, para encontrar rápido la capacidad de la red.
- **Sobre el umbral**: Evitación de congestión 
	* La ventana crece de manera lineal. Nos acercamos con cuidado a la capacidad máxima. 
	* MSS = *Maximum Segment Size* ( = STM en castellano).
### Reacción ante pérdida
Ante pérdida (timeout o 3 ACKs duplicados), Tahoe reacciona igual:
```
umbral (ssthresh) = VC actual / 2
VC = 1 MSS
```
1. Se detecta pérdida (timeout o 3 ACKs duplicados)
	* El emisor registra el evento de congestión.
2. Se guarda la mitad como umbral (`ssthresh = VC actual / 2`)
3. La ventana vuelve al mínimo `VC = 1 MSS`
	* Empezamos desde cero porque no sabemos si el estado de la red cambió.
4. Arranque lento hasta el umbral, luego crecimiento lineal.
### Estado estable: El "diente de sierra"
#### Patrón típico 
- La VC sube de forma lineal en evitación de congestión.
- Cuando llega al punto de saturación, se pierde un paquete.
- La VC se corta a la mitad (pasa a ser el nuevo umbral) y vuelve a 1 MSS.
- Arranque lento rápido hasta el umbral, luego crecimiento lineal otra vez.
Este ciclo es la forma clásica "**diente de sierra**".
![518](Screenshot_2026-05-26-14-13-35_10485.png)
## TCP Reno
Reno trata distinto a una pérdida detectada por ACKs duplicados que por un timeout:
* **Timeout** (=> la red se colapsó)
	* El "reloj de ACKs" se detuvo, probablemente ningún paquete está llegando. 
	* ¿Qué hace Reno? Mismo tratamiento que Tahoe: VC = 1 MSS (arranque lento).
* **3 ACKs duplicados** (=> la red sigue andando)
	* Están llegando paquetes posteriores, entonces la red entrega, por lo que hay congestión pero no colapso.
	* Acción: Salta el arranque lento y entra en recuperación rápida.
>[!tip] Tahoe y Reno son iguales al inicio de la conexión y ante timeouts. Difieren solo ante 3 ACKs duplicados.

![](Screenshot_2026-05-26-14-18-32_20628.png)
#### Cuadrito comparativo

| Evento                | TCP Tahoe                       | TCP Reno                                |
| :-------------------- | :------------------------------ | :-------------------------------------- |
| Inicio de conexión    | Arranque lento                  | Arranque lento (igual)                  |
| Timeout               | VC &larr; 1 MSS, arranque lento | VC &larr; 1 MSS, arranque lento (igual) |
| 3 ACKs duplicados     | VC &larr; 1MSS , arranque lento | VC &larr; ssthresh, recuperación rápida |
| Después de la pérdida | Vuelve a empezar desde 1        | Sigue enviando con la mitad de la VC    |
### Adicionales Importantes
#### La congestión también se detecta por delay
El problema de detectar congestión sólo con pérdidas es que, antes de que haya pérdidas: 
- Las colas en routers crecen.
- El RTT aumenta.
- Se acumula delay.
Las señales basadas en delay, permiten usar el aumento del RTT como indicador temprano.
* RTT esperado (sin cola)
* RTT medido (con cola)
* Si RTT es mayor, entonces hay cola y entonces hay congestión.
La congestión aparece antes del drop. El delay permite detectarla antes.
#### Control de Flujo + Control de congestión en TCP
![478](Screenshot_2026-05-26-14-25-36_5944.png)
# Duplicados retrasados
No se pueden entregar segmentos duplicados a la capa de aplicación. Como consecuencia, el receptor debe poder decidir para cada segmento que llega, si es duplicado o no.
#### La manera de detectar duplicados eficientemente 
Es a través de los **números de secuencia**:
- Numerar cada segmento con un número de secuencia.
- Dos segmentos con número distinto, son distintos.
Esto funcionaría perfecto si los números fueran de tamaño arbitrario, pero el espacio de secuencia es finito.
#### ¿Porqué?
Los segmentos tienen longitud máxima, el número de secuencia debe entrar en el campo del encabezado. Este campo tiene longitud fija, sólo hay un número finito de valores posibles.
Como consecuencia, los números se reutilizan. Numerar los segmentos sólo con eso ya no alcanza.
### Escenarios donde aparecen los problemas
#### Escenario A: Dentro de una conexión
El espacio de secuencia se cicla:
- Los números van 0 -> 1 -> 2 -> 3, vuelven a 0 y suben hasta 3 de nuevo. 
- Un segmento con número 3 queda demorado y llega cuando ya pasó el segundo 3.
- Resultado
	- El receptor lo acepta como nuevo: el espacio de secuencia se reutilizó y el duplicado vivió demasiado en la red.
#### Escenario B: Entre Conexiones
En el ejemplo anterior:
- Tenemos un espacio de secuencia de 2 bits (0-3).
- Antes de la segunda conexión se libera la primera.
- La segunda conexión llega rápido al número 2.
- Un duplicado retrasado de la primera conexión con número 2 aún vive en la red.
- Es aceptado por error en la segunda conexión.
### La idea unificadora: Limitar el tiempo de vida 
Asegurar que ningún paquete viva más de `T` segundos en la red. 
Eliminar paquetes viejos que sigan circulando hace que ambos escenarios sean manejables.
#### Solución para el escenario A 
El origen no debe reutilizar un número de secuencia dentro de `T` segundos (tiempo de vida del paquete).
Funciona porque, cuando se llega a un número ya usado, ese segmento viejo ya murió hace tiempo en la red. El espacio de secuencia debe ser lo bastante grande para que no se cicle dentro de `T` segundos.
#### Solución para el escenario B
Si la nueva conexión empieza en 0, puede chocar con duplicados de la conexión anterior.
Entonces, cada conexión elige un número inicial de secuencia distinto para no entrar en el rango de los duplicados de conexiones anteriores.
**Implementación 1: Número inicial aleatorio**
* Al crear una nueva conexión, cada extremo elige un número aleatorio de 32 bits. Ese número pasa a ser el número inicial de secuencia para los datos.
* Esto tiende a funcionar porque la probabilidad de que un duplicado retrasado caiga en el rango de la nueva conexión es baja gracias a la elección aleatoria.
**Implementación 2: número inicial atado al reloj**
* Cada host tiene un contador binario que se incrementa en intervalos uniformes. El número inicial de cada nueva conexión se toma del valor actual del reloj. Los relojes de los hosts no necesitan estar sincronizados. 
* Esto funciona porque el reloj avanza monotónicamente: El número inicial es siempre posterior al usado por la conexión previa, fuera del rango de sus duplicados. 
# Duran ya me garchaste 1 vez, esto es vuelta y vuelta, y ahora me toca a mi viejo gaga
# Capa de Red
Repasar Parte 1 y 2
# Algoritmo de Enrutamiento para WANs jerárquicas
## Introducción
Dividir la red en regiones (de igual importancia) o áreas (con un área dorsal) no es solo una cuestión administrativa: Cambia la forma en que cada enrutador percibe la WAN.
Ya no todos los enrutadores ven todo, cada uno ve su región o área con detalle y las demás con resúmenes construidos para preservar lo esencial sin arrastrar la complejidad completa.
## Enrutamiento en red de regiones
### Introducción
#### ¿Cómo adaptar el protocolo de estado de enlace para que funcione cuando cada región solo conoce su topología interna?
Para responderla, vamos a derivar un algoritmo que: 
* Mantiene el estado de enlace dentro de cada región
* Construye resúmenes de región para enviar a otras regiones atravesando enlaces desde un enrutador de borde a otro.
* Y distribuye esos resúmenes entre regiones mediante inundación controlada, que evita bucles y limita el alcance de cada paquete.
##### Resultado
Un protocolo que permite a cada enrutador reconstruir un **grafo global híbrido**: 
* Detallado en su región y resumido en las demás.
* Sobre ese grafo ejecutará Dijkstra para obtener rutas coherentes en toda la WAN.
### Tipos de enrutadores
#### Tipos de enrutadores en una región:
- **Interno:** Enrutador que no tiene enlaces hacia otras regiones.
- **De borde:** Enrutador que tiene enlaces hacia otras regiones.
##### ¿Qué ve un enrutador interno sobre su región?
Todos los enrutadores de la región y los enlaces entre ellos.
##### ¿Qué ve un enrutador en una región sobre una región ajena?
* Ve los enrutadores de borde.
* Ve arcos entre 2 enrutadores de borde si hay caminos dirigidos entre ellos.
* Un arco entre dos enrutadores de borde tiene como peso el del camino más corto entre ellos.
* Ejemplo: Si longitud de camino es cantidad de saltos, un enrutador de región 2 ve de la región 5: `{5A, 5C}` con peso 2.
![279](Screenshot_2026-05-27-20-37-39_31687.png)
##### ¿Cómo sería el grafo de toda la red que ve un enrutador?
* Hay que combinar la visión detallada de la región donde está con su visión resumida de las demás regiones.
* **Colocar**: Grafo de su región, los grafos resumidos de las demás regiones y enlaces entre enrutadores de borde de distintas regiones. 
* Ejemplo: Grafo del enrutador `2C`.
![365](Screenshot_2026-05-27-20-40-07_24620.png)
### Paquetes de estado de enlace (LSP)
##### ¿Cómo sería un paquete de estado de enlace (LSP) de un enrutador dentro de una región?
* Es el LSP con información de retardos a los vecinos de un enrutador.
* Por ejemplo: Armar paquete de estado de enlace de `2B`.
	* Enrutador `2B`, número de secuencia.
	* `2A, 1`.
	* `2D, 1`.
	* `2C, 2`.
#### ¿Cómo convertir la topología interna de una región en un resumen que otros enrutadores puedan usar para enrutar sin conocer todos los detalles?
##### ¿Cómo sería el paquete de estado de enlace que resume una región?
* **Nombre de enrutador de borde de región** que construye el paquete.
* **Número de secuencia** (para distinguirlo de paquetes de estado de enlace previos).
* **Información del grafo** que ve un enrutador ajeno a esa región. 
* Ejemplo: LSP de la región `5`.
	* Enrutador `5A, Seq`.
	* `{5A, 5C}` con peso `2`.
Además, un **enrutador de borde necesita construir un segundo tipo de paquete** de estado de enlace **con los enlaces con otros enrutadores de borde** con los cuales está conectado directamente. Lo llamamos **LSP de enlaces externos**.
* Ejemplo: El enrutador `3B` construye el paquete de estado de enlace:
	- `3B`, número de secuencia.
	- `1C, 1`.
	- `4A, 1`.
![349](Screenshot_2026-05-27-20-45-20_24739.png)
### Fases del protocolo de enlace
#### ¿Cuáles serían los pasos de un protocolo de enrutamiento para una red de regiones?
##### Fase 1: Para cada región `A` hacer lo siguiente
1. Se ejecuta el protocolo de estado de enlace dentro de `A`, hasta completar la difusión de los LSP internos.
2. Cada enrutador de borde construye LSP de enlaces externos.
3. Entre los enrutadores de borde de `A` se elige un enrutador de borde designado `R`. Este enrutador va a ser el único responsable de generar el **LSP de resumen de la región**.
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
#### Fase 1: Trabajo interno en la región y creación de resúmenes
En esta etapa, cada región trabaja de forma independiente para conocerse por dentro y preparar la información que enviará al exterior:
1. **Protocolo básico interno:** 
	* Todos los enrutadores dentro de una región "A" ejecutan el protocolo de estado de enlace clásico.
	* Envían paquetes _HELLO_ para descubrir vecinos,
	* _ECHO_ para medir retardos,
	* y luego inundan sus LSPs internos para que todos en la región conozcan la topología local exacta.
2. **LSPs de enlaces externos:** 
	* Los **enrutadores de borde** (los que tienen cables que salen hacia otras regiones) usan los paquetes _HELLO_ y _ECHO_ para identificar a qué enrutadores de borde externos están conectados y cuánto cuesta llegar a ellos.
	* Con esto arman un paquete especial llamado "**LSP de enlaces externos**".
3. **El enrutador de borde designado:** Entre todos los enrutadores de borde de la región "A", se elige a uno como el "designado".
4. **Creación del resumen:** 
	* Este enrutador designado toma el mapa interno de su región y ejecuta el algoritmo de Dijkstra para calcular los caminos más cortos entre los distintos enrutadores de borde de su zona.
	* Con esos datos, construye un **grafo resumido de la región** y lo empaqueta en un **LSP de resumen**.
#### Fase 2: Distribución global y cálculo de rutas
Una vez que los resúmenes están listos, hay que hacerlos circular por toda la interred y armar el rompecabezas final:
1. **Distribución (Inundación):** 
	* El enrutador designado inunda su "LSP de resumen" por toda su región.
	* Cuando los demás enrutadores de borde lo reciben, lo envían a través de sus enlaces hacia las regiones vecinas. 
	* Los enrutadores de las regiones vecinas lo reciben, lo inundan internamente y lo siguen propagando hacia otras regiones.
	* Los LSPs de enlaces externos se distribuyen de la misma manera.
2. **Construcción del Grafo Global:**
	* Llegará un momento en que un enrutador cualquiera tendrá en su memoria tres cosas:
		* los LSPs detallados de su propia región,
		* los LSPs de resumen de todas las demás regiones,
		* y los LSPs de enlaces externos.
	* Al unir estas piezas, el enrutador construye un **grafo global híbrido**: su propia región la ve con todo el detalle, mientras que a las demás las ve como áreas resumidas unidas por los enlaces externos.
3. **Ejecución de Dijkstra:** Finalmente, sobre este nuevo mapa global, el enrutador ejecuta el algoritmo de Dijkstra para encontrar el camino más corto hacia cualquier destino y así actualizar su **tabla de reenvío**.
### Tipos de inundación
1. **Inundación intra-región de paquetes de estado de enlace de retardos de vecinos**:
	- **Función:** Distribuye la topología detallada y exacta de una región.
	- **Alcance:** Los paquetes (LSPs) solo circulan de forma interna, sin salir de los límites de esa región específica.
	- **Manejo:** Requiere una tabla de paquetes vistos que se organiza e indexa por el **enrutador de origen** (es decir, el enrutador que creó el paquete).
2. **Inundación inter-regiones**:
	- **Función:** Se encarga de distribuir los grafos resumidos de cada región y los LSPs de enlaces externos hacia todas las demás regiones de la red jerárquica.
	- **Alcance:** Los paquetes circulan entre las distintas regiones, atravesando a través de los enrutadores de borde.
	- **Manejo:** Exige una tabla de inundación diferente, ya que se organiza e indexa por la **región de origen**.
3. **Inundación intra-región de paquetes de resumen de la región**:
	- **Función:** Difunde, internamente dentro de una región, el resumen del mapa que fue generado por su propio enrutador de borde designado.
	- **Alcance:** Interno, dentro de la propia región.
	- **Manejo:** Utiliza la misma tabla que la inundación inter-regiones, ya que estos paquetes de resumen se identifican por la región a la que resumen y no por el enrutador individual.
#### ¿Por qué se dividen en diferentes tipos?
Principalmente porque cada inundación necesita ser indexada por identificadores distintos, lo que hace imposible unificarlas en una sola tabla. Además, estos tres tipos se diferencian profundamente en sus **alcances**, en su **semántica** (topología detallada frente a topología resumida), y en sus **reglas de reenvío** (qué paquetes se deben reenviar, hacia dónde y cuáles se deben bloquear).
## Enrutamiento en áreas interconectadas
#### ¿Cómo debe modificarse el algoritmo anterior para funcionar en una jerarquía con un área dorsal y múltiples áreas no dorsales, considerando además que las tablas de reenvío apuntan a destinos que representan LAN, y se direccionan interfaces de máquinas?
El resultado es un protocolo donde cada enrutador construye un grafo global de acuerdo a su posición en la jerarquía, combinando topología detallada de áreas donde está con resúmenes bipartitos de las demás.
### Grafos de áreas
Nuestro objetivo es evitar que un enrutador tenga que procesar y memorizar cada cable y nodo de toda la red global
#### Representación de un área
- **Nodos y Arcos:** Los enrutadores y las redes LAN (donde están los usuarios finales) se convierten en nodos, y las conexiones se convierten en arcos direccionales con un "costo" o retardo.
- **Redes de multi-acceso** (como un conmutador): En lugar de dibujar líneas conectando a todos los enrutadores entre sí, la red en sí misma se dibuja como un nodo central. El salto desde esa red hacia los enrutadores tiene un costo de 0, lo que simplifica drásticamente el grafo.
![](Screenshot_2026-05-27-22-18-33_16816.png)
#### ¿Qué grafo ve un enrutador de un área ajena?
Si eres un enrutador, tu área local es tu "barrio", y las otras áreas son "ciudades lejanas". **No nos importa cómo están conectadas internamente las ciudades lejanas**. Por lo tanto, la topología de un área externa se resume o "comprime". 
Solo necesitamos saber dos cosas: 
* Cuáles son las puertas de entrada a esa ciudad, los **EBA**, **Enrutadores de Borde de Área**. 
* y el costo del camino más corto desde esa puerta de entrada hasta la LAN final donde quieres entregar el paquete.
#### ¿Qué grafo ve un enrutador interno sobre el área dorsal?
El área dorsal (o Área 0) funciona como la "autopista de tránsito" obligatoria que conecta todas las áreas entre sí. Si somos un enrutador interno, no necesitamos conocer en detalle los carriles y peajes de esa autopista. Para nosotros, el área dorsal se comprime en un resumen muy simple: **Solo vemos el costo de ir desde la puerta de entrada (el EBA del área) hacia la puerta de salida (el EBA del área ajena a la que queremos llegar)**. 
Al evitar compartir el mapa completo de la autopista con todos los enrutadores del mundo, los paquetes de resumen son pequeñísimos, lo que reduce el consumo de ancho de banda y memoria.
#### ¿Qué ve un enrutador que pertenece al área dorsal?
Los enrutadores dorsales _son_ la autopista. Como ellos tienen la responsabilidad de mover el tráfico pesado de un extremo a otro, **sí necesitan conocer el mapa completo y detallado del Área 0** (todos sus nodos y enlaces). 
Además, una particularidad del protocolo OSPF es que el peso o costo de estos enlaces no se calcula adivinando automáticamente el retardo, sino que es configurado a mano por el administrador de la red para dictar por dónde conviene que viaje el tráfico.
### Grafos Globales
#### El enrutador dorsal "puro" (R no EBA)
Este es un enrutador que vive **exclusivamente en la autopista principal** (red dorsal) y no pertenece a ninguna área local. Su Grafo Global se arma uniendo dos piezas:
- **Su mundo al detalle (Topología completa del área dorsal):** 
	- Como su trabajo es mover tráfico por el núcleo, conoce cada centímetro de la autopista.
	- Conoce a todos los enrutadores del Área 0 y todos los cables (enlaces) que los conectan.
- **El resto del mundo resumido (Grafo resumido de las áreas):** 
	- Las áreas locales (`A1`, `A2`, etc.) son ajenas a él. No le importa cuántos enrutadores hay allí adentro. Por eso, solo incorpora a su mapa un **grafo bipartito**, que es una forma de decir que solo ve "saltos directos" desde las puertas de entrada de esa área (los EBA) hasta los destinos finales (las LAN de computadoras).
![](Screenshot_2026-05-27-22-26-11_30931.png)
#### El Enrutador de Borde de Área (EBA)
Este enrutador actúa como una **frontera**. Tiene "doble nacionalidad": un cable lo conecta a la autopista central (Área 0) y otro cable lo conecta hacia adentro de un área local (por ejemplo, el Área 1). Su Grafo Global es un poco más grande porque une tres piezas:
- **Detalle 1 (Topología completa del área dorsal):** Al ser parte de la red central, también conoce toda la autopista perfectamente con todos sus costos.
- **Detalle 2 (Topología de sus áreas locales):** Como él vive físicamente en el Área 1, conoce de memoria todos los enrutadores internos y enlaces de ese "barrio".
- **El resto del mundo resumido (Grafo resumido de áreas ajenas):** Para las áreas donde él _no_ está (por ejemplo, el Área 2 o el Área 3), aplica la misma compresión que el caso anterior. Solo ve un grafo bipartito que conecta a los EBA ajenos con sus respectivas LAN.
### Paquetes de estado de enlace
#### Paquete de estado de enlace de retardos a los vecinos
Es lo mismo que ya vimos:
* Se construyen dentro de cada área no dorsal y dentro del área dorsal.
* Cada enrutador interno de un área construye un paquete de estado de enlace que contiene los costos hacia cada uno de sus vecinos dentro del área.
La estructura del paquete es la misma del protocolo de estado de enlace. La diferencia de OSPF con el protocolo es que los retardos no se estiman automáticamente, sino que son configurados por el administrador de la red.
#### Los pasos para construir el paquete de estado de enlace de retardos es lo mismo que ya vimos
#### Paquete de estado de enlace de resumen de área no dorsal `A`
* Este paquete es construido por un EBA región `R` del área `A`.
* Contiene la información necesaria para que los enrutadores de otras áreas puedan alcanzar las LAN de `A` sin conocer su topología interna.
* El paquete incluye para cada LAN de `A`:
	* El EBA de `A` que la anuncia,
	* El costo mínimo desde ese EBA hasta esa LAN dentro del área `A`.
* En otras palabras: El paquete resume el grafo bipartito que ya vimos “EBA de `A` -> LAN de `A`”, donde cada arco está representado por el costo mínimo de su EBA hacia su LAN.
##### Los pasos para distribuir este tipo de paquete, son
1. **Inundación dentro del área `A`:** Se distribuyen por inundación los LSP de retardos a vecinos de todos los enrutadores de `A`.
2. **Reconstrucción del grafo del área `A`:** Con los LSP de retardos a vecinos para los enrutadores de `A`, cada EBA de `A` reconstruye la topología completa del área `A`.
3. **Ejecutar Dijkstra para cada EBA de `A`:** para cada EBA de `A` se ejecuta Dijkstra sobre el grafo del área `A` para obtener su árbol de caminos mínimos hacia todas las LAN del área.	
4. **Construcción del paquete de resumen de `A`:** Usando los arboles de caminos mínimos de los EBA, se construye el paquete de estado de enlace de resumen del área `A`, que contiene los costos mínimos desde cada EBA de `A` hacia cada LAN de `A`.
![](Screenshot_2026-05-27-22-32-15_25526.png)
### Pasos del protocolo
1. **Construcción de los paquetes de estado de enlace:** Cada enrutador construye los diferentes tipos de LSP según corresponda:
	* Retardos a vecinos,
	* Resúmenes de áreas no dorsales,
	* Resúmenes del área dorsal.
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
Cada enrutador mantiene una **BDEE** 
* Contiene todos los LSP que ha recibido.
* Debe ser creada al iniciar el enrutador, y luego mantenerse actualizada.
* Dentro de un área todos los enrutadores deben tener la misma BDEE, para construir misma visión del grafo y, por lo tanto, tablas de reenvío coherentes.
* **Consecuencias** de tener una BDEE:
	* Almacena información que un enrutador puede intercambiar con sus vecinos.
	* Se actualiza cuando el enrutador recibe LSP más nuevos que los que ya tiene.
### Sincronización de dos enrutadores adyacentes
¿cómo hacer circular correctamente la información? OSPF usa un único mecanismo de inundación con ámbitos distintos y requiere sincronización entre enrutadores adyacentes para mantener coherencia.
* La inundación del protocolo opera mediante intercambio de información de estado de enlace entre enrutadores adyacentes.
#### ¿Qué tipos de paquetes se necesitan para intercambiar información entre enrutadores adyacentes? 
* Paquete de **descripción** de base de datos (**PDBD**):
	* Contiene un resumen de todos los LSP que el enrutador emisor contiene en su BDEE
	* Incluye:
		* El enrutador emisor y número de secuencia del LSP,
		* Y, si el emisor es un EBA, el tipo de LSP (porque un EBA genera varios tipos).
	* El receptor compara estos números de secuencia con los de su propia BDEE para determinar qué LSP faltan o están desactualizados.
* Paquete de **pedido** de estado de enlace (**PPEE**):
	* Se usan para solicitar LSP específicos que el receptor necesita, según lo que detectó al analizar el PDBD. 
* Paquete de **actualización** de estado de enlace (**PAEE**):
	* Se usa para mandar LSP asociado al enrutador emisor que le fue solicitado
* Paquete de **Confirmación** de estado de enlace (**PCEE**):
	* Se usa para confirmar la recepción de un PAEE. 
	* Es para garantizar que la inundación es confiable y que no se pierden LSP.
#### ¿Cómo sincronizan sus BDEE dos enrutadores adyacentes?
Dos enrutadores vecinos deben sincronizar sus BDEE para asegurarse que ambos tienen exactamente la misma información de estado de enlace.
* Para coordinar el proceso uno de los vecinos actúa como maestro y el otro como esclavo.
* El maestro controla el intercambio de los PDBD.
* Durante la sincronización, los vecinos intercambian los siguientes tipos de paquete en el orden:
	* PDBD: Resumen de los LSP que cada uno tiene.
	* PPEE:  Pedidos de LSP que faltan o están desactualizados.
	* PAEE:  Actualizaciones que contienen los LSP completos.
	* PCEE:  Confirmaciones de recepción de los PAEE.
### Problema
En una LAN de enrutadores, sería muy ineficiente que cada enrutador intercambie mensajes de estado de enlace con todos los demás enrutadores de la LAN.
Esto implica demasiadas sincronizaciones y demasiados paquetes.
* ¿Cómo evitar todo este trabajo? Elegir un **enrutador designado (DR)**
* El DR es el punto central de sincronización: 
	* Es el enrutador con el que todos los demás enrutadores de la LAN intercambian y sincronizan su BDEE.
	* De esta manera no se necesita que cada par de enrutadores se sincronice entre sí.
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
### Mecanismos de inundación
Acá se usa un único mecanismos de inundación, pero cada clase de LSP tiene un ámbito que determina hasta dónde se propagara.
Cada tipo de LSP tiene un alcance diferente, por lo que el algoritmo se aplica en distintos ámbitos:
* Para **LSP de retardo a vecinos** el ámbito es dentro del área donde se originan.
* Para **LSP de resumen de área no dorsal** el ámbito es el área dorsal y las otras áreas.
* Para **LSP de resumen de área dorsal** el ámbito es el área del EBA que creó ese resumen.
#### ¿Se inundan primero los LSP de retardos a los vecinos y después los resúmenes?
* Dentro de cada área primero se inundan los LSP de retardos a vecinos.
	* Esto permite reconstruir la topología interna.
* Luego, los EBA calculan los resúmenes de área (no dorsal y dorsal) y esos resúmenes se inundan donde corresponde, según su ámbito.
# Organización y reenvío para interredes
Acá vemos como múltiples redes pueden coordinarse para comportarse como una sola interred funcional, y qué mecanismos arquitectónicos permiten que el reenvío extremo a extremo sea posible.
## Qué es una interred + Conceptos básicos
Una **interred** es un conjunto de WAN interconectadas. No todas las WAN tienen el mismo protocolo, y para ir de una WAN a otra se usan dispositivos como las puertas de enlaces o un enrutador multiprotocolo.
![](Screenshot_2026-05-27-22-56-51_21368.png)
## Organización de una interred
Acá se analizan cómo se estructura una interred cuando múltiples redes deben coexistir y cooperar.
#### Tipos de redes proveedoras de servicios de red que pueden interconectarse
* Redes de acceso.
* Redes regionales.
* Redes globales de tránsito.
### Relaciones entre redes
#### Relación proveedor <-> consumidor
La red proveedora provee servicio de tránsito a la red consumidora. La red cliente paga a la red proveedora para entregar paquetes a otros destinos y recibir paquetes enviados de otros destinos.
#### Relación de compañerismo
Los compañeros no se cobran por mandarse mensajes entre sus destinos. 
El compañerismo no es transitivo.
##### Multihoming
Significa que una red consumidora está conectada con varias redes proveedoras. Esta técnica es usada para mejorar la confiabilidad, por si el camino por uno de los proveedores de servicio de red falla
## Reenvío entre redes
Nos centramos en los mecanismos que permiten que un paquete viaje a través de redes heterogéneas sin perder continuidad semántica ni funcional.
Veremos dos grandes familias:
* **Fragmentación**: Permite adaptar paquetes a los límites de capa de red
* **Entunelamiento**: Encapsula paquetes para atravesar tecnologías intermedias.
### Redes con distintos tamaño de paquete máximo
Cada red impone un tamaño máximo a sus paquetes.
Si un paquete grande `P` quiere viajar a través de una red cuyo tamaño máximo de paquete es más pequeño, las puertas de enlace dividen los paquetes en **fragmentos**, enviando cada fragmento como paquete de interred individual.
Las redes tienen el problema de unir nuevamente los fragmentos.
#### ¿Cómo fragmentamos? 
Tenemos varios tipos de fragmentación
##### Fragmentación Transparente
* Cuando un paquete de tamaño excesivo llega a una puerta de enlace, esta lo divide en fragmentos.
* Todos los fragmentos se dirigen a la misma puerta de enlace de salida, donde se recombinan las piezas.
Esto tiene las siguientes **desventajas**:
* Sobrecarga para reensamblar y volver a fragmentar repetidamente.
* Todos los paquetes deben salir por la misma puerta de enlace (afecta el desempeño).
##### Fragmentación No Transparente
* Una vez que se ha fragmentado un paquete, cada fragmento se trata como si fuera un paquete original. Todos los paquetes pasan por la puerta de enlace de salida.
* La recombinación ocurre en el host de destino.
* IPv4 funciona de este modo.
Como **desventaja**, tiene que:
* Requiere que todos los hosts puedan hacer el reensamblado.
* Al fragmentarse un paquete grande aumenta la sobrecarga total, pues cada fragmento debe tener un encabezado.
Para determinar el tamaño de los fragmentos, el protocolo de interred define un tamaño de fragmento elemental. Al fragmentarse un paquete todas las partes son iguales al tamaño de fragmento elemental, excepto la última que puede ser más corta.
#### Fragmentación en IPv4
Los enlaces de red tienen MTU (tamaño máximo de transferencia) que corresponde a la trama a nivel de capa de enlace más larga posible. 
El campo de identificación es necesario para que el host de destino determine a qué datagrama pertenece un fragmento recién llegado.
* Todos los fragmentos de un datagrama contienen el mismo valor en el campo de identificación.
![](Screenshot_2026-05-27-23-08-27_27266.png)
* `MF`: Es un bit que significa más fragmentos. Todos los fragmentos excepto el último tienen establecido este bit, que es necesario para saber cuándo han llegado todos los fragmentos de un datagrama.
* `offset`: Indica en qué parte del datagrama actual va este fragmento.
* `DF`: Significa una orden de no fragmentar (porque el destino es incapaz de juntar las piezas de nuevo).
### Redes con distintos formatos de paquetes
El mecanismo de fragmentación resuelve solo una parte del problema de interoperabilidad: Qué hacer cuando el obstáculo es el tamaño del paquete.
Pero, en una interred también aparecen incompatibilidades, como formatos de encabezados distintos, modelos de servicio distintos, etc.
Para estos casos la fragmentación no alcanza. Se necesita un mecanismo que permita transportar un paquete "tal cual es" a través de una red. Este mecanismo es el **Entunelamiento**, es una forma de encapsular paquetes para que puedan atravesar tecnologías intermedias sin perder su identidad real.
#### Problema: 
Un host origen `h1` y destino `h2` están en la misma clase de red, pero hay una red diferente en medio.
¿Cómo hacer para mandar un paquete de `h1` a `h2`?
* Usar entunelamiento.
* Los paquetes son encapsulados en la red del medio usando un encabezado.
## Tablas de reenvío en interredes
#### Asumimos
* Que los destinos son LANs
* Direccionamos interfaces de máquinas
#### ¿Cómo se puede achicar la tabla de reenvío?
##### Idea 1
Que los destinos lejanos sean rangos de direcciones de interfaces que cubren varias LAN.
##### Idea 2
Que los destinos lejanos sea un prefijo que contenga varios prefijos de LANs.
* Esto es usado por IP.
* A esto se le llama **agregación de prefijos**.
# Enrutamiento de interredes
## Introducción
Organización del enrutamiento en 2 niveles
El modelo de dos niveles permite escalar el enrutamiento sin que cada enrutador deba conocer la topología completa de la interred:
* **Protocolo de puerta de enlace interior** (**PPEI**). 
	* Cada red usa su propio protocolo interno. 
	* Pueden ser distintos entre sí. 
* **Protocolo de puerta de enlace exterior** (**PPEE**). 
	* Es el protocolo en común para enrutar entre redes.
	* Todas las redes deben usar el mismo PPEE. 
#### ¿Porqué vemos PPEE?
* Las tablas de reenvío deben permitir mandar mensajes entre máquinas conectadas a redes diferentes. 
* El PPEE permite agregar información a ser usada con ese fin a las tablas de reenvío de los enrutadores.
* El enrutamiento de PPEE se preocupa de establecer las rutas a usar (que pasan por diferentes WAN) para permitir que se comuniquen máquinas pertenecientes a distintas WAN.
#### Lo que PPEE NO puede asumir es que
- No puede ver la topología interna de otras redes.
- No puede depender de que todos usen el mismo PPEI.
- No puede imponer métricas globales.
- No puede forzar a los enrutadores internos a participar. 
El diseño debe funcionar con mínima información.
#### Limitaciones
 * **Visibilidad limitada:** Un enrutador en una interred sabe que puede llegar a un destino, pero no tiene idea de cómo es el camino por dentro de las otras WAN.  Cada WAN se comporta como una caja negra para las demás.
* **Soberanía de WAN**: Una puerta de enlace tiene el derecho político de decidir qué información comparte y qué tráfico permite pasar
#### No se usan resúmenes de WANs para armar un grafo => ¿De qué otra forma puede controlar una WAN la información que quiere hacer visible?
* **Anuncio de caminos**: Avisando a WANs vecinas caminos a prefijos (LANs o agregación de ellas).  
* **Listas de ruta**: se intercambian caminos que indican la lista de WANs por las que debe pasar para llegar a esos destinos.
#### ¿Qué requisitos pedir para un PPEE?
* **Robustez y alcance:**
	* Visibilidad global de destinos: Transportar avisos sobre destinos que no están en la misma WAN.  
	* Escalabilidad: Abstraer la complejidad interna de otras WAN para no saturar las tablas de reenvío.  
	* Garantizar rutas libres de bucles: Encontrar caminos hacia prefijos evitando ciclos de enrutamiento en ellas.  
* **Autonomía y negocio:**  
	* Selección inteligente: capacidad de elegir entre múltiples caminos posibles hacia un destino basándose en la conveniencia.  
	* Soberanía de las WAN : Respetar las políticas de cada WAN a lo largo del camino.
#### ¿Qué información ve un enrutador en el nivel PPEE? 
* Abstracción de topología : No ve la infraestructura interna de otras WAN. 
* Visión parcial y agregada: Solo percibe prefijos, los identificadores de algunas WAN, algunas puertas de enlace (por ejemplo, de contacto directo). 
#### ¿Qué es una Política?
Una política son reglas que expresan:  
* Preferencias de enrutamiento (qué caminos se prefieren).  
* Restricciones de enrutamiento (qué caminos están prohibidos).
#### ¿Dónde se ejecuta PPEE?
Los PPEE se ejecutan sobre las puertas de enlace (enrutadores mulitprotocolo). 
### Responsabilidades de las puertas de enlaces
Para que la interred sea estable y rentable, la puerta de enlace debe:
* **Seleccionar Rutas**: Evaluar y elegir la mejor opción entre múltiples caminos hacia un mismo destino (basándose en métricas y políticas).
* **Filtrar Anuncios**: Publicar rutas hacia sus vecinos únicamente si cumplen con las políticas y acuerdos comerciales locales. 
* **Transparencia de Camino**: Avisar a sus vecinos el camino exacto (secuencia de WANs) que están usando.
La Puerta de enlace es un ‘guardian’ de los intereses de su WAN.
## Enrutamiento basado en inundación
Acá vemos un protocolo exterior sin optimizaciones, caminos explícitos, sin sesiones, sin prefijos. Sólo con inundación, caminos explícitos y direccionamiento de enrutadores.
Objetivo: construir un protocolo de enrutamiento adecuado para este tipo de interredes.  
#### Suposiciones iniciales
* Consideramos una interred formada por varias WAN interconectadas.  
* Asumimos que las puertas de enlace pertenecen simultáneamente a las WAN que interconectan.  
* Consideramos 'destinos' a los enrutadores conectados a LAN. Este conjunto es mucho más pequeño que el total de los enrutadores presentes en todas las WAN.
### Abstracción de la interred en los enrutadores
No tiene sentido que cada enrutador vea toda la topología de la interred de manera detallada.
#### ¿Cómo reducimos la cantidad de información que debe ver un enrutador?
Basta con que cada enrutador conozca de otra WAN:
* Sólo algunos destinos. 
* Sólo algunas puertas de enlace. 
* Sólo alguna otra WAN con la que esta conectada.
No necesita ver la topología interna completa de todas las WAN.
### Grafos de interred
#### ¿Cómo se representa el grafo de una interred?
* Los nodos son enrutadores multiprotocolo,
* Y un lado entre dos enrutadores multiprotocolo significa que esos enrutadores están conectados vía una WAN.
![](Screenshot_2026-05-27-23-26-42_6131.png)
### Información de rutas
El protocolo no puede calcular caminos óptimos globales, pero igual necesita algún tipo de información de ruta para: 
- evitar loops,
- comparar rutas alternativas,
- elegir un camino razonable, 
- exportar rutas entre WANs.
#### La información mínima de rutas que sirve para todo esto
* `WAN-PATH`: 
	* Lista de pares: `<puerta de enlace, WAN>` Cada par indica por qué puerta y por qué WAN pasó el aviso en su recorrido hacia al enrutador de destino.
* Destino:
	* La dirección del enrutador de destino al que se refiere la ruta.
![](Screenshot_2026-05-27-23-27-53_15821.png)
#### ¿Cómo hacer para propagar información de rutas?
* Si la ruta a anunciar entra por puerta de enlace `P` entonces, se inunda por difusión esa ruta por todas las WAN conectadas a `P` donde esa ruta es válida, menos por la WAN por la que llegó.
El protocolo de enrutamiento que vamos a definir usa únicamente dos **tipos de avisos**: 
- **Aviso de ruta**: informa la existencia de un camino hacia un destino. 
- **Aviso de remoción de ruta**: Invalida un camino previamente enunciado.
### Avisos de rutas
Un aviso de ruta contiene tres elementos fundamentales: 
- Destino = `R` (dirección del enrutador al que se refiere la ruta) 
- Secuencia = `n` (número de secuencia que permite identificar cuál es el aviso más reciente) 
- Camino (`WAN-PATH`): lista de pares que describe por dónde pasó el aviso en su recorrido hacia el destino.
#### Relación con la tabla de inundación
- La tabla de inundación no guarda rutas completas.
- Sólo guarda referencias a los paquetes de aviso.
- Los paquetes de aviso con sus caminos explícitos se guardan localmente en el enrutador.
#### Inundación
Tabla de inundación:
- Guarda entradas de la forma: `<puerta de enlace de origen, destino, número de secuencia, id_paquete>`.
- Para cada puerta de enlace de origen, mantiene una lista ordenada lexicográficamente por:
	* Destino y
	* Número de secuencia.
##### ¿Qué referencia?
Cada entrada apunta al paquete de aviso correspondiente, que contiene el camino explícito (`WAN-PATH`). Los paquetes referenciados se guardan localmente en el enrutador.
##### ¿Para qué sirve?
- Mantener un registro histórico de ruta vistas (útil para comparar rutas y elegir la mejor).
- Saber cuál es el aviso mas reciente para cada destino (comparando secuencias).
- Evitar reprocesar avisos viejos (si llega aviso con secuencia menor o igual, se descarta).
- Evitar loops (el camino explícito permite detector si la Puerta ya apareció).
### Exportación de avisos
#### Situación: 
Una puerta de enlace recibe un aviso por la `WAN_A` a la que pertenece y decide exportarlo a la `WAN_B` 
##### ¿Cuáles son los pasos del proceso? 
1. Aplicar política de salida: la puerta de enlace evalúa: cumple_política_salida(aviso, WAN_B) 
2. Si no cumple: no se exporta. 
3. Si se cumple: extender el camino explícito. Antes de exportar, se agrega al final de la `WAN-PATH`. Esto registra por dónde llegó el aviso. 
4. Se reenvía el aviso por `WAN_B`.
5. Se envía el aviso actualizado hacia la WAN vecina, permitiendo que otras puertas de enlace conozcan la ruta
**Resultado** de la exportación de aviso: El camino explícito crece cada vez que una ruta cruza de una WAN a otra, permitiendo: rastrear el recorrido, evitar loops, comparar rutas, y aplicar políticas de exportación de manera local.
### Avisos de remoción
Un aviso de remoción indica que una ruta previamente anunciada deja de ser válida.  Incluye el camino a remover.
#### ¿Cuál es el comportamiento del enrutador al recibir un aviso de remoción? 
1. Eliminar la ruta correspondiente: se borra de la tabla de inundación la entrada indicada al camino indicado. 
2. Propagar la remoción: El aviso se reenvía igual que un aviso de ruta, respetando políticas de salida.
3. Recalcular la mejor ruta: Se elige una nueva ruta entre las restantes (si existen)
4. Actualizar la tabla de reenvío: si hay una nueva mejor ruta, se instala. Si no la hay, se elimina la entrada
#### ¿Dónde se genera un aviso de remoción? 
Cualquier puerta de enlace que detecte que una ruta ya no es válida, puede generar un aviso de remoción. 
#### ¿Cuándo detecta una puerta de enlace que debe generar un aviso de remoción?
Cuando la puerta de enlace `G` pierde conectividad hacia el destino `R` en su `WAN_A` porque el enrutador se apaga o se desconecta. `G` genera: 
* `AVISO_REMOVER(destino=R, secuencia=nueva, camino=[], alcance=GLOBAL)`
El mismo debe propagarse globalmente.  
Un cambio de política de salida no genera remociones, porque las rutas ya anunciadas permanecen vigentes hasta que se remuevan explícitamente.
### Independencia entre dominios
Cada WAN funciona como un dominio autónomo: 
* Sus decisiones internas no deben afectar automáticamente a otras WAN. 
#### ¿Qué significa la independencia entre dominios? 
- Cada WAN controla qué rutas acepta (mediante sus **políticas de entrada**). 
- Cada WAN controla qué rutas exporta (mediante sus **políticas de salida**). 
- Los cambios internos no se propagan hacia afuera (por ejemplo, remociones locales de rutas por políticas.) 
- Las rutas exportadas permanecen válidas en otras WAN aunque la WAN de origen cambie sus reglas internas.
#### La independencia entre dominios es importante porque:
- Evita que decisiones locales contaminen toda la interred. 
- Permite que cada WAN tenga sus propias reglas, preferencias y restricciones. 
- Mantiene la coherencia del protocolo: las remociones globales solo ocurren por pérdida real de conectividad, no por cambios internos.
### Políticas de entrada
La política de entrada se modela como una función abstracta. 
* `cumple_política_entrada(aviso)`.
Determina si un aviso puede entrar a la tabla de inundación. 
##### Las políticas de entrada permiten: 
- Controlar qué rutas se aceptan. 
- Proteger la WAN de rutas no deseadas. 
- Mantener la tabla de inundación limpia y coherente.
### Políticas de salida
La política de salida se modela como una función abstracta.
* `cumple_política_salida(aviso, WAN_destino)`.
Determina si el aviso puede exportarse hacia una WAN vecina.
##### Las políticas de salida permiten
- Controlar qué rutas se propagan hacia otras WAN. 
- Limitar la visibilidad de rutas internas. 
- Proteger la interred de información no confiable 
- Mantener independencia entre dominios.
### Selección de rutas a un destino
#### ¿Cómo decide un enrutador llegar a un destino `R`? 
Para elegir la mejor ruta hacia un destino `R`, el enrutador realiza:
1. Búsqueda en la tabla de inundación: Recupera todas las entradas asociadas al destino `R` (todas las secuencias que recibió).
2. Recuperación de los avisos: Obtiene los paquetes de aviso correspondientes, cada uno con su camino explicito (`WAN-PATH`).
3. Aplicación del criterio de selección: compara todas las rutas candidatas y elige la mejor.
#### Criterios de selección de rutas
1. Menor cantidad de WAN en el camino: Prefiere rutas que atraviesan menos dominios. 
2. Si hay empate: Menor costo de salida (se elige la ruta cuyo primer salto tiene el menor costo).
### Construcción de la tabla de reenvío (Enrutador `E` -> Destino `R`)
1. Selección de la mejor ruta: El enrutador `E` elige la mejor ruta disponible hacia el destino `R`, usando los criterios de selección.
2. Si no existe ninguna ruta hacia `R`: Se elimina la entrada correspondiente a `R` de la tabla de reenvío de `E`. 
3. Caso con una ruta válida: Si existe al menos una ruta hacia `R`:
	1. Identificar la puerta de enlace de salida `P`. Es la puerta de enlace por la cual `P` debe salir de su WAN para seguir el camino hacia `R`. 
	2. Usar la tabla de reenvío ya existente hacia `P`. La tabla de reenvío de `E` ya contiene línea de salida para llegar a `P` (resultado del algoritmo interno de enrutamiento).
	3. Reutilizar esa línea de salida. Esa misma línea de salida es la que `E` debe usar para llegar a `R`.
#### Resumen conceptual
No se recalculan caminos internos cada vez. La tabla de reenvío hacia `P` ya representa el camino más corto. Para llegar a `R`, basta seguir el mismo primer salto.
### Recapitulación
#### Propiedades del protocolo 
- *Convergencia dentro de cada WAN*: 
	- Si no hay cambios en la interred, todos los enrutadores de una misma WAN terminan con la misma información de rutas. 
- *Evita loops por inspección del camino:* 
	- Cada aviso incluye su camino explícito.
	- Los enrutadores pueden detectar y descartar rutas que formen ciclos. 
- *Permite políticas locales:* 
	- Cada WAN y cada puerta de enlace puede aplicar criterios propios para aceptar, rechazar o exportar avisos. 
- *Permite múltiples rutas candidatas:* 
	- Los enrutadores pueden almacenar varias rutas hacia un mismo destino, y elegir la mejor según criterios definidos. 
- *Permite remoción explícita:* 
	- Las rutas no expiran automáticamente. 
	- Solo se eliminan mediante avisos de remoción. 
- *No es un protocolo optimizado:* 
	- Este protocolo prioriza claridad conceptual sobre eficiencia. 
	- No optimiza selección de rutas, no optimiza propagación de avisos, no optimiza convergencia. 
	- Es un modelo didáctico para estudiar las ideas fundamentales.
## Hacia BGP desde los primeros principios
