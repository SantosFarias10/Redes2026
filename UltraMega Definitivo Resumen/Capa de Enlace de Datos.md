# Duran, Duran, Duran goloso si los hay
# Primera Parte
## ¿Para qué existe la capa de enlace?
#### Situación
Una capa superior quiere enviar un paquete, pero el canal físico: 
* comete errores ocasionales; 
* tiene tasa de datos finita; 
* tiene retardo de propagación; 
* puede ser compartido por varias máquinas; 
* no entiende “paquetes”, solo señales o bits. 
>[!tip] La capa de enlace convierte un canal físico imperfecto en un servicio de comunicación local más ordenado y utilizable. 
#### Alcance: Nodo a Nodo
La capa de enlace trabaja entre **máquinas adyacentes** conectadas por un mismo enlace.
![](Screenshot_2026-05-28-22-29-29_13429.png)
Cada salto puede usar una tecnología de enlace diferente, por ejemplo, Ethernet, WiFi, fibra, enlace satelital, etc.
#### Diferencia entre las diferentes capas
| Capa       |     Unidad típica | Alcance                       | Pregunta que responde                  |
| :--------- | ----------------: | :---------------------------- | :------------------------------------- |
| Física     |         bit/señal | medio físico                  | ¿cómo represento bits en el medio?     |
| Enlace     |             trama | un enlace local               | ¿cómo entrego tramas a un vecino?      |
| Red        | paquete/datagrama | extremo a extremo con routers | ¿por qué camino va?                    |
| Transporte |          segmento | proceso a proceso             | ¿cómo doy servicio a las aplicaciones? |
### Encapsulamiento
La capa de red entrega un paquete. La capa de enlace lo mete dentro de una trama: Agrega un encabezado local y un campo final de verificación.
![](Screenshot_2026-05-28-22-38-45_1876.png)
#### Ethernet II vs IEEE 802.3
Ambos describen tramas Ethernet con MAC destino, MAC origen, datos y FCS. La diferencia histórica está en el campo de 2 bytes después de la MAC origen.

| Formato               | Campo de 2 B          | ¿Para qué se usa?                                                                                             |
| :-------------------- | :-------------------- | :------------------------------------------------------------------------------------------------------------ |
| Ethernet II /<br>DIX  | `Type` /<br>EtherType | Identifica directamente el protocolo superior: IPv4, IPv6, ARP, etc.                                          |
| IEEE 802.3<br>clásico | `Length`              | Indica longitud de datos; el protocolo superior se identifica con un encabezado LLC/SNAP dentro de los datos. |
>[!tip] En redes actuales con IP, lo más común es ver tramas con EtherType. En IEEE 802.3 moderno, el campo se interpreta como Length si el valor es menor o igual a 1500 y como Type si es Mayor o igual a 1536.
### ¿Qué contiene una trama?
Una trama de enlace suele tener: 
* encabezado con direcciones locales; 
* información de control; 
* campo de datos o carga útil; 
* campo de detección de errores; 
* a veces longitud, tipo de protocolo, flags o relleno.
## Servicios de la capa de enlace
### Servicios principales
La capa de enlace puede ofrecer: 
* entramado; 
* detección y **a veces** corrección de errores; 
* recuperación con ACKs y retransmisiones;
* control de flujo;
* manejo de acceso al medio;
* direccionamiento local;
* multiplexación hacia protocolos superiores.
No todas las tecnologías ofrecen todo con la misma intensidad.
### Entramado
El canal físico entrega un flujo de bits. La capa de enlace necesita separar ese flujo en unidades.
```
bits continuos -> tramas delimitadas
```
El problema de diseño es reconocer inicio y fin de cada trama sin confundirse con datos que parezcan delimitadores.
#### Sincronización y entramado
El canal físico puede comportarse de distintas maneras, pero la capa de enlace siempre necesita delimitar unidades.

| Tipo de canal<br>físico     | Qué pasa cuando no hay datos                                              | Ejemplos                                                                     |
| :-------------------------- | :------------------------------------------------------------------------ | :--------------------------------------------------------------------------- |
| Sincrónico /<br>continuo    | Se mantiene sincronismo enviando símbolos de `idle` o una señal continua. | SONET/SDH, enlaces seriales Ethernet modernos como 1000BASE-X o 10GBASE-R.   |
| Resincronizado por<br>trama | El receptor recupera sincronismo al comienzo de cada transmisión.         | Ethernet con preámbulo + SFD, WiFi con preámbulo PHY, UART con bit de start. |
>[!tip] Aunque el receptor esté sincronizado a nivel físico, todavía necesita saber dónde empieza y termina cada trama de enlace.
### Confiabilidad salto a salto
Para hacer una transmisión más confiable, un protocolo de enlace puede usar: 
* **ACK**: confirmación positiva;
* **NAK**: confirmación negativa;
* **timeout**: temporizador de retransmisión;
* **número de secuencia**; 
* **retransmisión** de tramas perdidas o dañadas. 
#### ACK y timeout
El emisor:
1. envía una trama;
2. inicia un temporizador;
3. espera ACK;
4. si vence el temporizador, retransmite.
>[!tip] El timeout debe cubrir el retardo de ida, procesamiento, respuesta y vuelta. Si es demasiado corto, aparecen retransmisiones innecesarias.
#### Problema: ACK perdido
Si la trama llegó bien pero el ACK se perdió:
![](Screenshot_2026-05-28-22-54-53_27451.png)
El receptor podría recibir la misma trama dos veces.
>[!tip] Los números de secuencia permiten reconocer duplicados y evitar entregar dos veces los mismos datos a la capa de red.
### Control de flujo
Control de flujo significa:
* **No enviar más rápido de lo que el receptor puede aceptar**.
No es lo mismo que control de congestión:
* **Flujo**: protege al receptor; 
* **Congestión**: protege a la red.
### Piggybacking
Piggybacking era “llevar a caballito” una confirmación dentro de una trama de datos que va en sentido contrario.
![](Screenshot_2026-05-28-23-00-08_26387.png)
>[!tip] No es un mecanismo de corrección de errores. Es una optimización para no mandar una trama ACK separada cuando ya hay datos saliendo en la dirección opuesta.
#### Cuándo no conviene esperar
Si el receptor espera demasiado para piggybackear el ACK: 
* el emisor puede pensar que la trama se perdió; 
* vence el timeout; 
* se retransmite una trama que quizá ya había llegado bien.
**Regla práctica**: si aparece pronto una trama de datos en sentido inverso, se piggybackea; si no, se manda un ACK independiente.
### Servicios: Cómo aparecen en ¡¡¡Ejercicios!!!
| Enunciado típico | Concepto que evalúa |
| :--- | :--- |
| “el ACK se pierde” | duplicados y números de secuencia |
| “emisor rápido, receptor lento” | control de flujo |
| “se aprovecha una trama de retorno” | piggybacking |
| “error en bits de la trama” | detección de errores |
| “varios nodos comparten medio” | control de acceso al medio 
## Canales de difusión y MAC
### El problema de conectar muchos nodos
Si cada par de máquinas tuviera un enlace dedicado, con n máquinas se necesitarían:
```
n * (n - 1) conexiones dirigidas
```
Eso es caro e incómodo. La alternativa histórica fue usar un canal de difusión: si una máquina transmite, todas pueden recibir.
### Canal de difusión (Broadcast)
En un canal de difusión: 
* varios nodos comparten el mismo medio; 
* todos pueden intentar transmitir; 
* si dos transmisiones se superponen, 
* aparece una colisión; 
* hace falta decidir quién puede usar el canal.
>[!tip] La subcapa MAC resuelve el acceso al medio compartido
### MAC: Control de Acceso al medio
La subcapa MAC (*medium access control*) responde:
#### ¿Quién puede transmitir ahora?
**Contención** significa que varios nodos tienen tramas listas y compiten por usar el mismo medio compartido. No hay turno garantizado: 
* cada protocolo define cómo intentar transmitir, cómo esperar, y qué hacer si dos nodos intentan al mismo tiempo.
Los protocolos de acceso múltiple intentan: 
* evitar colisiones;
* detectarlas rápido cuando ocurren;
* recuperarse con retransmisión;
* repartir el medio de forma razonable.
### Tres familias de ideas
![](Screenshot_2026-05-28-23-15-20_27309.png)
#### ALOHA puro
En ALOHA puro, 
* el **emisor**: 
	* transmite cuando tiene una trama; 
	* espera un ACK; 
	* si no llega, asume pérdida o colisión;
	* espera un tiempo aleatorio; 
	* retransmite. 
* El **receptor**:
	* si la trama es válida, manda ACK;
	* si está dañada, la ignora.
##### ¿Dónde aparece esta idea?
ALOHA puro casi no se usa tal cual en LAN modernas, pero su espíritu aparece en sistemas de acceso aleatorio.
>[!tip] Se usa cuando el tráfico es esporádico, los nodos son simples o coordinar antes de transmitir cuesta más que aceptar algunas colisiones y reintentos.
##### Línea de tiempo
![](Screenshot_2026-05-28-23-17-37_12438.png)
ALOHA no escucha el canal antes de transmitir. Funciona razonablemente con carga baja, pero las colisiones crecen rápido cuando aumenta la carga.
##### Performance simplificada
Supongamos: 
* todas las tramas duran `T`; 
* los intentos de transmisión son aleatorios; 
* `G` es la carga ofrecida: intentos de transmisión por tiempo de trama, sin unidad;
* `S` es el throughput útil: tramas exitosas por tiempo de trama.
Ejemplo: 
* `G = 0.5` significa `0.5` intentos por tiempo de trama; 
* `G = 1`, un intento por tiempo de trama;
* `G = 2`, dos intentos por tiempo de trama.
##### Período vulnerable
Si una trama empieza en `t = 0`, colisiona si otra trama empieza:
```
entre -T y +T
```
Por eso, el período vulnerable mide:
```
2T
```
Con intentos aleatorios tipo Poisson, la probabilidad de que no aparezca otro intento en ese intervalo es:
```
P(exito) = e^{-2G}
```
##### throughput
El throughput útil es carga ofrecida por probabilidad de éxito:
```
S = G e^{-2G}
```
El máximo ocurre en `G = 0.5`:
```
S_max = 1 / (2e) ≈ 0.184
```
* **Interpretación**: ALOHA puro aprovecha como máximo alrededor del 18% del canal para tramas exitosas.
#### ALOHA ranurado como comparación
Si las estaciones solo pueden empezar a transmitir al comienzo de ranuras de tamaño `T`, el período vulnerable baja de `2T` a `T`.

| Variante       | Período vulnerable |      Throughput |  Máximo |
| :------------- | -----------------: | --------------: | ------: |
| ALOHA puro     |               `2T` | `S = G e^{-2G}` | `18.4%` |
| ALOHA ranurado |                `T` |  `S = G e^{-G}` | `36.8%` |
>[!tip] Esto muestra por qué escuchar el canal, ranurar el tiempo o coordinar acceso puede mejorar mucho el uso del medio
#### CSMA 1 - Persistente
CSMA agrega **detección de portadora**:
1. si tengo datos, escucho el canal;
2. si está ocupado, espero;
3. cuando se libera, transmito;
4. si no recibo ACK, espero aleatoriamente y reintento.
>[!tip] Escuchar antes de transmitir reduce colisiones, pero no las elimina. 
##### ¿Por qué “1 - persistente”? 
Si el canal está libre, la estación transmite inmediatamente con probabilidad 1. Si está ocupado, sigue escuchando de manera persistente hasta que se libere. Por contraste, CSMA no-persistente espera un tiempo aleatorio antes de volver a escuchar, y CSMA p-persistente transmite con probabilidad `p` cuando encuentra el canal libre.
##### ¿Por qué CSMA todavía colisiona?
* Caso 1: Retardo de propagación.
![](Screenshot_2026-05-28-23-31-28_5545.png)
Cuanto mayor el retardo de propagación relativo al tiempo de trama es pero.
##### Aun con retardo cero
* Caso 2: Varias estaciones esperan el mismo canal ocupado.
![](Screenshot_2026-05-28-23-32-38_21011.png)
La persistencia - 1 transmite inmediatamente al liberar el canal.
#### CSMA/CD
CSMA/CD significa: 
* ***Carrier Sense***: detectar si el canal está ocupado; 
* ***Multiple Access***: muchos nodos comparten el medio;
* ***Collision Detection***: detectar colisión mientras transmito. 
Fue la base de Ethernet en medio compartido.
##### Emisor en CSMA/CD
1. escucha el canal;
2. si está libre, transmite;
3. mientras transmite, sigue escuchando; 
4. si detecta colisión, aborta; 
5. espera un tiempo aleatorio;
6. reintenta. 
**La gran mejora**: no desperdicia todo el tiempo de una trama completa si la colisión ya ocurrió.
##### ¿Cómo detecta una colisión?
En Ethernet cableada clásica: 
* el hardware transmite y escucha el cable;
* compara lo que puso en el medio con lo que lee;
* si difiere, interpreta colisión;
* envía una señal de interferencia o ***jam***; 
* corta la transmisión.
Esto depende de propiedades físicas del medio cableado.
##### ¿Qué es la señal de colisión?
En CSMA/CD no llega una “trama de error” ordenada. Lo que aparece es una alteración física del medio: señales superpuestas que no coinciden con lo que el emisor estaba transmitiendo. 
La **señal de colisión** es esa perturbación que se propaga por el cable. En Ethernet, además, una estación que detecta colisión transmite una señal jam para asegurar que las demás estaciones también la perciban y aborten.
**Por eso importa el peor caso**: la perturbación debe tener tiempo de volver al emisor antes de que termine de transmitir la trama.
##### Períodos en CSMA/CD
![](Screenshot_2026-05-28-23-36-16_2470.png)
El uso del canal alterna entre: 
* períodos de contención (nodos compiten por usar el medio);
* transmisiones exitosas; 
* períodos de inactividad.
## CSMA/CD y tamaño mínimo de trama
### La Pregunta central
En CSMA/CD, una estación debe seguir transmitiendo el tiempo suficiente para detectar una colisión del peor caso. 
Si una trama termina antes de que vuelva la señal de colisión, el emisor puede creer falsamente que transmitió con éxito.
#### Peor caso: Dos estaciones extremas
![](Screenshot_2026-05-28-23-42-37_29018.png)
* `τ` es el tiempo de propagación entre las dos estaciones más alejadas.
##### Razonamiento del peor caso
1. A empieza en `t = 0`.
2. La señal de A tarda `τ` en llegar al extremo `B`. 
3. Justo antes de recibirla, `B` cree que el canal está libre.
4. `B` empieza a transmitir. 
5. `B` detecta casi inmediatamente la colisión.
6. La perturbación/jam causada por la colisión vuelve hasta `A`. 
7. A se entera cerca de `2τ`.
#### Condición de seguridad
`T_tx` es el **tiempo de transmisión**: cuánto tarda el emisor en poner todos los bits de una trama en el medio. 
Para que `A` detecte la colisión.
```
T_tx >= 2τ
```
Como `T_tx = L/R`:
```
L_min >= R * 2τ
```
Donde `L` es el tamaño de la trama en bits, `R` es la tasa de transmisión y `L_min` el tamaño mínimo en bits.
### Método para ¡¡¡ejercicios!!! CSMA/CD
1. Identificar tasa `R`. 
2. Calcular propagación de ida: `τ = distancia / velocidad`.
3. Calcular ida y vuelta: `2τ`. 
4. Multiplicar: `L_min = R * 2τ`. 
5. Convertir bits a bytes.
6. Redondear hacia arriba si hace falta.
7. Explicar por qué una trama menor no sirve.
#### Ejemplo clásico de Ethernet
Supongamos Ethernet de `10 Mbit/s` con tiempo de ida y vuelta máximo de `50 us`.
```
L_min = 10,000,000 bit/s * 50 * 10^(-6) s
```

```
L_min = 500 bits = 62.5 bytes
```
Ethernet fija un mínimo práctico de **64 bytes** desde dirección destino hasta checksum.
### Half-duplex vs full-duplex
* **Half-duplex**:
	* El enlace puede usarse en ambos sentidos, pero no al mismo tiempo. Si dos estaciones transmiten simultáneamente en un medio compartido, puede haber colisión.
* **Full-duplex**:
	* Ambos extremos pueden transmitir y recibir al mismo tiempo. En un enlace punto a punto con switch no hay contención CSMA/CD entre esos dos extremos.
**Regla Práctica**: **CSMA/CD pertenece al mundo half-duplex compartido**. En Ethernet conmutada full-duplex, cada enlace es punto a punto y las colisiones del medio compartido desaparecen.
### ¿Qué pasa al aumentar la velocidad?
Si la distancia máxima no cambia:
```
R sube -> L-min sube
```
Por eso, al escalar Ethernet: 
* se redujeron longitudes máximas en medio compartido;
* se favorecieron enlaces punto a punto;
* se volvió dominante Ethernet conmutada full-duplex;
* CSMA/CD quedó principalmente como concepto histórico y de examen.
### Backoff: Regla conceptual
Después de la colisión `i`, Ethernet elige aleatoriamente en un rango que crece como potencia de 2.
```
K en {0, 1, ..., 2^(i)-1}
```
En la práctica se limita el crecimiento y se descarta la trama tras demasiados intentos.
### Error típico
>[!bug] Confundir tiempo de transmisión con tiempo de propagación.
* **transmisión**: cuánto tarda el emisor en poner todos los bits en el medio;
* **propagación**: cuánto tarda una señal en viajar por el medio.
En CSMA/CD, la condición mezcla ambos: la transmisión debe durar al menos una ida y vuelta de propagación. 
## Ethernet
### Ethernet y 802.3
Ethernet/IEEE 802.3 define: 
* formato de trama;
* direcciones MAC;
* operación LAN;
* MAC común para muchas velocidades;
* operación half-duplex con CSMA/CD;
* operación full-duplex en enlaces conmutados. 
En la práctica actual, la Ethernet cotidiana es conmutada y full-duplex.
### Formato de tramas Ethernet
![](Screenshot_2026-05-28-23-57-37_16679.png)
**Campos importantes**: 
* dirección destino; 
* dirección origen;
* tipo o longitud;
* datos; 
* relleno si hace falta;
* checksum/FCS.
### Dirección MAC
Una dirección MAC Ethernet clásica tiene 48 bits:
```
6 bytes = 6 pares hexadecimales
```
Por ejemplo: `08:00:2b:4c:59:23`. Se usa para entrega local en la LAN, no para enrutamiento global.
### Campo tipo
El campo Tipo le dice al receptor qué protocolo de capa superior debe recibir la carga útil.
Ejemplos conceptuales: IPv4; IPv6; ARP.
>[!tip] Sin este campo, el sistema operativo no sabría a qué módulo entregarle los datos
### Longitud Mínima y relleno
Ethernet exige una trama mínima de 64 bytes desde dirección destino hasta FCS. Si los datos son pocos:
```
datos + encabezado + FCS < 64 B -> agregar pad
```
El relleno no es información de aplicación. Es un requisito de la tecnología de enlace.
### Hubs vs switches
* **Hub**:
	* Repite señales. Todos comparten dominio de colisión. Se parece al medio compartido clásico.
* **Switch**:
	* Almacena y reenvía tramas. Aprende MACs. Reduce colisiones y permite transmisiones en paralelo.
### Ethernet conmutada
* **Hub: capa física**:
	* Repite señales eléctricas/ópticas. No mira tramas ni direcciones MAC.
* **Switch: capa de enlace**:
	* Mira direcciones MAC y decide por qué puerto reenviar cada trama
### Tabla de aprendizaje
Cada entrada tiene:

| MAC | Puerto | Tiempo/estado           |
| :-- | -----: | :---------------------- |
| A   |      1 | aprendida recientemente |
| C   |      3 | aprendida recientemente |
La tabla se aprende dinámicamente observando la **MAC origen** de cada trama entrante.
### Reglas de un switch aprendiz
Cuando llega una trama por un puerto:
1. Aprende o actualiza: `MAC_origen -> puerto_de_entrada`.
2. Busca `MAC_destino`. 
3. Si la conoce, reenvía solo por ese puerto. 
4. Si no la conoce, inunda por todos excepto el puerto de entrada.
### Flooding no es broadcast
* Flooding: 
	* lo decide el switch;
	* ocurre cuando no conoce el destino;
	* se envía por varios puertos para descubrir dónde está.
* Broadcast:
	* lo pide la trama;
	* el destino es una dirección especial;
	* todos deben recibirla dentro del dominio correspondiente.
### Switches interconectados
Los switches también aprenden por MAC origen aunque estén conectados entre sí. La regla básica es la misma, solo que una entrada puede apuntar hacia otro switch.
## Ethernet Hoy
### ¿Qué sigue vigente?
Para **ejercicios**, CSMA/CD sigue siendo muy importante porque enseña: 
* retardo de propagación;
* tiempo de transmisión;
* colisiones; 
* tamaño mínimo de trama;
* diferencia entre medio compartido y enlace punto a punto.
Para redes modernas, lo común es:
* switches;
* enlaces full-duplex;
* autonegociación;
* velocidades de 1G, 2.5G, 5G, 10G y superiores; 
* fibra y cobre según distancia/costo.
### Ethernet en estándares actuales
Según IEEE 802.3-2022, Ethernet especifica operación LAN con velocidades seleccionadas desde `1 Mbit/s` hasta `400 Gbit/s`, usando una MAC común y múltiples PHY. 
Además: 
* CSMA/CD queda especificado para operación compartida half-duplex;
* full-duplex es central en redes conmutadas;
* repetidores se contemplan para redes compartidas hasta ciertas velocidades.
### Datacenters y AI/HPC
En datacenters modernos, Ethernet compite y convive con tecnologías de muy alta velocidad.
Tendencias relevantes:
* 100G/400G ampliamente usados;
* 800G en despliegue e interoperabilidad multivendor; 
* 1.6T en estandarización; 
* mucha atención a latencia, FEC, ópticas, consumo y cableado. 
>[!tip] Aunque el examen se centre en CSMA/CD y switches, el principio sigue: la capa de enlace decide cómo se entrega una trama en un medio concreto.
## ¡¡¡¡VER EJERCICIOS MODELOS!!!!
# Parte 2
## Redes inalámbricas 
### Dos organizaciones posibles
* Red ad hoc
	* No hay estación base. Los nodos se comunican directamente con otros dentro de alcance. 
* Red con infraestructura
	* Los hosts se asocian a un access point (AP), que conecta con la red cableada o con otros AP.
#### Red ad hoc
En una red ad hoc: 
* cada nodo tiene alcance limitado;
* no hay AP coordinando;
* los nodos compiten por el medio;
* si hace falta llegar más lejos, se necesitan rutas multi-salto;
* el modo distribuido es natural.
>[!tip] DCF (Distributed Coordination Function) está pensado como mecanismo distribuido: todos siguen reglas comunes para competir por el canal. 
#### Red con infraestructura
En infraestructura: 
* cada host se asocia a un AP; 
* el AP conecta la celda inalámbrica con un sistema de distribución; 
* los AP vecinos idealmente usan canales no conflictivos;
* el AP puede coordinar ciertos períodos de transmisión;
* la movilidad requiere asociación y reasociación. 
### BSS, ESS y sistema de destribución
* BSS:
	* una celda 802.11. En infraestructura está formada por un **AP** y las estaciones asociadas a ese AP. Comparten un medio inalámbrico y compiten por ese canal.
* ESS
	* varios BSS unidos por un **sistema de distribución** (DS), normalmente una red cableada con switches. Hacia el usuario puede verse como una misma WLAN, por ejemplo con el mismo SSID.
#### Vocabulario mínimo
| Término | Significado |
| :--- | :--- |
| STA | estación inalámbrica |
| AP | punto de acceso |
| BSS | conjunto básico de servicio |
| ESS | varios BSS conectados por un sistema de distribución |
| DS | sistema de distribución |
| portal | integración hacia una LAN no 802.11 |
### Asociación
Antes de enviar o recibir datos de capa de red, una estación debe asociarse con un AP. Servicios relacionados: 
* **asociación**: establece la relación inicial; 
* **reasociación**: transfiere la estación a otro AP;
* **desasociación**: finaliza la relación.
Esto aparece en teoría como movilidad, handoff y elección de AP.
#### Escaneo Activo
![](Screenshot_2026-05-29-10-20-06_23426.png)
En escaneo activo, la estación envía tramas de prueba y los AP en alcance responden. Luego la estación elige AP y pide asociación.
#### Escaneo pasivo
![](Screenshot_2026-05-29-10-20-37_8382.png)
En escaneo pasivo, los AP emiten beacons periódicos. La estación escucha, compara información y decide si asociarse o reasociarse.
## ¿Por qué WiFi no usa CSMA/CD?
### Idea de CSMA/CD no alcanza
CSMA/CD requiere que el transmisor pueda escuchar el medio mientras transmite. En WiFi esto es difícil porque: 
* la señal propia transmitida es muchísimo más fuerte que una señal recibida;
* el circuito receptor se satura; 
* las estaciones pueden tener visiones distintas del canal;
* dos transmisores que no se escuchan pueden interferir en un receptor común.
Hay hardware para separar transmisión y recepción, pero no elimina el problema general de WiFi: una cosa es transmitir y recibir en **frecuencias distintas**; otra mucho más difícil es detectar una colisión débil en el **mismo canal** mientras estoy transmitiendo.
#### ¿Y los duplexores o diplexores?
* Diplexor: 
	* combina o separa bandas/frecuencias distintas sobre una misma antena o cable.
* Duplexor: 
	* permite transmitir y recibir a la vez cuando el sistema usa frecuencias separadas, como en FDD: duplexación por división de frecuencia.
Se usan cuando el costo y el tamaño se justifican: redes celulares FDD, estaciones base, repetidoras, enlaces punto a punto, satélite y algunos sistemas de radio especializados.
En 802.11 típico no alcanza con poner un filtro: las estaciones comparten el mismo canal y CSMA/CD debería detectar interferencia en ese canal durante la propia transmisión. Además, el problema de estación oculta (discutido luego) sigue existiendo: la colisión puede ocurrir en el receptor aunque el transmisor no la perciba.
#### Consecuencia
En Ethernet cableada clásica.
```
escucho mientras transmito -> detecto colisión
```
En WiFi;
```
escucho antes, espero, uso ACK -> intento evitar colisión
```
Por eso hablamos de **CSMA/CA**: Collision Avoidance.
### Detección de portadora física y virtual
802.11 combina: 
* detección física: medir si el medio parece ocupado;
* detección virtual: NAV, un temporizador que dice “alguien reservó el canal”;
* ACKs: confirmar que el receptor recibió;
* backoff: separar intentos que podrían colisionar.
### Estación oculta
Situación típica:
![](Screenshot_2026-05-29-10-26-00_28027.png)
Si `C` transmite a `B` y `A` no escucha a `C`, `A` puede creer que el canal está libre y transmitir también a `B`. La colisión ocurre en el receptor `B`.
>[!bug] La colisión ocurre en el receptor `B`
#### ¿Cómo reconocer estación oculta?
Busca esta estructura: 
* dos posibles transmisores no se escuchan entre sí;
* ambos pueden interferir en el mismo receptor;
* el problema se descubre mirando el alcance de recepción, no solo quién transmite.
### Estación expuesta
Situación típica:
![](Screenshot_2026-05-29-10-28-16_19920.png)
* Pero `C -> D` no interfiere con `A`.
`C` se abstiene de transmitir porque escucha a `B`, aunque `C -> D` podría haber sido seguro.
#### ¿Cómo reconocer estación expuesta?
Buscá esta estructura:
* una estación escucha una transmisión cercana; 
* por escucharla, se calla;
* pero su receptor estaría suficientemente lejos del receptor actual;
* la transmisión simultánea podría no causar colisión. 
>[!tip] La estación expuesta reduce reutilización espacial: se desperdicia una oportunidad de transmitir.
#### Oculta vs expuesta
| Problema | Qué sale mal | Efecto |
| :--- | :--- | :--- |
| Oculta | alguien transmite porque no escucha a otro transmisor | colisión en el receptor |
| Expuesta | alguien no transmite porque escucha una transmisión que no le impediría comunicarse | subutilización del canal |
>[!tip] La oculta causa daño directo; la expuesta causa prudencia excesiva.
## DCF: Distributed Coordination Function
#### DCF en una frase
DCF es el modo distribuido de 802.11: 
* todos los nodos siguen el mismo protocolo;
* se usa contención por el canal;
* se espera DIFS (DCF Interframe Space) antes de iniciar un diálogo;
* se usa backoff aleatorio; 
* opcionalmente se usa RTS/CTS (Request-to-Send/Clear-to-Send);
* los ACKs van después de SIFS (Short Interframe Space). 
En DCF, **contención** quiere decir que las estaciones compiten por iniciar el próximo diálogo. Esperan DIFS, aplican backoff aleatorio y transmiten cuando su contador llega a cero. Si dos llegan juntas, puede haber colisión.
### Secuencia RTS/CTS/DATA/ACK
![](Screenshot_2026-05-29-10-41-46_13902.png)
La conversación básica:
```
RTS -> CTS -> DATA -> ACK
```
### ¿Qué hace cada trama?
| Trama | Quién la envía | Para qué sirve |
| :--- | :--- | :--- |
| RTS | emisor | solicita reservar el canal |
| CTS | receptor | concede y avisa a vecinos del receptor |
| DATA | emisor | transporta carga útil |
| ACK | receptor | confirma recepción correcta |
### NAV
NAV significa Network Allocation Vector. 
Es un temporizador local:
```
NAV > 0 -> me quedo callado
```
Los nodos cercanos al emisor o receptor escuchan RTS/CTS y estiman cuánto durará el intercambio.
### DCF con nodos vecinos
![468](Screenshot_2026-05-29-10-47-05_25098.png)
>[!tip] El **RTS** puede ser escuchado por vecinos del emisor. El **CTS** puede ser escuchado por vecinos del receptor. Ambos ayudan a reservar el medio.
### RTS/CTS y estación oculta
RTS/CTS ayuda especialmente con estación oculta: 
* el nodo oculto quizá no escucha al emisor;
* pero puede escuchar el CTS del receptor;
* al escuchar CTS, configura NAV; 
* evita transmitir durante el intercambio.
>[!quote] Ayuda, pero no vuelve imposible toda colisión. Los RTS mismos pueden colisionar.
### Colisiones en DCF
Dos nodos pueden: 
* detectar el canal libre;
* elegir backoffs que terminan al mismo tiempo;
* enviar RTS simultáneamente; 
* hacer que los RTS colisionen.
Si no llega CTS, el emisor asume falla y reintenta con backoff.
### Backoff en WiFi
El backoff es una **cuenta regresiva aleatoria en ranuras temporales** (slots), usada para separar estaciones que quieren transmitir después de que el canal quedó libre.
```
backoff = k * SlotTime, con 'k' elegido en [0, CW]
```
Proceso: 
1. la estación espera que el medio esté libre durante DIFS;
2. elige k al azar dentro de la ventana de contención CW;
3. resta 1 por cada slot libre;
4. si el canal se ocupa, congela el contador; 
5. cuando vuelve a estar libre, espera DIFS y continúa desde el valor congelado; 
6. transmite cuando el contador llega a 0.
#### Ejemplo mínimo
Una estación eligió `k = 4`
![](Screenshot_2026-05-29-10-53-17_22262.png)
>[!tip] La estación no vuelve a sortear mientras conserva el turno parcial ganado. Si la transmisión falla, aumenta la ventana de contención para reducir la probabilidad de repetir la colisión.
## SIFS, PIFS y DIFS
### ¿Por qué hay espacios entre tramas?
Los espacios entre tramas dan prioridad temporal.
```
SIFS < PIFS < DIFS
```
Quien debe responder dentro de un diálogo usa SIFS (Short IFS). El AP en PCF usa PIFS (PCF IFS). Las estaciones que compiten en DCF esperan DIFS (DCF IFS).
#### SIFS
SIFS es el espacio más corto.
Se usa para respuestas inmediatas dentro del mismo diálogo: 
* CTS después de RTS;
* DATA después de CTS;
* ACK después de DATA;
* fragmentos dentro de una ráfaga. 
>[!data] Después de SIFS solo hay una estación que debería responder, por eso se le da prioridad.
#### DIFS
DIFS lo usan estaciones que quieren empezar una nueva contienda DCF. 
Proceso típico: 
1. medio libre;
2. esperar DIFS;
3. ejecutar o continuar backoff; 
4. transmitir si el contador llega a cero. 
>[!warning] En cálculos de tasa efectiva, si el ejercicio no da backoff, se deja simbólico o se aclara que se ignora.
#### PIFS
PIFS está entre SIFS y DIFS. 
Lo usa el AP para tener prioridad al iniciar o continuar un período PCF libre de contención.
```
SIFS < PIFS < DIFS
```
Así el AP puede tomar el canal antes que estaciones DCF que esperan DIFS.
#### Valores del material original
En los ejercicios de las slides se usan

| Intervalo |  Valor |
| :-------- | -----: |
| SIFS      |  28 us |
| PIFS      |  78 us |
| DIFS      | 128 us |
>[!warning] Estos valores pueden variar por estándar/PHY, pero en examen se usan los dados por el enunciado (no hay que aprenderlos de memoria).
## PCF: Point Coordination Function
### PCF en una frase
PCF es un modo coordinado por el AP: 
* el AP toma control del medio;
* inicia un período sin contención;
* sondea estaciones con polling;
* las estaciones transmiten si son invitadas;
* se reducen o eliminan colisiones dentro del período coordinado.
### Período libre de contención
![](Screenshot_2026-05-29-11-01-48_7304.png)
El AP anuncia el período, fija NAV en estaciones y coordina quién transmite.
### Beacon y Polling
El AP puede: 
* transmitir un beacon;
* indicar duración del período libre de contención;
* enviar CF-Poll a una estación;
* recibir datos o trama nula;
* continuar con otra estación;
* terminar con CF-End.
### DCF y PCF
| Característica    | DCF                      | PCF                             |
| :---------------- | :----------------------- | :------------------------------ |
| Control           | distribuido              | AP coordinador                  |
| Acceso            | contención               | sondeo                          |
| Colisiones        | posibles                 | evitadas dentro del período PCF |
| Topología natural | ad hoc o infraestructura | infraestructura                 |
| Intervalo clave   | DIFS                     | PIFS                            |
## WiFi hoy
### Qué cambió y qué no
Cambió mucho: 
* tasas físicas más altas;
* MIMO, OFDMA, canales más anchos;
* 6 GHz y WiFi 6E/7;
* multi-link operation;
* mejoras de latencia, jitter y eficiencia;
* seguridad y privacidad más complejas. 
No cambió lo esencial para esta unidad:
```
El medio se comparte y el overhead importa
```
### 802.11be / WiFi 7
IEEE 802.11be-2024, asociado comercialmente con WiFi 7, define mejoras Extremely High Throughput.
Puntos relevantes: 
* al menos un modo con throughput máximo de al menos 30 Gbit/s en el SAP MAC;
* operación entre 1 y 7.250 GHz;
* compatibilidad y coexistencia con dispositivos 802.11 previos;
* mejoras de latencia y jitter en el peor caso.
## Tasa efectiva en 802.11
### Tasa física vs tasa efectiva
La tasa física dice a qué velocidad se modulan bits en el medio.
La tasa efectiva pregunta: 
```
bits útiles de datos / tiempo total observado
```
El denominador incluye overhead: 
* RTS, CTS, ACK; 
* SIFS, DIFS, PIFS;
* beacon, poll;
* backoff si el enunciado lo da;
* retransmisiones si ocurren. 
### Método para tasa efectiva
1. Dibujar la línea de tiempo. 
2. Separar tramas de control y datos.
3. Calcular duración de cada trama: `bits / tasa`.
4. Sumar intervalos SIFS/DIFS/PIFS dados.
5. Definir claramente qué cuenta como datos útiles.
6. Calcular `R_eff = bits_utiles / tiempo_total`.
### Cuidado con tasas distintas
En muchos ejercicios: 
* RTS, CTS y ACK usan tasa de control; 
* DATA usa tasa de datos;
* las tramas de control suelen ir más lento para ser más robustas;
* los intervalos entre tramas no dependen del tamaño de datos.
>[!warning] Error típico: Calcular todo a la tasa de datos y olvidar SIFS/DIFS.
### Línea de tiempo modelo DCF
![](Screenshot_2026-05-29-11-12-30_17895.png)
Dos conversaciones: 
1. `B -> D` 
2. `E -> D` 
Se usan RTS/CTS/DATA/ACK y un DIFS entre diálogos.
### Datos de ejercicios
| Dato | Valor |
| :--- | ---: |
| Tasa control | 6 Mbit/s |
| Tasa datos | 12 Mbit/s |
| RTS | 20 B |
| CTS | 14 B |
| ACK | 14 B |
| DATA | 1500 B |
| SIFS | 28 us |
| DIFS | 128 us |
No consideramos backoff porque el enunciado no lo da y asume que B gana la disputa inicial. 
#### Duración de tramas
* ```
  T_RTS = 20*8 / 6 Mbit/s = 26.7 us
  ```
* ```
  T_CTS = T_ACK = 14*8 / 6 Mbit/s = 18.7 us
```
* ```
  T_DATA = 1500*8 / 12 Mbit/s = 1000 us
```
#### Tiempo de un diálogo DCF
Un diálogo:
```
RTS + SIFS + CTS + SIFS + DATA + SIFS + ACK
```
Números:
```
26.7 + 28 + 18.7 + 28 + 1000 + 28 + 18.7 = 1148.1 us
```
#### Tiempo total DCF
Dos diálogos y un DIFS entre ellos:
```
T_total = 2 * 1148.1 + 128 = 2424.2 us
```
Datos útiles
```
2 * 1500 B * 8 = 24000 bits
```
#### Tasa efectiva DCF
```
R_eff = 24000 bits / 0.0024242 s = 9.90 Mbit/s
```
Aunque la tasa física de datos es 12 Mbit/s, la efectiva baja por overhead. 
Cada usuario que transmitió una sola trama ve aproximadamente la mitad:
```
9.90 / 2 = 4.95 Mbit/s
```
### Línea de tiempo modelo PCF
![](Screenshot_2026-05-29-11-18-10_22302.png)
PCF reemplaza RTS/CTS por beacon y polling coordinado por el AP.
#### Datos extra PCF
| Dato | Valor |
| :--- | ---: |
| Beacon | 100 B |
| Poll | 20 B |
| PIFS | 78 us |
Se mantienen: 
* tasa control `6 Mbit/s`; 
* tasa datos `12 Mbit/s`;
* DATA `1500 B`;
* ACK `14 B`; 
* SIFS `28 us`.
#### Duración de tramas PCF
* ```
  T_beacon = 100*8 / 6 Mbit/s = 133.3 us
```
* ```
  T_poll = 20*8 / 6 Mbit/s = 26.7 us
```
* ```
  T_ACK = 18.7 us, T_DATA = 1000 us
```
#### Tiempo total
Secuencia:
* ```
	Beacon + SIFS + Poll + SIFS + DATA + SIFS + ACK + PIFS + Poll + SIFS + DATA + SIFS + ACK
```
* ```
  T_total = 2450.8 us
```
#### Tasa efectiva PCF
```
R_eff = 24000 bits / 0.0024508 s = 9.79 Mbit/s
```
Con dos tramas, DCF y PCF dan valores similares. Con más tráfico, el beacon se amortiza y el poll puede ser más barato que RTS+CTS.
## VER EJERCICIOS MODELO
