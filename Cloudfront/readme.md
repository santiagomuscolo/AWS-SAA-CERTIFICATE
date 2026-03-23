### Vision general
- Red de entrega de contenidos (CDN)
- Mejora el rendimiento de lectura, el contenido se almacena en cache en edge location
- Mejora la experiencia de los usuarios
- +400 puntos de presencia a nivel mundial (ubicaciones edge)
- Proteccion DDoS, integracion con Shield y AWS web application firewall

**Origenes**
- Bucket S3
	- Para distribuir archivos y almacenarlos en cache edge
	- Seguridad mejorada con Cloudfront origin acces control (OAC)
	- Cloudfront puede utilizarse como entrada (para subir archivos S3)
- Origen personalizado HTTP
	- ALB
	- EC2
	- Sitio web S3
	- Cualquier backend HTTP

**Cloudfront a alto nivel**
![[Pasted image 20260323182108.png]]

**S3 como origen**
![[Pasted image 20260323182233.png]]

**Cloudfront vs S3 Cross Region Replication (CRR)**
La principal diferencia yace en que cloudfront es ideal para la entrega de contenido estatico mientras que CRR es ideal para contenido dinamico ya que se actualiza casi en tiempo real

- Cloudfront:
	- Red global edge
	- Los archivos se almacenan en un cache con un TTL configurable
 - CRR:
	 - Debe configurarse por cada region en la que quieras producir la replicacion
	 - Los archivos se actualizan casi en tiempo real
	 - Solo lectura

### Cloudfront - ALB o EC2 como origen
![[Pasted image 20260323185731.png]]
### Cloudfront - Restriccion Geografica
- Podemos restringir quien puede acceder a nuestra distribucion
	- Lista de permitidos: Permite que tus usuarios accedan a tu contenido solo si estan en uno de los paises de una lista de paises aprobados.
	- Lista de bloqueo: Evita que tus usuarios accedan a tu contenido si se encuentran en uno de los paises de la lista de paises prohibidos.
- El "pais" se determina usando una base de datos Geo-Ip de terceros
- Caso de uso: Leyes de derechos de autor para controlar el acceso a los contenidos.

### Cloudfront - Clases de precios
- Los edge location de cloudfront estan distribuidos mundialmente y su precio varia
![[Pasted image 20260323190231.png]]

**Clases de precios**
- Se puede reducir el precio reduciendo el numero de Edge locations
- Actualmente existen 3 clases de precios:
	- Clase de precio Todos o All: Incluye todas las regiones - mejor rendimiento.
	- Clase de precio 200: Incluye la mayoria de regiones pero exluye las mas caras.
	- Clase de precio 100: Incluye solo las regiones mas baratas.
![[Pasted image 20260323190518.png]]![[Pasted image 20260323190555.png]]

### Cloudfront - Invalidacion de cache
- En caso de que actualices el orgen del back-end, cloudfront no lo sabe y solo obtendra el contenido fresco cuando el TTL expire.
- Sin embargo, puede forzarse una invalidacion de la cache total o parcial realizando una "Invalidacion de Cloudfront"
- Puedes invalidar todos los archivos (*) o una ruta especial (/imagenes/)
![[Pasted image 20260323191205.png]]

### AWS - Global accelerator

**IP UNICAST vs IP ANYCAST**
- IP Unicast: un servidor tiene una direccion IP.
- IP Anycast: todos los servidores tienen la misma direccion IP y el cliente es dirigido al mas cercano.

**AWS Global Accelerator**
- Aprovecha la red interna de AWS para dirigirte a tu aplicacion.
- Se crean 2 IP Anycast para tu aplicacion.
- Las IP Anycast envian el trafico directamente a las Edge Locations
- Las Edge Locations envian el trafico a tu aplicacion.
- Funciona con IP, Instancias EC2, ALB, NLB
- Rendimiento consistente
	- Enrutamiento inteligente para baja latencia y una rapida conmutacion por error
	- No hay problemas con la cache del cliente por que la IP no cambia
	- Red interna de AWS
- Comprobaciones de salud
	- Realiza comprobaciones de salud a tus aplicaciones.
	- Ayuda a que tu aplicacion sea global
	- Genial para la recuperacion de desastres
- Seguridad
	- Solo hay que poner en la white list 2 IP externas
	- Proteccion DDoS gracias a AWS Shield

**AWS GA vs Cloudfront**
- Cloudfront
	- Mejora el rendimiento tanto del contenido almacenable en cache (como imagens y videos), como del contenido dinamico (como la aceleracopm de la API y la entrega de sitios dinamicos)
	- El contenido se sirve en el edge.
- AWS GA:
	- Mejora el rendimiento de una amplia gama de aplicaciones sobre TCP o UDP
	- Proxy de paquetes en el edge a las aplicaciones que se ejecutan en una o mas regiones de AWS
	- Es adecuado para casos de uso no HTTP, como juegos UDP o IoT (MQTT) o voz sobre IP
	- Bueno para casos de uso HTTP que requieren de IPs estaticas
	- Bueno para casos de uso de HTTP que requieran una conmutacion por error regional determinista y rapida.