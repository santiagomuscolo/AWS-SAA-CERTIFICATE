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

## DocumentDB
- Es una implementacion de AWS igual a mongoDB
- se utiliza para almacenar, consultar e indexar datos JSON
- conceptos de despliegue similares a los de aurora
- totalmente gestionado, de alta disponibilidad con replicacion a traves de 3 AZ
- Escala automaticamente a cargas de trabajo con millones de peticiones por segundo

## Neptune
- Base de datos grafica totalmente gestionada
- Una base de datos "grafica" hace referencia a un conjunto de datos de grafos, un ejemplo popular puede ser una red social
	- Los usuarios tienen amigos
	- Las publicaciones comentarios
	- Los comentarios likes
	- Los usuarios comparten y les gustan las publicaciones
- Alta disponibilidad en 3 AZ, con hasta 15 replicas de lectura
- Construye y ejecuta aplicaciones, que trabajen con conjuntos de datos altamente conectados, optimizados para estas complejas y dificiles consultas.
- Puede almacenar hasta miles de millones de relaciones y consultar el grafico con una latencia de milsiegundos
- Alta disponibilidad con replicas a traves de multiples AZs
- Excelente para grafos de conocimiento (WIKIPEDIA), deteccion de fraudes, motores de recomendacion, redes sociales.

## Keyspaces (apache cassandra)
- Apache Casandra es una base de datos NoSQL distribuida de codigo abierto
- Un servicio de base de datos administrado compatible con apache cassandra.
- serverless, escalable, de alta disponibilidad, totalmente administrado por AWS
- Escala automaticamente las tablas hacia arriba/abajo en funcion del trafico de la aplicacion
- Las tablas se replican 3 veces en multiples AZ
- Uso del lenguaje de consulta CQL
- Latencia de un milisegundo a cualquier escala, miles de solicitudes por segundo
- Capacidad: Modo bajo demanda o modo provisionado con autoescalado
- Cifrado, copia de seguridad, reucperacion puntual (PITR) de hasta 35 dias.
- Caso de uso: almacenar informacion de dispositivos IoT, datos de series temporales, ...

## QLDB
- QLDB significa "Quantum Ledger Database" (base de datos de libros contables)
- Un libro de contabilidad es un libro que registra transacciones financieras
- Totalmente gestionada, sin servidor, de alta disponibilidad, con replicacion en 3 AZ
- Se utiliza para revisar el historial de todos los cambios realizados en los datos de tu aplicacion a lo largo del tiempo
- Sistema inmutable: ninguna entrada puede ser eliminada o modificada, verificable criptograficamente
- ![[Pasted image 20260415165051.png]]
- Rendimiento 2-3 veces mejor que los marcos de blockchain de libro mayor comun
- Diferencia con Amazon Managed Blockchain: no hay un componente de descentralizacion como en el mismo, de acuerdo a las normas de regulacion financiera.

## Timestream
- base de datos de series temporales totalmente gestionada, rapida, escalable y sin servidor
- Se amplia y reduce automaticamente para ajustar capacidad
- Almacena y analiza billones de eventos al dia
- 1000 veces mas rapida y 1/10 del coste de las bases relacionales
- Consultas programadas, soporta SQL
- Almacenamiento de datos por niveles: los datos recientes se guardan en la memoria y los historicos en un almacenamiento de coste optimizado
- Funciones integradas de analisis de series temporales (ayuda a identificar patrones en los datos casi en tiempo real)
- Cifrado en transito y en reposo
- Caso de uso: Aplicaciones IoT, aplicaciones operativas, analisis en tiempo real, ...

**series de datos temporales hace referencia a datos ordenados por fecha, hora, segundo, etc.. y que se utilizan para ver como varian a lo largo del tiempo**

## Quiz
Question 1:
¿Qué base de datos te ayuda a almacenar conjuntos de datos relacionales, con compatibilidad con el lenguaje SQL y la capacidad de procesar transacciones como la inserción, la actualización y la eliminación?
- Amazon RDS

Question 2:
¿Qué servicio de AWS te proporciona una capacidad de almacenamiento en caché compatible con la API de Redis?
- ElastiCache

Question 3:
Quieres migrar una base de datos MongoDB NoSQL on-premise a AWS. No quieres gestionar ningún servidor de base de datos, así que quieres utilizar una base de datos NoSQL gestionada, preferiblemente sin servidor, que te proporcione alta disponibilidad, durabilidad y fiabilidad, y la capacidad de llevar tu base de datos a nivel global. ¿Qué base de datos debes elegir?
- AWS DynamoDB

Question 4:
Quieres realizar un Procesamiento de Transacciones en Línea (OLTP). Te gustaría utilizar una base de datos con capacidad de autoescalado integrada y que te proporcione el máximo número de réplicas para su almacenamiento subyacente. ¿Qué servicio de AWS recomiendas?
- Amazon Aurora

Question 5:
Como arquitecto de soluciones, una empresa emergente te ha pedido ayuda, ya que están trabajando en una arquitectura para un sitio web de redes sociales en el que los usuarios pueden ser amigos entre sí, y darles "me gusta" a las publicaciones de los demás. La empresa tiene previsto realizar algunas consultas complicadas, como "_¿Cuál es el número de "me gusta" de las publicaciones que han publicado los amigos de Mike?_". ¿Qué base de datos recomiendas?
- Amazon Neptune

Question 6:
Tienes un conjunto de archivos, de 100 MB cada uno, que quieres almacenar en un almacén de valores clave fiable y duradero. ¿Qué servicio de AWS recomiendas?
Amazon S3

Question 7:
Una empresa tiene un sitio web on-premise que utiliza ReactJS como frontend, NodeJS como backend y MongoDB como base de datos. Hay algunos problemas con la base de datos MongoDB autoalojada, ya que requiere mucho mantenimiento y no tienen ni pueden permitirse los recursos o la experiencia para manejar esos problemas. Así que se tomó la decisión de migrar el sitio web a AWS. Han decidido alojar la aplicación ReactJS del frontend en un bucket S3 y el backend NodeJS en un conjunto de instancias EC2. ¿Qué servicio de AWS pueden utilizar para migrar la base de datos MongoDB que les proporciona alta escalabilidad y disponibilidad sin hacer ningún cambio en el código?
- Amazon DocumentDB

Question 8:
Una empresa que utiliza una base de datos Apache Cassandra autoalojada en sus instalaciones y que quiere migrar a AWS. ¿Qué servicio de AWS pueden utilizar que les proporcione una base de datos Apache Cassandra totalmente gestionada, altamente disponible y escalable?
- Amazon Keyspaces

Question 9:
Una empresa de pagos online utiliza AWS para alojar su infraestructura. Debido a la naturaleza de la aplicación, tienen un requisito estricto de almacenar un registro preciso de las transacciones financieras, como las de crédito y débito. Dichas transacciones deben almacenarse en un almacenamiento seguro, inmutable y cifrado que pueda ser verificado criptográficamente. ¿Qué servicio de AWS es el más adecuado para este caso de uso?
- Amazon QLDB 

Question 10:
Una startup está trabajando en el desarrollo de un nuevo proyecto para reducir los incendios forestales debidos al cambio climático. La startup está desarrollando sensores que se extenderán por todo el bosque para realizar algunas lecturas como la temperatura, la humedad y las presiones que ayudarán a detectar los incendios forestales antes de que se produzcan. Van a tener miles de sensores que van a almacenar muchas lecturas cada segundo. Es necesario almacenar esas lecturas y hacer un análisis rápido para poder predecir si hay un incendio. ¿Qué servicio de AWS pueden utilizar para almacenar esas lecturas?
- Amazon Timestream