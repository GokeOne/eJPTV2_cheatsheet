# Localizar puntos finales de una red:

## Descripción

Identificar todos los dispositivos que están conectados a la red que se va a evaluar.

## Herramientas

NMAP:
Para ver todos los hosts activos de una red, podemos utilizar nmap ej:
- nmap -sP 192.168.1.0/24
Esto hará una prueba de ping a todos los host de una red.

NETDISCOVER:
- netdiscover -r 192.168.1.0/24 -i eth0

FPING:
- fping -g 192.168.1.0/24
Fping hacer una prueba de ping en toda la red que marquemos con el -g, podemos usar -a para que solo nos muestre los equipos que están activos.


# Identificar puertos y servicios abiertos en un objetivo:

## Descripción
Identificar que puertos están abierto en los dispositivos encontrados en la red, y identificar que servicios hay en cada puerto.

## Herramientas
Para ver los puertos abierto de un dispositivos podemos utilizar nmap, que es una herramienta muy potente.
Ej:
nmap -sS -p-     -- Esto escanearia todos los puertos TCP del dispositivo.
nmap -sV -p 22,25,80,443 192.168.1.10   -- identificar los servicios y su versión en los puertos 22, 25, 80 y 443.

Nmap tiene muchas más funcionalidades, por ejemplo cuenta con una variedad de scripts para comprobar si hay diferentes vulnerabilidades.
Ej:
nmap -sV -Pn --script auth 192.168.1.10     -Esto muestra un listado de usuarios que pueden ser accesibles por credential defaults o por contraseñas vacias, hay diferentes tipos de scripts.
Scripts: (auth, default, discovery, external, intrusive, safe, vuln, all)

Con netcat también se puede probar la conexión de puertos tcp y udp.

nc -zv 192.168.1.10 80   --- Esto probaria el puerto 80 de la ip en TCP
nc -zvu 192.168.1.10 80 --- Esto probaria el puerto 80 en la ip en UDP
La -z hace que no se envien datos, y la -v hace que sea en modo verbose, la -u hace que la prueba de conexión sea en UDP


# Identificar el sistema operativo
## Descripción
Determinar que sistema operativo está corriendo en el dispositivo objetivo.

## Herramientas
NMAP
La herramienta nmap tiene utilidades para detectar el sistema operativo del obejtivo Ej:
nmap -O 192.168.1.10   --Esto identificará el sistema operativo del host.


También lo podemos identificar por el ttl del ping:
- **64** – Linux/MAC OSX systems
- **128** – Windows systems
- **255** – Network devices like routers
# Extraer información de la empresa de fuentes públicas
## Descripción
Recopilar información sobre una empresa utilizando fuentes publicas. OSINT
## Herramientas
[OSINT-Framework](https://osintframework.com/)
Google Dorks sirve para filtrar las búsquedas de Google. Ej:
allintext: ||site: || intitle: || inurl: || intext: || ext: 
Podemos buscar extensiones, texto de la pagina, titulos de la pagina...
https://www.exploit-db.com/google-hacking-database aquÍ podemos ver ejemplo de Google Dorks

También tenemos herramientas como theHarvester, que sirve para recolectar correos electronicos, subdominios, hosts, nombres de empleados y más. Ej:
- theHarvester -d ieselcalamot.com -b yahoo,dnsdumpster,duckduckgo -n -t
Depende de la fuente que utilicemos para hacer la búsqueda, nos dará un resultado o otro.


# Reunir direcciones de correo electrónico de fuentes públicas
## Descripción
Buscar y recoger las direcciones de correo asociadas a una empresa.

## Herramientas
Para esto podemos utilizar la herramienta theHarvester, para encontrar diferentes correos. 
[Hunter](https://hunter.io) Y hunter que es un servicio en línea que nos permite buscar correos

# Recopilar información técnica de fuentes públicas
## Descripción
Obtener información relevante sobre las tecnologías utilizadas y la infraestructura de la empresa.

## Herramientas
Para recopilar información técnica de la empresa, podemos utilizar diferentes plataformas.
[Shodan](https://www.shodan.io/)
[NetCraft](https://www.netcraft.com/)   - tiene extensión de google
[Web-Checker](https://web-check.xyz/)

Estas son una de las pocas páginas web, en las que podemos consultar como esta formada la página de la empresa, por ejemplo podemos ver el tipo de certificados que tiene, también podemos ver las cabeceras http , donde esta alojado el servidor...

# Identificar vulnerabilidades en los servicios

## Descripción 
Encontrar las vulnerabilidades existentes de las diferentes versiones de cada servicio.

## Herramientas
[CVE](https://cve.mitre.org/)
[NVD](https://nvd.nist.gov/)
[Exploit Database](https://www.exploit-db.com/)
[Vulners](https://vulners.com/)
[CVE-DETAILS](https://www.cvedetails.com/)

Por ejemplo en cve-details podemos ir a la página buscar en la barra de busqueda apache 2.4.46 y ver los CVEs asociados a esta versión especifica de Apache.

# Evaluar la información y la criticidad o impacto de las vulnerabilidades
## Descripción
Cuando tengamos la versión del servicio y los cve existentes para esta, podemos determinar la gravedad del problema evaluando el impacto de las vulnerabilidades

## Herramientas
[INCIBE](https://www.incibe.es/)
[NVD](https://nvd.nist.gov/)

Podemos ver la severidad de los CVE, si ha sido muy utilizada e información sobre el problema que es esta vulnerabilidad EJ:
https://nvd.nist.gov/vuln/detail/CVE-2024-3094