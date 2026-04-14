## Elegir la base de datos adecuada
- Tenemos muchas bases de datos administradas por AWS para elegir
- Preguntar para elegir la base de datos en funcion de tu arquitectura:
	- Mucha lectura, mucha escritura o carga de trabajo equilibrada? necesidades de rendimiento? va a cambiar, necesita escalar o fluctuar durante el dia?
	- Cuantos datos almacenar y durante cuanto tiempo? va a crecer? tamano medio de los objetos? como se accede a ello?
	- durabilidad de los datos?
	- requerimientos de latencia? usuarios simultaneos?
	- Modelos de datos? como se consultaran? juntas? estructurados? semi-estructurados?
	- esquema solido? mas flexibilidad? informacion? busqueda? RDBMS / NoSQL?
	- costes de licencia? cambiar a una base de datos nativa de la nube como aurora?

**Tipos de bases de datos**
- RDBMS = SQL / Online Transaction Protocol (OLTP): RDS, Aurora - ideal para uniones
- Base de datos NoSQL - sin uniones, sin SQL: DynamoDB (JSON), ElastiCache (pares calve /valor), Neptune (graficos), DocumentDB (para MongoDB), Keyspaces (para apache cassandra)
- Almacen de objetos: S3 (para objetos grandes) / Glacier (para copias de seguridad / archivos)
- Almacen de datos - data warehouse (SQL Analytics / BI): Redshift (OLAP), Athena, EMR.
	- Aca cabe aclarar a que estan destinadas las data warehouses, las mismas sirven para analizar grandes volumenes de datos historicos (mismo lo dice sus siglas ONLINE ANALYTICAL PROCESSING)
- Busquedas: OpenSearch (JSON) - texto libre, busquedas no estructuradas.
- Graficos: AWS Neptune - muestra las relaciones entre los datos
- Ledger: Base de datos de Amazon Quantum Ledger
- Series temporales: Amazon Timestream

## RDS
- PostgreSQL gestionado / MySQL / SQL server / MariaDB / Personalizado
- Tamano de instancia RDS aprovisionada y tipo y tamano de volumenes EBS
- Capacidad de autoescalado para almacenamiento
- Soporte para replicas de lectura y Multi AZ
- Seguridad a traves de IAM, Grupos de seguridad, KMS, SSL en transito
- Copia de seguridad automatizada con funcion de restauracion puntual (35 dias)
- Instantanea manual de la base de datos para una recuperacion a largo plazo
- Mantenimiento gestionado y programado (con tiempo de inactividad)
- Soporte para autenticacion IAM, integracion con Secrets Manager
- RDS Custom para acceder y personalizar la instancia subyacente (Oracle y SQL server)
- Caso de uso: Almacenar conjuntos de datos relacionales (RDBMS / OLTP), realizar consultas SQL, transacciones.

## Aurora
- API comptaible para PostgreSQL/MySQL, separacion de almacenamiento y computacion
- Almacenamiento: los datos se almacenan en 6 replicas, a traves de 3 AZ - alta disponibilidad, auto-reparacion, auto-escalado.
- Computacion: Cluster de Instancia DB a traves de multiples AZ, auto-escalado de replicas de lectura
- Cluster: Endpoints personalizados par instancias de base de datos escritoras y lectoras
- Mismas caracteristicas de seguridad / monitorizacion / mantenimiento que RDS
- opciones de copia de seguridad y restauracion
- Aurora serverless - para cargas de trabajo impredecibles / intermitentes, sin planificacion de capacidad.
- Aurora Multi-Master: para conmutacion por error de escritura continua (alta disponibilidad de escritura)
- Aurora Global: hasta 16 instancias de lectura de BD en cada region, replicacion de almacenamiento < 1 segundo
- Aurora Machine Learning: realiza ML usando SageMaker & Comprehend en Aurora
- Aurora database cloning: nuevo cluster a partir de uno existente, mas rapido que restaurar una instantanea
- Caso de uso: igual que RDS, pero con menos mantenimiento / mas felxible / mas rendimiento / mas funciones

## ElastiCache
- Managed Redis / Memcached (oferta similar a RDS, pero para caches)
- Almacen de datos en memoria, latencia de submilisegundos
- Debe aprovisionar un tipo de instancia EC2
- Soporte para clustering (Redis) y multi AZ, Read replicas (sharding)
- Funcion de copia de seguridad / instantanea / restauracion puntual
- Mantenimiento gestionado y programado
- Requiere algunos cambios en el codigo de la aplicacion para ser aprovechado
- Caso de uso: Almacen de claves/valores, lecturas frecuentes, menos escrituras, cache de resultados para consultas a la base de datos, almacenamiento de datos de sesion par sitions web, no puede utilizar SQL.

## DynamoDB
- Tecnologia de AWS, base de datos NOSQL sin servidor administrada, latencia de milisegundos.
- Modos de capacidad: capacidad aprovisionada con autoescalado opcional o capacidad bajo demanda.
- Puede sustituir a ElastiCache como almacen de claves/valores (almacenamiento de datos por sesion, por ejemplo, utilizando la funcion TTL)
- Alta disponibilidad, Multi-AZ por defecto, lectura y escritura desacopladas, capacidad de transaccion.
- Cluster DAX para cache de lectura, latencia de lectura de microsegundos.
- Seguridad, autenticacion y autorizacion a traves de IAM.
- Procesamiento de eventos: DynamoDB streams para integrarse con AWS lambda, o Kinesis data streams
- Funcion de tabla global: configuracion activa-activa (replicacion de los datos entre tablas)
- Copias de seguridad automatizadas de hasta 35 dias con PITR (point in time recovery a una tabla nueva), o copias de seguridad bajo demanda.
- Exportacion a S3 sin usar RCU dentro de la ventana PITR, importacion desde S3 sin usar WCU
- Excelente para evolucionar rapidamente esquemas
- Caso de uso: desarrollo de aplicaciones serverless (con documentos chicos de hasta 100 KB), cache distribuida sin servidor, no dispone de lenguaje de consulta SQL

## S3
- S3 es un almacen de claves / valor para objetos
- Genial para objetos grandes y no tanto para muchos objetos mas chicos
- Serverless, escala infinitamente, el size maximo de un objeto es de 5TB, ademas posee capacidad de versionado
- Niveles: S3 standard, S3 IA, S3 intelligent tiering, S3 Glacier + politica de ciclo de vida
- Funciones: versionado, cifrado, replicacion, Eliminacion de MFA, Registros de acceso...
- Seguridad: IAM, politicas de bucket, ACL,  punto de acceso, Object lambda, CORS, Object/vault lock
- cifrado: SSE-S3, SSE-KMS, SSE-C, cifrado del lado del cliente, TLS en transito, cifrado por defecto
- Batch operations, listado de archivos mediante S3 inventory
- Rendimiento: carga multi-part, aceleracion de transferencias de S3, S3 Select.
- Automatizacion: Notificaciones de eventos S3 (SNS, SQS, Lambda, EventBridge)
- Casos de uso: archivos estaticos, sitios web estaticos, almacen para archivos grandes