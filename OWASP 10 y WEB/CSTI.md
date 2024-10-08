______
- Tags: #explotacion  #web #xss #csti #inyeccion  
______
**El modo de testear y encontrar un CSTI es el mismo que con el SSTI, pero a la hora de explotarlo, verás que no podrás listar archivos ni ejecutar código ni nada que tenga relacionado con el servidor. Ahí es cuando sabrás que estas ante un CSTI y no un SSTI.**

El Client-Side Template Injection (CSTI) es una vulnerabilidad de seguridad en la que un atacante puede inyectar código malicioso en una plantilla de cliente, que se ejecuta en el navegador del usuario en lugar del servidor.

A diferencia del Server-Side Template Injection (SSTI), en el que la plantilla de servidor se ejecuta en el servidor y es responsable de generar el contenido dinámico, en el CSTI, la plantilla de cliente se ejecuta en el navegador del usuario y se utiliza para generar contenido dinámico en el lado del cliente.

Los atacantes pueden aprovechar una vulnerabilidad de CSTI para inyectar código malicioso en una plantilla de cliente, lo que les permite ejecutar comandos en el navegador del usuario y obtener acceso no autorizado a la aplicación web y a los datos sensibles.

Por ejemplo, imagina que una aplicación web utiliza plantillas de cliente para generar contenido dinámico. Un atacante podría aprovechar una vulnerabilidad de CSTI para inyectar código malicioso en la plantilla de cliente, lo que permitiría al atacante ejecutar comandos en el navegador del usuario y obtener acceso no autorizado a los datos sensibles de la aplicación web.

*Una derivación común en un ataque de Client-Side Template Injection (CSTI) es aprovecharlo para realizar un ataque de Cross-Site Scripting (XSS).*

Una vez que un atacante ha inyectado código malicioso en la plantilla de cliente, puede manipular los datos que se muestran al usuario, lo que le permite ejecutar código JavaScript en el navegador del usuario. A través de este código malicioso, el atacante puede intentar robar la cookie de sesión del usuario, lo que le permitiría obtener acceso no autorizado a la cuenta del usuario y realizar acciones maliciosas en su nombre.

Para prevenir los ataques de CSTI, los desarrolladores de aplicaciones web deben validar y filtrar adecuadamente la entrada del usuario y utilizar herramientas y frameworks de plantillas seguros que implementen medidas de seguridad para prevenir la inyección de código malicioso.