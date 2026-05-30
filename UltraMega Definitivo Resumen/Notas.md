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
