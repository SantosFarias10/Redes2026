# PHP
Preprocesador de Hipertexto

Tienen la apariencia de páginas HTML donde se insertan comandos especiales. Esos comandos son interpretados por un servidor web

Algunas cosas que puede hacer PHP:
- generar contenido de página dinámica
- operar con archivos en el servidor
- recolectar datos de formulario 
- enviar y recibir cookies
- acceder a encabezados de pedido HTTP
- definir encabezados de respuesta HTTP
- acceder a base de datos
![](Screenshot_2026-06-21-19-14-21_27353.png)
![](Screenshot_2026-06-21-19-14-47_49.png)
Acceso a campos de formularios:
- `$POST`: usado para recolectar datos de formulario luego de someter un formulario con método POST. 
	* Por ejemplo: `$POST['fname'] // recolecta valor del campo de nombre ‘fname’`
- `$GET`: usado para recolectar datos de formulario luego de someter un formulario con método GET. Se usa como en el ítem anterior.
![](Screenshot_2026-06-21-19-16-16_27663.png)
### Tipos de datos de PHP
- `String`: secuencia de caracteres entre comillas.
- `Integer`: número entero entre -2,147,483,648 y 2,147,483,647
- `Float`: número con punto decimal o número en forma exponencial
- `Boolean`: valores booleanos TRUE, y FALSE
- `Array`: un arreglo almacena varios valores en una variable.
- `Object`:  PHP permite definir clases y objetos
### Operadores
- de comparación (como en C), de asignación (como en C)
- Para los distintos tipos de datos
### Acceso a información de encabezados HTTP:
- `$SERVER`: contiene información de encabezados, caminos y localización de scripts.
- Para acceder a encabezados poner como argumento alguna de las siguientes:
   `HTTP_USER_AGENT`, `SERVER_ADDR`, `SERVER_NAME`, `SERVER_SOFTWARE`, `SERVER_PROTOCOL`, `REQUEST_METHOD`, `REQUEST_TIME`, `QUERY_STRING`, `HTTP_ACCEPT`,  `HTTP_ACCEPT_CHARSET`, `HTTP_HOST`, etc.
- Por ejemplo: Para acceder al encabezado User-Agent: `$SERVER[‘HTTP_USER_AGENT’]`. 
- Por ejemplo: nombre del archivo del script ejecutándose: `$SERVER['PHP_SELF’]`.
- Por ejemplo: URL completo de la página corriente: `$SERVER['HTTP_REFERER’]`.
- Por ejemplo: el camino del script corriente: `$SERVER['SCRIPT_NAME’]`.
- Por ejemplo: el nombre de dominio del host servidor: `$SERVER['SCRIPT_NAME’]`.
- Por ejemplo: el encabezado Host del pedido actual: `$SERVER['HTTP_HOST']`.
![](Screenshot_2026-06-21-19-20-30_20041.png)
### Definición de encabezados de respuesta HTTP:
- Hay que usar la función header()
- Se deben fijar encabezados antes de la etiqueta <html> aparezca

Definición de cookies:
- Setcookie() define cookie para ser enviada junto con el resto de los
encabezados HTTP.
- Esta función debe usarse antes de generar cualquier salida, o sea antes que la etiqueta <html>
- Un cookie se crea con la función:
	setcookie(name, value, expire, path, domain, secure, httponly)
 <?php
	$cookie_name = "user";
	$cookie_value = "Alex Porter";
	setcookie($cookie_name, $cookie_value, time() + (86400 * 30), "/");
?>


**Acceso al valor de una cookie:**
- $_COOKIE se usa para retornar el valor de una cookie.
- P.ej:
	<?php
		echo '¡Hola ' . htmlspecialchars($_COOKIE["nombre"]). '!';
	?>
-  Asumiendo que la cookie "nombre" ha sido definida anteriormente, si el valor de esa cookie es “Juan” el resultado del ejemplo será :
- ¡Hola Juan! 
- htmlspecialchars — Convierte caracteres especiales en entidades HTML

![[Pasted image 20260402180511.png]]