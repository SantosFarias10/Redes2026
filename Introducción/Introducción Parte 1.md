# Nivel Físico
Este bloque se ocupa del nivel más fundamental: cómo viaja la información por un medio físico.
Este bloque responde a la pregunta ¿Cómo transformamos bits en algo que pueda viajar por un cable, por el aire o por la luz?
## Señales
- Señales analógicas y digitales:
  Representación de señales como funciones del tiempo
![](Screenshot_2026-06-21-09-58-37_28299.png)
-  Ondas sinusoidales 
  $s(t) = A \ sin(2 \pi ft + \phi)$ , t número real.
  Propiedades de las ondas sinusoidales: **frecuencia, amplitud, y fase**
![](Screenshot_2026-06-21-10-00-22_20960.png)
## Medios Físicos 
¿Por dónde viajan las señales? Por medios físicos, que se clasifican en:
- medios guiados:
	- cable de cobre: hay dos tipos de cable de cobre: cables de par trenzado de cobre y cable coaxial .
	- Fibra Óptica.
![](Screenshot_2026-06-21-10-02-59_14064.png)
- medios no guiados :
	* como radios o microondas
- medios magnéticos:
	* DVD's, Blu-ray, cintas magnéticas

Por ejemplo, la radio es una señal transportada en el espectro electromagnético, no se usan cables físicos, bidireccional. Efectos de la propagación en el entorno: reflexión, obstrucción por objetos, interferencia. 
### Los tipos de enlace de radio son los siguientes:
- Microondas terrestres.
- LAN (por ejemplo, WiFi).
- área amplia (Por ejemplo celular).
- satélite.
## Degradación de una señal
El transmitir señales por medios físicos nos lleva al siguiente problema: la degradación de las señales. Estudiaremos las distintas formas en que esta puede ocurrir.
- **Interferencia**:
	* Se refiere a perturbaciones externas que afectan la transmisión de la señal, puede ser interferencia electromagnética (EMI) causada por motores, radios, microonda, etc. Es cuando señales de un canal se mezclan con otro.
	* Su consecuencia es la degradación de la señal y posibles errores en la comunicación.
- **Ruido:**
	* Es cualquier señal no deseada que se mezcla con la señal útil durante la transmisión, puede ser causado por interferencias electromagnéticas, variaciones térmicas o equipos eléctricos cercanos.
	* Su efecto es la distorsión o pérdida de calidad en la comunicación, aumentando la probabilidad de errores en los datos.
![](Screenshot_2026-06-21-10-15-04_4959.png)
- **Atenuación**: tipos de enlace de radio
	* Es la pérdida de intensidad de la señal conforme viaja a través del medio de transmisión (por ejemplo: cable, fibra óptica). Las señales digitales sufren más de atenuación que las señales analógicas. A frecuencias mayores los pulsos se tornan más redondeados y pequeños.
![](Screenshot_2026-06-21-10-16-09_14773.png)
## Codificación y decodificación de señales
El siguiente paso lógico es preguntarnos qué se hace para pasar de secuencias de bits a señales físicas y recíprocamente.
* **Codificación**
	* Es el proceso de transformar la secuencia de bits (0 y 1) en una señal física que pueda transmitirse por el medio. Se representan los bits en forma de pulsos eléctricos, variaciones de voltaje, ondas de luz o radiofrecuencia.
	* El objetivo: asegurar que la señal sea interpretada correctamente por el receptor y que se minimicen errores durante la transmisión. 
* **Decodificación**
	* Es el proceso inverso; recibir la señal física y convertirla nuevamente en la secuencia original de bits (0 y 1). El receptor aplica el mismo esquema de codificación que usó el transmisor para interpretar correctamente la señal.
	* Si la señal llega afectada por ruido, atenuación o interferencia, la decodificación puede ser más difícil y requerir técnicas adicionales de corrección de errores.
## Modulación 
Las computadoras procesan y generan información en forma de señales digitales (secuencias de ceros y unos). Sin embargo, no todos los medios físicos de transmisión pueden transportar directamente esas señales digitales. 

*Modulación:* El proceso de modulación sirve para convertir la señal digital en una señal analógica, lo que permite que la información viaje de manera eficiente y confiable a través del medio que no permite señales digitales.
![](Screenshot_2026-06-21-10-21-00_10271.png)
## Multiplexado 
Modulación nos permite adaptar una señal al medio. Pero en redes se exige algo más: aprovechar al máximo ese medio. Para resolver esto introducimos el mecanismo de multiplexado.

*Multiplexado:* Consiste en combinar dos o más señales independientes para transmitirlas simultáneamente a través de un mismo medio físico. En el receptor, se aplica el proceso inverso llamado **demultiplexado**, que separa las señales originales
![](Screenshot_2026-06-21-10-22-25_28516.png)
* Si queremos mandar varias señales digitales por un mismo canal, usamos **multiplexado por división de tiempo:** varios emisores comparten un mismo medio físico enviando sus señales en distintos intervalos de tiempo, de modo que cada uno transmite solo en su propio turno.
* Esto es usado en redes telefónicas, redes troncales de fibra óptica, redes de celulares (GSM), redes de cable, en redes satelitales.
![](Screenshot_2026-06-21-10-24-00_29309.png)
* Si queremos multiplexar varias señales analógicas, utilizamos **multiplexado por división de frecuencia (FDM):** varios emisores comparten un mismo medio físico asignando a cada uno una banda de frecuencias distinta, de modo que todas las señales viajan simultáneamente sin interferirse.
* Esto es usado en cables de cobre y canales inalámbricos. En redes telefónicas, de celulares, de cable y satelitales.
![](Screenshot_2026-06-21-10-25-59_13180.png)
# Conexión Directa entre Máquinas
Este bloque responde a ¿Cómo logramos que varias máquinas compartan un medio y se comuniquen de forma confiable?
## Conexiones punto a punto
Hay conexión punto a punto cuando, dos dispositivos  están conectados directamente entre sí, sin intermediarios ni compartición del medio con otros usuarios. Por ejemplo, dos máquinas conectadas por un cable.

Si tenemos `n` máquinas y las queremos conectar entre si, usando conexiones punto a punto usando cables, necesitaríamos $\frac{n*(n-1)}{2}$ cables y cada máquina necesitaría `n` puertos para enchufar esos cables. 

>[!tip] Claramente no es escalable.
## Canales de difusión 
* ¿Cómo crear un medio compartido? 
	- Si lo queremos hacer físicamente usando cables, podemos usar un Hub (como en los cibers).
![](Screenshot_2026-06-21-10-30-33_13091.png)
- Si lo queremos hacer inalámbricamente, por ondas de radio, podemos usar WiFi, o redes móviles ad hoc.

A los **medios compartidos se los llama canales de difusión**, sin embargo, usar un medio compartido trae nuevos problemas como: si varias máquinas transmiten al mismo tiempo, sus señales se superponen y se destruyen, a eso llamamos **colisión**

#### Tenemos dos soluciones propuestas para las colisiones:
1. Si dos máquinas colisionan entonces esperan un tiempo aleatorio (diferente) cada una y pasado ese tiempo retransmiten (usada en Ethernet).
2. Una máquina coordina el orden de transmisión de las demás máquinas (usada en WiFi).
## Control de errores
Las colisiones son solo una de las formas en que un mensaje puede dañarse. Incluso si evitamos o controlamos las colisiones, el medio físico sigue introduciendo ruido, interferencia y atenuación. Por eso necesitamos mecanismos que permitan detectar si un mensaje llegó con errores.

Supongamos que mandamos un mensaje entre dos computadoras, y pasa por 10 computadoras antes de llegar a destino, y en el medio el mensaje es alterado. Para poder detectar que es un mensaje errado, ambos emisor y receptor acuerdan usar una función `f` que aplicada al mensaje da una secuencia de bits. Si el mensaje a mandar es `M`, se envía entonces `M ++ f(M)`.
Si `M` fue alterado a `M'`, el receptor va a recibir `M'++f(M)`, el receptor va a calcular `f(M')` y va a descubrir que no concuerda con `f(M)`, entonces se va a dar cuenta que el mensaje recibido contiene error.

>[!tip] La alteración puede ser causada por ruido.
## Comunicación confiable
Saber que un mensaje llegó dañado es solo la mitad del trabajo. La otra mitad es lograr que el mensaje correcto llegue finalmente al destino. Ese es el objetivo de la **comunicación confiable**.

Supongamos que un mensaje se recibió con error; ideas de cómo hacer para recibirlo de nuevo:
1. El receptor envía un mensaje NAK (*Negative Acknowledgement*) con el número de mensaje que se recibió mal. Luego, el emisor lo manda de nuevo. El receptor manda ACK con número de mensaje que recibió bien.
2. El emisor manda mensaje y dispara un temporizador; si el temporizador del mensaje expira, significa que el mensaje llegó mal o se perdió y hay que reenviarlo.
### Fragmentación de mensajes
Si el mensaje dañado es muy largo, no conviene retransmitirlo todo, es re costoso.
Debemos dividir el mensaje en tramas, entonces, en lugar de mandar el mensaje entero se mandan tramas. Si una trama llega dañada, entonces se retransmite.
Para esto, es necesario la numeración de tramas y reensamblado (en ese orden), confirmación de tramas buenas recibidas, y retransmisión de tramas dañadas.
## Repetidores 
Debemos resolver como hacer que la señal llegue lo suficientemente lejos como para que los mecanismos para tratar con mensajes dañados tengan sentido. 

Para extender el alcance de la red usamos repetidores. Un repetidor es un dispositivo que recibe, amplifica (regenera) y retransmite señales en ambas direcciones. Los repetidores introducen un retardo.
Para permitir redes mayores que un segmento, conectar multiples cables mediante repetidores.
![](Screenshot_2026-06-21-10-37-51_23181.png)
## Conmutadores
Cuanto más grande es el medio compartido, más colisiones hay. Para que la red escale sin que las colisiones exploten, necesitamos algo más inteligente que un repetidor.

Usando repetidores, todos los segmentos de cable forman un único canal. Entonces la probabilidad de colisiones aumenta. 
Por lo tanto usamos **conmutadores** (*switches*).

Tenemos varios dominios de colisiones y aumentamos mucho la velocidad para mandar de una máquina de un dominio de colisiones a una máquina en otro dominio de colisiones
![](Screenshot_2026-06-21-10-39-29_2278.png)
Hay dos opciones: 
- Cada **tarjeta** es un dominio de colisiones (todas las máquinas conectadas en la tarjeta están conectadas directamente entre sí)
- Cada **puerto** es un dominio de colisiones
## Redes de área local (LAN)
Los repetidores extienden el alcance y los conmutadores segmentan el tráfico. Los canales de difusión permiten compartir el medio. La combinación de estos elementos define una red organizada dentro de un área limitada: una **red de área local** (LAN).

Las redes de área local suelen cubrir el área de una casa, edificio o hasta un campus. 

Ejemplos de redes de área local:
- Un canal de difusión con computadoras conectadas.
- Un conjunto de canales de difusión interconectados usando repetidores.
- Un conjunto de canales de difusión interconectados usando conmutadores .
![599](Screenshot_2026-06-21-10-43-52_29152.png)
![600](Screenshot_2026-06-21-10-44-25_18922.png)
![](Screenshot_2026-06-21-10-44-55_10093.png)