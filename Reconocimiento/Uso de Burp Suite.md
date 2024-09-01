__________
- Tags: #reconocimiento #web #burpsuite #proxy
___
Burp Suite es una aplicación de seguridad de software que se utiliza para pruebas de penetración de aplicaciones web. Hay disponibles una versión gratuita y una de pago del software. El software es desarrollado por la empresa PortSwigger.

A continuación explicaré utilidades y curiosidades de uso de la herramienta:

___________
### Definir Scope para trabajar solo con el dominio seleccionado:
Hay veces que el proxy y otras utilidades van a estar interceptando tráfico que no te interesa. Esto se puede arreglar definiendo un scope.

Se puede hacer dentro de la pestaña **Target > Scope settings**.
![[Pasted image 20240606141830.png|500]]

Una vez definido, dentro de Proxy > Proxy settings, abajo del todo, tendremos la opción de desactivar o activar el que solo "loguee" el trafico in-scope.
![[Pasted image 20240606142057.png|500]]

__________

### Match and replace rules

Burp suite ofrece la capacidad de buscar tanto en la respuesta como en la petición ya sea en el body o en las cabeceras, campos que queramos y reemplazarlos por lo que definamos. Esto puede ser útil por ejemplo para al hacer session hyjacking y cambiar la cookie original por la robada en cada petición.
**Proxy > Proxy settings**

![[Pasted image 20240606142547.png|1250]]

_______

### Intruder

Esto permite la realización de ataques de fuerza bruta, seleccionando los campos que queremos sustituir y dentro de la pestaña payloads, podremos especificar los payloads a introducir.

![[Pasted image 20240606143541.png|875]]

