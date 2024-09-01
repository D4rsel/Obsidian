_______
- Tags: #explotacion #web  #lfi
_______

La vulnerabilidad Local File Inclusion (LFI) es una vulnerabilidad de seguridad informática que se produce cuando una aplicación web no valida adecuadamente las entradas de usuario, permitiendo a un atacante acceder a archivos locales en el servidor web.

*Nota*: Hay paginas en las que concatenan la extensión php al archivo que se solicita:
![[Pasted image 20240711165856.png]]
![[Pasted image 20240711165907.png]]

Por lo que el resultado sería courses.php

A parte de las [explotaciones básicas](https://book.hacktricks.xyz/pentesting-web/file-inclusion) que no tocaré en este documento, está el uso de wrappers y el abuso de "Filter chains":

_______
## Wrappers

**Representa un archivo php en base64**
- php://filter/convert.base64-encode/resource=db.php

[Más wrappers:](https://book.hacktricks.xyz/pentesting-web/file-inclusion#lfi-rfi-using-php-wrappers-and-protocols)


_______

## [RCE](https://book.hacktricks.xyz/pentesting-web/file-inclusion#lfi2rce)

Este wrapper necesita que pases la petición por POST y el codigo php a ejecutar para el RCE en el cuerpo:

- php://input 

![[Pasted image 20240711172034.png]]

- data://text/plain;base64,codigoPHPenBase64

![[Pasted image 20240711172430.png]]
__________

## Log Poisoning

El Log Poisoning es una técnica de ataque en la que **un atacante manipula los archivos de registro (logs) de una aplicación web para lograr un resultado malintencionado**. Esta técnica puede ser utilizada en conjunto con una vulnerabilidad LFI para lograr una ejecución remota de comandos en el servidor.

Si tenemos capacidad de lectura podemos [envenenar logs](https://book.hacktricks.xyz/pentesting-web/file-inclusion#via-apache-nginx-log-file) de apache, ssh, entre otros.

