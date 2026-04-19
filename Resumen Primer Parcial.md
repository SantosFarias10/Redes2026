# Introducción Parte 1
# Nivel Físico
## Degradación de una señal
El transmitir señales por medios físicos nos lleva al siguiente problema: La degradación de las señales. Pueden ocurrir:
* **Interferencias**: Se refiere a perturbaciones externas que afectan la transmisión de la señal. Lo que causa la interferencia es la degradación de la señal y posibles errores en la comunicación.
	* Puede ser **Interferencia Electromagnética** (**EMI**).
* **Ruido**: Es cualquier señal no deseada que se mezcla con la señal útil durante la transmisión. Es causa por EMI, variaciones térmicas o equipos cercanos. Lo que causa es la **Distorsión** o perdida de calidad en la comunicación, aumentando la probabilidad de errores en los datos
* **Atenuación**: Es la **perdida de intensidad** de la señal conforme viaja a través del medio de transmisión. Las señales digitales sufren mas de atenuación que las señales analógicas, a frecuencias mayores los pulsos se tornan mas redondeados y pequeños.
## Codificación y Decodificación de Señales
Que se hace para pasar de secuencias de bits a señales físicas y recíprocamente.
### Codificación
Es el proceso de **transformar la secuencia de bits** en una señal física que pueda transmitirse por el medio, se representan los bits en forma de pulsos electrónicos, variaciones de voltaje, ondas de luz o radiofrecuencias.
El **objetivo** es asegurar que la señal sea **interpretada correctamente** por el receptor y que se minimicen errores durante la transmisión.
### Decodificación
Es el proceso inverso. **Recibir la señal físicas** y convertirla nuevamente en la secuencia original, el receptor aplica el mismo esquema de codificación que uso el transmisor para interpretar correctamente la señal.
## Modulación
El proceso de modulación sirve para **convertir la señal digital en una señal analógica**, lo que permite que la información viaje de manera eficiente y confiable a través del medio que no permite señales digitales.
## Multiplexado
Consiste en **combinar dos o mas señales independientes** para transmitirlas simultáneamente a través de un mismo medio físico. En el receptor, se aplica el proceso inverso llamado **Demultiplexado**, que separa las señales originales.
### Problema
Queremos mandar varias señales digitales por un mismo canal.
### Solución
Es **multiplexado por división de tiempo** (**TDM**), varios emisores comparten un mismo medio físico enviando sus señales en distintos intervalos de tiempo, de modo que cada uno transmite solo en su propio turno.
### ¿Cómo hacemos para multiplexar varias señales?
**Multiplexado por división de frecuencia** (**FDM**), varios emisores comparten un mismo medio físico asignado a cada uno una **banda de frecuencias distintas**, de modo que todas las señales viajan simultáneamente **sin interferirse**.

---
# Conexión Directa entre Máquinas
## Conexión Punto a Punto
**Dos dispositivos** están conectados directamente entre si, sin intermediarios ni compartición del medio con otros usuarios.
### ¿Qué pasa si tenemos `n` maquinas y las queremos conectar entre si, usando conexiones punto a punto usando cables?
Se necesita $\frac{n(n-1)}{2}$ cables, cada maquina necesita `n` puertos para enchufar esos cables.
## Canales de difusión
A los medios compartidos se los llama **canales de difusión**.
## Colisiones
Si varias máquinas transmiten al mismo tiempo, sus señales se superponen y se destruyen. Esto se lo llama **Colisión**
### ¿Cómo controlamos las colisiones pero en el caso de computadoras conectadas a un canal de difusión?
* **Primera Solución**: Si dos máquinas colisionan, entonces **esperamos un tiempo aleatorio** (diferente) para cada una y pasado ese tiempo retransmitimos.
* **Segunda Solución**: Una máquina **coordina el orden** de transmisión de las demás máquinas.
## Comunicación confiable
Saber que un mensaje llego dañado es solo la mita del trabajo. La otra mitad es lograr que el mensaje correcto llegue finalmente al destino. Ese es el **objetivo de la comunicación confiable**.
Supongamos que un mensaje se recibió con error ¿Cómo hacemos para recibir de nuevo el mensaje, o sea para retransmitirlo?
* **Primera Idea**: 
	* El receptor envía un mensaje NAK (ACK negativo) con el número de mensaje que se recibió mal. 
	* Luego el emisor lo manda de nuevo. 
	* El receptor manda un ACK con el número de mensaje que recibió bien.
* **Segunda Idea**:
	* El receptor manda un ACK con el número de mensaje que recibió bien. 
	* El emisor manda un mensaje y dispara un temporizador;
		* Si el temporizador del mensaje expira, entonces significa que el mensaje llego mal o se perdió y hay que reenviarlo.
## Fragmentación de mensajes
La comunicación confiable nos permite la retransmisión de un mensaje cuando llega con errores. Pero ¿Qué pasa si el mensaje es demasiado largo? ¿Conviene retransmitirlo todo? No ya que seria demasiado costoso
### ¿Qué se puede hacer para retransmitir la parte dañada?
Con **Fragmentación** dividimos el mensaje en **tramas**. En lugar de mandar el mensaje entero se mandan tramas. Si una trama llega dañada, entonces se retransmite solo esa trama.
### ¿Que seria necesario para reconstruir el mensaje original?
* Numeración de trama y reensamblado (en ese orden).
* Confirmación de tramas buenas recibidas.
* Retransmisión de tramas dañadas.
## Repetidores
Debido a la atenuación, una señal a medida que se va propagando por un cable se va debilitando. Llega un punto a partir del cual la señal es demasiado débil como para continuar su viaje
### ¿Cómo hacemos para que la señal pueda viajar mas alla de ese punto?
Definimos un mecanismo físico para extender el alcance físico de la red llamado **Repetidores**; es un dispositivo que recibe, amplifica (regenera) y retransmite en ambas direcciones. Pero los repetidores introducen un **retardo**.
## Conmutadores
Usando los repetidores, todos los segmentos de cable forman un único canal. Entonces la probabilidad de colisiones aumenta. ¿Cómo podemos hacerlo mejor? Usando ***Switch*** (Conmutadores).
## Local Area Network (LAN)
Los repetidores extienden el alcance y los conmutadores segmentan el tráfico. Los canales de difusión permiten compartir el medio. La combinación de todos estos elementos define una red organizada dentro de un área limitada: Una **red de área local**.

---
# Preguntas de repaso para la introducción parte 1
1. ***¿Cuál es la definición de una **red de computadoras** y cuándo se considera que dos computadoras están **interconectadas**?***
	* Una **red de computadoras** es un conjunto de dispositivos interconectados. Se considera que dos hosts están **interconectados** específicamente si tienen la capacidad de intercambiar información entre sí.
2. ***Explique la **diferencia fundamental entre una señal analógica y una señal digital** según su representación en el tiempo.***
	* Una **señal analógica** se representa como una función continua del tiempo con variaciones suaves de nivel, mientras que una **señal digital** se compone de valores discretos y cambios bruscos de nivel (como pulsos) para representar bits.
3. ***¿Cuáles son las **tres propiedades principales de una onda sinusoidal** que pueden modificarse para representar información?***
	* Las propiedades fundamentales de una onda sinusoidal son la **amplitud** (altura de la onda), la **frecuencia** (número de ciclos por unidad de tiempo) y **la fase** (posición relativa en el ciclo).
4. *****Clasifique los medios físicos de transmisión** mencionados en el texto y proporcione dos ejemplos de cada uno.***
	* Los medios se clasifican en **guiados** (cable de cobre, fibra óptica), **no guiados** (radio, microondas) y **medios magnéticos** (DVDs, cintas magnéticas).
5. ***¿En qué consiste el fenómeno de la **atenuación** y cómo afecta de manera distinta a las señales analógicas y digitales?***
	* La **atenuación** es la pérdida de intensidad de la señal conforme viaja por el medio. Las señales digitales sufren más este efecto, ya que a frecuencias mayores los pulsos se vuelven más redondeados y pequeños.
6. ***Describa el **propósito de la modulación** en el contexto de las comunicaciones que utilizan medios que no permiten señales digitales directas.***
	* **La modulación** transforma una señal digital en una analógica para que la información pueda viajar por medios que no soportan la transmisión digital directa, como el aire. Se logra alterando la amplitud, frecuencia o fase de una onda base.
7. ***¿**Qué es el multiplexado** y cuál es la diferencia operativa entre el multiplexado por división de tiempo (TDM) y el de división de frecuencia (FDM)?***
	* El **multiplexado** combina varias señales para transmitirlas por un mismo medio. TDM asigna intervalos de tiempo distintos a cada emisor, mientras que FDM asigna a cada uno una banda de frecuencia diferente para que viajen simultáneamente.
8. ***¿Por qué se afirma que las conexiones punto a punto no son escalables cuando el número de máquinas ($n$)?***
	* No escalan porque conectar $n$ máquinas requiere $\frac{n(n-1)}{2}$ cables y que cada máquina posea $n$ puertos físicos. En redes con cientos de dispositivos, esta cantidad de hardware se vuelve impracticable.
9. ***Explique **qué es una colisión** en un canal de difusión y mencione una solución común para gestionarla.***
	* Una **colisión** ocurre cuando dos o más máquinas transmiten simultáneamente en un medio compartido, causando que las señales se superpongan y se destruyan. Una solución es que cada máquina espere un tiempo aleatorio diferente antes de retransmitir.
10. ***¿Cuál es la **diferencia funcional entre un repetidor y un conmutador** (switch) en términos de dominios de colisión?***
	* Un repetidor simplemente regenera y extiende la señal, manteniendo un único canal donde las colisiones aumentan. Un conmutador es más inteligente y segmenta el tráfico en varios dominios de colisión, aumentando la velocidad efectiva de la red.
---
# Introducción parte 2
# Conectando redes de área local
## Conectar LANs
### Enrutadores
es un nodo intermedio cuya función principal es interconectar múltiples redes de área local.
![](ConectarLANs.png)
#### ¿Cómo sabe el enrutador por cual línea de salida mandar un mensaje a una de las máquinas de A a la F?
Definiendo una **tabla de reenvió**
![](TablaDeReenvio.png)
#### ¿Cómo puede manejar el enrutador que recibe varios mensajes que se van acumulando para ser enviados por una línea de salida?
Usando una **Cola de Mensajes** en la línea de salida.
### Almacenamiento y reenvío
#### ¿Cómo hace el enrutador para mandar un mensaje de A a F?
1. El enrutador consulta la tabla de reenvío y descubre que hay que usar la línea l2.
2. Encola el mensaje en la cola de la línea l2.
3. Cuando el mensaje llega a la cabeza de la cola el enrutador lo reenvía.
A esto lo llamamos **almacenamiento y reenvío**.
#### Como calcular la demora de almacenamiento y reenvío
$$
d_{nodal} = d_{proc} + d_{queue} + d_{trans} + d_{prop}
$$
donde:
*  $d_{nodal}:$ Procesamiento del Nodo. Representa el tiempo total de demora que experimenta un mensaje o paquete en un nodo específico (como el enrutador) durante el proceso de almacenamiento y reenvío
* $d_{queue}:$ Demora por Encolado. Es el tiempo que el mensaje pasa esperando en la cola del enlace de salida antes de que pueda ser transmitido. Este valor es variable y depende directamente de qué tan congestionado se encuentre el enrutador en ese momento.
* $d_{trans}:$ Demora en Transmitir los Paquetes. Es el tiempo que el nodo demora en empujar (transmitir) todos los datos del paquete hacia el enlace. Se calcula utilizando la fórmula $d_{trans} = \frac{L}{R}$, donde $L$ es la longitud del paquete y $R$ es la tasa de transferencia de datos de la línea.
* $d_{prop}$: Representa el tiempo de propagación que tarda la señal en viajar a través del enlace hacia el siguiente destino.
* $d_{proc}$: Es el tiempo que le toma al nodo procesar el paquete. Esto incluye tareas como el chequeo de errores y la consulta a la tabla de reenvío para determinar la línea de salida correspondiente.
## Wide Area Network (WAN)
Si se intenta conecta cientos o miles de LAN a un solo enrutador, colapsa. Ahí es donde se necesita pasar a una estructura mas grande, **Redes de área amplia**.
## Algoritmo de Enrutamiento
### ¿Seguir cualquier ruta al destino da lo mismo?
NO. Las rutas difieren en la cantidad de enrutadores por lo que hay que pasar, el trafico, las tasas de datos soportadas por los enlaces, etc. Por lo que no todas las rutas son iguales.
### ¿Cómo hacer para poder tener acceso a las mejores rutas?
Modificando las tablas de reenvío con ese propósito. Los algoritmos que modifican estas tablas son los algoritmos de enrutamiento.

---
# Conectando redes de área amplia
## Redes de acceso
Existen empresas que se llaman **proveedores de servicio de red** y suelen operar en una región especifica. Para usuarios particulares estas redes se llaman **redes de acceso**.
Las LAN se conectan a las redes de acceso, por lo que para poder interconectar todas las LAN entre si hacen falta varias redes de acceso interconectadas. A esto se le llama **Interred**.
## Redes globales de transito
Las redes de acceso se conectan a una red global de transito
## Redes regionales
Las redes globales de transito forman la infraestructura que permite mover trafico entre países y continentes, conectando grandes proveedores y sosteniendo la escala planetaria  de redes como la internet.
### ¿Cómo evitar que las redes de acceso de esa región se queden incomunicadas de otras regiones?
Usando una **Red regional** en la región que se va a conectar con alguna red global de transito.

---
# Redes proveedoras de contenido
Son infraestructuras propias diseñadas para llevar su contenido lo mas cerca posible de los usuarios. Se conectan directamente a redes de acceso o a redes regionales.
## Punto de presencia
### ¿Cómo hacer para que el contenido este mas cerca de los usuarios sin depender de redes globales?
Se usa puntos de presencia (**PoPs**); son nodos distribuidos que reducen la latencia y evitan transito global.
Un PoPs esta pensado para entregar contenido a usuarios cercanos, no para generarlo ni coordinarlo. Si solo existiera los PoPs, aparecen 3 problemas inevitables:
1. **No pueden generar contenido**.
2. **No pueden mantenerlo actualizado**.
3. **No pueden replicar contenido entre ellos sin pagar transito**.
### ¿Qué necesita un PoP para cumplir su función?
* Almacenar contenido popular localmente para reducir latencia.
* Responder a muchas usuarios simultáneamente.
* Conectarse a redes de acceso o regionales para entregar contenido.
* Recibir contenido actualizado desde algún centro de datos.
### ¿Como atiende un PoP a muchos usuarios a la vez ?
Hace falta **servidores de borde** para atender usuarios y balanceadores de carga para repartir la carga
### ¿Cómo se conecta un PoP a redes de acceso o regionales?
Usando equipo de redes, enlaces de alta capacidad y acuerdo de *peering local* con redes de acceso y regionales.
## Centro de datos
### ¿Cómo aliviar los PoPs y mejorar la arquitectura?
**Separando las funciones**. Los PoPs debe solo entregar contenido y conectarse a redes de acceso o regionales. Otros componentes se deben de encargar de generar, almacenar, actualizar y coordinar el contenido.
Los centros de datos aparecen como la única forma viable de:
* Tener la Versión maestra del contenido.
* Procesarlo (Transcodificación, empaquetado, indexación).
* Mantenerlo actualizado.
* Replicarlo hacia los PoPs.
* Coordinar consistencia entre múltiples ubicaciones.
* Balancear carga y Manejar picos globales.
### ¿Qué necesita hacer un centro de datos?
* Generar contenido.
* Mantener la version maestra de cada objeto.
* Coordinar actualizaciones para que todos los PoPs tengan contenido consistente.
* Replicar contenido hacia los PoPs de forma eficiente.
* Almacenar grandes volumenes de datos.
* Balancear carga global entre regiones.
* Tener alta disponibilidad.
* Conectarse a una red propia que distribuya contenido sin pagar transito.
### ¿Cómo almacena contenido un PoP sin ser un centro de datos?
Usando una **caché** dentro de un PoP que guarde solo lo mas pedido.
### ¿Cómo se genera y procesa contenido a gran escala?
Se usa una **Capa de Procesamiento Pesado** con:
* Servidores de computo.
* Clusters de procesamiento.
* Sistemas de Transcodificación.
	* La **Transcodificación** es el proceso de convertir un contenido digital de un formato, resolución o tasa de bits a otro, para que pueda ser entregado de manera eficiente a distintos tipos de dispositivos, redes y condiciones de ancho de banda.
### ¿Como se mantiene una version maestra del contenido?
Se usan **Capas de Almacenamiento Maestro**:
* Sistemas de almacenamiento de objetos distribuidos en varios nodos,
* Sistemas de Archivos distribuidos,
* Base de Datos.
### ¿Como se coordinan actualizaciones entre regiones?
Se usan **Capa de Control y Coordinación**. Se encarga de:
* La orquestación.
* Coordinación global.
* Gestión de Versiones,
* Consistencia, que los PoPs tengan lo que deben de tener.
* Políticas de Distribución.
* Monitoreo y Telemetría
* Replicación.
### ¿Como se Asegura disponibilidad Continua?
* Climatización
* Monitoreo
* Redundancia Energética
* Failover Regional, es el mecanismo mediante el cual un servicio puede migrar automáticamente su operación desde una region que falla hacia otra region que sigue funcionando, sin que los usuarios pierdan acceso al contenido. Este mecanismo garantiza continuidad incluso ante fallas grandes.
## Red privada global
### ¿Como llevar contenido desde los centros de datos a los PoPs sin pagar transito?
Usando una **Red Privada Global** propia del proveedor de contenido, que conecte centros de datos y PoPs sin depender del transito global.
Esto permite enlaces troncales privados, rutas optimizadas para replicación masiva.
Con esto el proveedor puede mover contenido desde los centros de datos hacia la periferia (PoPs) sin pagar transito y con baja latencia.

---
# Preguntas de repaso para la introducción parte 2
1. ***¿Cuál es la función principal de un enrutador al interconectar redes de área local?***
	* **Función del enrutador:** El enrutador actúa como un nodo intermedio que permite la comunicación entre máquinas de diferentes redes locales. Su tarea es decidir, mediante tablas de reenvío y algoritmos de enrutamiento, por qué línea de salida debe viajar cada mensaje para llegar a su destino de manera eficiente.
2. ***Explique brevemente en qué consiste el proceso de "almacenamiento y reenvío" (store and forward).***
	* **Almacenamiento y reenvío:** Es el proceso donde un enrutador recibe un mensaje, consulta su tabla de reenvío para identificar la interfaz de salida y lo coloca en una cola. Una vez que el mensaje llega a la cabeza de la cola y la interfaz está disponible, el enrutador lo transmite hacia el siguiente nodo.
3. ***¿Cómo se calcula la demora nodal total ($d_{nodal}$) en un enrutador?***
	* **Cálculo de la demora nodal:** La demora nodal es la suma de cuatro componentes: 
		* la demora de procesamiento ($d_{proc}$),
		* la demora de encolado ($d_{queue}$), 
		* la demora de transmisión ($d_{trans} = \frac{L}{R}$) 
		* y la demora de propagación ($d_{prop}$). 
	* Representa el tiempo total que tarda un paquete desde que llega al nodo hasta que es enviado.
4. ***¿Por qué es inviable que una sola empresa conecte todas las LAN del planeta?***
	* **Imposibilidad de una empresa única:** Esto se debe principalmente a que la inversión necesaria en infraestructura a escala global sería prohibitiva. Además, existen complicaciones legales y políticas de permisos que varían drásticamente entre los diferentes países, lo que obliga a la existencia de múltiples proveedores regionales.
5. ***¿Qué factores determinan que una ruta sea considerada "mejor" que otra en una red de área amplia?***
	* **Criterios de mejor ruta:** Una ruta se considera superior basándose en el "costo" de atravesar sus enlaces. Este costo se mide por la combinación de la longitud del enlace, la congestión de las colas en los enrutadores intermedios y las tasas de transferencia de datos soportadas por los cables.
6. ***¿Cuál es la diferencia jerárquica y económica entre las redes de acceso, las redes regionales y las redes globales de tránsito?***
	* **Jerarquía de redes:** Las redes de acceso conectan directamente a los usuarios y pagan a las redes regionales por conectividad. A su vez, las redes regionales interconectan las de acceso de una zona y pagan a las redes globales de tránsito, que poseen infraestructuras de alta velocidad (fibra óptica) para mover tráfico entre continentes.
7. ***¿Qué es un Punto de Intercambio de Internet (IXP) y para qué sirve?***
	* **Punto de Intercambio de Internet (IXP):** Es una infraestructura técnica diseñada para interconectar múltiples redes de distintos tipos (acceso, regionales, globales o de contenido). Facilita el intercambio de tráfico directo (peering) entre ellas para mejorar la eficiencia y reducir la dependencia de terceros.
8. ***¿Cuál es el propósito fundamental de una Red Proveedora de Contenido (CDN)?***
	* **Propósito de las CDN:** Las CDN buscan entregar contenido (videos, archivos, aplicaciones) a millones de usuarios con la menor latencia posible. Para ello, despliegan infraestructura propia que acerca el contenido a la periferia de la red, evitando los costos elevados y las limitaciones de las redes de tránsito global.
9. ***¿Qué funciones críticas desempeña un Centro de Datos que un Punto de Presencia (PoP) no puede realizar?***
	* **Funciones del Centro de Datos vs. PoP:** Mientras que el PoP solo entrega contenido popular localmente mediante cachés, el Centro de Datos alberga la "versión maestra" del contenido. El centro de datos se encarga del procesamiento pesado (transcodificación), la coordinación global, el almacenamiento masivo y la actualización de todos los PoPs.
10. ***¿En qué consiste el "failover regional" y qué beneficios aporta a la disponibilidad del servicio?***
	* **Failover regional:** Es un mecanismo de redundancia que migra automáticamente la operación de un servicio de una región que ha fallado a otra que sigue operativa. Garantiza la continuidad del servicio ante desastres climáticos, cortes eléctricos masivos o fallas graves de software sin que el usuario pierda acceso.

---
# Introducción parte 3
# Conectando procesos
### ¿Cómo distinguir los mensajes para un programa con los mensajes para otro programa?
Los mensajes indican cual es el programa receptor.
### ¿Cómo se puede indicar?
Usando un **identificador de programa** (en internet se lo llama **Puerto**).
## Ordenamiento de paquetes
### ¿Si mandamos dos paquetes de un mismo mensaje, esos paquetes van a seguir la misma ruta?
No necesariamente, puede que cambien las rutas entre paquetes porque se ejecutó el algoritmo de enrutamiento.
### ¿Si dos paquetes de un mensaje son enviados, ¿Van a llegar en orden?
No necesariamente, puede pasar que:
* El segundo paquete sigue una ruta mas corta y llega antes.
* El primer paquete se pierde y debe ser reenviado; lo que causa que el segundo llegue antes.
Por lo que, los paquetes de un mismo mensaje pueden llegar fuera de orden. Entonces es necesario ordenar los paquetes de un mensaje porque una aplicación  de red necesita procesar el mensaje completo ordenado.
## Control de congestión
¿Qué pasa cuando muchos flujos simultáneos compiten por la misma red? En este escenario, aunque cada flujo pueda ordenar sus paquetes, la red puede saturarse, perder paquetes masivamente o volverse inestable.
### ¿Qué pasa si en la línea de salida de un enrutador llegan demasiados paquetes a reenviar?
* La cola de salida se hace muy grande y el reenvío se pone lento.
* La capacidad de la línea de salida se puede saturar y entonces comienzan a perderse paquetes.
A este fenómeno se lo llama **Congestión**, y ocurre cuando la red no puede manejar la carga de paquetes que recibe de manera aceptable.
### ¿Como se controla la congestión?
La computadora emisora se entera de la congestión y reduce el tráfico de salida. A esto lo llamamos **control de congestión**.
## Control de flujo
El control de congestión solo considera el estado de la red, no el estado de los procesos que se comunican. Si no hay congestión, un receptor puede saturarse si el emisor envía datos mas rápido de lo que puede procesarlo. Esto causa que el receptor pierda paquetes y el emisor probablemente va a interpretar esas pérdidas como congestión.
### ¿Qué pasa si un emisor envía paquetes a un receptor más rápido que la capacidad del receptor de procesar cada paquete?
Se satura de paquetes el buffer del receptor hasta que este ya no puede almacenar mas paquetes que le lleguen y comienzan a perder paquetes.
### ¿Cómo se evita que un emisor rápido sature de paquetes a un receptor lento?
Uso de **retroalimentación al emisor**, o sea el receptor le indica al emisor cuando y cuanto puede enviar.
### ¿Cómo se puede perder paquetes en una red?
Los paquetes pueden perderse por varias razones:
* Por congestión en los enrutadores cuando las colas se llenan;
* porque el emisor envía mas rápido que lo que el receptor puede procesar y este descarta paquetes;
* Por errores físicos o daños en los paquetes durante su transmisión.
### ¿Cómo manejar la perdida de paquetes?
Los paquetes recibidos son confirmados por el receptor.
El emisor detecta que paquetes faltan. Porque no recibió sus confirmaciones de recepción durante un cierto tiempo. Ese tiempo se mide con un temporizador asociado a cada paquete.
Si un paquete no es confirmado, el emisor lo retransmite.

---
# Aplicaciones de red
## Protocolos
Un **protocolo** es un conjunto de reglas y convenciones que definen cómo se comunican, interactúan y cooperan los procesos o aplicaciones a través de una red. Las aplicaciones de red no utilizan la red simplemente arrojando datos, sino que negocian y siguen un protocolo que especifica todos los detalles exactos de cómo se llevará a cabo dicha comunicación.
Tenemos aplicaciones que proporcionan servicios (llamadas **Aplicaciones de Red**) definidas mediante protocolos.
## Arquitectura
Una arquitectura describe que procesos participan en la aplicación, que roles cumplen y como se comunican entre si. Las arquitecturas ayudan a diseñar la aplicación, ya que define la estructura básica sobre la cual luego se construyen los protocolos de comunicación.
### Arquitectura Cliente-servidor
Un proceso actúa como cliente (inicia un pedido) y otro como servidor (atiende el pedido).
### Arquitectura P2P
Los procesos pueden cumplir ambos roles: A veces piden, a veces responden, y pueden compartir recursos entre si sin un servidor central.

---
# Modelo de capa de una red
![](ModeloDeCapas.png)
* La **Capa 5 de Aplicación** produce un mensaje y lo pasa a la capa de transporte.
* La **Capa 4 de Transporte** pone un encabezado en el mensaje para identificarlo y pasa el resultado a la capa de Red. El encabezado contiene Numeros de Secuencia para que la capa de transporte de la maquina de destino entregue los mensajes en el orden correcto.
* La **Capa 3 de Red** pone limitaciones en el tamaño de los mensajes, divide en **Paquetes** los mensajes que llegan. A cada paquete se le coloca otro encabezado. Pasa los paquetes a la capa de enlace de datos.
* La **Capa 2 de Enlace de Datos** agrega un encabezado y un terminador, a cada pieza. Pasa la unidad resultante a la capa física para su transmisión.
* La **maquina receptora** pasa hacia arriba de capa en capa, perdiendo los encabezados conforme avanza.
![](ModeloDeCapas2.png)
---
# Preguntas de repaso para la introducción parte 3
1. ***¿Por qué es fundamental la introducción del concepto de "puertos" al conectar procesos?***
	* Los puertos son necesarios porque permiten identificar programas específicos dentro de una máquina una vez que esta ya está conectada globalmente. Sin identificadores de programa o puertos, los mensajes no sabrían a qué proceso receptor deben entregarse.
2. ***¿Cuáles son las dos razones principales por las que los paquetes de un mismo mensaje pueden llegar fuera de orden al receptor?***
	* Los paquetes pueden llegar fuera de orden si el algoritmo de enrutamiento cambia las rutas durante la transmisión, haciendo que un paquete posterior tome un camino más corto. También ocurre si el primer paquete se pierde y debe ser retransmitido, llegando después que el segundo.
3. ***¿En qué consiste el fenómeno de la congestión y qué sucede en los enrutadores cuando esto ocurre?***
	* La congestión ocurre cuando la red recibe una carga de paquetes mayor a la que puede manejar aceptablemente. En los enrutadores, esto provoca que las colas de salida crezcan demasiado, ralentizando el reenvío o saturando la capacidad de la línea, lo que resulta en la pérdida masiva de paquetes.
4. ***¿Cómo pueden las computadoras emisoras mitigar el problema de la congestión en la red?***
	* Las computadoras emisoras pueden controlar la congestión al enterarse del estado de saturación de la red y reducir el tráfico de salida. Esta reducción voluntaria del flujo de datos evita que la red colapse cuando múltiples procesos compiten por los mismos recursos.
5. ***¿Cuál es la diferencia fundamental entre el control de congestión y el control de flujo?***
	* El control de congestión se enfoca en el estado general de la red y los enrutadores para evitar su saturación. Por el contrario, el control de flujo considera el estado de los procesos individuales, asegurando que un emisor no envíe datos más rápido de lo que un receptor específico puede procesar.
6. ***¿De qué manera un receptor puede evitar que un emisor rápido sature su capacidad de procesamiento?***
	* Para evitar la saturación, se utiliza un mecanismo de retroalimentación donde el receptor le indica al emisor cuándo y cuánto volumen de datos puede enviar. Esto protege el búfer del receptor contra desbordes y pérdidas de paquetes locales.
7. ***¿Qué mecanismos utiliza un emisor para detectar y manejar la pérdida de paquetes en una comunicación confiable?***
	* El receptor confirma cada paquete recibido y el emisor utiliza temporizadores asociados a cada paquete para medir el tiempo de respuesta. Si el emisor no recibe la confirmación dentro de un tiempo determinado, detecta que el paquete se ha perdido y procede a retransmitirlo.
8. ***Según el ejercicio del pedido de pizza, ¿qué tres elementos definen a un protocolo de aplicación?***
	* Un protocolo se define por la estructura y tipos de mensajes que se intercambian, las reglas de comunicación que rigen la interacción entre procesos y la información de estado que se debe mantener durante la operación.
9. ***¿En qué se diferencia la arquitectura peer-to-peer de la arquitectura cliente-servidor?***
	* En la arquitectura cliente-servidor, los roles están fijos: un proceso siempre pide y el otro siempre responde. En la arquitectura peer-to-peer, los procesos pueden cumplir ambos roles simultáneamente, compartiendo recursos sin necesidad de un servidor central.
10. ***¿Cómo se define la arquitectura de red y qué relación tiene con la "pila de protocolos"?***
	* La arquitectura de red se define como el conjunto integral de capas y protocolos que permiten la comunicación. Este conjunto es lo que comúnmente se denomina "pila de protocolos", donde cada nivel ofrece servicios específicos al superior.
