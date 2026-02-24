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

### TTL
El Time to live es el tiempo especifico que vivira en cache un registro, en este caso de route 53 por ejemplo una IP de un dominio, hay 2 estrategias para esto:
- TTL alto - por ejemplo 24 horas:
  - Menos trafico en route 53
  - Registros posiblemente obsoletos.
- TTL bajo - por ejemplo, 60 segundos.
	- Mas trafico en route 53 por ende mas costoso
	- los registros estan desfasados por menos tiempo
	- Facilidad para cambiar los registros
- Excepto los registros de alias, el TTL es obligatorio para cada registro DNS

### CNAME vs ALIAS
- Los recursos de AWS (Load balancer, cloudfront) exponen un nombre de host de AWS:
  - ibl-1234.us-east-2.elb.amazonaws.com y quieres myapp.midominio.com
- CNAME: 
	- Apunta un nombre de host a cualquier otro nombre de host (app.midominio.com => blablabla.algo.com)
	- SOLO PARA DOMINIOS NO ROOT
- Alias:
	- Apunta un nombre de host a un recurso de AWS (app.midomain.com => blabla.amazonaws.com)
	- Funciona para dominio raiz y dominio no raiz
	- Gratis
	- Comprobacion de salud nativa

**Registros con alias**
- Asigna un nombre de host a un recurso de AWS
- Una extension de la funcionalidad del DNS
- Reconoce automaticamente los cambios en las direcciones IP del recurso
- A diferencia de CNAME, puede utilizarse para el nodo superior de un espacio de nombres DNS (Zona Apex), por ejemplo: example.com
- El registro Alias es siempre del tipo A/AAAA para los recursos AWS (IPv4 y 6)
- No puedes establecer el TTL

**Objetivos Registros con Alias**
- Elastic load balancers
- distribuciones cloudfront
- api gateway
- entornos elastic beanstalk
- sitios web s3
- endpoints de interfaz vpc
- acelerador global
- registro route 53 en la misma zona alojada
- NO PUEDES ESTABLECER UN REGISTRO ALIAS PARA UN NOMBRE DNS DE EC2

### Politica de enrutamiento
No se debe confundir "enrutamiento" con el de por ejemplo: un load balancer que redirige trafico...
El enrutamiento en el DNS no enruta trafico, solo responde a consultas DNS.

#### Simple
- Normalmente dirige el trafico a un solo recurso aunque pueden especificarse varios valores en un mismo registro, de ser asi el cliente elige uno al azar.
- Cuando se habilita el Alias, solos epuede especificar un recurso de AWS
- No se puede asociar a los controles de salud

#### Ponderadas
Politica de enrutamiento basado en el peso de las instancias:
- Controla el % de las solicitudes que van a cada recurso especifico.
- Asigna a cada registro un peso relativo:
  traffic(%) = Weight ofr a specific record / Sum of all the weights for all records
- Los registros DNS deben tener el mismo nombre y tipo.
- Pueden asociarse a las comprobaciones de salud.
- Casos de uso: equilibrar la carga entre regiones, probar nuevas versiones de aplicaciones.
- Asigna un peso de 0 a un registro para dejar de enviar trafico a un recurso.
- Si todos los registros tienen un peso de 0 se devolveran todos los registros por igual

#### Basadas en latencia
- Redigir al recurso que tenga la menor latencia cerca de nosotros.
- Muy util cuando la latencia para los usuarios es una prioridad.
- La latencia se basa en el traifoc entre los usuarios y las regiones de AWS
- Los usuarios de Alemania pueden ser dirigidos a EEUU (si esa es la latencia mas baja)
- Se puede asociar a los controles de salud (tiene capacidad de conmutacion por error)

### Controles de salud
- Las comprobaciones de salud HTTP son solo para recursos publicos.
- Comprobacion de salud => conmutacion por error de DNS automatizada:
  - Comprobaciones de salud que supervisan un endpoint
  - Controles de salud que controlas otros controles de salud
  - Controels de salud que supervisan alarmas de cloudwatch
- Los controles de salud se integran con las metricas de cloudwatch

**Monitorizar un endpoint**
- Unos 15 verificadores de salud globales comprobaran la salud del endpoint
	- Umbral de salud/no salud - 3 por defecto
	- Intervalo - 30 segundos (customizable)
	- Protocolo soportado - TCP, HTTP y HTTPS
	- Si > 18% de los comprobadores de salud ifnorman que el endpoint esta sano Route 53 lo declara sano.
	- Es posible elegir las ubicaciones que quieres que utilice Route 53.
- Las comprobaciones de salud solo pasan si el endpoint responde 2xx y 3xx
- Se pueden configurar las comprobaciones para que pasen o no en funcion del texto de los primeros 5120 bytes de la respuesta.
- Se puede configurar el Firewall para dejar pasar los checks de Route 53.