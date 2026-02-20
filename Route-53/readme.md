## Que es un DNS
La DNS o sistema de nombres de dominio es la columna vertebral del internet encargada de traducir los dominios amigables para los usuarios a las IPs correspondientes, por ejemplo:
www.google.com -> 172.217.18.36

**Terminologia de DNS**
- Registrador de nombres de dominios: Amazon Route 53, GoDaddy, ...
- Registros DNS: A, AAAA, CNAME, NS, ...
- Archivos de zona: Contiene registros DNS
- Servidor de nombres: Resuelve las consultas DNS (autorizadas o no autorizadas)
- Dominio de primer nivel (TLD): .com, .us, .in, ...
- Dominio de segundo nivel (SLD): amazon.com, google.com

**Como funciona el DNS**
navegador web -> servidor DNS local -> servidor DNS raiz -> servidor DNS TLD -> Servidor DNS SLD


## Amazon Route 53
- Un DNS altamente disponible, escalable, totalmente gestionado y autoritativo
	- Autoritario = el cliente puede actualizar registros DNS
- Route 53 tambien es un registrador de dominios.
- Posibilidad de comprobar la salud de tus recursos
- El unico servicio de AWS que ofrece un SLA (service level agreement) de disponibilidad del 100%

**Registros**
- Cada registro contiene:
	- Nombre del dominio/subdominio - por ejemplo, ejemplo.com
	- Tipo de registro -  por ejemplo, A, AAAA
	- Valor - por ejemplo, 12.34.56.78
	- Politica de enrutamiento - como responde Route 53 a las consultas
	- TTL - cantidad de tiempo que el registro se almacena en cache en los Resolvers DNS
- Route 53 soporta los siguientes tipos de registros:
	- (obligatorio): A / AAAA/ NS / CNAME
	- (avanzado): CAA / DS / MX / NAPTR / PTR / SOA / TXT / SPF / SRV

**Tipos de registro**
- A - asigna un nombre de host a IPv4
- AAAA - asigna un nombre de host a IPv6
- CNAME - asigna ujn nombre de host a otro nombre de host
	- El objetivo es un nombre de dominio que debe tener un registro A o AAAA
	- No puedes crear un registro CNAME para el nodo superior de un espacio de nombres DNS (Zona Apex)
	- Ejemplo: no puedes crear para example.com, pero si para www.example.com
- NS - servidores de nombres para la Zona Alojada
	- Controla como se enruta el trafico de un dominio

**Zonas de alojamiento**
- Un contenedor para los registros que definen como dirigir el trafico a un dominio y sus subdominios.
- Zonas de alojamiento publico: Contiene registros que especifican como enrutar el trafico en internet
- Zonas de alojamiento privadas: Contienen registros que especificar como enrutar el trafico dentro de una o mas VPC
