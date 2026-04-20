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
---
# Capa de Aplicación
La capa de aplicación define **como las aplicaciones usan la red** para intercambiar información y coordinarse.
La capa de aplicación responde a 3 preguntas esenciales:
1. **¿Quien habla con quién?**
	* Para eso se definen las arquitecturas de aplicaciones de red (cliente-servidor, P2P, híbrida).
2. **¿Qué mensajes se intercambian y qué significan?**
	* Para esto se definen protocolos de aplicación.
3. **¿Cómo se implementan esa comunicación?**
	* Para esto se usan tecnologías de complementación (socket, web, abstracciones y compromisos).
La capa de aplicación es donde se diseña la lógica de interacción entre programas distribuidos.
# Arquitecturas de aplicaciones de red
La arquitectura es el esqueleto de una aplicación de red: Define quien hace qué y cómo fluye la información.
## Cliente-Servidor
![](Cliente-Servidor.png)
* Tenemos nodo clientes y nodo servidor.
* El servidor es la componente dominante que controla el estado, coordina los clientes, almacena datos y es el único punto de acceso.
* El cliente hace pedidos de diferentes tipos y el servidor los proceso y responde.
* Características: Control fuerte, simplicidad, pero poca escalabilidad sin infraestructura adicional.
### Los servidores
* La ubicación suele ser fija.
* Las direcciones IP suelen no cambiar.
* El tiempo de vida es largo.
### Los clientes
* La ubicación pueden ser fijos o móviles.
* Las direcciones IP suelen ser dinámicas.
* Y el tiempo de vida suele ser mas corto (usar el servidor y luego salen).
## P2P (compañero a compañero)
La arquitectura cliente-servidor tiene un modelo claro y ordenado donde un servidor central coordina, almacena y responde a los pedidos de múltiples clientes. Pero ese orden tiene un costo, el servidor se convierte en un **Punto único de carga, de falla y de control**. A medida que las aplicaciones crecen en cantidad de usuarios, volumen de datos o necesidad de resiliencia, este modelo empieza a mostrar límites estructurales.
Para superar esas restricciones surge otra forma de pensar la organización: Distribuir el trabajo entre los propios nodos, permitir que colaboren directamente y eliminar la dependencia de un único punto central. Este cambio de perspectiva nos lleva al modelo P2P, donde los nodos dejan de ser meros consumidores y pasan a ser participantes en la red.
### ¿Qué es una arquitectura P2P?
Una **arquitectura P2P** es un modelo de organización para aplicaciones de red donde **el trabajo se distribuye directamente entre los propios nodos**, eliminando la dependencia de un servidor o punto central único. En este enfoque, los nodos dejan de ser simples consumidores de información y se convierten en **participantes activos** que colaboran entre sí.
### Características de los compañeros 
En esta arquitectura, los nodos (llamados **compañeros** o _peers_) asumen la doble responsabilidad de **solicitar servicios a otros compañeros y, al mismo tiempo, proveer servicios** en respuesta a los pedidos de los demás. Estos compañeros suelen tener direcciones IP dinámicas y privadas, y tienen la característica de poder entrar y salir de la red en cualquier momento.
### Ejercicio
![](Ejercicio2DistribucionDeArchivoP2P.png)
Analizamos el mejor caso: 
* Asumimos que los enrutadores están muy cerca entre sí, para no necesitar contar el tiempo de propagación.
* El enrutamiento es optimo, eso quiere decir que:
	* No hay pérdida de paquetes.
	* Los paquetes llegan sin errores.
	* El tiempo de procesamiento no importa.
#### Cliente-servidor
* Asumimos que solo el servidor manda datos a la mayor velocidad.
* Los enrutadores no van a formar colas, van a mandar en cuento reciben.
* ¿Como hacemos funcionar el enrutador conectado directamente al servidor?
	* El enrutador divide el tráfico entre las dos líneas de salida, y el tráfico sigue por dos caminos.
	* Como consecuencia el destino recibe datos a 1 Gbps (Giga bits por segundo).
	* ¿Cuanto es 1,25 GB en bits?
		* $1,25 GB = 1,25 * 2^{30} * 8 b = 10 * 2^{30} b = 10 * 1037$ millones bits $= 10,37 Gb$
		* Luego el tiempo de recepción del archivo es ed 10,37 segundos.
#### P2P
* Asumimos que el servidor se comporta como un compañero más.
* ¿Cuanto es la máxima cantidad de Gbps que le puede llegar al enrutador de destino considerando los enlaces que llegan al enrutador más a la derecha?
	* Contando todas las líneas de entrada al enrutador, puede mandar a 1,1 Gbps.
	* Suponiendo que llegan datos a esa velocidad, ¿Cuantos segundos toma descargar el archivo completo?
		* Ya vimos antes que 10,37 Gb es el tamaño del archivo.
		* Entonces 
```
1 segundo ------>  1,1 Gb
X         ------> 10,37 Gb
=> X = 9,42 segundos
```
* ¿Como puede llegar 1,1 Gbps al enrutador más a la derecha?
	* Hay muchas posibilidades para lograrlo: El servidor manda 500 Mbps y *peer* 2 manda a 500 Mbps y el *peer* 3 manda a 100 Mbps.
## Arquitectura híbrida
Una **arquitectura híbrida** es un modelo intermedio que **combina elementos del modelo cliente-servidor y del modelo P2P** para aprovechar las ventajas de ambos enfoques.
A medida que las redes P2P crecen y se vuelven más complejas, los servidores livianos dejan de ser suficientes porque los nodos necesitan ayuda para conectarse, iniciar la comunicación o mantener la información organizada. Las arquitecturas híbridas resuelven esto buscando un equilibrio: Mantienen la **escalabilidad y distribución de carga** del modelo P2P, pero incorporan la **coordinación, descubrimiento de nodos y control** característicos del modelo cliente-servidor
### Los servidores
Tienen un rol activo y asumen funciones organizativas más "pesadas". Se encargan de la señalización compleja (descubrir quién está conectado y establecer la sesión), autenticar a los usuarios, manejar el estado de la red e indexar la información
### Los compañeros
Siguen siendo los encargados de realizar el trabajo verdaderamente pesado, como la transferencia directa de los archivos o el procesamiento de datos distribuidos
## Comparación de los tipos de arquitectura
![](ComparacionTiposArquitectura.png)
# Protocolos de aplicación de red
### ¿Cómo se entienden los nodos de una aplicación de red entre si?
Usando protocolos. Un **Protocolo** es el lenguaje de la aplicación distribuida: Sin él, no hay coordinación posible.
### ¿Que cosas se definen en un protocolo de aplicación de red?
* Tipos de mensajes.
* Sintaxis del mensaje.
* Semántica del mensaje.
* Reglas.
* Estado de la aplicación.
### ¿Que es el estado?
El **estado es la información que una aplicación mantiene y recuerda entre los distintos mensajes** que se intercambian durante una comunicación.
### ¿Porque es necesario el estado?
Sin el manejo del estado, **cada mensaje recibido sería ambiguo** y no habría forma de interpretar las acciones dentro de un contexto adecuado. Además, sin esta "memoria", sería imposible realizar tareas cotidianas en la red como mantener una sesión iniciada, hacer transferencias de archivos prolongadas, ejecutar operaciones compuestas o simplemente coordinar las aplicaciones entre sí.
## Protocolo de transferencia de archivos
Es un protocolo de aplicación diseñado bajo una arquitectura **cliente-servidor** cuya función principal es permitir la autenticación de usuarios, la navegación por directorios y el intercambio o transferencia de archivos a través de una red.
# Tecnologías de implementación
## Socket
Un Socket es un punto de conexión que permite a una aplicación:
* Enviar y recibir datos por la red.
* Sin conocer los detalles internos de TCP o UDP.
* Usando una interfaz uniforme en cualquier sistema operativo.
Un socket no es un protocolo: Es una abstracción para acceder a protocolos de transporte. Los socket se usan para invocar a TCP o UDP a través del sistema operativo.
## La Web
La **Web** es una plataforma de alto nivel de aplicaciones de red basada en protocolos estandarizados (como HTTP/HTTPS) que permite acceder a la información y servicios desde cualquier navegador, sin manejar detalles de bajo nivel como los sockets.
Las **ventajas de la web**:
* Se trabaja con una abstracción más alta al no tener que gestionar paquetes, puertos, ni protocolos de capa de transporte.
* Cualquier dispositivo con navegador puede usar la aplicación sin instalar nada.
* La web permite enfocarse en la lógica de la aplicación en lugar de la infraestructura de red.
* Permite interoperabilidad entre lenguajes, sistemas y dispositivos.
* Es la base de la mayoría de las aplicaciones modernas.
## Comparación de tecnologías de implementación
![](ComparacionTecnologiasDeImplementacion.png)

---
# Preguntas de repaso para la capa de aplicación
1. ***¿Cuáles son las tres preguntas fundamentales que responde la capa de aplicación?*** La capa de aplicación define quién habla con quién a través de las arquitecturas, qué mensajes se intercambian y su significado mediante protocolos, y cómo se implementa dicha comunicación utilizando tecnologías específicas. Estas tres dimensiones permiten coordinar la lógica de interacción entre programas distribuidos.
	* ¿Quién habla con quién? (Arquitecturas), ¿Qué mensajes se intercambian? (Protocolos) y ¿Cómo se implementa? (Tecnologías).
2. ***¿En qué se diferencia el rol de un servidor del de un cliente en una arquitectura cliente-servidor?*** El servidor es el componente dominante que controla el estado, almacena los datos y actúa como el único punto de acceso permanente con una dirección IP fija. Por el contrario, el cliente inicia los pedidos, tiene una vida útil más corta y suele poseer una dirección IP dinámica.
	* Servidor: Control total, IP fija, larga vida. Cliente: Inicia pedidos, IP dinámica, vida corta
3. ***¿Cómo gestiona una aplicación cliente-servidor la pérdida de paquetes cuando utiliza el protocolo UDP?*** Dado que UDP no garantiza la entrega, el cliente debe implementar un temporizador (timeout) después de enviar un paquete. Si el tiempo expira sin recibir respuesta, la lógica de la aplicación decide si debe retransmitir el pedido o abandonar la operación.
	* Uso de timeouts y lógica de retransmisión o abandono por parte del cliente.
4. ***¿Cuál es la principal ventaja de la arquitectura P2P en términos de escalabilidad?*** En el modelo P2P, la capacidad de la red aumenta proporcionalmente al número de compañeros (peers), ya que cada nuevo nodo aporta recursos como ancho de banda y almacenamiento. Esto evita que un servidor central se convierta en un punto único de carga o falla.
	* La red crece con los usuarios; cada peer aporta recursos (ancho de banda/almacenamiento).
5. ***¿Qué función cumple un "tracker" en un sistema de distribución de archivos como BitTorrent?*** El tracker es un nodo que mantiene la lista de los compañeros activos en la red y la proporciona a los nuevos nodos que desean unirse. Permite el descubrimiento de pares para que estos puedan registrarse, informar su presencia o avisar cuando se retiran.
	* Mantiene y entrega la lista de direcciones IP de los compañeros activos.
6. ***¿Por qué es recomendable solicitar primero los "trozos menos comunes" de un archivo en una red P2P?*** Solicitar los fragmentos menos comunes asegura que dichas piezas se repliquen más rápidamente en la red antes de que los nodos que las poseen se desconecten. Esto previene que la descarga del archivo quede incompleta o se postergue indefinidamente por la falta de fuentes.
	* Para aumentar su redundancia y evitar que desaparezcan si los poseedores se retiran de la red.
7. ***¿Qué define a una arquitectura de red como "híbrida"?*** Una arquitectura híbrida combina la escalabilidad del P2P con la coordinación del modelo cliente-servidor, asignando funciones de descubrimiento y control a servidores y la transferencia de datos a los pares. Este modelo busca un equilibrio entre el control centralizado y la distribución eficiente de la carga.
	* Uso de servidores para coordinación/señalización y peers para transferencia masiva de datos.
8. ***¿A qué se refiere el "estado" dentro de un protocolo de aplicación?*** El estado es la información que una aplicación conserva entre mensajes para dar continuidad y coherencia a la interacción, como la identidad del usuario o la fase del diálogo. Sin estado, cada mensaje sería ambiguo y no se podrían mantener sesiones o transferencias complejas.
	* Memoria de la interacción (autenticación, fase, recursos) necesaria para interpretar mensajes en contexto.
9. ***¿Qué es un socket y cuál es su propósito principal?*** Un socket es una abstracción que actúa como punto de conexión para que una aplicación envíe y reciba datos sin necesidad de conocer los detalles internos de TCP o UDP. Funciona como la frontera entre la lógica de la aplicación y los mecanismos de transporte del sistema operativo.
	* Interfaz uniforme para acceder a protocolos de transporte (TCP/UDP) de manera simplificada y estándar.
10. ***¿Cuál es la principal diferencia de abstracción entre usar Sockets y usar la Web (HTTP)?*** Los sockets ofrecen un bajo nivel de abstracción con control total sobre el formato y flujo de los mensajes, lo que aumenta la complejidad para el programador. La Web, en cambio, provee una abstracción de alto nivel con reglas y estructuras estandarizadas que facilitan la interoperabilidad y el desarrollo.
	* Sockets requiere definir el protocolo desde cero; la Web usa estándares predefinidos (HTTP) que resuelven la estructura.
---
# La Web Parte 1
# Paginas Web
La **web original** = hypertexto + transferencia de archivo.
## Páginas web Estáticas
Es un documento accesible a través de la web que un navegador puede solicitar, interpretar y mostrar al usuario. Siempre muestra lo mismo; son documentos.
### ¿Que elementos contiene?
* Referencias a medias.
* Vínculos a otras páginas web.
* Texto.
### Considerando los vínculos a otras páginas qué estructura se forma y qué permite?
* Grafo de páginas web (llamado **hipertexto** para páginas de solo texto, e **hipermedia** para páginas conteniendo medias).
	* Este grafo nos permite **Navegar** por las páginas (o sea, recorrerlas siguiendo los vínculos).
## Paginas web dinámicas
Son generadas y cambian según datos del usuario o datos de bases de datos.
* Pueden usar parámetros y bases de datos.
* Su apariencia puede ser como la de una páginas web estática, pero generada por código.
# Sitios web y aplicaciones web
## Sitio web
Un **Sitio Web** es un conjunto de páginas web relacionadas (usualmente estáticas); producida por una organización o persona; con único nombre de dominio y en al menos un servidor web.
## Aplicaciones web
Una **Aplicación Web** es un servicio al que el usuario accede desde su navegador para realizar tareas, resolver problemas o gestionar información, sin necesidad de instalar software en su computadora. Una aplicación web procesa lógica, toma decisiones, interactúa con bases de datos y genera contenido dinámico según lo que el usuario hace.
# Infraestructura para la web
## Arquitectura y protocolo
### Sobre que tipo de arquitectura se apoya la web?
sobre la Arquitectura cliente-servidor.
* El cliente ejecuta en un navegador (*browser*).
* El servidor ejecuta en un servidor web.
### ¿Que tipo de protocolo necesitamos para la web?
Uno que permita solicitar páginas web (indicando parámetros si es preciso) y recibir páginas web (se usa HTTP).
### ¿Que se necesita para especificar la estructura de una página web?
Un lenguaje con sintaxis apropiada (se usa HTML).
### ¿Que es un buscador web?
es un servicio que permite encontrar páginas en la web. Para ello el usuario ingresa una **expresión contenido palabras claves**. El buscador para responder consultas un **índice** (que relaciona palabras clave con páginas). El buscador web retorna una **lista ordenada de páginas** con texto explicativos.
Tecnología para la web inicial: HTTP, buscadores, browsers, servidores web, HTML.
### ¿Como integrar browser, servidor web, HTTP y HTML?
El browser pide (usando HTTP) una páginas/objetos a un  servidor web. La página pedida puede ser de cualquier de los 3 tipos.
El servidor web retorna (usando HTTP) la página/objetos pedidos.
Si el servidor retornó una página en HTML, el browser interpreta el texto HTML y despliega la página en pantalla.
# Identificación de páginas web y formularios
### ¿Qué informaciones necesito usar para identificar una páginas estática?
* Nombre de dominio de host que contiene la página (para la institución)
* Camino a la página (en el sistema de archivos).
* Nombre del documento que contiene la página (página estática)
---
# Preguntas de repaso para la web parte 1
1. ***¿Qué define técnicamente a una página web estática y qué tipo de elementos puede contener?***
	* Una página web estática es un documento accesible por la web que un navegador solicita, interpreta y muestra, manteniendo siempre el mismo contenido. Contiene texto organizado, referencias a medios (como imágenes JPEG o videos MPEG) y vínculos a otras páginas.
2. ***¿Cómo se transforma una colección de páginas web en una estructura de hipermedia?***
	* La estructura de hipermedia se forma a través de los vínculos (links) entre diversas páginas, creando un grafo de documentos interconectados. Este grafo permite al usuario navegar y recorrer la información siguiendo los enlaces establecidos.
3. ***¿Cuál es la diferencia fundamental entre una página web dinámica y una de página única (Single Page)?***
	* Las páginas dinámicas se generan mediante código y bases de datos según los parámetros del usuario, mientras que las de página única presentan toda la interfaz en una sola página. Estas últimas suelen parecerse más a aplicaciones de escritorio, realizando pedidos de datos específicos al servidor sin recargar todo el sitio.
4. ***¿Qué requisitos debe cumplir un conjunto de páginas para ser considerado un "sitio web"?***
	* Un sitio web es un conjunto de páginas relacionadas, usualmente estáticas, producidas por una organización o persona. Debe operar bajo un único nombre de dominio y estar alojado en al menos un servidor web.
5. ***¿Cómo se define una aplicación web y qué procesos ejecuta para el usuario?***
	* Una aplicación web es un servicio interactivo que procesa lógica, toma decisiones y gestiona información desde el navegador sin instalación local. Interactúa con bases de datos para generar contenido dinámico basado específicamente en las acciones del usuario.
6. ***¿Qué componentes tecnológicos son esenciales para la infraestructura de la web inicial?***
	* La infraestructura básica se apoya en la arquitectura cliente-servidor y requiere del protocolo HTTP para la comunicación. Además, utiliza HTML para especificar la estructura de las páginas, navegadores para visualizarlas y servidores web para alojarlas.
7. ***¿Qué informaciones son necesarias para identificar correctamente una página dinámica mediante una URL?***
	* Para identificar una página dinámica se requiere el nombre de dominio del host, el camino en el sistema de archivos y el nombre del documento. Adicionalmente, es indispensable incluir parámetros (nombre y valor), como se observa en las consultas de formularios.
8. ***¿Cuál es la función de los tipos MIME en la interacción entre el servidor y el navegador?***
	* El tipo MIME es una etiqueta que el servidor envía junto con la página para indicarle al navegador la naturaleza del documento recibido. El navegador utiliza esta información para decidir si puede desplegar el contenido internamente o si requiere un visor externo.
9. ***¿Cuáles son los pasos iniciales que realiza un navegador para resolver la dirección IP de una URL?***
	* El navegador primero revisa su caché local para ver si ya conoce la IP; de lo contrario, envía una consulta DNS vía UDP al puerto 53. El sistema _resolver_ devuelve la dirección IP final, permitiendo que el navegador sepa a qué destino conectarse.
10. ***¿Cómo mejora el uso de una memoria caché el rendimiento de un servidor web?***
	* La caché permite almacenar páginas estáticas directamente en la memoria del servidor, evitando accesos repetitivos al disco físico. Debido a que el acceso a la memoria es significativamente más rápido que al disco, se reduce drásticamente el tiempo de respuesta.
---
# La web parte 2
# Navegador web
### ¿Cuándo llega un documento del servidor web, cómo se sabe el navegador de qué tipo de documento se trata?
Junto con la página que devuelve el servidor indica de que tipo de documento se trata (llamado tipo **MIME**).
El *browser* tiene un **conjunto de tipos MIME integrados** que sabe desplegar.
### ¿Cómo se hace si el tipo MIME de una página no es de los integrados?
Para ver documentos de tipos MIME no integrados se usan **Visores**. Se usa una **Tabla de Tipos MIME** que asocia un tipo MIME con un visor.

### Pasos de la comunicación entre el browser y el servidor web
* Primero consideramos el uso de aplicación que traduce la URL a la dirección IP (DNS). Luego consideramos etapas de uso de TCP.
#### ¿Cuales son los pasos para averiguar el IP de una URL?
* El navegador revisa en el **Caché Local** si ya se conoce el IP del dominio.
* Si no está en caché, se usa UDP para enviar una consulta DNS al **Resolver Configurado**.
* El resolver devuelve usando UDP la **IP** final.
* Entonces el browser va a saber a qué IP conectarse.
#### ¿Cuáles son entonces los pasos usando TCP?
* El cliente inicia conexión TCP con servidor web usando el puerto 80.
* El servidor web acepta la conexión.
* mensajes HTTP de pedido y de respuesta son intercambiados entre el browser y el servidor web usando el canal TCP.
* la conexión TCP se cierra.
# Servidores web
### Tipos de tareas que hace un servidor web
* Procesar pedidos de páginas estáticas.
* Devolver páginas estáticas (accediendo a su sistema de archivos).
* Ejecutar páginas dinámicas (recibe nombre de programa y parámetros).
* Devolver resultados de ejecutar páginas dinámicas.
### ¿Cómo sería un servidor web con un solo hilo de ejecución?
1. Acepta conexión (ACCEPT).
2. Lee solicitud (READ).
3. Procesa la lógica.
4. Genera respuesta (WRITE).
5. Cierra la conexión (CLOSE).
6. Go TO 1.
## Servidores web concurrentes
### Pasos de un servidor web con múltiples hilos para manejar pedidos de páginas estáticas y dinámicas.
1. Llega solicitud al front end, la acepta y construye registro de ella.
2. El front end entrega el registro a uno de los Módulos de Procesamiento (selección por round robin, cola o carga actual).
3. Módulos de Procesamiento determina si es pedido de página estática o dinámica.
4. **Si es pedido de página estática**:
	1. El Módulos de Procesamiento verifica el caché para ver si el archivo está allí.
	2. Si el archivo está en caché: Se lo regresa al cliente.
	3. Si el archivo no está en caché: El Modulo de Procesamiento inicia una operación de disco.
		* Cuando el archivo llega del disco se coloca en la caché y se regresa al cliente.
5. **Si es pedido de página dinámica**:
	1. Prepara el entorno (datos con los que trabaja el programa).
	2. El Modulo de Procesamiento invoca el motor dinámico (ejecuta la página dinámica).
	3. El Modulo de Procesamiento recibe la salida generada y la envía al front end.
6. Modulo de Procesamiento registra el log
# Protocolo para la web
### ¿En qué protocolo de capa de transporte conviene apoyarse para definir un protocolo para la web?.
Conviene TCP ya que una respuesta puede requerir varios paquetes y al fragmentar la información aparecen los problemas que resuelve TCP.
### Solución implementada por HTTP 2.0
* Enviar muchos pedidos y muchas respuestas en paralelo dentro de una misma conexión, intercalando fragmentos ("***frames***") de cada uno.
* El servidor web puede enviar recursos antes de que el cliente los pida.
* El cliente puede indicar prioridades para el envío de recursos.
# Manejo de estado de sesión
### ¿Qué es el estado de sesión de una aplicación web?
Es la información que la aplicación guarda sobre el usuario entre una petición y otra para mantener continuidad, personalización y seguridad en su experiencia.
### ¿Cómo nombrar y dar valor a una información de estado de sesión?
Usando un par: `<Nombre de la información, valor>`.
## Almacenamiento del estado de sesión
### ¿Donde almacenar el estado de sesión?
* Del lado del browser:
	* Las informaciones de estado se llaman *Cookies* y se guardan en un directorio de cookies.
* Del lado del servidor:
	* En base de datos tradicionales (en disco).
	* En memoria RAM, no en disco (esto es mucho mas veloz).
## Duración de estado de sesión
### ¿Cómo expresar la duración de una información de estado de sesión?
Guardar el día y la hora en que va a expirar una información de estado de sesión.
### ¿Qué hacer cuando expira una información de estado de sesión?
El browser puede borrar esa información (borrado periódico).
# Páginas dinámicas
## Lo que vimos
Las Páginas dinámicas son páginas web generadas por programas que se ejecutan del lado del servidor. Puede ser programas en lenguaje de programación o de scripting.
### ¿Cuales son los pasos para generar una página dinámica?
1. El mensaje es entregado a un programa para procesarlo.
2. El programa solicita la información a un servidor de bases de datos.
3. El servidor de bases de datos responde con la información requerida.
4. El programa genera una página HTML personalizada y la envía al cliente.
5. El browser muestra la página recibida al usuario.
### Tareas que hacen las páginas dinámicas
* Procesar parámetros de formularios.
* Procesar información especial del pedido (como el encabezado de pedido HTTP).
* Pedir datos a fuentes de datos.
* Generar páginas web con los datos recibidos.
* Generar feedback e informaciones especiales de respuesta (como encabezados de respuesta HTTP).
## PHP (Preprocesador de Hipertexto)
xd

---
# Preguntas de repaso para la web parte 2
1. ***¿Por qué se prefiere el protocolo TCP sobre otros para sustentar la comunicación en la web?*** 
	* Se prefiere TCP porque garantiza una comunicación confiable, lo cual es crítico dado que una respuesta web puede requerir múltiples paquetes. Al fragmentar la información, surgen problemas de orden y pérdida que TCP resuelve de manera eficiente.
2. ***¿Cuál es la diferencia entre las conexiones no persistentes de HTTP 1.0 y las persistentes de HTTP 1.1?*** 
	* En HTTP 1.0, se utiliza una conexión TCP independiente para cada recurso solicitado, lo que genera una alta sobrecarga. En cambio, HTTP 1.1 permite el uso de una sola conexión persistente para descargar la página y todos sus recursos asociados, procesando los pedidos en orden.
3. ***¿Qué ocurre en una conexión persistente si un recurso (como un video pesado) tarda mucho en descargarse?*** 
	* Se produce un bloqueo donde la respuesta demorada detiene el envío de todas las demás respuestas pendientes en la misma conexión. Para solucionar esto, es necesario alterar el orden de las respuestas o mezclar fragmentos de varias para que los recursos rápidos no esperen por los pesados.
4. ***¿Cómo reduce HTTP 2.0 la latencia percibida por el usuario?*** 
	* Reduce la latencia permitiendo el envío de múltiples pedidos y respuestas en paralelo mediante "frames" intercalados en una sola conexión. Además, permite que el servidor envíe recursos antes de que el cliente los pida y que el cliente asigne prioridades a los recursos más críticos.
5. ***¿Qué información debe contener esencialmente un mensaje de pedido (request) según el protocolo?*** 
	* Un pedido debe incluir una línea de solicitud con el método (como GET o POST), el URL del recurso y la versión del protocolo. Además, incluye líneas de encabezado con información adicional (como el tipo de navegador o el idioma) y, opcionalmente, un cuerpo con datos de formularios o documentos.
6. ***¿Qué es el estado de sesión y por qué es necesario si HTTP es un protocolo "sin estado"?*** 
	* El estado de sesión es la información que una aplicación guarda sobre el usuario (como el contenido de un carrito de compras) para dar continuidad a su experiencia entre distintas peticiones. Es necesario porque HTTP no recuerda nada de una petición a otra, y las aplicaciones requieren memoria para funciones como la autenticación o personalización.
7. ***¿Cuáles son las opciones de almacenamiento para el estado de sesión del lado del servidor?*** 
	* Puede almacenarse en bases de datos relacionales tradicionales (en disco) o, para mayor velocidad, en memoria RAM utilizando sistemas como Redis. En el caso de Redis, el servidor genera un ID de sesión que se envía al cliente en una cookie, vinculando ese ID con un objeto de información en la memoria del servidor.
8. ***¿Qué problemas de seguridad surgen si no se controla la duración de la información de sesión?*** 
	* La falta de expiración permite que usuarios que ya no deberían estar autenticados mantengan el acceso o que terceros roben y utilicen información de sesión antigua. Además, puede causar inconsistencias donde la interfaz del cliente cree que el usuario está logueado pero el servidor ya ha rechazado la sesión.
9. ***¿Cuál es el flujo de trabajo básico para generar una página dinámica en el servidor?*** 
	* Primero, el mensaje de pedido se entrega a un programa (como uno en PHP), el cual solicita datos específicos a un servidor de bases de datos. Una vez recibida la información, el programa genera una página HTML personalizada y la envía como respuesta al navegador para que este la muestre al usuario.
10. ***¿Qué capacidades sintácticas debe tener un lenguaje de scripting como PHP para funcionar en la web?*** 
	* Debe poseer sintaxis para acceder a parámetros de formularios, leer y definir encabezados de respuesta HTTP y manejar información de estado de sesión (cookies). También debe permitir la inserción de comandos lógicos dentro de la estructura HTML para mostrar datos dinámicos.
---
# Introducción Capa de transporte
# Capa de transporte
## Propósito y funciones principales
### ¿Qué hace la capa de transporte?
* Provee **Comunicación Lógica entre Procesos** de aplicación en diferentes hosts. Es decir, los procesos se comunican como si estuvieran directamente conectados.
* **Comunica procesos**
* Mejora los servicios de la capa de red
	* Retransmite paquetes pedidos en redes no orientadas a la conexión
	* Regula la tasa de transmisión ante congestión en la red.
## Ubicación y componentes
### ¿Dónde se ejecuta la capa de transporte?
Se ejecuta por completo en los ***Hosts***/sistemas finales (computadoras de origen y destino).
### Relación con la capa de red
* La capa de transporte depende de la capa de red (IP) para mover paquetes entre hosts, pero agrega: **Entrega confiable, control de flujo y multiplexación**.
### Entidades de transporte (ET)
* Es el software (o hardware) que implementa la lógica de la capa de transporte en cada hosts.
## Problemas que resuelve
* **Confiabilidad**: Uso de temporizadores y retransmisiones para recuperar paquetes perdidos.
* **Direccionamiento**: Identificar el proceso correcto en el hosts destino.
* **Eficiencia**: Uso de búferes para una comunicación confiable sin desperdiciar recursos.
* **Control de Flujo**: Evitar que un emisor rápido sature a un receptor lento.
* **Control de congestión**: Regular la cantidad de paquetes inyectados a la red: Evitar que los búferes de los nodos intermedios se saturen y pierdan (hagan *drop* de) paquetes.
	* Si la capa de red pierda paquetes por congestión, la capa de transporte puede compensarlo.
## Segmento y encapsulamiento
* **Segmento**: Unidad de datos del protocolo de transporte (TDPU).
	* Los segmentos se encapsulan en **Paquetes** (Capa de red) y luego en **Tramas** (Capa de enlace).
* **Encapsulamiento**: Agregar un encabezado de la capa inferior al contenido de la capa superior, como una carta dentro de un sobre dentro de una caja.
* **Confirmaciones de Recepción** (ACK): El receptor notifica al emisor que los datos llegaron bien.
## Entrega confiable y ordenada
### Problema
La red no garantiza orden ni entrega:
* La capa de red puede **Perder Paquetes**, **Duplicarlos** o entregarlos **Fuera de Orden**.
La capa de transporte debe solucionar esto y es responsable de garantizar:
* **Entrega Efectiva** de los segmentos al host destino.
* **Entrega Ordenada**: Que los datos lleguen en el mismo orden en que fueron enviados por la capa de aplicación.
Lo logra mediante mecanismos de entrega confiable
## Mecanismos de entrega confiable
### Solución
1. El emisor asigna un **Número de Secuencia** a cada segmento, respetando el orden del flujo de datos.
2. Al enviar un segmento, dispara un **Temporizador de Retransmisión**.
3. El receptor envía **Confirmaciones de Recepción** (ACK) por cada segmento recibido correctamente.
4. Si el temporizador expira sin recibir un ACK, entonces el emisor **Retransmite** el segmento.
5. El receptor **Reensambla en Orden** los segmentos recibidos y los entrega a la capa de aplicación.
# TCP
### ¿Qué es TCP?
TCP es El **protocolo de transporte** más usado en internet. Garantiza que los datos lleguen **Completos**, en **Orden** y **Sin Errores**, incluso cuando la red debajo (IP) no lo garantiza.
### Entidad TCP (ETCP)
Software en cada host que implementa TCP (generalmente en el kernel del SO).
## Problemas que resuelve
* **Retransmisión de paquetes**: Mediante números de secuencia, ACKs y temporizadores.
* **Duración de temporizadores**: Algoritmos complejos para fijar tiempos de espera adaptativos.
* **Manejo de conexiones**: Establecer y terminar conexiones entre pares de procesos.
* **Direccionamiento**: Identificar procesos en hosts remotos mediante puertos.
* **Control de congestión**: Regular la inyección de paquetes para no saturar la red.
* **Control de Flujo**: Evitar que un emisor rápido desborde a un receptor lento.
## Socket y conexiones
### Socket
Para usar TCP, tanto el cliente como el servidor crean **Sockets** (puntos de acceso al servicio).
* `Direccion de socket = direccion IP + numero de puerto`.
### Conexiones
* Se debe **establecer una conexión** explícita entre socket emisor y uno receptor.
* Un mismo socket puede tener **Múltiples conexiones** simultáneas.
* Cada conexión se identifica por el par: `(socket_origen, socket_destino)`.
## Número de secuencia y segmento
### Números de secuencia en TCP
Cada byte del flujo de datos tiene su propio **Número de Secuencia** de **32 bits**. Esto impone un límite teórico al tamaño del flujo: $2^{32} = ~4GB$.
### ¿Para qué sirven los números de secuencia?
Permiten al receptor enviar ACK precisas. También permiten detectar datos faltantes, duplicados y desordenados.
### Estructura de un segmento
```
Segmento = encabezado TCP + datos (0 o más bytes)
```
## Tamaño máximo de segmento (MTU)
### ¿Qué limita el tamaño de un segmento TCP?
* **Límite IP**: Cada segmento debe caber en la carga útil del datagrama IP en la capa de red (máx. 65.515 bytes).
* **MTU (Unidad Máxima de Transferencia)**: Cada red física impone un límite al tamaño de trama. El segmento debe caber en la MTU.
	* En la práctica, la MTU suele ser de 1500 bytes (carga útil de Ethernet).
### En resumen
El tamaño máximo de segmento (MSS) es el menor entre el límite IP y la MTU de la red, descontando encabezados. En Ethernet: MSS típico ~ 1460 bytes (`1500 - 20 IP - 20 TCP`).
## Temporizadores y retransmisiones
### Problema: IP no garantiza la entrega
La capa de red (IP) no garantiza que los datagramas se entregarán, ni que llegarán correctamente.
### Solución de TCP: ACKs + temporizadores
* Si un segmento llega correctamente, entonces el receptor envía un ACK (confirmación). 
* Si el **Temporizador** expira sin recibir ACK, entonces TCP retransmite el segmento.
* TCP es responsable de gestionar los temporizadores y ejecutar las retransmisiones según sea necesario.
## Reordenamiento de segmentos
### Problema: Los datagramas pueden llegar fuera de orden
En redes de datagramas, cada paquete puede tomar rutas diferentes y llegar en orden distinto al de envío.
### Solución de TCP
TCP usa los números de secuencia para reensamblar los segmentos en la secuencia correcta antes de entregarlos a la aplicación.
# Segmento TCP
## Estructura del encabezado
### Todos los segmentos TCP tiene 3 partes:
1. **Encabezado Fijo**: 20 bytes. Contiene puertos, ACK, flags y control de flujo.
2. **Opciones**: Longitud variable (en palabras de 32 bits). Negocian parámetros como MSS.
3. **Datos**: Opcionales. Un segmento puede ser solo encabezado.
![](EstructuraDeUnSegmento.png)
## Puertos y direccionamiento
### Segmento sin datos
Se usan para enviar ACK y Mensajes de control (SYN, FIN). Solo contienen el encabezado TCP, sin datos de aplicación.
### Puerto de origen y puerto de destino
* Cada uno ocupa 16 bits (valores de 0 a 65.535).
	* Puertos conocidos: 80 (HTTP), 443 (HTTPS), 22 (SSH), 25 (SMTP).
* `IP + Puerto`: Socket (punto terminal único de 48 bits que identifica un proceso en un host).
* El par `(socket_origen, socket_destino)` identifica de forma única cada conexión TCP.
# Encabezado TCP
## Campos de secuencia y ACK
### Campo: Numero de secuencia (32 bits)
* Identifica la posición del primer byte de datos del segmento dentro del flujo de bytes total.
	* Por ejemplo, si el flujo empieza en byte 0 y un segmento contiene los bytes 1000-1499, su número de secuencia es 1000.
### Campo: Número de confirmación (ACK number, 32 bits)
* Indica el siguiente byte que el receptor espera recibir.
	* Como por ejemplo, si recibió correctamente hasta el byte 1499, el ACK number será 1500.
* Confirma implícitamente todos los bytes anteriores (ACK acumulativo).
## Flag ACK
### Flag ACK (1 bit en el encabezado)
* Si `ACK = 1`: El campo "número de confirmación" es válido, entonces el segmento confirma datos recibidos.
* Si `ACK = 0`: El campo se ignora, entonces no hay confirmación en este segmento.
### Piggybacking: ACKs "gratis" con datos
En la práctica, casi todos los segmentos (excepto el primer SYN) llevan `ACK = 1`, porque TCP aprovecha cada envío para confirmar lo recibido.
### Ejemplo
A envía datos (`seq = 1000`, 400 bytes), entonces *B* responde con datos proprios y `ACK = 1500` en el mismo segmento, entonces confirma los 500 bytes de *A* sin un paquete extra.
Longitud y opciones
### Campo: Longitud del encabezado (4 bits)
Indica el número de palabras de 32 bits en el encabezado TCP. Es necesario porque el encabezado tiene tamaño variable (por las opciones).
* Mínimo: 5 palabras = 20 bytes (encabezado fijo, sin opciones).
* Máximo: 15 palabras = 60 bytes (con opciones llenas).
### Campo de opciones (longitud variable)
* Permite negociar parámetros entre emisor y receptor al inicio de la conexión.
	* MSS: Tamaño máximo de segmento que acepta cada lado.
	* Windows Scale: Ampliar la ventana de recepción más allá de 64 KB.
	* SACK: Confirmaciones selectivas (confirmar bloques no contiguos).
* Máximo espacio para opciones: 40 bytes (60 - 20 del encabezado fijo).
## Flags del encabezado
1. **URG** (*urgent*): Indica que el segmento contiene datos urgentes que deben procesarse de inmediato. El campo ***Urgent Pointer*** acompaña este indicador y señala la posición en el flujo de datos donde terminan los datos urgentes.
2. **PSH** (*Push*): Sirve para pedir al receptor que procese y entregue los datos inmediatamente al nivel superior (aplicación), en lugar de esperar a completar el buffer. Esto se usa en escenario donde la inmediatez es clave.
3. **RST** (*Reset*): Se utiliza para reiniciar una conexión. Se puede enviar, por ejemplo, cuando hay un error crítico en la comunicación o se quiere rechazar una conexión no deseada.
4. **Urgent Pointer**: Completamente el indicador URG. Su propósito es especificar la ubicación del último byte de datos urgentes dentro del segmento.
5. **CWR** (*Congestion Window Reduced*) y ECE (*Explicit Congestion Notification Echo*): Relacionados con el manejo de congestión en la red. CWR indica que el transmisor ha reducido su ventana de congestión. ECE señala que el receptor ha detectado congestión a través de notificación explícita.
# Ejercicios de las filminas
## Ejercicio 1: Verdadero o Falso
La capa de transporte se ejecuta tanto en los host como en los routers intermedios.
**Falso**. Se ejecuta solo en los hosts. Los routers solo reenvían paquetes sin inspeccionar la capa de transporte.
## Ejercicio 2: Verdadero o Falso
TCP de los datos de la aplicación como mensajes individuales y los transmite respetando sus limites.
**Falso.** No se conoce los límites de los mensajes de la aplicación: Ve un flujo continuo de bytes (*Stream*) y lo fragmenta en segmentos según convenga.
## Ejercicio 3: Multiple Choice
¿Que es un Segmento en el contexto de TCP?
1. Un paquete de la capa de red.
2. La unidad de datos del protocolo de transporte
3. Una trama de la capa de enlace.
4. Un datagrama IP completo
**(2)** El segmento es la unidad de datos de la capa de transporte. Se encapsula dentro de un paquete IP (capa de red).
## Ejercicio 4: Completar
El campo longitud del encabezado tiene 4 bits. ¿Cuál es el número máximo de palabras de 32 bits que puede tener el encabezado TCP?
Con 4 bits el valor máximo es $2^{4}-1 = 15$ . Eso equivale a $15*4 = 60$ bytes.
## Ejercicio 5: Completar
Si el encabezado fijo de TCP mide 20 bytes y el máximo es de 60 bytes, ¿Cuántos bytes pueden ocupar las opciones como máximo?
**40 bytes**. 60 bytes (máximo total) - 20 bytes (encabezado fijo) = 40 bytes para opciones.
## Ejercicio 6: Verdadero o Falso
un socket se identifica únicamente con el número de puerto.
**Falso**. Un `socket = direccion IP + Puerto`. Dos hosts distintos pueden usar el mismo puerto (como por ejemplo, ambos en puerto 80), pero sus sockets son diferentes porque tienen distintas IP.
## Ejercicio 7: Completar
Un host recibió correctamente todos los bytes de un flujo TCP hasta el byte 4999. ¿Qué valor tendrá el campo ACK number en su próximo segmento?
**5000**. El ACK number indica el siguiente byte esperado. Si recibió hasta el 4999 espera el 5000.
## Ejercicio 8: Verdadero o Falso
Si el flag ACK vale 0, el campo número de confirmación contiene un valor válido.
**Falso**. Cuando `ACK = 0`, el campo se ignora. Solo es válido cuando `ACK = 1`.
## Ejercicio 9: Completar
Un enlace tiene MTU de 1500 bytes. IP ocupa 20 bytes y TCP ocupa 20 bytes. ¿Cuál es el MSS?
**1460 bytes**. MSS = MTU - IP - TCP = 1500 - 20 - 20 = 1460 bytes.
## Ejercicio 10: Multiple Choice
¿Qué flag del encabezado TCP se utiliza para iniciar una conexión?
1. FIN
2. RST
3. SYN
4. PSH
**(c)** SYN (*Synchronize*) se usa para solicitar el establecimiento de una conexión TCP.
## Ejercicio 11: Multiple Choice
¿Para qué sirve la opción MSS en el encabezado TCP?
1. Indicar el tamaño de la ventana de congestión.
2. Negociar el tamaño máximo de segmento que acepta cada lado.
3. Definir el número de secuencia inicial.
4. Establecer el tiempo de vida del paquete.
**(b)** MSS (*Maximum Segment Size*) se negocia al inicio de la conexión para que cada lado informe el tamaño máximo de datos que puede recibir por segmento.
## Ejercicio 12: Verdadero o Falso
Una conexión TCP se identifica de forma única por un solo socket (IP + Puerto)
**Falso**. Una conexión TCP se identifica por el par `(socket_origen, socket_destino)`, o sea, dos sockets: Uno en cada extremo.

---
# Preguntas de repaso para la Introducción a la capa de transporte
1. ***¿Cuál es la diferencia fundamental entre la comunicación que provee la capa de red y la que provee la capa de transporte?***
	* La capa de red se encarga de la comunicación entre hosts (computadoras), mientras que la capa de transporte provee una comunicación lógica entre procesos específicos de aplicación. Esto permite que los procesos se comuniquen como si estuvieran conectados directamente, independientemente de la infraestructura física subyacente.
2. ***¿En qué dispositivos de la red se ejecuta la capa de transporte y qué implicancia tiene esto para los nodos intermedios?***
	* La capa de transporte se ejecuta exclusivamente en los hosts o sistemas finales (emisor y receptor). Los routers y switches intermedios no participan en esta capa, limitándose a reenviar paquetes sin inspeccionar el contenido del protocolo de transporte.
3. ***¿Qué es un "segmento" y cómo se relaciona con el proceso de encapsulamiento?***
	* El segmento es la unidad de datos del protocolo de transporte (TPDU). Este se encapsula dentro de un paquete en la capa de red, el cual a su vez se introduce en una trama en la capa de enlace, similar a una carta dentro de sucesivos sobres.
4. ***¿De qué manera TCP maneja los datos recibidos desde la capa de aplicación?***
	* TCP no reconoce los límites de los mensajes individuales de una aplicación, sino que los visualiza como un flujo continuo o "stream" de bytes. Para su transmisión, TCP fragmenta este flujo en segmentos de tamaño manejable, usualmente limitados por la capacidad de la red.
5. ***¿Qué elementos componen un socket y cuál es su función en la red?***
	* Un socket es un punto de acceso al servicio que se define mediante la combinación de una dirección IP y un número de puerto de 16 bits. Funciona como un punto terminal único que identifica un proceso específico dentro de un host determinado.
6. ***¿Cómo funciona el mecanismo de temporizadores para garantizar la entrega confiable de datos?***
	* Al enviar un segmento, el emisor inicia un temporizador de retransmisión. Si el emisor no recibe una confirmación (ACK) del receptor antes de que el temporizador expire, el protocolo asume que el segmento se perdió y procede a retransmitirlo.
7. ***Cuál es la función primordial de los números de secuencia en el protocolo TCP?***
	* Los números de secuencia permiten al receptor reensamblar los segmentos en el orden correcto y detectar si faltan datos o si hay duplicados. Además, son la base para que el receptor pueda enviar confirmaciones de recepción precisas indicando qué bytes han llegado exitosamente.
8. ***¿Qué diferencia existe entre la Unidad Máxima de Transferencia (MTU) y el Tamaño Máximo de Segmento (MSS)?***
	* La MTU es el límite físico del tamaño de una trama impuesto por la red (ej. 1500 bytes en Ethernet). El MSS es la cantidad máxima de datos que puede contener un segmento TCP, calculado restando los encabezados de IP y TCP de la MTU disponible.
9. ***¿En qué consiste la técnica de "piggybacking" dentro del contexto de las confirmaciones de recepción?***
	* El piggybacking es la práctica de incluir una confirmación de recepción (ACK) dentro de un segmento que ya transporta datos en el sentido inverso. Esto optimiza el uso de la red al evitar el envío de paquetes de control puros cuando existe comunicación bidireccional activa.
10. ***¿Cómo identifica TCP de forma única una conexión entre dos sistemas finales?***
	* TCP identifica cada conexión mediante un par de sockets: el socket de origen y el socket de destino. Esta combinación cuádruple (IP origen, puerto origen, IP destino, puerto destino) garantiza que cada flujo de datos sea único, incluso si un mismo socket tiene múltiples conexiones simultáneas.
---
# Direccionamiento
## Problemas
* El **direccionamiento** explícito de los destinos.
	* ¿Cómo hacer para que un proceso servidor adecuado atienda a las necesidades de una máquina cliente?
	* Consideramos los casos:
		* El cliente podría no saber cuál proceso servidor es el adecuado para un servicio particular.
		* El cliente podría saber cuál proceso servidor es adecuado, pero dicho proceso servidor no atiende porque esta inactivo.
* El cliente necesita un tipo de servicio.
	* Sin embargo no sabe cuál proceso servidor es adecuado para contactar.
	* Asumimos que los procesos están activos.
## Solución
Existe un proceso especial llamado **Servidor de Directorio** que para cada tipo de servicio sabe cuáles son los puertos de los servidores que prestan ese tipo de servicio.
Pasos seguidos:
1. El usuario establece una conexión con el servidor de directorio (que escucha en un puerto bien conocido).
2. El usuario envía un mensaje especificado el nombre del servicio.
3. El servidor de directorio le devuelve la dirección puerto.
4. El usuario libera la conexión con el servidor de directorio y establece una nueva con el servicio deseado.
# Caso 2: El servidor inactivo => protocolo inicial de conexión
## Problema
El cliente conoce el proceso servidor adecuado, pero este se encuentra **Inactivo** y no puede responder a solicitudes.
* El servidor existe en la máquina remota, pero su proceso no está corriendo en ese momento.
* Se necesita un mecanismo para **Activar** al servidor bajo demando cuando llega una solicitud.
## Solución
Usar un **Servidor de Procesos** (**Protocolo inicial de conexión**), un intermediario que activa servidores inactivos cuando se los necesita.
* El servidor de procesos escucha en los puertos de servidores de menor uso que están inactivos.
* Cuando recibe una solicitud, **genera** (*fork*) el servidor correspondiente y le transfiere la conexión del cliente.
* Los pasos detallados se muestran en las figuras de las siguientes diapositivas.
# Protocolo inicial de conexión
## Pasos 1 y 2
1. **Escucha Múltiple**: El servidor de procesos escucha en un grupo de puertos simultáneamente, esperando solicitudes de conexión entrantes de clientes.
2. **Solicitud *CONNECT***: Un cliente emite una solicitud CONNECT, especificando el número de puerto del servicio que desea utilizar.
## Pasos 3 y 4
3. **Redirección**: Si no hay ningún servidor activo esperando en ese puerto, la solicitud llega al servidor de proceso, quien obtiene la conexión.
4. **Generación** (*fork*): El servidor de procesos genera (*fork*) el servidor solicitando y le permite **heredar la conexión** existente con el  cliente, de modo que la comunicación continúa sin interrupción.
## Paso 5 (Final)
5. **Servicio Activo**: El nuevo servidor recientemente activado realiza el trabajo solicitado por el cliente. Mientras tanto, el servidor de procesos vuelve a escuchar en los puertos, listo para atender nuevas solicitudes entrantes.
# Ejercicios de las filminas
## Preguntas
### ¿Cuál es la diferencia fundamental entre la solución del servidor de procesos y la solución del servidor de directorio?
El servidor de directorio resuelve el problema de **Descubrimiento**: El cliente no sabe en qué puerto escucha el servicio, y el directorio le indica la dirección. Asume que el servidor ya está activo.
El servidor de procesos resuelve el problema de **Activación**: El cliente conoce el puerto, pero el servidor está activo, y el servidor de procesos lo genera (fork) bajo demanda y le transfiere la conexión.
### ¿En qué escenario haría falta combinar ambas soluciones?
Cuando el cliente no conoce el puerto del servicio y además el servidor está inactivo. Primero se consulta al servidor de directorio para descubrir el puerto, y luego el servidor de procesos se encarga de activar el Demonio correspondiente para atender la solicitud. Se combinan ambos mecanismos en secuencia: Descubrimiento + activación.
# Direccionamiento en TCP
## Puertos Bien Conocidos (Well-Known Ports)
Son los puertos con números menores a 1024, reservados para servicios estándar del sistema.
![](PuertosBienConocidos.png)
## Demonios (daemons)
Son procesos servidores que se ejecutan en segundo plano y se asocian a un puerto específico en el momento del arranque del sistema.
* Como por ejemplo: El demonio FTP se conecta al **puerto 21** durante el arranque y queda escuchando solicitudes de transferencia de archivos.
# Demonio inetd, solución al consumo de memoria
## Problema
Si cada servicio tiene su propio demonio ejecutándose permanentemente, se podría llenar la memoria con demonios que están activos la mayor parte del tiempo, desperdiciando recursos del sistema.
## Solución
Un único demonio llamado **inetd** (***Internet Daemon***) reemplaza a múltiples demonios individuales. Este proceso:
* Escucha simultáneamente en un conjunto de puertos y espera solicitudes de conexión entrantes.
* Cuando un cliente emite un pedido CONNECT a un puerto específico, inetd verifica si ya hay un servidor escuchando en ese puerto.
* Si no hay servidor activo, inetd bifurca (fork) un nuevo proceso, ejecuta el demonio apropiado en él, y ese demonio maneja la solicitud del cliente.
# Configuración de inetd y demonios permanentes
## Archivo de configuración de inetd
inetd determina qué puertos debe supervisar a partir de un archivo de configuración (generalmente `/etc/inetd.conf`). Este archivo lista los servicios disponibles, sus puertos y los programas a ejecutar.
* Los demonios asociados a estos puertos solo se activan cuando hay trabajo, ahorrando memoria y recursos del sistema.
## Estrategia híbrida: Demonios permanentes + inetd
En la práctica se usa una estrategia combinada:
* **Demonios permanente** es los puertos más utilizados (como por ejemplo, HTTP puerto 80, SSH puerto 22), porque el costo de tenerlos siempre activos se justifica por su alta demanda.
* **inetd** gestiona los demás servicios de menor uso, activándolos bajo demanda para ahorrar recursos.
Esta decisión la toma el administrador del sistema, según el perfil de uso de cada servidor.
# Ejercicios de las filminas
## Ejercicio 1: Análisis de Escenarios
Para cada situación, indicar qué mecanismo se necesita: Servidor de directorio, servidor de procesos, ambos o ninguno. Justificar.
1. Un navegador web quiere conectarse al servidor HTTP de una máquina remota. Sabe que HTTP usa el puerto 80, y el servidor está activo permanentemente como demonio.
	* **Ninguno**. El cliente ya conoce el puerto (80 es *well-known*) y el servidor está activo como demonio permanente. No se necesita descubrimiento ni activación.
2. Una aplicación necesita un servicio de impresión remota. Sabe que el servicio existe en la máquina destino pero desconoce en qué puerto escucha.
	* **Servidor de Directorio**. El problema es de descubrimiento: El cliente no sabe el puerto. Consulta al directorio, que le devuelve la dirección del servicio de impresión. El servicio se asume activo.
3. Un cliente quiere usar el servicio (puerto 79) en un servidor Unix. El servicio está configurado en `/etc/inetd.conf` pero no hay ningún demonio finger corriendo actualmente.
	* **Servidor de Procesos** (inetd). El problema es de activación: El cliente conoce el puerto 79, pero el demonio está inactivo. inetd escucha en ese puerto, recibe la solicitud, genera (fork) el proceso finger y le transfiere la conexión.
4. Un proceso cliente necesita un servicio personalizado que fue recién instalado en el servidor. No conoce su puerto, y el servicio solo se activa bajo demanda mediante inetd.
	* **Ambos**. Hay dos problemas  simultáneo: No conoce el puerto (=> directorio) y el servicio se activa bajo demanda (=> servidor de procesos/inetd). Primero consulta al directorio para descubrir el puerto, luego inetd activa el servicio al recibir la conexión.
5. Un administrador tiene un servidor con 50 servicios distintos pero solo 5 se usan frecuentemente. ¿Qué estrategia de direccionamiento/activación debería implementar para optimizar el uso de memoria?
	* **Estrategia Híbrida**. Los 5 servicios frecuentes se configuran como demonios permanentes (evitar la latencia de fork). Los 45 restantes se gestionan con inetd para ahorrar memoria, activándolos solo bajo demanda.
## Ejercicio 2: Verdadero o Falso
Indicar si cada afirmación es Verdadera o Falsa. Si es falsa, corregirla.
1. El servidor de directorio y el servidor de procesos resuelven el mismo problema de direccionamiento.
	* **Falso**. Resuelven problemas diferentes: El directorio resuelve el descubrimiento del puerto; el servidor de procesos resuelve la activación del proceso servidor inactivo.
2. Los puertos bien conocidos (well-know ports) son aquellos con números menores a 1024, reservados para servicios estándar.
	* **Verdadero**. Los puertos 0-1023 están reservados por IANA para servicios estándar (por ejemplo HTTP = 80, FTP = 21, SSH = 22).
3. El demonio inetd necesita tener todos los servidores cargados en memoria permanentemente para poder redirigir las solicitudes.
	* **Falsa**. Justamente lo contrario: inetd evita tener los servidores en memoria. Solo está activo, escuchando en múltiples puertos, y genera (fork) cada servidor bajo demanda cuando llega una solicitud.
4. En el protocolo inicial de conexión, el servidor de procesos genera (fork) el servidor solicitando y le permite heredar la conexión existente con el cliente.
	* **Verdadera**. El servidor de procesos hace fork, creando un proceso hijo que hereda los descriptores de archivo (incluido la conexión TCP con el cliente), y el padre vuelve a escuchar.
5. Para registrar un nuevo servicio en un servidor de directorio, basta con que el cliente envíe una solicitude de conexión al puerto deseado.
	* **Falsa**. El registro lo hace el servidor (no el cliente): El nuevo servicio debe enviar su identificador y puerto al servidor de directorio para que este lo incluya en su tabla de búsqueda.
6. En la práctica, todos los servicios de un servidor Unix deberían ser gestionados por inetd para minimizar el consumo de memoria.
	* **Falso**. La estrategia óptima es híbrida: Los servicios de alto tráfico (HTTP, SSH) deben ser demonios permanentes para evitar la latencia de fork; solo los servicios esporádicos se gestionan con inetd.
7. El servidor de directorio escucha en un puerto bien conocido para que los clientes puedan conectarlo sin necesitar otro directorio.
	* **Verdadera**. Si el propio directorio no tuviera un puerto conocido, se crearía un problema de recursividad infinita. Por eso el directorio siempre usa un puerto well-know.
## Ejercicio 3: Diseño de Solución
### Situación
Una universidad tiene un servidor central con 30 servicios disponibles para los alumnos (correo, FTP, SSH, base de datos, impresoras, consulta de notas, etc.). Solo 4 servicios se usan constantemente (correo, SSH, DNS, HTTP). Los demás se usan esporadicámente. Se pide diseñar la estrategia de direccionamiento y activación del servidor, respondiendo:
1. **Clasificar**: ¿Cuáles servicios configuraría como demonios permanentes y cuáles gestionaría mediante inetd? Justificar el criterio utilizado.
	* **Demonios permanentes**: Correo, SSH, DNS y HTTP (alto tráfico => evitar latencia de fork en cada solicitud). **Gestionados por inetd**: Los 26 restantes (FTP, bases de datos, impresoras, consulta de notas, etc.) => uso esporádico, no justifica ocupar memoria permanentemente. Criterio: Frecuencia de uso y tiempo de respuesta aceptable.
2. **Directorio**: Un alumno quiere usar el servicio de "consulta de notas" pero no conoce su puerto. ¿Qué pasos seguiría el sistema para resolver esta solicitud?
	1. El cliente del alumno contacta al servidor de directorio (puerto well-know).
	2. Envía un lookup con el identificador "consulta de notas".
	3. El directorio busca en su tabla y responde con el puerto asignado.
	4. El cliente se conecta a ese puerto.
	5. Como está gestionado por inetd, este recibe la conexión, hace fork del proceso y le transfiere la conexión.
3. **Nuevo Servicio**: Si se agrega un servicio de "reserva de aulas", ¿Qué pasos debe realizar el administrado para que los clientes puedan descubrirlo y utilizarlo?
	1. Asignar un puerto de servicio "reserva de aulas".
	2. Agregar una entrada en `/etc/inetd.conf` con el puerto, protocolo y ruta del ejecutable.
	3. Registrar el servicio en el servidor de directorio (enviar identificador + puerto).
	4. Reiniciar inetd para que lea la nueva configuración. Los clientes podrán descubrirlo vía directorio y será activado bajo demanda por inetd.
4. **Diagrama**: Dibujar el flujo completo de mensajes entre el cliente, el servidor de directorio e inetd cuando un alumno accede al servicio de "consulta de notas" por primera vez.
	* Cliente -> Srv. Directorio: "¿puerto de consulta de notas?" -> Directorio responde: "Puerto N" -> Cliente se conecta a puerto N -> inetd (escuchando en N) acepta conexión -> inetd hace fork del proceso "consulta_notas" -> Hijo hereda conexión y atiende al cliente -> inetd vuelve a escuchar.
---
# Preguntas de repaso para Direccionamiento
1. ***¿Cuál es la función principal de un servidor de directorio en el contexto del direccionamiento?***
	* El servidor de directorio resuelve el problema de descubrimiento de servicios. Su función es informar al cliente cuál es el puerto específico en el que escucha un servidor que presta un tipo de servicio determinado, asumiendo que dicho servidor ya está activo.
2. ***¿Qué pasos debe seguir un usuario para obtener la dirección de un servicio a través de un servidor de directorio?***
	* El usuario establece conexión con el servidor de directorio en un puerto conocido, envía el nombre del servicio deseado y recibe la dirección del puerto correspondiente. Finalmente, libera la conexión con el directorio y establece una nueva con el servicio solicitado.
3. ***¿Qué problema específico busca resolver el Protocolo Inicial de Conexión mediante un servidor de procesos?***
	* Busca resolver el problema de servidores inactivos. Permite activar un servidor bajo demanda cuando llega una solicitud de un cliente que ya conoce el puerto, pero cuyo proceso servidor no está corriendo en la máquina remota en ese momento.
4. ***Explique brevemente el proceso de "fork" realizado por un servidor de procesos al recibir una solicitud.***
	* Al recibir una solicitud `CONNECT` en un puerto supervisado, el servidor de procesos genera (mediante un "fork") un nuevo proceso para el servidor solicitado. Este nuevo proceso hijo hereda la conexión del cliente para atender la solicitud, mientras el padre vuelve a su estado de escucha.
5. ***¿Qué son los "puertos bien conocidos" (well-known ports) y qué entidad los asigna?***
	* Son puertos con números menores a 1024 reservados para servicios estándar del sistema (como HTTP en el puerto 80 o FTP en el 21). Estos números son asignados oficialmente por la IANA (Internet Assigned Numbers Authority).
6. ***¿Cuál es la ventaja principal de utilizar el demonio** **inetd** **en lugar de múltiples demonios individuales?***
	* El demonio `inetd` optimiza el uso de memoria y recursos del sistema al evitar que múltiples demonios inactivos estén cargados permanentemente. Actúa como un único intermediario que solo activa los servidores específicos cuando existe una demanda real de trabajo.
7. ***¿Cómo determina** **inetd** **qué puertos debe supervisar y qué programas debe ejecutar?***
	* `inetd` utiliza un archivo de configuración, generalmente ubicado en `/etc/inetd.conf`. Este archivo contiene una lista detallada de los servicios disponibles, sus puertos asociados y las rutas de los programas ejecutables que deben iniciarse.
8. ***Describa la "estrategia híbrida" de gestión de demonios y cuándo se recomienda aplicar cada parte.***
	- Consiste en mantener demonios permanentes para servicios de alta demanda (como HTTP o SSH) para evitar la latencia del "fork", mientras se deja a `inetd` la gestión de servicios esporádicos. La decisión depende del perfil de uso definido por el administrador.
9. ***¿Por qué es necesario que un servidor de directorio escuche en un puerto bien conocido?***
	* Se utiliza un puerto bien conocido para evitar un problema de recursividad infinita. Si el servidor de directorio no tuviera una ubicación predefinida y conocida, el cliente necesitaría otro servicio de directorio previo para encontrarlo.
10. ***En una arquitectura de red, ¿en qué escenario se requeriría la combinación de un servidor de directorio y un servidor de procesos?***
	* Se requiere cuando el cliente desconoce el puerto del servicio (necesita descubrimiento) y, además, el servidor se encuentra inactivo (necesita activación). Primero se consulta al directorio para obtener el puerto y luego el servidor de procesos activa el demonio al recibir la conexión.
---
# Transferencia de datos confiables
# Entrega de datos confiable
Se necesita un **Protocolo de Entrega Confiable** que defina reglas precisas para el emisor y receptor.
### ¿Por qué hay varios protocolos?
Porque el protocolo óptimo depende de la características de la red:
* **Latencia**, cuántas tarda un paquete en llegar.
* **Tasa de Errores**, qué tan frecuente es la pérdida/corrupción
* **Capacidad**, tasa de datos que puede manejar la red.
## Problema de paquetes duplicados
### Problema
Se perdió un ACK, el emisor retransmite, pero el paquete original sí llegó al receptor. El receptor recibe el mismo paquete dos veces y lo entrega a la aplicación duplicado.
### Solución
* Cada paquete lleva un Número de Secuencia Único.
* Cuando llega un paquete, el receptor revisar si ya recibió ese número.
* Si es **duplicado**, entonces lo **descarta**. Si es **nuevo**, entonces lo **entrega** a la aplicación.
# Protocolo de parada y espera
### Situación
La latencia de la red es Baja
* El **RTT** (***Round Trip Time*** o **Tiempo de Ida y Vuelta**) es bajo comparado con el tiempo de transmitir un paquete.
* **Consecuencias**: El ACK llega antes de que se termine de preparar el siguiente paquete.
* Entonces solo se puede tener un paquete "en vuelo" a la vez.
### Parada y espera
* Envía un paquete y se detiene hasta recibir confirmación.
* Si llega el ACK, entonce envía el siguiente paquete.
* Si expira el temporizador, entonces retransmite el mismo paquete.
* Es el protocolo más simple, ideal cuando RTT es bajo.
## Protocolo de parada y espera
### Mecanismos usados
* **Número de secuencia**: 1 bit basta (alterna 0/1) para detectar duplicados.
* **ACKs**: Incluyen el Número de secuencia del paquete confirmado.
* **Temporizadores**: Detectan pérdidas y disparan retransmisiones.
### Diagramas de secuencia de mensajes
![](ParadaYEsperaEnAccion.png)
#### ¿Qué pasa si se pierde el `pkt1`?
![](PerdidaDePaquete.png)
#### ¿Qué pasa si se pierde el `ACK1`?
![](PerdidaDelACK.png)
#### ¿Qué pasa si el `ACK1` se demora?
![](ACKDemorado.png)
## Evaluando parada y espera
### Variables del análisis de Parada y Espera
* `L`     = **Longitud del segmento** (bits)
* `T`     = **Tasa de transmisión** (bits/seg)
* `D`     = **Demora de propagación**.
* `RTT` (va entre 0 y 1, se puede expresar en porcentaje) = Ida y Vuelta = `2D`.
* `L / T` = El **Tiempo de transmisión**
* $U_{sender}$ () = **Utilización del canal** = $\frac{\frac{L}{R}}{RTT + \frac{L}{R}}$.
![](RTT_LT.png)
### Desempeño de parada y espera
* Ejemplo Concreto: Enlace de 1 Gbps, propagación de 15 ms, paquetes de 8000 bits.
	* $D_{envio}$ es la **demora de enviar un paquete**.
	* $U_{sender}$: **Utilización**, es la fracción del tiempo en que el emisor está ocupado enviando.
	* RTT es el tiempo de ida y vuelta de un bit: RTT = 30 msec.
* El protocolo de red limita el uso de recursos físicos.
![](DesempeñoParadaYEspera.png)
# Protocolos de tubería (*pipelining*)
### Situación
La latencia de la red es **Alta**.
* El **RTT** es mucho mayor que el tiempo de transmitir un paquete.
* Entonces, se pueden enviar múltiples paquetes antes de recibir el primer ACK.
### Problema de parada y espera
si usamos Parada y Espera con alta latencia, el emisor pasa la mayor parte del tiempo ocioso esperando el ACK, entonces utilización muy baja del enlace.
### Solución
Usar protocolo de tubería
* Permite enviar múltiples paquetes sin esperar confirmación de cada uno.
* Mantiene el **"tubo" lleno**, entonces aprovecha mejor el ancho de banda disponible.
* Necesita: Número de secuencia más grandes, buffers, y reglas de retransmisión más complejas.
## Dos enfoques claves
### Retroceso-N (*Go-Back-N*)
* Ventana de N paquetes sin confirmar.
* Si se pierde un paquete: Retransmite ese y todos los siguientes.
* Receptor: Solo acepta paquetes en orden (ACK acumulativo).
* Más simple, pero puede desperdiciar ancho de banda.
### Repetición Selectiva
* Ventana de `N` sin confirmar.
* Si se pierde un paquete: Retransmite solo ese paquete.
* Receptor: Acepta paquetes fuera de orden (buffer + ACK individual).
* Más eficiente, pero más complejo (buffer en receptor).
## Utilización incrementada
![](ProtocoloDeTuberiaUtilizacionIncrementada.png)
## Uso de bufferes en el emisor
* La Entidad de Transporte emisora debe manejar bufferes para los mensajes de salida.
* Esto es necesario porque puede hacer falta retransmitirlos.
### ¿Cómo se usan bufferes en el emisor?
El emisor almacena en buffer todas los segmentos hasta se confirma su recepción.
## ¿Cuál protocolo elegir?
Ambos protocolos usan tubería (pipelining), pero difieren en cómo manejan los paquetes perdidos:
1. **Protocolo Retroceso-N** (Go-Back-N)
	* Receptor Simple: Descarta paquetes fuera de orden.
	* Ideal cuando la tasa de errores es baja.
2. **Protocolo de Repetición Selectiva**
	* Receptor más complejo: Guarda paquetes fuera de orden en buffer.
	* Mejor cuando la tasa de errores es significativa.
La elección depende de las condiciones de la red.
# Retroceso-N
### Situación Ideal para retroceso-N
* La latencia es grande (RTT alto, entonces se justifica el pipelining)
* La proporción de errores o pérdida de paquetes es muy baja.
* Rara vez se demoran los paquetes.
## ¿Qué pasa si se pierde un paquete?
El emisor envía paquetes 1, 2, **3**, 4, 5... pero el paquete 3 se pierde.
### Restricción clave
La capa de transporte debe entregar datos en orden a la capa de aplicación.
* Los paquetes 4 y 5 llegaron bien, pero no puede entregarse porque falta el 3.
### Pregunta central
¿Qué hacer con los paquetes correctos que llegaron después del perdido?
* Opción A (Retroceso-N): **Descartarlos (es mas simple)**.
* Opción B (Repetición Selectiva): Guardarlos en buffer, es más simple.
## Estrategia del receptor
### Solución ante una pérdida
* El receptor descarta todos los paquetes posteriores al perdido.
* No envía ACK para los paquetes descartados.
### ¿Qué pasa después?
* El emisor no recibe ACK, entonces expira el temporizador.
* El emisor "retrocede" y retransmite **Todos** los paquetes desde el perdido en adelante.
## Comportamiento del receptor
El receptor usa ACK acumulativo: Confirma hasta el mayor número de secuencia recibido en orden.
### Al recibir paquete `n`:
#### Caso 1: `n` está en orden y correcto
* Entonces envía ACK(n) y entrega los datos del paquete `n` a la capa superior.
#### Caso 2: `n` está fuera de orden, dañado o duplicado
* Entonces descarta el paquete `n` y reenvía ACK del último paquete recibido en orden.
 Clave: El receptor no necesita buffer, es muy simple.
## Comportamiento del emisor
El emisor mantiene un **único temporizador** para el paquete más antiguo no confirmado.
### Si expira el temporizador:
Entonces retransmite Todos los paquetes no confirmados (desde el más antiguo)
### Si llega un ACK nuevo:
* Si quedan paquetes sin confirmar, entonces reinicia el temporizador.
* Si todos están confirmados, entonces detiene el temporizador.
## Bufferes y ventana del emisor
### Supuestos del emisor:
* Todos los bufferes son del mismo tamaño (un paquete por buffer).
* La cantidad de bufferes es Fija, calculada para llenar el "tubo" durante un RTT.
### Conceptos clave: Ventana del emisor
* Es el conjunto de número de secuencia asignado a esos bufferes.
* Define cuántos paquetes puede tener "en vuelo" simultáneamente.
* Se "desliza" hacia adelante cuando llega ACKs confirmando paquetes.
### En el emisor
* La "ventana" permite hasta N paquetes consecutivos sin confirmar
* **Ventana Emisora** = Tramas enviadas sin ACK positivo o tramas listas para ser enviadas.
![](RetrocesoNVentana.png)
* *timeout*(n): Retransmite paquete `n` y todos los paquetes de mayor número de secuencia en la ventana.
## ACK acumulativo y expectedSeqnum
### ¿Qué número de secuencia lleva el ACK?
* El ACK lleva el número de secuencia más alto recibido en orden consecutivo.
* Esto se llama **ACK acumulativo**: Confirma todos hasta ese punto.
### ¿Qué pasa si se pierde un paquete intermedio?
* Los paquetes siguientes genera ACKs duplicados (repiten el último ACK en orden).
### Variable clave del receptor:
* **expectedSeqnum** = número de secuencia más chico que aún no llegó.
## Retroceso-N en acción
![](RetrocesoNEnAccion.png)
## Tamaño máximo de la ventana emisora
### Pregunta
Si el espacio de número de secuencia tiene `MAX_SEQ + 1` valores (0 a `MAX_SEQ`), ¿Puede la ventana emisora ser de tamaño `MAX_SEQ + 1`? **NO**.
### Conclusión
El tamaño máximo de la ventana emisora es `MAX_SEQ` (no `MAX_SEQ + 1`). Si fuera igual al espacio de secuencia completo, el receptor no podría distinguir paquetes nuevos de retransmisiones.
### ¿Por qué limitar la ventana?
#### Escenario problemático (`MAX_SEQ = 7`):
1. El emisor envía paquetes 0, 1, 2, 3, ..., 7 (8 paquetes = toda la ventana).
2. Llega ACK acumulativo para paquete 7, todos los confirmados.
3. Emisor envía otros 8 paquetes con número de secuencia 0, 1, 2, ..., 7 (se reciclan).
4. Llega otro ACK para paquete 7.
**Absurdo** El emisor no puede distinguir:
* ¿Es un reenvío del ACK del paso 2? (los paquetes del paso 3 se perdieron).
* ¿O es un ACK nuevo confirmando los paquetes del paso 3?
#### En este escenario
* En ambos casos (éxito o pérdida del segundo bloque), el receptor envía ACK 7.
* El emisor no puede distinguir en caso del otro.
#### Conclusión
* El tamaño de la ventana emisora no puede superar `MAX_SEQ` cuando hay `MAX_SEQ + 1` número de secuencia.
* Esto garantiza que nunca se reciclen número de secuencia mientras haya paquetes sin confirmar, elimina la ambigüedad.
## Limitación de Retroceso-N
### Problema principal de Retroceso-N
 * El uso ineficiente del canal cuando hay segmentos perdidos o demorados.
 * Un solo paquete perdido obliga a retransmitir todos los siguientes, aunque  hayan llegado bien.
 * En redes con alta tasa de pérdida, esto desperdicia mucho ancho de banda.
 * Con muchos errores, GBN retransmite demasiado paquetes correctos, desperdicio.
# Repetición Selectiva
## ¿Cuando usar repetición selectiva?
### Situación:
* La latencia es alta (como en GBN).
* Además: La tasa de errores/pérdida de paquetes es significativa.
* Los paquetes pueden demorarse y llegar fuera de orden.
### Enfoque de Repetición selectiva
* Receptor más complejo: Acepta paquetes fuera de orden, usa buffer, envía ACK individuales.
* ***Tradeoff***: Código más complejo, pero mucho más eficiente con alta tasa de errores.
## Mismo problema, otra solución
### Escenario
El paquete `T` se pierde a mitad de una serie larga. Los paquetes siguientes llegan bien.
### ¿Qué hacer con los paquetes correctos que llegaron después del pedido?
* En GBN: se descartan todos, es simple pero ineficiente 
* En **repetición selectiva**: **Se guardan en buffer, solo se retransmite el paquete perdido**.
## Buffer en el receptor
### Estrategia del receptor
Los paquetes correctos que llegan después de un paquete dañado `E` se almacenan en un buffer.
Cuando el paquete `E` finalmente llega correcto:
* El receptor entrega a la capa de aplicación, en orden, tanto `E` como todos los paquetes consecutivos almacenados en el buffer.
### Ventaja
solo se retransmite el paquete perdido.
## Retransmisiones y NAK
### Mecanismo
* El temporizador del paquete `E` expira, entonces el emisor lo retransmite.
	* Notar que cada paquete tiene su propio temporizador.
* El receptor detecta que falta un paquete y envía un **NAK** al emisor. 
* El emisor retransmite antes de que expire el temporizador, esto nos da mejor rendimiento.
## Resumen
### Receptor
* Confirma individualmente cada paquete recibido correctamente (ACK por paquete). 
* Almacena en buffer los paquetes fuera de orden hasta poder entregarlos en secuencia a la aplicación.
### Emisor
* Solo retransmite paquetes cuyo ACK no llegó o que recibieron un NAK. 
* Mantiene un temporizador individual para cada paquete no confirmado.
## Ventana del emisor
### Estructura de la ventana emisora 
Contiene `N` número de secuencia consecutivos. Esto limita la cantidad de paquetes no confirmados en tránsito. 
### Tipos de paquetes dentro de la ventana del **emisor**
* **Enviados y confirmados**: Aún en ventana porque antes hay paquetes sin confirmar (no puede avanzar la ventana).
* **Enviados y NO confirmados**: En tránsito, esperando ACK.
* **Listos para enviar**: En buffer, esperando turno.
Notar que a diferencia de GBN, acá pueden haber "huecos" de paquetes confirmados entre no confirmados. 
## ventana corrediza del receptor
### Situación
El receptor necesita almacenar paquetes que llegan fuera de orden mientras espera el paquete faltante.
### ¿Cómo representar qué paquetes puede almacenar el receptor?
#### Solución: **Ventana corrediza** (***Sliding window***)
* Un intervalo de número de secuencia dentro del espacio total. 
* Define qué paquetes el receptor está dispuesto a aceptar y guardar en buffer.
* Se "desliza" hacia adelante a medida que se entregan paquetes a la aplicación. 
### Tipos de paquetes en ventana receptor
* **Esperados y no recibidos**: Los que faltan (ej: el paquete perdido).
* **Recibidos fuera de orden**: Almacenados en buffer, esperando al faltante.
* **Aceptables pero no llegados**: Dentro de la ventana, todavía en tránsito.
## Ventana de emisor y del receptor
![](VentanaRepeticionSelectiva.png)
## Recepción de paquetes en el receptor
### Inicialización
* La ventana emisora comienza en tamaño 0 y crece hasta `MAX_SEQ`.
* El receptor tiene un buffer dedicado para cada número de secuencia en su ventana.
### ¿Qué ocurre cuando llega un paquete?
* Se verifica si su número de secuencia cae dentro de la ventana receptora.
* Si está dentro y no fue recibido aún, entonces se acepta y almacena en el buffer correspondiente.
* Si está fuera de la ventana, entonces se descarta (puede ser un duplicado).
## Repetición selectiva en acción
![](RepeticionSelectivaEnAccion.png)
### Dilema
![](DilemaRepeticiónSelectiva.png)
## Tamaño máximo de ventana receptora
### Regla fundamental
Tamaño de ventana **receptora** = $\frac{(MAXSEQ + 1)}{2}$ 
### ¿Por qué esta restricción?
*  Con ventanas más grandes, el receptor no puede distinguir entre paquetes nuevos y retransmisiones de la ronda anterior. 
* La mitad del espacio de secuencia garantiza que las ventanas de emisor y receptor nunca se solapan. 
* Con tamaños mayores de ventana: el protocolo no funciona correctamente.
## Comunicación bidireccional
Cada paquete incluye un número de secuencia de `k` bits en su encabezado. 
### Problema
hasta ahora asumimos transferencia unidireccional. En la práctica, ambas máquinas envían y reciben datos simultáneamente.
### Solución ***Piggybacking***
* Cuando llega un segmento S con datos, el receptor espera a que la aplicación le pase el siguiente paquete `P` para enviar.
* El ACK de `S` se anexa a `P` usando el campo ACK del encabezado del segmento de salida.
#### Idea
En comunicación bidireccional, en lugar de enviar ACKs en paquetes separados, se puede superponer el ACK dentro de un paquete de datos que viaja en dirección contraria.
#### Como funciona
* La capa de transporte espera a que haya un paquete de datos para enviar en la dirección de regreso. 
* El ACK se incluye dentro de ese paquete de datos.
#### Ventaja
Reduce el tráfico en la red (menos paquetes de ACK independientes).
## Temporizador auxiliar para ACKs
### Problema del piggybacking
¿Qué pasa si no hay tráfico de regreso? El ACK se retrasa indefinidamente y el emisor cree que el paquete se perdió.
### Solución
* Al recibir un paquete en secuencia, se arranca un temporizador auxiliar.
* Si aparece tráfico de regreso antes del timeout, entonces el ACK va a caballito (piggybacking).
* Si expira el temporizador sin tráfico, entonces se envía un ACK independiente.
## ¿Porque el timer auxiliar debe ser corto?
El tiempo de temporizador auxiliar << tiempo de temporizador de retransmisiones.
* El << significa mucho menor.
### ¿Por qué?
Para asegurarse que la ACK de un paquete correctamente recibido llegue antes que el emisor termine su temporización y retransmita el paquete.
# Ejercicios de las filminas
## Ejercicio 1: Preguntas Conceptuales
1. ¿Por qué Parada y Espera necesita números de secuencia si solo envía un paquete a la vez? 
	* Para detectar paquetes duplicados. Si el ACK se pierde, el emisor retransmite. Sin N° de secuencia, el receptor no sabría si es un paquete nuevo o una copia.
2. En Retroceso-N, ¿por qué el receptor descarta paquetes fuera de orden en lugar de almacenarlos?
	 * GBN usa ACK acumulativo: confirma hasta el último paquete recibido en orden. Guardar los desordenados obligaría a manejar un buffer complejo en el receptor, lo cual contradice la filosofía de simplicidad del receptor de GBN.
3. ¿Cuál es la ventaja principal de Repetición Selectiva sobre Retroceso-N?
	* SR solo retransmite el paquete perdido, no toda la ventana. En redes con alta tasa de errores y ventanas grandes, esto ahorra mucho ancho de banda. El costo: receptor más complejo con buffer.
## Ejercicio 2: Cálculo de Utilización
### Datos
Enlace de 100 Mbps, propagación 25 ms, paquetes de 10.000 bits. ACK despreciable.
1. Calcule `U` para Parada y Espera
	* $D_{envio} = \frac{L}{R} = \frac{10000}{100*10^{6}} = 0,1ms$. RTT = 2 × 25 ms = 50 ms.
	* U = $\frac{D_{envio}}{(RTT+D_{envio})} = \frac{0,1}{50+0,1} = 0,002 = 0,2$% => muy ineficiente
2. ¿Cuántos paquetes en vuelo necesita un protocolo de tubería para lograr U ≥ 90%?
	* U = $\frac{N*D_{envio}}{(RTT + D_{envio})}$. Queremos 0,9 ≤$\frac{N*0,1}{50,1}$=> N ≥ $\frac{50,1*0,9}{0,1}$= 450,9.
	* Se necesitan al menos N = 451 paquetes en vuelo para alcanzar 90% de utilización. 
3. ¿Cuál sería el throughput efectivo con Parada y Espera?
	* Throughput = U × R = 0,002 × 100 Mbps = 200 kbps (solo 0,2% del enlace de 100 Mbps).
## Ejercicio 3: Parada y espera vs. Tubería
Enlace de 1 Gbps, propagación 15 ms, paquetes de 8.000 bits. ACK despreciable. RTT = 2 × 15 ms = 30 ms.
1. Calcule U y throughput para Parada y Espera
	* $D_{envio} = \frac{L}{R} = \frac{8000}{10^{9}}$ = 0,008ms.
	* U = $\frac{0,008}{30 + 0,008}$ = 0,00027 = 0,027%
	* Throughput = 0,00027 × 1 Gbps = 270 kbps.
2. Si se envían 3 paquetes en tubería (como en slide 22), ¿cuánto mejora?
	* U = $\frac{N*D_{envio}}{(RTT + D_{envio})} = \frac{3*0,008}{30,008}$ = 0,0008 = 0,08%.
	* Mejora: ×3 respecto a P&E. Throughput = 0,0008 × 1 Gbps = 800 kbps. Aún muy bajo.
3. ¿Cuántos paquetes en vuelo se necesitan para U = 100%?
	* $1 = \frac{N*0,008}{30,008}$ => $N = \frac{30,008}{0,008}$ = 3.751 paquetes.
	* Conclusión: Con alta velocidad y alta latencia, se necesita una ventana enorme para llenar el enlace. Esto exige muchos bits para N° de secuencia.
## Ejercicio 4: Utilización y número de secuencia
Enlace de 10 Mbps, propagación 10 ms, paquetes de 5.000 bits. ACK despreciable. Se usa Retroceso-N.
1. ¿Cuál es la utilización con Parada y Espera?
	* $D_{envio} = \frac{5000}{10*10^{6}}$ = 0,5ms. RTT = 20 ms. U = 0,5 / 20,5 = 0,**0244 = 2,44%**.
2. ¿Cuántos paquetes en vuelo necesita GBN para U ≥ 80%?
	* 0,8 ≤ $\frac{N*0,25}{20,5}$=> N ≥ $\frac{20,5*0,8}{0,5}$ = 32,8 => N = 33 paquetes.
3. ¿Cuántos bits de N° de secuencia necesita GBN para esa ventana?
	* GBN necesita MAX_SEQ ≥ 33, o sea MAX_SEQ + 1 ≥ 34 secuencias. Se requiere 2k ≥ 34 => k = 6 bits (26 = 64 secuencias, ventana máx = 63).
4. ¿Y si se usara Repetición Selectiva con los mismos 6 bits?
	* Ventana máx SR = $\frac{2^{6}}{2}$ = 32 paquetes. U = $\frac{32*0,5}{20,5}$ = 0,78 = 78%.
	* No alcanza el 80%: SR necesitaría 7 bits (ventana máx = 64)
## Ejercicio 5: Tamaño máximo de ventana
Un protocolo usa N° de secuencia de 3 bits (secuencias 0 a 7, MAX_SEQ = 7)
1. ¿Cuál es el tamaño máximo de la ventana emisora en Retroceso-N?
	* Ventana máx. = MAX_SEQ = 7. No puede ser 8 (= MAX_SEQ + 1) porque el receptor no podría distinguir un paquete nuevo del paquete 0 retransmitido.
2. ¿Cuál es el tamaño máximo de la ventana receptora en Repetición Selectiva?
	* Ventana máx. = $\frac{(MAXSEQ + 1)}{2} = \frac{7+1}{2}$ = 4. La mitad del espacio de secuencia garantiza que las ventanas de emisor y receptor nunca se solapen.
3. Si se usaran 4 bits para N° de secuencia (0 a 15), ¿cuántos paquetes en vuelo permitiría GBN?
	* MAX_SEQ = $2^{4} - 1$ = 15. Ventana máx. GBN = 15 paquetes. Y para SR: ventana máx. =  $\frac{15+1}{2}$ = 8 paquetes.
## Ejercicio 6: Escenario y piggybacking
1. **Escenario**: Un emisor usa GBN con ventana = 4 y envía paquetes 0, 1, 2, 3. El paquete 1 se pierde. ¿Qué paquetes retransmite el emisor al expirar el temporizador? 
	* El receptor acepta pkt 0 (envía ACK 0), pero descarta pkts 2 y 3 (fuera de orden). El emisor retransmite 1, 2, 3 (toda la ventana desde el paquete perdido). 
2. Mismo escenario con Repetición Selectiva. ¿Qué cambia?
	* El receptor almacena pkts 2 y 3 en su buffer y envía ACK individual para cada uno. Solo retransmite el paquete 1. Al recibirlo, el receptor entrega 1, 2, 3 en orden a la capa superior. Ahorro: 2 retransmisiones menos.
3. ¿Qué es piggybacking y cuándo resulta útil?
	* Piggybacking = incluir el ACK dentro de un paquete de datos que viaja en sentido contrario. Es útil en comunicación bidireccional porque ahorra un paquete completo. Si no hay datos para enviar rápidamente, se usa un temporizador auxiliar corto para enviar el ACK solo y evitar que expire el temporizador del emisor.
---
# Preguntas de repaso para la transferencia de datos confiables
1. ***¿Cuál es la función principal de los números de secuencia en un protocolo de transferencia de datos?***.
	* **Función de los números de secuencia:** Sirven para identificar cada paquete de forma única, permitiendo al receptor detectar y descartar duplicados. Esto es esencial cuando se pierden confirmaciones (ACKs) y el emisor retransmite un paquete que ya había llegado correctamente.
2. ***¿Bajo qué condiciones de red se considera que el protocolo de Parada y Espera es óptimo?***.
	* **Condiciones para Parada y Espera:** Este protocolo es ideal cuando la latencia de la red es baja, es decir, cuando el tiempo de ida y vuelta (RTT) es pequeño en comparación con el tiempo de transmisión del paquete. En este escenario, el emisor no pasa mucho tiempo ocioso esperando la confirmación antes de enviar el siguiente segmento.
3. ***¿Qué tres herramientas básicas utiliza la capa de transporte para garantizar la entrega confiable?***
	* **Herramientas básicas:** Los mecanismos fundamentales son los ACKs (confirmaciones de recepción), los temporizadores (timers) para detectar pérdidas por expiración de tiempo (timeout) y las retransmisiones para reenviar paquetes dañados o perdidos.
4. ***Explique el concepto de "Utilización del emisor" ($U_{sender}$​) y qué factores la afectan.***
	* **Utilización del emisor ($U_{sender}$):** Representa la fracción del tiempo que el emisor está ocupado transmitiendo datos en el canal. Depende de la longitud del paquete (L), la tasa de transmisión (R) y el tiempo de ida y vuelta (RTT), calculándose mediante la fórmula $U_{sender}$​=(L/R)/(RTT+L/R).
5. ***¿Cuál es la diferencia fundamental en la estrategia del receptor entre el protocolo Retroceso-N (GBN) y la Repetición Selectiva (SR)?***
	* **Diferencia de estrategia en el receptor:** En GBN, el receptor es simple y descarta cualquier paquete que llegue fuera de orden, aceptando solo el siguiente número de secuencia esperado. En cambio, en SR el receptor es más complejo y cuenta con un buffer para almacenar paquetes que llegan fuera de orden hasta que los faltantes sean recibidos.
6. ***¿Por qué el protocolo Retroceso-N utiliza un "ACK acumulativo"?***
	* **ACK acumulativo en GBN:** Este mecanismo confirma que todos los paquetes hasta un número de secuencia _n_ han sido recibidos correctamente y en orden. Si llega un paquete fuera de secuencia, el receptor vuelve a enviar el ACK del último paquete recibido correctamente, lo que indica al emisor dónde debe retomar la transmisión.
7. ***En el protocolo de Repetición Selectiva, ¿por qué es necesario que el emisor mantenga temporizadores individuales para cada paquete?***.
	* **Temporizadores individuales en SR:** A diferencia de GBN que usa un solo temporizador para la ventana, SR necesita uno por cada paquete no confirmado para poder retransmitir únicamente aquel paquete específico que se perdió. Esto maximiza la eficiencia al evitar el reenvío innecesario de segmentos que ya están en el buffer del receptor.
8. ***¿Cuál es la restricción de tamaño de ventana en Retroceso-N en relación con el espacio de números de secuencia y por qué existe?***
	* **Restricción de ventana en GBN:** El tamaño máximo de la ventana debe ser MAX_SEQ, siendo el espacio de secuencia MAX_SEQ+1. Esta limitación evita la ambigüedad en el receptor, impidiendo que se confunda una retransmisión de un paquete antiguo con el envío de un paquete nuevo que use el mismo número de secuencia reciclado.
9. ***Defina el mecanismo de "Piggybacking" y mencione su principal beneficio.***
	* **Piggybacking:** Consiste en incluir el ACK de un paquete recibido dentro del encabezado de un paquete de datos que viaja en la dirección opuesta. Su beneficio principal es la reducción del tráfico en la red al disminuir la cantidad de paquetes independientes que solo contienen confirmaciones.
10. ***¿Para qué se utiliza un temporizador auxiliar en el contexto del piggybacking?***
	* **Temporizador auxiliar:** Se utiliza para esperar un tiempo breve por tráfico de datos de regreso donde "montar" el ACK. Si el temporizador expira y no hay datos para enviar, el protocolo genera un paquete de ACK independiente para evitar que el temporizador de retransmisión del emisor original expire innecesariamente.
---
Capa de Red I so sorry
