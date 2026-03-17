# Bloque 1
Este bloque se ocupa del nivel mas fundamenta, como viaja la información por un **Medio Físico**. 

---
## Señales

### Señales Analógicas.

![Señales Analogicas](SeñalesAnalogicas.png)

### Ondas Sinusoidales

$s(t) = A.sin(2\pi f t + \theta)$, $t$ numero real.

### Señales Digitales.

![](SeñalesDigitales.png)

#### ¿Cómo se representan los 0 y un 1 con una señal digital?

![](Representacion0y1.png)

---
## Medios Físicos
Es por donde viajan las señales.

### Los medios físicos se clasifican en:
* Medios Guiados: Cable de Cobre, Fibra Óptica.
* **Medios no Guiados**: Radio, Microondas.
* **Medios Magnéticos**: DVD, Blu-ray, cintas magnéticas.
	* Cables de Cobre
---
#### Existen 2 Tipos de Cable de Cobre:
* Cables de Par Trenzado de Cobre.
![](ParTrenzado.png)

* Cable Coaxial.
![](CableCoaxial.png)

---
**Radio**: La Señal es transportada en el espectro electromagnético. No se usan cables Físicos. Es Bidireccional.

#### Tipos de Enlace de Radio
* Microondas Terrestres.
* LAN.
* WAN.
* Satélite.

---
## Degradación de una Señal
El transmitir señales por medios físicos nos lleva al siguiente problema: La **Degradación de las Señales**. Pueden ocurrir:
* **Interferencias**: Se refiere a perturbaciones externas que afectan la transmisión de la señal. Puede ser **Interferencia Electromagnética** (**EMI**). Lo que causa la interferencia es la degradación de la señal y posibles errores en la comunicación.
* **Ruido**: Es cualquier señal no deseada que se mezcla con la señal útil durante la transmisión. Es causado por EMI, variaciones térmicas o equipos cercanos. Lo que causa es la **Distorsión** o perdida de calidad en la comunicación, aumentando la probabilidad de errores en los datos. El grafico con ruido:
![](GraficoConRuido.png)
* **Atenuación**: Es la **Perdida de Intensidad** de la señal conforme viaja a través del media de transmisión (por ejemplo: cable, Fibra Óptica). Por ejemplo, en un cable largo, la señal llega mas débil al receptor que como salió del transmisor. Las señales digitales sufren mas de atenuación que las señales analógicas, a frecuencias mayores los pulsos se tornan mas redondeados y pequeños.
![](Atenuacion.png)

---
## Codificación y Decodificación de Señales
Que se hace para pasar de secuencias de bits a señales físicas y recíprocamente.
### Codificación
Es el proceso de **Transformar la Secuencia de Bits** (**0 y 1**) en una señal física que pueda transmitirse por el medio, se representan los bits en forma de pulsos electrónicos, variaciones de voltaje, ondas de luz o radiofrecuencia.
El objetivo es asegurar que la señal sea **Interpretada Correctamente** por el receptor y que se minimicen errores durante la transmisión.
### Decodificación
Es el proceso inverso xd: **Recibir la Señal Física** y convertirla nuevamente en la secuencia original de bits (0 y 1), el receptor aplica el mismo esquema de codificación que uso el transmisor para interpretar correctamente la señal.

---
## Modulación
Las computadoras procesan y generan información en forma de **Señales Digitales** (secuencias de ceros y unos). Sin embargo, no todos los medios físicos de transmisión, como el aire de comunicaciones inalámbricas o ciertos tipos de cableado, pueden transportar directamente esas señales digitales. Para resolver esto se introduce un nuevo mecanismo: La **Modulación**.
El proceso de modulación sirve para **Convertir la Señal Digital en una Señal Analógica**, lo que permite que la información viaje de manera eficiente y confiable a través del medio que no permite señales digitales.
Se asume que un cero se representa con un nivel de voltaje mas bajo que el nivel de voltaje usado para representar un uno. ¿Cómo modular una señal digital usando esta relación entre niveles de voltaje? Asumimos que podemos **Introducir Variaciones** en distintas propiedades de una onda sinusoidal que sirve como señal base para representar los niveles de voltaje asociados a los bits 0 y 1.
+ Las propiedades que pueden modificarse son su amplitud, su frecuencia o su fase. Por ejemplo: Una idea posible seria distinguir un 0 de un 1 cambiando la amplitud de la onda.

---
## Multiplexado

Modulación nos permite adaptar una señal al medio. Pero en redes se exige aprovechar al máximo ese medio. Para resolver esto introducimos el mecanismo de **Multiplexado**.
Consiste en **Combinar Dos o Mas Señales Independientes** para transmitirlas simultáneamente a través de un mismo medio físico. En el receptor, se aplica el proceso inverso llamado **Demultiplexado**, que separa las señales originales.
![](Multiplexado.png)

#### Problema:
Queremos mandar varias señales digitales por un mismo canal.
#### Solución:
Es **Multiplexado por División de Tiempo** (**TDM**), varios emisores comparten un mismo medio físico enviando sus señales en distintos intervalos de tiempo, de modo que cada uno transmite solo en su propio turno.
Es usado en redes telefónicas, redes troncales de fibra óptica, redes de celulares, redes de cable, en redes satelitales.
![](MultiplexadoPorDivisionDeTiempo.png)

#### ¿Para multiplexar varias señales como hacemos?
**Multiplexado por División de Frecuencia** (**FDM**), varios emisores comparten un mismo medio físico asignado a cada uno una **Banda de Frecuencias Distinta**, de modo que todas las señales viajan simultáneamente **Sin Interferirse**.
Es Usado en cables de cobre y canales inalámbricos, redes telefónicas, de celulares, de cable y satélites.
![](MultiplexadoPorDivisionDeFrecuencia.png)