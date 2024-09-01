____
- Tags: #explotacion #web #sql #sqli #inyeccion 
____
### Descripción

Inyección SQL es un método de infiltración de código intruso que se vale de una vulnerabilidad informática presente en una aplicación en el nivel de validación de las entradas para realizar operaciones sobre una base de datos.​

______

## Procedimiento

Como atacante, lo primero es tratar de determinar el numero de columnas de los datos que se están mostrando.

_________

### Detección de numero de columnas error based

**Ejemplo de tabla:**
![[Pasted image 20240606183837.png]]

En el siguiente caso, es fácil de ver a simple vista, pero no siempre será así. Este ejemplo es una página que trata de insertar un ID y te devuelve el usuario correspondiente:
![[Pasted image 20240606184048.png]]

Escapando con una comilla y usando la orden ``order by``, podremos determinar esto.

Empezaremos inyectando la orden ``order by`` con un numero de columnas alto que sabemos de antemano que no es el correcto:
![[Pasted image 20240606184401.png]]

En este caso, la pagina nos muestra un error: **Unknown column '10' in order clause**. Esto significa que el numero de columnas que hemos intentado que la base de datos nos muestre es demasiado alto y que, por lo tanto, el numero de columnas con el que trabaja es inferior.

**Hay casos en los que no se muestra ningún error**, en esos casos, si al hacer la petición la base de datos no muestra ninguna info, significará lo mismo:

La misma página pero con los errores desactivados:
![[Pasted image 20240606184708.png|825]]

A partir de ahora, queda ir reduciendo el número hasta dar con la clave.
![[Pasted image 20240606184818.png]]

Como he dicho antes, en este caso el numero de columnas usadas es una así que al inyectar el order by y viendo que no nos devuelve un error y que nos devuelve datos, **habremos averiguado el numero de columnas.**

**Hay veces en las que las comillas no son necesarias:**
![[Pasted image 20240607160548.png]]
En este caso el campo **id** no está puesto con comillas como en este ejemplo:
![[Pasted image 20240607160629.png]]
Así que si está programado de esta forma, la inyecciones serían directamente como ``?id=9 order by 5``

______

### UNION SELECT

Una vez averiguado el numero de columnas podremos dumpear los datos que queramos a través de estas.

Para explicar esto, usaré la misma página pero habiéndola modificado para que muestre dos columnas:
![[Pasted image 20240606190047.png]]

Para ejecutar la orden ``union select`` necesitaremos el numero exacto de columnas, ya que si no las representas, dará error.

Esta orden nos permite insertar valores para que se representen en las columnas con las que trabaja la web. En este caso sabemos que hay dos columnas, así que hemos forzado que en estas dos columnas nos muestre los valores que he insertado con ``union select {valor1,valor2}-- -``. 

![[Pasted image 20240606190500.png]]

**Importante**: tienes que evitar que en las columnas la página nos de la información que legítimamente nos pide, por eso en la foto anterior, el campo ``id`` está vacío. Si no lo estuviera no se vería lo inyectado con ``union select``, sino los datos recibidos de la db:

![[Pasted image 20240606190914.png]]

Sabiendo esto, podemos cambiar los valores de prueba '1' y '2' que hemos introducido en el ``union select`` por algo más jugoso como ver el nombre de la base de datos en uso:

![[Pasted image 20240606191141.png]]

Como se puede ver, aún solo queriendo usar una columna para sacar la info, hay que rellenar obligatoriamente el resto con cualquier valor para que no nos de un error.

Inyectando ``schema_name from information_schema.schemata`` podremos ver la totalidad de bases de datos, pero, **hay un inconveniente**. Es que solo se te mostrará un valor por pantalla. Para ir viendo los siguientes valores habrá que jugar con ``limit 0,1`` para la primera, ``limit 1,1`` para la segunda, ``limit 2,1`` para la tercera, etc:

![[Pasted image 20240606192513.png]]
![[Pasted image 20240606192542.png]]

Si queremos ver todos los valores de una vez, será suficiente cambiando el payload a lo siguiente: ``group_concat(schema_name) from information_schema.schemata``

![[Pasted image 20240606192701.png]] 

- **Ver tablas:** ``group_concat(table_name) from information_schema.tables where table_schema='hack4u'``
- **Ver columnas:** ``group_concat(column_name) from information_schema.columns where table_schema='hack4u' and table_name='users'``
- **Ver valores de columnas de la db en uso:** ``group_concat(username) from users-- -``
- **Ver columnas de una db no en uso:** ``group_concat(username) from hack4u.users-- -``

Con group_concat podemos concatenar distintos valores:

![[Pasted image 20240606193848.png]]

_____

### BLIND SQLI

[Blind SQL injection](https://owasp.org/www-community/attacks/Blind_SQL_Injection) is nearly identical to normal SQL Injection, the only difference being the way the data is retrieved from the database. When the database does not output data to the web page, an attacker is forced to steal data by asking the database a series of true or false questions. This makes exploiting the SQL Injection vulnerability more difficult, but not impossible.

Hay dos tipos de Blind SQLi:
- Content based
- Time based
