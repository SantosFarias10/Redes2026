# Bloque 2
Ahora que sabemos como viajan los bits por un medio físico, podemos ver como se **Organiza la Comunicación entre Dos Maquinas**

---
## Conexión Punto a Punto
**Dos Dispositivos** están conectados directamente entre si, sin intermediarios ni compartición del medio con otros usuarios, como por ejemplo dos maquinas conectadas por un cable.

#### ¿Qué pasa si tenemos `n` maquinas y las queremos conectar entre si, usando conexiones punto a punto usando cables?
Se necesita $\frac{n(n-1)}{2}$ cables, cada maquina necesita `n` puertos para enchufar esos cables.

---
## Canales de Difusión
Conectar maquinas de a pares funciona mientras la red es pequeña, pero cuando el numero de maquinas crece, la cantidad de cables y puertos necesarios se vuelve impracticable. Estos nos obliga a buscar una forma de **Compartir un Mismo Medio Entre Varias Maquinas**.
A Los medios Compartidos se los llama **Canales de Difusión**. Pero al usar medios compartidos trae nuevos problemas, como por ejemplo: 
* Si el medio compartido es el aire ¿Qué pasa si dos personas hablan al mismo tiempo? O sea, mas de una maquina manda simultáneamente un mensaje. Lo que ocurre es que los mensajes colisionan y se dañan.

---
## Colisiones
Compartir un mismo resuelve el problema del cableado excesivo, pero introduce un nuevo problema; si varias maquinas transmiten al mismo tiempo, sus señales se superponen y se destruyen. A esto lo llamamos **Colisión**.

#### ¿Cómo Controlamos las Colisiones pero en el Caso de Computadoras Conectadas a un Canal de Difusión?
* **Solución 1**: Si dos maquinas colisionan => esperamos un **Tiempo Aleatorio** (diferente) para cada una y pasado ese tiempo retransmitimos.
* **Solución 2**: Una maquina **Coordinada el Orden** de transmisión de las demás maquinas.

---
## Controles de Errores
Las colisiones son solo una de las formas en que un mensaje puede dañarse. Incluso si evitamos o controlamos las colisiones, el medio físico sigue introduciendo ruido, interferencia y atenuación. Es por eso que necesitamos mecanismos que nos permitan detectar si un mensaje llego correctamente.

##### Ejemplo
Supongamos que dos alumnos se mandan un mensaje y tiene que pasar por 10 alumnos antes de llegar a destino, y uno de los alumnos intermedios lo altera agregando palabras ¿Cómo podemos hacer para que el alumno receptor pueda detectar que es un mensaje errado? Una idea es que tanto el emisor y receptor acuerdan usar una función $f$ que aplicada al mensaje da una secuencia de bits. Si el mensaje a mandar es `M`, se envía entonces `M ++ f(M)`.
¿Por que esta idea funcionaria? Si `M` fue alterado a `M'`, el receptor va a recibir `M' ++ f(M)`. El receptor va calcular `f(M')` y va a descubrir que no concuerda con `f(M)`. Entonces se va a dar cuenta que el mensaje recibido contiene error.

---
## Comunicación Confiable
Saber que un mensaje llego dañado es solo la mitad del trabajo. La otra mitad es lograr que el mensaje correcto llegue finalmente al destino. Ese es el **Objetivo** de la **Comunicación Confiable**.

Supongamos que un mensaje se recibió con error ¿Cómo hacer para recibir de nuevo el mensaje, o sea para retransmitirlo xd?
* **Idea 1**: El receptor envía un mensaje NAK con el numero de mensaje que se recibió mal. Luego el emisor lo manda de nuevo. El receptor manda un ACK con numero de mensaje que recibió bien.
* **Idea 2**: El receptor manda un ACK con el numero de mensaje que recibió bien. El emisor manda un mensaje y dispara un temporizador; si el temporizador del mensaje expira, significa que el mensaje llego mal o se perdió y hay que reenviarlo.

---
## Fragmentación de Mensajes
La comunicación confiable nos permite pedir la retransmisión de un mensaje cuando llega con errores. Pero si el mensaje dañado es muy largo ¿Conviene retransmitirlo todo? No ya que seria demasiado costoso.

#### ¿Qué se Puede Hacer Para Retransmitir la Parte Dañada?
Con **Fragmentación**; Dividir el mensaje en **Tramas**. En lugar de mandar el mensaje entero se mandan tramas. Si una trama llega dañada, entonces se retransmite.

#### ¿Cómo Hacemos para Reconstruir el Mensaje Original Cuando Algunas Partes son Dañadas, Que Seria Necesario?
* Numeración de Tramas y Reensamblado (en ese orden).
* Confirmación de Tramas buenas recibidas.
* Retransmisión de Tramas dañadas.

---
## Repetidores
Ya que sabemos como tratar con los mensajes dañados. Ahora debemos resolver como hacer que la señal llegue lo suficientemente lejos como para que esos mecanismos tengan sentido.

Debido a la atenuación, una señal a medida que se va propagando por un cable se va debilitando. Llega un punto a partir del cual la señal es demasiado débil como para continuar su viaje.
#### ¿Cómo Hacer para que la Señal Pueda Viajar Mucho Mas Alla de ese Punto?
Definimos un mecanismo físico para extender el alcance físico de la red llamado **Repetidores**; Un **Repetidor** es un dispositivo que recibe, amplifica (regenera) y retransmite en ambas direcciones. Pero los repetidores introducen un Retardo.

---
## Conmutadores
Usando repetidores, todos los segmentos de cable forman un único canal. Entonces la probabilidad de colisiones aumenta ¿Cómo podemos hacerlo mejor? Usando **Conmutadores** (***Switch***).
* Tenemos varios **Dominios de Colisiones** y aumentamos mucho la velocidad para mandar de una maquina de un dominio de colisiones a una maquina en otro dominio de colisiones.
![](Switch.png)

#### Hay 2 Opciones:
* Cada tarjeta es un Domino de Colisiones (todas las maquinas conectadas en la tarjeta esta conectadas directamente entre si).
* Cada puerto es un Domino de Colisiones.

---
## Redes de Área Local (LAN)
Los repetidores extienden el alcance. Los conmutadores segmentan el trafico. Los canales de difusión permiten compartir el medio. La combinación de estos elementos define una red organizada dentro de un área limitada: Una **Red de Área Local** (***Local Area Network***); suelen cubrir el área de una casa, edificio o hasta un campus.

##### Los tipos de LAN que hemos visto son:
* Un canal de difusión con computadoras conectadas.
* Un conjunto de canales de difusión interconectados usando repetidores.
* Un conjunto de canales de difusión interconectados usando conmutadores.

#### Ejemplos de LAN
* Red Hogareña
![](RedHogareña.png)
* Red de Acceso Empresarial
![](RedDeAccesoEmpresarial.png)
* Red Cableada Institucional
![](RedCableadaInstitucional.png)
