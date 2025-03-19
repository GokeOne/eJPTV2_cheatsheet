# Identificar vulnerabilidades en aplicaciones web

Para identificar vulnerabilidades de una aplicación web, podemos buscar la aplicación por google y ver si tiene alguna vulnerabilidad. Examinar la página de manera manual, como formularios, urls... Podemos encontrar algún command injection o sqli de manera manual.

También podemos hacer uso de herramientas automáticas como por ejemplo: OWASP ZAP, esta herramienta analiza una página y muestra todo tipo de vulnerabilidades que tenga.
Burp suite puede ayudar a encontrar problemas como xss.

Nikto es una herramienta que escanea servidores web en busca de archivos y configuraciones vulnerables.

Pruebas manuales:

SQL injection: Podemos probar de introducir `' OR 1=1 --`, tambien podemos probar con `1' or '1'='1`

Cross-Site Scripting (XSS) Intenta inyectar scripts como `<script>alert ('XSS')</script>` en formularios o parámetros de la URL.

Revisión de cabeceras HTTP: Verifica la seguridad de las cabeceras utilizando herramientas como cURL o [SecurityHeaders](https://securityheaders.com/)

# Localizar archivos y directorios ocultos

Gobuster y ffuf son herramientas muy buenas para este problema.

`gobuster dir -u http://target.com -w /directorio/del/wordlist.txt`

`ffuf -u http://target.com/FUZZ -w /directorio/del/wordlist.txt`



# Realizar un ataque de inicio de sesión por fuerza bruta

Con hydra podemos realizar ataques de fuerza bruta en formularios de inicio de sesión.

`hydra -l admin -P /path/to/passwordlist.txt http-post-form "/login:username=^USER^&password=^PASS^:F=incorrecto" -V`

	· hydra: Programa para realizar ataques de fuerza bruta
	· -l admin: especifica el nombre de usuario. Se podría usar un -L /directorio/del/wordlist.txt
	· -P /directorio/del/wordlist.txt Indica el diccionario para las contraseñas.
	· http-post-form: Indica que las peticiones las hace a un formulario que trabaja con post. En otro caso se podría usar el http-get-form
	· `"/login:username=^USER^&password=^PASS^:F=incorrecto"` Este es el parametro más completo y especifica como se debe formar la solicitud en HTTP POST, /login es la URL a la que se envia la solicitud,
	`username=^USER^&password=^PASS^` esto es el "payload" que se envía a la solicitud. Lo del interior de ^ se cambia por lo que hayamos introducido en hydra
	F=incorrecto Es la condición de fallo, indica a hydra que si le da error que cambie de combinación.
	De todo esto puede variar la url /login podria ser perfectamente /auth, también puede variar el username y el password, en caso de que sea user y pass, y la condición de fallo puede ser un código de estado de http o incluso una redirección inesperada.
	· -V Indica el parámetro de verbosidad, indica a hydra que muestre información más detallada sobre cada intento. 
Donde pone /login, digamos que tendriamos que poner http://lapagina.com/login:user=^USER^...

Para saber la condición de fallo y el username/user del payload, podemos obtenerlo con burpsuite haciendo una peticion con credenciales falsas y capturandolo con el proxy.


También podemos hacerlo con burpsuite y su opción de intruder.




# Realizar reconocimiento de aplicaciones web

Para identificar las tecnologías utilizadas en una aplicación web podemos utilizar herramientas como: WhatWeb o Wappalyzer

Para enumerar los subdominios podemos utilizar herramientas como sublist3r o amass.

Ej con sublist3r
`sublist3r -d target.com`



