# Bloque 6
Ahora que sabemos como conectar maquinas a escala global; ahora debemos lograr que los programas dentro de esas maquinas puedan comunicarse.
Por esto los mensajes deben indicar cual es le programa receptor, introduciendo la idea de **Puertos**.
Como la red subyacente a una aplicación no se ocupa de manejar todas sus imperfecciones, se requiere cubrir asuntos como ordenamiento de paquetes, control de congestión, control de flujo y comunicación confiable.
#### ¿Como distinguir los mensajes para un programa con los mensajes para otro programa?
Los mensajes deben indicar cual es el programa receptor.
#### ¿Como se puede indicar?
Usando un **Identificador de Programa** (en Internet se lo llama **Puerto**).

---
## Ordenamiento de Paquetes
#### ¿Si mandamos dos paquetes de un mismo mensaje, esos paquetes van a seguir la misma ruta?
No necesariamente, puede que cambien las rutas entre paquetes porque se ejecutó el algoritmo de enrutamiento.
#### Si dos paquetes de un mensaje son enviados, ¿Van a llegar en orden?
No necesariamente:
* El segundo paquetes sigue una ruta mas corta y llega antes (por cambio de rutas).
* El primer paquete se pierde y debe ser reenviado; lo que causa que el segundo paquete llegue antes.
Por lo tanto, los paquetes de un mismo mensaje pueden llegar fuera de orden.
Entonces es necesario ordenar los paquetes de un mensaje porque una aplicación de red necesita procesar el mensaje completo ordenado.

---
## Control de Congestión
Resolver el problema del ordenamiento nos garantiza que un flujo pueda reconstruirse correctamente.
¿Que pasa cuando muchos flujos simultáneos compiten por la misma red? En este escenario, aunque cada flujo pueda ordenar sus paquetes, la red puede saturarse, perder paquetes masivamente o volverse inestable.
#### ¿Que pasa si en la linea de salida de un enrutador llegan demasiados paquetes a reenviar?
* La cola de salida se hace mu grande y el reenvió se pone lento.
* La capacidad de la linea de salida se puede saturar y entonces comienzan a perderse paquetes.
A este fenómeno se lo llama **Congestión**, y ocurre cuando la red no puede manejar la carga de paquetes que recibe de manera aceptable.
#### ¿Como controlamos la congestión?
La computadoras emisoras se enteran de la congestión y reducen el tráfico de salida. A esto se le llama **Control de Congestión**.

---
## Control de Flujo
El control de congestión evita que la red se sature cuando muchos flujos compiten por los mismos enrutadores. Pero este mecanismos solo considera el estado de la red, no el estado de los procesos que se comunican. Incluso si no hay congestión, un receptor puede saturarse si el emisor envía datos mas rápido de lo que puede procesarlo. Cuando ocurre esto, el receptor pierde paquetes y el emisor probablemente va a interpretar esas perdidas como congestión, reaccionando de manera incorrecta.
Para evitar que el emisor sobrecargue al receptor, es necesario definir un mecanismo adicional de **Control de Flujo**.
#### ¿Que pasa si un emisor envía paquetes a un receptor mas rápido que la capacidad del receptor de procesar cada paquete?
Se satura de paquetes el *buffer* del receptor hasta que este ya no puede almacenar mas paquetes que le lleguen y comienza a perder paquetes
#### ¿Como evitamos que un emisor rápido sature de paquetes a un receptor lento?
Uso de **Retroalimentación al Emisor**, o sea el receptor le indica al emisor cuando y cuanto puede enviar.

---
El control de flujo evita que el emisor envíe datos mas rápido de lo que el receptor puede procesar, protegiendo al *host* de desbordes y perdidas de paquetes.
Pero aun cuando el receptor no se satura, eso no garantiza que la comunicación sea correcta; la red puede perder, duplicar o desordenar paquetes, y los procesos necesitan mecanismos para detectar y corregir esos problemas.
Por eso, ademas del control de flujo, es necesario un proceso que asegure una **Comunicación Confiable** entre procesos, permitiendo reconstruir los datos completos y en el orden adecuado.
#### ¿Como se puede perder paquetes en una red?
Los paquetes pueden perderse por varias razones:
* Por congestión en los enrutadores cuando las colas se llenan;
* porque el emisor envía mas rápido que lo que el receptor puede procesar y este descarta paquetes;
* Por errores físicos o daños en los paquetes durante su transmisión.
#### ¿Como manejar la perdida de paquetes?
Los paquetes recibidos son confirmados por el receptor.
El emisor detecta que paquetes faltan. Porque no recibio sus confirmaciones de recepcion durante un cierto tiempo. Ese tiempo se mide con un temporizador asociado a cada paquete.
Si un paquete no es confirmado, el emisor lo retransmite.

