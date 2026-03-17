# Bloque 5
Enormes plataformas, como servicios de vídeos, redes sociales, buscadores, almacenamiento en la nube, etc, generan volumen masivos de datos que deben llegar a los usuarios. Estas plataformas no siempre quieren depender de las reglas, los costos o las políticas de servicio de las redes de transito o regionales.
¿Como solucionamos esto? **Redes Proveedoras de Contenido**, son infraestructuras propias diseñadas para llevar su contenido lo mas cerca posible de los usuarios. Se conectan directamente a redes de acceso o a redes regionales.
![](RedesProveedorasDeContenido.png)

## Puntos de Presencia
#### ¿Como hacer para que el contenido este mas cerca de los usuarios sin depender de redes globales?
En la practica se usa **Puntos de Presencia** (**PoPs**), son nodos distribuidos que reducen la latencia y evitan transito global.
Un PoP esta pensado para entregar contenido a usuarios cercanos, no para generarlo ni coordinarlo. Si solo existiera los PoPs, aparecen 3 problemas inevitables:
1. **No Pueden Generar Contenido**, ya que no tienen la infraestructura para producir, transcodificar o almacenar la versión maestra.
2. **No Pueden Mantenerlo Actualizado**: Cada PoP tendría que obtener actualizaciones desde algún origen remoto.
3. **No Pueden Replicar Contenido entre Ellos Sin Pagar Transito**: Si un PoP copia contenido desde otro PoP lejano, el trafico cruza redes regionales o globales, generando altos costos y alta latencia.
#### ¿Que necesita un PoP para cumplir su función?
* Almacenar contenido popular localmente para reducir latencia.
* Responder a muchas usuarios simultáneamente.
* Conectarse a redes de acceso o regionales para entregar contenido.
* Recibir contenido actualizado desde algún centro de datos.
#### ¿Como atiende un PoP a muchos usuarios a la vez?
Hace falta **Servidores de Borde** para atender usuarios y balanceadores de carga para repartir la carga.
#### ¿Como se conecta un PoP a redes de acceso o regionales?
Usando equipos de redes, enlaces de alta capacidad y acuerdos de *peering local* con redes de acceso y regionales.
![](PeeringLocal.png)

## Centro de Datos
#### ¿Como Aliviar los PoPs y Mejorar la Arquitectura?
**Separando las Funciones**. Los PoPs deben solo entregar contenido y conectarse a redes de acceso o regionales. Otros componentes deben generar, almacenar, actualizar y coordinar el contenido.
Esto lleva directamente la necesidad de un **Centro de Datos**. Aparecen como la única forma viable de:
* Tener la Versión maestra del contenido.
* Procesarlo (Transcodificación, empaquetado, indexación).
* Mantenerlo actualizado.
* Replicarlo hacia los PoPs.
* Coordinar consistencia entre múltiples ubicaciones.
* Balancear carga y Manejar picos globales.
![](CentroDeDatos.png)
#### ¿Que necesita hacer un Centro de Datos?
* Generar contenido.
* Manetener la version maestra de cada objeto.
* Coordinar actualizaciones para que todos los PoPs tengan contenido consistente.
* Replicar contenido hacia los PoPs de forma eficiente.
* Almacenar grandes volumentes de datos.
* Balancear carga global entre regiones.
* Tener alta disponibilidad.
* Conectarse a una red propia que distribuya contenido sin pagar transito.
#### ¿Como almacena contenido popular un PoP sin ser un centro de datos?
Usando una **Caché** dentro de un PoP que guarde solo lo mas pedido.
#### ¿Como se genera y procesa contenido a gran escala?
Se usa una **Capa de Procesamiento Pesado** con:
* Servidores de computo.
* Clusters de procesamiento.
* Sistemas de Transcodificacion.
	* La **Transcodificacion** es el proceso de convertir un contenido digital de un formato, resolución o tasa de bits a otro, para que pueda ser entregado de manera eficiente a distintos tipos de dispositivos, redes y condiciones de ancho de banda.
#### ¿Como se mantiene una version maestra del contenido?
Se usan **Capas de Almacenamiento Maestro**:
* Sistemas de almacenamiento de objetos distribuidos en varios nodos,
* Sistemas de Archivos distribuidos,
* Base de Datos.
#### ¿Como se coordinan actualizaciones entre regiones?
Se usan **Capa de Control y Coordinacion**. Se encarga de:
* La orquestacion.
* Cordinacion global.
* Gestion de Versiones,
* Consistencia, que los PoPs tengan lo que deben de tener.
* Politicas de Distribucion.
* Monitoreo y Telemetria
* Replicacion.
#### ¿Como se Asegura disponibilidad Continua?
* Climatizacion
* Monitoreo
* Redundancia Energetica
* Failover Regional, es el mecanismo mediante el cual un servicio puede migrar automaticamente su operacion desde una region que falla hacia otra region que sigue funcionando, sin que los usuarios pierdan acceso al contenido. Este mecanismo garantiza continuidad incluso ante fallas grandes.
## Red Privada Global
#### ¿Como llevar contenido desde los centros de datos a los PoPs sin pagar transito?
Usando una **Red Privada Global** propia del proveedor de contenido, que conecte centros de datos y PoPs sin depender del transito global
![](RedPrivadaGlobal.png)
Esto permite enlaces troncales privados, rutas optimizadas para replicación masiva.
Con esto el proveedor puede mover contenido desde los centros de datos hacia la periferia (PoPs) sin pagar transito y con baja latencia.

