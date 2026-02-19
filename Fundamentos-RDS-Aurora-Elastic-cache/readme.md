- RDS significa servicio de Base de Datos Regional
- Es un servicio de bases de datos gestionado para que las bases de datos utilicen SQL como lenguaje de consulta.
- Permite crear bases en el cloud que son gestionadas por AWS:
	- Postgres
	- MySQL
	- MariaDB
	- Oracle
	- Microsoft SQL Server
	- Aurora (base de datos propia de AWS)

RDS es un servicio gestionado que provee lo siguiente:
- Aprovisionamiento automatizado, parcheo del SO
- Copias de seguridad continuas y restauracion a una fecha determinada
- Dashboards de monitorizacion
- Replicas de lectura para mejorar el rendimiento de lectura
- Configuracion multi AZ para DR (Disaster Recovery)
- Ventanas de mantenimiento para actualizaciones
- Capacidad de escalado vertical y horizontal.
- Almacenamiento respaldado por EBS

**Autoescalado de almacenamiento**
- Te ayuda a aumentar el almacenamiento de tu instancia de base de datos RDS de forma dinamica
- Cuando RDS detecta que te estas quedando sin almacenamiento gratuito en la base de datos, escala automaticamente.
- Evita escalar manualmente el almacenamiento de tu base de datos.
- Se debe establecer un umbral maximo de almacenamiento
- Modifica automaticamente el almacenamiento si:
	- El almacenamiento gratuito es inferior al 10% del almacenamiento asignado
	- El almacenamiento bajo dura al menos 5 minutos
	- Han pasado 6 horas desde la ultima modificacion
- Util para aplicaciones con cargas de trabajo imprevisibles
- Soporta todos los motores de bases de datos RDS

### Replicas de lectura RDS vs Multi AZ
- Hasta 5 replicas de lectura.
- Dentro de AZ, a traves de AZ o a traves de la region.
- La replicacion en ASYNC, por lo que las lecturas son finalmente consistentes.
- Las replicas pueden ser promovidas a su propia BD.
- Las aplicaciones deben actualizar la cadena de conexion para aprovechar las replicas de lectura.

**RDS Multi AZ**
- Replicacion ASYNC
- Un nombre DNS - Conmutacion automatica de la aplicacion a la espera
- Aumenta la disponibilidad
- Conmutacion por error en caso de perdida de AZ, perdida de red, fallo de instancia o de almacenamiento
- Sin intervencion manual en las apps
- No se utiliza para escalar
- La replicacion Multi-AZ es gratis
- Las replicas de lectura deben configurarse como multi AZ para la recuperacion de desastres.

### RDS personalizado para Oracle y Microsoft SQL
- Base de datos gestionada de Oracle y Microsoft SQL server con personalizacion del sistema operativo y de la base de datos.
- RDS: automatiza la configuracion, el funcionamiento y el escalado de la base de datos de AWS
- Personalizada: acceso a la base de datos subyacente y al SO para que puedas:
	- Configurar los ajustes
	- Instalar parches
	- Habilitar las funciones nativas
	- Acceder a la instancia EC2 subyacente mediante SSH o SSM Session Manager
- Desactivar el modo automatizacion permite realizar la personalizacion.
- RDS vd RDS Personalizada:
	- RDS: Toda la base de datos y el SO son gestionados por AWS
	- RDS pers.: Acceso administrativo completo al SO subyacente y a la base de datos.

### Amazon Aurora
 Aurora es una tecnologica propietaria de AWS que soporta MySQL y Postgres como bases de datos, la misma esta optimizada para el cloud de AWS y su rendimiento es 5 veces superor al de MySQL en RDS y mas de 3 veces superior al rendimiento de Postgres en RDS.
 El escalado del almacenamiento es automatico y el mismo va de 10 en 10 gb hasta los 128TB.
 - Puede tener hasta 15 replicas.
 - La conmutacion por error es instantanea, nativa de la Alta Disponibilidad.

**Alta disponibilidad y escalado de lectura de Aurora**
- 6 copias de tus datos en 3 AZ:
	- 4 copias de las 6 necesarias para las escrituras
	- 3 copias de las 6 necesarias para las lecturas
	- Autoreparacion con replicacion entre pares
	- El almacenamiento esta dividido en 100 volumenes
- Una instancia de Aurora se encarga de las escrituras (maestra)
- Recuperacion automatica del maestro en menos de 30 segundos
- El maestro + hasta 15 replicas de lectura de Aurora realizan lecturas.
- Soporta para la replicacion entre regiones

### Amazon Aurora Conceptos avanzados

**Autoescalado de replicas**
En el punto final del lector si nuestra aplicacion procesa muchisimas lectura y nuestras replicas estas haciendo mucho uso de CPU aurora permite configurar el autoescaling horizontal de las mismas

**Endpoints personalizados**
- Definir un subconjunto de instancias de aurora como endpoint personalizado
- Ejemplo: Ejecutar consultas analiticas en replicas especificas.
- El endpoint del reader generalmente no se utiliza despues de definir endpoints personalizados.

**Aurora serverless**
- Instanciacion automatica de la base de datos y autoescalado en funcion del uso real
- Bueno para cargas de trabajo poco frecuentes, intermitentes o imprevisibles
- No es necesario planificar la capacidad
- Pagas por segundo, puede ser mas rentable

**Aurora Multi-Master**
- En caso de querer una conmutacion por error inmediata para el nodo de escritura
- Cada nodo hace R/W - rente a la promocion de un RR como nuevo maestro

**Aurora global**
- Replicas de lectura entre regiones
- Base de datos global de Aurora
	- 1 region primaria (lectura/escritura)
	- Hasta 5 regiones secundarias (solo de lectura)
	- Hasta 16 replicas de lectura por region secundaria

**Aurora Machine Learning**
- Te permite agregar predicciones basadas en ML a tus predicciones a traves de SQL
- Integracion sencilla, optimizada y segura entre Aurora y servicios de ML de AWS
- Servicios soportados:
	- Amazon SageMaker (se utiliza con cualquier modelo de ML)
	- Amazon Comprehend (para analisis de sentimientos)
- No necesitas tener experiencia en ML
- Caso de uso: deteccion de fraudes, orientacion de anuncios, analisis de sentimientos, recomendaciones de productos

### Copias de seguridad de RDS y Aurora
- Copiad de seguridad automatizadas:
	- Se generan cada 5 minutos y se pueden retener de 1 a 35 dias
- Snapshots manuales
	- Se activan manualmente por el usuario
	- Retencion ilimitada
- Truco: en una base RDS parada, seguiras pagando el almacenamiento. Si planear detenerla durante mucho tiempo , deberias hacer un snapshot y restaurar en su lugar.

En aurora:
- Las copias de seguridad no se pueden desactivar
- recuperacion puntual en ese intervalo de tiempo (1 a 35 dias)
- Snapshots manuales de la BD
	- se comporta igual que RDS

### Seguridad en RDS
- Cifrado en reposo
	- Cifrado de la base de datos maestra y de las replicas mediante AWS KMS - debe definirse en el momento de lanzamiento.
	- Si la base maestra no esta cifrada, las replicas de lectura no pueden ser cifradas.
	- Para cifrar una base de datos no cifrada, pasa por un Snapshot de la base de datos y restaura como cifrada.
- Cifrado en vuelo: Preparado para TLS por defecto, utiliza certificados root del lado del cliente de AWS TLS
- Autenticacion IAM: Roles de IAM para conectarse a tu base de datos (en lugar de usuario/pass)
- Grupos de seguridad: Controla el acceso de red a tu RDS / Aurora DB
- No hay SSH disponible excepto en RDS Custom
- Los logs de auditoria pueden ser activados y enviados a CloudWatch Logs para una mayor retencion.

### Proxy RDS
- Proxy de base de datos totalmente gestionado para RDS
- Permite a las apps agrupar y compartir las conexiones a la base de datos establecidas
- Mejora la eficiencia de la base de datos reduciendo el estres de los recursos de la base de datos (por ejemplo, CPU, RAM) y minimizando las conexiones abiertas (y los tiempos de espera)
- Sin servidor, con autoescalado y alta disponibilidad multi AZ
- Reduce el tiempo de conmutacion por error de RDS y Aurora hasta en un 66%
- Soporta RDS (MySQL, PostgreSQL y MariaDB) y Aurora (MySQL y PostgreSQL)
- No se requieren cambios de codigo para la mayoria de aplicaciones
- Aplica la autenticacion IAM para la base de datos y almacena de forma segura las credenciales en AWS Secrets Manager
- El proxy RDS nunca es accesible al publico (debe accederse desde la VPC)

### Vision general de ElastiCache
- De la misma manera que RDS es para conseguir bases de datos relacionales gestionadas...
- ElastiCache es para obtener Redis o Memcached gestionados.
- Las caches son bases de datos en memoria  con un rendimieento realmente alto y baja latencia
- Ayuda a reducir la carga de las bases de datos para cargas de trabajo de lectura intesniva
- Ayuda a que tu aplicacion no tenga estado
- AWS se encarga del mantenimiento/parche del sistema operativo, las optimizaciones, la instalacion, la configuracion, la supervision, la recuperacion de fallos y las copias de seguridad
- La implementacion de ElastiCache implica grandes cambios en el codigo.

**Redis vs Memcached**
Redis:
- Multi AZ con Auto-Failover
- Replicas de lectura para escalar las lecturas y tener alta disponibilidad.
- Durabilidad de los datos mediante persistencia AOF
- Funciones de copia de seguridad y restauracion.

Memcached:
- Multiples nodos para la particion de datos (sharding)
- Sin alta disponibilidad (replicacion)
- No es persistente
- No hay copia de seguridad ni restauracion
- Arquitectura multihilo

### ElastiCache para Solutions Architect
- Todas las caches de ElastiCache:
	- No soportan autenticacion de IAM
	- Las poltiicas de IAM en ElastiCache solo se utilzian para la seguridad a nivel de API de AWS
- Redis AUTH
	- Puedes establecer una "contrasena/token" cuando crees un cluster de redis
	- Se trata de un nivel adicional de seguridad para tu cache
	- Soporta el cifrado SSL en vuelo
- Memcached
	- Soporta la autenticacion basada en SASL 

**Patrones para ElastiCache**
- Carga lenta (lazy): todos los datos leidos se almacenan en cache y pueden quedar obsoletos.
- Escribir a traves: agrega o actualiza los datos en la cache cuando se escriben en una BD (no hay datos obsoletos)
- Almacenamiento de sesion: Almacena los datos temporales de la sesion en una cache usando TTL

**Caso de uso de ElastiCache - Redis**
- Las tablas de clasificacion en los juegos son computacionalmente complejas
- Redis brinda los "conjuntos ordenados" los cuales garantizan tanto la unicidad como el orden de los elementos.
- Cada vez que se agrega un nuevo elemento, se clasifica en tiempo real y se agrega el orden correcto.

### Lista de puertos
**Puertos importantes**
FTP: 21
SSH: 22
SFTP: 22
HTTP: 80
HTTPS: 443

**Puertos de bases de datos**
PostgreSQL: 5432
MySQL: 3306
Oracle RDS: 1521
Servidor MSSQL: 1433
Maria DB: 3306
Aurora: 5432 (postgreSQL) o 3306 (MySQL)